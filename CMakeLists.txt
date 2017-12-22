cmake_minimum_required(VERSION 3.0)

project(ibl)

set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

add_subdirectory(bgfx.cmake)

set( SHADERS "" )
set( SHADER_DIR "" )
list( APPEND SHADER_DIR "${CMAKE_CURRENT_SOURCE_DIR}/common" )
list( APPEND SHADER_DIR "${CMAKE_CURRENT_SOURCE_DIR}/01-cubes" )
foreach( DIR ${SHADER_DIR} )
	file( GLOB GLOB_SHADERS ${DIR}/*.sc )
	list( APPEND SHADERS ${GLOB_SHADERS} )
endforeach()

foreach(FILE ${SHADERS})
	get_filename_component( FILENAME "${FILE}" NAME_WE )
	string( SUBSTRING "${FILENAME}" 0 2 TYPE )
	if( "${TYPE}" STREQUAL "fs" )
		set( TYPE "FRAGMENT" )
		set( D3D_PREFIX "ps" )
	elseif( "${TYPE}" STREQUAL "vs" )
		set( TYPE "VERTEX" )
		set( D3D_PREFIX "vs" )
	else()
		set( TYPE "" )
	endif()
	if( NOT "${TYPE}" STREQUAL "" )
		set( COMMON FILE ${FILE} ${TYPE} INCLUDES ${BGFX_DIR}/src )
		set( OUTPUTS "" )
		set( OUTPUTS_PRETTY "" )
		# glsl
		set( GLSL_OUTPUT ${CMAKE_CURRENT_BINARY_DIR}/shaders/glsl/${FILENAME}.bin )
		shaderc_parse( GLSL ${COMMON} LINUX PROFILE 120 OUTPUT ${GLSL_OUTPUT} )
		list( APPEND OUTPUTS "GLSL" )
		set( OUTPUTS_PRETTY "${OUTPUTS_PRETTY}GLSL" )
		set( OUTPUT_FILES "" )
		set( COMMANDS "" )
		foreach( OUT ${OUTPUTS} )
			list( APPEND OUTPUT_FILES ${${OUT}_OUTPUT} )
			list( APPEND COMMANDS COMMAND "$<TARGET_FILE:shaderc>" ${${OUT}} )
			get_filename_component( OUT_DIR ${${OUT}_OUTPUT} DIRECTORY )
			file( MAKE_DIRECTORY ${OUT_DIR} )
		endforeach()
		add_custom_command(
			MAIN_DEPENDENCY
			${FILE}
			OUTPUT
			${OUTPUT_FILES}
			${COMMANDS}
			COMMENT "Compiling shader ${FILE} for ${OUTPUTS_PRETTY}"
		)
	endif()
	list(APPEND SHADER_BINARY_FILES ${OUTPUT_FILES})
endforeach(FILE)
add_custom_target( Shaders DEPENDS ${SHADER_BINARY_FILES})
file(GLOB SRC "./01-cubes/cubes.cpp")
add_executable(cubes ${SRC})
add_dependencies(cubes Shaders)
add_definitions(-DENTRY_CONFIG_IMPLEMENT_MAIN=1)
target_include_directories(cubes PUBLIC bgfx/examples/01-cubes)
target_link_libraries(cubes example-common)
