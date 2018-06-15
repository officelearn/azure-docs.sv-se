---
title: Registrering självbetjäningsportalen för Azure Active Directory B2B-samarbete | Microsoft Docs
description: Azure Active Directory B2B-samarbete ger stöd för dina företagsomfattande relationer genom att tilldela affärspartner selektiv åtkomst till dina affärsprogram
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/08/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: b16f7c040212db6d0dbeb7161a18f205cf524090
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
ms.locfileid: "33930443"
---
# <a name="self-service-portal-for-azure-ad-b2b-collaboration-sign-up"></a>Självbetjäningsportalen för Azure AD B2B-samarbete registrering

Kunder kan göra mycket med inbyggda funktioner som är tillgängliga via den [Azure-portalen](https://portal.azure.com) och [programmet åtkomstpanelen](https://myapps.microsoft.com) för slutanvändare. Du kan behöva anpassa onboarding arbetsflödet för B2B-användare så att de passar din organisations behov. Du kan göra det med [inbjudan API](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation).

Som en bjuda in organisation kan du inte vet i förväg som enskilda externa samarbetspartners som behöver åtkomst till resurser. Du behöver ett sätt för användare från partnerföretag registrera sig själva med en uppsättning principer som du som bjuda in organisationen styr. Det här scenariot är möjligt via API: erna. Det finns en [exempelprojektet på GitHub](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web) som har precis så.

Det här projektet i GitHub visar hur organisationer kan använda API: erna för att ge en anmälan funktioner för principbaserad, Självbetjäning för dina betrodda partners med regler som styr appar som de kan komma åt. Partneranvändarna kan få åtkomst till resurser när de behöver dem. De kan göra detta på ett säkert sätt, utan att bjuda in organisationen för att manuellt publicera de. Du kan enkelt distribuera projektet till en Azure-prenumeration önskat.

## <a name="as-is-code"></a>Som-kod

Den här koden görs tillgänglig som ett exempel för att demonstrera användning av Azure Active Directory B2B-inbjudan API. Det bör anpassas av Utvecklingsteamet eller en partner och bör granskas innan du distribuerar den i ett produktionsscenario.

## <a name="next-steps"></a>Nästa steg

* [Vad är Azure AD B2B-samarbete?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Azure AD B2B-samarbete och licensiering](active-directory-b2b-licensing.md)
* [Vanliga och frågor svar om Azure Active Directory B2B-samarbete](active-directory-b2b-faq.md)