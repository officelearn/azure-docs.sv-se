---
title: Exempel-guide för Azure-infrastrukturen | Microsoft Docs
description: Läs mer om viktiga riktlinjer för utformning och implementering för distribution av en exempel-infrastruktur i Azure.
documentationcenter: ''
services: virtual-machines-linux
author: zr-msft
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 281fc2c0-b533-45fa-81a3-728c0049c73d
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: zarhoads
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 794182e3988a353b1e305a36da0475bacdea69b8
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2018
ms.locfileid: "49469858"
---
# <a name="example-azure-infrastructure-walkthrough-for-linux-vms"></a>Exempel-guide för virtuella Linux-datorer på Azure-infrastrukturen
Den här artikeln beskriver att bygga ut en exempel-infrastruktur för programmet. Vi förklarar vi utformar en infrastruktur för en enkel onlinebutik som sammanför alla riktlinjer och beslut om namngivningskonventioner, tillgänglighetsuppsättningar, virtuella nätverk och belastningsutjämnare och faktiskt distribuerar dina virtuella datorer (VM).

## <a name="example-workload"></a>Exempel-arbetsbelastning
Adventure Works Cycles vill skapa en App i online-butik i Azure som består av:

* Två nginx-servrar som kör klienten klientdelen i en webbnivå
* Två nginx-servrar som bearbetar data och order i en programnivå
* Två MongoDB-servrar ingår i ett delat kluster för att lagra produktdata och order i en databasnivå
* Två Active Directory-domänkontrollanter för kunder och leverantörer i en nivå av autentisering
* Alla servrar finns i två undernät:
  * ett klientdelsundernät för webbservrar 
  * ett backend-undernät för programservrar, MongoDB-kluster och domänkontrollanter

![Diagram över olika nivåer för infrastruktur](./media/infrastructure-example/example-tiers.png)

Säker inkommande webbtrafik måste vara belastningsutjämnade webbservrarna när kunder bläddrar online-butik. Orderhantering trafik i form av HTTP-begäranden från webben servrar måste vara Utjämning av nätverksbelastning på programservrarna. Dessutom måste infrastrukturen utformas för hög tillgänglighet.

Den resulterande designen måste innehålla:

* Ett konto och Azure-prenumeration
* En enskild resursgrupp
* Azure Managed Disks
* Ett virtuellt nätverk med två undernät
* Tillgänglighetsuppsättningar för virtuella datorer med en liknande roll
* Virtuella datorer

Alla ovanstående följer du dessa namngivningsregler:

* Adventure Works Cycles använder **[IT arbetsbelastning]-[plats]-[Azure-resurs]** som ett prefix
  * I det här exemplet ”**azos**” (Azure online Store) är namnet för IT-arbetsbelastning och ”**använder**” (östra USA 2) är platsen
* Virtuella nätverk använder AZOS-användning – VN **[antal]**
* Tillgänglighetsuppsättningar använder azos-Använd-som-**[roll]**
* Namn på virtuella datorer använda azos-Använd-vm -**[vmname]**

## <a name="azure-subscriptions-and-accounts"></a>Azure-prenumerationer och konton
Adventure Works Cycles använder sin Enterprise-prenumeration med namnet Adventure Works Enterprise-prenumeration för att tillhandahålla faktureringen för den här arbetsbelastningen för IT.

## <a name="storage"></a>Storage
Adventure Works Cycles fastställt att de ska använda Azure Managed Disks. När du skapar virtuella datorer används för båda lagringsnivåerna i tillgängligt lagringsutrymme:

* **Standardlagring** för webbservrar, programservrar, och domänkontrollanter och deras datadiskar.
* **Premiumlagring** för MongoDB shardade klusterservrarna och deras datadiskar.

## <a name="virtual-network-and-subnets"></a>Virtuellt nätverk och undernät
Eftersom det virtuella nätverket inte behöver pågående anslutning till det lokala nätverket för Adventure Work Cycles, bestämt de dig för ett endast molnbaserat virtuellt nätverk.

De har skapat ett endast molnbaserat virtuellt nätverk med följande inställningar med hjälp av Azure portal:

* Namn: AZOS-användning – VN01
* Plats: Östra USA 2
* Virtuella nätverkets adressutrymme: 10.0.0.0/8
* Första undernät:
  * Namn: FrontEnd
  * Adressutrymme: 10.0.1.0/24
* Andra undernät:
  * Namn: BackEnd
  * Adressutrymme: 10.0.2.0/24

## <a name="availability-sets"></a>Tillgänglighetsuppsättningar
Om du vill upprätthålla hög tillgänglighet för alla fyra nivåer av deras online-butik valt Adventure Works Cycles fyra tillgänglighetsuppsättningar:

* **azos användning som web** för webbservrar
* **azos använda som appar** för programservrarna
* **azos Använd som db** för servrar i det fragmenterade (sharded) MongoDB-klustret
* **azos användning som dc** för domänkontrollanter

## <a name="virtual-machines"></a>Virtuella datorer
Adventure Works Cycles valt följande namn för sina virtuella Azure-datorer:

* **azos-användning – vm-web01** för den första webbservern
* **azos-användning – vm-web02** för den andra webbservern
* **azos-användning – vm-app01** för den första programservern
* **azos-användning – vm-app02** för den andra programservern
* **azos-användning – vm-db01** för den första MongoDB-servern i klustret
* **azos-användning – vm-db02** för den andra MongoDB-servern i klustret
* **azos-användning – vm-dc01** för den första domänkontrollanten
* **azos-användning – vm-dc02** för den andra domänkontrollanten

Här är den resulterande konfigurationen.

![Sista programinfrastruktur som distribueras i Azure](./media/infrastructure-example/example-config.png)

Den här konfigurationen omfattar:

* Ett endast molnbaserat virtuellt nätverk med två undernät (FrontEnd och BackEnd)
* Azure Managed Disks använder både Standard och Premium-diskar
* Fyra tillgänglighetsuppsättningar en för varje nivå av online-butik
* De virtuella datorerna för fyra nivåer
* En extern belastningsutjämnad uppsättning för HTTPS-baserade Internet-trafik från Internet till webbservrar
* Ett internt belastningsutjämnade uppsättningen för okrypterade webbtrafik från webbservrarna till programservrarna
* En enskild resursgrupp
