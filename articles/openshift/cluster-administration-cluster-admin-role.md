---
title: Rollen Azure Red Hat OpenShift kluster administratör | Microsoft Docs
description: Tilldelning och användning av rollen Azure Red Hat OpenShift kluster administratör
services: container-service
author: mjudeikis
ms.author: jzim
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: 1c676b2671b73084a2b4ae8908acb83c23a59b7b
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/03/2019
ms.locfileid: "71936935"
---
# <a name="azure-red-hat-openshift-customer-administrator-role"></a>Azure Red Hat OpenShift Customer-administratörs roll

Du är kluster administratör för ett kluster med en Red Hat OpenShift. Ditt konto har ökat behörigheter och åtkomst till alla projekt som skapats av användare.

När ditt konto har rollen OSA-Customer-admin-auktorisering, kan det automatiskt hantera ett projekt.

> [!Note] 
> Kluster rollen OSA-Customer-admin är inte samma som kluster rollen kluster-admin.


Du kan till exempel köra åtgärder kopplade till en uppsättning verb (`create`) för att hantera en uppsättning resurs namn (`templates`). Om du vill visa information om dessa roller och deras uppsättningar med verb och resurser kör du följande kommando:

`$ oc describe clusterrole/osa-customer-admin`

Verben är inte nödvändigt vis alla mappar direkt till `oc`-kommandon. De likställer allmänt med de typer av CLI-åtgärder som du kan utföra. 

Om du till exempel har verbet `list` kan du Visa en lista över alla objekt i ett resurs namn (`oc get`). Verbet `get` innebär att du kan visa information om ett enskilt objekt om du känner till dess namn (`oc describe`).

## <a name="configure-the-customer-administrator-role"></a>Konfigurera rollen som kund administratör

Du kan bara konfigurera kund administratörs rollen när klustret skapas genom att ange flaggan `--customer-admin-group-id`. Information om hur du konfigurerar Azure Active Directory och gruppen administratörer finns i [Azure Active Directory integration för Azure Red Hat OpenShift](howto-aad-app-configuration.md).

## <a name="next-steps"></a>Nästa steg

Konfigurera rollen OSA-Customer-admin:
> [!div class="nextstepaction"]
> [Azure Active Directory-integrering för Azure Red Hat OpenShift](howto-aad-app-configuration.md)
