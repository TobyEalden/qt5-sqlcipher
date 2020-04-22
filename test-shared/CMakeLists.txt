set(CMAKE_BUILD_TYPE Debug)
set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_executable(qsqlcipher-test main.cpp)
target_link_libraries(qsqlcipher-test Qt5::Sql)

add_test(NAME qsqlcipher-test COMMAND qsqlcipher-test)
