---
title: Introduktion till Azure Cosmos DB-etcd API
description: Den här artikeln ger en översikt och nyckeln fördelarna med etcd API i Azure Cosmos DB
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: 30d18e1e23767ce3a179221714e001574f8e9778
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075497"
---
# <a name="introduction-to-the-azure-cosmos-db-etcd-api-preview"></a>Introduktion till Azure Cosmos DB-etcd API (förhandsversion)

Azure Cosmos DB är Microsofts globalt distribuerad databastjänst för verksamhetskritiska program. Den erbjuder nyckelfärdig global distribution, elastisk skalning av dataflöde och lagring, ensiffrig svarstid som den 99: e percentilen och garanterar hög tillgänglighet, allt uppbackat av branschledande SLA.

[Etcd](https://github.com/etcd-io/etcd) är en distribuerad nyckel/värde-lager. I [Kubernetes](https://kubernetes.io/), etcd används för att lagra status och konfiguration av Kubernetes-kluster. Att säkerställa tillgänglighet, pålitlighet och prestanda för etcd är avgörande för övergripande hälsotillstånd hos kluster, skalbarhet, flexibilitet när det gäller tillgänglighet och prestanda för ett Kubernetes-kluster. 

Etcd API i Azure Cosmos DB kan du använda Azure Cosmos DB som backend-store för [Azure Kubernetes](../aks/index.yml). etcd API i Azure Cosmos DB är för närvarande i förhandsversion. Azure Cosmos DB implementerar kabelprotokoll etcd. Med etcd API i Azure Cosmos DB utvecklare automatiskt får en mycket pålitlig [tillgängliga](high-availability.md), [globalt distribuerade](distribute-data-globally.md) Kubernetes. Detta API kan utvecklare att skala Kubernetes tillståndshantering på en helt hanterad interna PaaS-tjänst. 

> [!NOTE]
> Till skillnad från andra API: er i Azure Cosmos DB, kan inte du etablera ett etcd API-konto via Azure portal, CLI eller SDK: er. Du kan etablera ett etcd API-konto genom att distribuera Resource Manager-mallen. detaljerade anvisningar finns i [hur man etablerar Azure Kubernetes med Azure Cosmos DB](bootstrap-kubernetes-cluster.md) artikeln.  

## <a name="wire-level-compatibility"></a>Under överföring på kompatibilitet

Azure Cosmos DB implementerar kabelprotokoll etcd version 3 och gör den [överordnade noden](https://kubernetes.io/docs/concepts/overview/components/) API servrar att använda Azure Cosmos DB, precis som den skulle göra i en miljö med lokalt installerade etcd. Etcd API stöder TLS ömsesidig autentisering. 

I följande diagram visas komponenterna i ett Kubernetes-kluster. I kluster-master använder API-servern Azure Cosmos DB etcd API, i stället för lokalt installerade etcd. 

![Azure Cosmos DB implementerar etcd-kabelprotokoll](./media/etcd-api-introduction/etcd-api-wire-protocol.png)

## <a name="key-benefits"></a>Viktiga fördelar

### <a name="no-etcd-operations-management"></a>Inga etcd åtgärdshantering

Som en fullständigt hanterad interna molntjänst eliminerar Azure Cosmos DB behovet av Kubernetes-utvecklare att konfigurera och hantera etcd. Etcd API i Azure Cosmos DB är skalbar och högtillgänglig, feltolerant och hög prestanda. Arbetet med att konfigurera replikering över flera noder, utför du rullande uppdateringar, säkerhetsuppdateringar, och hälsoövervakning etcd hanteras av Azure Cosmos DB.

### <a name="global-distribution--high-availability"></a>Global distribution och hög tillgänglighet 

Med hjälp av etcd API, Azure Cosmos DB garanterar 99,99% tillgänglighet för dataläsning och skriver i en enda region och 99,999% tillgänglighet i flera regioner. 

### <a name="elastic-scalability"></a>Elastisk skalbarhet

Azure Cosmos DB erbjuder elastisk skalbarhet för Läs- och skrivförfrågningar över olika regioner.
När Kubernetes-klustret växer kan skalas Elastiskt etcd API-konto i Azure Cosmos DB utan någon avbrottstid. Etcd data lagras i Azure Cosmos DB i stället för de överordnade Kubernetes-noderna kan också mer flexibel skalning av huvudnoden. 

### <a name="security--enterprise-readiness"></a>Säkerhets- och enterprise-beredskap

När etcd data lagras i Azure Cosmos DB, Kubernetes-utvecklare får automatiskt den [inbyggd kryptering i vila](database-encryption-at-rest.md), [certifieringar och regelefterlevnad](compliance.md), och [säkerhetskopiering och återställning funktioner](online-backup-and-restore.md) stöds av Azure Cosmos DB. 

## <a name="next-steps"></a>Nästa steg

* [Hur du använder Azure Kubernetes med Azure Cosmos DB](bootstrap-kubernetes-cluster.md)
* [Viktiga fördelar med Azure Cosmos DB](introduction.md)
* [Snabbstartsguide för AKS-motorn](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md)