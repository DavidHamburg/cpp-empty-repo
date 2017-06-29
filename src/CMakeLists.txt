set(SOURCE_FILES )

add_library(myproject_lib OBJECT ${SOURCE_FILES})
add_executable(myproject main.cpp $<TARGET_OBJECTS:myproject_lib>)
TARGET_LINK_LIBRARIES(myproject ${MYPROJECT_DEPS})
