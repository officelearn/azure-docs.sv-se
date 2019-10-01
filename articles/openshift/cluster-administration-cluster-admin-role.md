---
title: Rollen Azure Red Hat OpenShift kluster administratör | Microsoft Docs
description: Azure Red Hat OpenShift kluster administratör roll tilldelning och användning
services: container-service
author: mjudeikis
ms.author: jzim
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: 6d7aacea5e34e21513452880448227a1ca5f9b67
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709953"
---
# <a name="azure-red-hat-openshift-customer-administrator-role"></a>Azure Red Hat OpenShift Customer-administratörs roll

Som en ARO (Azure Red Hat OpenShift) av kluster administratören för ett OpenShift-kluster har ditt konto ökat behörigheter och åtkomst till alla projekt som skapats av användare.

När ditt konto har rollen OSA-Customer-admins som är knuten till den, kan den automatiskt hantera ett projekt.

> [!Note] 
> OSA-Customer admin-clusterrole är inte detsamma som kluster-admin-clusterrole


Du kan till exempel köra åtgärder kopplade till en uppsättning verb (`create`) för att hantera en uppsättning resurs namn (`templates`). Om du vill visa information om dessa roller och deras uppsättningar med verb och resurser kör du följande kommando:

`$ oc describe clusterrole/osa-customer-admin`

Verben är inte nödvändigt vis alla mappar direkt till oc-kommandon, utan motsvarar i allmänhet de typer av CLI-åtgärder som du kan utföra. Om du till exempel använder verbet `list` kan du Visa en lista över alla objekt i ett angivet resurs namn (`oc get`), medan verbet `get` innebär att du kan visa information om ett specifikt objekt om du känner till dess namn (`oc describe`).

## <a name="how-to-configure-customer-administrator-role"></a>Konfigurera rollen som kund administratör

Rollen kund administratör kan bara konfigureras när klustret skapas genom att ange flaggan `--customer-admin-group-id`. Så här konfigurerar du Azure Active Directory-och administratörer-gruppen följ hur du får hjälp: [Azure Active Directory-integrering för Azure Red Hat OpenShift](howto-aad-app-configuration.md)

## <a name="next-steps"></a>Nästa steg

Konfigurera OSA-Customer-admin-rollen:
> [!div class="nextstepaction"]
> [Azure Active Directory-integrering för Azure Red Hat OpenShift](howto-aad-app-configuration.md)
