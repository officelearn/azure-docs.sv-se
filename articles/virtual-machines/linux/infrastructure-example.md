---
title: Exempel på genomgången av Azure-infrastruktur
description: Lär dig mer om de viktigaste riktlinjerna för design och implementering för distribution av en exempelinfrastruktur i Azure.
author: cynthn
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: 6040bf8862131f5a8a5564cd2f5d845fa0490a95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944909"
---
# <a name="example-azure-infrastructure-walkthrough-for-linux-vms"></a>Exempel på Azure-infrastrukturgenomgång för virtuella Linux-datorer
Den här artikeln går igenom att bygga ut ett exempel på programinfrastruktur. Vi beskriver utformningen av en infrastruktur för en enkel online-butik som samlar alla riktlinjer och beslut kring namngivningskonventioner, tillgänglighetsuppsättningar, virtuella nätverk och belastningsutjämnare och faktiskt distribuerar dina virtuella datorer .

## <a name="example-workload"></a>Exempel på arbetsbelastning
Adventure Works Cycles vill skapa ett online-lagringsprogram i Azure som består av:

* Två nginx-servrar som kör klientens klientklient i en webbnivå
* Två nginx-servrar som bearbetar data och order på en programnivå
* Två MongoDB-servrar som ingår i ett fragmenterat kluster för lagring av produktdata och order på en databasnivå
* Två Active Directory-domänkontrollanter för kundkonton och leverantörer på en autentiseringsnivå
* Alla servrar finns i två undernät:
  * ett frontend-undernät för webbservrarna 
  * ett backend-undernät för programservrar, MongoDB-kluster och domänkontrollanter

![Diagram över olika nivåer för programinfrastruktur](./media/infrastructure-example/example-tiers.png)

Inkommande säker webbtrafik måste vara belastningsbalanserad mellan webbservrarna när kunderna surfar i onlinebutiken. Orderbehandlingstrafik i form av HTTP-begäranden från webbservrarna måste vara belastningsbalanserad mellan programservrarna. Dessutom måste infrastrukturen utformas för hög tillgänglighet.

Den resulterande konstruktionen skall innehålla följande:

* En Azure-prenumeration och ett konto
* En enskild resursgrupp
* Azure Managed Disks
* Ett virtuellt nätverk med två undernät
* Tillgänglighetsuppsättningar för de virtuella datorerna med en liknande roll
* Virtuella datorer

Alla ovanstående följer dessa namngivningskonventioner:

* Adventure Works Cycles använder **[IT-arbetsbelastning]-[plats]-[Azure-resurs]** som prefix
  * I det här exemplet är "**azos**" (Azure On-line Store) IT-arbetsbelastningsnamnet och "**använd**" (Östra USA 2) platsen
* Virtuella nätverk använder AZOS-USE-VN<strong>[nummer]</strong>
* Tillgänglighetsuppsättningar använder azos-use-as-**[roll]**
* Namn på virtuella datorer använder azos-use-vm-**[vmname]**

## <a name="azure-subscriptions-and-accounts"></a>Azure-prenumerationer och -konton
Adventure Works Cycles använder sin Enterprise-prenumeration, adventure works enterprise-prenumeration, för att tillhandahålla fakturering för den här IT-arbetsbelastningen.

## <a name="storage"></a>Lagring
Adventure Works Cykler fastställt att de ska använda Azure Managed Disks. När du skapar virtuella datorer används båda lagrings tillgängliga lagringsnivåer:

* **Standardlagring** för webbservrar, programservrar och domänkontrollanter och deras datadiskar.
* **Premiumlagring** för MongoDB-fragmenterade klusterservrar och deras datadiskar.

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
För att upprätthålla hög tillgänglighet för alla fyra nivåer i deras online-butik, Adventure Works Cycles beslutat om fyra tillgänglighet uppsättningar:

* **azos-use-as-web** för webbservrarna
* **azos-use-as-app** för programservrarna
* **azos-use-as-db** för servrarna i mongoDb-klustrade klustret
* **azos-use-as-dc** för domänkontrollanterna

## <a name="virtual-machines"></a>Virtuella datorer
Adventure Works Cycles har beslutat om följande namn för sina virtuella Azure-datorer:

* **azos-use-vm-web01** för den första webbservern
* **azos-use-vm-web02** för den andra webbservern
* **azos-use-vm-app01** för den första programservern
* **azos-use-vm-app02** för den andra programservern
* **azos-use-vm-db01** för den första MongoDB-servern i klustret
* **azos-use-vm-db02** för den andra MongoDB-servern i klustret
* **azos-use-vm-dc01** för den första domänkontrollanten
* **azos-use-vm-dc02** för den andra domänkontrollanten

Här är den resulterande konfigurationen.

![Slutlig programinfrastruktur som distribueras i Azure](./media/infrastructure-example/example-config.png)

Den här konfigurationen innehåller:

* Ett virtuellt nätverk endast för molnet med två undernät (FrontEnd och BackEnd)
* Hanterade Azure-diskar med både Standard- och Premium-diskar
* Fyra tillgänglighetsuppsättningar, en för varje nivå i onlinebutiken
* De virtuella datorerna för de fyra nivåerna
* En extern belastningsbalanserad uppsättning för HTTPS-baserad webbtrafik från Internet till webbservrarna
* En intern belastningsbalanserad uppsättning för okrypterad webbtrafik från webbservrarna till programservrarna
* En enskild resursgrupp
