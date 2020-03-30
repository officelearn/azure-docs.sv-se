---
title: Exempel på azure-infrastrukturgenomgång
description: Lär dig mer om de viktigaste riktlinjerna för design och implementering för distribution av en exempelinfrastruktur i Azure.
documentationcenter: ''
services: virtual-machines-windows
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 7032b586-e4e5-4954-952f-fdfc03fc1980
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 12/15/2017
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ab6f304d78357e261c68ebbcfcb3746844edce8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74038569"
---
# <a name="example-azure-infrastructure-walkthrough-for-windows-vms"></a>Exempel på Azure-infrastrukturgenomgång för virtuella Windows-datorer
Den här artikeln går igenom att bygga ut ett exempel på programinfrastruktur. Vi beskriver utformningen av en infrastruktur för en enkel onlinebutik som samlar alla riktlinjer och beslut kring namngivningskonventioner, tillgänglighetsuppsättningar, virtuella nätverk och belastningsutjämnare och faktiskt distribuerar dina virtuella datorer .

## <a name="example-workload"></a>Exempel på arbetsbelastning
Adventure Works Cycles vill bygga ett onlinebutiksprogram i Azure som består av:

* Två IIS-servrar som kör klientens klientklient på en webbnivå
* Två IIS-servrar som bearbetar data och order på en programnivå
* Två Microsoft SQL Server-instanser med AlwaysOn-tillgänglighetsgrupper (två SQL-servrar och ett majoritetsnördvitt) för lagring av produktdata och order på en databasnivå
* Två Active Directory-domänkontrollanter för kundkonton och leverantörer på en autentiseringsnivå
* Alla servrar finns i två undernät:
  * ett frontend-undernät för webbservrarna 
  * ett backend-undernät för programservrar, SQL-kluster och domänkontrollanter

![Diagram över olika nivåer för programinfrastruktur](./media/infrastructure-example/example-tiers.png)

Inkommande säker webbtrafik måste vara belastningsbalanserad mellan webbservrarna när kunderna surfar i onlinebutiken. Orderbearbetningstrafik i form av HTTP-begäranden från webbservrarna måste balanseras mellan programservrarna. Dessutom måste infrastrukturen utformas för hög tillgänglighet.

Den resulterande konstruktionen skall innehålla följande:

* En Azure-prenumeration och ett konto
* En enskild resursgrupp
* Azure Managed Disks
* Ett virtuellt nätverk med två undernät
* Tillgänglighetsuppsättningar för de virtuella datorerna med en liknande roll
* Virtuella datorer

Alla ovanstående följer dessa namngivningskonventioner:

* Adventure Works Cycles använder **[IT-arbetsbelastning]-[plats]-[Azure-resurs]** som prefix
  * I det här exemplet är "**azos**" (Azure Online Store) IT-arbetsbelastningsnamnet och "**använd**" (Östra USA 2) är platsen
* Virtuella nätverk använder AZOS-USE-VN **[nummer]**
* Tillgänglighetsuppsättningar använder azos-use-as-**[roll]**
* Namn på virtuella datorer använder azos-use-vm-**[vmname]**

## <a name="azure-subscriptions-and-accounts"></a>Azure-prenumerationer och -konton
Adventure Works Cycles använder sin Enterprise-prenumeration, adventure works enterprise-prenumeration, för att tillhandahålla fakturering för den här IT-arbetsbelastningen.

## <a name="storage"></a>Lagring
Adventure Works Cykler fastställt att de ska använda Azure Managed Disks. När du skapar virtuella datorer används båda tillgängliga lagringsnivåer:

* **Standardlagring** för webbservrar, programservrar och domänkontrollanter och deras datadiskar.
* **Premiumlagring** för virtuella SQL Server-datorer och deras datadiskar.

## <a name="virtual-network-and-subnets"></a>Virtuellt nätverk och undernät
Eftersom det virtuella nätverket inte behöver kontinuerlig anslutning till Adventure Work Cycles lokalt nätverk, bestämde de sig för ett virtuellt nätverk endast för molnet.

De skapade ett virtuellt nätverk endast för molnet med följande inställningar med Hjälp av Azure-portalen:

* Namn: AZOS-USE-VN01
* Plats: Östra USA 2
* Virtuellt nätverksadressutrymme: 10.0.0.0/8
* Första undernätet:
  * Namn: FrontEnd
  * Adressutrymme: 10.0.1.0/24
* Andra undernätet:
  * Namn: BackEnd
  * Adressutrymme: 10.0.2.0/24

## <a name="availability-sets"></a>Tillgänglighetsuppsättningar
För att upprätthålla hög tillgänglighet för alla fyra nivåer i sin webbutik, Adventure Works Cycles beslutat om fyra tillgänglighet uppsättningar:

* **azos-use-as-web** för webbservrarna
* **azos-use-as-app** för programservrarna
* **azos-use-as-sql för SQL-servrar**
* **azos-use-as-dc** för domänkontrollanterna

## <a name="virtual-machines"></a>Virtuella datorer
Adventure Works Cycles har beslutat om följande namn för sina virtuella Azure-datorer:

* **azos-use-vm-web01** för den första webbservern
* **azos-use-vm-web02** för den andra webbservern
* **azos-use-vm-app01** för den första programservern
* **azos-use-vm-app02** för den andra programservern
* **azos-use-vm-sql01** för den första SQL Server-servern i klustret
* **azos-use-vm-sql02** för den andra SQL Server-servern i klustret
* **azos-use-vm-dc01** för den första domänkontrollanten
* **azos-use-vm-dc02** för den andra domänkontrollanten

Här är den resulterande konfigurationen.

![Slutlig programinfrastruktur som distribueras i Azure](./media/infrastructure-example/example-config.png)

Den här konfigurationen innehåller:

* Ett virtuellt nätverk endast för molnet med två undernät (FrontEnd och BackEnd)
* Azure Managed Disks med både Standard- och Premium-diskar
* Fyra tillgänglighetsuppsättningar, en för varje nivå i onlinebutiken
* De virtuella datorerna för de fyra nivåerna
* En extern belastningsbalanserad uppsättning för HTTPS-baserad webbtrafik från Internet till webbservrarna
* En intern belastningsbalanserad uppsättning för okrypterad webbtrafik från webbservrarna till programservrarna
* En enskild resursgrupp
