---
title: "Datasäkerhet i säkerhets- och granskningslösningen i Operations Management Suite | Microsoft Docs"
description: "Det här dokumentet beskriver hur data hanteras och skyddas i säkerhets- och granskningslösningen i Operations Management Suite."
services: operations-management-suite
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: 
ms.assetid: 9cdf7deb-2a30-4672-b89f-71179ee8326a
ms.service: operations-management-suite
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/15/2016
ms.author: yurid
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: d81d3ff9c91d0056c69f5f190d3dfaa507c1e340


---
# <a name="operations-management-suite-security-and-audit-solution-data-security"></a>Datasäkerhet i säkerhets- och granskningslösningen i Operations Management Suite
[Säkerhets- och granskningslösningen i Operations Management Suite (OMS)](operations-management-suite-overview.md) hjälper dig att förhindra, upptäcka och svara på hot genom att samla in och bearbeta data om dina resurser, inklusive:

* Säkerhetshändelselogg
* ETW-händelser (Event Tracing for Windows)
* AppLocker-granskningshändelser
* Windows-brandväggslogg
* Advanced Threat Analytics-händelser
* Resultat från utvärdering av säkerhetsbaslinje
* Resultat från utvärdering av program mot skadlig kod
* Resultat från utvärdering av uppdateringar/korrigeringar
* Syslog-dataströmmar som uttryckligen har aktiverats på agenten

Vi arbetar hårt för att skydda sekretessen och säkerheten för dessa data. Microsoft följer strikta riktlinjer för efterlevnad och säkerhet – från kodning till driften av en tjänst.
Den här artikeln beskriver hur data hanteras och skyddas i säkerhets- och granskningslösningen i OMS.

## <a name="data-sources"></a>Datakällor
Säkerhets- och granskningslösningen i OMS analyserar data från virtuella och fysiska datorer som OMS-agenten är installerad på. Säkerhets- och granskningslösningen i OMS kan samla in konfigurationsinformation om säkerhetshändelser, t.ex. Windows-händelser, granskningsloggar, IIS-loggar och syslog-meddelanden. Exempel på den här typen av data är: operativsystemets typ och version, aktiva processer, datornamn, IP-adresser, inloggad användare och klient-ID.  

## <a name="data-protection"></a>Dataskydd
**Datauppdelning**: Data lagras logiskt och separat på varje komponent i tjänsten. Alla data taggas efter organisation. Den här taggningen finns kvar i informationens hela livscykel och används på varje lager i tjänsten. 

**Dataåtkomst**: För att kunna erbjuda säkerhetsrekommendationer och undersöka möjliga säkerhetshot kan Microsofts personal komma åt information som samlas in eller analyseras av tjänster. Vi följer [villkoren för Microsoft Online Services](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) och tillhörande [sekretesspolicy](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx), som garanterar att Microsoft inte använder kunddata eller härleder information från dem för reklamändamål eller i liknande kommersiellt syfte. För att kunna erbjuda säkerhetsrekommendationer och undersöka möjliga säkerhetshot kan Microsofts personal komma åt information som samlas in eller analyseras av tjänster. Vi använder bara kunddata i den mån det är nödvändigt för att tillhandahålla Azure-tjänsterna, samt för därtill relaterade ändamål. Du äger alla rättigheter till dina egna data.

**Dataanvändning**: Microsoft använder mönster och hotinformation som identifieras från flera klientorganisationer i syfte att förbättra våra skydds- och identifieringsfunktioner. Vi gör detta i enlighet med våra sekretessåtaganden som beskrivs i [sekretesspolicyn](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx).

> [!NOTE]
> Platsen för data konfigureras på arbetsytenivå i OMS när arbetsytan skapas, vilket är ett av stegen i den ursprungliga konfigurationsprocessen för säkerhets- och granskningslösningen i OMS.
> 
> 

## <a name="see-also"></a>Se även
I det här dokumentet har du lärt dig hur data hanteras och skyddas i OMS. Mer information om säkerhets- och granskningslösningen i OMS finns här:

* [Översikt över Operations Management Suite (OMS)](operations-management-suite-overview.md)
* [Övervaka och svara på säkerhetsaviseringar i säkerhets- och granskningslösningen i Operations Management Suite](oms-security-responding-alerts.md)
* [Övervaka resurser i säkerhets- och granskningslösningen i Operations Management Suite](oms-security-monitoring-resources.md)




<!--HONumber=Nov16_HO2-->


