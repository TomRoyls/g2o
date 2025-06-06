add_library(freeglut_minimal ${G2O_LIB_TYPE}
  freeglut_font.cpp
  freeglut_stroke_mono_roman.cpp
  freeglut_stroke_roman.cpp
)

target_link_libraries(freeglut_minimal PUBLIC ${G2O_OPENGL_TARGET})
target_include_directories(freeglut_minimal PUBLIC
  "$<BUILD_INTERFACE:${g2o_SOURCE_DIR};${g2o_BINARY_DIR}>"
  $<INSTALL_INTERFACE:include/g2o/freeglut_minimal>
)
target_compile_features(freeglut_minimal PUBLIC cxx_std_17)

set_target_properties(freeglut_minimal PROPERTIES OUTPUT_NAME ${LIB_PREFIX}ext_freeglut_minimal)
set_target_properties(freeglut_minimal PROPERTIES
  VERSION ${G2O_LIB_VERSION}
  SOVERSION ${G2O_LIB_SOVERSION})
if (APPLE)
  set_target_properties(freeglut_minimal PROPERTIES INSTALL_NAME_DIR "${CMAKE_INSTALL_PREFIX}/${CMAKE_INSTALL_LIBDIR}")
endif()

install(TARGETS freeglut_minimal
  EXPORT ${G2O_TARGETS_EXPORT_NAME}
  RUNTIME DESTINATION ${RUNTIME_DESTINATION}
  LIBRARY DESTINATION ${LIBRARY_DESTINATION}
  ARCHIVE DESTINATION ${ARCHIVE_DESTINATION}
  INCLUDES DESTINATION ${INCLUDES_DESTINATION}
)

file(GLOB headers "${CMAKE_CURRENT_SOURCE_DIR}/*.h" "${CMAKE_CURRENT_SOURCE_DIR}/*.hpp")
install(FILES ${headers} DESTINATION ${INCLUDES_INSTALL_DIR}/EXTERNAL/freeglut)
