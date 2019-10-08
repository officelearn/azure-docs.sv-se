---
title: Datasäkerhet i Azure Security Center | Microsoft Docs
description: Det här dokumentet beskriver hur data hanteras och skyddas i Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33f2c9f4-21aa-4f0c-9e5e-4cd1223e39d7
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2018
ms.author: memildin
ms.openlocfilehash: 3c63283a1a13e2deb178a53dfc5045d371d6a28a
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/07/2019
ms.locfileid: "71996931"
---
# <a name="azure-security-center-data-security"></a>Datasäkerhet i Azure Security Center
Azure Security Center hjälper kunder att förhindra, upptäcka och svara på hot genom att samla in och bearbeta säkerhetsrelaterade data, inklusive konfigurationsinformation, metadata, händelseloggar, kraschdumpfiler och annat. Microsoft följer strikta riktlinjer för efterlevnad och säkerhet – från kodning till driften av en tjänst.

Den här artikeln beskriver hur data hanteras och skyddas i Azure Security Center.

## <a name="data-sources"></a>Datakällor
Azure Security Center analyserar data från följande källor för att ge dig information om säkerhetstillstånd, identifiera säkerhetsproblem, rekommendera lösningar och upptäcka aktiva hot:

- Azure-tjänster: Använder information om konfigurationen av Azure-tjänster som du har distribuerat genom att kommunicera med tjänstens resurs leverantör.
- Nätverks trafik: Använder exempel på metadata från Microsofts infrastruktur, till exempel käll-/mål-IP/port, paket storlek och nätverks protokoll.
- Partner lösningar: Använder säkerhets aviseringar från integrerade partner lösningar, till exempel brand väggar och lösningar mot skadlig kod.
- Dina Virtual Machines och servrar: Använder konfigurations information och information om säkerhets händelser, till exempel händelser och gransknings loggar i Windows, IIS-loggar, syslog-meddelanden och krasch dum par-filer från dina virtuella datorer. När en avisering skapas kan dessutom Azure Security Center generera en ögonblicksbild av den berörda virtuella datordisken och extrahera artefakter relaterade till aviseringen från den virtuella datordisken, till exempel en registerfil, för datautredningsändamål.


## <a name="data-protection"></a>Dataskydd
**Data uppdelning**: Data lagras logiskt åtskilda på varje komponent i tjänsten. Alla data taggas efter organisation. Den här taggningen finns kvar i informationens hela livscykel och används på varje lager i tjänsten.

**Dataåtkomst**: För att kunna erbjuda säkerhets rekommendationer och undersöka möjliga säkerhetshot kan Microsofts personal komma åt information som samlas in eller analyseras av Azure-tjänster, inklusive krasch dum par-filer, process skapande händelser, ögonblicks bilder av VM-diskar och artefakter. Detta kan oavsiktligt inkludera kunddata eller personliga data från dina virtuella datorer. Vi följer [villkoren för Microsoft Online Services och tillhörande sekretesspolicy](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31), som garanterar att Microsoft inte använder kunddata eller härleder information från dem för reklamändamål eller i liknande kommersiellt syfte. Vi använder bara kunddata i den mån det är nödvändigt för att tillhandahålla Azure-tjänsterna, samt för därtill relaterade ändamål. Du äger alla rättigheter till dina kunddata.

**Data användning**: Microsoft använder mönster och hot information som visas över flera klienter för att förbättra våra förebyggande-och identifierings funktioner. Vi gör detta i enlighet med de sekretess åtaganden som beskrivs i vår [sekretess policy](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx).

## <a name="data-location"></a>Dataplats

**Din arbets yta (er)** : En arbets yta anges för följande geografiska områden och data som samlas in från dina virtuella Azure-datorer, inklusive krasch dum par, och vissa typer av aviserings data, lagras i den närmaste arbets ytan.

| Region för virtuell dator                              | Region för arbetsyta |
|-------------------------------------|---------------|
| USA, Brasilien, Sydafrika | USA |
| Kanada                              | Kanada        |
| Europa (exklusive Storbritannien)   | Europa        |
| Storbritannien och Nordirland                      | Storbritannien och Nordirland |
| Asien (exklusive Indien, Japan, Korea, Kina)   | Asien och stillahavsområdet  |
| Korea                              | Asien och stillahavsområdet  |
| Indien                               | Indien         |
| Japan                               | Japan         |
| Kina                               | Kina         |
| Australien                           | Australien     |


Ögonblicksbilder av virtuella datordiskar lagras på samma lagringskonto som den virtuella datordisken.

För virtuella datorer och servrar som körs i andra miljöer, t.ex. lokalt, kan du ange arbetsytan och regionen där insamlade data lagras.

**Azure Security Center lagring**: Information om säkerhets aviseringar, inklusive partner aviseringar, lagras regionalt enligt platsen för den relaterade Azure-resursen, medan information om säkerhets hälso status och rekommendation lagras centralt i antingen USA eller Europa enligt kundens plats.
Azure Security Center samlar in tillfälliga kopior av dina kraschdumpfiler och analyserar dem efter tecken på kryphål och säkerhetsintrång. Azure Security Center utför den här analysen inom samma geografiska region som arbetsytan och tar bort de tillfälliga kopiorna när analysen är klar.

Artefakter lagras centralt i samma region som den virtuella datorn.


## <a name="managing-data-collection-from-virtual-machines"></a>Hantera datainsamling från virtuella datorer

När du väljer att aktivera Security Center i Azure är datainsamling aktiverat för var och en av dina Azure-prenumerationer. Du kan också aktivera datainsamling för dina prenumerationer i avsnittet Säkerhetsprincip i Azure Security Center. När datainsamling är aktiverat etablerar Azure Security Center Microsoft Monitoring Agent på alla befintliga virtuella Azure-datorer som stöds och eventuella nya som skapas.
Microsoft Monitoring Agent söker efter olika säkerhetsrelaterade konfigurationer och händelser och skickar dem till [Event Tracing for Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx). Dessutom utlöser operativsystemet händelser för händelseloggar när datorn körs. Exempel på sådana data är: operativsystemets typ och version, operativsystemloggar (Windows-händelseloggar), processer som körs, datornamn, IP-adresser, inloggad användare och klient-ID. Microsoft Monitoring Agent läser händelseloggposter och ETW-spårning och kopierar dem till din arbetsyta/dina arbetsytor för analys. Microsoft Monitoring Agent kopierar även kraschdumpfiler till dina arbetsytor, möjliggör händelsegenerering när processer skapas och kommandoradsgranskning.

Om du använder Azure Security Center Free, kan du inaktivera datainsamling från virtuella datorer i säkerhetsprincipen. Insamling av data krävs för prenumerationer på standardnivån. Funktionerna för ögonblicksbilder av virtuella datordisker och artefaktinsamling är fortfarande aktiverade även om datainsamling har inaktiverats.

## <a name="data-consumption"></a>Dataförbrukning

Kunder kan använda Security Center-relaterade data från olika dataströmmar:

* **Azure-aktivitet**: alla säkerhets aviseringar, godkända Security Center [just-in-Time-](https://docs.microsoft.com/azure/security-center/security-center-just-in-time) begäranden och alla aviseringar som genereras av [anpassningsbara program kontroller](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application).
* **Azure Monitor loggar**: alla säkerhets aviseringar.


> [!NOTE]
> Säkerhetsrekommendationer kan också användas via REST-API. Mer information finns i [Security Resource Provider REST API Reference](https://msdn.microsoft.com/library/mt704034(Azure.100).aspx).

## <a name="see-also"></a>Se även
I det här dokumentet har du lärt dig hur data hanteras och skyddas i Azure Security Center. Mer information om Azure Security Center finns här:

* [Planerings- och användningsguide för Azure Security Center](security-center-planning-and-operations-guide.md) – Lär dig mer om planering och viktiga designaspekter när du ska börja använda Azure Security Center.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md) – Lär dig hur du övervakar dina Azure-resursers hälsa.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Lär dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md) – Lär dig hur du övervakar dina partnerlösningars hälsostatus.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten
* [Azures säkerhetsblogg](https://blogs.msdn.com/b/azuresecurity/) – Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure
