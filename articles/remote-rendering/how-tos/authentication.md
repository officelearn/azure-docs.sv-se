---
title: Autentisering
description: Förklarar hur autentisering fungerar
author: florianborn71
ms.author: flborn
ms.date: 02/12/2019
ms.topic: how-to
ms.openlocfilehash: fbc27f2fa3b7517151c4bcdbec5b146e83dd868e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681641"
---
# <a name="configure-authentication"></a>Konfigurera autentisering

Azure fjärrrendering använder samma autentiseringsmekanism som [Azure spatiala ankare (ASA)](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp). Klienterna måste ange *AccountKey*, *AuthenticationToken*eller *AccessToken* för att anropa REST-API: erna. *AccountKey* kan hämtas på fliken "nycklar" för det fjärranslutna åter givnings kontot på Azure Portal. *AuthenticationToken* är en Azure AD-token som kan hämtas med hjälp av [ADAL-biblioteket](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries). *AccessToken* är en Mr-token som kan hämtas från Azure Mixed Reality Security Token Service (STS).

## <a name="authentication-for-deployed-applications"></a>Autentisering för distribuerade program

 Användning av konto nycklar rekommenderas för snabb registrering, men under utveckling/prototypering. Vi rekommenderar starkt att du inte levererar ditt program till produktion med hjälp av en inbäddad konto nyckel, och i stället använder du användar-eller tjänstebaserade Azure AD-autentisering.

 Azure AD-autentisering beskrivs i avsnittet [Azure AD-användarautentisering](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp#azure-ad-user-authentication) i tjänsten [Azure spatial ankare (ASA)](https://docs.microsoft.com/azure/spatial-anchors/) .

## <a name="role-based-access-control"></a>Rollbaserad åtkomstkontroll

För att hjälpa till att kontrol lera åtkomst nivån för program, tjänster eller Azure AD-användare av tjänsten har följande roller skapats så att du kan tilldela efter behov mot dina Azure Remote rendering-konton:

* **Administratören för fjärran sluten åter givning**: ger användare med konvertering, hantering av sessioner, åter givning och diagnostik för Azure-fjärrrendering.
* **Fjärran sluten klient**: ger användaren möjlighet att hantera sessioner, åter givning och diagnostik för Azure-fjärrrendering.

## <a name="next-steps"></a>Nästa steg

* [Skapa ett konto](create-an-account.md)
* [Använda Azure-frontend-API: er för autentisering](frontend-apis.md)
* [PowerShell-exempelskript](../samples/powershell-example-scripts.md)
