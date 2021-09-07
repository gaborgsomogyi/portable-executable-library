set(PROJECT_NAME pe_bliss)

################################################################################
# Source groups
################################################################################
set(no_group_source_files
    "readme.txt"
)
source_group("" FILES ${no_group_source_files})

set(Header_Files
    "pe_base.h"
    "pe_bliss.h"
    "pe_bliss_resources.h"
    "pe_exception.h"
    "pe_factory.h"
    "pe_properties.h"
    "pe_properties_generic.h"
    "pe_rebuilder.h"
    "pe_section.h"
    "pe_structures.h"
    "stdint_defs.h"
)
source_group("Header Files" FILES ${Header_Files})

set(Header_Files__Other
    "entropy.h"
    "pe_checksum.h"
    "pe_rich_data.h"
    "utils.h"
)
source_group("Header Files\\Other" FILES ${Header_Files__Other})

set(Header_Files__PE_Directories
    "pe_bound_import.h"
    "pe_debug.h"
    "pe_directory.h"
    "pe_dotnet.h"
    "pe_exception_directory.h"
    "pe_exports.h"
    "pe_imports.h"
    "pe_load_config.h"
    "pe_relocations.h"
    "pe_resources.h"
    "pe_tls.h"
)
source_group("Header Files\\PE Directories" FILES ${Header_Files__PE_Directories})

set(Header_Files__PE_Resources
    "file_version_info.h"
    "message_table.h"
    "pe_resource_manager.h"
    "pe_resource_viewer.h"
    "resource_bitmap_reader.h"
    "resource_bitmap_writer.h"
    "resource_cursor_icon_reader.h"
    "resource_cursor_icon_writer.h"
    "resource_data_info.h"
    "resource_internal.h"
    "resource_message_list_reader.h"
    "resource_string_table_reader.h"
    "resource_version_info_reader.h"
    "resource_version_info_writer.h"
    "version_info_editor.h"
    "version_info_types.h"
    "version_info_viewer.h"
)
source_group("Header Files\\PE Resources" FILES ${Header_Files__PE_Resources})

set(Source_Files
    "pe_base.cpp"
    "pe_exception.cpp"
    "pe_factory.cpp"
    "pe_properties.cpp"
    "pe_properties_generic.cpp"
    "pe_rebuilder.cpp"
    "pe_section.cpp"
)
source_group("Source Files" FILES ${Source_Files})

set(Source_Files__Other
    "entropy.cpp"
    "pe_checksum.cpp"
    "pe_rich_data.cpp"
    "utils.cpp"
)
source_group("Source Files\\Other" FILES ${Source_Files__Other})

set(Source_Files__PE_Directories
    "pe_bound_import.cpp"
    "pe_debug.cpp"
    "pe_directory.cpp"
    "pe_dotnet.cpp"
    "pe_exception_directory.cpp"
    "pe_exports.cpp"
    "pe_imports.cpp"
    "pe_load_config.cpp"
    "pe_relocations.cpp"
    "pe_resources.cpp"
    "pe_tls.cpp"
)
source_group("Source Files\\PE Directories" FILES ${Source_Files__PE_Directories})

set(Source_Files__PE_Resources
    "file_version_info.cpp"
    "message_table.cpp"
    "pe_resource_manager.cpp"
    "pe_resource_viewer.cpp"
    "resource_bitmap_reader.cpp"
    "resource_bitmap_writer.cpp"
    "resource_cursor_icon_reader.cpp"
    "resource_cursor_icon_writer.cpp"
    "resource_data_info.cpp"
    "resource_message_list_reader.cpp"
    "resource_string_table_reader.cpp"
    "resource_version_info_reader.cpp"
    "resource_version_info_writer.cpp"
    "version_info_editor.cpp"
    "version_info_viewer.cpp"
)
source_group("Source Files\\PE Resources" FILES ${Source_Files__PE_Resources})

set(ALL_FILES
    ${no_group_source_files}
    ${Header_Files}
    ${Header_Files__Other}
    ${Header_Files__PE_Directories}
    ${Header_Files__PE_Resources}
    ${Source_Files}
    ${Source_Files__Other}
    ${Source_Files__PE_Directories}
    ${Source_Files__PE_Resources}
)

################################################################################
# Target
################################################################################
add_library(${PROJECT_NAME} STATIC ${ALL_FILES})
set_target_properties(${PROJECT_NAME} PROPERTIES FOLDER "PE Library")

use_props(${PROJECT_NAME} "${CMAKE_CONFIGURATION_TYPES}" "${DEFAULT_CXX_PROPS}")
set(ROOT_NAMESPACE pe_bliss)

set_target_properties(${PROJECT_NAME} PROPERTIES
    VS_GLOBAL_KEYWORD "Win32Proj"
)
if("${CMAKE_VS_PLATFORM_NAME}" STREQUAL "Win32")
    set_target_properties(${PROJECT_NAME} PROPERTIES
        INTERPROCEDURAL_OPTIMIZATION_RELEASE "TRUE"
    )
elseif("${CMAKE_VS_PLATFORM_NAME}" STREQUAL "x64")
    set_target_properties(${PROJECT_NAME} PROPERTIES
        INTERPROCEDURAL_OPTIMIZATION_RELEASE "TRUE"
    )
endif()
################################################################################
# MSVC runtime library
################################################################################
get_property(MSVC_RUNTIME_LIBRARY_DEFAULT TARGET ${PROJECT_NAME} PROPERTY MSVC_RUNTIME_LIBRARY)
if("${CMAKE_VS_PLATFORM_NAME}" STREQUAL "Win32")
    string(CONCAT "MSVC_RUNTIME_LIBRARY_STR"
        $<$<CONFIG:Debug>:
            MultiThreadedDebug
        >
        $<$<CONFIG:Release>:
            MultiThreaded
        >
        $<$<NOT:$<OR:$<CONFIG:Debug>,$<CONFIG:Release>>>:${MSVC_RUNTIME_LIBRARY_DEFAULT}>
    )
elseif("${CMAKE_VS_PLATFORM_NAME}" STREQUAL "x64")
    string(CONCAT "MSVC_RUNTIME_LIBRARY_STR"
        $<$<CONFIG:Debug>:
            MultiThreadedDebug
        >
        $<$<CONFIG:Release>:
            MultiThreaded
        >
        $<$<NOT:$<OR:$<CONFIG:Debug>,$<CONFIG:Release>>>:${MSVC_RUNTIME_LIBRARY_DEFAULT}>
    )
endif()
set_target_properties(${PROJECT_NAME} PROPERTIES MSVC_RUNTIME_LIBRARY ${MSVC_RUNTIME_LIBRARY_STR})

################################################################################
# Compile definitions
################################################################################
if("${CMAKE_VS_PLATFORM_NAME}" STREQUAL "Win32")
    target_compile_definitions(${PROJECT_NAME} PRIVATE
        "$<$<CONFIG:Debug>:"
            "_DEBUG"
        ">"
        "$<$<CONFIG:Release>:"
            "NDEBUG"
        ">"
        "WIN32;"
        "_LIB;"
        "UNICODE;"
        "_UNICODE"
    )
elseif("${CMAKE_VS_PLATFORM_NAME}" STREQUAL "x64")
    target_compile_definitions(${PROJECT_NAME} PRIVATE
        "$<$<CONFIG:Debug>:"
            "_DEBUG"
        ">"
        "$<$<CONFIG:Release>:"
            "NDEBUG"
        ">"
        "_WIN64;"
        "_LIB;"
        "UNICODE;"
        "_UNICODE"
    )
endif()

################################################################################
# Compile and link options
################################################################################
if(MSVC)
    if("${CMAKE_VS_PLATFORM_NAME}" STREQUAL "Win32")
        target_compile_options(${PROJECT_NAME} PRIVATE
            $<$<CONFIG:Debug>:
                /Od
            >
            $<$<CONFIG:Release>:
                /O2;
                /Oi;
                /Gy
            >
            /W3;
            ${DEFAULT_CXX_DEBUG_INFORMATION_FORMAT};
            ${DEFAULT_CXX_EXCEPTION_HANDLING};
            /Y-
        )
    elseif("${CMAKE_VS_PLATFORM_NAME}" STREQUAL "x64")
        target_compile_options(${PROJECT_NAME} PRIVATE
            $<$<CONFIG:Debug>:
                /Od
            >
            $<$<CONFIG:Release>:
                /O2;
                /Oi;
                /Gy
            >
            /W3;
            ${DEFAULT_CXX_DEBUG_INFORMATION_FORMAT};
            ${DEFAULT_CXX_EXCEPTION_HANDLING};
            /Y-
        )
    endif()
    if("${CMAKE_VS_PLATFORM_NAME}" STREQUAL "Win32")
        target_link_options(${PROJECT_NAME} PRIVATE
            $<$<CONFIG:Release>:
                /OPT:REF;
                /OPT:ICF
            >
            /SUBSYSTEM:WINDOWS
        )
    elseif("${CMAKE_VS_PLATFORM_NAME}" STREQUAL "x64")
        target_link_options(${PROJECT_NAME} PRIVATE
            $<$<CONFIG:Release>:
                /OPT:REF;
                /OPT:ICF
            >
            /SUBSYSTEM:WINDOWS
        )
    endif()
endif()

