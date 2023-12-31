CMAKE_MINIMUM_REQUIRED( VERSION 2.8 ) 
PROJECT( hnode2-document-manager ) 

SET( MAJOR_VERSION 1 )
SET( MINOR_VERSION 0 )
SET( PATCH_VERSION 0 )
SET( RELEASE_VERSION 0 )

SET( DAEMON_NAME hndocmgrd )

SET(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${CMAKE_SOURCE_DIR}/cmake) 

# Default the install prefix to just /usr
IF(CMAKE_INSTALL_PREFIX_INITIALIZED_TO_DEFAULT)
  SET( CMAKE_INSTALL_PREFIX /usr CACHE PATH "Default to /usr install" FORCE )
ENDIF(CMAKE_INSTALL_PREFIX_INITIALIZED_TO_DEFAULT)

SET( VERSION ${MAJOR_VERSION}.${MINOR_VERSION}.${PATCH_VERSION} )
SET( DEBVERSION ${VERSION}-${RELEASE_VERSION} )

ADD_DEFINITIONS( -DVERSION=${VERSION} )

SET( HNDOCMGRD_SRC
     ${CMAKE_SOURCE_DIR}/src/daemon/hndocmgrd.cpp
     ${CMAKE_SOURCE_DIR}/src/daemon/HNDocMgrDevice.cpp
)

SET(CMAKE_BUILD_TYPE Debug)

FIND_PACKAGE( Poco REQUIRED Util Foundation Net NetSSL JSON )
FIND_PACKAGE( HNode2 REQUIRED )

INCLUDE_DIRECTORIES( ${CMAKE_SOURCE_DIR}/src/include )

ADD_EXECUTABLE( hndocmgrd ${HNDOCMGRD_SRC} )
TARGET_LINK_LIBRARIES( hndocmgrd PRIVATE
    ${Poco_Util_LIBRARY}
    ${Poco_Foundation_LIBRARY}
    ${Poco_Net_LIBRARY}
    ${Poco_NetSSL_LIBRARY}
    ${Poco_JSON_LIBRARY}
)
TARGET_LINK_LIBRARIES( hndocmgrd PRIVATE HNode2::common )

INSTALL( TARGETS hndocmgrd DESTINATION ${CMAKE_INSTALL_PREFIX}/sbin COMPONENT daemon )

SET( CPACK_GENERATOR "DEB" )

SET( CPACK_DEB_COMPONENT_INSTALL 1 )
SET( CPACK_COMPONENTS_ALL daemon )

SET( CPACK_PACKAGE_VERSION_MAJOR ${MAJOR_VERSION} )
SET( CPACK_PACKAGE_VERSION_MINOR ${MINOR_VERSION} )
SET( CPACK_PACKAGE_VERSION_PATCH ${PATCH_VERSION} )

SET( CPACK_PACKAGE_DESCRIPTION "A HNode2 device for managing archival documents, such as pdfs, picture, etc." )
SET( CPACK_PACKAGE_DESCRIPTION_SUMMARY "The hnode2 document manager." )
SET( CPACK_PACKAGE_CONTACT "Curtis Nottberg" )
SET( CPACK_PACKAGE_ARCHITECTURE DEB-DEFAULT )

SET( CPACK_DEBIAN_PACKAGE_PRIORITY "optional" )
SET( CPACK_DEBIAN_PACKAGE_SECTION "Daemon" )
SET( CPACK_DEBIAN_PACKAGE_MAINTAINER "Curtis Nottberg" ) #required
SET( CPACK_DEBIAN_PACKAGE_VERSION ${DEBVERSION} )
SET( CPACK_DEBIAN_FILE_NAME DEB-DEFAULT )

INCLUDE(CPack)

