---
title: Vad är Azure Active Directory B2B-samarbete? | Microsoft Docs
description: Azure Active Directory B2B-samarbete har stöd för dina företagsomfattande relationer genom att tilldela affärspartner selektiv åtkomst till företagets program.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 04/26/2018
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 51a969ae215583a0be8d75ff1de11173e0696a22
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "35649260"
---
# <a name="what-is-azure-ad-b2b-collaboration"></a>Vad är Azure AD B2B-samarbete?

Azure Active Directory (Azure AD) business-to-business (B2B) samarbetsfunktionerna att alla organisationer som använder Azure AD ska fungera på ett säkert sätt och på ett säkert sätt med användare från någon annan organisation, små eller stora. Dessa organisationer kan vara med eller utan Azure AD och behöver även inte ha en IT-avdelning.

Organisationer som använder Azure AD kan ge åtkomst till dokument, resurser och program till sina partners, samtidigt som de behåller fullständig kontroll över sina egna företagets data. Utvecklare kan använda Azure AD business-to-business API: er att skriva program som sammanför två organisationer mer på ett säkert sätt. Dessutom är det enkelt för användarna att navigera.

Följande videoklipp ger en bra översikt.
>[!VIDEO https://www.youtube.com/embed/AhwrweCBdsc]

## <a name="key-benefits-of-azure-ad-b2b-collaboration"></a>Viktiga fördelar med Azure AD B2B-samarbete

### <a name="work-with-any-user-from-any-partner"></a>Arbeta med alla användare från någon part

- Partner använda sina egna autentiseringsuppgifter
- Inga krav för partner att använda Azure AD
- Inga externa kataloger eller komplex konfiguration som krävs

### <a name="simple-and-secure-collaboration"></a>Enkelt och säkert samarbete

- Ge åtkomst till företagets app eller data, samtidigt som du använder avancerade, Azure AD-baserade auktoriseringsprinciper
- Enkelt för användarna
- Säkerhet i företagsklass för appar och data

### <a name="no-management-overhead"></a>Inga hanteringskostnader för

- Ingen extern hantering av konto eller lösenord
- Ingen synkronisering eller manuell Livscykelhantering för konto
- Inga externa administrativa kostnader

## <a name="easily-add-b2b-collaboration-users"></a>Lägg enkelt till användare i B2B-samarbetet

Som administratör kan du kan enkelt lägga till användare i B2B-samarbetet (Gäst) för din organisation i den [Azure-portalen](https://portal.azure.com).

![lägga till gästanvändare](media/what-is-b2b/adding-b2b-users-admin.png)

### <a name="enable-your-collaborators-to-bring-their-own-identity"></a>Aktivera dina medarbetare att ta med sin egen identitet

B2B-medarbetare kan logga in med en identitet som helst. Om användaren inte har ett Microsoft-konto eller ett Azure AD-konto – skapas ett för dem sömlöst vid tidpunkten för inlösen av erbjudandet.

### <a name="delegate-to-application-and-group-owners"></a>Delegera till programmet och gruppägare

Som ett program eller en gruppägare, kan du lägga till B2B-användare direkt till alla program som du bryr dig om, oavsett om det är ett Microsoft-program eller inte. Administratörer kan delegera behörighet att lägga till B2B-användare till icke-administratörer. Icke-administratörer kan använda den [Azure AD-Programåtkomstpanelen](https://myapps.microsoft.com) att lägga till användare i B2B-samarbetet i program eller grupper.

![Åtkomstpanelen](media/what-is-b2b/access-panel.png)

![Lägg till medlem](media/what-is-b2b/add-member.png)

### <a name="authorization-policies-protect-your-corporate-content"></a>Auktoriseringsprinciper skyddar företagets innehållet

Principer för villkorlig åtkomst som multifaktorautentisering, kan tillämpas:
- På klientnivån
- På programnivå
- För specifika användare att skydda företagets appar och data

### <a name="use-apis-and-sample-code-to-easily-build-applications-to-onboard"></a>Använd API: er och exempelkoden för att enkelt bygga program för att publicera

Ta med din externa partners ombord på sätt som anpassas efter din organisations behov.

Använd den [B2B-samarbetsinbjudan API: er](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation) att anpassa din onboarding inträffar, inklusive att skapa registrering självbetjäningsportaler. Vi ger exempelkod för en självbetjäningsportal [på Github](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web).

![registreringsportalen](media/what-is-b2b/sign-up-portal.png)

Med Azure AD B2B-samarbete får du full nytta av Azure AD att skydda dina partnerrelationer på ett sätt som slutanvändarna hitta enkelt och intuitivt.

## <a name="next-steps"></a>Nästa steg

- [Hur lägger Azure Active Directory-administratörer till B2B-samarbetare?](add-users-administrator.md)
- [Hur lägger informationsarbetare till användare i B2B-samarbetet?](add-users-information-worker.md)
- [B2B-samarbete inlösning av inbjudan](redemption-experience.md)
- [Azure AD B2B-samarbete och licensiering](licensing-guidance.md)
- Och som alltid kan ansluta med produktteam för någon feedback och diskussioner förslag via vår [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b).