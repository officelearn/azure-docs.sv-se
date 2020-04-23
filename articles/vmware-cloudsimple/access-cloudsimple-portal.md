---
title: Få tillgång till Azure VMware-lösning via CloudSimple - Portal
description: Beskriver hur du kommer åt portalen för VMware Solution by CloudSimple via Azure Portal
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 91d3dd2eee6f771df23b72eae7a33122c42f1690
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869336"
---
# <a name="access-the-vmware-solution-by-cloudsimple-portal-from-the-azure-portal"></a>Få tillgång till VMware-lösningen via CloudSimple-portalen från Azure-portalen

Enkel inloggning stöds för åtkomst till CloudSimple-portalen. När du har loggat in på Azure-portalen kan du komma åt CloudSimple-portalen utan att logga in igen. Första gången du öppnar CloudSimple-portalen uppmanas du att auktorisera [CloudSimple-tjänstauktoriseringsprogrammet.](#consent-to-cloudsimple-service-authorization-application)  Auktorisering är en engångsåtgärd.

## <a name="before-you-begin"></a>Innan du börjar

Användare med inbyggda **ägar-** och **deltagarroller** kan komma åt CloudSimple-portalen.  Rollerna måste konfigureras i resursgruppen där CloudSimple-tjänsten distribueras.  Rollerna kan också konfigureras på CloudSimple-tjänstobjektet.  Mer information om hur du kontrollerar din roll finns i [artikeln Visa rolltilldelningar.](https://docs.microsoft.com/azure/role-based-access-control/check-access) Endast användare med inbyggda **ägar-** och **deltagarroller** kan komma åt CloudSimple-portalen.  Rollerna måste konfigureras för prenumerationen.  Mer information om hur du kontrollerar din roll finns i [artikeln Visa rolltilldelningar.](https://docs.microsoft.com/azure/role-based-access-control/check-access)

Om du använder anpassade roller bör rollen ha någon ```Actions```av följande åtgärder under .  Mer information om anpassade roller finns i [Anpassade roller för Azure-resurser](https://docs.microsoft.com/azure/role-based-access-control/custom-roles).  Om någon av åtgärderna är ```NotActions```en del av kan användaren inte komma åt CloudSimple-portalen.

```
Microsoft.VMwareCloudSimple/*
Microsoft.VMwareCloudSimple/*/write
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*/write
```

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Få åtkomst till CloudSimple-portalen

1. Välj **Alla tjänster**.

2. Sök efter **CloudSimple Services**.

3. Välj den CloudSimple-tjänst som du vill skapa ditt privata moln på.

4. Klicka på Gå **till CloudSimple-portalen**på sidan **Översikt** .  Om du använder CloudSimple-portalen från Azure-portalen för första gången uppmanas du att auktorisera [CloudSimple-tjänstauktoriseringsprogrammet.](#consent-to-cloudsimple-service-authorization-application) 

    ![Starta CloudSimple-portalen](media/launch-cloudsimple-portal.png)

> [!NOTE]
> Om du väljer en privat molnåtgärd (till exempel skapa eller expandera ett privat moln) direkt från Azure-portalen öppnas CloudSimple-portalen till den angivna sidan.

I CloudSimple-portalen väljer du **Hem** på sidomenyn för att visa sammanfattande information om dina privata moln. Resurserna och kapaciteten för dina privata moln visas, tillsammans med aviseringar och aktiviteter som kräver uppmärksamhet. Om du vill ha vanliga uppgifter klickar du på de namngivna ikonerna högst upp på sidan.

![Hemsida](media/cloudsimple-portal-home.png)

## <a name="consent-to-cloudsimple-service-authorization-application"></a>Samtycke till CloudSimple Service Auktoriseringsprogram

Om du startar CloudSimple-portalen från Azure-portalen för första gången krävs ditt samtycke för CloudSimple-tjänstauktoriseringsprogrammet.  Välj Acceptera om du **vill** bevilja begärda behörigheter och komma åt CloudSimple-portalen.

![Samtycke till CloudSimple Service Authorization - administratörer](media/cloudsimple-azure-consent.png)

Om du har global administratörsbehörighet kan du godkänna din organisation.  Välj **Samtyck åt din organisation**.

![Samtycke till CloudSimple Service Authorization - global administratör](media/cloudsimple-azure-consent-global-admin.png)

Om dina behörigheter inte tillåter åtkomst till CloudSimple-portalen kontaktar du den globala administratören för din klient för att bevilja nödvändiga behörigheter.  En global administratör kan medgivande för din organisation.

![Samtycke till CloudSimple Service Authorization - kräver administratörer](media/cloudsimple-azure-consent-requires-administrator.png)

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [skapar ett privat moln](https://docs.microsoft.com/azure/vmware-cloudsimple/create-private-cloud/)
* Lär dig hur du [konfigurerar en privat molnmiljö](quickstart-create-private-cloud.md)
