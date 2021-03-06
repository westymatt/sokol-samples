# a dummy library with the system library dependencies
macro(sokol_system_deps)
    if (FIPS_OSX)
        if (FIPS_IOS)
            if (SOKOL_USE_METAL)
                fips_frameworks_osx(UIKit Metal MetalKit AudioToolbox)
            else()
                fips_frameworks_osx(UIKit OpenGLES GLKit AudioToolbox)
            endif()
        else()
            if (SOKOL_USE_METAL)
                fips_frameworks_osx(Cocoa QuartzCore Metal MetalKit AudioToolbox)
            else()
                fips_frameworks_osx(Cocoa QuartzCore OpenGL AudioToolbox)
            endif()
        endif()
    else()
        if (FIPS_ANDROID)
            fips_libs(GLESv3 EGL OpenSLES log android)
        elseif (FIPS_LINUX)
            fips_libs(X11 Xi Xcursor GL m dl asound)
        endif()
    endif()
endmacro()

# the general sokol implementations library (compiled as C, C++ or ObjC depending on platform)
fips_begin_lib(sokol)
    fips_vs_warning_level(4)
    if (FIPS_OSX)
        fips_files(sokol.m)
    elseif (FIPS_UWP)
        fips_files(sokol.cc)
    else()
        fips_files(sokol.c)
    endif()
    sokol_system_deps()
fips_end_lib()

# the sokol implementations library (compiled as C++)
fips_begin_lib(sokol-cpp)
    fips_vs_warning_level(4)
    if (FIPS_OSX)
        fips_files(sokol.mm)
    else()
        fips_files(sokol.cc)
    endif()
    sokol_system_deps()
fips_end_lib()

# the sokol implementations library with SOKOL_NO_ENTRY support
if (NOT FIPS_ANDROID AND NOT FIPS_UWP)
fips_begin_lib(sokol-noentry)
    fips_vs_warning_level(4)
    if (FIPS_OSX)
        fips_files(sokol-noentry.m)
    else()
        fips_files(sokol-noentry.c)
    endif()
    sokol_system_deps()
fips_end_lib()
endif()

# a variant with memory tracking for leak-detection
fips_begin_lib(sokol-memtrack)
    fips_vs_warning_level(4)
    if (FIPS_OSX)
        fips_files(sokol-memtrack.m)
    elseif (FIPS_UWP)
        fips_files(sokol-memtrack.cc)
    else()
        fips_files(sokol-memtrack.c)
    endif()
    sokol_system_deps()
fips_end_lib()

# the sokol implementations library as DLL
# FIXME: implement this also for other platforms
if (FIPS_WINDOWS AND NOT FIPS_UWP)
fips_begin_sharedlib(sokol-dll)
    fips_vs_warning_level(4)
    fips_files(sokol-dll.c)
fips_end_sharedlib()
endif()
