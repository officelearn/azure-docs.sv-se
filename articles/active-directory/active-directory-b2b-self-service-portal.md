---
title: "Registrering självbetjäningsportalen för Azure Active Directory B2B-samarbete | Microsoft Docs"
description: "Azure Active Directory B2B-samarbete ger stöd för dina företagsomfattande relationer genom att tilldela affärspartner selektiv åtkomst till dina affärsprogram"
services: active-directory
documentationcenter: 
author: twooley
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
ms.author: twooley
ms.reviewer: sasubram
ms.openlocfilehash: bb63a3b23bdcaac5c94d43bb8e7294a82b0c3fa0
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2018
---
# <a name="self-service-portal-for-azure-ad-b2b-collaboration-sign-up"></a>Självbetjäningsportalen för Azure AD B2B-samarbete registrering

Kunder kan göra mycket med inbyggda funktioner som är tillgängliga via IT-administratören [Azure-portalen](https://portal.azure.com) och [programmet åtkomstpanelen](https://myapps.microsoft.com) för slutanvändare. Men det finns också företag måste anpassa onboarding arbetsflödet för B2B-användare efter organisationens behov. De kan göra det med [inbjudan API](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation).

I diskussioner med kunder krävs en gemensam som stiger upp över alla andra. Bjuda in organisationen kanske inte vet i förväg som enskilda externa samarbetspartners som behöver åtkomst till sina resurser. De vill ha ett sätt för användare från partnerföretag registrera sig själva med en uppsättning principer som styr bjuda in organisationen. Det här scenariot är möjligt via API: er, så vi har publicerat ett projekt på Github som har just: [Github exempelprojektet](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web).

Det här projektet i Github visar hur organisationer kan använda API: er och funktioner för en principbaserad, självbetjäning registrering för deras betrodda partners med regler som styr appar som de kan komma åt. Partneranvändarna kan få åtkomst till resurser när de behöver dem, på ett säkert sätt, utan att bjuda in organisationen för att manuellt publicera de. Du kan enkelt distribuera projektet till en Azure-prenumeration önskat.

## <a name="as-is-code"></a>Som-kod

Kom ihåg att den här koden görs tillgänglig som ett exempel för att demonstrera användning av Azure Active Directory B2B-inbjudan API. Det bör anpassas av Utvecklingsteamet eller en partner och bör granskas innan de kan distribueras i ett produktionsscenario.

## <a name="next-steps"></a>Nästa steg

Bläddra andra artiklar om Azure AD B2B-samarbete:
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