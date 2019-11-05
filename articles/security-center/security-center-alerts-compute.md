---
title: Hot identifiering för inbyggd molnbaserad data behandling i Azure Security Center | Microsoft Docs
description: Den här artikeln visar de molnbaserade interna beräknings aviseringar som finns i Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 5aa5efcf-9f6f-4aa1-9f72-d651c6a7c9cd
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: f6fba7bc8e8b7d040805f0be62d436caebf638af
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73520907"
---
# <a name="threat-detection-for-cloud-native-computing-in-azure-security-center"></a>Hot identifiering för inbyggd molnbaserad data behandling i Azure Security Center

Som en inbyggd lösning har Azure Security Center unik insyn i interna loggar för att kunna identifiera attack metoder över flera mål. Den här artikeln visar de aviseringar som är tillgängliga för följande Azure-tjänster:

* [Azure App Service](#app-services)
* [Azure-behållare](#azure-containers) 

> [!NOTE]
> Dessa tjänster är för närvarande inte tillgängliga i Azure myndigheter och suveräna moln regioner.

## Azure App Service<a name="app-services"></a>

Security Center använder molnets skala för att identifiera angrepps mål program som körs över App Service. Angripare avsöker webb program för att hitta och utnyttja svagheter. Innan de dirigeras till vissa miljöer går förfrågningar till program som körs i Azure genom flera gatewayer, där de inspekteras och loggas. Dessa data används sedan för att identifiera sårbarheter och angripare och för att lära dig nya mönster som ska användas senare.

Genom att använda den synlighet som Azure har som moln leverantör analyserar Security Center App Service interna loggar för att identifiera angrepps metodik på flera mål. Metodik omfattar till exempel omfattande genomsökning och distribuerade attacker. Den här typen av angrepp kommer vanligt vis från en liten delmängd av IP-adresser och visar mönster för crawlning till liknande slut punkter på flera värdar. Attackerna söker efter en sårbar sida eller ett plugin-program och kan inte identifieras från en enda värds synvinkel.

Security Center också ha åtkomst till de underliggande sand lådorna och de underliggande virtuella datorerna. Tillsammans med data utredning kan infrastrukturen berätta om artikeln, från en ny attack som cirkulerar på vilda sätt att kompromissa med kund maskiner. Det innebär att även om Security Center distribueras efter att en webbapp har utnyttjats, kan det vara möjligt att identifiera pågående attacker.

> [!div class="mx-tableFixed"]

|Varning|Beskrivning|
|---|---|
|**Ett misstänkt WordPress-tema har påträffats**|App Service aktivitets loggen indikerar en möjlig kod inmatnings aktivitet på din App Service-resurs.<br/> Den här misstänkta aktiviteten liknar aktivitet som manipulerar ett WordPress-tema som stöder körning av kod på Server sidan, följt av en direkt webbegäran om att anropa den manipulerade tema filen. Den här typen av aktivitet kan vara en del av en angrepps kampanj över WordPress.|
|**Anslutning till webb sida från avvikande IP-adress upptäcktes**|App Service aktivitets loggen anger en anslutning till en känslig webb sida från en käll adress som aldrig är ansluten till den. Detta kan tyda på att någon försöker göra ett brutet Force-angrepp till webbappens administrations sidor. Det kan också vara resultatet av en legitim användare som använder en ny IP-adress.|
|**En IP-adress som är ansluten till Azure App Service FTP-gränssnittet hittades i Hot information**|App Service FTP-loggar har identifierat en anslutning från en käll adress som hittades i Hot intelligens-flödet. Under den här anslutningen har en användare till gång till de sidor som listats.|
|**Webb-finger avtryck upptäckt**|I aktivitets loggen för App Service anges en möjlig webb finger avtrycks aktivitet på din App Service-resurs. <br/>Den här misstänkta aktiviteten är associerad med ett verktyg som kallas blind elefant. Verktyget finger avtrycks webb servrar och försöker identifiera installerade program och deras versioner. Angripare använder ofta verktyget för att söka efter sårbarheter i webb programmen.|
|**Misstänkt åtkomst till möjligen sårbar webb sida upptäcktes**|App Service aktivitets loggen anger att en webb sida som verkar vara känslig har öppnats. <br/>Den här misstänkta aktiviteten härstammar från en käll adress vars åtkomst mönster liknar en webb skanner. Den här typen av aktivitet är ofta kopplad till ett försök från en angripare att söka igenom nätverket och försöka få åtkomst till känsliga eller sårbara webb sidor.|
|**PHP-fil i upload-mapp**|App Service aktivitets loggen indikerar att något har åtkomst till en misstänkt PHP-sida som finns i upload-mappen. <br/>Den här typen av mapp innehåller vanligt vis PHP-filer. Förekomsten av den här typen av fil kan tyda på att det finns ett utnyttjande av eventuella sårbarheter för fil uppladdning.|
|**Ett försök att köra Linux-kommandon på en Windows App Service**|Analyser av App Service processer upptäckte ett försök att köra ett Linux-kommando på en Windows-App Service. Den här åtgärden kördes av webb programmet. Det här beteendet visas ofta under kampanjer som utnyttjar en sårbarhet i ett gemensamt webb program.|
|**Misstänkt PHP-körning har identifierats**|Dator loggar indikerar att en misstänkt PHP-process körs. Åtgärden innehöll ett försök att köra operativ Systems kommandon eller PHP-kod från kommando raden med hjälp av PHP-processen. Även om det här beteendet kan vara legitimt kan det här beteendet tyda på skadliga aktiviteter i webb program, till exempel försök att infektera webbplatser med webb gränssnitt.|
|**Process körning från tillfällig mapp**|Analys av App Service processer har identifierat en körning av en process från appens tillfälliga mapp. Även om det här beteendet kan vara legitimt kan det hända att det här beteendet visar skadliga aktiviteter i webb program.|
|**Försök att köra kommandot med hög behörighet upptäcktes**|Analyser av App Service processer har identifierat ett försök att köra ett kommando som kräver hög behörighet. Kommandot kördes i webb program kontexten. Även om det här beteendet kan vara legitimt kan det hända att det här beteendet visar skadliga aktiviteter i webb program.|

## Azure-behållare<a name="azure-containers"></a>

Security Center tillhandahåller hot identifiering i real tid för dina behållares miljöer och genererar aviseringar för misstänkta aktiviteter. Du kan använda den här informationen för att snabbt åtgärda säkerhetsproblem och förbättra säkerheten för dina behållare.

Vi identifierar hot på olika nivåer: 

* **Värdnivå** – Security Centers agent (tillgänglig på standard-nivån finns det information om [priser](security-center-pricing.md) ) övervakar Linux för misstänkta aktiviteter. Agenten utlöser aviseringar för misstänkta aktiviteter som härstammar från noden eller en behållare som körs på den. Exempel på sådana aktiviteter är identifiering av webb gränssnitt och anslutning med kända misstänkta IP-adresser. </br>
För en djupare insikt i säkerheten i din behållare miljö övervakar agenten molnbaserad analys. Den utlöser aviseringar om händelser som till exempel skapande av privilegierade behållare, misstänkt åtkomst till API-servrar och SSH-servrar (Secure Shell) som körs i en Docker-behållare.

    >[!NOTE]
    > Om du väljer att inte installera agenterna på dina värdar får du bara en delmängd av hot identifierings fördelarna och-aviseringarna. Du får fortfarande aviseringar om nätverks analyser och kommunikation med skadliga servrar.

* För **AKS kluster nivå**finns det hot identifierings övervakning baserat på Kubernetes gransknings loggar analys. Om du vill aktivera övervakning utan **agent** lägger du till alternativet Kubernetes i din prenumeration på sidan **pris & inställningar** (se [prissättning](security-center-pricing.md)). Om du vill generera aviseringar på den här nivån övervakar Security Center dina AKS-hanterade tjänster med hjälp av de loggar som hämtats av AKS. Exempel på händelser på den här nivån är exponerade Kubernetes-instrumentpaneler, skapandet av hög privilegierade roller och känsliga monteringar. 

    >[!NOTE]
    > Security Center genererar identifierings aviseringar för Azure Kubernetes service-åtgärder och distributioner som inträffar när alternativet Kubernetes har Aktiver ATS i prenumerations inställningarna. 

Dessutom övervakar vårt globala team av säkerhets forskare det hot landskapet. De lägger till maskinvaruspecifika aviseringar och sårbarheter när de upptäcks.