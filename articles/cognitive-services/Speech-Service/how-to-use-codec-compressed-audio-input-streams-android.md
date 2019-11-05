---
title: Stream codec Compressed Audio med talet SDK på Android-tal-tjänsten
titleSuffix: Azure Cognitive Services
description: Lär dig hur du direktuppspelar komprimerat ljud till Azure Speech Services med talet SDK på Android.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: amishu
ms.openlocfilehash: 2e741e8a8df2cebff167a381cef41351ead4c6cf
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73464366"
---
# <a name="using-codec-compressed-audio-input-with-the-speech-sdk-on-android"></a>Använda codec-komprimerad ljud inspelning med talet SDK på Android

Talet SDK: s **komprimerade ljud inspelnings** -API ger ett sätt att strömma komprimerat ljud till tal tjänsten med hjälp av PullStream eller PushStream.

> [!IMPORTANT]
> Strömmande komprimerade indata-ljud stöds för närvarande för [ C++, C#och Java på Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9)](how-to-use-codec-compressed-audio-input-streams.md). Det finns också stöd för java i Android och [mål-C på iOS-](how-to-use-codec-compressed-audio-input-streams-ios.md) plattformen.
> Tal SDK-version 1.7.0 eller högre krävs.

För WAV/PCM ser du Mainline tal-dokumentationen.  Utanför WAV/PCM stöds följande codec-komprimerade indataformat:

- MP3
- OPUS/OGG
- FLAC
- ALAW i WAV-behållare
- MULAW i WAV-behållare

## <a name="prerequisites-to-using-codec-compressed-audio-input-on-android"></a>Krav för att använda codec-komprimerad ljud inspelning på Android

Codec-komprimerat ljud implementeras med [gstreamer](https://gstreamer.freedesktop.org). Av licens skäl kompileras inte gstreamer-binärfiler med SDK. Du måste använda färdiga binärfiler för Android. Information om hur du hämtar färdiga bibliotek finns i [Installera för Android-utveckling](https://gstreamer.freedesktop.org/documentation/installing/for-android-development.html?gi-language=c). 

`libgstreamer_android.so` krävs. Kontrol lera att dina GStreamer-plugin-program är länkade i `libgstreamer_android.so`.

```make
GSTREAMER_PLUGINS := coreelements app audioconvert mpg123 audioresample audioparsers ogg opusparse opus wavparse alaw mulaw flac
```

Ett exempel på `Android.mk` och `Application.mk`-filen finns nedan. Följ de här stegen för att skapa gstreamer-delade objekt: `libgstreamer_android.so`.

```make
# Android.mk
LOCAL_PATH := $(call my-dir)

include $(CLEAR_VARS)

LOCAL_MODULE    := dummy
LOCAL_SHARED_LIBRARIES := gstreamer_android
LOCAL_LDLIBS := -llog
include $(BUILD_SHARED_LIBRARY)


ifndef GSTREAMER_ROOT_ANDROID
$(error GSTREAMER_ROOT_ANDROID is not defined!)
endif

ifndef APP_BUILD_SCRIPT
$(error APP_BUILD_SCRIPT is not defined!)
endif

ifndef TARGET_ARCH_ABI
$(error TARGET_ARCH_ABI is not defined!)
endif

ifeq ($(TARGET_ARCH_ABI),armeabi)
GSTREAMER_ROOT        := $(GSTREAMER_ROOT_ANDROID)/arm
else ifeq ($(TARGET_ARCH_ABI),armeabi-v7a)
GSTREAMER_ROOT        := $(GSTREAMER_ROOT_ANDROID)/armv7
else ifeq ($(TARGET_ARCH_ABI),arm64-v8a)
GSTREAMER_ROOT        := $(GSTREAMER_ROOT_ANDROID)/arm64
else ifeq ($(TARGET_ARCH_ABI),x86)
GSTREAMER_ROOT        := $(GSTREAMER_ROOT_ANDROID)/x86
else ifeq ($(TARGET_ARCH_ABI),x86_64)
GSTREAMER_ROOT        := $(GSTREAMER_ROOT_ANDROID)/x86_64
else
$(error Target arch ABI not supported: $(TARGET_ARCH_ABI))
endif

GSTREAMER_NDK_BUILD_PATH  := $(GSTREAMER_ROOT)/share/gst-android/ndk-build/
include $(GSTREAMER_NDK_BUILD_PATH)/plugins.mk
GSTREAMER_PLUGINS         :=  coreelements app audioconvert mpg123 audioresample audioparsers ogg opusparse opus wavparse alaw mulaw flac
GSTREAMER_EXTRA_LIBS      := -liconv
include $(GSTREAMER_NDK_BUILD_PATH)/gstreamer-1.0.mk
```

```make
# Application.mk
APP_STL = c++_shared
APP_PLATFORM = android-21
APP_BUILD_SCRIPT = Android.mk
```

Du kan bygga `libgstreamer_android.so` med följande kommando på Ubuntu 16,04 eller 18,04. Följande kommando rader har endast testats för [gstreamer Android-version 1.14.4](https://gstreamer.freedesktop.org/data/pkg/android/1.14.4/gstreamer-1.0-android-universal-1.14.4.tar.bz2) med [Android NDK B16B.](https://dl.google.com/android/repository/android-ndk-r16b-linux-x86_64.zip)

```sh
# assuming wget and unzip already installed on the system
mkdir buildLibGstreamer
cd buildLibGstreamer
wget https://dl.google.com/android/repository/android-ndk-r16b-linux-x86_64.zip
unzip -q -o android-ndk-r16b-linux-x86_64.zip
export PATH=$PATH:$(pwd)/android-ndk-r16b
export NDK_PROJECT_PATH=$(pwd)/android-ndk-r16b
wget https://gstreamer.freedesktop.org/data/pkg/android/1.14.4/gstreamer-1.0-android-universal-1.14.4.tar.bz2
mkdir gstreamer_android
tar -xjf gstreamer-1.0-android-universal-1.14.4.tar.bz2 -C $(pwd)/gstreamer_android/
export GSTREAMER_ROOT_ANDROID=$(pwd)/gstreamer_android

mkdir gstreamer
# Copy the Application.mk and Android.mk from the documentation above and put it inside $(pwd)/gstreamer

# Enable only one of the following at one time to create the shared object for the targeted ABI
echo "building for armeabi-v7a. libgstreamer_android.so will be placed in $(pwd)/armeabi-v7a"
ndk-build -C $(pwd)/gstreamer "NDK_APPLICATION_MK=Application.mk" APP_ABI=armeabi-v7a NDK_LIBS_OUT=$(pwd)

#echo "building for arm64-v8a. libgstreamer_android.so will be placed in $(pwd)/arm64-v8a"
#ndk-build -C $(pwd)/gstreamer "NDK_APPLICATION_MK=Application.mk" APP_ABI=arm64-v8a NDK_LIBS_OUT=$(pwd)

#echo "building for x86_64. libgstreamer_android.so will be placed in $(pwd)/x86_64"
#ndk-build -C $(pwd)/gstreamer "NDK_APPLICATION_MK=Application.mk" APP_ABI=x86_64 NDK_LIBS_OUT=$(pwd)

#echo "building for x86. libgstreamer_android.so will be placed in $(pwd)/x86"
#ndk-build -C $(pwd)/gstreamer "NDK_APPLICATION_MK=Application.mk" APP_ABI=x86 NDK_LIBS_OUT=$(pwd)
```

När det delade objektet (libgstreamer_android. so) är skapat måste Application Developer placera det delade objektet i Android-appen, så att det kan läsas in med tal-SDK.

## <a name="example-code-using-codec-compressed-audio-input"></a>Exempel kod med codec komprimerad ljud inspelning

Skapa `PullAudioInputStream` eller `PushAudioInputStream`för att strömma i ett komprimerat ljud format till tal tjänsterna. Skapa sedan en `AudioConfig` från en instans av data ström klassen och ange komprimerings formatet för data strömmen.

Vi antar att du har en data Ströms klass som heter `myPullStream` och använder OPUS/OGG. Din kod kan se ut så här:

```java
import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
import com.microsoft.cognitiveservices.speech.audio.AudioInputStream;
import com.microsoft.cognitiveservices.speech.audio.AudioStreamFormat;
import com.microsoft.cognitiveservices.speech.audio.PullAudioInputStream;
import com.microsoft.cognitiveservices.speech.internal.AudioStreamContainerFormat;

SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

// Create an audio config specifying the compressed audio format and the instance of your input stream class.
AudioStreamFormat audioFormat = AudioStreamFormat.getCompressedFormat(AudioStreamContainerFormat.OGG_OPUS);
AudioConfig audioConfig = AudioConfig.fromStreamInput(myPullStream, audioFormat);

SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, audioConfig);

SpeechRecognitionResult result = recognizer.recognizeOnceAsync().get()

String text = result.getText();
```

## <a name="next-steps"></a>Nästa steg

- [Hämta en kostnadsfri utvärderingsprenumeration på Speech](https://azure.microsoft.com/try/cognitive-services/)
* [Se identifiera tal i Java](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java)
