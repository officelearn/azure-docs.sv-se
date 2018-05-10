---
title: Azure Container registret autentisering med tjänstens huvudnamn
description: Lär dig att ge åtkomst till bilder i ditt privata behållare registret med hjälp av en Azure Active Directory-tjänstens huvudnamn.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 04/23/2018
ms.author: marsma
ms.openlocfilehash: 16af83522dd55744c485f6dd3696481e16da1b22
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="azure-container-registry-authentication-with-service-principals"></a>Azure Container registret autentisering med tjänstens huvudnamn

Du kan använda en Azure Active Directory (AD Azure) tjänstens huvudnamn för att ge behållaren image `docker push` och `pull` åtkomst till behållaren registret. Du kan ge åtkomst till ”fjärradministrerade” tjänster och program med hjälp av ett huvudnamn för tjänsten.

## <a name="what-is-a-service-principal"></a>Vad är en tjänstens huvudnamn?

Azure AD *tjänsten säkerhetsobjekt* ger åtkomst till Azure-resurser i din prenumeration. Du kan tänka dig en tjänst huvudnamn som användare för en tjänst där ”tjänst” är alla program, en tjänst eller en plattform som behöver åtkomst till resurserna. Du kan konfigurera ett huvudnamn för tjänsten med åtkomstbehörigheter begränsad endast till de resurser som du anger. Du kan sedan konfigurera programmet eller tjänsten för att använda tjänstens huvudnamn autentiseringsuppgifter för åtkomst till dessa resurser.

I samband med Azure Container registret, kan du skapa en Azure AD tjänstens huvudnamn med behörigheter för pull, push och pull eller ägare till ditt privata Docker-registret i Azure.

## <a name="why-use-a-service-principal"></a>Varför ska jag använda ett huvudnamn för tjänsten?

Du kan ange begränsade åtkomst i privata behållare registret med hjälp av en Azure AD-tjänstens huvudnamn. Du kan skapa olika tjänstens huvudnamn för var och en av dina program eller tjänster med skräddarsydda åtkomstbehörighet till registret. Och eftersom du kan undvika delning av autentiseringsuppgifter mellan tjänster och program, rotera autentiseringsuppgifter eller återkalla åtkomst för endast tjänstens huvudnamn (och därmed programmet) du väljer.

Webbprogrammet kan till exempel använda ett huvudnamn för tjänsten som ger det bild `pull` komma åt endast när ditt system kan använda ett huvudnamn för tjänsten som ger med både `push` och `pull` åtkomst. Om utvecklingen av ditt program ändras händer kan rotera du dess Tjänstereferenser principen utan att påverka systemets build.

## <a name="when-to-use-a-service-principal"></a>När du ska använda ett huvudnamn för tjänsten

Du bör använda ett huvudnamn för tjänsten för att ge åtkomst till registret i **fjärradministrerade scenarier**. Alla program, en tjänst eller ett skript som måste push och pull-behållaren bilder som är i automatisk eller på annat sätt oövervakat.

För enskilda åtkomst till ett register, till exempel när du manuellt hämtar en avbildning av behållare på utvecklingsdatorn, bör du istället använda egna [Azure AD identity](container-registry-authentication.md#individual-login-with-azure-ad) för åtkomst till registret (till exempel med [az acr inloggningen][az-acr-login]).

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

## <a name="sample-scripts"></a>Exempelskript

Du hittar de föregående exempel på skript för Azure CLI på GitHub, som korrekt versioner för Azure PowerShell:

* [Azure CLI][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="next-steps"></a>Nästa steg

Du kan använda dess autentiseringsuppgifter i dina program och tjänster för interaktion från registret när du har ett huvudnamn för tjänsten som du har beviljat åtkomst till behållaren-registret.

Konfigurera enskilda program att använda service principal autentiseringsuppgifter är utanför omfånget för den här artikeln, hittar du anvisningar för vissa specifika tjänster och plattformar här:

* [Autentisera med Azure-behållaren registret från Azure Kubernetes-tjänsten (AKS)](container-registry-auth-aks.md)
* [Autentisera med Azure-behållaren registret från Azure-Behållarinstanser (ACI)](container-registry-auth-aci.md)

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az_acr_login