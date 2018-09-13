---
title: Registrera dig självbetjäningsportalen för Azure Active Directory B2B-samarbete | Microsoft Docs
description: Azure Active Directory B2B-samarbete ger stöd för dina företagsomfattande relationer genom att tilldela affärspartner selektiv åtkomst till dina affärsprogram
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/08/2018
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 94001b005a883c172cab279029b47ac1ad0c0de5
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "35647297"
---
# <a name="self-service-portal-for-azure-ad-b2b-collaboration-sign-up"></a>Självbetjäningsportalen för Azure AD B2B-samarbete registrering

Kunder kan göra mycket med de inbyggda funktionerna som är tillgängliga via den [Azure-portalen](https://portal.azure.com) och [Programåtkomstpanelen](https://myapps.microsoft.com) för slutanvändare. Du kan dock behöva anpassa onboarding-arbetsflöde för B2B-användare så att de passar behoven i din organisation. Du kan göra det med [inbjudan API](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation).

Som en organisationen som bjuder in kan du inte vet förbereds i förväg som enskilda externa medarbetare som behöver åtkomst till dina resurser. Du behöver ett sätt för användare från partnerföretag att registrera sig själva med en uppsättning principer som du som organisationen som bjuder in styr. Det här scenariot är möjlig tack vare API: erna. Det finns en [exempelprojektet på GitHub](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web) som gör just detta.

Den här GitHub-projektet visar hur organisationer kan använda API: erna för att tillhandahålla en registrering funktion för principbaserad, Självbetjäning för dina betrodda partner med reglerna som bestämmer vilka appar som de kan komma åt. Partneranvändare kan få åtkomst till resurser när det behövs. De kan göra detta på ett säkert sätt, utan att organisationen som bjuder in att manuellt registrera de. Du kan enkelt distribuera projektet till en Azure-prenumeration du väljer.

## <a name="as-is-code"></a>Som-är kod

Den här koden är tillgänglig som ett exempel för att demonstrera användningen av Azure Active Directory B2B-inbjudan API. Den bör anpassas av Utvecklingsteamet eller en partner och bör granskas innan du distribuerar den i ett produktionsscenario för.

## <a name="next-steps"></a>Nästa steg

* [Vad är Azure AD B2B-samarbete?](what-is-b2b.md)
* [Azure AD B2B-samarbete och licensiering](licensing-guidance.md)
* [Vanliga frågor (och svar FAQ) om Azure Active Directory B2B-samarbete](faq.md)