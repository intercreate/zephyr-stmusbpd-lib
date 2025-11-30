
zephyr_library_named(usbpdstm32)

add_dependencies(${ZEPHYR_CURRENT_LIBRARY} zephyr_generated_headers)

target_link_libraries(
  app PUBLIC
  ${CMAKE_CURRENT_SOURCE_DIR}/Core/lib/USBPDCORE_PD3_FULL_CM0PLUS_wc32.a
  ${ZEPHYR_CURRENT_LIBRARY}
  )

zephyr_compile_definitions(
    -DUSBPD_PORT_COUNT=CONFIG_STM32_XCUBEUSBPD_PORTNUM
    -DUSBPDCORE_LIB_PD3_FULL
    -D_ERROR_RECOVERY
    -D_RTOS
    )

zephyr_compile_definitions_ifdef(CONFIG_STM32_XCUBEUSBPD_HAS_SNK_SUPPORT _SNK)
zephyr_compile_definitions_ifdef(CONFIG_STM32_XCUBEUSBPD_HAS_SRC_SUPPORT _SRC)
zephyr_compile_definitions_ifdef(CONFIG_STM32_XCUBEUSBPD_HAS_DRP_SUPPORT _DRP)
zephyr_compile_definitions_ifdef(CONFIG_STM32_XCUBEUSBPD_HAS_VDM_SUPPORT _VDM)

zephyr_library_include_directories(
    Core/inc
    )

zephyr_library_sources(
    Core/src/usbpd_dpm_core.c
    )

set(stm_socs
    stm32g0x
    )

foreach(stm_soc ${stm_socs})
    string(TOUPPER ${stm_soc} SOC_TO_UPPER)
    if(CONFIG_SOC_SERIES_${SOC_TO_UPPER})
        zephyr_library_include_directories(Devices/${SOC_TO_UPPER}X/inc)
        zephyr_include_directories(Devices/${SOC_TO_UPPER}X/inc)
        add_subdirectory(Devices/${SOC_TO_UPPER}X/src)
    endif()
endforeach()
