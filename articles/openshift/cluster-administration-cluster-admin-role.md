---
title: Rollen Azure Red Hat OpenShift kluster administratör | Microsoft Docs
description: Tilldelning och användning av rollen Azure Red Hat OpenShift kluster administratör
services: container-service
author: mjudeikis
ms.author: jzim
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: 0cb875122c63be18f7c39cdfea7986d705ed434e
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2019
ms.locfileid: "74539273"
---
# <a name="azure-red-hat-openshift-customer-administrator-role"></a>Azure Red Hat OpenShift Customer-administratörs roll

Du är kluster administratör för ett kluster med en Red Hat OpenShift. Ditt konto har ökat behörigheter och åtkomst till alla projekt som skapats av användare.

När ditt konto har rollen kund-admin-Cluster-auktorisering kan du hantera ett projekt automatiskt.

> [!Note] 
> Rollen kund-admin-Cluster är inte samma som kluster rollen kluster-admin.


Du kan till exempel köra åtgärder kopplade till en uppsättning verb (`create`) för att använda en uppsättning resurs namn (`templates`). Om du vill visa information om dessa roller och deras uppsättningar med verb och resurser kör du följande kommando:

`$ oc get clusterroles customer-admin-cluster -o yaml`

Verben är inte nödvändigt vis alla mappar direkt till `oc` kommandon. De likställer allmänt med de typer av CLI-åtgärder som du kan utföra. 

Om du till exempel har `list` verbet kan du Visa en lista över alla objekt i ett resurs namn (`oc get`). `get` verbet innebär att du kan visa information om ett enskilt objekt om du känner till dess namn (`oc describe`).

## <a name="configure-the-customer-administrator-role"></a>Konfigurera rollen som kund administratör

Du kan konfigurera rollen kund-admin-kluster kluster endast när klustret skapas genom att ange flaggan `--customer-admin-group-id`. Information om hur du konfigurerar Azure Active Directory och gruppen administratörer finns i [Azure Active Directory integration för Azure Red Hat OpenShift](howto-aad-app-configuration.md).

## <a name="next-steps"></a>Nästa steg

Konfigurera rollen kund-admin-kluster kluster:
> [!div class="nextstepaction"]
> [Azure Active Directory-integrering för Azure Red Hat OpenShift](howto-aad-app-configuration.md)
