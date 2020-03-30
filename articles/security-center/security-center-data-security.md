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
ms.openlocfilehash: a25bbd0f14d38a70624dbc58755c0e814753a181
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77604177"
---
# <a name="azure-security-center-data-security"></a>Datasäkerhet i Azure Security Center
Azure Security Center hjälper kunder att förhindra, upptäcka och svara på hot genom att samla in och bearbeta säkerhetsrelaterade data, inklusive konfigurationsinformation, metadata, händelseloggar, kraschdumpfiler och annat. Microsoft följer strikta riktlinjer för efterlevnad och säkerhet – från kodning till driften av en tjänst.

Den här artikeln beskriver hur data hanteras och skyddas i Azure Security Center.

## <a name="data-sources"></a>Datakällor
Azure Security Center analyserar data från följande källor för att ge dig information om säkerhetstillstånd, identifiera säkerhetsproblem, rekommendera lösningar och upptäcka aktiva hot:

- Azure-tjänster: Använder information om konfigurationen av Azure-tjänster som du har distribuerat genom att kommunicera med tjänstens resursprovider.
- Nätverkstrafik: Använder samplade metadata för nätverkstrafiken från Microsofts infrastruktur, till exempel källans/målets IP-adress/port, paketstorlek och nätverksprotokoll.
- Partnerlösningar: Använder säkerhetsaviseringar från integrerade partnerlösningar, till exempel brandväggar och lösningar mot skadlig kod.
- Dina virtuella datorer och servrar: Använder information om konfigurationer och säkerhetshändelser, till exempel händelser och granskningsloggar i Windows, IIS-loggar, syslog-meddelanden och kraschdumpfiler från dina virtuella datorer. När en avisering skapas kan dessutom Azure Security Center generera en ögonblicksbild av den berörda virtuella datordisken och extrahera artefakter relaterade till aviseringen från den virtuella datordisken, till exempel en registerfil, för datautredningsändamål.


## <a name="data-protection"></a>Dataskydd
**Datauppdelning**: Data lagras logiskt och separat på varje komponent i tjänsten. Alla data taggas efter organisation. Den här taggningen finns kvar i informationens hela livscykel och används på varje lager i tjänsten.

**Dataåtkomst**: För att kunna ge säkerhetsrekommendationer och undersöka potentiella säkerhetshot kan Microsoft-personal använda information som samlats in eller analyserats av Azure-tjänster. Det kan till exempel vara kraschdumpfiler, händelser som genereras när processer skapas, ögonblicksbilder av virtuella datordiskar och artefakter, vilka oavsiktligt kan innehålla kunddata eller personuppgifter från dina virtuella datorer. Vi följer [villkoren för Microsoft Online Services och tillhörande sekretesspolicy](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31), som garanterar att Microsoft inte använder kunddata eller härleder information från dem för reklamändamål eller i liknande kommersiellt syfte. Vi använder bara kunddata i den mån det är nödvändigt för att tillhandahålla Azure-tjänsterna, samt för därtill relaterade ändamål. Du äger alla rättigheter till dina kunddata.

**Dataanvändning**: Microsoft använder mönster och hotinformation som identifieras från flera klientorganisationer i syfte att förbättra våra skydds- och identifieringsfunktioner. Vi gör detta i enlighet med våra sekretessåtaganden som beskrivs i [sekretesspolicyn](https://www.microsoft.com/privacystatement/OnlineServices/Default.aspx).

## <a name="data-location"></a>Dataplats

**Arbetsytor**: En arbetsyta definieras för följande regioner och data som samlas in från dina virtuella Azure-datorer, inklusive kraschdumpar och vissa typer av aviseringsdata, lagras i den närmaste arbetsytan.

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

**Azure Security Center Storage**: Information om säkerhetsaviseringar, inklusive partneraviseringar lagras regionalt enligt platsen för den relaterade Azure-resursen, medan information om hälsostatus för säkerhet och rekommendationer lagras centralt i USA eller Europa, beroende på var kunden befinner sig.
Azure Security Center samlar in tillfälliga kopior av dina kraschdumpfiler och analyserar dem efter tecken på kryphål och säkerhetsintrång. Azure Security Center utför den här analysen inom samma geografiska region som arbetsytan och tar bort de tillfälliga kopiorna när analysen är klar.

Artefakter lagras centralt i samma region som den virtuella datorn.


## <a name="managing-data-collection-from-virtual-machines"></a>Hantera datainsamling från virtuella datorer

När du väljer att aktivera Security Center i Azure är datainsamling aktiverat för var och en av dina Azure-prenumerationer. Du kan också aktivera datainsamling för dina prenumerationer i avsnittet Säkerhetsprincip i Azure Security Center. När datainsamling är aktiverat etablerar Azure Security Center Microsoft Monitoring Agent på alla befintliga virtuella Azure-datorer som stöds och eventuella nya som skapas.
Agenten Microsoft Monitoring söker efter olika säkerhetsrelaterade konfigurationer och händelser med hjälp av [Event Tracing for Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx). Dessutom utlöser operativsystemet händelselogghändelser när datorn körs. Exempel på sådana data är: operativsystemets typ och version, operativsystemloggar (Windows-händelseloggar), processer som körs, datornamn, IP-adresser, inloggad användare och klient-ID. Microsoft Monitoring Agent läser händelseloggposter och ETW-spårning och kopierar dem till din arbetsyta/dina arbetsytor för analys. Microsoft Monitoring Agent kopierar även kraschdumpfiler till dina arbetsytor, möjliggör processgenereringshändelser och granskning av kommandorad.

Om du använder Azure Security Center Free, kan du inaktivera datainsamling från virtuella datorer i säkerhetsprincipen. Insamling av data krävs för prenumerationer på standardnivån. Funktionerna för ögonblicksbilder av virtuella datordisker och artefaktinsamling är fortfarande aktiverade även om datainsamling har inaktiverats.

## <a name="data-consumption"></a>Dataförbrukning

Kunder kan förbruka Security Center-relaterade data från olika dataströmmar:

* **Azure-aktivitet:** alla säkerhetsaviseringar, godkända säkerhetscenter [just-in-time-begäranden](https://docs.microsoft.com/azure/security-center/security-center-just-in-time) och alla aviseringar som genereras av [adaptiva programkontroller](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application).
* **Azure Monitor loggar:** alla säkerhetsaviseringar.


> [!NOTE]
> Säkerhetsrekommendationer kan också användas via REST-API. Mer information finns i [Security Resource Provider REST API Reference](https://msdn.microsoft.com/library/mt704034(Azure.100).aspx).

## <a name="see-also"></a>Se även
I det här dokumentet har du lärt dig hur data hanteras och skyddas i Azure Security Center. Mer information om Azure Security Center finns här:

* [Planerings- och driftsguide för Azure Security Center](security-center-planning-and-operations-guide.md) – Lär dig hur du planerar och förstår designövervägandena om att anta Azure Security Center.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md) – Lär dig hur du övervakar hälsotillståndet för dina Azure-resurser
* [Hantera och svara på säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Lär dig hur du hanterar och svarar på säkerhetsaviseringar
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md) – Lär dig hur du övervakar hälsostatusen för dina partnerlösningar.
* [Azure Security Blog](https://blogs.msdn.com/b/azuresecurity/) – Hitta blogginlägg om Azure-säkerhet och efterlevnad
