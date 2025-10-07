cmake_minimum_required(VERSION 3.11)

project(PSPInterfaceTest)
set(PROJECT_EX_NAME "PSP VLF Interface Test")
set(PROJECT_VERSION_MAJOR 1)
set(PROJECT_VERSION_MINOR 1)

# Set helper variables:
set(PSPSDK ${PSPDEV}/psp/sdk)
set(PSPBIN ${PSPDEV}/bin)
set(PSPCMAKE ${PSPDEV}/psp/share/cmake)

list(APPEND PROJECTDATA_ALL_FILES_SRC
    "src/crt0.c"
    "src/main.c"
)

link_directories(${CMAKE_SOURCE_DIR}/libs/lib)
add_executable(${PROJECT_NAME} ${PROJECTDATA_ALL_FILES_SRC})
include_directories(${PROJECT_NAME} PRIVATE ${CMAKE_SOURCE_DIR}/libs/include)

target_link_libraries(${PROJECT_NAME} PRIVATE
    ${PSP_LIBRARIES}
    pspuser
    pspmodinfo
    vlfgui
    vlfgu
    vlfutils
    vlflibc
)

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -O2 -G0 -Wall -fshort-wchar -fno-pic -mno-check-zero-division")
set(CMAKE_CXX_FLAGS "${CMAKE_C_FLAGS} -fno-exceptions -fno-rtti")
set(CMAKE_EXE_LINKER_FLAGS "-L${PSPDEV}/lib -L${PSPDEV}/psp/lib -L${PSPDEV}/psp/sdk/lib -Wl,-zmax-page-size=128 -nostdlib -nodefaultlibs")

# Create an EBOOT.PBP file
create_pbp_file(
    TARGET ${PROJECT_NAME}
    ICON_PATH ${CMAKE_SOURCE_DIR}/prx/icon0.png
    BACKGROUND_PATH NULL
    PREVIEW_PATH NULL
    TITLE ${PROJECT_EX_NAME}
    BUILD_PRX TRUE
)

# After that's done, let's put everything in an output folder.
add_custom_command(
    TARGET ${PROJECT_NAME} 
    POST_BUILD
    COMMAND echo "Copying files to Output."
    COMMAND ${CMAKE_COMMAND} -E make_directory ${CMAKE_SOURCE_DIR}/output
    COMMAND ${CMAKE_COMMAND} -E copy ${CMAKE_SOURCE_DIR}/Build/EBOOT.PBP ${CMAKE_SOURCE_DIR}/output/EBOOT.PBP
    COMMAND ${CMAKE_COMMAND} -E copy ${CMAKE_SOURCE_DIR}/Build/${PROJECT_NAME}.prx ${CMAKE_SOURCE_DIR}/output/${PROJECT_NAME}.prx
    COMMAND ${CMAKE_COMMAND} -E copy ${CMAKE_SOURCE_DIR}/prx/intraFont.prx ${CMAKE_SOURCE_DIR}/output/intraFont.prx
    COMMAND ${CMAKE_COMMAND} -E copy ${CMAKE_SOURCE_DIR}/prx/iop.prx ${CMAKE_SOURCE_DIR}/output/iop.prx
    COMMAND ${CMAKE_COMMAND} -E copy ${CMAKE_SOURCE_DIR}/prx/vlf.prx ${CMAKE_SOURCE_DIR}/output/vlf.prx
    COMMAND ${CMAKE_COMMAND} -E copy ${CMAKE_SOURCE_DIR}/prx/logo.png ${CMAKE_SOURCE_DIR}/output/logo.png
)
