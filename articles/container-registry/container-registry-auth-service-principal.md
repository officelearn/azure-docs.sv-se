---
title: Azure Container Registry autentisering med ett huvud namn för tjänsten
description: Ge åtkomst till avbildningar i ditt privata behållar register med hjälp av ett Azure Active Directory tjänstens huvud namn.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 12/13/2018
ms.author: danlep
ms.openlocfilehash: 97c45a009b155eea7bc61a9dd337090b9e3c1b42
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2019
ms.locfileid: "68309950"
---
# <a name="azure-container-registry-authentication-with-service-principals"></a>Azure Container Registry autentisering med tjänstens huvud namn

Du kan använda ett Azure Active Directory (Azure AD) tjänstens huvud namn för att `docker push` tillhandahålla `pull` behållar avbildning och åtkomst till behållar registret. Genom att använda ett huvud namn för tjänsten kan du ge åtkomst till "konsol löst" tjänster och program.

## <a name="what-is-a-service-principal"></a>Vad är ett huvudnamn för tjänsten?

Azure AD *-tjänstens huvud namn* ger åtkomst till Azure-resurser i din prenumeration. Du kan tänka på ett huvud namn för tjänsten som användar identitet för en tjänst, där "tjänst" är alla program, tjänster eller plattformar som behöver åtkomst till resurserna. Du kan konfigurera ett huvud namn för tjänsten med enbart åtkomst rättigheter för de resurser som du anger. Konfigurera sedan ditt program eller din tjänst att använda tjänstens huvud namn för att få åtkomst till dessa resurser.

I samband med Azure Container Registry kan du skapa en Azure AD-tjänstens huvud namn med pull, push och pull eller andra behörigheter till ditt privata register i Azure. En fullständig lista finns i [Azure Container Registry roller och behörigheter](container-registry-roles.md).

## <a name="why-use-a-service-principal"></a>Varför ska jag använda ett huvud namn för tjänsten?

Genom att använda ett Azure AD-tjänstens huvud namn kan du ge begränsad åtkomst till ditt privata behållar register. Du kan skapa olika tjänst huvud namn för var och en av dina program och tjänster, var och en med anpassade åtkomst rättigheter till registret. Eftersom du kan undvika att dela autentiseringsuppgifter mellan tjänster och program kan du rotera autentiseringsuppgifter eller återkalla åtkomsten för enbart tjänstens huvud namn (och därmed det program) du väljer.

Ditt webb program kan till exempel använda ett huvud namn för tjänsten som bara ger avbildnings `pull` åtkomst, medan ditt build-system kan använda ett huvud namn för tjänsten som ger dem både `push` och `pull` . Om utvecklingen av dina program ändringar händer kan du rotera dess autentiseringsuppgifter för tjänst principen utan att påverka build-systemet.

## <a name="when-to-use-a-service-principal"></a>När du ska använda ett huvud namn för tjänsten

Du bör använda ett huvud namn för tjänsten för att ge register åtkomst i **konsolbaserade scenarier**. Det vill säga alla program, tjänster eller skript som måste push-överföra eller hämta behållar avbildningar på ett automatiserat eller på annat sätt obevakat sätt.

För individuell åtkomst till ett register, till exempel när du hämtar en behållar avbildning manuellt till din utvecklings arbets Station, bör du i stället använda din egen [Azure AD-identitet](container-registry-authentication.md#individual-login-with-azure-ad) för register åtkomst (till exempel med [AZ ACR login][az-acr-login]).

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

## <a name="sample-scripts"></a>Exempelskript

Du kan hitta de föregående exempel skripten för Azure CLI på GitHub, och även Azure PowerShell:

* [Azure CLI][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="next-steps"></a>Nästa steg

När du har fått ett huvud namn för tjänsten som du har beviljat åtkomst till behållar registret kan du använda dess autentiseringsuppgifter i dina program och tjänster för att interagera med konsol lösta register. Du kan använda autentiseringsuppgifter för tjänstens huvud namn från alla Azure-tjänster som kan autentiseras med ett Azure Container Registry. Exempel:

* [Autentisera med Azure Container Registry från Azure Kubernetes service (AKS)](container-registry-auth-aks.md)
* [Autentisera med Azure Container Registry från Azure Container Instances (ACI)](container-registry-auth-aci.md)

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
