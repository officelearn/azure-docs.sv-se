---
title: Exempel-guide för Azure-infrastrukturen | Microsoft Docs
description: Läs mer om viktiga riktlinjer för utformning och implementering för distribution av en exempel-infrastruktur i Azure.
documentationcenter: ''
services: virtual-machines-windows
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 7032b586-e4e5-4954-952f-fdfc03fc1980
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ddbaed6704fd32f7fd4fe5a790424cbf829d2f1c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60540410"
---
# <a name="example-azure-infrastructure-walkthrough-for-windows-vms"></a>Exempel-guide för Windows-datorer på Azure-infrastrukturen
Den här artikeln beskriver att bygga ut en exempel-infrastruktur för programmet. Vi förklarar vi utformar en infrastruktur för en enkel onlinebutik som sammanför alla riktlinjer och beslut om namngivningskonventioner, tillgänglighetsuppsättningar, virtuella nätverk och belastningsutjämnare och faktiskt distribuerar dina virtuella datorer (VM).

## <a name="example-workload"></a>Exempel-arbetsbelastning
Adventure Works Cycles vill bygga ett onlinebutik program i Azure som består av:

* Två IIS-servrar som kör klienten klientdelen i en webbnivå
* Två IIS-servrar som bearbetar data och order i en programnivå
* Två Microsoft SQL Server-instanser med AlwaysOn-Tillgänglighetsgrupper (två SQL-servrar och en majoritet noden vittne) för att lagra produktdata och order i en databasnivå
* Två Active Directory-domänkontrollanter för kunder och leverantörer i en nivå av autentisering
* Alla servrar finns i två undernät:
  * ett klientdelsundernät för webbservrar 
  * ett backend-undernät för programservrar, SQL-kluster och domänkontrollanter

![Diagram över olika nivåer för infrastruktur](./media/infrastructure-example/example-tiers.png)

Säker inkommande webbtrafik måste vara belastningsutjämnade webbservrarna när kunder bläddrar onlinebutiken. Ordna bearbetning trafik i form av HTTP-begäranden från webben servrar måste balanseras mellan programservrarna. Dessutom måste infrastrukturen utformas för hög tillgänglighet.

Den resulterande designen måste innehålla:

* Ett konto och Azure-prenumeration
* En enskild resursgrupp
* Azure Managed Disks
* Ett virtuellt nätverk med två undernät
* Tillgänglighetsuppsättningar för virtuella datorer med en liknande roll
* Virtuella datorer

Alla ovanstående följer du dessa namngivningsregler:

* Adventure Works Cycles använder **[IT arbetsbelastning]-[plats]-[Azure-resurs]** som ett prefix
  * I det här exemplet ”**azos**” (Azure Online Store) är namnet för IT-arbetsbelastning och ”**använder**” (östra USA 2) är platsen
* Virtuella nätverk använder AZOS-användning – VN **[antal]**
* Tillgänglighetsuppsättningar använder azos-Använd-som-**[roll]**
* Namn på virtuella datorer använda azos-Använd-vm -**[vmname]**

## <a name="azure-subscriptions-and-accounts"></a>Azure-prenumerationer och konton
Adventure Works Cycles använder sin Enterprise-prenumeration med namnet Adventure Works Enterprise-prenumeration för att tillhandahålla faktureringen för den här arbetsbelastningen för IT.

## <a name="storage"></a>Storage
Adventure Works Cycles fastställt att de ska använda Azure Managed Disks. När du skapar virtuella datorer används både tillgängliga nivåer:

* **Standardlagring** för webbservrar, programservrar, och domänkontrollanter och deras datadiskar.
* **Premiumlagring** för SQL Server-datorer och deras datadiskar.

## <a name="virtual-network-and-subnets"></a>Virtuellt nätverk och undernät
Eftersom det virtuella nätverket inte behöver pågående anslutning till det lokala nätverket för Adventure Work Cycles, bestämt de dig för ett endast molnbaserat virtuellt nätverk.

De har skapat ett endast molnbaserat virtuellt nätverk med följande inställningar med hjälp av Azure portal:

* Namn: AZOS-USE-VN01
* Plats: USA, östra 2
* Virtuella nätverkets adressutrymme: 10.0.0.0/8
* Första undernät:
  * Namn: FrontEnd
  * Adressutrymme: 10.0.1.0/24
* Andra undernät:
  * Namn: BackEnd
  * Adressutrymme: 10.0.2.0/24

## <a name="availability-sets"></a>Tillgänglighetsuppsättningar
Om du vill upprätthålla hög tillgänglighet för alla fyra nivåer av deras onlinebutik valt Adventure Works Cycles fyra tillgänglighetsuppsättningar:

* **azos användning som web** för webbservrar
* **azos använda som appar** för programservrarna
* **azos Använd som sql** för SQL-servrar
* **azos användning som dc** för domänkontrollanter

## <a name="virtual-machines"></a>Virtuella datorer
Adventure Works Cycles valt följande namn för sina virtuella Azure-datorer:

* **azos-användning – vm-web01** för den första webbservern
* **azos-användning – vm-web02** för den andra webbservern
* **azos-användning – vm-app01** för den första programservern
* **azos-användning – vm-app02** för den andra programservern
* **azos-användning – vm-sql01** för den första servern i SQL Server i klustret
* **azos-användning – vm-sql02** för den andra servern för SQL Server i klustret
* **azos-användning – vm-dc01** för den första domänkontrollanten
* **azos-användning – vm-dc02** för den andra domänkontrollanten

Här är den resulterande konfigurationen.

![Sista programinfrastruktur som distribueras i Azure](./media/infrastructure-example/example-config.png)

Den här konfigurationen omfattar:

* Ett endast molnbaserat virtuellt nätverk med två undernät (FrontEnd och BackEnd)
* Azure Managed Disks med Standard och Premium-diskar
* Fyra tillgänglighetsuppsättningar en för varje nivå av onlinebutiken
* De virtuella datorerna för fyra nivåer
* En extern belastningsutjämnad uppsättning för HTTPS-baserade Internet-trafik från Internet till webbservrar
* Ett internt belastningsutjämnade uppsättningen för okrypterade webbtrafik från webbservrarna till programservrarna
* En enskild resursgrupp
