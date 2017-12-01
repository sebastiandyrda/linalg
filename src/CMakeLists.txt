# Locate the dependencies
add_subdirectory(external/ferror)
add_subdirectory(external/lapack)

# Define the source files
set(qrupdate_sources external/qrupdate/dch1dn.f external/qrupdate/dch1up.f 
    external/qrupdate/dlup1up.f external/qrupdate/dqhqr.f 
    external/qrupdate/dqr1up.f external/qrupdate/dqrot.f 
    external/qrupdate/dqrqh.f external/qrupdate/dqrtv1.f)

set(linalg_sources lapack.f90 linalg_c_binding.f90 linalg_constants.f90 
    linalg_core.f90 linalg_eigen.f90 linalg_factor.f90 linalg_solve.f90 
    linalg_sorting.f90)
list(APPEND linalg_sources ${qrupdate_sources})

# Build the library
add_library(linalg ${linalg_sources})
if (WIN32)
    if (BUILD_SHARED_LIBS)
        add_custom_command(
            TARGET linalg
            POST_BUILD
            COMMAND ${CMAKE_COMMAND} -E copy
            "${PROJECT_SOURCE_DIR}/build/src/external/lapack/bin/liblapack.dll"
            ${CMAKE_RUNTIME_OUTPUT_DIRECTORY})
        add_custom_command(
            TARGET linalg
            POST_BUILD
            COMMAND ${CMAKE_COMMAND} -E copy
            "${PROJECT_SOURCE_DIR}/build/src/external/lapack/bin/libblas.dll"
            ${CMAKE_RUNTIME_OUTPUT_DIRECTORY})

        add_custom_command(
            TARGET linalg
            POST_BUILD
            COMMAND ${CMAKE_COMMAND} -E copy
            "${PROJECT_SOURCE_DIR}/build/src/external/lapack/lib/liblapack.dll.a"
            ${CMAKE_RUNTIME_OUTPUT_DIRECTORY})
        add_custom_command(
            TARGET linalg
            POST_BUILD
            COMMAND ${CMAKE_COMMAND} -E copy
            "${PROJECT_SOURCE_DIR}/build/src/external/lapack/lib/libblas.dll.a"
            ${CMAKE_RUNTIME_OUTPUT_DIRECTORY})
        target_link_libraries(linalg ferror lapack blas)
    else()
        add_custom_command(
            TARGET linalg
            POST_BUILD
            COMMAND ${CMAKE_COMMAND} -E copy
            "${PROJECT_SOURCE_DIR}/build/src/external/lapack/lib/liblapack.a"
            ${CMAKE_RUNTIME_OUTPUT_DIRECTORY})
        add_custom_command(
            TARGET linalg
            POST_BUILD
            COMMAND ${CMAKE_COMMAND} -E copy
            "${PROJECT_SOURCE_DIR}/build/src/external/lapack/lib/libblas.a"
            ${CMAKE_RUNTIME_OUTPUT_DIRECTORY})
        target_link_libraries(linalg ferror lapack)
    endif()
else()
    target_link_libraries(linalg ferror lapack)
endif()