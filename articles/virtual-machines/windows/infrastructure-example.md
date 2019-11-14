---
title: Exempel genom gång av Azure-infrastruktur
description: Lär dig mer om rikt linjerna för nyckel design och implementering för att distribuera en exempel infrastruktur i Azure.
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
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74038569"
---
# <a name="example-azure-infrastructure-walkthrough-for-windows-vms"></a>Exempel genom gång av Azure-infrastruktur för virtuella Windows-datorer
Den här artikeln vägleder dig genom att skapa en exempel program infrastruktur. Vi beskriver hur du utformar en infrastruktur för en enkel onlinebutik som sammanfattar alla rikt linjer och beslut kring namngivnings konventioner, tillgänglighets uppsättningar, virtuella nätverk och belastningsutjämnare och distribuerar sedan dina virtuella datorer (VM).

## <a name="example-workload"></a>Exempel arbets belastning
Adventure Works-cykler vill skapa ett program för onlinebutik i Azure som består av:

* Två IIS-servrar som kör klient klient delen på en webb nivå
* Två IIS-servrar bearbetar data och order i en program nivå
* Två Microsoft SQL Server instanser med AlwaysOn-tillgänglighetsgrupper (två SQL-servrar och ett majoritets nods vittne) för att lagra produkt data och order i en databas nivå
* Två Active Directory domänkontrollanter för kund konton och leverantörer i en autentiseringsnivå
* Alla servrar finns i två undernät:
  * ett klient dels under nät för webb servrarna 
  * ett Server dels undernät för program servrar, SQL-kluster och domänkontrollanter

![Diagram över olika nivåer för program infrastrukturen](./media/infrastructure-example/example-tiers.png)

Inkommande säker webb trafik måste vara belastningsutjämnad bland webb servrarna när kunder bläddrar i onlinebutiken. Bearbetnings trafiken i form av HTTP-begäranden från webb servrarna måste balanseras mellan program servrarna. Dessutom måste infrastrukturen vara utformad för hög tillgänglighet.

Den resulterande designen måste innehålla:

* En Azure-prenumeration och ett konto
* En enda resurs grupp
* Azure Managed Disks
* Ett virtuellt nätverk med två undernät
* Tillgänglighets uppsättningar för de virtuella datorerna med en liknande roll
* Virtuella datorer

Ovanstående följer dessa namn konventioner:

* Adventure Works-cykler använder **[IT-arbetsbelastning]-[plats]-[Azure Resource]** som prefix
  * I det här exemplet är "**azos**" (Azure Online Store) namnet på den aktuella arbets belastningen och "**Använd**" (östra USA 2) är platsen
* Virtuella nätverk använder AZOS-USE-VN **[Number]**
* Tillgänglighets uppsättningar använder azos-Use-as- **[Role]**
* Namn på virtuella datorer Använd azos-use-VM- **[VMName]**

## <a name="azure-subscriptions-and-accounts"></a>Azure-prenumerationer och-konton
Adventure Works-cykler använder sin företags prenumeration, som heter Adventure Works Enterprise-prenumeration, för att tillhandahålla fakturering för IT-arbetsbelastningen.

## <a name="storage"></a>Storage
Adventure Works-cykler fastställde att de bör använda Azure Managed Disks. När du skapar virtuella datorer används båda tillgängliga lagrings nivåer:

* **Standard lagring** för webb servrar, program servrar och domänkontrollanter och deras data diskar.
* **Premium Storage** för SQL Server virtuella datorer och deras data diskar.

## <a name="virtual-network-and-subnets"></a>Virtuellt nätverk och undernät
Eftersom det virtuella nätverket inte behöver en pågående anslutning till Adventure Worker-nätverket, har de beslutat om ett virtuellt nätverk i molnet.

De skapade ett virtuellt nätverk med endast ett moln med följande inställningar med hjälp av Azure Portal:

* Namn: AZOS-USE-VN01
* Plats: USA, östra 2
* Adress utrymme för virtuellt nätverk: 10.0.0.0/8
* Första undernät:
  * Namn: FrontEnd
  * Adress utrymme: 10.0.1.0/24
* Andra undernät:
  * Namn: Server del
  * Adress utrymme: 10.0.2.0/24

## <a name="availability-sets"></a>Tillgänglighetsuppsättningar
För att upprätthålla hög tillgänglighet för alla fyra nivåerna i sin onlinebutik, har Adventure Works cykler som beslut ATS på fyra tillgänglighets uppsättningar:

* **azos – Använd som webb** för webb servrar
* **azos – Använd som app** för program servrarna
* **azos – Använd som SQL** för SQL-servrar
* **azos – Använd som domänkontrollant** för domän kontrol Lanterna

## <a name="virtual-machines"></a>Virtuella datorer
Adventure Works-cykler beslutade följande namn för sina virtuella Azure-datorer:

* **azos-use-VM-web01** för den första webb servern
* **azos-use-VM-web02** för den andra webb servern
* **azos-use-VM-app01** för den första program servern
* **azos-use-VM-app02** för den andra program servern
* **azos-use-VM-sql01** för den första SQL Server servern i klustret
* **azos-use-VM-sql02** för den andra SQL Server servern i klustret
* **azos-use-VM-dc01** för den första domänkontrollanten
* **azos-use-VM-dc02** för den andra domänkontrollanten

Här är den resulterande konfigurationen.

![Slutgiltig program infrastruktur distribuerad i Azure](./media/infrastructure-example/example-config.png)

Den här konfigurationen omfattar:

* Ett virtuellt nätverk i molnet med två undernät (FrontEnd och BackEnd)
* Azure Managed Disks med både standard-och Premium diskar
* Fyra tillgänglighets uppsättningar, en för varje nivå i onlinebutiken
* De virtuella datorerna för de fyra nivåerna
* En extern belastningsutjämnad uppsättning för HTTPS-baserad webb trafik från Internet till webb servrar
* En intern belastningsutjämnad uppsättning för okrypterad webb trafik från webb servrarna till program servrarna
* En enda resurs grupp
