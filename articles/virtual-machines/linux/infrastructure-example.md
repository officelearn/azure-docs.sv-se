---
title: Exempel genom gång av Azure-infrastruktur
description: Lär dig mer om rikt linjerna för nyckel design och implementering för att distribuera en exempel infrastruktur i Azure.
author: cynthn
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: 500de3f89bd041adf0b73e21762495d8c89e19c8
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87286299"
---
# <a name="example-azure-infrastructure-walkthrough-for-linux-vms"></a>Exempel genom gång av Azure-infrastruktur för virtuella Linux-datorer
Den här artikeln vägleder dig genom att skapa en exempel program infrastruktur. Vi beskriver hur du utformar en infrastruktur för en enkel online-butik som samlar alla rikt linjer och beslut kring namngivnings konventioner, tillgänglighets uppsättningar, virtuella nätverk och belastningsutjämnare och distribuerar sedan dina virtuella datorer (VM).

## <a name="example-workload"></a>Exempel arbets belastning
Adventure Works-cykler vill skapa ett Online Store-program i Azure som består av:

* Två nginx-servrar som kör klient klient delen på en webb nivå
* Två nginx-servrar bearbetar data och order i en program nivå
* Två MongoDB-servrar som ingår i ett shardade-kluster för lagring av produkt data och order i en databas nivå
* Två Active Directory domänkontrollanter för kund konton och leverantörer i en autentiseringsnivå
* Alla servrar finns i två undernät:
  * ett klient dels under nät för webb servrarna 
  * ett Server dels undernät för program servrar, MongoDB-kluster och domänkontrollanter

![Diagram över olika nivåer för program infrastrukturen](./media/infrastructure-example/example-tiers.png)

Inkommande säker webb trafik måste vara belastningsutjämnad bland webb servrarna när kunder bläddrar i den lokala butiken. Bearbetnings trafiken i form av HTTP-förfrågningar från webb servrarna måste vara belastningsutjämnad mellan program servrarna. Dessutom måste infrastrukturen vara utformad för hög tillgänglighet.

Den resulterande designen måste innehålla:

* En Azure-prenumeration och ett konto
* En enda resurs grupp
* Azure Managed Disks
* Ett virtuellt nätverk med två undernät
* Tillgänglighets uppsättningar för de virtuella datorerna med en liknande roll
* Virtuella datorer

Ovanstående följer dessa namn konventioner:

* Adventure Works-cykler använder **[IT-arbetsbelastning]-[plats]-[Azure Resource]** som prefix
  * I det här exemplet är "**azos**" (Azure on-line Store) namnet på den aktuella arbets belastningen och "**Använd**" (USA, östra 2) är platsen
* Virtuella nätverk använder AZOS-USE-VN<strong>[Number]</strong>
* Tillgänglighets uppsättningar använder azos-Use-as-**[Role]**
* Namn på virtuella datorer Använd azos-use-VM-**[VMName]**

## <a name="azure-subscriptions-and-accounts"></a>Azure-prenumerationer och-konton
Adventure Works-cykler använder sin företags prenumeration, som heter Adventure Works Enterprise-prenumeration, för att tillhandahålla fakturering för IT-arbetsbelastningen.

## <a name="storage"></a>Storage
Adventure Works-cykler fastställde att de bör använda Azure Managed Disks. När du skapar virtuella datorer används båda lagrings nivåerna tillgängligt för lagring:

* **Standard lagring** för webb servrar, program servrar och domänkontrollanter och deras data diskar.
* **Premium Storage** för MongoDB shardade-kluster servrar och deras data diskar.

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
För att upprätthålla hög tillgänglighet för alla fyra nivåer av sin onlinebutik, kan Adventure Works-cykler bestämmas på fyra tillgänglighets uppsättningar:

* **azos – Använd som webb** för webb servrar
* **azos – Använd som app** för program servrarna
* **azos – Använd som-databas** för servrarna i MongoDB shardade-klustret
* **azos – Använd som domänkontrollant** för domän kontrol Lanterna

## <a name="virtual-machines"></a>Virtuella datorer
Adventure Works-cykler beslutade följande namn för sina virtuella Azure-datorer:

* **azos-use-VM-web01** för den första webb servern
* **azos-use-VM-web02** för den andra webb servern
* **azos-use-VM-app01** för den första program servern
* **azos-use-VM-app02** för den andra program servern
* **azos-use-VM-db01** för den första MongoDB-servern i klustret
* **azos-use-VM-db02** för den andra MongoDB-servern i klustret
* **azos-use-VM-dc01** för den första domänkontrollanten
* **azos-use-VM-dc02** för den andra domänkontrollanten

Här är den resulterande konfigurationen.

![Slutgiltig program infrastruktur distribuerad i Azure](./media/infrastructure-example/example-config.png)

Den här konfigurationen omfattar:

* Ett virtuellt nätverk i molnet med två undernät (FrontEnd och BackEnd)
* Azure Managed Disks med hjälp av både standard-och Premium diskar
* Fyra tillgänglighets uppsättningar, en för varje nivå i den online-butiken
* De virtuella datorerna för de fyra nivåerna
* En extern belastningsutjämnad uppsättning för HTTPS-baserad webb trafik från Internet till webb servrar
* En intern belastningsutjämnad uppsättning för okrypterad webb trafik från webb servrarna till program servrarna
* En enda resurs grupp
