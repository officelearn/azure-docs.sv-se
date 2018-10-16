---
title: 'Utveckla för mobila enheter med Azure IoT SDK: er | Microsoft Docs'
description: 'Developer Användarguide – Lär dig mer om hur du utvecklar för mobila enheter med Azure IoT Hub SDK: er.'
author: yzhong94
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/16/2018
ms.author: yizhon
ms.openlocfilehash: 4a94abe69b525dc1b03fe2c1ae9593f3c6399f56
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2018
ms.locfileid: "49339761"
---
# <a name="develop-for-mobile-devices-using-azure-iot-sdks"></a>Utveckla för mobila enheter med Azure IoT SDK: er

Saker i Sakernas Internet kan referera till en mängd olika enheter med olika funktioner: sensorer, mikrostyrenheter, smarta enheter, industriella gatewayer och även mobila enheter.  En mobil enhet kan vara en IoT-enhet, där det skickar enhet-till-moln-telemetri och hanteras i molnet.  Det kan också vara enheten som kör ett program för backend-tjänst som hanterar andra IoT-enheter.  I båda fallen [Azure IoT Hub SDK: er](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) kan användas för att utveckla program som fungerar för mobila enheter.  

## <a name="develop-for-native-ios-platform"></a>Utveckla för interna iOS-plattformen

Azure IoT Hub SDK: er ge support för interna iOS-plattformen via Azure IoT Hub C SDK.  Du kan se det som en iOS-SDK som du kan lägga till i Swift eller Objective C XCode-projektet.  Det finns två sätt att använda C SDK för iOS:

* Använd CocoaPod-biblioteken i XCode-projekt direkt.  

* Ladda ned källkoden för C SDK och skapa för iOS-plattformen den [skapa instruktion](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) för MacOS.  

Azure IoT Hub C SDK är skriven i C99 för maximal portabilitet för olika plattformar.  Porteringsguiden måste du skriva en tunn införande lager för plattformsspecifika-komponenterna, som finns här för [iOS](https://github.com/Azure/azure-c-shared-utility/tree/master/pal/ios-osx).  Funktioner i C SDK kan utnyttjas på iOS-plattformen, inklusive Azure IoT Hub-primitiver som stöds och SDK-specifika funktioner som återförsöksprincip för nätverket tillförlitlighet.  Gränssnittet för iOS SDK är också liknar gränssnitt för Azure IoT Hub C SDK.  

Dessa dokumentation gå igenom hur du utvecklar ett enhetprogram eller service-program på en iOS-enhet:

* [Snabbstart: Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-ios.md)  

* [Skicka meddelanden från molnet till enheten med IoT hub](iot-hub-ios-swift-c2d.md) 

## <a name="develop-with-azure-iot-hub-cocoapod-libraries"></a>Utveckla med Azure IoT Hub CocoaPod bibliotek

Azure IoT Hub SDK släpper en uppsättning Objective-C CocoaPod klientbibliotek för iOS-utveckling.  Du hittar den senaste listan över CocoaPod bibliotek i [CocoaPods för Microsoft Azure IoT](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/ios/CocoaPods.md).  När de relevanta biblioteken ingår i ditt XCode-projekt, finns det två sätt att skriva IoT Hub relaterade koden:

* Mål-C-funktion: om ditt projekt är skriven i Objective-C, kan du anropa API: er från Azure IoT Hub C SDK direkt.  Om ditt projekt är skrivna i Swift, kan du anropa `@objc func` innan skapar din funktion och fortsätt med att skriva alla logics som rör Azure IoT Hub med C eller Objective-C-koden.  En uppsättning exempel som visar båda finns i den [exempellagringsplatsen](https://github.com/Azure-Samples/azure-iot-samples-ios).  

* Införliva C-exempel: Om du har skrivit ett program för enhet C, du kan använda den direkt i XCode-projektet:

    * Lägg till filen sample.c till ditt XCode-projekt från XCode.  
    
    * Lägg till huvudfilen till din beroende.  En rubrikfil ingår i den [exempellagringsplatsen](https://github.com/Azure-Samples/azure-iot-samples-ios) som exempel. Mer information finns i Apples dokumentationssidan för [Objective-C](https://developer.apple.com/documentation/objectivec).

## <a name="next-steps"></a>Nästa steg

* [IoT Hub REST API-referens](https://docs.microsoft.com/rest/api/iothub/)
* [Källkoden för Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)
