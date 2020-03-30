---
title: Azure Red Hat OpenShift klusteradministratörsroll | Microsoft-dokument
description: Tilldelning och användning av Azure Red Hat OpenShift-klusteradministratörsrollen
services: container-service
author: mjudeikis
ms.author: jzim
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: ae9a421a165d6c8bda688819c5233ae5bb1a8562
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79139104"
---
# <a name="azure-red-hat-openshift-customer-administrator-role"></a>Azure Red Hat OpenShift-kundadministratörsroll

Du är klusteradministratör för ett Azure Red Hat OpenShift-kluster. Ditt konto har ökat behörigheter och åtkomst till alla användarskapade projekt.

När ditt konto har den behörighetsroll som är bunden till kunden och administratörsklusteret kan det automatiskt hantera ett projekt.

> [!Note] 
> Rollen för customer-admin-cluster-kluster är inte samma sak som kluster-admin-klusterrollen.

Du kan till exempel köra åtgärder som är`create`associerade med en uppsättning verb`templates`( ) för att arbeta med en uppsättning resursnamn ( ). Om du vill visa information om dessa roller och deras uppsättningar av verb och resurser kör du följande kommando:

`$ oc get clusterroles customer-admin-cluster -o yaml`

Verbnamnen mappas inte nödvändigtvis `oc` direkt till kommandon. De motsvarar mer allmänt de typer av CLI-åtgärder som du kan utföra. 

Att till exempel `list` ha verbet innebär att du kan visa`oc get`en lista över alla objekt i ett resursnamn ( ). Verbet `get` innebär att du kan visa information om ett`oc describe`visst objekt om du vet dess namn ( ).

## <a name="configure-the-customer-administrator-role"></a>Konfigurera rollen kundadministratör

Du kan bara konfigurera klusterklusterrollen för kund-admin-kluster genom att tillhandahålla flaggan `--customer-admin-group-id`. Det här fältet kan för närvarande inte konfigureras i Azure-portalen. Mer information om hur du konfigurerar Azure Active Directory och gruppen Administratörer finns i [Azure Active Directory-integrering för Azure Red Hat OpenShift](howto-aad-app-configuration.md).

## <a name="confirm-membership-in-the-customer-administrator-role"></a>Bekräfta medlemskap i rollen kundadministratör

Om du vill bekräfta medlemskapet i kundadministratörsgruppen `oc get nodes` `oc projects`provar du OpenShift CLI-kommandona eller . `oc get nodes`visar en lista över noder om du har rollen customer-admin-cluster och ett behörighetsfel om du bara har rollen kund-admin-projekt. `oc projects`kommer att visa alla projekt i klustret i motsats till bara de projekt du arbetar i.

Om du vill utforska roller och behörigheter [`oc policy who-can <verb> <resource>`](https://docs.openshift.com/container-platform/3.11/admin_guide/manage_rbac.html#managing-role-bindings) ytterligare i klustret kan du använda kommandot.

## <a name="next-steps"></a>Nästa steg

Konfigurera klusterrollen för kund-admin-kluster:
> [!div class="nextstepaction"]
> [Azure Active Directory-integrering för Azure Red Hat OpenShift](howto-aad-app-configuration.md)
