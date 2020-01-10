---
title: Hot identifiering för virtuella datorer och servrar i Azure Security Center | Microsoft Docs
description: I det här avsnittet presenteras de virtuella dator-och Server aviseringarna som finns i Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: dd2eb069-4c76-4154-96bb-6e6ae553ef46
ms.service: security-center
ms.topic: conceptual
ms.date: 01/05/2020
ms.author: memildin
ms.openlocfilehash: a5ed91cef6e49fcb71c35f2262479be45a018651
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2020
ms.locfileid: "75754305"
---
# <a name="threat-detection-for-vms-and-servers-in-azure-security-center"></a>Hot identifiering för virtuella datorer och servrar i Azure Security Center

Det här avsnittet innehåller olika typer av identifierings metoder och aviseringar som är tillgängliga för virtuella datorer och servrar med följande operativ system. En lista över versioner som stöds finns i [plattformar och funktioner som stöds av Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-os-coverage).

* [Windows](#windows-machines)
* [Linux](#linux-machines)

## Aktivitets<a name="windows-machines"></a>

Azure Security Center integreras med Azure-tjänster för att övervaka och skydda dina Windows-baserade datorer. Security Center visar aviseringar och reparations förslag från alla dessa tjänster i ett lättanvänt format.

* **Microsoft Defender ATP** <a name="windows-atp"></a> – Security Center utökar sina moln skydds plattformar genom att integrera med Microsoft Defender Advanced Threat Protection (ATP). Detta ger omfattande EDR-funktioner (slut punkts identifiering och svar).

    > [!NOTE]
    > Microsoft Defender ATP-sensorn aktive ras automatiskt på Windows-servrar som använder Security Center.

    När Microsoft Defender ATP identifierar ett hot utlöses en avisering. Aviseringen visas på instrument panelen för Security Center. Från instrument panelen kan du pivotera till Microsoft Defender ATP-konsolen och utföra en detaljerad undersökning för att få fram omfattningen av angreppet. För ytterligare information om Microsoft Defender ATP, se [onboard-servrar till Microsoft Defender ATP-tjänsten](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints).

* **Analys** <a name="windows-dump"></a> av krasch dum par – när program vara kraschar fångar en kraschdump en del av minnet vid tidpunkten för kraschen.

    En krasch kan ha orsakats av skadlig kod eller innehålla skadlig kod. För att undvika att de upptäcks av säkerhets produkter använder olika former av skadlig kod en fil lös attack, vilket förhindrar skrivning till disk eller kryptering av program varu komponenter som skrivs till disk. Den här typen av attack är svår att identifiera med hjälp av traditionella diskbaserade metoder.

    Genom att använda minnes analyser kan du dock identifiera den här typen av attack. Genom att analysera minnet i krasch dumpningen kan Security Center identifiera de tekniker som angreppet använder. Till exempel kan angreppet försöka utnyttja sårbarheter i program varan, komma åt konfidentiella data och ligger gömda kvar på en komprometterad dator. Security Center fungerar detta med minimal prestanda påverkan på värdar.

    Mer information om analys aviseringar för krasch dumpning finns i [referens tabellen för aviseringar](alerts-reference.md#alerts-windows).

* **Filbaserad attack identifiering** <a name="windows-fileless"></a> – filåterställda attacker riktade mot slut punkterna är vanliga. För att undvika identifiering kan fil lösa angrepp injicera skadliga nytto laster i minnet. Angripares nytto laster finns kvar i minnet för komprometterade processer och utför en mängd olika skadliga aktiviteter.

    Med fil lösa angrepp kan automatiserade minnes kriminal tekniska-tekniker identifiera fillösa verktyg för attacker, tekniker och beteenden. Den här lösningen genomsöker regelbundet datorn vid körning och extraherar insikter direkt från minnet för säkerhets kritiska processer.

    Det hittar bevis för utnyttjande, kod inmatning och körning av skadliga nytto laster. Vid fil lös angrepp genereras detaljerade säkerhets aviseringar för att påskynda aviseringen prioritering, korrelation och underordnade svars tider. Den här metoden kompletterar event-baserade EDR-lösningar och ger större identifierings täckning.

    Mer information om aviseringar om filbaserad attack identifiering finns i [referens tabellen för aviseringar](alerts-reference.md#alerts-windows).

> [!NOTE]
> Du kan simulera Windows-aviseringar genom att hämta [Azure Security Center Spelbok: säkerhets aviseringar](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046).

## Linux<a name="linux-machines"></a>

Security Center samlar in gransknings poster från Linux-datorer med hjälp av **granskning**, ett av de vanligaste Linux-gransknings ramverken. granskade liv i Mainline-kärnan. 

* <a name="linux-auditd"></a> **Integration av Linux-granskade varningar och Microsoft Monitoring Agent (MMA)** – det granskade systemet består av ett under system i kernel-nivå, som ansvarar för att övervaka system anrop. Den filtrerar dem efter en angiven regel uppsättning och skriver meddelanden för dem till en socket. Security Center integrerar funktioner från det granskade paketet i Microsoft Monitoring Agent (MMA). Den här integrationen aktiverar insamling av granskade händelser i alla Linux-distributioner som stöds, utan krav.  

    granskade poster samlas in, berikas och sammanställs i händelser med hjälp av Linux MMA-agenten. Security Center lägger kontinuerligt till nya analyser som använder Linux-signaler för att identifiera skadliga beteenden i molnet och lokala Linux-datorer. På samma sätt som Windows-funktioner, är dessa analys omfång över misstänkta processer, misstänkta inloggnings försök, inläsning av kernel-modul och andra aktiviteter. Dessa aktiviteter kan tyda på att en dator är utsatt för en attack eller har brutits.  

    Följande är flera exempel på analyser som sträcker sig över olika faser av attackens livs cykel.

    En lista över Linux-aviseringar finns i [referens tabellen för aviseringar](alerts-reference.md#alerts-linux).

> [!NOTE]
> Du kan simulera Linux-aviseringar genom att ladda ned [Azure Security Center Spelbok: Linux-identifieringar](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef).

 
 ## <a name="next-steps"></a>Nästa steg

Exempel och mer information om Security Center identifiering finns i:

* [Hur Azure Security Center automatiserar identifieringen av cyberhot-attack](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)
* [Hur Azure Security Center identifierar sårbarheter med hjälp av administrations verktyg](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)
* [Utnyttja Azure Security Center för att upptäcka när komprometterade Linux-datorer angrips](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)
* [Azure Security Center kan identifiera nya sårbarheter i Linux](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)