cmake_minimum_required(VERSION 3.26)
set(JMETER_VERSION 5.6)
project(JMeter
  VERSION ${JMETER_VERSION}
  DESCRIPTION "Apache JMeter packaged by DEVCOM"
  HOMEPAGE_URL https://github.com/DevelopersCommunity/cmake-jmeter
  LANGUAGES NONE
  )

set(JMETER_TARBALL apache-jmeter-${CMAKE_PROJECT_VERSION}.zip)
set(JMETER_TARBALL_SHA512 c9887eb821ae7381b8d1f90aca93560bb6c8c5294ad7e9c615dbed0e2bd5556afdb68134a63c9aa07f4a54d07fb6281930a68f98e1f9359a79af70d99ccbe59e)

if(NOT EXISTS ${CMAKE_CURRENT_LIST_DIR}/${JMETER_TARBALL})
  file(DOWNLOAD https://dlcdn.apache.org//jmeter/binaries/${JMETER_TARBALL}
    ${CMAKE_CURRENT_LIST_DIR}/${JMETER_TARBALL}
    EXPECTED_HASH SHA512=${JMETER_TARBALL_SHA512}
    )
  file(ARCHIVE_EXTRACT INPUT ${CMAKE_CURRENT_LIST_DIR}/${JMETER_TARBALL}
    DESTINATION ${CMAKE_CURRENT_LIST_DIR}
    )
endif()

if(WIN32)
  set(JMETER_LOGO jmeter.svg)
  if(NOT EXISTS ${CMAKE_CURRENT_LIST_DIR}/${JMETER_LOGO})
    file(DOWNLOAD https://svn.apache.org/repos/asf/comdev/project-logos/originals/${JMETER_LOGO}
      ${CMAKE_CURRENT_LIST_DIR}/${JMETER_LOGO}
      )
  endif()

  add_custom_command(OUTPUT jmeter.ico
    COMMAND magick ${CMAKE_CURRENT_LIST_DIR}/${JMETER_LOGO} -gravity Center -extent 256x256 jmeter.ico
    MAIN_DEPENDENCY ${CMAKE_CURRENT_LIST_DIR}/${JMETER_LOGO}
    COMMENT "Generating JMeter icon"
    )
  add_custom_target(jmetericon ALL DEPENDS jmeter.ico)
endif()

set(JMETER_DIR ${CMAKE_CURRENT_LIST_DIR}/apache-jmeter-${CMAKE_PROJECT_VERSION})

install(FILES
  ${JMETER_DIR}/bin/ApacheJMeter.jar
  ${JMETER_DIR}/bin/BeanShellAssertion.bshrc
  ${JMETER_DIR}/bin/BeanShellFunction.bshrc
  ${JMETER_DIR}/bin/BeanShellListeners.bshrc
  ${JMETER_DIR}/bin/BeanShellSampler.bshrc
  ${JMETER_DIR}/bin/hc.parameters
  ${JMETER_DIR}/bin/jaas.conf
  ${JMETER_DIR}/bin/jmeter.properties
  ${JMETER_DIR}/bin/krb5.conf
  ${JMETER_DIR}/bin/log4j2.xml
  ${JMETER_DIR}/bin/reportgenerator.properties
  ${JMETER_DIR}/bin/saveservice.properties
  ${JMETER_DIR}/bin/system.properties
  ${JMETER_DIR}/bin/upgrade.properties
  ${JMETER_DIR}/bin/user.properties
  ${JMETER_DIR}/bin/upgrade.properties
  ${JMETER_DIR}/bin/utility.groovy
  DESTINATION bin
  )

install(PROGRAMS
  ${JMETER_DIR}/bin/create-rmi-keystore.bat
  ${JMETER_DIR}/bin/create-rmi-keystore.sh
  ${JMETER_DIR}/bin/heapdump.cmd
  ${JMETER_DIR}/bin/heapdump.sh
  ${JMETER_DIR}/bin/jmeter
  ${JMETER_DIR}/bin/jmeter.bat
  ${JMETER_DIR}/bin/jmeter.sh
  ${JMETER_DIR}/bin/jmeter-n.cmd
  ${JMETER_DIR}/bin/jmeter-n-r.cmd
  ${JMETER_DIR}/bin/jmeter-server
  ${JMETER_DIR}/bin/jmeter-server.bat
  ${JMETER_DIR}/bin/jmeter-t.cmd
  ${JMETER_DIR}/bin/jmeterw.cmd
  ${JMETER_DIR}/bin/mirror-server
  ${JMETER_DIR}/bin/mirror-server.cmd
  ${JMETER_DIR}/bin/mirror-server.sh
  ${JMETER_DIR}/bin/shutdown.cmd
  ${JMETER_DIR}/bin/shutdown.sh
  ${JMETER_DIR}/bin/stoptest.cmd
  ${JMETER_DIR}/bin/stoptest.sh
  ${JMETER_DIR}/bin/threaddump.cmd
  ${JMETER_DIR}/bin/threaddump.sh
  TYPE BIN
  )

install(DIRECTORY ${JMETER_DIR}/bin/examples DESTINATION bin)
install(DIRECTORY ${JMETER_DIR}/bin/report-template DESTINATION bin)
install(DIRECTORY ${JMETER_DIR}/bin/templates DESTINATION bin)
install(DIRECTORY ${JMETER_DIR}/docs DESTINATION .)
install(DIRECTORY ${JMETER_DIR}/extras DESTINATION .)
install(DIRECTORY ${JMETER_DIR}/lib DESTINATION .)
install(DIRECTORY ${JMETER_DIR}/licenses DESTINATION .)
install(DIRECTORY ${JMETER_DIR}/printable_docs DESTINATION .)
install(FILES ${JMETER_DIR}/LICENSE ${JMETER_DIR}/NOTICE ${JMETER_DIR}/README.md
  DESTINATION .)

if(WIN32)
  set(CPACK_GENERATOR WIX)
  set(CPACK_WIX_UPGRADE_GUID "5859d326-2961-4596-870e-d8bf7c35b2c3")
  set(CPACK_WIX_PROGRAM_MENU_FOLDER "${CPACK_PACKAGE_NAME}")
  set(CPACK_WIX_PRODUCT_ICON jmeter.ico)
  list(APPEND CPACK_WIX_EXTENSIONS WixUtilExtension)
  list(APPEND CPACK_WIX_PATCH_FILE ${CMAKE_CURRENT_LIST_DIR}/WixPatch.xml)
endif()
file(COPY_FILE ${JMETER_DIR}/LICENSE ${JMETER_DIR}/LICENSE.txt)
set(CPACK_RESOURCE_FILE_LICENSE ${JMETER_DIR}/LICENSE.txt)
set(CPACK_PACKAGE_INSTALL_DIRECTORY ${CMAKE_PROJECT_NAME})
set(CPACK_PACKAGE_VENDOR DEVCOM)
include(CPack)
