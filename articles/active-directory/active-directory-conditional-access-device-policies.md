---
title: "Azure Active Directory device principer för villkorlig åtkomst för Office 365-tjänster | Microsoft Docs"
description: "Läs mer om hur du etablerar enhetsprinciper för villkorlig åtkomst för att se företagsresurser mer säkert, samtidigt som användaren efterlevnad och komma åt tjänster."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 8664c0bb-bba1-4012-b321-e9c8363080a0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2017
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 2b586aee9559b62d4cc8b21ab88ab193e61e7c14
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="active-directory-conditional-access-device-policies-for-office-365-services"></a>Active Directory device principer för villkorlig åtkomst för Office 365-tjänster

Villkorlig åtkomst kräver flera uppgifter ska fungera. Det innebär att en Multi-Factor autentiserade användare, en autentiserad enhet och en kompatibel enhet, bland annat. I den här artikeln fokuserar vi främst på enhetsbaserad villkor som din organisation kan använda för att hjälpa dig att styra åtkomsten till Office 365-tjänster. 

Företagsanvändare vill komma åt Office 365-tjänster som Exchange och SharePoint Online på arbetet eller skolan från sina personliga enheter. Du vill komma åt säker. Du kan etablera enhetsprinciper för villkorlig åtkomst för att se företagsresurser säkrare när bevilja åtkomst till tjänster för användare som använder kompatibla enheter. Du kan ange principer för villkorlig åtkomst till Office 365 i Microsoft Intune-portalen för villkorlig åtkomst.

Azure Active Directory (AD Azure) tillämpar principer för villkorlig åtkomst för att skydda åtkomsten till Office 365-tjänster. Du kan skapa en princip för villkorlig åtkomst som blockerar en användare som använder en icke-kompatibla enheter från att komma åt en Office 365-tjänst. Användaren måste följa företagets principer för enheter innan beviljas åtkomst till tjänsten. Alternativt kan du skapa en princip som kräver att användare registrerar sina enheter för att få åtkomst till en Office 365-tjänst. Principer kan tillämpas för alla användare i en organisation eller begränsas till några målgrupper. Du kan lägga till flera målgrupper för en princip med tiden.

En förutsättning för att genomdriva principer för enheter är att användare registrerar sina enheter med Azure AD device registration service. Du kan välja om du vill aktivera multifaktorautentisering för enheter som registreras med Azure AD device registration service. Multifaktorautentisering rekommenderas för enhetsregistreringstjänsten för Azure Active Directory. När multifaktorautentisering aktiveras kan användare registrerar sina enheter med Azure AD device registration service att dirigeras till andra faktor-autentisering.

## <a name="how-does-a-conditional-access-policy-work"></a>Hur fungerar en princip för villkorlig åtkomst?

När en användare begär åtkomst till en Office 365-tjänst från en stödd enhetsplattform, autentiserar Azure AD användaren och enheten. Azure AD ger åtkomst till tjänsten endast om användaren uppfyller principen för tjänsten. På enheter som inte är registrerade instruktioner om hur du registrerar och vara kompatibla för att få åtkomst till företagets Office 365-tjänster. Användare på iOS och Android-enheter krävs för att registrera sina enheter med hjälp av företagsportal-programmet. När en användare registrerar en enhet, enheten är registrerad med Azure AD och den har registrerats för hantering av enheter och efterlevnad. Du måste använda registreringstjänsten för Azure AD-enheter med Microsoft Intune för hantering av mobila enheter för Office 365-tjänster. Registrering av enheter krävs för användare åtkomst till Office 365-tjänster när enhetsprinciper tillämpas.

När en användare har registrerar en enhet, blir enheten betrodda. Azure AD ger autentiserad användare enkel inloggning åtkomst till företagets program. Azure AD tillämpar en princip för villkorlig åtkomst för att bevilja åtkomst till en tjänst inte bara första gången användaren begär åtkomst, men varje gång användaren förnyar en begäran om åtkomst. Användaren nekas åtkomst till tjänster när inloggningsuppgifter ändras, enheten tappas bort eller blir stulen eller villkor för principen är inte uppfyllda vid tidpunkten för begäran om förnyelse.

## <a name="deployment-considerations"></a>Distributionsöverväganden

Du måste använda enhetsregistreringstjänsten för Azure AD för att registrera enheter.

När lokala användare är i färd med att autentiseras, Active Directory Federation Services (AD FS) (version 1.0 och senare) krävs. Multifaktorautentisering för anslutning till arbetsplatsen misslyckas när identitetsleverantören inte kan utföra multifaktorautentisering. Du kan till exempel använda multifaktorautentisering med AD FS 2.0. Se till att lokalt AD FS fungerar med multifaktorautentisering och som en giltig multifaktorautentisering metod är på plats innan du aktiverar multifaktorautentisering i Azure AD för registreringstjänsten för enheten. Till exempel har AD FS i Windows Server 2012 R2 funktioner för multifaktorautentisering. Också måste du ange ytterligare giltig (multifaktorautentisering) en autentiseringsmetod på AD FS-servern innan du aktiverar multifaktorautentisering i Azure AD för registreringstjänsten för enheten. Mer information om metoder stöds multifaktorautentisering i AD FS finns [konfigurera ytterligare autentiseringsmetoder för AD FS](/windows-server/identity/ad-fs/operations/configure-additional-authentication-methods-for-ad-fs).

## <a name="next-steps"></a>Nästa steg

*   Svar på vanliga frågor finns i [villkorlig åtkomst för Azure Active Directory vanliga frågor och svar](active-directory-conditional-faqs.md).
