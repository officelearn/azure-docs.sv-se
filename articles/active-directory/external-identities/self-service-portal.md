---
title: Registrerings Portal för självbetjäning för B2B-samarbete – Azure AD
description: Lär dig hur du anpassar arbets flödet för onboarding för Azure Active Directory B2B-användare så att de passar din organisations behov.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 62805564f716d255f38c9312da5c5c986fba944c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91265552"
---
# <a name="self-service-for-azure-ad-b2b-collaboration-sign-up"></a>Registrera dig för Azure AD B2B-samarbete via självbetjäning

Kunder kan göra mycket med de inbyggda funktionerna som finns tillgängliga via [Azure-portalen](https://portal.azure.com) och [Programåtkomstpanelen](https://myapps.microsoft.com) för slutanvändare. Du kan dock behöva anpassa arbetsflödet för registrering för B2B-användare så att de passar behoven i din organisation.

## <a name="azure-ad-entitlement-management-for-b2b-guest-user-sign-up"></a>Hantering av Azure AD-rättighets hantering för B2B-gäst användar registrering

Som bjudande organisation kanske du inte vet i förväg vilka enskilda externa medarbetare är som behöver åtkomst till dina resurser. Du behöver ett sätt för användare från partner företag att logga in med principer som du styr. Om du vill göra det möjligt för användare från andra organisationer att begära åtkomst och godkännas med gäst konton och tilldelas till grupper, appar och SharePoint Online-webbplatser kan du använda [Azure AD-hantering](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview) för att konfigurera principer som [hanterar åtkomst för externa användare](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-external-users#how-access-works-for-external-users).

## <a name="azure-active-directory-b2b-invitation-api"></a>API för inbjudan till Azure Active Directory B2B

Organisationer kan använda [API: et Microsoft Graph Inbjudnings hanteraren](https://docs.microsoft.com/graph/api/resources/invitation?view=graph-rest-1.0) för att bygga sina egna onboarding-upplevelser för B2B-gäst användare. När du vill erbjuda självbetjäning B2B gäst användar registrering rekommenderar vi att du använder [hantering av Azure AD-rättigheter](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview). Men om du vill skapa en egen upplevelse kan du använda [API: et för att skapa inbjudan](https://docs.microsoft.com/graph/api/invitation-post?view=graph-rest-1.0&tabs=http) för att automatiskt skicka den anpassade e-postinbjudan direkt till B2B-användaren, till exempel. Eller så kan din app använda inviteRedeemUrl som returnerades i ett svar för att skapa en egen inbjudan (via din kommunikations mekanism) till den inbjudna användaren.

## <a name="next-steps"></a>Nästa steg

* [Vad är Azure AD B2B-samarbete?](what-is-b2b.md)
* [Azure AD B2B-samarbetslicensiering](licensing-guidance.md)
* [Vanliga frågor och svar om Azure Active Directory B2B-samarbete](faq.md)
