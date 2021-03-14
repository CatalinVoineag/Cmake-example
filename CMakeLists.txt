cmake_minimum_required(VERSION 3.15.2)

project(test)

add_executable(${PROJECT_NAME} main.cpp)

# Download all submodule
find_package(Git QUIET)
if(GIT_FOUND AND EXISTS "${PROJECT_NAME}/.git")
# Update submodules as needed
	option(GIT_SUBMODULE "Check submodules during build" ON)
	if(GIT_SUBMODULE) 
		message(STATUS "Submodule update")
		execute_process(
			COMMAND ${GIT_EXECUTABLE} submodule update --init --recursive
			WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}
			RESULT_VARIABLE GIT_SUBMOD_RESULT
		)
		if(NOT GIT_SUBMOD_RESULT EQUAL "0")
			message(FATAL_ERROR "git submodule update --init failed with ${GIT_SUBMOD_RESULT}")
		endif()
	endif()
endif()

# Check all submodules
if(NOT EXISTS "${PROJECT_SOURCE_DIR}/external/glfw/CMakeLists.txt")
	message(FATAL_ERROR "The glfw submodules was not downloaded! GIT_SUBMODULE was turned off or failed")
endif()
						

add_subdirectory(Adder)
add_subdirectory(external/glfw)

target_include_directories(
	${PROJECT_NAME}
	PUBLIC Adder 
	PUBLIC external/glfw/include
)

target_link_directories(
	${PROJECT_NAME}
	PRIVATE Adder
	PRIVATE external/glfw/src
)

target_link_libraries(
	${PROJECT_NAME}
	adder
	glfw
)
