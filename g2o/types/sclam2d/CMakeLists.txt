add_library(types_sclam2d ${G2O_LIB_TYPE}
  edge_se2_sensor_calib.cpp edge_se2_sensor_calib.h
  vertex_odom_differential_params.cpp vertex_odom_differential_params.h
  edge_se2_odom_differential_calib.cpp edge_se2_odom_differential_calib.h
  odometry_measurement.cpp odometry_measurement.h
  types_sclam2d.cpp types_sclam2d.h
  g2o_types_sclam2d_api.h
)

set_target_properties(types_sclam2d PROPERTIES OUTPUT_NAME ${LIB_PREFIX}types_sclam2d)
set_target_properties(types_sclam2d PROPERTIES
  VERSION ${G2O_LIB_VERSION}
  SOVERSION ${G2O_LIB_SOVERSION})
if (APPLE)
  set_target_properties(types_sclam2d PROPERTIES INSTALL_NAME_DIR "${CMAKE_INSTALL_PREFIX}/${CMAKE_INSTALL_LIBDIR}")
endif()
target_compile_features(types_sclam2d PUBLIC cxx_std_17)
target_link_libraries(types_sclam2d types_slam2d core)
if(G2O_HAVE_OPENGL)
  target_link_libraries(types_sclam2d opengl_helper)
endif()

target_include_directories(types_sclam2d PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
  $<INSTALL_INTERFACE:include/g2o/types/sclam2d>)

install(TARGETS types_sclam2d
  EXPORT ${G2O_TARGETS_EXPORT_NAME}
  RUNTIME DESTINATION ${RUNTIME_DESTINATION}
  LIBRARY DESTINATION ${LIBRARY_DESTINATION}
  ARCHIVE DESTINATION ${ARCHIVE_DESTINATION}
)

# Create alias target for easier usage when included as subdirectory
add_library(g2o::types_sclam2d ALIAS types_sclam2d)

file(GLOB headers "${CMAKE_CURRENT_SOURCE_DIR}/*.h" "${CMAKE_CURRENT_SOURCE_DIR}/*.hpp")
install(FILES ${headers} DESTINATION ${INCLUDES_INSTALL_DIR}/types/sclam2d)
