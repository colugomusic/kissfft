cmake_minimum_required(VERSION 3.24)

project(kissfft LANGUAGES C)

find_package(OpenMP REQUIRED COMPONENTS C)

add_library(kissfft STATIC)
add_library(kissfft::kissfft ALIAS kissfft)

target_sources(kissfft
	PRIVATE
		kiss_fft.c
		kiss_fftr.c
		kiss_fftnd.c
		kiss_fftndr.c
		kfc.c
	PUBLIC
		FILE_SET HEADERS
		BASE_DIRS
			${CMAKE_CURRENT_SOURCE_DIR}
		FILES
			kiss_fft.h
			kiss_fftr.h
			kiss_fftnd.h
			kiss_fftndr.h
)

target_include_directories(kissfft PUBLIC
	$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
	$<INSTALL_INTERFACE:include>
)

target_compile_definitions(kissfft PUBLIC
	kiss_fft_scalar=float
)

if(OpenMP_C_FOUND)
	target_link_libraries(kissfft PUBLIC OpenMP::OpenMP_C)
endif()

include(GNUInstallDirs)

include(CMakePackageConfigHelpers)
install(TARGETS kissfft
	EXPORT kissfft-targets
	FILE_SET HEADERS DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/kissfft
	ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
)
install(EXPORT kissfft-targets
	FILE kissfft-targets.cmake
	NAMESPACE kissfft::
	DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/kissfft
)
configure_package_config_file(
    "${CMAKE_CURRENT_LIST_DIR}/cmake/kissfft-config.cmake.in"
    "${CMAKE_CURRENT_BINARY_DIR}/kissfft-config.cmake"
    INSTALL_DESTINATION lib/cmake/kissfft
)
install(FILES "${CMAKE_CURRENT_BINARY_DIR}/kissfft-config.cmake" DESTINATION lib/cmake/kissfft)
