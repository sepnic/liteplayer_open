set(TOP_DIR ${CMAKE_CURRENT_SOURCE_DIR}/../../../..)

IF(CONFIG_IDF_TARGET STREQUAL "esp32")
    set(ESP_TARGET_CHIP "esp32")
ELSEIF(CONFIG_IDF_TARGET STREQUAL "esp32s3")
    set(ESP_TARGET_CHIP "esp32s3")
ENDIF()

set(COMPONENT_ADD_INCLUDEDIRS ${TOP_DIR}/include)

set(COMPONENT_SRCS dummy.c)

register_component()

target_link_libraries(${COMPONENT_TARGET} "-L ${TOP_DIR}/lib/${ESP_TARGET_CHIP}")

IF(IDF_VER MATCHES "v4.")
    add_prebuilt_library(liteplayer_core "${TOP_DIR}/lib/${ESP_TARGET_CHIP}/libliteplayer_core.a"
            PRIV_REQUIRES liteplayercore)
ENDIF()

target_link_libraries(${COMPONENT_TARGET} "-Wl,--start-group" liteplayer_core "-Wl,--end-group")
