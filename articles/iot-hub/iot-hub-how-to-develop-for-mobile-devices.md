---
title: 'Utveckla för mobila enheter med Azure IoT SDK: er | Microsoft-dokument'
description: Utvecklarguide – Lär dig mer om hur du utvecklar för mobila enheter med Azure IoT Hub SDK:er.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/16/2018
ms.author: robinsh
ms.openlocfilehash: 945b02003a443c04e692fdc06ca5714de362d074
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68883086"
---
# <a name="develop-for-mobile-devices-using-azure-iot-sdks"></a>Utveckla för mobila enheter med Azure IoT SDK:er

Saker och ting på Sakernas Internet kan referera till ett brett spektrum av enheter med varierande kapacitet: sensorer, mikrokontroller, smarta enheter, industriella gateways och till och med mobila enheter.  En mobil enhet kan vara en IoT-enhet, där den skickar telemetri från enhet till moln och hanteras av molnet.  Det kan också vara enheten som kör ett backend-tjänstprogram, som hanterar andra IoT-enheter.  I båda fallen kan [Azure IoT Hub SDK:er](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) användas för att utveckla program som fungerar för mobila enheter.  

## <a name="develop-for-native-ios-platform"></a>Utveckla för inbyggd iOS-plattform

Azure IoT Hub SDK:er tillhandahåller inbyggt iOS-plattformsstöd via Azure IoT Hub C SDK.  Du kan se det som en iOS SDK som du kan införliva i ditt Swift- eller Objective C XCode-projekt.  Det finns två sätt att använda C SDK på iOS:

* Använd CocoaPod-biblioteken i XCode-projektet direkt.  
* Ladda ner källkoden för C SDK och bygg för iOS-plattformen enligt [bygginstruktionen](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) för MacOS.  

Azure IoT Hub C SDK är skriven i C99 för maximal portabilitet till olika plattformar.  Portningsprocessen innebär att skriva ett tunt införandelager för de plattformsspecifika komponenterna, som finns här för [iOS](https://github.com/Azure/azure-c-shared-utility/tree/master/pal/ios-osx).  Funktionerna i C SDK kan utnyttjas på iOS-plattformen, inklusive Azure IoT Hub-primitiver som stöds och SDK-specifika funktioner som återförsöksprincip för nätverkstillförlitlighet.  Gränssnittet för iOS SDK liknar också gränssnittet för Azure IoT Hub C SDK.  

Dessa dokumentationer går igenom hur du utvecklar ett enhetsprogram eller tjänstprogram på en iOS-enhet:

* [Snabbstart: Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-ios.md)  
* [Skicka meddelanden från molnet till din enhet med IoT-hubb](iot-hub-ios-swift-c2d.md) 

### <a name="develop-with-azure-iot-hub-cocoapod-libraries"></a>Utveckla med Azure IoT Hub CocoaPod-bibliotek

Azure IoT Hub SDK:er släpper en uppsättning Objective-C CocoaPod-bibliotek för iOS-utveckling.  Information om hur du ser den senaste listan över CocoaPod-bibliotek finns i [CocoaPods för Microsoft Azure IoT](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/ios/CocoaPods.md).  När de relevanta biblioteken har införlivats i ditt XCode-projekt finns det två sätt att skriva IoT Hub-relaterad kod:

* Objektiv C-funktion: Om projektet är skrivet i Objective-C kan du anropa API:er från Azure IoT Hub C SDK direkt.  Om projektet är skrivet i `@objc func` Swift kan du anropa innan du skapar din funktion och fortsätta att skriva alla logiker som är relaterade till Azure IoT Hub med C- eller Objective-C-kod.  En uppsättning exempel som visar båda finns i [exempelarkivet](https://github.com/Azure-Samples/azure-iot-samples-ios).  

* Införliva C-exempel: Om du har skrivit ett C-enhetsprogram kan du referera direkt till det i XCode-projektet:
    * Lägg till filen sample.c i XCode-projektet från XCode.  
    * Lägg till rubrikfilen i ditt beroende.  En rubrikfil ingår i [exempeldatabasen](https://github.com/Azure-Samples/azure-iot-samples-ios) som ett exempel. Mer information finns på Apples dokumentationssida för [Objective-C](https://developer.apple.com/documentation/objectivec).

## <a name="develop-for-android-platform"></a>Utveckla för Android-plattform
Azure IoT Hub Java SDK stöder Android-plattformen.  För den specifika API-versionen som testats, besök vår [plattformssupportsida](iot-hub-device-sdk-platform-support.md) för den senaste uppdateringen.

Dessa dokumentationer går igenom hur du utvecklar ett enhetsprogram eller tjänstprogram på en Android-enhet med Gradle och Android Studio:

* [Snabbstart: Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-android.md)  
* [Snabbstart: Styra en enhet](quickstart-control-device-android.md) 

## <a name="next-steps"></a>Nästa steg

* [IoT Hub REST API-referens](https://docs.microsoft.com/rest/api/iothub/)
* [Azure IoT C SDK-källkod](https://github.com/Azure/azure-iot-sdk-c)
