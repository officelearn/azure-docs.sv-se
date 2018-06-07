---
title: Utveckla för mobila enheter med Azure IoT SDK | Microsoft Docs
description: 'Utvecklare guide – Lär dig mer om hur du utvecklar för mobila enheter med Azure IoT-hubb SDK: er.'
author: yzhong94
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/16/2018
ms.author: yizhon
ms.openlocfilehash: 9c4b4a61698556f14a6362984b04e5cc409b5763
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34634955"
---
# <a name="develop-for-mobile-devices-using-azure-iot-sdks"></a>Utveckla för mobila enheter med Azure IoT-SDK

Saker i Sakernas Internet kan referera till en mängd olika enheter med olika funktioner: sensorer, mikrostyrenheter, smarta enheter, industriella gateways och även mobila enheter.  En mobil enhet kan vara en IoT-enhet, där den skickar enhet till moln telemetri och hanteras av molnet.  Det kan också vara enheten kör ett program för backend-tjänst som hanterar andra IoT-enheter.  I båda fallen [Azure IoT-hubb SDK] [ lnk-sdk-overview] kan användas för att utveckla program som fungerar för mobila enheter.  

## <a name="develop-for-native-ios-platform"></a>Utveckla för interna iOS-plattformen

Azure IoT-hubb SDK ger stöd för interna iOS-plattformen via Azure IoT-hubb C SDK.  Du kan se det som en iOS SDK som du kan använda i Swift eller Objective C XCode-projekt.  Det finns två sätt att använda C SDK för iOS:
- Använda CocoaPod-biblioteken i XCode-projekt direkt.  
- Hämta källkoden för C SDK och skapa för iOS-plattformen efter den [skapa instruktion] [ lnk-c-devbox] för MacOS.  

Azure IoT-hubb C SDK skrivs i C99 för maximal portabilitet till olika plattformar.  Porting innebär skriva ett tunn införande lager för plattformsspecifika komponenter, som finns här för [iOS][lnk-ios-pal].  Funktioner i SDK C kan utnyttjas på iOS-plattformen, inklusive Azure IoT Hub-primitiver som stöds och SDK-specifika funktioner som gör att principen för nätverket tillförlitlighet.  Gränssnittet för iOS SDK liknar också gränssnittet för Azure IoT-hubb C SDK.  

Dessa dokumentation går igenom hur du utvecklar ett enhetsprogram eller tjänstprogrammet på en iOS-enhet:
- [Snabbstart: Skicka telemetri från en enhet till en IoT-hubb][lnk-device-ios-quickstart]  
- [Skicka meddelanden från moln till din enhet med IoT-hubb][lnk-service-ios-quickstart]  

### <a name="develop-with-azure-iot-hub-cocoapod-libraries"></a>Utveckla med Azure IoT-hubb CocoaPod bibliotek

Azure IoT-hubb SDK släpper en uppsättning Objective-C CocoaPod bibliotek för iOS-utveckling.  Den senaste listan över CocoaPod bibliotek finns [CocoaPods för Microsoft Azure IoT][lnk-cocoapod-list].  När de relevanta biblioteken ingår i ditt XCode-projekt, finns det två sätt att skriva IoT-hubb relaterade kod:
- Funktionen för Objective C: om projektet är skriven i Objective-C, du kan anropa API: er från Azure IoT-hubb C SDK direkt.  Om ditt projekt skrivs i Swift, kan du anropa ``@objc func`` innan skapa din funktion och fortsätta att skriva alla logics som rör Azure IoT-hubb med hjälp av C eller Objective-C-koden.  En uppsättning exempel som visar båda finns i den [exempel databasen][lnk-ios-samples-repo].  
- Inkludera C-exempel: Om du har skapat ett program för enhet C, du kan referera till det direkt i ditt XCode-projekt:
    - Lägga till filen sample.c i ditt XCode-projekt från XCode.  
    - Lägg till huvudfilen beroendet.  Ett huvud ingår i den [exempel databasen] [ lnk-ios-samples-repo] som exempel.  Mer information finns i Apples dokumentationssidan för [Objective-C](https://developer.apple.com/documentation/objectivec).

## <a name="next-steps"></a>Nästa steg
* [IoT Hub REST API-referens][lnk-rest-ref]
* [Azure IoT C SDK-källkod][lnk-c-sdk]

[lnk-sdk-overview]: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks
[lnk-c-devbox]: https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md
[lnk-device-ios-quickstart]:https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-ios
[lnk-service-ios-quickstart]: https://docs.microsoft.com/azure/iot-hub/iot-hub-ios-swift-c2d
[lnk-cocoapod-list]: https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/ios/CocoaPods.md
[lnk-ios-samples-repo]: https://github.com/Azure-Samples/azure-iot-samples-ios
[lnk-ios-pal]: https://github.com/Azure/azure-c-shared-utility/tree/master/pal/ios-osx
[lnk-c-sdk]: https://github.com/Azure/azure-iot-sdk-c
[lnk-rest-ref]: https://docs.microsoft.com/rest/api/iothub/
