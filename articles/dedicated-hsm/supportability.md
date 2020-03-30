---
title: Support - Azure Dedikerad HSM | Microsoft-dokument
description: Supportalternativ och ansvarsområden för Azure Dedicated HSM i olika scenarier
services: dedicated-hsm
author: johndaw
manager: rkarlin
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.custom: seodec18
ms.date: 03/27/2019
ms.author: mbaldwin
ms.openlocfilehash: d83d688707baf6098d63dfde9b4181eb04fb9729
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70881015"
---
# <a name="azure-dedicated-hsm-supportability"></a>Azure dedikerad HSM-support

Azure Dedicated HSM-tjänsten tillhandahåller en fysisk enhet för ensam kundanvändning med fullständigt administrativt kontroll- och hanteringsansvar. Enheten som görs tillgänglig är en [Gemalto SafeNet Luna 7 HSM modell A790](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/). Microsoft har ingen administrativ åtkomst när den har etablerats av en kund, utöver den fysiska bifogade seriella portar som en övervakningsroll.  Utan åtkomst kan Microsoft inte ha något pågående underhåll på programvarunivå eller systemadministrationsansvar. Som ett resultat av detta är kunderna ansvariga för typiska operativa aktiviteter.
Kunderna är fullt ansvariga för program som använder HSM och bör arbeta med Gemalto för support eller konsultbaserad hjälp. På grund av omfattningen av kundägandet av drifthygien är det inte möjligt för Microsoft att erbjuda någon form av hög tillgänglighetsgaranti för den här tjänsten. Det är kundens ansvar att se till att deras program är korrekt konfigurerade för att uppnå hög tillgänglighet. Microsoft övervakar och underhåller enhetens hälsa och nätverksanslutning.

## <a name="getting-support"></a>Få support

Kundsupport för Dedicated HSM är en gemensam insats mellan Microsoft och Gemalto. Alla maskinvaruproblem eller problem med nätverkssökvägen kommer att åtgärdas av Microsoft, och allt som har att göra med den faktiska HSM, såsom konfiguration, programvara, firmware och programutveckling, kommer att åtgärdas av Gemalto. Den här supportmodellen säkerställer den snabbaste vägen till det mest effektiva stödet. Om du är osäker på ett visst problem, ta upp en supportbegäran med Microsoft så ser vi till att du dirigeras på rätt sätt. Microsoft kommer att fortsätta engagera sig i alla supportscenarier och sträva efter den bästa supportupplevelsen för våra kunder.

## <a name="gemalto-support"></a>Support för Gemalto

Kunder som använder den dedikerade HSM-tjänsten kvalificerar sig för support från Gemalto enligt deras Plus-supportplan. Detta kräver bara en registreringsprocess med hjälp av Gemalto-supportportalen. Ett kund-ID och instruktioner kommer att tillhandahållas för detta som en del av det första engagemanget med Microsoft för att få tillgång till den dedikerade HSM-tjänsten. Mekanismen för att få support från Gemalto är via deras [kundsupportportal.](https://supportportal.gemalto.com/csm/)
En viktig punkt att notera är att Gemalto kommer att tillhandahålla all programvara och dokumentation som krävs för att använda HSM (till exempel klientåtkomst programvara och SDKs) via nedladdning på kundsupportportalen.

### <a name="software-components"></a>Programvarukomponenter

Olika programvarukomponenter används i konfigurationen av HSM-enheter:

* Klientprogramvara
* SDK
* Verktyg

### <a name="guidance"></a>Riktlinjer

Gemalto ställer till förfogande för administrations- och konfigurationsvägledning via [kundsupportportalen.](https://supportportal.gemalto.com/csm/) När dessa dokument har loggat in med ett giltigt kund-ID finns de tillgängliga för hämtning. Gemalto tillhandahåller också en rad integrationsguider för att hjälpa kunder med olika scenarier och programvaruintegrationer. Mer information finns på [Gemalto-partnerwebbplatsen för Microsoft](https://safenet.gemalto.com/partners/microsoft/).

### <a name="support"></a>Support

Alla problem på programvarunivå eller frågor i samband med användning av HSM som en del av den särskilda HSM-tjänsten, bör riktas direkt till Gemalto-supporten. Alla programvarukomponenter som anges ovan, och alla anpassade HSM-konfigurationer som är efter etablering, kommer att åtgärdas av Gemalto. Mer information finns i [Gemaltos kundsupportportal](https://supportportal.gemalto.com/csm/).

### <a name="consulting-services"></a>Konsulttjänster

Om du vill ha hjälp med design, utveckling och distribution av anpassade program som använder HSM kontaktar du din Gemalto-kontorepresentant.

## <a name="microsoft-support"></a>Microsoft Support

Microsoft kommer att se till att fysiska HSM-enheter är nätverksanslutna och i drift för exklusiv användning av en enskild kund. Kunderna ansvarar för konfiguration, administration och hantering av enheten. Microsofts ansvarsområden omfattar:

* Se till att enheten har ström och kylning
* Upprätthålla ett drifttillstånd för HSM (till exempel break/fix-scenarier)
* Enheten är tillgänglig via nätverket.

Problem som följande ska rapporteras till Microsoft:

* Komponentfel
* Fullständigt enhetsfel
* Problem med nätverksåtkomst
* Problem med etablering och avetablering.

Microsoft har fysisk seriell portåtkomst till enheten via en övervakningsroll (det vill än administrativ roll) som möjliggör grundläggande hälsotelemetri.  Detta gör det möjligt för Microsoft att tillhandahålla proaktiva meddelanden om problem till kunden om inte kunden väljer att begränsa den här behörigheten. 

### <a name="provisioning-and-decommissioning"></a>Etablering och avveckling

När en kund har en godkänd registrering för dedikerad HSM-tjänst kan de skapa HSM-resurser (för närvarande via PowerShell eller kommandoradsgränssnitt och inte Azure-portalen). Resursen går igenom en allokeringsprocess som mappar en fysisk enhet i en angiven region till en kunds fördefinierade virtuella nätverk (VNet). När den är synlig på ett virtuella nätverk kan kunden komma åt enheten och konfigurera den ytterligare enligt kraven. Kunderna får tillgång till sina dedikerade HSM:er med Hjälp av Gemaltos klientprogram och verktyg. Processen för att skapa resurser stöds av Microsoft. Anpassad konfigurationsprocess och därefter stöds av Gemalto. (se Gemalto-stöd ovan). När en kund har avslutat med en HSM måste den återställas (eller nollställas) för att säkerställa att data inte finns kvar. Processen för att återställa enheten tar bort all anpassad konfiguration och data. Microsoft frigör enheten och returnerar den till poolen i ett orört tillstånd. Det innebär att när enheten returneras till poolen finns det inga tecken på tidigare kundaktivitet. 

### <a name="hardware-issues"></a>Maskinvaruproblem

HSM-enheten har redundanta och utbytbara nätaggregat och fläktenheter.  Borttagning av fläktenheter kommer dock fortfarande att orsaka en manipuleringshändelse. När ett komponentfel inträffar använder Microsoft den lämpligaste processen för att åtgärda problemet på komponentnivå på ett sätt som orsakar minimala avbrott och lägsta risk för våra kunders tjänsttillgänglighet.
Eventuella allvarligare fel på enheten kommer att resultera i att enheten ersätts av en ny från den fria poolen. Kunden inkluderar helt enkelt den nya enheten i det befintliga HA-paret för att den ska synkronisera och återgå till fullt drifttillstånd. Den misslyckade enheten kommer att få sina datalagerenheter borttagna och strimlade på plats i datacentret. Endast chassit kommer att returneras till Gemalto för återvinning.


### <a name="networking-issues"></a>Problem med nätverk

Om kunderna får problem med nätverksåtkomst till HSM-enheten bör de kontakta Microsoft-supporten. Ett enkelt test för nätverksåtkomst är att använda SSH för att ansluta till HSM-enheten. Om detta misslyckas kontaktar du Microsoft-supporten.

## <a name="service-level-expectations-for-support"></a>Servicenivå förväntningar på support

Microsoft-supporttjänstnivåer finns i [Azure-supportplanen](https://azure.microsoft.com/support/plans/).
För Gemalto support servicenivåer, se [Gemalto Support Essentials](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Nästa steg

Vi rekommenderar att viktiga begrepp som hög tillgänglighet och säkerhet är väl förstådda innan enhetsetablering och programdesign eller distribution.

* [Distributionsarkitektur](deployment-architecture.md)
* [Hög tillgänglighet](high-availability.md)
* [Fysisk säkerhet](physical-security.md)
* [Nätverk](networking.md)

