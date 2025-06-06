add_library(stuff ${G2O_LIB_TYPE}
  filesys_tools.h    string_tools.h
  color_macros.h     macros.h         timeutil.cpp
  command_args.cpp   misc.h           sparse_helper.cpp  timeutil.h
  command_args.h     sparse_helper.h
  filesys_tools.cpp  string_tools.cpp
  property.cpp       property.h       tuple_tools.h
  sampler.cpp        sampler.h        unscented.h
  tictoc.cpp tictoc.h
  logger.cpp logger.h
  g2o_stuff_api.h
)

target_include_directories(stuff PUBLIC
  "$<BUILD_INTERFACE:${g2o_SOURCE_DIR};${g2o_BINARY_DIR}>"
  $<INSTALL_INTERFACE:include/g2o/stuff>
)

if (G2O_HAVE_LOGGING)
  if(TARGET spdlog::spdlog)
    target_link_libraries(stuff PUBLIC spdlog::spdlog)
  else()
    target_link_libraries(stuff PUBLIC spdlog::spdlog_header_only)
  endif()
endif()
set_target_properties(stuff PROPERTIES OUTPUT_NAME ${LIB_PREFIX}stuff)
set_target_properties(stuff PROPERTIES
  VERSION ${G2O_LIB_VERSION}
  SOVERSION ${G2O_LIB_SOVERSION})
target_compile_features(stuff PUBLIC cxx_std_17)
target_link_libraries(stuff PUBLIC ${G2O_EIGEN3_EIGEN_TARGET})

if (APPLE)
  set_target_properties(stuff PROPERTIES INSTALL_NAME_DIR "${CMAKE_INSTALL_PREFIX}/${CMAKE_INSTALL_LIBDIR}")
endif()

install(TARGETS stuff
  EXPORT ${G2O_TARGETS_EXPORT_NAME}
  RUNTIME DESTINATION ${RUNTIME_DESTINATION}
  LIBRARY DESTINATION ${LIBRARY_DESTINATION}
  ARCHIVE DESTINATION ${ARCHIVE_DESTINATION}
  INCLUDES DESTINATION ${INCLUDES_DESTINATION}
)

# Create alias target for easier usage when included as subdirectory
add_library(g2o::stuff ALIAS stuff)

# build our OpenGL helper library
if(OPENGL_FOUND AND G2O_HAVE_OPENGL)
  add_library(opengl_helper ${G2O_LIB_TYPE}
    opengl_primitives.cpp opengl_primitives.h
    )
  if (APPLE)
    set_target_properties(opengl_helper PROPERTIES INSTALL_NAME_DIR "${CMAKE_INSTALL_PREFIX}/${CMAKE_INSTALL_LIBDIR}")
  endif()

  install(TARGETS opengl_helper
    EXPORT ${G2O_TARGETS_EXPORT_NAME}
    RUNTIME DESTINATION ${RUNTIME_DESTINATION}
    LIBRARY DESTINATION ${LIBRARY_DESTINATION}
    ARCHIVE DESTINATION ${ARCHIVE_DESTINATION}
    INCLUDES DESTINATION ${INCLUDES_DESTINATION}
    )

  target_include_directories(opengl_helper PUBLIC
    "$<BUILD_INTERFACE:${g2o_SOURCE_DIR};${g2o_BINARY_DIR}>"
    $<INSTALL_INTERFACE:include/g2o/stuff>
  )

  target_link_libraries(opengl_helper PUBLIC ${G2O_OPENGL_TARGET} ${G2O_EIGEN3_EIGEN_TARGET})
  target_compile_features(opengl_helper PUBLIC cxx_std_17)
  set_target_properties(opengl_helper PROPERTIES OUTPUT_NAME "${LIB_PREFIX}opengl_helper")
  set_target_properties(opengl_helper PROPERTIES
    VERSION ${G2O_LIB_VERSION}
    SOVERSION ${G2O_LIB_SOVERSION})

  # Create alias target for easier usage when included as subdirectory
  add_library(g2o::opengl_helper ALIAS opengl_helper)
endif()

if(${CMAKE_SYSTEM_NAME} MATCHES "Linux" AND NOT ANDROID)
  target_link_libraries(stuff PUBLIC rt)
endif()

file(GLOB headers "${CMAKE_CURRENT_SOURCE_DIR}/*.h" "${CMAKE_CURRENT_SOURCE_DIR}/*.hpp")

install(FILES ${headers} DESTINATION ${INCLUDES_INSTALL_DIR}/stuff)
