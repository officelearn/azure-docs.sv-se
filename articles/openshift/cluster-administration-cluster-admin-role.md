---
title: Rollen Azure Red Hat OpenShift kluster administratör | Microsoft Docs
description: Tilldelning och användning av rollen Azure Red Hat OpenShift kluster administratör
services: container-service
author: mjudeikis
ms.author: jzim
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: 610b1e0112b8135aa09ade5c800eaed987635cb4
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2020
ms.locfileid: "76545644"
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

Du kan konfigurera rollen kund-admin-kluster kluster endast när klustret skapas genom att ange flaggan `--customer-admin-group-id`. Det här fältet kan för närvarande inte konfigureras i Azure Portal. Information om hur du konfigurerar Azure Active Directory och gruppen administratörer finns i [Azure Active Directory integration för Azure Red Hat OpenShift](howto-aad-app-configuration.md).

## <a name="confirm-membership-in-the-customer-administrator-role"></a>Bekräfta medlemskap i rollen kund administratör

Om du vill bekräfta ditt medlemskap i kund administratörs gruppen kan du prova kommandona OpenShift CLI `oc get nodes` eller `oc projects`. `oc get nodes` visar en lista över noder om du har rollen kund administratör-kluster och ett behörighets fel om du bara har rollen kund administratör-projekt. `oc projects` visar alla projekt i klustret i stället för enbart de projekt som du arbetar med.

Du kan utforska roller och behörigheter i klustret ytterligare genom att använda kommandot [`oc policy who-can <verb> <resource>`](https://docs.openshift.com/container-platform/3.11/admin_guide/manage_rbac.html#managing-role-bindings) .

## <a name="next-steps"></a>Nästa steg

Konfigurera rollen kund-admin-kluster kluster:
> [!div class="nextstepaction"]
> [Azure Active Directory-integrering för Azure Red Hat OpenShift](howto-aad-app-configuration.md)
