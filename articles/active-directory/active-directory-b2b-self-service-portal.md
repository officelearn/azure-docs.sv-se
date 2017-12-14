---
title: "Registrering självbetjäningsportalen för Azure Active Directory B2B-samarbete | Microsoft Docs"
description: "Azure Active Directory B2B-samarbete ger stöd för dina företagsomfattande relationer genom att tilldela affärspartner selektiv åtkomst till dina affärsprogram"
services: active-directory
documentationcenter: 
author: sasubram
manager: mtillman
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 05/24/2017
ms.author: sasubram
ms.openlocfilehash: 28c93212e247dae23cfb35a587d0d6e87cb56a6f
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="self-service-portal-for-azure-ad-b2b-collaboration-sign-up"></a>Självbetjäningsportalen för Azure AD B2B-samarbete registrering

Kunder kan göra mycket med inbyggda funktioner som är tillgängliga via vår IT-administratören [Azure-portalen](https://portal.azure.com) och vår [programmet åtkomstpanelen](https://myapps.microsoft.com) för slutanvändare. Men det finns också företag måste anpassa onboarding arbetsflödet för B2B-användare efter organisationens behov. De kan göra det med [vårt API](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation).

I diskussioner med våra kunder finns en gemensam måste öka upp över alla andra. Bjuda in organisationen kanske inte vet i förväg som enskilda externa samarbetspartners som behöver åtkomst till sina resurser. De vill ha ett sätt för användare från partnerföretag registrera sig själva med en uppsättning principer som styr bjuda in organisationen. Det här scenariot är möjligt via våra API: er, så vi har publicerat ett projekt på Github som har just: [Github exempelprojektet](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web).

Vår Github-projektet visar hur organisationer kan använda våra API: er och funktioner för en principbaserad, självbetjäning registrering för deras betrodda partners med regler som styr appar som de kan komma åt. Partneranvändarna kan få åtkomst till resurser när de behöver dem, på ett säkert sätt, utan att bjuda in organisationen för att manuellt publicera de. Du kan enkelt distribuera projektet till en Azure-prenumeration önskat.

## <a name="as-is-code"></a>Som-kod

Kom ihåg att den här koden görs tillgänglig som ett exempel för att demonstrera användning av Azure Active Directory B2B-inbjudan API. Det bör anpassas efter dev-team eller en partner och bör granskas innan de kan distribueras i ett produktionsscenario.

## <a name="next-steps"></a>Nästa steg

Läs andra artiklar om Azure AD B2B-samarbete:
* [Vad är Azure AD B2B-samarbete?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Hur lägger Azure Active Directory-administratörer till B2B-samarbete användare?](active-directory-b2b-admin-add-users.md)
* [Hur lägger informationsarbetare till B2B-samarbete användare?](active-directory-b2b-iw-add-users.md)
* [Elementen i e-postinbjudan B2B-samarbete](active-directory-b2b-invitation-email.md)
* [B2B-samarbete inbjudan inlösning](active-directory-b2b-redemption-experience.md)
* [Azure AD B2B-samarbete och licensiering](active-directory-b2b-licensing.md)
* [Felsökning av Azure Active Directory B2B-samarbete](active-directory-b2b-troubleshooting.md)
* [Vanliga och frågor svar om Azure Active Directory B2B-samarbete](active-directory-b2b-faq.md)
* [Multi-Factor Authentication för användare av B2B-samarbete](active-directory-b2b-mfa-instructions.md)
* [Lägg till B2B-samarbete användare utan inbjudan](active-directory-b2b-add-user-without-invite.md)
* [Artikelindex för programhantering i Azure Active Directory](active-directory-apps-index.md)