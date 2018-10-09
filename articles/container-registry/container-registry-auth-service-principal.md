---
title: Azure Container Registry-autentisering med tjänstens huvudnamn
description: Lär dig hur du ger tillgång till avbildningar i ditt privata behållarregister med hjälp av en Azure Active Directory-tjänstobjekt.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 04/23/2018
ms.author: danlep
ms.openlocfilehash: 30f0eb04b4b7d07785854e3079bc6656889edec6
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2018
ms.locfileid: "48854498"
---
# <a name="azure-container-registry-authentication-with-service-principals"></a>Azure Container Registry-autentisering med tjänstens huvudnamn

Du kan använda ett huvudnamn för tjänsten Azure Active Directory (Azure AD) för att ge behållaravbildningen `docker push` och `pull` åtkomst till ditt behållarregister. Genom att använda ett huvudnamn för tjänsten kan ge du åtkomst till ”fjärradministrering” tjänster och program.

## <a name="what-is-a-service-principal"></a>Vad är ett huvudnamn för tjänsten?

Azure AD *tjänsthuvudnamn* ger åtkomst till Azure-resurser i din prenumeration. Du kan tänka dig en tjänst huvudnamn som en användaridentitet för en tjänst, där ”tjänst” är alla program, tjänst eller plattform som behöver åtkomst till resurser. Du kan konfigurera ett huvudnamn för tjänsten med åtkomsträttigheter som omfattar endast dessa resurser som du anger. Du kan sedan konfigurera programmet eller tjänsten om du vill använda autentiseringsuppgifter för tjänstens huvudnamn kan komma åt resurserna.

I samband med Azure Container Registry, kan du skapa en Azure AD-tjänstens huvudnamn med pull, push och pull eller ägare behörigheter till ditt privata Docker-register i Azure.

## <a name="why-use-a-service-principal"></a>Varför ska jag använda ett huvudnamn för tjänsten?

Med hjälp av en Azure AD-tjänstobjekt, kan du ge begränsad åtkomst till ditt privata behållarregister. Du kan skapa olika tjänsthuvudnamn för var och en av dina program eller tjänster, som var med skräddarsydda åtkomsträttigheter till registret. Och eftersom du inte behöver dela autentiseringsuppgifter mellan tjänster och program, kan du rotera autentiseringsuppgifter eller återkalla åtkomsten för endast tjänstens huvudnamn (och därmed programmet) du väljer.

Webbprogrammet kan till exempel använda ett huvudnamn för tjänsten som ger den avbildningen `pull` bara kommer åt, även om ditt system kan använda ett huvudnamn för tjänsten som tillhandahåller med både `push` och `pull` åtkomst. Om utveckling av din app ändras händer kan rotera du dess autentiseringsuppgifter för tjänstens huvudnamn utan att påverka build-system.

## <a name="when-to-use-a-service-principal"></a>När du ska använda ett huvudnamn för tjänsten

Du bör använda ett huvudnamn för tjänsten för att ge åtkomst till registret i **fjärradministrerad scenarier**. Det vill säga bilder alla program, en tjänst eller ett skript som måste skicka eller hämta behållare i automatiserade eller på annat sätt oövervakat.

För enskilda åtkomst till ett register, till exempel när du manuellt hämtar en behållaravbildning till utvecklingsdatorn, bör du istället använda din egen [Azure AD-identitet](container-registry-authentication.md#individual-login-with-azure-ad) för åtkomst till behållarregister (till exempel med [az acr logga in][az-acr-login]).

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

## <a name="sample-scripts"></a>Exempelskript

Du hittar de föregående exempelskript för Azure CLI på GitHub som och versioner för Azure PowerShell:

* [Azure CLI][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="next-steps"></a>Nästa steg

När du har ett huvudnamn för tjänsten att du har beviljats åtkomst till behållarregistret kan använda du dess autentiseringsuppgifter i dina program och tjänster för registret interaktion.

Konfigurera enskilda program att använda autentiseringsuppgifter för tjänstens huvudnamn är utanför omfånget för den här artikeln, hittar du anvisningar för vissa specifika tjänster och plattformar här:

* [Autentisera med Azure Container Registry från Azure Kubernetes Service (AKS)](container-registry-auth-aks.md)
* [Autentisera med Azure Container Registry från Azure Container Instances (ACI)](container-registry-auth-aci.md)

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login