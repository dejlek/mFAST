file (GLOB headers "*.h") ## retrieve all header files in current directory
file (GLOB sources "*.cpp")     ## retrieve all source files in current directory



SET(LIBRARY_OUTPUT_PATH ${PROJECT_BINARY_DIR}/lib)

set(mfast_sqlite3_SRCS  ${sources} ${headers})

add_library(mfast_sqlite3_static STATIC ${mfast_sqlite3_SRCS})

if (UNIX)
	set_target_properties(mfast_sqlite3_static PROPERTIES OUTPUT_NAME mfast_sqlite3)
endif()
set_target_properties(mfast_sqlite3_static PROPERTIES COMPILE_FLAGS -DMFAST_SQLITE3_STATIC_DEFINE)

install(TARGETS mfast_sqlite3_static
        EXPORT  mFASTTargets
        RUNTIME DESTINATION "${INSTALL_BIN_DIR}" COMPONENT bin
		ARCHIVE DESTINATION "${INSTALL_LIB_DIR}" COMPONENT lib)


if (BUILD_SHARED_LIBS)
  add_library(mfast_sqlite3 SHARED ${mfast_sqlite3_SRCS})
  if (CMAKE_COMPILER_IS_GNUCXX OR ("${CMAKE_CXX_COMPILER_ID}" STREQUAL "Clang"))
	set_target_properties(mfast_sqlite3 PROPERTIES COMPILE_FLAGS -fvisibility=hidden)
  endif()
  target_link_libraries(mfast_sqlite3 mfast sqlite3)

  install(TARGETS mfast_sqlite3
          EXPORT  mFASTTargets
          RUNTIME DESTINATION "${INSTALL_BIN_DIR}" COMPONENT bin
  	      LIBRARY DESTINATION "${INSTALL_LIB_DIR}" COMPONENT lib)

  SET(MFAST_DYNAMIC_COMPONENTS ${MFAST_DYNAMIC_COMPONENTS} sqlite3 CACHE INTERNAL "")
endif()

  SET(MFAST_STATIC_COMPONENTS ${MFAST_STATIC_COMPONENTS} sqlite3_static CACHE INTERNAL "")



install(FILES mfast_sqlite3_export.h converter.h field_masks.h
        DESTINATION "${INSTALL_INCLUDE_DIR}/mfast/sqlite3")