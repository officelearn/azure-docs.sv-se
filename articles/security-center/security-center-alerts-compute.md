---
title: Hot identifiering för moln intern beräkning i Azure Security Center | Microsoft Docs
description: I det här avsnittet visas molnets interna beräknings aviseringar i Azure Security Center.
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
ms.date: 7/02/2019
ms.author: memildin
ms.openlocfilehash: c3fcbadf93ff72f7d2a1dca3b25ace81c9d4f1ae
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202623"
---
# <a name="threat-detection-for-cloud-native-compute-in-azure-security-center"></a>Hot identifiering för moln intern beräkning i Azure Security Center

Som en moln leverantör använder Azure Security Center den unika synbarheten för att analysera interna loggar och identifiera angrepps metoder på flera mål. Det här avsnittet visar de aviseringar som är tillgängliga för följande Azure-tjänster:

* [Azure App Service](#app-services)
* [Azure Container Service](#azure-containers) 

## Azure App Service<a name="app-services"></a>

Security Center använder molnets skala för att identifiera angrepps mål program som körs över App Service. Webb program är vanliga i moderna nätverk och avsöknings avsökningar för att hitta dessa och utnyttja svagheter. Innan de dirigeras till vissa miljöer går förfrågningar till program som körs i Azure genom flera gatewayer, där de inspekteras och loggas. Dessa data används sedan för att identifiera sårbarheter och angripare och för att lära dig nya mönster som ska användas senare.

Genom att använda den synlighet som Azure har som moln leverantör analyserar Security Center App Service interna loggar för att identifiera angrepps metodik på flera mål. Metodik omfattar till exempel omfattande genomsökning och distribuerade attacker. Den här typen av angrepp kommer vanligt vis från en liten delmängd av IP-adresser och innehåller mönster för crawlning till liknande slut punkter på flera värdar. Attackerna söker efter en sårbar sida eller ett plugin-program och kan inte identifieras från en enda värds synvinkel.

Security Center också ha åtkomst till de underliggande sand lådorna och de underliggande virtuella datorerna. Tillsammans med data utredning kan infrastrukturen berätta om artikeln, från en ny attack som cirkulerar på vilda sätt att kompromissa med kund maskiner. Därför kan Security Center identifiera attacker mot webb program länge efter att ha utnyttjats.

> [!div class="mx-tableFixed"]

|Varning|Beskrivning|
|---|---|
|**Ett misstänkt WordPress-tema har påträffats**|App Service aktivitets loggen indikerar en möjlig kod inmatnings aktivitet på din App Service-resurs.<br/> Den här misstänkta aktiviteten liknar aktivitet som manipulerar ett WordPress-tema som stöder körning av kod på Server sidan, följt av en direkt webbegäran om att anropa den manipulerade tema filen. Den här typen av aktivitet kan vara en del av en angrepps kampanj över WordPress.|
|**Anslutning till webb sida från avvikande IP-adress upptäcktes**|App Service aktivitets loggen anger en anslutning till en känslig webb sida från en käll adress som aldrig är ansluten till den. Detta kan tyda på att någon försöker göra ett brutet Force-angrepp till webbappens administrations sidor. Det kan också vara resultatet av en legitim användare som använder en ny IP-adress.|
|**En IP-adress som är ansluten till Azure App Service FTP-gränssnittet hittades i Hot information**|App Service FTP-loggar har identifierat en anslutning från en käll adress som hittades i Hot intelligens-flödet. Under den här anslutningen har en användare till gång till de sidor som listats.|
|**Webb-finger avtryck upptäckt**|I aktivitets loggen för App Service anges en möjlig webb finger avtrycks aktivitet på din App Service-resurs. <br/>Den här misstänkta aktiviteten är associerad med ett verktyg som kallas blind elefant. Verktyget finger avtrycks webb servrar och försöker identifiera installerade program och deras versioner. Angripare använder ofta verktyget för att söka efter sårbarheter i webb programmen.|
|**Misstänkt åtkomst till möjligen sårbar webb sida upptäcktes**|App Service aktivitets loggen anger att en webb sida som verkar vara känslig har öppnats. <br/>Den här misstänkta aktiviteten härstammar från en käll adress vars åtkomst mönster liknar en webb skanner. Den här typen av aktivitet är ofta kopplad till ett försök från en angripare att söka igenom ditt nätverk för att försöka få åtkomst till känsliga eller sårbara webb sidor.|
|**PHP-fil i upload-mapp**|App Service aktivitets loggen indikerar att något har åtkomst till en misstänkt PHP-sida som finns i upload-mappen. <br/>Den här typen av mapp innehåller vanligt vis PHP-filer. Förekomsten av den här typen av fil kan tyda på att det finns ett utnyttjande av eventuella sårbarheter för fil uppladdning.|
|**Ett försök att köra Linux-kommandon på en Windows App Service**|Analyser av App Service processer upptäckte ett försök att köra ett Linux-kommando på en Windows-App Service. Den här åtgärden kördes av webb programmet. Det här beteendet visas ofta under kampanjer som utnyttjar en sårbarhet i ett gemensamt webb program.|
|**Misstänkt PHP-körning har identifierats**|Dator loggar indikerar att en misstänkt PHP-process körs. Åtgärden innehöll ett försök att köra operativ Systems kommandon eller PHP-kod från kommando raden med hjälp av PHP-processen. Även om det här beteendet kan vara legitimt kan det här beteendet tyda på skadliga aktiviteter i webb program, till exempel försök att infektera webbplatser med webb gränssnitt.|
|**Process körning från tillfällig mapp**|Analys av App Service processer har identifierat en körning av en process från appens tillfälliga mapp. Även om det här beteendet kan vara legitimt kan det hända att det här beteendet visar skadliga aktiviteter i webb program.|
|**Försök att köra kommandot med hög behörighet upptäcktes**|Analyser av App Service processer har identifierat ett försök att köra ett kommando som kräver hög behörighet. Kommandot kördes i webb program kontexten. Även om det här beteendet kan vara legitimt kan det hända att det här beteendet visar skadliga aktiviteter i webb program.|

> [!NOTE]
> Security Center hot identifiering för App Service är för närvarande inte tillgängligt i moln regionerna Azure myndigheter och vår suveräna region.

## Azure Container Service<a name="azure-containers"></a>

Security Center tillhandahåller hot identifiering i real tid för dina behållare på Linux-datorer baserat på det granskade ramverket. Aviseringarna identifierar flera misstänkta Docker-aktiviteter, t. ex. skapandet av en privilegie rad behållare på en värd, en indikation på en SSH-server (Secure Shell) som körs i en Docker-behållare eller användning av kryptografisk gruv arbetare. 

Du kan använda den här informationen för att snabbt åtgärda säkerhetsproblem och förbättra säkerheten för dina behållare. Förutom Linux-identifieringar erbjuder Security Center också analyser som är mer exakta för behållare distributioner.
