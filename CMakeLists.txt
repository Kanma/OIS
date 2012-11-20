cmake_minimum_required(VERSION 2.6)
project(OIS)


##########################################################################################
# XMake-related settings

# Set the output paths
if (NOT DEFINED XMAKE_BINARY_DIR)
    set(XMAKE_BINARY_DIR "${OIS_BINARY_DIR}")
    set(CMAKE_RUNTIME_OUTPUT_DIRECTORY "${XMAKE_BINARY_DIR}/bin")
    set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY "${XMAKE_BINARY_DIR}/lib")
    set(CMAKE_LIBRARY_OUTPUT_DIRECTORY "${XMAKE_BINARY_DIR}/bin")
endif()

# Set the dependencies path
if (NOT DEFINED XMAKE_DEPENDENCIES_DIR)
    set(XMAKE_DEPENDENCIES_DIR "${OIS_SOURCE_DIR}/dependencies")
endif()


##########################################################################################
# XMake importation

if (NOT EXISTS "${XMAKE_DEPENDENCIES_DIR}/XMake/XMake.cmake")
    message(FATAL_ERROR
"Missing dependency: XMake
This distribution of OIS requires XMake.

It is provided as a GIT submodule of this repository.

Did you forgot to execute the following commands?
   git submodule init
   git submodule update")
endif()

include("${XMAKE_DEPENDENCIES_DIR}/XMake/XMake.cmake")


##########################################################################################
# Declaration of the library

set(OIS_HEADERS
    include/OIS/OISConfig.h
    include/OIS/OISEffect.h
    include/OIS/OISEvents.h
    include/OIS/OISException.h
    include/OIS/OISFactoryCreator.h
    include/OIS/OISForceFeedback.h
    include/OIS/OIS.h
    include/OIS/OISInputManager.h
    include/OIS/OISInterface.h
    include/OIS/OISJoyStick.h
    include/OIS/OISKeyboard.h
    include/OIS/OISMouse.h
    include/OIS/OISMultiTouch.h
    include/OIS/OISObject.h
    include/OIS/OISPrereqs.h
)

set(OIS_SOURCES
    src/OISEffect.cpp
    src/OISException.cpp
    src/OISForceFeedback.cpp
    src/OISInputManager.cpp
    src/OISJoyStick.cpp
    src/OISKeyboard.cpp
    src/OISObject.cpp
    src/extras/LIRC/OISLIRC.cpp
    src/extras/LIRC/OISLIRCFactoryCreator.cpp
)


if (WIN32)
    set(OIS_PLATFORM_HEADERS
        include/OIS/win32/Win32ForceFeedback.h
        include/OIS/win32/Win32InputManager.h
        include/OIS/win32/Win32JoyStick.h
        include/OIS/win32/Win32KeyBoard.h
        include/OIS/win32/Win32Mouse.h
        include/OIS/win32/Win32Prereqs.h
    )
    
    set(OIS_PLATFORM win32)
    
    set(OIS_PLATFORM_SOURCES
        src/win32/Win32ForceFeedback.cpp
        src/win32/Win32InputManager.cpp
        src/win32/Win32JoyStick.cpp
        src/win32/Win32KeyBoard.cpp
        src/win32/Win32Mouse.cpp
        src/win32/extras/WiiMote/hiddevice.cpp
        src/win32/extras/WiiMote/OISWiiMote.cpp
        src/win32/extras/WiiMote/OISWiiMoteFactoryCreator.cpp
        src/win32/extras/WiiMote/OISWiiMoteForceFeedback.cpp
        src/win32/extras/WiiMote/OISWiiMoteRingBuffer.cpp
        src/win32/extras/WiiMote/wiimote.cpp
    )
    
    # need DirectX includes and libraries
    find_package(DirectX)
    include_directories(${DirectX_INCLUDE_DIRS})
    
    set(OIS_PLATFORM_LIBS ${DirectX_DINPUT8_LIBRARY} ${DirectX_DXGUID_LIBRARY})

elseif (APPLE)
    # if (OGRE_BUILD_PLATFORM_APPLE_IOS)
    #     set(OIS_PLATFORM_HEADERS
    #             include/OIS/iphone/iPhoneAccelerometer.h
    #             include/OIS/iphone/iPhoneHelpers.h
    #             include/OIS/iphone/iPhoneInputManager.h
    #             include/OIS/iphone/iPhoneMultiTouch.h
    #             include/OIS/iphone/iPhonePrereqs.h
    #     )
    #     
    #     set(OIS_PLATFORM iphone)
    #     
    #     set(OIS_PLATFORM_SOURCES
    #             src/iphone/iPhoneAccelerometer.mm
    #             src/iphone/iPhoneInputManager.mm
    #             src/iphone/iPhoneMultiTouch.mm
    #     )
    # else()
        set(OIS_PLATFORM_HEADERS
            include/OIS/mac/CocoaInputManager.h
            include/OIS/mac/CocoaJoyStick.h
            include/OIS/mac/CocoaKeyBoard.h
            include/OIS/mac/CocoaMouse.h
            include/OIS/mac/CocoaHelpers.h
            include/OIS/mac/MacHelpers.h
            include/OIS/mac/MacHIDManager.h
            include/OIS/mac/MacInputManager.h
            include/OIS/mac/MacJoyStick.h
            include/OIS/mac/MacKeyboard.h
            include/OIS/mac/MacMouse.h
            include/OIS/mac/MacPrereqs.h
        )
        
        set(OIS_PLATFORM mac)
        
        set(OIS_PLATFORM_SOURCES
            src/mac/CocoaInputManager.mm
            src/mac/CocoaJoyStick.mm
            src/mac/CocoaKeyBoard.mm
            src/mac/CocoaMouse.mm
            src/mac/MacHelpers.cpp
            src/mac/MacHIDManager.cpp
            src/mac/MacInputManager.cpp
            src/mac/MacJoyStick.cpp
            src/mac/MacKeyboard.cpp
            src/mac/MacMouse.cpp
        )
    # endif()

elseif (ANDROID)
    set(OIS_PLATFORM android)

else()
    set(OIS_PLATFORM_HEADERS
            include/OIS/linux/EventHelpers.h
            include/OIS/linux/LinuxForceFeedback.h
            include/OIS/linux/LinuxInputManager.h
            include/OIS/linux/LinuxJoyStickEvents.h
            include/OIS/linux/LinuxKeyboard.h
            include/OIS/linux/LinuxMouse.h
            include/OIS/linux/LinuxPrereqs.h
    )

    set(OIS_PLATFORM linux)

    set(OIS_PLATFORM_SOURCES
            src/linux/EventHelpers.cpp
            src/linux/LinuxForceFeedback.cpp
            src/linux/LinuxInputManager.cpp
            src/linux/LinuxJoyStickEvents.cpp
            src/linux/LinuxKeyboard.cpp
            src/linux/LinuxMouse.cpp
    )
endif ()


# List the include paths
set(INCLUDE_PATHS "${OIS_SOURCE_DIR}/include/OIS"
                  "${XMAKE_BINARY_DIR}/include")

include_directories(${INCLUDE_PATHS})


# Treat as Objective-C++ code if under OSX
if(APPLE)
    set_source_files_properties(${OIS_HEADERS} ${OIS_SOURCES} PROPERTIES COMPILE_FLAGS "-x objective-c++")
endif ()


# Declaration of the library
add_definitions(-DOIS_NONCLIENT_BUILD)
xmake_create_static_library(OIS ois ${OIS_HEADERS} ${OIS_SOURCES} ${OIS_PLATFORM_HEADERS} ${OIS_PLATFORM_SOURCES})

if (APPLE)
    xmake_add_to_property(OIS LINK_FLAGS "-framework IOKit -framework Cocoa -framework Carbon")
endif()


# Exportations
xmake_export_include_paths(OIS "${OIS_SOURCE_DIR}/include")
