---
title: "Exemplet Azure-infrastrukturen genomgången | Microsoft Docs"
description: "Läs mer om viktiga design och implementeringslösning riktlinjer för att distribuera en exempel-infrastruktur i Azure."
documentationcenter: 
services: virtual-machines-linux
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 281fc2c0-b533-45fa-81a3-728c0049c73d
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 06/26/2017
ms.author: iainfou
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b18be0d81d6fad7328edb47c9b69af4eecd3b971
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="example-azure-infrastructure-walkthrough-for-linux-vms"></a>Exemplet Azure-infrastrukturen genomgång för virtuella Linux-datorer

[!INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)]

Den här artikeln beskriver hur bygga ut infrastruktur för ett exempel. Vi i detalj utformar en infrastruktur för en enkel onlinebutik som sammanför de riktlinjer och beslut runt namngivningskonventioner, tillgänglighetsuppsättningar, virtuella nätverk och belastningsutjämnare och faktiskt distribuerar virtuella datorer (VM).

## <a name="example-workload"></a>Exempel arbetsbelastning
Adventure Works Cycles vill skapa en onlinebutik program i Azure som består av:

* Två nginx-servrar som kör klienten klientdelen i en webbnivå
* Två nginx-servrar bearbetar data och order i en program-nivå
* Två MongoDB-servrar ingår i ett delat kluster för att lagra produktdata och order i en databasnivå
* Två Active Directory-domänkontrollanter för kundkonton och leverantörer i en nivå för autentisering
* Alla servrar finns i två undernät:
  * ett frontend undernät för webbservrar 
  * en backend-undernät för programservrar, MongoDB-kluster och domänkontrollanter

![Diagram över olika nivåer för infrastruktur](./media/infrastructure-example/example-tiers.png)

Inkommande säker webbtrafik måste vara belastningsutjämnad webbservrarna som kunder bläddra online-butik. Order som bearbetar trafik i form av HTTP-begäranden från webben servrar måste vara belastningsutjämnad på programservrarna. Dessutom måste infrastrukturen utformas för hög tillgänglighet.

Den resulterande designen innehålla:

* Ett konto och Azure-prenumeration
* En enskild resursgrupp
* Azure Managed Disks
* Ett virtuellt nätverk med två undernät
* Tillgänglighetsuppsättningar för virtuella datorer med en liknande roll
* Virtuella datorer

Alla ovanstående följer du dessa namngivningsregler:

* Adventure Works Cycles använder **[IT arbetsbelastning]-[plats]-[Azure resurs]** som ett prefix
  * I det här exemplet ”**azos**” (Azure online Store) är det arbetsbelastning namnet och ”**använder**” (östra USA 2) är platsen
* Virtuella nätverk använder AZOS-Använd-VN**[antal]**
* Tillgänglighetsuppsättningar använder azos-Använd-som-**[roll]**
* Namn på virtuella datorer använda azos-Använd-vm -**[vmname]**

## <a name="azure-subscriptions-and-accounts"></a>Azure-prenumerationer och konton
Adventure Works Cycles använder sin Enterprise-prenumeration med namnet Adventure Works Enterprise prenumeration för att förse faktureringen för den här IT-arbetsbelastning.

## <a name="storage"></a>Lagring
Adventure Works Cycles fastställt att de ska använda Azure hanterade diskar. När du skapar virtuella datorer används både tillgängligt lagringsutrymme lagringsnivåer:

* **Standardlagring** för webbservrar, programservrar och domänkontrollanter och deras datadiskar.
* **Premium-lagring** för MongoDB delat klusterservrar och deras datadiskar.

## <a name="virtual-network-and-subnets"></a>Virtuellt nätverk och undernät
Eftersom det virtuella nätverket inte behöver ha en anslutning till Adventure arbete Cycles lokalt nätverk, valt de en endast molnbaserad virtuellt nätverk.

De skapade ett virtuellt nätverk som endast molnbaserad med följande inställningar med hjälp av Azure portal:

* Namn: AZOS-Använd-VN01
* Plats: Östra USA 2
* Virtuellt nätverks-adressutrymme: 10.0.0.0/8
* Första undernätet:
  * Namn: klientdel
  * Adressutrymmet: 10.0.1.0/24
* Andra undernät:
  * Namn: BackEnd
  * Adressutrymmet: 10.0.2.0/24

## <a name="availability-sets"></a>Tillgänglighetsuppsättningar
För att upprätthålla hög tillgänglighet för alla fyra nivåer av deras onlinebutik valt Adventure Works Cycles fyra tillgänglighetsuppsättningar:

* **azos används som web** för webbservrar
* **azos används som appen** för programmet
* **azos används som db** för servrarna i MongoDB delat kluster
* **azos används som dc** för domänkontrollanter

## <a name="virtual-machines"></a>Virtuella datorer
Adventure Works Cycles valt följande namn för sina virtuella Azure-datorer:

* **azos-användning-vm-web01** för den första webbservern
* **azos-användning-vm-web02** för den andra webbservern
* **azos-användning-vm-app01** för den första programservern
* **azos-användning-vm-app02** för andra programserver
* **azos-användning-vm-db01** för den första MongoDB-servern i klustret
* **azos-användning-vm-db02** för den andra MongoDB-servern i klustret
* **azos-användning-vm-dc01** för den första domänkontrollanten
* **azos-användning-vm-dc02** för den andra domänkontrollanten

Det här är den resulterande konfigurationen.

![Sista infrastruktur för distribuerade i Azure](./media/infrastructure-example/example-config.png)

Den här konfigurationen omfattar:

* Endast molnbaserad virtuellt nätverk med två undernät (klient- och Servergränssnitten)
* Azure hanterade diskar med hjälp av Standard- och Premium-diskar
* Fyra tillgänglighetsuppsättningar, ett för varje nivå av online-butik
* De virtuella datorerna för de fyra nivåerna
* En extern belastningsutjämnad uppsättning för HTTPS-baserade webbtrafik från Internet till webbservrar
* Ett internt belastningsutjämnade uppsättningen okrypterad webbtrafik från webbservrarna till programservern
* En enskild resursgrupp
