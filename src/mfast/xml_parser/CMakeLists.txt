file (GLOB_RECURSE headers CONFIGURE_DEPENDS "*.h") ## retrieve all header files in current directory
file (GLOB_RECURSE sources CONFIGURE_DEPENDS "*.cpp")     ## retrieve all source files in current directory

find_package(tinyxml2 CONFIG REQUIRED)

add_library(mfast_xml_parser_static STATIC)
target_sources(mfast_xml_parser_static PRIVATE ${sources})
target_sources(mfast_xml_parser_static PUBLIC FILE_SET HEADERS BASE_DIRS ${PROJECT_SOURCE_DIR}/src FILES ${headers})
target_link_libraries(mfast_xml_parser_static PUBLIC mfast_static tinyxml2::tinyxml2)
target_compile_definitions(mfast_xml_parser_static PUBLIC MFAST_CODER_STATIC_DEFINE)

if (UNIX)
  set_target_properties(mfast_xml_parser_static PROPERTIES OUTPUT_NAME mfast_xml_parser)
endif()

install(TARGETS mfast_xml_parser_static 
        EXPORT  mFASTTargets
        FILE_SET HEADERS)

if (BUILD_SHARED_LIBS)
  add_library(mfast_xml_parser SHARED)
  target_sources(mfast_xml_parser PRIVATE ${sources})
  target_sources(mfast_xml_parser PUBLIC FILE_SET HEADERS BASE_DIRS ${PROJECT_SOURCE_DIR}/src FILES ${headers})
  target_compile_definitions(mfast_xml_parser INTERFACE MFAST_DYN_LINK)
  target_link_libraries(mfast_xml_parser PUBLIC mfast PRIVATE tinyxml2::tinyxml2)
  set_property(TARGET mfast_xml_parser PROPERTY VERSION ${MFAST_VERSION})
  set_property(TARGET mfast_xml_parser PROPERTY SOVERSION ${MFAST_VERSION})

  install(TARGETS mfast_xml_parser
          EXPORT  mFASTTargets
          FILE_SET HEADERS)
endif (BUILD_SHARED_LIBS)