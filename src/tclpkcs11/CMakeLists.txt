SET(LIBRARY_NAME ${CMAKE_PROJECT_NAME})

ADD_LIBRARY(
#ADD_EXECUTABLE(
    ${LIBRARY_NAME}
    SHARED
#    STATIC
    gost_r3411_2012.h
    hash_bit.h
    tclpkcs11.c
    gost_r3411_2012.c
    pkcs11_wrapper.def
)


IF (CMAKE_HOST_WIN32)
    SET(CW_LIRBASE "${CMAKE_CURRENT_SOURCE_DIR}/tclpkcs11-windows")
    IF (CMAKE_SIZEOF_VOID_P EQUAL 8) # 64-bit
        SET(CW_ARCH "x86_64")
    ELSE (CMAKE_SIZEOF_VOID_P EQUAL 8) #32-bit
        SET(CW_ARCH "x86")
    ENDIF (CMAKE_SIZEOF_VOID_P EQUAL 8)
    
ELSEIF (CMAKE_HOST_APPLE)
    set_target_properties(${LIBRARY_NAME} PROPERTIES PREFIX "")
    SET(CW_LIRBASE "${CMAKE_CURRENT_SOURCE_DIR}/tclpkcs11-mac")
# TODO: OS X version
ELSEIF (CMAKE_HOST_UNIX)
#    set_target_properties(target-name PROPERTIES PREFIX "")
    set_target_properties(${LIBRARY_NAME} PROPERTIES PREFIX "")
    SET(CW_LIRBASE "${CMAKE_CURRENT_SOURCE_DIR}/tclpkcs11-linux")

#VREMENNO
#    SET(CW_LIRBASE "${CMAKE_CURRENT_SOURCE_DIR}/lir-linux-nacl")

    IF (CMAKE_SIZEOF_VOID_P EQUAL 8) # 64-bit
        SET(CW_ARCH "x86_64")
    ELSE (CMAKE_SIZEOF_VOID_P EQUAL 8) #32-bit
        SET(CW_ARCH "x86")
    ENDIF (CMAKE_SIZEOF_VOID_P EQUAL 8)
ENDIF (CMAKE_HOST_WIN32)

# 32 or 64
#if (64BIT)
#	set (CW_ARCH x86_64)
#else (64BIT)
#	set (CW_ARCH x86)
#endif (64BIT)
# FOR x86_64
#	set (CW_ARCH x86_64)


INCLUDE_DIRECTORIES(
    IF (CMAKE_HOST_WIN32)
	C:/Tcl/include
    ENDIF (CMAKE_HOST_WIN32)

    ${CW_LIRBASE}/include/pkcs11
)

#Проверяем Android
if( ${ANDROID_ABI} MATCHES "armeabi-v7a" )
	    target_link_libraries(${LIBRARY_NAME} ${CMAKE_CURRENT_SOURCE_DIR}/tclpkcs11-android/armeabi-v7a/libtclstub8.6.a)
else  ( ${ANDROID_ABI} MATCHES "armeabi-v7a" )

SET(CW_LIB_PREFIX "${CW_LIRBASE}/${CW_ARCH}/lib/")

IF (CMAKE_HOST_WIN32)
    TARGET_LINK_LIBRARIES(
        ${LIBRARY_NAME}

#	${CW_LIB_PREFIX}/tcl/tclstub85.lib
	${CW_LIB_PREFIX}/tcl/tclstub86.lib
#CryptoAPI
    )
ELSEIF (CMAKE_HOST_APPLE)
# TODO: OS X version
    TARGET_LINK_LIBRARIES(
        ${LIBRARY_NAME}
	${CW_LIB_PREFIX}/tcl/libtclstub.a

    )
ELSEIF (CMAKE_HOST_UNIX)
    TARGET_LINK_LIBRARIES(
        ${LIBRARY_NAME}
#	${CW_LIB_PREFIX}/tcl/libtclstub8.6.a
	${CW_LIB_PREFIX}/tcl/libtclstub.a
        dl
    )
ENDIF (CMAKE_HOST_WIN32)

endif  ( ${ANDROID_ABI} MATCHES "armeabi-v7a" )
