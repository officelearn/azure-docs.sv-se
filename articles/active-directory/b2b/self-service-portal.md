---
title: Självbetjäningsannonsportal för B2B-samarbete - Azure AD
description: Azure Active Directory B2B-samarbete ger stöd för dina företagsomfattande relationer genom att tilldela affärspartner selektiv åtkomst till dina affärsprogram
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: sample
ms.date: 02/12/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: cfa67f529408efcc2a703a4f80b15143c774f0b9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77195801"
---
# <a name="self-service-for-azure-ad-b2b-collaboration-sign-up"></a>Självbetjäning för Azure AD B2B-samarbete

Kunder kan göra mycket med de inbyggda funktionerna som finns tillgängliga via [Azure-portalen](https://portal.azure.com) och [Programåtkomstpanelen](https://myapps.microsoft.com) för slutanvändare. Du kan dock behöva anpassa arbetsflödet för registrering för B2B-användare så att de passar behoven i din organisation.

## <a name="azure-ad-entitlement-management-for-b2b-guest-user-sign-up"></a>Hantering av Azure AD-berättigande för B2B-gästanvändare

Som en inbjudande organisation kanske du inte vet i förväg vilka de enskilda externa medarbetarna är som behöver åtkomst till dina resurser. Du behöver ett sätt för användare från partnerföretag att registrera sig med policyer som du kontrollerar. Om du vill att användare från andra organisationer ska kunna begära åtkomst och efter godkännande etableras med gästkonton och tilldelas grupper, appar och SharePoint Online-webbplatser, kan du använda [Azure AD-berättigandehantering](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview) för att konfigurera principer som [hanterar åtkomst för externa användare](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-external-users#how-access-works-for-external-users).

## <a name="azure-active-directory-b2b-invitation-api"></a>Api för Azure Active Directory B2B-inbjudning

Organisationer kan använda [API:et](https://docs.microsoft.com/graph/api/resources/invitation?view=graph-rest-1.0) för Microsoft Graph invitation manager för att skapa egna introduktionsupplevelser för B2B-gästanvändare. När du vill erbjuda självbetjäning b2b gästanvändare registrering, rekommenderar vi att du använder [Azure AD rättshantering](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview). Men om du vill bygga din egen upplevelse kan du använda [API:et](https://docs.microsoft.com/graph/api/invitation-post?view=graph-rest-1.0&tabs=http) för att automatiskt skicka ditt anpassade e-postmeddelande direkt till B2B-användaren, till exempel. Eller din app kan använda inbjudnaRedeemUrl tillbaka i skapandet svar att hantverk din egen inbjudan (genom din kommunikationsmekanism val) till den inbjudna användaren.

## <a name="next-steps"></a>Nästa steg

* [Vad är Azure AD B2B-samarbete?](what-is-b2b.md)
* [Azure AD B2B-samarbetslicensiering](licensing-guidance.md)
* [Vanliga frågor och svar om Azure Active Directory B2B-samarbete](faq.md)
