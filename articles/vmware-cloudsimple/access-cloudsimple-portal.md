---
title: Azure VMware-lösning för åtkomst av CloudSimple - Portal
description: Beskriver hur du kommer åt VMware lösning av CloudSimple portalen från Azure-portalen
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 61c3d37f365034984231c780199e181872c010c6
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67544118"
---
# <a name="accessing-the-vmware-solution-by-cloudsimple-portal-from-azure-portal"></a>Åtkomst till VMware-lösningen av CloudSimple portalen från Azure-portalen

Enkel inloggning har stöd för åtkomst till CloudSimple-portalen. När du har loggat in på Azure Portal kan du komma åt CloudSimple portal utan att logga in igen. Första gången du använder CloudSimple-portalen uppmanas du att auktorisera den [CloudSimple Service auktorisering](#consent-to-cloudsimple-service-authorization-application) program.  Auktorisering är en engångsåtgärd.

## <a name="before-you-begin"></a>Innan du börjar

Användare med builtin **ägare** och **deltagare** roller kan komma åt CloudSimple-portalen.  Rollerna konfigureras på resursgruppen där CloudSimple tjänsten har distribuerats.  Rollerna kan också konfigureras på CloudSimple objektet.  Läs mer om hur du felsöker din roll [visa rolltilldelningar](https://docs.microsoft.com/azure/role-based-access-control/check-access) artikeln.

Om du använder anpassade roller rollen ska ha någon av följande åtgärder under ```Actions```.  Mer information om anpassade roller finns i [anpassade roller för Azure-resurser](https://docs.microsoft.com/azure/role-based-access-control/custom-roles).  Om någon av åtgärderna är en del av ```NotActions```, användaren kan inte komma åt CloudSimple-portalen. 

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

3. Välj tjänsten CloudSimple där du vill skapa ditt privata moln.

4. På den **översikt** klickar du på **gå till portalen CloudSimple**.  Om du försöker komma åt CloudSimple portalen från Azure portal för första gången, uppmanas du att auktorisera den [CloudSimple Service auktorisering](#consent-to-cloudsimple-service-authorization-application) program. 

    ![Starta CloudSimple-portalen](media/launch-cloudsimple-portal.png)

> [!TIP]
> Om du väljer en privat moln-åtgärd (t.ex skapa eller utöka ett privat moln) direkt från Azure portal, öppnar portalen CloudSimple till den angivna sidan.

I portalen CloudSimple väljer **Start** på menyn på klientsidan för att visa sammanfattningsinformation om dina privata moln. Resurser och kapacitet för ditt privata moln som visas, tillsammans med aviseringar och aktiviteter som kräver uppmärksamhet. Klicka på namngivna ikonerna längst ned på sidan för vanliga uppgifter.

![Startsida](media/cloudsimple-portal-home.png)

## <a name="consent-to-cloudsimple-service-authorization-application"></a>Samtyck till program CloudSimple Service-auktorisering

Starta CloudSimple portalen från Azure portal för första gången kräver ditt medgivande för programmet CloudSimple auktoriseringen av tjänsten.  Välj **acceptera** att bevilja behörigheter som krävs och få åtkomst till CloudSimple-portalen. 

![Godkänna CloudSimple auktoriseringen av tjänsten - administratörer](media/cloudsimple-azure-consent.png)

Om du har behörighet för global administratör, kan du ge samtycke för din organisation.  Välj **ge samtycke åt din organisation**.

![Godkänna CloudSimple auktoriseringen av tjänsten - global administratör](media/cloudsimple-azure-consent-global-admin.png)

Om din behörighet inte tillåter åtkomst till CloudSimple-portalen, kontaktar du global administratör för din klient för att bevilja behörigheter som krävs.  En global administratör kan ge samtycke åt din organisation.

![Godkänna CloudSimple Service auktorisering – kräver administratörer](media/cloudsimple-azure-consent-requires-administrator.png)

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [skapa ett privat moln](https://docs.azure.cloudsimple.com/create-private-cloud/)
* Lär dig hur du [konfigurera en privat molnmiljö](quickstart-create-private-cloud.md)
