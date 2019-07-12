---
title: Hotidentifiering för molnet interna beräkningsfunktioner i Azure Security Center | Microsoft Docs
description: Det här avsnittet innehåller molnet interna beräkningsfunktioner aviseringar tillgängliga i Azure Security Center. i Azure Security Center.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: 5aa5efcf-9f6f-4aa1-9f72-d651c6a7c9cd
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: monhaber
ms.openlocfilehash: bc3cb66d43e71777e06c6bd63dcff35e2ff19df8
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571691"
---
# <a name="threat-detection-for-cloud-native-compute-in-azure-security-center"></a>Identifiering av hot för molnet interna beräkningsfunktioner i Azure Security Center

Security Center använder unika synlighet så måste den analysera interna loggar och identifiera attack metod på flera mål som en molntjänstleverantör. Det här avsnittet innehåller aviseringarna som är tillgängliga för följande Azure-tjänster:

* [Azure App Service](#app-services)
* [Containrar](#azure-containers) 

## Azure App Service <a name="app-services"></a>

Security Center utnyttjar skalan för molnet för att identifiera hot mot kunder program som körs via Azure App Service. Med webbprogram som praktiskt taget i alla moderna nätverk, avsökning angripare för att identifiera dessa behov och utnyttja svagheter. Innan du dirigeras till specifika miljöer går begäranden till program som körs i Azure genom flera gateways där de granskas och loggas. Den här informationen används sedan för att identifiera hot, angripare, och för att lära dig nya mönster som ska användas senare.

Genom att utnyttja synlighet där Azure är en leverantör av analyserar Security Center interna App Service-loggarna för att identifiera attacker metod på flera mål. Exempelvis utökas genomsökning och distribuerade attacker. Den här typen av attack normalt kommer från en liten delmängd av IP-adresser och användarlägestråden mönster för crawling liknande slutpunkter på flera värdar, söka efter en sårbara sida eller plugin-programmet. Detta kan identifieras med hjälp av molnet men kan inte identifieras för en enda värd.

Security Center har även åtkomst till den underliggande sandbox-miljöer och virtuella datorer. Tillsammans med minne datautredning kan infrastrukturen visa budskapet, från en ny attack som förekommer i naturen till kompromisser i kundens virtuella datorer. Därför kan Security Center identifiera attacker mot webbprogram som är långt när utnyttjas.

> [!div class="mx-tableFixed"]

|Varning|Beskrivning|
|---|---|
|**Misstänkt WordPress tema anrop har identifierats**|Aktivitetsloggen för Azure App Service indikerar en möjlig kodaktivitet inmatning för din App Service-resurs.<br/> Den här misstänkt aktivitet liknar aktivitet som hanterar ett WordPress-tema för serversidan körning av kod, följt av en direkt webbegäran att anropa behandlas temafilen. Den här typen av aktivitet setts tidigare som en del av en attackkampanj över WordPress.|
|**Anslutningen till webbsidan från den avvikande IP-adress har identifierats**|Azure App Service-aktivitetsloggen anger en anslutning till en känslig webbsida från en källadress som aldrig ansluten till den tidigare. Detta kan tyda på att någon försöker en brute-force attack på sidorna för administration av Web App. Det kan också vara resultatet av en ny IP-adress som används av en legitim användare.|
|**En IP-adress som är ansluten till din Azure App Service FTP-gränssnittet hittades i Hotinformation**|Analys av Azure App Service FTP-loggar har identifierat en anslutning från en källadress som hittades i Hotinformation feed. Under den här anslutningen åtkomst en användare till de sidor som listas nedan.|
|**Web fingeravtryck har identifierats**|Azure App Service-aktivitetsloggen indikerar ett möjligt webb fingeravtryck aktivitet i din App Service-resurs. <br/>Den här misstänkt aktivitet har identifierats är associerad med ett verktyg som kallas hemlig Elefant. Verktyget fingeravtryck webbservrar och försöker identifiera installerade program och deras versioner. Angripare använder ofta det här verktyget för webbprogram för att hitta sårbarheter.|
|**Misstänkt åtkomst till utsatt för risker webbsida som har identifierats**|Azure App Service-aktivitetsloggen anger att en webbsida som verkar vara känslig användes. <br/>Den här misstänkt aktivitet som kommer från en källadress vars åtkomstmönster liknar det i en web-skanner. Den här typen av aktivitet är ofta kopplat ett försök av en angripare att söka igenom nätverket för att få tillgång till känsliga eller sårbara webbsidor.|
|**PHP-fil i ladda upp mapp**|Azure App Service-aktivitetsloggen anger något har åtkomst till en misstänkt PHP-sida som finns i mappen ladda upp. <br/>Den här typen av mapp innehåller vanligtvis inte PHP-filer. Förekomsten av den här typen av fil kan tyda på ett utnyttjande genom att dra nytta av godtyckliga filer uppladdning säkerhetsrisker.|
|**Ett försök att köra Linux-kommandon på en Windows-App Service**|Analys av App Service-processer upptäckte ett försök att köra ett Linux-kommando på en Windows-App Service. Den här åtgärden kördes av webbprogrammet. Detta visas ofta under kampanjer som har utnyttjat en sårbarhet i ett vanligt webbprogram.|
|**Misstänkt PHP-körning har identifierats**|Datorloggarna visar en som en misstänkt PHP-processen körs. Åtgärden med ett försök att köra OS-kommandon eller PHP-kod från kommandoraden med hjälp av PHP-processen. Det här beteendet kan vara legitima, i webbprogram observeras detta också i skadliga aktiviteter som försöker angripa webbplatser med web-gränssnitt.|
|**Körning av process från tillfällig mapp**|Analys av processer för App Service har identifierat en körning av en process från appens tillfällig mapp. Det här beteendet kan vara legitima i webbprogram som det här beteendet är också observerats vid skadlig aktivitet.|
|**Försök att köra Privilegierade kommando har identifierats**|Analys av processer för App Service har identifierat ett försök att köra ett kommando som kräver höga privilegier. Kommandot kördes i kontexten för web-program. Det här beteendet kan vara legitima i webbprogram som det här beteendet är också observerats vid skadlig aktivitet.|

> [!NOTE]
> Security Center-Hotidentifiering för App Service är för närvarande inte tillgänglig i Azure government och nationellt molnregioner.

Mer information om App Service-hotidentifiering aviseringar besök skydda App Service med Azure Security Center och Läs om hur du aktiverar övervakning och skydd av dina App Service-arbetsbelastningar.

## Behållare <a name="azure-containers"></a>

Security Center tillhandahåller hotidentifiering i realtid för dina behållare på Linux-datorer baserat på det auditd-ramverket. Aviseringarna som identifierar flera misstänkta Docker-aktiviteter, till exempel att skapa en privilegierad behållare på en värd, en indikation på Secure Shell (SSH)-server som körs i en Docker-behållare eller användning av kryptografi datautvinnare. Du kan använda den här informationen för att snabbt åtgärda säkerhetsproblem och förbättra säkerheten för dina behållare. Förutom Linux identifieringar erbjuder även analytics som är mer specifika för distribution av behållare i Security Center.
