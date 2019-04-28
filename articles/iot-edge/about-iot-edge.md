---
title: Vad är Azure IoT Edge | Microsoft Docs
description: Översikt över tjänsten Azure IoT Edge
author: kgremban
manager: philmea
ms.reviewer: chipalost
ms.service: iot-edge
services: iot-edge
ms.topic: overview
ms.date: 04/17/2019
ms.author: kgremban
ms.custom: mvc
ms.openlocfilehash: 70a8e17ecdd318a800c51e04b4453c182d1fbdc1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61248885"
---
# <a name="what-is-azure-iot-edge"></a>Vad är Azure IoT Edge?

Azure IoT Edge flyttar molnbaserade analyser och anpassad affärslogik så att din organisation kan fokusera på affärsinsikter istället för datahantering. Skala ut din IoT-lösning genom att paketera din affärslogik i standard behållare kan du distribuera de här behållarna till någon av dina enheter och övervaka alltihop från molnet. 

Analyser ger IoT-lösningar värde, men alla analyser behövs inte i molnet. Om du vill reagera så snabbt som möjligt vid nödfall kan köra du arbetsbelastningar för identifiering av avvikelser vid gränsen. Om du vill minska bandbreddskostnaderna och undvika att överföra terabyte med rådata kan du rensa och aggregera data lokalt sedan bara skicka insikterna till molnet för analys. 

Azure IoT Edge består av tre komponenter:
* **IoT Edge-moduler** är behållare som kör Azure-tjänster, tjänster från tredje part eller din egen kod. Moduler distribueras till IoT Edge-enheter och körs lokalt på enheterna. 
* Den **IoT Edge-körningen** körs på varje IoT Edge-enhet och hanterar modulerna som distribueras till varje enhet. 
* En **molnbaserad gränssnitt** gör det möjligt för dig att övervaka och hantera IoT Edge-enheter.

>[!NOTE]
>Azure IoT Edge finns tillgängligt på kostnadsfri och standardnivå för IoT Hub. Den kostnadsfria nivån är endast för testning och utvärdering. Mer information om nivåerna Basic och Standard finns i artikeln om att [välja rätt nivå för IoT Hub](../iot-hub/iot-hub-scaling.md).

## <a name="iot-edge-modules"></a>IoT Edge-moduler

IoT Edge-moduler är körningsenheter som är implementerade som Docker-kompatibla containrar och kör din affärslogik på gränsen. Flera moduler kan konfigureras för att kommunicera med varandra, vilket skapar en pipeline med databearbetning. Du kan utveckla anpassade moduler eller paketera vissa Azure-tjänster i moduler som ger insikter offline och på gränsen. 

### <a name="artificial-intelligence-at-the-edge"></a>Artificiell intelligens på gränsen

Med Azure IoT Edge kan du distribuera komplex händelsebearbetning, maskininlärning, bildigenkänning och annan värdefull AI utan att skriva internt. Azure-tjänster som Azure Functions, Azure Stream Analytics och Azure Machine Learning kan köras lokalt via Azure IoT Edge. Du är inte begränsad till Azure-tjänster, men. Vem som helst kan skapa AI-moduler och göra dem tillgängliga för communityn för användning via Microsoft Azure Marketplace. 

### <a name="bring-your-own-code"></a>Ha med din egen kod

När du vill distribuera din egen kod till dina enheter kan du göra det med Azure IoT Edge. Azure IoT Edge innehåller samma programmeringsmiljö som de andra Azure IoT-tjänsterna. Samma kod kan köras på en enhet eller i molnet. Azure IoT Edge stöder både Linux och Windows, så du kan koda till vilken plattform du vill. Den har stöd för Java, .NET Core 2.0, Node.js, C och Python. Det gör att dina utvecklare kan koda på ett språk de redan kan och använda befintlig affärslogik.

## <a name="iot-edge-runtime"></a>IoT Edge-körning

Azure IoT Edge-körning möjliggör anpassad och molnbaserad logik på IoT Edge-enheter. Det finns på IoT Edge-enheten och utför åtgärder för hantering och kommunikation. Körningen utför flera funktioner:

* Installerar och uppdaterar arbetsbelastningar på enheten.
* Upprätthåller Azure IoT Edge-säkerhetsstandarder på enheten.
* Säkerställer att IoT Edge-moduler alltid körs.
* Rapporterar modulens hälsa till molnet för fjärrövervakning.
* Hanterar kommunikationen mellan nedströms lövenheter och en IoT Edge-enhet, mellan modulerna på en IoT Edge-enhet, och mellan en IoT Edge-enhet och molnet.

![IoT Edge-körning skickar insikter och rapportering till IoT Hub](./media/about-iot-edge/runtime.png)

Det är upp till dig hur du använder en Azure IoT Edge-enhet. Körningen används ofta för att distribuera AI till gatewayenheter som sammanställa och bearbeta data från andra lokala enheter, men denna distributionsmodell är bara ett alternativ. 

Azure IoT Edge-körningen körs på en stor uppsättning IoT-enheter för att möjliggöra körningen på många olika sätt. Den stöder både Linux- och Windows-operativsystem och avlägsnar maskinvaruinformation. Använd en enhet är mindre än en Raspberry Pi 3 om du inte bearbetning av för mycket data eller använda en industriella server för att köra resursintensiva arbetsbelastningar.

## <a name="iot-edge-cloud-interface"></a>IoT Edge-molngränssnitt

Det är svårt att hantera programvarans livscykel för miljontals IoT-enheter som är ofta olika tillverkare och modeller eller geografiskt utspridd. Arbetsbelastningar skapas och konfigureras för en viss typ av enhet, distribueras till alla dina enheter och övervakas för att fånga alla enheter som beter sig felaktigt. Dessa aktiviteter kan inte utföras på enhetsbasis och måste göras i stor skala.

Azure IoT Edge integreras sömlöst med Azure IoT-lösningsacceleratorer för att tillhandahålla en kontrollplan för lösningens behov. Med molntjänster kan du:

* Skapa och konfigurera en arbetsbelastning som ska köras på en specifik typ av enhet.
* Skicka en arbetsbelastning till en uppsättning enheter.
* Övervaka arbetsbelastningar som körs på enheter i fältet.

![Enhetstelemetri och åtgärder samordnas med molnet](./media/about-iot-edge/cloud-interface.png)

## <a name="next-steps"></a>Nästa steg

Prova de här koncepten genom att [distribuera IoT Edge på en simulerad enhet](quickstart.md).