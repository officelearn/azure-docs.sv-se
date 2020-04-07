---
title: Autentisering
description: Förklarar hur autentisering fungerar
author: florianborn71
ms.author: flborn
ms.date: 02/12/2019
ms.topic: how-to
ms.openlocfilehash: fbc27f2fa3b7517151c4bcdbec5b146e83dd868e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681641"
---
# <a name="configure-authentication"></a>Konfigurera autentisering

Azure Remote Rendering använder samma autentiseringsmekanism som [Azure Spatial Anchors (ASA)](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp). Klienter måste ange *AccountKey,* *AuthenticationToken*eller *AccessToken* för att anropa REST-API:erna. *AccountKey* kan erhållas på fliken "Nycklar" för fjärr renderingskontot på Azure-portalen. *AuthenticationToken* är en Azure AD-token som kan erhållas med hjälp av [ADAL-biblioteket](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries). *AccessToken* är en MR-token som kan erhållas från Azure Mixed Reality Security Token Service (STS).

## <a name="authentication-for-deployed-applications"></a>Autentisering för distribuerade program

 Användning av kontonycklar rekommenderas för snabb ombordstigning, men endast under utveckling/prototyper. Vi rekommenderar starkt att du inte levererar ditt program till produktion med hjälp av en inbäddad kontonyckel i det och i stället använder de användarbaserade eller tjänstbaserade Azure AD-autentiseringsmetoderna.

 Azure AD-autentisering beskrivs i azure [AD-användarautentiseringsavsnittet](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp#azure-ad-user-authentication) i [ASA-tjänsten (Azure Spatial Anchors).](https://docs.microsoft.com/azure/spatial-anchors/)

## <a name="role-based-access-control"></a>Rollbaserad åtkomstkontroll

För att kontrollera åtkomstnivån för program, tjänster eller Azure AD-användare av din tjänst har följande roller skapats för att du ska kunna tilldela efter behov mot dina Azure Remote Rendering-konton:

* **Fjärr renderingsadministratör:** Ger användaren funktioner för konvertering, hantering av sessioner, rendering och diagnostik för Azure Remote Rendering.
* **Fjärrrendenklient:** Ger användaren funktioner för hantering av sessions-, renderings- och diagnostikfunktioner för Azure Remote Rendering.

## <a name="next-steps"></a>Nästa steg

* [Skapa ett konto](create-an-account.md)
* [Använda Azure Frontend API:er för autentisering](frontend-apis.md)
* [Exempel på PowerShell-skript](../samples/powershell-example-scripts.md)
