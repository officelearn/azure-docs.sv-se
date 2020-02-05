---
title: Komma åt Azure VMware-lösningar (AVS) – Portal
description: Beskriver hur du kommer åt Azure VMware-lösningar (AVS) från Azure Portal
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c0b0601b9d2cf505aa5b25e10875338352cab771
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2020
ms.locfileid: "77015958"
---
# <a name="access-azure-vmware-solutions-avs-from-the-azure-portal"></a>Få åtkomst till Azure VMware-lösningar (AVS) från Azure Portal

Enkel inloggning stöds för åtkomst till AVS-portalen. När du har loggat in på Azure Portal kan du komma åt AVS-portalen utan att logga in igen. Första gången du får åtkomst till den AVS-Portal du uppmanas att godkänna [AVS-tjänstens Authorization-](#consent-to-avs-service-authorization-application) program. Auktorisering är en engångs åtgärd.

## <a name="before-you-begin"></a>Innan du börjar

Användare med rollen fördefinierad **ägare** och **deltagare** har åtkomst till AVS-portalen. Rollerna måste konfigureras i resurs gruppen där AVS-tjänsten distribueras. Rollerna kan också konfigureras för objektet AVS-tjänst. Mer information om hur du kontrollerar din roll finns i artikeln [Visa roll tilldelningar](https://docs.microsoft.com/azure/role-based-access-control/check-access) . Endast användare med inbyggda **ägare** och **deltagar** roller kan komma åt AVS-portalen. Rollerna måste konfigureras för prenumerationen. Mer information om hur du kontrollerar din roll finns i artikeln [Visa roll tilldelningar](https://docs.microsoft.com/azure/role-based-access-control/check-access) .

Om du använder anpassade roller bör rollen ha någon av följande åtgärder under ```Actions```.  Mer information om anpassade roller finns i [anpassade roller för Azure-resurser](https://docs.microsoft.com/azure/role-based-access-control/custom-roles). Om någon av åtgärderna är en del av ```NotActions```kan användaren inte komma åt AVS-portalen. 

```
Microsoft.VMwareCloudSimple/*
Microsoft.VMwareCloudSimple/*/write
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*/write
```

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-avs-portal"></a>Åtkomst till AVS-portalen

1. Välj **Alla tjänster**.

2. Sök efter **AVS-tjänster**.

3. Välj den AVS-tjänst som du vill skapa ditt privata moln på.

4. På sidan **Översikt** klickar du på **gå till AVS-portalen**. Om du ansluter till AVS-portalen från Azure Portal för första gången uppmanas du att auktorisera programmet för [AVS-tjänstens auktorisering](#consent-to-avs-service-authorization-application) . 

    ![Starta AVS-portalen](media/launch-cloudsimple-portal.png)

> [!NOTE]
> Om du väljer en privat moln åtgärd (t. ex. genom att skapa eller expandera ett privat moln) direkt från Azure Portal, öppnas AVS-portalen på den angivna sidan.

I AVS-portalen väljer du **Start** på sidan för att Visa sammanfattnings information om ditt moln i molnet. Resurserna och kapaciteten för ditt moln privata moln visas, tillsammans med aviseringar och uppgifter som kräver åtgärder. För vanliga uppgifter klickar du på de namngivna ikonerna överst på sidan.

![Start sida](media/cloudsimple-portal-home.png)

## <a name="consent-to-avs-service-authorization-application"></a>Medgivande till AVS-tjänstens Authorization-program

Att starta AVS-portalen från Azure Portal för första gången kräver ditt medgivande för AVS-tjänstens Authorization-program. Välj **acceptera** för att bevilja begärda behörigheter och åtkomst till AVS-portalen.

![Medgivande till AVS-tjänsteauktorisering – administratörer](media/cloudsimple-azure-consent.png)

Om du har global administratörs behörighet kan du godkänna din organisation. Välj **medgivande för din organisations räkning**.

![Medgivande till AVS-tjänsteauktorisering – global administratör](media/cloudsimple-azure-consent-global-admin.png)

Om dina behörigheter inte tillåter åtkomst till AVS-portalen kontaktar du den globala administratören för din klient organisation för att bevilja de behörigheter som krävs. En global administratör kan samtycka åt din organisation.

![Medgivande till AVS-tjänsteauktorisering – kräver administratörer](media/cloudsimple-azure-consent-requires-administrator.png)

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [skapar ett privat moln](https://docs.azure.cloudsimple.com/create-private-cloud/)
* Lär dig hur du [konfigurerar en privat moln miljö](quickstart-create-private-cloud.md)
