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
ms.date: 11/05/2019
ms.author: memildin
ms.openlocfilehash: 6b6acb0ae1452795fe02906779b920e4b41f9a55
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748390"
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
|**Anslutning till webb sida från avvikande IP-adress upptäcktes**|App Service aktivitets loggen anger en anslutning till en känslig webb sida från en käll adress som aldrig är ansluten till den. Den här anslutningen kan tyda på att någon försöker orsaka ett brutet Force-angrepp i webbappens administrations sidor. Det kan också vara resultatet av en legitim användare som använder en ny IP-adress.|
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

* **Värdnivå** – Security Centers agent (tillgänglig på standard-nivån finns det information om [priser](security-center-pricing.md) ) övervakar Linux för misstänkta aktiviteter. Agenten utlöser aviseringar för misstänkta aktiviteter som härstammar från noden eller en behållare som körs på den. Exempel på sådana aktiviteter är identifiering av webb gränssnitt och anslutning med kända misstänkta IP-adresser.

    För en djupare insikt i säkerheten i din behållare miljö övervakar agenten molnbaserad analys. Den utlöser aviseringar om händelser som till exempel skapande av privilegierade behållare, misstänkt åtkomst till API-servrar och SSH-servrar (Secure Shell) som körs i en Docker-behållare.

    >[!NOTE]
    > Om du väljer att inte installera agenterna på dina värdar får du bara en delmängd av hot identifierings fördelarna och-aviseringarna. Du får fortfarande aviseringar om nätverks analyser och kommunikation med skadliga servrar.

* För **AKS kluster nivå**finns det en övervakning av hot identifiering som baseras på Kubernetes gransknings loggar analys. Om du vill aktivera övervakning utan **agent** lägger du till alternativet Kubernetes i din prenumeration på sidan **pris & inställningar** (se [prissättning](security-center-pricing.md)). Om du vill generera aviseringar på den här nivån övervakar Security Center dina AKS-hanterade tjänster med hjälp av de loggar som hämtats av AKS. Exempel på händelser på den här nivån är exponerade Kubernetes-instrumentpaneler, skapande av hög privilegierade roller och skapande av känsliga monteringar.

    >[!NOTE]
    > Security Center genererar identifierings aviseringar för Azure Kubernetes service-åtgärder och distributioner som inträffar när alternativet Kubernetes har Aktiver ATS i prenumerations inställningarna. 

Dessutom övervakar vårt globala team av säkerhets forskare det hot landskapet. De lägger till maskinvaruspecifika aviseringar och sårbarheter när de upptäcks.


### <a name="aks-cluster-level-alerts"></a>AKS-aviseringar på kluster nivå

> [!div class="mx-tableFixed"]

|Varning|Beskrivning|
|---|---|
|**För hands version – roll bindning till rollen kluster-admin har identifierats**|Kubernetes gransknings loggs analys upptäckte en ny bindning till kluster administratörs rollen som resulterade i administratörs behörighet. Onödigt att ge administratörs behörighet kan leda till problem med eskalering av privilegier i klustret.|
|**För hands VERSIONs Kubernetes-instrumentpanelen har identifierats**|Kubernetes Gransknings logg analys upptäckte exponering av Kubernetes-instrumentpanelen av en LoadBalancer-tjänst. Exponerade instrument paneler tillåter oautentiserad åtkomst till kluster hantering och utgör en säkerhets risk.|
|**För hands version – ny roll med hög behörighet har identifierats**|Kubernetes Gransknings logg analys upptäckte en ny roll med hög behörighet. En bindning till en roll med hög behörighet ger utökade privilegier för användare/grupp i klustret. Om du inte behöver utökade privilegier kan det leda till problem med eskalering av privilegier i klustret.|
|**För hands version – ny behållare i Kube-systemets namnrymd identifieras**|Kubernetes gransknings loggs analys identifierade en ny behållare i Kube-systemets namnrymd som inte är bland de behållare som normalt körs i det här namn området. Kube-systemets namn rymder får inte innehålla användar resurser. Angripare kan använda det här namn området för att dölja skadliga komponenter.|
|**PREVIEW – utvinnings behållare för digital valuta har identifierats**|Kubernetes Gransknings logg analys har identifierat en behållare som har en avbildning kopplad till ett verktyg för digital valuta utvinning.|
|**Förhands granskning-identifierad behållare**|Kubernetes Gransknings logg analys upptäckte en ny privilegie rad behållare. En privilegie rad behållare har åtkomst till nodens resurser och bryter isoleringen mellan behållare. Om en angripare har komprometterats kan en angripare använda den privilegierade behållaren för att få åtkomst till noden.|
|**PREVIEW-container med en känslig volym montering har identifierats**|Kubernetes Gransknings logg analys upptäckte en ny behållare med en känslig volym montering. Den volym som upptäcktes är en hostPath-typ som monterar en känslig fil eller mapp från noden till behållaren. Om behållaren komprometteras kan angriparen använda den här monteringen för att få åtkomst till noden.|



### <a name="host-level-alerts"></a>Värd nivå varningar

> [!div class="mx-tableFixed"]

|Varning|Beskrivning|
|---|---|
|**Privilegie rad behållare upptäcktes**|Dator loggar indikerar att en privilegie rad Docker-behållare körs. En privilegie rad behållare har fullständig åtkomst till värdens resurser. Om en angripare har komprometterats kan en angripare använda den privilegierade behållaren för att få åtkomst till värddatorn.|
|**Privilegie rad kommando körning i behållare**|Dator loggar indikerar att ett privilegie rad kommando kördes i en Docker-behållare. Ett privilegie rad kommando har utökade privilegier på värddatorn.|
|**Exponerad Docker-daemon identifierad**|Dator loggar indikerar att Docker daemon (dockerd) exponerar en TCP-socket. Docker-konfigurationen använder som standard inte kryptering eller autentisering när en TCP-socket är aktive rad. Alla som har åtkomst till den relevanta porten kan sedan få fullständig åtkomst till Docker daemon.|
|**SSH-servern körs i en behållare**|Dator loggar indikerar att en SSH-server körs i en Docker-behållare. Även om det här beteendet kan vara avsiktligt, tyder det ofta på att en behållare är felkonfigurerad eller överträtt.|
|**Container med en Miner-avbildning identifierad**|Dator loggar indikerar körning av en Docker-behållare som kör en avbildning som är kopplad till den digitala valuta utvinning. Detta kan bero på att dina resurser missbrukas.|
|**Misstänkt begäran till Kubernetes-API**|Dator loggar indikerar att en misstänkt begäran gjordes till Kubernetes-API: et. Begäran skickades från en Kubernetes-nod, eventuellt från en av de behållare som körs i noden. Även om det här beteendet kan vara avsiktligt kan det tyda på att noden kör en komprometterad behållare.|
|**Misstänkt förfrågan till Kubernetes-instrumentpanelen**|Dator loggar indikerar att en misstänkt begäran gjordes till Kubernetes-instrumentpanelen. Begäran skickades från en Kubernetes-nod, eventuellt från en av de behållare som körs i noden. Även om det här beteendet kan vara avsiktligt kan det tyda på att noden kör en komprometterad behållare.|