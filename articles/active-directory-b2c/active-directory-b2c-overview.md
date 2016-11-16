---
title: "Azure Active Directory B2C: Översikt | Microsoft Docs"
description: Utveckla konsumentinriktade program med Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: c465dbde-f800-4f2e-8814-0ff5f5dae610
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 07/24/2016
ms.author: swkrish
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 217ffc63e07d66de522accc42c246125d01713c8


---
# <a name="azure-active-directory-b2c-sign-up-and-sign-in-consumers-in-your-applications"></a>Azure Active Directory B2C: Registrera och logga in användare i dina program
Azure Active Directory B2C är en heltäckande lösning för identitetshantering i molnet för konsumentinriktade webb- och mobilprogram. Det är en global tjänst med hög tillgänglighet som kan skalas till flera hundra miljoner konsumentidentiteter. Azure Active Directory B2C bygger på en säker plattform i företagsklass och ser till att dina program, ditt företag och dina användare är skyddade.

Tidigare var programutvecklare som ville registrera och logga in användare i sina program tvungna att skriva egen kod. Och de använde lokala databaser eller system för att lagra användarnamn och lösenord. Azure Active Directory B2C erbjuder utvecklare ett bättre sätt att integrera identitetshanteringen för användare i deras program med hjälp av en säker, standardbaserad plattform och en omfattande uppsättning utökningsbara principer. När du använder Azure Active Directory B2C kan dina användare registrera sig för dina program genom att använda sina befintliga sociala konton (Facebook, Google, Amazon, LinkedIn osv.) eller genom att skapa nya autentiseringsuppgifter (e-postadress och lösenord eller användarnamn och lösenord). Det sistnämnda kallar vi för ”lokala konton”.

## <a name="get-started"></a>Kom igång
Om du vill skapa ett program som stöder registrering och inloggning av användare måste du först registrera programmet med en Azure Active Directory B2C-klient. Skaffa en egen klient genom att följa stegen i [Skapa en Azure AD B2C-klient](active-directory-b2c-get-started.md).

Du kan skriva ditt program mot Azure Active Directory B2C-tjänsten antingen genom att skicka protokollmeddelanden direkt, med hjälp av [OAuth 2.0 eller Öppna ID Connect](active-directory-b2c-reference-protocols.md), eller genom att använda våra bibliotek och låta dem göra arbetet åt dig. Välj din favoritplattform i följande tabell och kom igång.

[!INCLUDE [active-directory-b2c-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]

## <a name="whats-new"></a>Nyheter
Kom tillbaka ofta om du är nyfiken på framtida förändringar i Azure Active Directory B2C. Vi kommer även att twittra om eventuella uppdateringar med @AzureAD.

* Lär dig mer om vårt [expanderbara principramverk](active-directory-b2c-reference-policies.md) och om vilka typer av principer som du kan skapa och använda i dina program.
* Spara vår [serviceblogg](https://blogs.msdn.microsoft.com/azureadb2c/) som ett bokmärke för att få meddelanden om mindre serviceproblem, uppdateringar, status och åtgärder. Fortsätt också att övervaka [instrumentpanelen för Azure-status](https://azure.microsoft.com/status/).
* Aktuella [begränsningar, restriktioner och villkor för tjänsten](active-directory-b2c-limitations.md).
* Slutligen ett [kodexempel](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-b2c) med Azure AD B2C & ASP.NET Core.

## <a name="howto-articles"></a>Instruktionsartiklar
Lär dig hur du använder specifika funktioner i Azure Active Directory B2C:

* Konfigurera [Facebook-](active-directory-b2c-setup-fb-app.md), [Google+-](active-directory-b2c-setup-goog-app.md), [Microsoft-](active-directory-b2c-setup-msa-app.md), [Amazon-](active-directory-b2c-setup-amzn-app.md) och [LinkedIn-](active-directory-b2c-setup-li-app.md)konton för användning i dina konsumentinriktade program.
* [Använd anpassade attribut för att samla in information om dina användare](active-directory-b2c-reference-custom-attr.md).
* [Aktivera Azure Multi-Factor Authentication i dina konsumentinriktade program](active-directory-b2c-reference-mfa.md).
* [Konfigurera lösenordsåterställning via självbetjäning för dina användare](active-directory-b2c-reference-sspr.md).
* [Anpassa utseende på registrerings- och inloggningssidorna och på andra konsumentinriktade sidor](active-directory-b2c-reference-ui-customization.md) som hanteras av Azure Active Directory B2C.
* [Använd Azure Active Directory Graph API för att skapa, läsa, uppdatera och ta bort användare via programmering](active-directory-b2c-devquickstarts-graph-dotnet.md) i din Azure Active Directory B2C-klient.

## <a name="next-steps"></a>Nästa steg
Använd dessa länkar om du vill utforska tjänsten mer i detalj:

* Ta del av [Azure Active Directory B2C-prisinformation](https://azure.microsoft.com/pricing/details/active-directory-b2c/).
* Få hjälp på Stack Overflow genom att använda taggarna [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) eller [adal](http://stackoverflow.com/questions/tagged/adal).
* Lämna feedback via [User Voice](https://feedback.azure.com/forums/169401-azure-active-directory/) – vi vill veta vad du tycker! Använd frasen ”AzureADB2C:” i rubriken på ditt inlägg så att vi kan hitta den.
* Granska [protokollreferens för Azure AD B2C](active-directory-b2c-reference-protocols.md).
* Granska [tokenreferens för Azure AD B2C](active-directory-b2c-reference-tokens.md).
* Läs [vanliga frågor och svar om Azure Active Directory B2C](active-directory-b2c-faqs.md).
* [Skapa supportförfrågningar för Azure Active Directory B2C](active-directory-b2c-support.md).

## <a name="get-security-updates-for-our-products"></a>Hämta säkerhetsuppdateringar för våra produkter
Vi rekommenderar att du aktiverar aviseringar om säkerhetsincidenter genom att gå till [den här sidan](https://technet.microsoft.com/security/dd252948) och prenumerera på Microsoft Security Advisory-aviseringar.




<!--HONumber=Nov16_HO2-->


