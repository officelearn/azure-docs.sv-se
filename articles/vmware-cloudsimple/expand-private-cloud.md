---
title: Expandera Azure VMware-lösning via CloudSimple Private Cloud
description: Beskriver hur du expanderar ett befintligt CloudSimple Private Cloud för att lägga till kapacitet i ett befintligt eller nytt kluster
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a82ba1b433e62ed1c4b72b8e942d4ade29f26c4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025307"
---
# <a name="expand-a-cloudsimple-private-cloud"></a>Expandera ett cloudsimple privat moln

CloudSimple ger flexibiliteten att dynamiskt expandera ett privat moln. Du kan börja med en mindre konfiguration och sedan expandera när du behöver högre kapacitet. Du kan också skapa ett privat moln baserat på aktuella behov och sedan expandera när förbrukningen växer.

Ett privat moln består av ett eller flera vSphere-kluster. Varje kluster kan ha 3 till 16 noder.  När du expanderar ett privat moln lägger du till noder i det befintliga klustret eller skapar ett nytt kluster. Om du vill expandera ett befintligt kluster måste ytterligare noder vara av samma typ (SKU) som de befintliga noderna. För att skapa ett nytt kluster kan noderna vara av en annan typ. Mer information om begränsningar i private cloud finns i avsnittet Gränser i [cloudSimple-artikel med privata moln.](cloudsimple-private-cloud.md)

Ett privat moln skapas med ett **standarddatacenter** på vCenter.  Varje datacenter fungerar som en enhet för hantering på högsta nivå.  För ett nytt kluster ger CloudSimple valet att lägga till det befintliga datacentret eller skapa ett nytt datacenter.

Som en del av den nya klusterkonfigurationen konfigurerar CloudSimple VMware-infrastrukturen.  Inställningarna inkluderar lagringsinställningar för vSAN-diskgrupper, VMware hög tillgänglighet och DRS (Distributed Resource Scheduler).

Ett privat moln kan utökas flera gånger. Expansion kan bara göras när du håller dig inom de övergripande nodgränserna. Varje gång du expanderar ett privat moln lägger du till i det befintliga klustret eller skapar ett nytt.

## <a name="before-you-begin"></a>Innan du börjar

Noder måste etableras innan du kan expandera ditt privata moln.  Mer information om etablering av noder finns i [Etableringsnoder för VMware-lösning av CloudSimple - Azure-artikel.](create-nodes.md)  För att skapa ett nytt kluster måste du ha minst tre tillgängliga noder av samma SKU.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure-portalen på [https://portal.azure.com](https://portal.azure.com).

## <a name="expand-a-private-cloud"></a>Expandera ett privat moln

1. [Öppna CloudSimple-portalen](access-cloudsimple-portal.md).

2. Öppna sidan **Resurser** och välj det privata moln som du vill expandera för.

3. Klicka på **Expandera**i sammanfattningsavsnitt.

    ![Expandera privat moln](media/resources-expand-private-cloud.png)

4. Välj om du vill expandera det befintliga klustret eller skapa ett nytt vSphere-kluster. När du gör ändringar uppdateras sammanfattningsinformationen på sidan.

    * Om du vill expandera det befintliga klustret klickar du på **Expandera befintligt kluster**. Markera det kluster som du vill expandera och ange antalet noder som ska läggas till. Varje kluster kan ha högst 16 noder.
    * Om du vill lägga till ett nytt kluster klickar du på **Skapa nytt kluster**. Ange ett namn för klustret. Välj ett befintligt datacenter eller ange ett namn för att skapa ett nytt datacenter. Välj nodtyp. Du kan välja en annan nodtyp när du skapar ett nytt vSphere-kluster, men inte när du expanderar ett befintligt vSphere-kluster. Välj antalet noder. Varje nytt kluster måste ha minst tre noder.

    ![Expandera privat moln - lägg till noder](media/resources-expand-private-cloud-add-nodes.png)

5. Klicka på **Skicka** om du vill expandera det privata molnet.

## <a name="next-steps"></a>Nästa steg

* [Använda virtuella VMware-datorer på Azure](quickstart-create-vmware-virtual-machine.md)
* Läs mer om [privata moln](cloudsimple-private-cloud.md)