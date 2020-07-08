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
ms.openlocfilehash: cdec5ade4fc72fba6fcfba131b69ca9eb373874c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84871780"
---
# <a name="frequently-asked-questions-about-the-azure-api-for-fhir"></a>Vanliga frågor och svar om Azure API för FHIR

## <a name="what-is-fhir"></a>Vad är FHIR?
Resurserna för snabb sjukvårds samverkan (FHIR-uttalad "brand") är en driftskompatibilitet som är avsedd att möjliggöra utbyte av sjukvårds data mellan olika hälso system. Den här standarden har utvecklats av HL7-organisationen och antas av sjukvårds organisationer runtom i världen. Den mest aktuella versionen av FHIR som är tillgänglig är R4 (version 4). Azure API för FHIR stöder R4 och har även stöd för den tidigare versionen STU3 (standard för utvärderings version 3). Mer information om FHIR finns på [HL7.org](http://hl7.org/fhir/summary.html).

## <a name="is-the-data-behind-the-fhir-apis-stored-in-azure"></a>Är data bakom FHIR-API: er lagrade i Azure?

Ja, data lagras i hanterade databaser i Azure. Azure API för FHIR ger inte direkt åtkomst till det underliggande data lagret.

## <a name="what-identity-provider-do-you-support"></a>Vilken identitetsprovider stöder du?

Vi stöder för närvarande Microsoft Azure Active Directory som identitets leverantör.

## <a name="what-fhir-version-do-you-support"></a>Vilken FHIR-version har du stöd för?

Vi stöder versioner av 4.0.0 och 3.0.1 för både Azure API för FHIR (PaaS) och FHIR Server för Azure (öppen källkod).

Mer information finns i [funktioner som stöds](fhir-features-supported.md). Läs om vad som har ändrats mellan versioner i [versions historiken för HL7-FHIR](https://hl7.org/fhir/R4/history.html).

## <a name="whats-the-difference-between-the-open-source-microsoft-fhir-server-for-azure-and-the-azure-api-for-fhir"></a>Vad är skillnaden mellan Microsoft FHIR-servern med öppen källkod för Azure och Azure API för FHIR?

Azure API för FHIR är en värdbaserad och hanterad version av Microsoft FHIR-servern med öppen källkod för Azure. I den hanterade tjänsten tillhandahåller Microsoft alla underhåll och uppdateringar. 

När du kör FHIR Server för Azure har du direkt åtkomst till de underliggande tjänsterna. Men du ansvarar också för att underhålla och uppdatera servern och all nödvändig kompatibilitet om du lagrar PHI-data.

Från en utvecklings synpunkt distribueras alla funktioner till Microsoft FHIR-servern med öppen källkod för Azure först. När den har verifierats i öppen källkod kommer den att lanseras till PaaS Azure API för FHIR-lösningen. Tiden mellan versionen i öppen källkod och PaaS beror på komplexiteten i funktionen och andra prioriteringar för prioriteringar. 

## <a name="what-is-smart-on-fhir"></a>Vad är SMART på FHIR?

SMARTA (utbytbara medicinska program och återanvändbar teknik) på FHIR är en uppsättning öppna specifikationer för att integrera partner program med FHIR-servrar och andra hälso-IT-system, till exempel elektroniska hälso poster och hälso informations utbyten. Genom att skapa ett SMART on FHIR-program kan du se till att ditt program kan nås och utnyttjas av en mängd olika med olika system.
Autentisering och Azure API för FHIR. Om du vill veta mer om SMART kan du gå till [Smart hälsa](https://smarthealthit.org/).

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du läst några av de vanliga frågorna om Azure API för FHIR. Läs om de funktioner som stöds i FHIR Server för Azure:
 
>[!div class="nextstepaction"]
>[FHIR funktioner som stöds](fhir-features-supported.md)