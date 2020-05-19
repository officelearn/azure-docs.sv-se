---
title: Identitets leverantörer för externa identiteter – Azure AD
description: Azure Active Directory B2B-samarbete stöder Multi-Factor Authentication (MFA) för selektiv åtkomst till företagets program
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 395473e70137ead2b7185d54d165f82e9b3f1f04
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83598152"
---
# <a name="identity-providers-for-external-identities"></a>Identitets leverantörer för externa identiteter

En *identitetsprovider* skapar, underhåller och hanterar identitetsinformation samt autentiseringstjänster till program. När du delar dina appar och resurser med externa användare är Azure AD standard identitets leverantören för delning. Det innebär att när du bjuder in externa användare som redan har en Azure AD eller Microsoft-konto, kan de automatiskt logga in utan ytterligare konfiguration på din sida.

Du kan dock göra det möjligt för användarna att logga in med olika identitets leverantörer. Du kan till exempel konfigurera Federation med sociala identitets leverantörer som stöds av Azure AD, inklusive Google och Facebook. Du kan också federera med en extern identitetsprovider som stöder SAML-eller WS-utfodras protokoll. Med extern Identity Provider Federation kan du erbjuda externa användare möjlighet att logga in på dina appar med sina befintliga sociala konton eller företags konton.

## <a name="how-it-works"></a>Så här fungerar det

Externa Azure AD-identiteter är förkonfigurerade för federation med Google och Facebook. Om du vill konfigurera dessa identitets leverantörer i din Azure AD-klient skapar du ett program hos varje identitets leverantör och konfigurerar autentiseringsuppgifter. Du får ett klient-eller app-ID och en klient-eller app-hemlighet, som du sedan kan lägga till i din Azure AD-klient.

När du har lagt till en identitetsprovider till din Azure AD-klient:

- När du bjuder in en extern användare till appar eller resurser i organisationen kan den externa användaren logga in med sitt eget konto hos den identitets leverantören.
- När du aktiverar [självbetjänings registrering](self-service-sign-up-overview.md) för dina appar kan externa användare registrera sig för dina appar med hjälp av sina egna konton med de identitets leverantörer som du har lagt till. 

När du löser in din inbjudan eller registrerar dig för din app, har den externa användaren möjlighet att logga in och autentisera med den sociala identitets leverantören:

![Skärm bild som visar inloggnings skärmen med Google-och Facebook-alternativ](media/identity-providers/sign-in-with-social-identity.png)

För en optimal inloggnings upplevelse kan du federera med identitets leverantörer närhelst det är möjligt, så att du kan ge dina inbjudna gäster en sömlös inloggnings upplevelse när de kommer åt dina appar.  

## <a name="next-steps"></a>Nästa steg

Information om hur du lägger till identitets leverantörer för inloggning till dina program finns i följande artiklar:

- [Lägg till Google](google-federation.md) i din lista över sociala identitets leverantörer
- [Lägg till Facebook](facebook-federation.md) i listan över sociala identitets leverantörer
- [Konfigurera direkt Federation](direct-federation.md) med en organisation vars IDENTITETSPROVIDER stöder SAML 2,0 eller WS-utfodras protokoll. Observera att direkt Federation inte är ett alternativ för självbetjänings registrering av användar flöden.
