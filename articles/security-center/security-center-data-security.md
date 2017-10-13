---
title: "Datasäkerhet i Azure Security Center | Microsoft Docs"
description: "Det här dokumentet beskriver hur data hanteras och skyddas i Azure Security Center."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 33f2c9f4-21aa-4f0c-9e5e-4cd1223e39d7
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/12/2017
ms.author: yurid
ms.openlocfilehash: 6f95cf7631664f4630edbbcdadfd1d98105fdb98
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-security-center-data-security"></a>Datasäkerhet i Azure Security Center
Azure Security Center hjälper kunder att förhindra, upptäcka och svara på hot genom att samla in och bearbeta säkerhetsrelaterade data, inklusive konfigurationsinformation, metadata, händelseloggar, kraschdumpfiler och annat. Microsoft följer strikta riktlinjer för efterlevnad och säkerhet – från kodning till driften av en tjänst.

Den här artikeln beskriver hur data hanteras och skyddas i Azure Security Center.

>[!NOTE] 
>Från och med tidig juni 2017 använder Security Center Microsoft Monitoring Agent för att samla in och lagra data. Mer information finns under [plattformsmigrering i Azure Security Center](security-center-platform-migration.md). Informationen i den här artikeln representerar Security Centers funktionalitet efter övergången till Microsoft Monitoring Agent.
>


## <a name="data-sources"></a>Datakällor
Azure Security Center analyserar data från följande källor för att ge dig information om säkerhetstillståndet, identifiera säkerhetsproblem och rekommendera lösningar och identifiera aktiva hot:

- Azures-tjänster: Använder information om konfigurationen av Azure-tjänster som du har distribuerat genom att kommunicera med tjänstens resursprovider.
- Nätverkstrafik: Använder samplade metadata för nätverkstrafiken från Microsofts infrastruktur, till exempel käll- och mål IP-adress/port, paketstorlek och nätverksprotokoll.
- Partnerlösningar: Använder säkerhetsvarningar från integrerade partnerlösningar, till exempel brandväggar och lösningar mot skadlig kod. 
- Virtual Machines and Servers: Använder information om konfigurationer och säkerhetshändelser, till exempel händelser och granskningsloggar i Windows, IIS-loggar, syslog-meddelanden och kraschdumpfiler från dina virtuella datorer. När en avisering skapas kan dessutom Azure Security Center generera en ögonblicksbild av den berörda virtuella datordisken och extrahera artefakter relaterade till aviseringen från den virtuella datordisken, till exempel en registerfil, för datautredningsändamål.


## <a name="data-protection"></a>Dataskydd
**Datauppdelning**: Data lagras logiskt och separat på varje komponent i tjänsten. Alla data taggas efter organisation. Den här taggningen finns kvar i informationens hela livscykel och används på varje lager i tjänsten.

**Dataåtkomst**: För att kunna ge säkerhetsrekommendationer och undersöka potentiella säkerhetshot kan Microsoft-personal använda information som samlats in eller analyserats av Azure-tjänster. Det kan till exempel vara kraschdumpfiler, processgenereringshändelser, ögonblicksbilder av virtuella datordiskar och artefakter, vilka oavsiktligt kan innehålla kunddata eller personuppgifter från dina virtuella datorer. Vi följer [villkoren för Microsoft Online Services och tillhörande sekretesspolicy](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31), som garanterar att Microsoft inte använder kunddata eller härleder information från dem för reklamändamål eller i liknande kommersiellt syfte. Vi använder bara kunddata i den mån det är nödvändigt för att tillhandahålla Azure-tjänsterna, samt för därtill relaterade ändamål. Du äger alla rättigheter till dina kunddata.

**Dataanvändning**: Microsoft använder mönster och hotinformation som identifieras från flera klientorganisationer i syfte att förbättra våra skydds- och identifieringsfunktioner. Vi gör detta i enlighet med våra sekretessåtaganden som beskrivs i [sekretesspolicyn](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx).

## <a name="data-location"></a>Dataplats

**Your Workspace(s)**: En arbetsyta har angetts för följande regioner och data som samlas in från dina virtuella Azure-datorer, inklusive kraschdumpar och vissa typer av aviseringsdata, lagras i arbetsytan närmaste. 

| VM Geo                        | Workspace Geo |
|-------------------------------|---------------|
| USA, Brasilien, Kanada | USA |
| Europa, Storbritannien        | Europa        |
| Asien och stillahavsområdet, Japan, Indien    | Asien och stillahavsområdet  |
| Australien                     | Australien     |

 
Ögonblicksbilder av virtuella datordiskar lagras på samma lagringskonto som den virtuella datordisken.
 
För virtuella datorer och servrar som körs i andra miljöer, t.ex. lokalt, kan du ange arbetsytan och regionen där insamlade data lagras. 

**Azure Security Center Storage**: Information om säkerhetsaviseringar, inklusive partneraviseringar lagras regionalt enligt platsen för den relaterade Azure-resursen, medan information om hälsostatus för säkerhet och rekommendationer lagras centralt i USA eller Europa, beroende på var kunden befinner sig.
Azure Security Center samlar in tillfälliga kopior av dina kraschdumpfiler och analyserar dem efter tecken på kryphål och säkerhetsintrång. Azure Security Center utför den här analysen inom samma geografiska region som arbetsytan och tar bort de tillfälliga kopiorna när analysen är klar.

Artefakter lagras centralt i samma region som den virtuella datorn. 


## <a name="managing-data-collection-from-virtual-machines"></a>Hantera datainsamling från virtuella datorer

När du väljer att aktivera Security Center i Azure är datainsamling aktiverat för var och en av dina Azure-prenumerationer. Du kan också aktivera datainsamling för dina prenumerationer i avsnittet Säkerhetsprincip i Azure Security Center. När datainsamling är aktiverat etablerar Azure Security Center Microsoft Monitoring Agent på alla befintliga virtuella Azure-datorer som stöds och eventuella nya som skapas. Agenten Microsoft Monitoring söker efter olika säkerhetsrelaterade konfigurationer och händelser med hjälp av [ETW-spårning (Event Tracing for Windows)](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx). Dessutom utlöser operativsystemet händelselogghändelser när datorn körs. Exempel på sådana data är: operativsystemets typ och version, operativsystemloggar (Windows-händelseloggar), processer som körs, datornamn, IP-adresser, inloggad användare och klient-ID. Microsoft Monitoring Agent läser händelseloggposter och ETW-spårning och kopierar dem till din arbetsyta/dina arbetsytor för analys. Microsoft Monitoring Agent kopierar också kraschdumpfiler till dina arbetsytor.

Om du använder Azure Security Center Free, kan du inaktivera datainsamling från virtuella datorer i säkerhetsprincipen. Insamling av data krävs för prenumerationer på standardnivån. Funktionerna för ögonblicksbilder av virtuella datordisker och artefaktinsamling är fortfarande aktiverade även om datainsamling har inaktiverats.


## <a name="see-also"></a>Se även
I det här dokumentet har du lärt dig hur data hanteras och skyddas i Azure Security Center. Mer information om Azure Security Center finns här:

* [Planerings- och användningsguide för Azure Security Center](security-center-planning-and-operations-guide.md) – Lär dig mer om planering och viktiga designaspekter när du ska börja använda Azure Security Center.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md) – Lär dig hur du övervakar dina Azure-resursers hälsa.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Lär dig hur du hanterar och åtgärdar säkerhetsvarningar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md) – Lär dig hur du övervakar dina partnerlösningars hälsostatus.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/) – Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure
