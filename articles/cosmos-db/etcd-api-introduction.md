---
title: 'Introduktion till API: et för Azure Cosmos DB etcd'
description: Den här artikeln innehåller en översikt och viktiga fördelar med etcd API i Azure Cosmos DB
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2020
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: 208f0d73b4c8bb11adc9c73796ebc6a622f5ed50
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "85118176"
---
# <a name="introduction-to-the-azure-cosmos-db-etcd-api-preview"></a>Introduktion till Azure Cosmos DB etcd-API (för hands version)

Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller för verksamhetskritiska program. Den erbjuder nyckel färdig global distribution, elastisk skalning av data flöde och lagring, ensiffriga millisekunder i svars tider vid 99 percentil och garanterad hög tillgänglighet, allt backas upp av branschledande service avtal.

[Etcd](https://github.com/etcd-io/etcd) är ett distribuerat nyckel/värde-lager. I [Kubernetes](https://kubernetes.io/)används etcd för att lagra status och konfigurationen för Kubernetes-klustren. Att säkerställa tillgänglighet, tillförlitlighet och prestanda hos etcd är avgörande för den övergripande kluster hälsa, skalbarhet, elastisk tillgänglighet och prestanda för ett Kubernetes-kluster.

Med etcd-API: et i Azure Cosmos DB kan du använda Azure Cosmos DB som server dels Arkiv för Azure Kubernetes. etcd-API i Azure Cosmos DB är för närvarande en för hands version. Azure Cosmos DB implementerar etcd-Wire-protokollet. Med [etcd-API](high-availability.md): n i Azure Cosmos DB får utvecklare automatiskt en mycket tillförlitlig, [globalt distribuerad](distribute-data-globally.md) Kubernetes. Med det här API: et kan utvecklare skala Kubernetes State Management i en fullständigt hanterad PaaS-tjänst. 

> [!NOTE]
> Till skillnad från andra API: er i Azure Cosmos DB kan du inte etablera ett etcd-API-konto via Azure Portal, CLI eller SDK: er. Du kan etablera ett etcd-API-konto genom att bara distribuera Resource Manager-mallen. detaljerade anvisningar finns i [så här etablerar du Azure-Kubernetes med Azure Cosmos DB](bootstrap-kubernetes-cluster.md) artikel. Azure Cosmos DB etcd-API: n är i begränsad för hands version. Du kan [Registrera dig för förhands granskningen](https://aka.ms/cosmosetcdapi-signup)genom att fylla i registrerings formuläret.

## <a name="wire-level-compatibility"></a>Kompatibilitet på högsta nivå

Azure Cosmos DB implementerar Wire-Protocol i etcd version 3 och tillåter [huvudnodens](https://kubernetes.io/docs/concepts/overview/components/) API-servrar att använda Azure Cosmos DB precis som det skulle göra i en lokalt installerad etcd-miljö. Etcd-API: et stöder ömsesidig TLS-autentisering. 

I följande diagram visas komponenterna i ett Kubernetes-kluster. I kluster hanteraren använder API-servern Azure Cosmos DB etcd-API, i stället för lokalt installerade etcd. 

:::image type="content" source="./media/etcd-api-introduction/etcd-api-wire-protocol.png" alt-text="Azure Cosmos DB implementera etcd-kabel-protokollet" border="false":::

## <a name="key-benefits"></a>Viktiga fördelar

### <a name="no-etcd-operations-management"></a>Ingen hantering av etcd-åtgärder

Som en helt hanterad intern moln tjänst Azure Cosmos DB eliminerar behovet av Kubernetes-utvecklare att konfigurera och hantera etcd. Etcd-API: et i Azure Cosmos DB är skalbart, hög tillgängligt, feltolerant och ger hög prestanda. Omkostnader för att konfigurera replikering över flera noder, utföra löpande uppdateringar, säkerhets uppdateringar och övervakning av etcd-hälsa hanteras av Azure Cosmos DB.

### <a name="global-distribution--high-availability"></a>Global distribution & hög tillgänglighet 

Med hjälp av etcd-API: et Azure Cosmos DB garanterar 99,99% tillgänglighet för data läsningar och skrivningar i en enda region och 99,999% tillgänglighet över flera regioner. 

### <a name="elastic-scalability"></a>Elastisk skalbarhet

Azure Cosmos DB erbjuder elastisk skalbarhet för Läs-och skriv förfrågningar i olika regioner.
När Kubernetes-klustret växer skalar etcd API-kontot i Azure Cosmos DB elastiskt skala utan drift avbrott. Genom att lagra etcd-data i Azure Cosmos DB, i stället för Kubernetes-huvudnoderna, aktive ras även mer flexibel skalning av huvudnoder. 

### <a name="security--enterprise-readiness"></a>Säkerhet & Enterprise readiness

När etcd-data lagras i Azure Cosmos DB får Kubernetes-utvecklare automatiskt den [inbyggda krypteringen i vila](database-encryption-at-rest.md),  [certifieringar och efterlevnad](compliance.md), samt [säkerhets kopierings-och återställnings funktioner](online-backup-and-restore.md) som stöds av Azure Cosmos dB. 

## <a name="next-steps"></a>Nästa steg

* [Så här använder du Azure Kubernetes med Azure Cosmos DB](bootstrap-kubernetes-cluster.md)
* [Viktiga fördelar med Azure Cosmos DB](introduction.md)
* [Snabb starts guide för AKS-motor](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md)