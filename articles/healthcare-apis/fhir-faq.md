---
title: Vanliga frågor och svar om FHIR-tjänster i Azure – Azure API för FHIR
description: 'Få svar på vanliga frågor om Azure API för FHIR, till exempel lagrings platsen för data bakom FHIR-API: er och versions stöd.'
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: af891935fd474e6f1f83ff1c2ce56ef71cd065c6
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86536733"
---
# <a name="frequently-asked-questions-about-the-azure-api-for-fhir"></a>Vanliga frågor och svar om Azure API för FHIR

## <a name="azure-api-for-fhir"></a>Azure API för FHIR

### <a name="what-is-fhir"></a>Vad är FHIR?
Resurserna för snabb sjukvårds samverkan (FHIR-uttalad "brand") är en driftskompatibilitet som är avsedd att möjliggöra utbyte av sjukvårds data mellan olika hälso system. Den här standarden har utvecklats av HL7-organisationen och antas av sjukvårds organisationer runtom i världen. Den mest aktuella versionen av FHIR som är tillgänglig är R4 (version 4). Azure API för FHIR stöder R4 och har även stöd för den tidigare versionen STU3 (standard för utvärderings version 3). Mer information om FHIR finns på [HL7.org](http://hl7.org/fhir/summary.html).

### <a name="is-the-data-behind-the-fhir-apis-stored-in-azure"></a>Är data bakom FHIR-API: er lagrade i Azure?

Ja, data lagras i hanterade databaser i Azure. Azure API för FHIR ger inte direkt åtkomst till det underliggande data lagret.

### <a name="what-identity-provider-do-you-support"></a>Vilken identitetsprovider stöder du?

Vi stöder för närvarande Microsoft Azure Active Directory som identitets leverantör.

### <a name="what-fhir-version-do-you-support"></a>Vilken FHIR-version har du stöd för?

Vi stöder versioner av 4.0.0 och 3.0.1 för både Azure API för FHIR (PaaS) och FHIR Server för Azure (öppen källkod).

Mer information finns i [funktioner som stöds](fhir-features-supported.md). Läs om vad som har ändrats mellan versioner i [versions historiken för HL7-FHIR](https://hl7.org/fhir/R4/history.html).

### <a name="whats-the-difference-between-the-open-source-microsoft-fhir-server-for-azure-and-the-azure-api-for-fhir"></a>Vad är skillnaden mellan Microsoft FHIR-servern med öppen källkod för Azure och Azure API för FHIR?

Azure API för FHIR är en värdbaserad och hanterad version av Microsoft FHIR-servern med öppen källkod för Azure. I den hanterade tjänsten tillhandahåller Microsoft alla underhåll och uppdateringar. 

När du kör FHIR Server för Azure har du direkt åtkomst till de underliggande tjänsterna. Men du ansvarar också för att underhålla och uppdatera servern och all nödvändig kompatibilitet om du lagrar PHI-data.

Från en utvecklings synpunkt distribueras alla funktioner till Microsoft FHIR-servern med öppen källkod för Azure först. När den har verifierats i öppen källkod kommer den att lanseras till PaaS Azure API för FHIR-lösningen. Tiden mellan versionen i öppen källkod och PaaS beror på komplexiteten i funktionen och andra prioriteringar för prioriteringar. 

### <a name="what-is-smart-on-fhir"></a>Vad är SMART på FHIR?

SMARTA (utbytbara medicinska program och återanvändbar teknik) på FHIR är en uppsättning öppna specifikationer för att integrera partner program med FHIR-servrar och andra hälso-IT-system, till exempel elektroniska hälso poster och hälso informations utbyten. Genom att skapa ett SMART on FHIR-program kan du se till att ditt program kan nås och utnyttjas av en mängd olika med olika system.
Autentisering och Azure API för FHIR. Om du vill veta mer om SMART kan du gå till [Smart hälsa](https://smarthealthit.org/).


## <a name="iot-connector-preview"></a>IoT-anslutning (förhands granskning)

### <a name="what-is-iomt"></a>Vad är IoMT?
IoMT står för medicinska saker och det är en kategori av IoT-enheter som samlar in och utbyter hälso-och Wellness data med andra sjukvårds IT-system över ett nätverk. Några exempel på IoMT-enheter är lämplighet och klinisk wearables, övervakning av sensorer, aktivitets Spårare, skötsel av karriär platser eller till och med en smart Pill.

### <a name="how-many-iot-connectors-do-i-need"></a>Hur många IoT-anslutningar behöver jag?
En enda IoT-koppling kan användas för att mata in data från ett stort antal olika typer av enheter. Du kan fortfarande välja att använda olika anslutningar av följande anledningar:
- **Skala**: för offentlig för hands version är resurs kapaciteten för IoT-kopplingar fast och förväntas tillhandahålla ett data flöde på cirka 200 meddelanden per sekund. Du kan lägga till fler IoT-kopplingar, om det krävs mer data flöde.
- **Enhets typ**: du kan konfigurera en separat IoT-anslutning för varje typ av IoMT-enheter som du har för enhets hanterings orsaker.

### <a name="is-there-a-limit-on-number-of-iot-connectors-during-public-preview"></a>Finns det en gräns för antalet IoT-kopplingar under den offentliga för hands versionen?
Ja, du kan bara skapa två IoT-kopplingar per prenumeration medan funktionen finns i en offentlig för hands version. Den här gränsen finns för att förhindra oväntad kostnad eftersom funktionen är tillgänglig kostnads fritt under för hands versionen. På begäran kan den här gränsen höjas upp till högst fem IoT-kopplingar.

### <a name="what-azure-regions-iot-connector-feature-is-available-during-public-preview"></a>Vilka Azure-regioner IoT Connector-funktionen är tillgänglig under den offentliga för hands versionen?
IoT Connector är tillgängligt i alla Azure-regioner där Azure API för FHIR är tillgängligt.

### <a name="can-i-configure-scaling-capacity-for-iot-connector"></a>Kan jag konfigurera skalnings kapacitet för IoT Connector?
Eftersom IoT Connector är kostnads fritt under den offentliga för hands versionen, är dess skalnings kapacitet fast och begränsad. Konfigurationen av IoT Connector som är tillgänglig i den offentliga för hands versionen förväntas tillhandahålla ett data flöde på cirka 200 meddelanden per sekund. Viss typ av resurs kapacitets konfiguration görs tillgänglig i allmän tillgänglighet (GA).

### <a name="what-fhir-version-does-iot-connector-support"></a>Vilken FHIR-version stöder IoT Connector?
IoT Connector stöder för närvarande endast FHIR version R4. Den här funktionen visas därför bara på R4-instanser av Azure API för FHIR och Microsoft planerar inte att stödja version STU3 just nu.

### <a name="why-cant-i-install-iot-connector-when-private-link-is-enabled-on-azure-api-for-fhir"></a>Varför kan jag inte installera IoT Connector när en privat länk är aktive rad på Azure API för FHIR?
IoT Connector stöder inte funktionen för privat länk för tillfället. Om du har aktiverat privat länk i Azure API för FHIR kan du därför inte installera IoT Connector och vice versa. Den här begränsningen förväntas gå bort när IoT Connector är tillgängligt för allmän tillgänglighet (GA).

### <a name="whats-the-difference-between-the-open-source-iomt-fhir-connector-for-azure-and-iot-connector-feature-of-azure-api-for-fhir-service"></a>Vad är skillnaden mellan IoMT FHIR-anslutaren med öppen källkod för Azure och IoT Connector i Azure API for FHIR service?
IoT Connector är en värdbaserad och hanterad version av IoMT FHIR-anslutaren med öppen källkod för Azure. I den hanterade tjänsten tillhandahåller Microsoft alla underhåll och uppdateringar.

När du kör IoMT FHIR Connector för Azure har du direkt åtkomst till de underliggande resurserna. Men du ansvarar också för att underhålla och uppdatera servern och all nödvändig kompatibilitet om du lagrar PHI-data.

Från en utvecklings synpunkt distribueras alla funktioner till IoMT FHIR-anslutningen med öppen källkod för Azure först. När den har verifierats i öppen källkod kommer den att lanseras till PaaS IoT Connector-funktionen i Azure API för FHIR-tjänsten. Tiden mellan versionen i öppen källkod och PaaS beror på komplexiteten i funktionen och andra prioriteter för vägens karta.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du läst några av de vanliga frågorna om Azure API för FHIR. Läs om de funktioner som stöds i FHIR Server för Azure:
 
>[!div class="nextstepaction"]
>[FHIR funktioner som stöds](fhir-features-supported.md)