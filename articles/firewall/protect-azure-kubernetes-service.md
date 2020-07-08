---
title: Använd Azure-brandväggen för att skydda AKS-distributioner (Azure Kubernetes service)
description: Lär dig hur du använder Azure Firewall för att skydda AKS-distributioner (Azure Kubernetes service)
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 07/02/2020
ms.author: victorh
ms.openlocfilehash: 81d65954197c0ebe0de77dc2fea63239d4c3f17b
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2020
ms.locfileid: "86056675"
---
# <a name="use-azure-firewall-to-protect-azure-kubernetes-service-aks-deployments"></a>Använd Azure-brandväggen för att skydda AKS-distributioner (Azure Kubernetes service)

Azure Kubernetes service (AKS) erbjuder ett hanterat Kubernetes-kluster på Azure. Det minskar komplexiteten och driften för att hantera Kubernetes genom att avlasta mycket av det ansvaret till Azure. AKS hanterar kritiska aktiviteter, till exempel hälso övervakning och underhåll åt dig och levererar ett säkert kluster i företags klass med enklare styrning.

Kubernetes dirigerar kluster av virtuella datorer och schemalägger behållare som ska köras på de virtuella datorerna baserat på de tillgängliga beräknings resurserna och resurs kraven för varje behållare. Behållare grupperas i poddar, den grundläggande operativa enheten för Kubernetes och de poddar skalar till det tillstånd som du vill ha.

I hanterings-och drift syfte måste noder i ett AKS-kluster ha åtkomst till vissa portar och fullständiga domän namn (FQDN). De här åtgärderna kan vara till för att kommunicera med API-servern eller ladda ned och installera kärn Kubernetes-kluster komponenter och nodens säkerhets uppdateringar. Azure-brandväggen kan hjälpa dig att låsa din miljö och filtrera utgående trafik.

Följ rikt linjerna i den här artikeln för att ge ytterligare skydd för ditt Azure Kubernetes-kluster med hjälp av Azure-brandväggen.

## <a name="prerequisites"></a>Krav

- Ett distribuerat Azure Kubernetes-kluster med program som körs.

   Mer information finns i [Självstudier: Distribuera ett Azure Kubernetes service (AKS)-kluster](../aks/tutorial-kubernetes-deploy-cluster.md) och [Självstudier: köra program i Azure KUBERNETES service (AKS)](../aks/tutorial-kubernetes-deploy-application.md).


## <a name="securing-aks"></a>Skydda AKS

Azure-brandväggen tillhandahåller en AKS FQDN-tagg för att förenkla konfigurationen. Använd följande steg för att tillåta utgående AKS-plattforms trafik:

- När du använder Azure-brandväggen för att begränsa utgående trafik och skapar en användardefinierad väg (UDR) för att dirigera all utgående trafik, måste du se till att du skapar en lämplig DNAT-regel i brand väggen för att tillåta inkommande trafik korrekt. 

   Genom att använda Azure-brandväggen med en UDR bryts den inkommande installationen på grund av asymmetrisk routning. Problemet uppstår om AKS-undernätet har en standard väg som går till brand väggens privata IP-adress, men du använder en offentlig belastningsutjämnare. Till exempel inkommande eller Kubernetes tjänst av typen *Loadbalancer*.

   I det här fallet tas den inkommande belastnings Utjämnings trafiken emot via dess offentliga IP-adress, men retur vägen går genom brand väggens privata IP-adress. Eftersom brand väggen är tillstånds känslig, släpps det returnerade paketet eftersom brand väggen inte är medveten om en etablerad session. Information om hur du integrerar Azure-brandväggen med ingångs-eller tjänst belastnings utjämning finns i [integrera Azure-brandväggen med azure standard Load Balancer](integrate-lb.md).
- Skapa en regel samling för program och Lägg till en regel för att aktivera *AzureKubernetesService* FQDN-taggen. Käll-IP-adressintervallet är det virtuella nätverk som är värd för poolen, protokollet är https och målet är AzureKubernetesService.
- Följande utgående portar/nätverks regler krävs för ett AKS-kluster:

   - TCP-port 443
   - TCP [*IPAddrOfYourAPIServer*]: 443 krävs om du har en app som behöver kommunicera med API-servern. Den här ändringen kan anges efter att klustret har skapats.
   - TCP-port 9000 och UDP-port 1194 för tunnelns front-Pod för att kommunicera med tunnel slut på API-servern.

      Mer information finns i **. HCP. <location> . azmk8s.io* och adresser i följande tabell.
   - UDP-port 123 för NTP-tidssynkronisering (Network Time Protocol) (Linux-noder).
   - UDP-port 53 för DNS krävs också om du har poddar direkt åtkomst till API-servern.
- Konfigurera AzureMonitor-och Storage Service-taggar. Azure Monitor tar emot Log Analytics-data. 

   Du kan också tillåta URL: en för din arbets yta individuellt: `<worksapceguid>.ods.opinsights.azure.com` , och `<worksapceguid>.oms.opinsights.azure.com` . Du kan åtgärda detta på något av följande sätt:

    - Tillåt https-åtkomst från värd poolens undernät till `*. ods.opinsights.azure.com` och `*.oms. opinsights.azure.com` . Dessa jokertecken för jokertecken aktiverar nödvändig åtkomst men är mindre restriktiva.
    - Använd följande Log Analytics-fråga för att lista de exakta FQDN: er som krävs, och Tillåt dem uttryckligen i brand Väggs program reglerna:
   ```
   AzureDiagnostics 
   | where Category == "AzureFirewallApplicationRule" 
   | search "Allow" 
   | search "*. ods.opinsights.azure.com" or "*.oms. opinsights.azure.com"
   | parse msg_s with Protocol " request from " SourceIP ":" SourcePort:int " to " FQDN ":" * 
   | project TimeGenerated,Protocol,FQDN 
   ```


## <a name="next-steps"></a>Nästa steg

- Läs mer om Azure Kubernetes service, se [Kubernetes Core-koncept för Azure Kubernetes service (AKS)](../aks/concepts-clusters-workloads.md).
