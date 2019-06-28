---
title: Expandera Azure VMware-lösning av CloudSimple privat moln
description: Beskriver hur du expanderar en befintlig CloudSimple privata moln för att lägga till kapacitet i ett befintligt eller ett nytt kluster
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/06/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 293a09c57ca95e2774e44ff4bc9f9f2c31be2f49
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/22/2019
ms.locfileid: "67333171"
---
# <a name="expand-a-cloudsimple-private-cloud"></a>Expandera ett CloudSimple privat moln

CloudSimple ger flexibilitet för att dynamiskt expandera ett privat moln. Du kan börja med en konfiguration med mindre och sedan utöka när du behöver högre kapacitet. Eller du kan skapa ett privat moln baserat på aktuella behov och sedan utöka när förbrukning växer.

Ett privat moln består av en eller flera vSphere-kluster. Varje kluster kan ha 3 till 16 noder.  När du expanderar ett privat moln måste du lägga till noder i det befintliga klustret eller skapa ett nytt kluster. Ytterligare noder måste vara av samma typ (SKU) som de befintliga noderna för att utöka ett befintligt kluster. Noderna kan vara av en annan typ för att skapa ett nytt kluster. Mer information om privat moln begränsar begränsar finns i avsnittet i [CloudSimple optimeras översikt](cloudsimple-private-cloud.md) artikeln.

Ett privat moln skapas med en standard **Datacenter** på vCenter.  Varje datacenter fungerar som en översta management-entitet.  För ett nytt kluster ger CloudSimple valet av att lägga till befintliga datacenter eller skapa ett nytt datacenter.

Som en del av den nya konfigurationen för klustret konfigurerar CloudSimple VMware-infrastruktur.  Inställningarna omfattar lagringsinställningar för virtuellt SAN diskgrupper, VMware, hög tillgänglighet och distribuerade resursen Scheduler (DRS).

Ett privat moln kan utökas flera gånger. Expansion kan göras endast när du håller dig inom gränserna för övergripande noden. Varje gång du expandera ett privat moln som du lägger till att det befintliga klustret eller skapa en ny.

## <a name="before-you-begin"></a>Innan du börjar

Noder måste etableras innan du kan expandera ditt privata moln.  Mer information om etablering noder finns i [etablera noder för VMware-lösning genom CloudSimple - Azure](create-nodes.md) artikeln.  För att skapa ett nytt kluster, måste du ha minst tre tillgängliga noder i samma SKU.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="expand-a-private-cloud"></a>Expandera ett privat moln

1. [Få åtkomst till portalen CloudSimple](access-cloudsimple-portal.md).

2. Öppna den **resurser** sidan och väljer du det privata moln som du vill expandera.

3. Klicka på Sammanfattning under **Expandera**.

    ![Expandera privat moln](media/resources-expand-private-cloud.png)

4. Välj om du vill visa ditt befintliga kluster eller skapa ett nytt vSphere-kluster. När du gör ändringar, uppdateras den sammanfattande informationen på sidan.

    * Om du vill expandera ditt befintliga kluster, klickar du på **Expandera klustret med befintliga**. Välj det kluster du vill expandera och ange antalet noder för att lägga till. Varje kluster kan ha högst 16 noder.
    * Lägg till ett nytt kluster, klicka på **Skapa nytt kluster**. Ange ett namn för klustret. Välj ett befintligt datacenter eller ange ett namn för att skapa ett nytt datacenter. Välj den noden. Du kan välja en annan nod-typ när du skapar ett nytt vSphere-kluster, men inte när du expanderar ett befintligt vSphere-kluster. Välj antalet noder. Varje nytt kluster måste ha minst tre noder.

    ![Expandera privat moln – lägga till noder](media/resources-expand-private-cloud-add-nodes.png)

5. Klicka på **skicka** att utöka det privata molnet.

## <a name="next-steps"></a>Nästa steg

* [Använda virtuella VMware-datorer på Azure](quickstart-create-vmware-virtual-machine.md)
* Läs mer om [privata moln](cloudsimple-private-cloud.md)