---
title: Mappa Azure-prenumeration till resurspooler på Azure VMware-lösning genom CloudSimple
description: Beskriver hur du mappar en resurspool på Azure VMware-lösning genom CloudSimple till din Azure-prenumeration
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/05/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: efda996e03d46a2f97d19558f7c2930b623a639e
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332794"
---
# <a name="map-resource-pools-from-your-private-cloud-to-your-azure-subscription"></a>Mappa resurspooler från ditt privata moln på Azure-prenumerationen

Mappning av Azure-prenumeration kan du mappa resurspooler från ditt privata moln vCenter till din Azure-prenumeration. Du kan mappa endast den prenumeration där du har skapat tjänsten CloudSimple.  Skapa en virtuell VMware-dator från Azure portal distribuerar den virtuella datorn i den mappade resurspoolen.  I CloudSimple-portalen kan du visa och hantera Azure-prenumerationen för dina privata moln.

En prenumeration kan mappas till flera vCenter-resurspooler för ett privat moln.  Du måste mappa resurspooler för varje privat moln.  Endast de mappade resurspoolerna blir tillgänglig för att skapa en virtuell VMware-dator från Azure-portalen.

> [!IMPORTANT]
> Mappning av en resurspool visar också eventuella underordnade resurspooler. En resurspool för överordnade kan inte mappas om alla underordnade resurspooler har redan mappats.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har en CloudSimple tjänst och privat moln i din prenumeration.  Om du vill skapa en tjänst för CloudSimple [Snabbstart – skapa tjänst](quickstart-create-cloudsimple-service.md).  Om du vill skapa ett privat moln finns i [Snabbstart – konfigurera en privat molnmiljö](quickstart-create-private-cloud.md).

Du kan mappa vCenter-klustret (rot-resurspool) till din prenumeration.  Om du vill skapa en ny resurspool [skapa en resurspool](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.resmgmt.doc/GUID-0F6C6709-A5DA-4D38-BE08-6CB1002DD13D.html) artikeln på dokumentationsplatsen för VMware.

## <a name="default-resource-group"></a>Standardresursgruppen

Skapa en ny CloudSimple virtuell dator från Azure-portalen kan du välja resursgruppen.  En virtuell dator som skapats på privat moln vcenter-Server i en mappade resurspool kommer att visas på Azure-portalen.  Den identifierade virtuella datorn kommer att placeras i standard Azure-resursgrupp.  Du kan ändra namnet på resursgruppen som standard.

## <a name="map-azure-subscription"></a>Mappa Azure-prenumeration

1. Åtkomst till den [CloudSimple portal](access-cloudsimple-portal.md).

2. Öppna den **resurser** och väljer det privata moln som du vill mappa.

3. Välj **Azure-prenumerationer mappning**.

4. Klicka på **redigera Azure-prenumeration mappning**.

5. Om du vill mappa tillgängliga resurspooler, markerar du dem till vänster och klicka på högerpilen.

6. Välj dem till höger och klicka på vänsterpilen för att ta bort mappningar.

    ![Azure-prenumerationer](media/resources-azure-mapping.png)

7. Klicka på **OK**.

## <a name="change-default-resource-group-name"></a>Ändra standard resursgruppens namn

1. Åtkomst till den [CloudSimple portal](access-cloudsimple-portal.md).

2. Öppna den **resurser** och väljer det privata moln som du vill mappa.

3. Välj **Azure-prenumerationer mappning**.

4. Klicka på **redigera** under Azure resursgruppens namn.

    ![Redigera resursgruppens namn](media/resources-edit-resource-group-name.png)

5. Ange ett nytt namn för resursgruppen och klickar på **skicka**.

    ![Ange nytt resursgruppnamn](media/resources-new-resource-group-name.png)

## <a name="next-steps"></a>Nästa steg

* [Använda virtuella VMware-datorer på Azure](quickstart-create-vmware-virtual-machine.md)
* Läs mer om [CloudSimple virtuella datorer](cloudsimple-virtual-machines.md)