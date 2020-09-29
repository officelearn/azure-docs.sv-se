---
title: Azure Defender för Kubernetes – fördelar och funktioner
description: Lär dig mer om fördelarna och funktionerna i Azure Defender för Kubernetes.
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 3308a72421b851402642f12daf56359c7e3c9216
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/29/2020
ms.locfileid: "91449076"
---
# <a name="introduction-to-azure-defender-for-kubernetes"></a>Introduktion till Azure Defender för Kubernetes

Azure Kubernetes service (AKS) är Microsofts hanterade tjänst för utveckling, distribution och hantering av program i behållare.

Azure Security Center och AKS utgör det bästa molnet Kubernetes säkerhets erbjudande och tillsammans de ger miljö härdning, skydd av arbets belastning och kör tids skydd som beskrivs nedan.

För hot identifiering för dina Kubernetes-kluster aktiverar du **Azure Defender för Kubernetes**.

Hot identifiering på värdnivå för dina Linux AKS-noder är tillgängligt om du aktiverar [Azure Defender för servrar](defender-for-servers-introduction.md).

## <a name="availability"></a>Tillgänglighet

|Aspekt|Information|
|----|:----|
|Versions tillstånd:|Allmänt tillgänglig (GA)|
|Priset|**Azure Defender for Kubernetes** faktureras så som det visas på [sidan med priser](security-center-pricing.md)|
|Nödvändiga roller och behörigheter:|**Säkerhets administratören** kan stänga aviseringar.<br>**Säkerhets läsaren** kan visa resultat.|
|Moln|![Ja](./media/icons/yes-icon.png) Kommersiella moln<br>![Inga](./media/icons/no-icon.png) National/suverän (US Gov, Kina gov, andra gov)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-kubernetes"></a>Vilka är fördelarna med Azure Defender för Kubernetes?

### <a name="run-time-protection"></a>Kör tids skydd

Genom kontinuerlig analys av följande AKS-källor tillhandahåller Security Center real tids skydd för dina behållares miljöer och genererar aviseringar för hot och skadlig aktivitet som identifieras på värd- *och* AKS-klustret. Du kan använda den här informationen för att snabbt åtgärda säkerhetsproblem och förbättra säkerheten för dina containrar.

Security Center ger hot skydd på olika nivåer: 

- **Värdnivå (tillhandahålls av Azure Defender för servrar)** – med samma Log Analytics-agent som Security Center använder på andra virtuella datorer, övervakar Azure Defender dina Linux AKS-noder för misstänkta aktiviteter som identifiering av webb gränssnitt och anslutning med kända misstänkta IP-adresser. Agenten övervakar även certifikatbaserad analys, till exempel skapande av privilegierade behållare, misstänkt åtkomst till API-servrar och SSH-servrar (Secure Shell) som körs i en Docker-behållare.

    En lista över AKS-aviseringar om värd nivå finns i [referens tabellen för aviseringar](alerts-reference.md#alerts-containerhost).

    >[!IMPORTANT]
    > Om du väljer att inte installera agenterna på dina värdar får du bara en del av fördelarna med skydd mot hot och säkerhets aviseringar. Du får fortfarande aviseringar om nätverks analyser och kommunikation med skadliga servrar.

- **AKS-kluster nivå (tillhandahålls av Azure Defender för Kubernetes)** – på kluster nivå baseras skydd mot hot på analys av Kubernetes gransknings loggar. Aktivera Azure Defender om du vill aktivera övervakning utan **agent** . Om du vill generera aviseringar på den här nivån övervakar Security Center dina AKS-hanterade tjänster med hjälp av de loggar som hämtats av AKS. Exempel på händelser på den här nivån är exponerade Kubernetes-instrumentpaneler, skapande av hög privilegierade roller och skapande av känsliga monteringar.

    En lista över AKS-aviseringar på kluster nivå finns i [referens tabellen för aviseringar](alerts-reference.md#alerts-akscluster).

    >[!NOTE]
    > Security Center genererar säkerhets aviseringar för Azure Kubernetes service-åtgärder och distributioner som inträffar när alternativet Kubernetes har Aktiver ATS i prenumerations inställningarna. 

Dessutom övervakar vårt globala team av säkerhets forskare det hot landskapet. De lägger till maskinvaruspecifika aviseringar och sårbarheter när de upptäcks.

> [!TIP]
> Du kan simulera behållar aviseringar genom att följa anvisningarna i [det här blogg inlägget](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270).



## <a name="how-does-security-centers-kubernetes-protection-work"></a>Hur fungerar Security Center Kubernetes Protection?

Nedan visas ett diagram med hög nivå på interaktionen mellan Azure Security Center, Azure Kubernetes-tjänsten och Azure Policy.

Du kan se att de objekt som har tagits emot och analyseras av Security Center inkluderar:

- gransknings loggar från API-servern
- rå säkerhets händelser från Log Analytics agent
- kluster konfigurations information från AKS-klustret
- arbets belastnings konfiguration från Azure Policy (via **Azure policy-tillägget för Kubernetes**). [Lär dig mer om bästa metoder för arbets belastnings skydd med Kubernetes-åtkomstkontroll](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control)

:::image type="content" source="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png" alt-text="Övergripande arkitektur av interaktionen mellan Azure Security Center, Azure Kubernetes-tjänsten och Azure Policy" lightbox="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png":::




## <a name="azure-defender-for-kubernetes---faq"></a>Azure Defender för Kubernetes – vanliga frågor och svar

### <a name="can-i-still-get-aks-protections-without-the-log-analytics-agent"></a>Kan jag fortfarande få AKS-skydd utan Log Analytics-agenten?

Som nämnts ovan ger de valfria **Azure Defender for Kubernetes** -planen skydd på kluster nivå, Log Analytics agenten för **Azure Defender för servrar** skyddar dina noder. 

Vi rekommenderar att du distribuerar båda, för det mest kompletta skyddet.

Om du väljer att inte installera agenten på dina värdar får du bara en del av fördelarna med skydd mot hot och säkerhets aviseringar. Du får fortfarande aviseringar om nätverks analyser och kommunikation med skadliga servrar.


## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om Security Center Kubernetes Protection, inklusive Azure Defender för Kubernetes. 

Information om relaterade material finns i följande artiklar: 

- [Aktivera Azure Defender](security-center-pricing.md)
- [Exportera aviseringar till en Azure Sentinel eller en SIEM från tredje part](continuous-export.md)
- [Referens tabell för aviseringar](alerts-reference.md)