cmake_minimum_required(VERSION 3.25...4.3)
project(elib VERSION 2.7.1 LANGUAGES CXX)

if(PROJECT_IS_TOP_LEVEL)
    if(MSVC)
        add_compile_options(/MP)
    endif()

    if(BUILDSYS_SANITIZER STREQUAL "MaxSan")
        if(MSVC)
            add_compile_options(
                "$<$<CONFIG:Debug>:/fsanitize=address>"
                "$<$<CONFIG:Debug>:/Zi>"
            )
            add_link_options("$<$<CONFIG:Debug>:/INCREMENTAL:NO>")
        elseif(CMAKE_CXX_COMPILER_ID MATCHES "GNU|Clang")
            add_compile_options(
                "$<$<CONFIG:Debug>:-fsanitize=address,undefined>"
                "$<$<CONFIG:Debug>:-fno-omit-frame-pointer>"
            )
            add_link_options("$<$<CONFIG:Debug>:-fsanitize=address,undefined>")
        endif()
    endif()
endif()

add_library(elib STATIC)
add_library(elib::elib ALIAS elib)

add_subdirectory(src)

if(PROJECT_IS_TOP_LEVEL)
    include(CTest)
    if(BUILD_TESTING)
        add_subdirectory(tests)
    endif()
endif()


# CPack
set(CPACK_SOURCE_GENERATOR "TGZ;ZIP")
set(CPACK_SOURCE_IGNORE_FILES
    /.git/
    /.github/
    /build/
    /.*build.*/
    /\\\\.DS_Store
)
set(CPACK_VERBATIM_VARIABLES YES)
include(CPack)