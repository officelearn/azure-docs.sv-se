---
title: Vad är Azure Active Directory B2B-samarbete? | Microsoft Docs
description: Azure Active Directory B2B-samarbete stöder gästanvändaråtkomst så att du på ett säkert sätt kan dela resurser och samarbeta med externa partner.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 09/14/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: mal
ms.openlocfilehash: d8aecb4dd8c680c876cf43f6c0bcbe1a4f911f41
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2019
ms.locfileid: "55082120"
---
# <a name="what-is-guest-user-access-in-azure-active-directory-b2b"></a>Vad är gästanvändaråtkomst i Azure Active Directory B2B?

Med samarbete i Azure Active Directory (Azure AD) B2B (företag till företag) kan du på ett säkert sätt dela företagets program och tjänster med gästanvändare från valfri organisation, samtidigt som du behåller kontrollen över företagets egna data. Arbeta tryggt och säkert med externa partners, stora som små, även om de inte har Azure AD eller någon IT-avdelning. Tack vare en enkel process för inbjudan och inlösen kan dessa partners använda sina egna autentiseringsuppgifter för att få åtkomst till företagets resurser. Utvecklare kan använda Azure AD B2Bs API:er för att anpassa inbjudningsprocessen eller skriva program som självbetjäningsportaler för registrering.

Titta på videon och lär dig hur du på ett säkert sätt kan samarbeta med gästanvändare genom att bjuda in dem till att logga in på företagets appar och tjänster med hjälp av sina egna identiteter.

Följande videoklipp ger en bra översikt.

>[!VIDEO https://www.youtube.com/embed/AhwrweCBdsc]

## <a name="collaborate-with-any-partner-using-their-identities"></a>Samarbeta med alla partners med hjälp av deras identiteter
Med Azure AD B2B använder dina partners sina egna lösningar för identitetshantering, vilket innebär att inte tillkommer några externa administrativa kostnader för din organisation. 
- Dina partners använder sina egna identiteter och autentiseringsuppgifter. Azure AD är inte obligatoriskt. 
- Du behöver inte hantera externa konton eller lösenord. 
- Du behöver inte synkronisera konton eller hantera kontolivscykler.  

![lägg till medlem](media/what-is-b2b/add-member.png)

## <a name="invite-guest-users-with-a-simple-invitation-and-redemption-process"></a>Bjud in gästanvändare med en enkel process för inbjudan och inlösen
Gästanvändarna loggar in på dina appar och tjänster med sina egna arbets- eller skolidentiteter eller sociala identiteter. Om en gästanvändare inte har något Microsoft-konto eller ett Azure AD-konto skapas ett när användaren löser in sin inbjudan. 
- Bjud in gästanvändare med valfri e-postidentitet.
- Skicka en direktlänk till en app eller skicka en inbjudan till gästanvändaren egen åtkomstpanel. 
- Gästanvändarna loggar in genom några enkla inlösningssteg.

![åtkomstpanel](media/what-is-b2b/consentscreen.png)

## <a name="use-policies-to-securely-share-your-apps-and-services"></a>Använda principer för att på ett säkert sätt dela dina appar och tjänster
Du kan använda auktoriseringsprinciper för att skydda företagets innehåll. Principer för villkorsstyrd åtkomst, som multifaktorautentisering, kan tillämpas:
- På klientorganisationsnivå.
- På programnivå.
- För specifika gästanvändare för att skydda företagets appar och data.

![lägga till gästanvändare](media/what-is-b2b/tutorial-mfa-policy-2.png)


## <a name="easily-add-guest-users-in-the-azure-ad-portal"></a>Lägg enkelt till gästanvändare i Azure AD-portalen

Som administratör kan du enkelt lägga till gästanvändare för din organisation i Azure Portal.
- Skapa en ny gästanvändare i Azure AD på liknande sätt som du lägger till en ny användare.
- Gästanvändaren får direkt en anpassningsbar inbjudan som låter hen logga in på sin åtkomstpanel.
- Gästanvändare i katalogen kan tilldelas appar eller grupper.  

![lägga till gästanvändare](media/what-is-b2b/adding-b2b-users-admin.png)

## <a name="let-application-and-group-owners-manage-their-own-guest-users"></a>Låt program och gruppägare hantera sina egna gästanvändare

Du kan delegera hanteringen av gästanvändarna till programägarna, så att de kan lägga till gästanvändare direkt till de program de vill dela, oavsett om det är ett Microsoft-program eller inte. 
 - Administratörer konfigurerar självbetjäningapp och grupphantering.
 - Icke-administratörer använda sina [åtkomstpanelet](https://myapps.microsoft.com) för att lägga till gästanvändare till program eller grupper.

![lägga till gästanvändare](media/what-is-b2b/access-panel-manage-app.png)

## <a name="use-apis-and-sample-code-to-easily-build-applications-to-onboard"></a>Använd API:er och exempelkod för att enkelt bygga program att publicera

Integrera dina externa partners så passar din organisations behov.
- Använd [API:erna för B2B-samarbetsinbjudan](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation) för att anpassa integreringen, t.ex. genom att skapa självbetjäningsportaler för registrering. 
- Använd den exempelkod som vi tillhandahåller för en självbetjäningsportal [på GitHub](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web).

![registreringsportal](media/what-is-b2b/sign-up-portal.png)

## <a name="next-steps"></a>Nästa steg

- [Licensieringsguide för Azure AD B2B-samarbete](licensing-guidance.md)
- [Lägg till gästanvändare för B2B-samarbete i portalen](add-users-administrator.md)
- [Förstå inlösningsprocessen för inbjudningar](redemption-experience.md)
- Och som alltid kan du kontakta produktgruppen med eventuell feedback och förslag via vår [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b).
