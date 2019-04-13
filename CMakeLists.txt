cmake_minimum_required (VERSION 3.8)

include (ExternalProject)

set (GENGINE_RELEASE_NAME "gengine-superbuild")
set (GENGINE_RELEASE_VERSION "0.0.1")

project ("${GENGINE_RELEASE_NAME}" VERSION "${GENGINE_RELEASE_VERSION}" LANGUAGES CXX)

if ("${PROJECT_SOURCE_DIR}" STREQUAL "${PROJECT_BINARY_DIR}")

	message (FATAL_ERROR "In-tree builds are neither supported nor recommended.  See README.md for building information.")

endif ()

list (APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_LIST_DIR}/cmake")

set (GENGINE_INSTALL_PREFIX "${CMAKE_CURRENT_LIST_DIR}/dist")

set (GENGINE_DEFAULT_ARGS
	"-DCMAKE_PREFIX_PATH:PATH=${GENGINE_INSTALL_PREFIX};${CMAKE_PREFIX_PATH}"
	"-DCMAKE_INSTALL_PREFIX:PATH=${GENGINE_INSTALL_PREFIX}"
)

function (build_local)

	set (one_value_args NAME)
	set (multi_value_args DEPENDS)
	cmake_parse_arguments (NEW_PROJECT "" "${one_value_args}" "${multi_value_args}" ${ARGN})

	ExternalProject_Add ("${NEW_PROJECT_NAME}"
		SOURCE_DIR "${CMAKE_CURRENT_SOURCE_DIR}/source/${NEW_PROJECT_NAME}"
		CMAKE_CACHE_ARGS ${GENGINE_DEFAULT_ARGS}
		BUILD_ALWAYS TRUE
		DEPENDS "${NEW_PROJECT_DEPENDS}"
	)

endfunction ()

function (build_from_git)

	set (one_value_args NAME REPO)
	set (multi_value_args DEPENDS)
	cmake_parse_arguments (NEW_PROJECT "" "${one_value_args}" "${multi_value_args}" ${ARGN})

	ExternalProject_Add("${NEW_PROJECT_NAME}"
		GIT_REPOSITORY "https://github.com/${NEW_PROJECT_REPO}"
		GIT_TAG origin/master
		CMAKE_CACHE_ARGS ${GENGINE_DEFAULT_ARGS}
		BUILD_ALWAYS TRUE
		DEPENDS "${NEW_PROJECT_DEPENDS}"
	)

endfunction ()

include (projects)
