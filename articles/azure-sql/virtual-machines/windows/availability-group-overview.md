---
title: Översikt över SQL Server Always on-tillgänglighetsgrupper
description: I den här artikeln beskrivs SQL Server Always on-tillgänglighetsgrupper på Azure Virtual Machines.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.assetid: 601eebb1-fc2c-4f5b-9c05-0e6ffd0e5334
ms.service: virtual-machines-sql
ms.topic: overview
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/07/2020
ms.author: mathoma
ms.custom: seo-lt-2019
ms.openlocfilehash: d04f689dec3a3c182c0da23007247c20c4f8063d
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94504398"
---
# <a name="always-on-availability-group-on-sql-server-on-azure-vms"></a>Always on-tillgänglighetsgrupper på SQL Server på virtuella Azure-datorer
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

I den här artikeln introduceras alltid tillgänglighets grupper för SQL Server på Azure Virtual Machines (VM). 

## <a name="overview"></a>Översikt

Always on-tillgänglighetsgrupper på Azure Virtual Machines liknar [Always on-tillgänglighets grupper lokalt](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). Eftersom de virtuella datorerna finns i Azure finns det dock några ytterligare överväganden, till exempel VM-redundans och routning av trafik i Azure-nätverket. 

Följande diagram illustrerar en tillgänglighets grupp för SQL Server på virtuella Azure-datorer:

![Tillgänglighetsgrupp](./media/availability-group-overview/00-EndstateSampleNoELB.png)


## <a name="vm-redundancy"></a>VM-redundans 

För att öka redundansen och hög tillgänglighet ska de virtuella SQL Server datorerna antingen finnas i samma [tillgänglighets uppsättning](../../../virtual-machines/windows/tutorial-availability-sets.md#availability-set-overview)eller olika [tillgänglighets zoner](../../../availability-zones/az-overview.md).

En tillgänglighets uppsättning är en gruppering av resurser som är konfigurerade så att det inte finns två land i samma tillgänglighets zon. Detta förhindrar att flera resurser i gruppen påverkas under distributions upprullningar. 


## <a name="connectivity"></a>Anslutningsmöjlighet 

I en traditionell lokal distribution ansluter klienter till tillgänglighets gruppens lyssnare med hjälp av det virtuella nätverks namnet (VNN) och lyssnaren dirigerar trafik till lämplig SQL Server replik i tillgänglighets gruppen. Det finns dock ett extra krav för att dirigera trafik i Azure-nätverket. 

Med SQL Server på virtuella Azure-datorer konfigurerar du en [belastningsutjämnare](availability-group-vnn-azure-load-balancer-configure.md) för att dirigera trafik till din tillgänglighets grupps lyssnare eller, om du är på SQL Server 2019 CU8 och senare, kan du konfigurera en [DNN-lyssnare (Distributed Network Name)](availability-group-distributed-network-name-dnn-listener-configure.md) för att ersätta den traditionella VNN tillgänglighets gruppens lyssnare. 


### <a name="vnn-listener"></a>VNN-lyssnare 

Använd en [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) för att dirigera trafik från klienten till den traditionella tillgänglighets gruppens virtuella nätverks namn (VNN) lyssnare i Azure-nätverket. 

Belastningsutjämnaren innehåller IP-adresserna för VNN-lyssnaren. Om du har fler än en tillgänglighets grupp kräver varje grupp en VNN-lyssnare. En belastningsutjämnare har stöd för flera lyssnare.

Information om hur du kommer igång finns i [Konfigurera en belastningsutjämnare](availability-group-vnn-azure-load-balancer-configure.md). 

### <a name="dnn-listener"></a>DNN-lyssnare

SQL Server 2019 CU8 introducerar stöd för DNN-lyssnaren (Distributed Network Name). DNN-lyssnaren ersätter den traditionella tillgänglighets gruppens lyssnare, vilket avstår från behovet av att en Azure-belastningsutjämnare ska dirigera trafik i Azure-nätverket. 

DNN-lyssnaren är den rekommenderade HADR anslutnings lösningen i Azure eftersom den fören klar distributionen, minskar underhållet och kostnaden och minskar redundansväxlingen om det skulle uppstå ett fel. 

Använd DNN-lyssnaren för att ersätta en befintlig VNN-lyssnare, eller Använd den tillsammans med en befintlig VNN-lyssnare, så att tillgänglighets gruppen har två distinkta anslutnings punkter – en med VNN lyssnar namn (och port om de inte är standard) och en med hjälp av DNN lyssnar namn och port. Detta kan vara användbart för kunder som vill undvika svars tids fördröjning för belastningsutjämnare men ändå dra nytta av SQL Server funktioner som är beroende av VNN-lyssnaren, till exempel distribuerade tillgänglighets grupper, Service Broker eller FILESTREAM. Läs mer i DNN- [lyssnare och SQL Server funktions samverkan](availability-group-dnn-interoperability.md)

Information om hur du kommer igång finns i [Konfigurera en DNN-lyssnare](availability-group-distributed-network-name-dnn-listener-configure.md).


## <a name="deployment"></a>Distribution 

Det finns flera alternativ för att distribuera en tillgänglighets grupp till SQL Server på virtuella Azure-datorer, med mer automatisering än andra. 

Följande tabell innehåller en jämförelse av tillgängliga alternativ:

| | Azure Portal | Azure CLI/PowerShell | Snabb starts mallar | Manuell |
|---------|---------|---------|---------|---------|
|**SQL Server-version** |2016 + |2016 +|2016 +|2012 +|
|**SQL Server-utgåva** |Enterprise |Enterprise |Enterprise |Enterprise, standard|
|**Windows Server-version**| 2016 + | 2016 + | 2016 + | Alla|
|**Skapar klustret åt dig**|Ja|Ja | Ja |Inga|
|**Skapar tillgänglighets gruppen åt dig** |Ja |Inga|Inga|Inga|
|**Skapar lyssnare och belastningsutjämnare separat** |Inga|Inga|Inga|Ja|
|**Möjligt att skapa DNN-lyssnare med den här metoden?**|Inga|Inga|Inga|Ja|
|**Konfiguration av WSFC-kvorum**|Molnvittne|Molnvittne|Molnvittne|Alla|
|**DR med flera regioner** |Inga|Inga|Inga|Ja|
|**Stöd för multinätet** |Ja|Ja|Ja|Ja|
|**Stöd för en befintlig AD**|Ja|Ja|Ja|Ja|
|**DR med flera zoner i samma region**|Ja|Ja|Ja|Ja|
|**Distribuerad AG utan AD**|Inga|Inga|Inga|Ja|
|**Distribuerad AG utan kluster** |Inga|Inga|Inga|Ja|

Mer information finns i [Azure Portal](availability-group-azure-portal-configure.md), [Azure CLI/PowerShell](./availability-group-az-commandline-configure.md), [snabb starts mallar](availability-group-quickstart-template-configure.md)och [manuell](availability-group-manually-configure-prerequisites-tutorial.md).

## <a name="considerations"></a>Överväganden 

Om du har ett redundanskluster på en virtuell Azure IaaS-gästdator, rekommenderar vi ett enda nätverkskort per server (klusternod) och ett enda undernät. Azure-nätverk har fysisk redundans, vilket gör att ytterligare nätverkskort och undernät inte behövs på ett gäst kluster för en Azure IaaS-dator. Även om klustrets verifieringsrapport utfärdar en varning om att noderna endast kan nås i ett enda nätverk, kan varningen ignoreras för redundanskluster på virtuella Azure IaaS-gästdatorer. 

## <a name="next-steps"></a>Nästa steg

Granska [metod tipsen för hadr](hadr-cluster-best-practices.md) och kom igång med att distribuera tillgänglighets gruppen med hjälp av [Azure Portal](availability-group-azure-portal-configure.md), [Azure CLI/PowerShell](./availability-group-az-commandline-configure.md), [snabb starts mallar](availability-group-quickstart-template-configure.md) eller [manuellt](availability-group-manually-configure-prerequisites-tutorial.md).

Alternativt kan du distribuera en [klustrad tillgänglighets grupp](availability-group-clusterless-workgroup-configure.md) eller en tillgänglighets grupp i [flera regioner](availability-group-manually-configure-multiple-regions.md).