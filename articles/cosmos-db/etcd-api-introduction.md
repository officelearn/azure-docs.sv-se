---
title: Introduktion till Azure Cosmos DB etcd API
description: Den här artikeln innehåller en översikt och viktiga fördelar med ETCD API i Azure Cosmos DB
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: acd87fac5ec2edc40d27d98f073e13c0acae8d8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79498589"
---
# <a name="introduction-to-the-azure-cosmos-db-etcd-api-preview"></a>Introduktion till Azure Cosmos DB etcd API (förhandsversion)

Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller för verksamhetskritiska program. Den erbjuder total global distribution, elastisk skalning av dataflöde och lagring, ensiffriga millisekunders latenser vid den 99:e percentilen och garanterad hög tillgänglighet, allt med stöd av branschledande SLA-system.

[Etcd](https://github.com/etcd-io/etcd) är en distribuerad nyckel/värdebutik. I [Kubernetes](https://kubernetes.io/)används etcd för att lagra tillståndet och konfigurationen av Kubernetes-kluster. Att säkerställa tillgänglighet, tillförlitlighet och prestanda för etcd är avgörande för den övergripande klusterhälsa, skalbarhet, elasticitet tillgänglighet och prestanda för en Kubernetes kluster. 

Med ETCD-API:et i Azure Cosmos DB kan du använda Azure Cosmos DB som serverningslagring för [Azure Kubernetes](../aks/index.yml). etcd API i Azure Cosmos DB är för närvarande i förhandsversion. Azure Cosmos DB implementerar etcd-trådprotokollet. Med ETCD API i Azure Cosmos DB får utvecklare automatiskt mycket tillförlitliga, [tillgängliga](high-availability.md), [globalt distribuerade](distribute-data-globally.md) Kubernetes. Med det här API:et kan utvecklare skala Kubernetes tillståndshantering på en fullständigt hanterad molnbaserad PaaS-tjänst. 

> [!NOTE]
> Till skillnad från andra API:er i Azure Cosmos DB kan du inte etablera ett API-konto med läst via Azure-portalen, CLI eller SDK:er. Du kan etablera ett API-konto med etcd genom att endast distribuera Resource Manager-mallen. Detaljerade steg finns i Så här etablerar du [Azure Kubernetes med Azure Cosmos](bootstrap-kubernetes-cluster.md) DB-artikel. Azure Cosmos DB etcd API är för närvarande i begränsad förhandsversion. Du kan [registrera dig för förhandsgranskningen](https://aka.ms/cosmosetcdapi-signup)genom att fylla i registreringsformuläret.

## <a name="wire-level-compatibility"></a>Kompatibilitet på trådnivå

Azure Cosmos DB implementerar trådprotokollet för etcd version 3 och gör det möjligt för [huvudnodens](https://kubernetes.io/docs/concepts/overview/components/) API-servrar att använda Azure Cosmos DB precis som i en lokalt installerad etcd-miljö. Etcd API stöder TLS ömsesidig autentisering. 

I följande diagram visas komponenterna i ett Kubernetes-kluster. I klusterhanteraren använder API-servern Azure Cosmos DB etcd API, i stället för lokalt installerade etcd. 

![Azure Cosmos DB implementerar etcd-trådprotokollet](./media/etcd-api-introduction/etcd-api-wire-protocol.png)

## <a name="key-benefits"></a>Viktiga fördelar

### <a name="no-etcd-operations-management"></a>Ingen etcd operations management

Som en fullständigt hanterad inbyggd molntjänst tar Azure Cosmos DB bort behovet av Kubernetes-utvecklare att konfigurera och hantera etcd. Etcd-API:et i Azure Cosmos DB är skalbart, högtillgängligt, feltolerant och erbjuder hög prestanda. Omkostnaderna för att konfigurera replikering över flera noder, utföra rullande uppdateringar, säkerhetskorrigeringar och övervakning av etcd-hälsotillstånd hanteras av Azure Cosmos DB.

### <a name="global-distribution--high-availability"></a>Global distribution & hög tillgänglighet 

Genom att använda ETCD API garanterar Azure Cosmos DB 99,99 % tillgänglighet för dataläsningar och skrivningar i en enda region och 99,999 % tillgänglighet i flera regioner. 

### <a name="elastic-scalability"></a>Elastisk skalbarhet

Azure Cosmos DB erbjuder elastisk skalbarhet för läs- och skrivbegäranden i olika regioner.
När Kubernetes-klustret växer skalas API-kontot med etcd i Azure Cosmos DB elastiskt utan avbrott. Lagring av etcd-data i Azure Cosmos DB, i stället för Kubernetes-huvudnoderna möjliggör också mer flexibel huvudnodskalning. 

### <a name="security--enterprise-readiness"></a>Säkerhet & företagets beredskap

När etcd-data lagras i Azure Cosmos DB får Kubernetes-utvecklare automatiskt den [inbyggda krypteringen i vila,](database-encryption-at-rest.md) [certifieringar och efterlevnad](compliance.md)samt [säkerhetskopierings- och återställningsfunktioner](../synapse-analytics/sql-data-warehouse/backup-and-restore.md) som stöds av Azure Cosmos DB. 

## <a name="next-steps"></a>Nästa steg

* [Så här använder du Azure Kubernetes med Azure Cosmos DB](bootstrap-kubernetes-cluster.md)
* [Viktiga fördelar med Azure Cosmos DB](introduction.md)
* [Snabbstartsguide för AKS-motorn](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md)