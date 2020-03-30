---
title: Vanliga frågor och svar om Azure Security Center – Allmänna frågor
description: Vanliga frågor allmänna frågor om Azure Security Center, en produkt som hjälper dig att förebygga, upptäcka och svara på hot
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: f9043ae1414b63f25583d52100774f3e87754a74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77661847"
---
# <a name="faq---general-questions-about-azure-security-center"></a>Vanliga frågor och svar – Allmänna frågor om Azure Security Center

## <a name="what-is-azure-security-center"></a>Vad är Azure Security Center?
Azure Security Center hjälper dig att förebygga, identifiera och svara på hot med ökad insyn i och kontroll över säkerheten för dina resurser. Härifrån kan du övervaka och hantera principer för alla prenumerationer på en gång och upptäcka hot som annars kanske skulle förbli oupptäckta. Azure Security Center fungerar tillsammans med ett vittomfattande ekosystem med säkerhetslösningar.

Security Center använder Microsoft Monitoring Agent för att samla in och lagra data. Detaljerad information finns i [Datainsamling i Azure Security Center](security-center-enable-data-collection.md).


## <a name="how-do-i-get-azure-security-center"></a>Hur skaffar jag Azure Security Center?
Azure Security Center är aktiverat med din Microsoft Azure-prenumeration och nås från [Azure-portalen](https://azure.microsoft.com/features/azure-portal/). Om du vill komma åt [den loggar du in på portalen,](https://portal.azure.com)väljer **Bläddra**och bläddrar till **Säkerhetscenter**.


## <a name="which-azure-resources-are-monitored-by-azure-security-center"></a>Vilka Azure-resurser övervakas av Azure Security Center?
Azure Security Center övervakar följande Azure-resurser:

* Virtuella datorer (VMs) (inklusive [molntjänster)](../cloud-services/cloud-services-choose-me.md)
* Skalningsuppsättningar för virtuella datorer
*  Azure Virtual Networks
* Containrar
* Azure SQL-tjänst
* Azure Storage-konto
* Azure Web Apps (i [App Service Environment)](../app-service/environment/intro.md)
* Partnerlösningar integrerade med din Azure-prenumeration, till exempel brandvägg för webbprogram på virtuella datorer och i App Service Environment

Dessutom kan datorer som inte är Azure (inklusive lokala) också övervakas av Azure Security Center. Både [Windows-maskiner](./quick-onboard-windows-computer.md) och [Linux-maskiner](./quick-onboard-linux-computer.md) stöds.


## <a name="how-can-i-see-the-current-security-state-of-my-azure-resources"></a>Hur kan jag se det aktuella säkerhetstillståndet för mina Azure-resurser?
Sidan **Översikt över säkerhetscenter** visar den övergripande säkerhetspositionen för din miljö uppdelad efter beräkning, nätverk, lagring & data och program. Varje resurstyp har en indikator som visar identifierade säkerhetsproblem. Om du klickar på varje panel visas en lista över säkerhetsproblem som identifierats av Security Center, tillsammans med en inventering av resurserna i prenumerationen.



## <a name="what-is-a-security-policy"></a>Vad är en säkerhetsprincip?
En säkerhetsprincip definierar den uppsättning kontroller som rekommenderas för resurser inom den angivna prenumerationen. I Azure Security Center definierar du principer för dina Azure-prenumerationer enligt företagets säkerhetskrav och typen av program eller känslighet för data i varje prenumeration.

Säkerhetsprinciperna som är aktiverade i Azure Security Center kör säkerhetsrekommendationer och övervakning. Mer information om säkerhetsprinciper finns [i Hälsoövervakning för säkerhet i Azure Security Center](security-center-monitoring.md).


## <a name="who-can-modify-a-security-policy"></a>Vem kan ändra en säkerhetsprincip?
Om du vill ändra en säkerhetsprincip måste du vara säkerhetsadministratör eller ägare eller deltagare i prenumerationen.

Mer information om hur du konfigurerar en säkerhetsprinciper finns [i Ange säkerhetsprinciper i Azure Security Center](tutorial-security-policy.md).


## <a name="what-is-a-security-recommendation"></a>Vad är en säkerhetsrekommendation?
Azure Security Center analyserar säkerhetstillståndet hos dina Azure-resurser. När potentiella säkerhetsproblem identifieras skapas rekommendationer. Rekommendationerna guidar dig genom processen att konfigurera den nödvändiga kontrollen. Några exempel:

* Etablering av skadlig kod mot skadlig kod för att identifiera och ta bort skadlig programvara
* [Nätverkssäkerhetsgrupper](../virtual-network/security-overview.md) och regler för att styra trafiken till virtuella datorer
* Etablering av en brandvägg för webbprogram för att skydda mot attacker som riktar sig mot dina webbprogram
* genomföra alla systemuppdateringar som fattas
* se till att operativsystemen är konfigurerade enligt rekommenderade baslinjer

Endast rekommendationer som är aktiverade i säkerhetsprinciper visas här.



## <a name="what-triggers-a-security-alert"></a>Vad utlöser en säkerhetsavisering?
Azure Security Center samlar automatiskt in, analyserar och säkringar loggar data från dina Azure-resurser, nätverket och partnerlösningar som antimalware och brandväggar. Om hot upptäcks skapas en säkerhetsavisering. Exempel på hot:

* angripna virtuella datorer som kommunicerar med IP-adresser som man vet är skadliga
* Avancerad skadlig kod som upptäckts med windows-felrapportering
* nyckelsökningsangrepp mot virtuella datorer
* Säkerhetsvarningar från integrerade partnersäkerhetslösningar som Anti-Malware eller Brandväggar för webbprogram


## <a name="why-did-secure-score-values-change"></a>Varför ändrades värdena för säker poäng? <a name="secure-score-faq"></a>
Från och med februari 2019 justerade Security Center poängen för några rekommendationer för att bättre passa deras svårighetsgrad. Som ett resultat av den här justeringen kan det finnas ändringar i de övergripande värdena för säker poäng.  Mer information om Säker poäng finns i [Beräkning av säker poäng](security-center-secure-score.md).


## <a name="whats-the-difference-between-threats-detected-and-alerted-on-by-microsoft-security-response-center-versus-azure-security-center"></a>Vad är skillnaden mellan hot som upptäcks och varnas av Microsoft Security Response Center kontra Azure Security Center?
Microsoft Security Response Center (MSRC) utför utvald säkerhetsövervakning av Azure-nätverket och Azure-infrastrukturen och tar emot hotinformation och missbruksklagomål från tredje part. När MSRC får kännedom om att kunddata har använts av en olaglig eller obehörig part eller att kundens användning av Azure inte överensstämmer med villkoren för godtagbar användning, meddelar en säkerhetsincidenthanterare kunden. Meddelande sker vanligtvis genom att skicka ett e-postmeddelande till de säkerhetskontakter som anges i Azure Security Center eller Azure-prenumerationsägaren om en säkerhetskontakt inte har angetts.

Security Center är en Azure-tjänst som kontinuerligt övervakar kundens Azure-miljö och tillämpar analyser för att automatiskt identifiera ett brett spektrum av potentiellt skadlig aktivitet. Dessa identifieringar visas som säkerhetsaviseringar på instrumentpanelen i Security Center.