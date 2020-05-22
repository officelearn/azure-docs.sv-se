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
ms.date: 05/21/2020
ms.author: memildin
ms.openlocfilehash: dfa3f00e668488574abeb08964909a8972c8913f
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2020
ms.locfileid: "83772955"
---
# <a name="azure-security-center-data-security"></a>Datasäkerhet i Azure Security Center
För att hjälpa kunder att förhindra, upptäcka och reagera på hot, Azure Security Center samla in och bearbeta säkerhetsrelaterade data, inklusive konfigurations information, metadata, händelse loggar och mycket annat. Microsoft följer strikta riktlinjer för efterlevnad och säkerhet – från kodning till driften av en tjänst.

Den här artikeln beskriver hur data hanteras och skyddas i Azure Security Center.

## <a name="data-sources"></a>Datakällor
Azure Security Center analyserar data från följande källor för att ge dig information om säkerhetstillstånd, identifiera säkerhetsproblem, rekommendera lösningar och upptäcka aktiva hot:

- Azure-tjänster: Använder information om konfigurationen av Azure-tjänster som du har distribuerat genom att kommunicera med tjänstens resursprovider.
- Nätverkstrafik: Använder samplade metadata för nätverkstrafiken från Microsofts infrastruktur, till exempel källans/målets IP-adress/port, paketstorlek och nätverksprotokoll.
- Partnerlösningar: Använder säkerhetsaviseringar från integrerade partnerlösningar, till exempel brandväggar och lösningar mot skadlig kod.
- Dina Virtual Machines och servrar: använder konfigurations information och information om säkerhets händelser, till exempel händelser och gransknings loggar i Windows, IIS-loggar och syslog-meddelanden från dina virtuella datorer. När en avisering skapas kan dessutom Azure Security Center generera en ögonblicksbild av den berörda virtuella datordisken och extrahera artefakter relaterade till aviseringen från den virtuella datordisken, till exempel en registerfil, för datautredningsändamål.


## <a name="data-protection"></a>Dataskydd
**Datauppdelning**: Data lagras logiskt och separat på varje komponent i tjänsten. Alla data taggas efter organisation. Den här taggningen finns kvar i informationens hela livscykel och används på varje lager i tjänsten.

**Data åtkomst**: för att kunna erbjuda säkerhets rekommendationer och undersöka möjliga säkerhetshot kan Microsofts personal komma åt information som samlas in eller analyseras av Azure-tjänster, inklusive process skapande händelser, ögonblicks bilder av VM-diskar och artefakter, som oavsiktligt kan inkludera kunddata eller personliga data från dina virtuella datorer. Vi följer [villkoren för Microsoft Online Services och tillhörande sekretesspolicy](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31), som garanterar att Microsoft inte använder kunddata eller härleder information från dem för reklamändamål eller i liknande kommersiellt syfte. Vi använder bara kunddata i den mån det är nödvändigt för att tillhandahålla Azure-tjänsterna, samt för därtill relaterade ändamål. Du äger alla rättigheter till dina kunddata.

**Dataanvändning**: Microsoft använder mönster och hotinformation som identifieras från flera klientorganisationer i syfte att förbättra våra skydds- och identifieringsfunktioner. Vi gör detta i enlighet med våra sekretessåtaganden som beskrivs i [sekretesspolicyn](https://www.microsoft.com/privacystatement/OnlineServices/Default.aspx).

## <a name="data-location"></a>Dataplats

**Dina arbets ytor**: en arbets yta har angetts för följande geografiska områden och data som samlas in från dina virtuella Azure-datorer, inklusive vissa typer av aviserings data, lagras i den närmaste arbets ytan.

| Region för virtuell dator                              | Region för arbetsyta |
|-------------------------------------|---------------|
| USA, Brasilien, Sydafrika | USA |
| Kanada                              | Kanada        |
| Europa (exklusive Storbritannien)   | Europa        |
| Storbritannien                      | Storbritannien |
| Asien (exklusive Indien, Japan, Korea, Kina)   | Asien och stillahavsområdet  |
| Korea                              | Asien och stillahavsområdet  |
| Indien                               | Indien         |
| Japan                               | Japan         |
| Kina                               | Kina         |
| Australien                           | Australien     |


Ögonblicksbilder av virtuella datordiskar lagras på samma lagringskonto som den virtuella datordisken.

För virtuella datorer och servrar som körs i andra miljöer, t.ex. lokalt, kan du ange arbetsytan och regionen där insamlade data lagras.

**Azure Security Center Storage**: Information om säkerhetsaviseringar, inklusive partneraviseringar lagras regionalt enligt platsen för den relaterade Azure-resursen, medan information om hälsostatus för säkerhet och rekommendationer lagras centralt i USA eller Europa, beroende på var kunden befinner sig. Artefakter lagras centralt i samma region som den virtuella datorn.

## <a name="managing-data-collection-from-virtual-machines"></a>Hantera datainsamling från virtuella datorer

När du väljer att aktivera Security Center i Azure är datainsamling aktiverat för var och en av dina Azure-prenumerationer. Du kan också aktivera datainsamling för dina prenumerationer i avsnittet Säkerhetsprincip i Azure Security Center. När data insamling är aktiverat Azure Security Center etablerar Log Analytics agenten på alla befintliga virtuella Azure-datorer som stöds och eventuella nya som skapas.
Log Analytics agent söker efter olika säkerhetsrelaterade konfigurationer och händelser i [ETW (Event tracing for Windows)](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW) spår. Dessutom utlöser operativsystemet händelselogghändelser när datorn körs. Exempel på sådana data är: operativsystemets typ och version, operativsystemloggar (Windows-händelseloggar), processer som körs, datornamn, IP-adresser, inloggad användare och klient-ID. Log Analytics agent läser händelse logg poster och ETW-spårning och kopierar dem till dina arbets ytor för analys. Log Analytics agenten aktiverar också process skapande händelser och kommando rads granskning.

Om du använder Azure Security Center Free, kan du inaktivera datainsamling från virtuella datorer i säkerhetsprincipen. Insamling av data krävs för prenumerationer på standardnivån. Funktionerna för ögonblicksbilder av virtuella datordisker och artefaktinsamling är fortfarande aktiverade även om datainsamling har inaktiverats.

## <a name="data-consumption"></a>Dataförbrukning

Kunder kan förbruka Security Center-relaterade data från olika dataströmmar:

* **Azure-aktivitet**: alla säkerhets aviseringar, godkända Security Center [just-in-Time-](https://docs.microsoft.com/azure/security-center/security-center-just-in-time) begäranden och alla aviseringar som genereras av [anpassningsbara program kontroller](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application).
* **Azure Monitor loggar**: alla säkerhets aviseringar.


> [!NOTE]
> Säkerhetsrekommendationer kan också användas via REST-API. Mer information finns i [Security Resource Provider REST API Reference](https://msdn.microsoft.com/library/mt704034(Azure.100).aspx).

## <a name="see-also"></a>Se även
I det här dokumentet har du lärt dig hur data hanteras och skyddas i Azure Security Center. Mer information om Azure Security Center finns här:

* [Azure Security Center planerings-och drift guide](security-center-planning-and-operations-guide.md) – lär dig att planera och förstå design överväganden för att anta Azure Security Center.
* [Övervakning av säkerhets hälsa i Azure Security Center](security-center-monitoring.md) – lär dig att övervaka hälso tillståndet för dina Azure-resurser
* [Hantera och åtgärda säkerhets aviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – lär dig hur du hanterar och åtgärdar säkerhets aviseringar
* [Övervaka partner lösningar med Azure Security Center](security-center-partner-solutions.md) – lär dig hur du övervakar dina partner lösningars hälso status.
* [Azures säkerhets blogg](https://blogs.msdn.com/b/azuresecurity/) – hitta blogg inlägg om säkerhet och efterlevnad i Azure
