## Catch
set(CATCH_INCLUDE_DIR ${MYPROJECT_LIB_DIR}/catch)
file(DOWNLOAD 
    "https://github.com/philsquared/Catch/releases/download/v1.9.4/catch.hpp" 
    "${CATCH_INCLUDE_DIR}/catch.hpp" 
    SHOW_PROGRESS
    EXPECTED_MD5;1af57a3c17849b0767937eecf678f001
    STATUS status
    LOG log)

add_library(Catch INTERFACE)
target_include_directories(Catch INTERFACE ${CATCH_INCLUDE_DIR})

## FakeIt
set(FAKEIT_INCLUDE_DIR ${MYPROJECT_LIB_DIR}/fakeit)
file(DOWNLOAD
    "https://raw.githubusercontent.com/eranpeer/FakeIt/2.0.4/single_header/catch/fakeit.hpp"
    "${FAKEIT_INCLUDE_DIR}/fakeit.hpp"
    SHOW_PROGRESS
    EXPECTED_MD5;d8ca74cbbb5165436e7ef5421f521c8c
    STATUS status
    LOG log)

add_library(FakeIt INTERFACE)
target_include_directories(FakeIt INTERFACE ${FAKEIT_INCLUDE_DIR})

include_directories(${MYPROJECT_SRC_DIR})

set(TEST_SOURCES
  main.cpp
)

add_executable(tests ${TEST_SOURCES} $<TARGET_OBJECTS:myproject_lib>)
target_link_libraries(tests Catch FakeIt ${MYPROJECT_DEPS})
add_test(NAME tests COMMAND tests)
add_dependencies(tests myproject)
