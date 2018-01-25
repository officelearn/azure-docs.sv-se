---
title: "Azure Container registret autentisering med tjänstens huvudnamn"
description: "Lär dig att ge åtkomst till bilder i ditt privata behållare registret med hjälp av en Azure Active Directory-tjänstens huvudnamn."
services: container-registry
author: mmacy
manager: timlt
ms.service: container-registry
ms.topic: article
ms.date: 01/24/2018
ms.author: marsma
ms.openlocfilehash: 97036ecabceb12b87b76c6ecb7e521157cbef827
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2018
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

## <a name="next-steps"></a>Nästa steg

Du kan använda dess autentiseringsuppgifter i dina program och tjänster för interaktion från registret när du har ett huvudnamn för tjänsten som du har beviljat åtkomst till behållaren-registret.

Konfigurera enskilda program att använda service principal autentiseringsuppgifter är utanför omfånget för den här artikeln, hittar du anvisningar för vissa specifika tjänster och plattformar här:

* [Autentisera med Azure-behållaren registret från Azure Container Service (AKS)](container-registry-auth-aks.md)
* [Autentisera med Azure-behållaren registret från Azure-Behållarinstanser (ACI)](container-registry-auth-aci.md)

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az_acr_login