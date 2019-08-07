---
title: Mappa Azure-prenumerationen till resurspooler på Azure VMware-lösningen från CloudSimple
description: Beskriver hur du mappar en resurspool på Azure VMware-lösningen från CloudSimple till din Azure-prenumeration
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1bf721f35500d2ff1344996e7750c5e574f40f31
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816261"
---
# <a name="map-resource-pools-from-your-private-cloud-to-your-azure-subscription"></a>Mappa resurspooler från ditt privata moln till din Azure-prenumeration

Med mappning av Azure-prenumeration kan du mappa resurspooler från ditt privata moln vCenter till din Azure-prenumeration. Du kan bara mappa den prenumeration där du har skapat CloudSimple-tjänsten.  Att skapa en virtuell VMware-dator från den Azure Portal distribuerar den virtuella datorn i den mappade resurspoolen.  I CloudSimple-portalen kan du Visa och hantera Azure-prenumerationen för dina privata moln.

En prenumeration kan mappas till flera vCenter-resurspooler för ett privat moln.  Du måste mappa resurspooler för varje privat moln.  Endast mappade resurspooler kommer att vara tillgängliga för att skapa en virtuell VMware-dator från Azure Portal.

> [!IMPORTANT]
> När du mappar en resurspool mappas även eventuella underordnade resurspooler. Det går inte att mappa en överordnad resurspool om några underordnade resurspooler redan har mappats.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har en CloudSimple-tjänst och ett privat moln i din prenumeration.  Information om hur du skapar en CloudSimple-tjänst finns i [snabb start – skapa tjänst](quickstart-create-cloudsimple-service.md).  Om du behöver skapa ett privat moln, se [snabb start – konfigurera en privat moln miljö](quickstart-create-private-cloud.md).

Du kan mappa vCenter-klustret (resurspool) till din prenumeration.  Om du vill skapa en ny resurspool kan du läsa artikeln [skapa en](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.resmgmt.doc/GUID-0F6C6709-A5DA-4D38-BE08-6CB1002DD13D.html) resurspool på VMware-dokumentations webbplatsen.

## <a name="default-resource-group"></a>Standard resurs grupp

Genom att skapa en ny virtuell CloudSimple-dator från Azure Portal kan du välja resurs gruppen.  En virtuell dator som skapats i privat moln vCenter i en mappad resurspool visas på Azure Portal.  Den identifierade virtuella datorn kommer att placeras i Azures standard resurs grupp.  Du kan ändra namnet på standard resurs gruppen.

## <a name="map-azure-subscription"></a>Mappa Azure-prenumeration

1. Få åtkomst till [CloudSimple-portalen](access-cloudsimple-portal.md).

2. Öppna sidan **resurser** och välj det privata moln som du vill mappa.

3. Välj **Mappning av Azure**-prenumerationer.

4. Klicka på **Redigera Azure-prenumerations mappning**.

5. Om du vill mappa tillgängliga resurspooler väljer du dem till vänster och klickar på pilen till höger.

6. Om du vill ta bort mappningar markerar du dem till höger och klickar på pilen till vänster.

    ![Azure-prenumerationer](media/resources-azure-mapping.png)

7. Klicka på **OK**.

## <a name="change-default-resource-group-name"></a>Ändra standard resurs grupps namn

1. Få åtkomst till [CloudSimple-portalen](access-cloudsimple-portal.md).

2. Öppna sidan **resurser** och välj det privata moln som du vill mappa.

3. Välj **Mappning av Azure**-prenumerationer.

4. Klicka på **Redigera** under namn på Azure-resurs grupp.

    ![Redigera resurs grupp namn](media/resources-edit-resource-group-name.png)

5. Ange ett nytt namn på resurs gruppen och klicka på **Skicka**.

    ![Ange ett nytt resurs grupp namn](media/resources-new-resource-group-name.png)

## <a name="next-steps"></a>Nästa steg

* [Använda virtuella VMware-datorer i Azure](quickstart-create-vmware-virtual-machine.md)
* Läs mer om [virtuella CloudSimple-datorer](cloudsimple-virtual-machines.md)