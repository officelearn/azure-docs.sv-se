---
title: Expandera Azure VMware-lösningar (AVS) privat moln
description: Beskriver hur du expanderar ett befintligt AVS-privat moln för att lägga till kapacitet i ett befintligt eller nytt kluster
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3286b7537056a6c2f282533aa629ebbe47612690
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025307"
---
# <a name="expand-an-avs-private-cloud"></a>Expandera ett privat AVS-moln

AVS ger flexibiliteten att dynamiskt utöka ett molns privata moln. Du kan börja med en mindre konfiguration och sedan expandera när du behöver större kapacitet. Du kan också skapa ett privat moln i molnet baserat på aktuella behov och sedan expandera när förbrukningen växer.

Ett privat AVS-moln består av ett eller flera vSphere-kluster. Varje kluster kan ha 3 till 16 noder. När du expanderar ett privat moln moln kan du lägga till noder i det befintliga klustret eller skapa ett nytt kluster. Om du vill expandera ett befintligt kluster måste ytterligare noder vara av samma typ (SKU) som de befintliga noderna. Noderna kan vara av en annan typ för att skapa ett nytt kluster. Mer information om gränser för molnets privata moln finns i avsnittet begränsningar i [Översikt över AVS-privat moln](cloudsimple-private-cloud.md) .

Ett privat AVS-moln skapas med ett standard **Data Center** på vCenter. Varje data Center fungerar som en hanterings enhet på den översta nivån. I ett nytt kluster erbjuder AVS valet att lägga till i det befintliga data centret eller skapa ett nytt Data Center.

Som en del av den nya kluster konfigurationen konfigurerar AVS VMware-infrastrukturen. Inställningarna omfattar lagrings inställningar för virtuellt San disk grupper, VMware hög tillgänglighet och Distributed Resource Scheduler (DRS).

Ett privat AVS-moln kan expanderas flera gånger. Expansion kan bara utföras när du ligger inom gränserna för den totala noden. Varje gången du expanderar ett moln privat moln som du lägger till i det befintliga klustret eller skapar ett nytt.

## <a name="before-you-begin"></a>Innan du börjar

Noder måste vara etablerade innan du kan expandera ditt molns privata moln. Mer information om hur du etablerar noder finns i [etablera noder för VMware-lösning enligt AVS-Azure-](create-nodes.md) artikeln. För att skapa ett nytt kluster måste du ha minst tre tillgängliga noder av samma SKU.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="expand-an-avs-private-cloud"></a>Expandera ett privat AVS-moln

1. [Få åtkomst till AVS-portalen](access-cloudsimple-portal.md).

2. Öppna sidan **resurser** och välj det AVS-privata moln som du vill expandera.

3. I avsnittet Sammanfattning klickar du på **expandera**.

    ![Expandera AVS-privat moln](media/resources-expand-private-cloud.png)

4. Välj om du vill utöka det befintliga klustret eller skapa ett nytt vSphere-kluster. När du gör ändringar uppdateras sammanfattnings informationen på sidan.

    * Klicka på **Visa befintligt kluster**för att expandera det befintliga klustret. Välj det kluster som du vill expandera och ange antalet noder som ska läggas till. Varje kluster kan ha högst 16 noder.
    * Om du vill lägga till ett nytt kluster klickar du på **Skapa nytt kluster**. Ange ett namn för klustret. Välj ett befintligt Data Center eller ange ett namn för att skapa ett nytt Data Center. Välj nodtyp. Du kan välja en annan nodtyp när du skapar ett nytt vSphere-kluster, men inte när du expanderar ett befintligt vSphere-kluster. Välj antalet noder. Varje nytt kluster måste ha minst tre noder.

    ![Expandera AVS-privat moln – Lägg till noder](media/resources-expand-private-cloud-add-nodes.png)

5. Klicka på **Skicka** för att expandera det privata AVS-molnet.

## <a name="next-steps"></a>Nästa steg

* [Använda virtuella VMware-datorer i Azure](quickstart-create-vmware-virtual-machine.md)
* Lär dig mer om [moln privata moln](cloudsimple-private-cloud.md)