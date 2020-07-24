---
title: 'Begrepp: data flöde i IoT Connector (för hands version) i Azure API för FHIR'
description: Förstå IoT Connectors data flöde. IoT Connector-inmatningar, normalisering, grupper, transformeringar och bevarar IoMT-data till Azure API för FHIR.
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 05/13/2020
ms.author: punagpal
ms.openlocfilehash: c2d150fcd35bc51478a1d7f4a0407abce1446c06
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87100278"
---
# <a name="iot-connector-preview-data-flow"></a>IoT Connector (förhands granskning) data flöde

Den här artikeln innehåller en översikt över data flödet i IoT Connector. Du lär dig mer om olika data bearbetnings faser i IoT Connector som omvandlar enhets data till FHIR [observations](https://www.hl7.org/fhir/observation.html) resurser.

![Dataflöde i IoT-anslutningsprogram](media/concepts-iot-data-flow/iot-connector-data-flow.png)

Diagrammet ovan visar olika data flödes faser i IoT Connector. 

## <a name="ingest"></a>Mata in
Inmatning är det första steget där enhets data tas emot i IoT-anslutningen. Inmatnings slut punkten för enhets data finns i en [Azure Event Hub](https://docs.microsoft.com/azure/event-hubs/). Azure Event Hub-plattformen stöder hög skalning och data flöde med möjlighet att ta emot och bearbeta miljon tals meddelanden per sekund. Du kan också använda IoT Connector för att använda meddelanden asynkront och ta bort behovet av enheter för att vänta medan enhets data bearbetas.

> [!NOTE]
> JSON är det enda format som stöds för tillfället för enhets data.

## <a name="normalize"></a>Normalisera
Normalisering är nästa steg där enhets data hämtas från ovanstående Azure Event Hub och bearbetas med hjälp av enhets mappnings mallar. Den här mappnings processen resulterar i omvandling av enhets data till ett normaliserat schema. 

Normaliserings processen fören klar inte bara data bearbetning i senare skeden, men ger även möjlighet att projicera ett indatameddelande i flera normaliserade meddelanden. Till exempel kan en enhet skicka flera viktiga tecken för text temperatur, puls frekvens, blod tryck och respiration frekvens i ett enda meddelande. Detta indatameddelande skulle skapa fyra separata FHIR-resurser. Varje resurs representerar olika viktiga tecken, med indatamängden projiceras i fyra olika normaliserade meddelanden.

## <a name="group"></a>Grupp
Grupp är nästa steg där de normaliserade meddelanden som är tillgängliga från föregående steg grupperas med hjälp av tre olika parametrar: enhets identitet, mätnings typ och tids period.

Gruppering av enhets identitet och mått typ möjliggör användning av [SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData) -mått typ. Den här typen är ett koncist sätt att representera en tidsbaserad serie mätningar från en enhet i FHIR. Tids perioden styr svars tiden då observations resurser som genereras av IoT Connector skrivs till Azure API för FHIR.

> [!NOTE]
> Värdet för tids perioden är standard 15 minuter och kan inte konfigureras för för hands versionen.

## <a name="transform"></a>Transformering
I Transformations skedet bearbetas grupperade, normaliserade meddelanden genom FHIR mappar. Meddelanden som matchar en malltyp omvandlas till FHIR observations resurser som anges genom mappningen.

I det här läget hämtas även [enhets](https://www.hl7.org/fhir/device.html) resursen, tillsammans med tillhör ande [patient](https://www.hl7.org/fhir/patient.html) resurs, från FHIR-servern med hjälp av enhets-ID: n som finns i meddelandet. Dessa resurser läggs till som en referens till observations resursen som skapas.

> [!NOTE]
> Alla identiteter som ser ut cachelagras när de har lösts för att minska belastningen på FHIR-servern. Om du planerar att återanvända enheter med flera patienter rekommenderar vi att du skapar en virtuell enhets resurs som är unik för patienten och skickar den virtuella enhets identifieraren i meddelande nytto lasten. Den virtuella enheten kan länkas till den faktiska enhets resursen som överordnad.

Om det inte finns någon enhets resurs för en angiven enhets identifierare i FHIR-servern beror resultatet av värdet i `Resolution Type` uppsättningen vid tidpunkten för skapandet. När det är inställt på `Lookup` , ignoreras det angivna meddelandet och pipelinen fortsätter att bearbeta andra inkommande meddelanden. Om detta är inställt på `Create` , skapar IoT Connector en enhet med ett helt ben och patient resurser på FHIR-servern.  

## <a name="persist"></a>Försöker
När observations FHIR-resursen har genererats i Transformations fasen sparas resursen i Azure API för FHIR. Om FHIR-resursen är ny kommer den att skapas på servern. Om FHIR-resursen redan finns kommer den att uppdateras.

## <a name="next-steps"></a>Nästa steg

Klicka nedan för att lära dig hur du skapar mallar för enhets-och FHIR mappar.

>[!div class="nextstepaction"]
>[Mallar för mappning i IoT-anslutningsprogram](iot-mapping-templates.md)


FHIR är ett registrerat varumärke som tillhör HL7 och används med tillåtelse av HL7.
