CC=cc
CFLAGS += -Os -v

ANDROID_APP_PLATFORM = android-21
NDK_BUILD_FLAGS = V=1

BRANCH=mbedtls-2.14
BASE_URL_H=https://raw.githubusercontent.com/ARMmbed/mbedtls/$(BRANCH)/include/mbedtls
BASE_URL_C=https://raw.githubusercontent.com/ARMmbed/mbedtls/$(BRANCH)/library

aesTest: aesTest.c mbedtls/*.c
	$(CC) $(CFLAGS) -o $@ $^

test: aesTest
	./$< encode abc
	@printf "%b\n" "------------------------------------------"
	./$< decode EBBF70FEF806D11C4F1FA1007597105A

ndk-build:
	ndk-build NDK_PROJECT_PATH=. APP_BUILD_SCRIPT=Android.mk APP_PLATFORM=$(ANDROID_APP_PLATFORM) $(NDK_BUILD_FLAGS)

AES.class: AES.java
	javac -cp . $^

AES-java: AES.class
	java -cp . AES abc

clean:
	rm -rf libs
	rm -rf obj
	rm -f aesTest
	rm -f AES.class

download-src:
	([ -d mbedtls ] || mkdir -p mbedtls) && cd mbedtls && \
	printf "%b\n" "#define MBEDTLS_AES_C\n#define MBEDTLS_CIPHER_MODE_CBC" > config.h && { \
        for item in aes.h platform_util.h platform.h threading.h; \
        do \
            curl -LO $(BASE_URL_H)/$${item}; \
        done \
    } && { \
        for item in aes.c platform_util.c platform.c threading.c; \
        do \
            curl -LO $(BASE_URL_C)/$${item}; \
        done \
    } && \
	curl -LO https://raw.githubusercontent.com/leleliu008/C/master/algorithm/base16/base16.h && \
	curl -LO https://raw.githubusercontent.com/leleliu008/C/master/algorithm/base16/base16.c && \
    sed -i    's@mbedtls/@@g' * 2> /dev/null || \
    sed -i "" 's@mbedtls/@@g' *

.PHONY: ndk-build clean download-src
