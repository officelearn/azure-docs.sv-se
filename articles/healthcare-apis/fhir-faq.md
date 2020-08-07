---
title: Vanliga frågor och svar om FHIR-tjänster i Azure – Azure API för FHIR
description: 'Få svar på vanliga frågor om Azure API för FHIR, till exempel lagrings platsen för data bakom FHIR-API: er och versions stöd.'
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 08/03/2020
ms.author: matjazl
ms.openlocfilehash: 252abcac6e9e39930593c1b110bf6d55ffdfc33f
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2020
ms.locfileid: "87843529"
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

## <a name="azure-iot-connector-for-fhir-preview"></a>Azure IoT Connector för FHIR (för hands version)

### <a name="what-is-iomt"></a>Vad är IoMT?
IoMT står för medicinska saker och det är en kategori av IoT-enheter som samlar in och utbyter hälso-och Wellness data med andra sjukvårds IT-system över ett nätverk. Några exempel på IoMT-enheter är lämplighet och klinisk wearables, övervakning av sensorer, aktivitets Spårare, skötsel av karriär platser eller till och med en smart Pill.

### <a name="how-many-azure-iot-connector-for-fhir-preview-do-i-need"></a>Hur många Azure IoT-anslutningar för FHIR (för hands version) behöver jag?
En enda Azure IoT-anslutning för FHIR * kan användas för att mata in data från ett stort antal olika typer av enheter. Du kan fortfarande välja att använda olika anslutningar av följande anledningar:
- **Skala**: för offentlig för hands version är Azure IoT Connector för FHIR resurs kapacitet fast och förväntas tillhandahålla ett data flöde på cirka 200 meddelanden per sekund. Du kan lägga till fler Azure IoT-anslutningar för FHIR, om det krävs högre data flöde.
- **Enhets typ**: du kan konfigurera en separat Azure IoT-anslutning för FHIR för varje typ av IoMT-enheter som du har för enhets hanterings skäl.

### <a name="is-there-a-limit-on-number-of-azure-iot-connector-for-fhir-preview-during-public-preview"></a>Finns det en gräns för antalet Azure IoT Connector för FHIR (för hands version) under den offentliga för hands versionen?
Ja, du kan bara skapa två Azure IoT-kopplingar för FHIR per prenumeration medan funktionen finns i en offentlig för hands version. Den här gränsen finns för att förhindra oväntad kostnad eftersom funktionen är tillgänglig kostnads fritt under för hands versionen. På begäran kan den här gränsen höjas upp till högst fem Azure IoT-anslutningar för FHIR.

### <a name="what-azure-regions-azure-iot-connector-for-fhir-preview-feature-is-available-during-public-preview"></a>Vilka Azure-regioner Azure IoT Connector för FHIR (för hands version) är tillgänglig under den offentliga för hands versionen?
Azure IoT Connector för FHIR är tillgängligt i alla Azure-regioner där Azure API för FHIR är tillgängligt.

### <a name="can-i-configure-scaling-capacity-for-azure-iot-connector-for-fhir-preview"></a>Kan jag konfigurera skalnings kapacitet för Azure IoT Connector för FHIR (för hands version)?
Eftersom Azure IoT Connector för FHIR är kostnads fritt under en offentlig för hands version, är dess skalnings kapacitet fast och begränsad. Azure IoT Connector för FHIR-konfiguration som är tillgänglig i den offentliga för hands versionen förväntas tillhandahålla ett data flöde på cirka 200 meddelanden per sekund. Viss typ av resurs kapacitets konfiguration görs tillgänglig i allmän tillgänglighet (GA).

### <a name="what-fhir-version-does-azure-iot-connector-for-fhir-preview-support"></a>Vilken FHIR-version stöder Azure IoT Connector för FHIR (för hands version)?
Azure IoT Connector för FHIR stöder för närvarande endast FHIR-R4. Den här funktionen visas därför bara på R4-instanser av Azure API för FHIR och Microsoft planerar inte att stödja version STU3 just nu.

### <a name="why-cant-i-install-azure-iot-connector-for-fhir-preview-when-private-link-is-enabled-on-azure-api-for-fhir"></a>Varför kan jag inte installera Azure IoT Connector för FHIR (för hands version) när en privat länk är aktive rad på Azure API för FHIR?
Azure IoT Connector för FHIR har inte stöd för funktionen för privat länk för tillfället. Om du har aktiverat privat länk i Azure API för FHIR kan du därför inte installera Azure IoT Connector för FHIR och vice versa. Den här begränsningen förväntas gå bort när Azure IoT Connector för FHIR är tillgängligt för allmän tillgänglighet (GA).

### <a name="whats-the-difference-between-the-open-source-iomt-fhir-connector-for-azure-and-azure-iot-connector-for-fhir-preview-feature-of-azure-api-for-fhir-service"></a>Vad är skillnaden mellan IoMT FHIR-anslutaren med öppen källkod för Azure och Azure IoT Connector för FHIR (för hands version) av Azure API för FHIR-tjänsten?
Azure IoT Connector för FHIR är en värdbaserad och hanterad version av IoMT FHIR-anslutaren med öppen källkod för Azure. I den hanterade tjänsten tillhandahåller Microsoft alla underhåll och uppdateringar.

När du kör IoMT FHIR Connector för Azure har du direkt åtkomst till de underliggande resurserna. Men du ansvarar också för att underhålla och uppdatera servern och all nödvändig kompatibilitet om du lagrar PHI-data.

Från en utvecklings synpunkt distribueras alla funktioner till IoMT FHIR-anslutningen med öppen källkod för Azure först. När den har verifierats i öppen källkod kommer den att lanseras till PaaS Azure IoT Connector för FHIR-funktionen i Azure API för FHIR-tjänsten. Tiden mellan versionen i öppen källkod och PaaS beror på komplexiteten i funktionen och andra prioriteter för vägens karta.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du läst några av de vanliga frågorna om Azure API för FHIR. Läs om de funktioner som stöds i FHIR Server för Azure:
 
>[!div class="nextstepaction"]
>[FHIR funktioner som stöds](fhir-features-supported.md)

* I Azure Portal kallas Azure IoT Connector för FHIR IoT Connector (för hands version).

FHIR är ett registrerat varumärke som tillhör HL7 och används med tillåtelse av HL7.