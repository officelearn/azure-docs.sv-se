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
ms.date: 01/05/2020
ms.author: memildin
ms.openlocfilehash: a85ec565077ac229cd24bb7b9cf753015aa56ebf
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "76024851"
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

Om du kör en Windows-baserad App Service plan har Security Center också åtkomst till de underliggande sand lådorna och de underliggande virtuella datorerna. Tillsammans med de loggdata som anges ovan kan infrastrukturen berätta för historien, från en ny attack som cirkulerar på vilda sätt att kompromissa med kund maskiner. Det innebär att även om Security Center distribueras efter att en webbapp har utnyttjats, kan det vara möjligt att identifiera pågående attacker.

En lista över Azure App Service aviseringar finns i [referens tabellen för aviseringar](alerts-reference.md#alerts-azureappserv).

## Azure-behållare<a name="azure-containers"></a>

Security Center tillhandahåller hot identifiering i real tid för dina behållares miljöer och genererar aviseringar för misstänkta aktiviteter. Du kan använda den här informationen för att snabbt åtgärda säkerhetsproblem och förbättra säkerheten för dina containrar.

Vi identifierar hot på olika nivåer: 

* **Värdnivå** – Security Centers agent (tillgänglig på standard-nivån finns det information om [priser](security-center-pricing.md) ) övervakar Linux för misstänkta aktiviteter. Agenten utlöser aviseringar för misstänkta aktiviteter som härstammar från noden eller en behållare som körs på den. Exempel på sådana aktiviteter är identifiering av webb gränssnitt och anslutning med kända misstänkta IP-adresser.

    För en djupare insikt i säkerheten i din behållare miljö övervakar agenten molnbaserad analys. Den utlöser aviseringar om händelser som till exempel skapande av privilegierade behållare, misstänkt åtkomst till API-servrar och SSH-servrar (Secure Shell) som körs i en Docker-behållare.

    >[!NOTE]
    > Om du väljer att inte installera agenterna på dina värdar får du bara en delmängd av hot identifierings fördelarna och-aviseringarna. Du får fortfarande aviseringar om nätverks analyser och kommunikation med skadliga servrar.

    En lista över aviseringar om värd nivå finns i [referens tabellen för aviseringar](alerts-reference.md#alerts-containerhost).


* För **AKS kluster nivå**finns det en övervakning av hot identifiering som baseras på Kubernetes gransknings loggar analys. Om du vill aktivera övervakning utan **agent** lägger du till alternativet Kubernetes i din prenumeration på sidan **pris & inställningar** (se [prissättning](security-center-pricing.md)). Om du vill generera aviseringar på den här nivån övervakar Security Center dina AKS-hanterade tjänster med hjälp av de loggar som hämtats av AKS. Exempel på händelser på den här nivån är exponerade Kubernetes-instrumentpaneler, skapande av hög privilegierade roller och skapande av känsliga monteringar.

    >[!NOTE]
    > Security Center genererar identifierings aviseringar för Azure Kubernetes service-åtgärder och distributioner som inträffar när alternativet Kubernetes har Aktiver ATS i prenumerations inställningarna. 

    En lista över AKS-aviseringar på kluster nivå finns i [referens tabellen för aviseringar](alerts-reference.md#alerts-akscluster).

Dessutom övervakar vårt globala team av säkerhets forskare det hot landskapet. De lägger till maskinvaruspecifika aviseringar och sårbarheter när de upptäcks.

## <a name="next-steps"></a>Nästa steg

* Mer information om App Service-planer finns i [App Service planer](https://azure.microsoft.com/pricing/details/app-service/plans/).