---
title: Om Azure Kinect DK
description: Översikt över verktygen för Azure Kinect Developer Kit (DK) och integrerade tjänster.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.topic: overview
ms.date: 06/26/2019
keywords: 'Azure, Kinect, översikt, dev kit, DK, enhet, djup, innehålls spårning, tal, kognitiva tjänster, SDK: er, inbyggd program vara'
ms.openlocfilehash: d188d37c0247aebb16f51b4404da81d469136468
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359661"
---
# <a name="about-azure-kinect-dk"></a>Om Azure Kinect DK

 ![Azure Kinect DK](./media/index/device-image.jpg)

Azure Kinect DK är en utvecklingssats med avancerade AI-sensorer som tillhandahåller avancerat visuellt innehåll och talmodeller.  Kinect innehåller en rumslig mikrofonmatris med djupsensor och videokamera och en orienteringssensor i en liten enhet med flera lägen, alternativ och SDK:er (Software Development Kits). Den kan köpas i [Microsoft Online Store](https://www.microsoft.com/p/azure-kinect-dk/8pp5vxmd9nhq).

Azure Kinects DK-utvecklings miljö består av följande flera SDK: er:

- Sensor-SDK för sensor-och enhets åtkomst på låg nivå.
- Body tracking SDK för att spåra kroppar i 3D.
- Tal Cognitive Services SDK för att aktivera mikrofon åtkomst och Azure Cloud-baserade tal tjänster.

Dessutom kan kognitiva vision Services användas med enhetens RGB-kamera.

   ![Azure Kinect SDK-diagram](./media/quickstarts/sdk-diagram.jpg)

## <a name="azure-kinect-sensor-sdk"></a>Azure Kinect Sensor SDK

Azure Kinect sensor SDK tillhandahåller sensor åtkomst på låg nivå för Azure Kinect DK-maskinvaru sensorer och enhets konfiguration.

Mer information om Azure Kinect sensor SDK finns i [using sensor SDK](about-sensor-sdk.md).

### <a name="azure-kinect-sensor-sdk-features"></a>Azure Kinect sensor SDK-funktioner

Sensor-SDK: n har följande funktioner som fungerar när de är installerade och körs på Azure Kinect DK:

- Djup kamera åtkomst och läges kontroll (ett passivt IR-läge, plus breda och smala fält för att visa djup) 
- Åtkomst och kontroll över RGB-kamera (till exempel exponering och vit balans) 
- Rörelse sensor (Gyroscope och accelerometer)-åtkomst 
- Synkroniserat djup – RGB-kamera strömning med konfigurerbar fördröjning mellan kameror 
- Synkronisering av extern enhet med konfigurerbar fördröjnings förskjutning mellan enheter 
- Kamera ram meta-data åtkomst för bild upplösning, tidsstämpel osv. 
- Åtkomst till enhets kalibrerings data 

### <a name="azure-kinect-sensor-sdk-tools"></a>Azure Kinect sensor SDK-verktyg

Följande verktyg är tillgängliga i sensorn SDK:

- Ett visnings verktyg för att övervaka enhets data strömmar och konfigurera olika lägen.
- Ett sensor inspelnings verktyg och uppspelnings läsar-API som använder behållar formatet Matroska.
- Ett uppdaterings verktyg för Azure Kinect DK-programvara.

## <a name="azure-kinect-body-tracking-sdk"></a>SDK för kroppsspårning i Azure Kinect

SDK: n för brödtext innehåller ett Windows-bibliotek och körnings miljö för att spåra kroppar i 3D när det används med Azure Kinect DK-maskinvaran.

### <a name="azure-kinect-body-tracking-features"></a>Funktioner för innehålls spårning i Azure Kinect

Följande kropps spårnings funktioner är tillgängliga i medföljande SDK:

- Tillhandahåller innehålls segmentering.
- Innehåller ett anatomisk korrekt Skeleton för varje delvis eller fullständig brödtext i FOV.
- Erbjuder en unik identitet för varje brödtext.
- Kan spåra kroppar över tid.

### <a name="azure-kinect-body-tracking-tools"></a>Innehålls spårnings verktyg för Azure Kinect

- Body-spåraren har ett visnings verktyg för att spåra kroppar i 3D.

## <a name="speech-cognitive-services-sdk"></a>Speech kognitiv Services SDK

Talet SDK möjliggör Azure-anslutna tal tjänster.

### <a name="speech-services"></a>Taltjänster

- Tal till text
- Talöversättning
- Text till tal

>[!NOTE]
>Azure Kinect DK har inga högtalare.

Mer information och information finns i [dokumentationen om tal service](../cognitive-services/speech-service/index.yml).

## <a name="vision-services"></a>Vision Services

Följande Azure-tjänster för [kognitiv vision](https://azure.microsoft.com/services/cognitive-services/directory/vision/) tillhandahåller Azure-tjänster som kan identifiera och analysera innehåll i bilder och videor.

- [Dator vision](https://azure.microsoft.com/services/cognitive-services/computer-vision/)
- [Ansiktsigenkänning](https://azure.microsoft.com/services/cognitive-services/face/)
- [Video Indexer](https://azure.microsoft.com/services/media-services/video-indexer/)
- [Content moderator](https://azure.microsoft.com/services/cognitive-services/content-moderator/)
- [Anpassad vision](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/)

Tjänster utvecklas och förbättras ständigt, så kom ihåg att regelbundet kontrol lera om det finns nya eller ytterligare [kognitiva tjänster](https://azure.microsoft.com/services/cognitive-services/) för att förbättra ditt program. En förhandstitt på nya tjänster hittar du i de [kognitiva tjänst labben](https://labs.cognitive.microsoft.com/).

## <a name="azure-kinect-hardware-requirements"></a>Maskin varu krav för Azure Kinect

Azure Kinect DK integrerar Microsofts senaste sensor teknik i ett enda USB-kopplat tillbehör. Mer information finns i [maskin varu specifikationen för Azure Kinect DK](hardware-specification.md).

## <a name="next-steps"></a>Nästa steg

Nu har du en översikt över Azure Kinect DK. Nästa steg är att sätta igång och ställa in det!

> [!div class="nextstepaction"]
>[Snabb start: Konfigurera Azure Kinect DK](set-up-azure-kinect-dk.md)