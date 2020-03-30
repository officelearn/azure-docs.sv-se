---
title: Sökning av användarnamn under inloggning - Azure Active Directory | Microsoft-dokument
description: Så här återspeglar meddelanden på skärmen uppslag på användarnamn under inloggning i Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8b6a65a964016f702fcf75aa4cbdab33a952e3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74024258"
---
# <a name="home-realm-discovery-for-azure-active-directory-sign-in-pages"></a>Identifiering av hemsfär för inloggningssidor i Azure Active Directory

Vi ändrar vårt inloggningsbeteende för Azure Active Directory (Azure AD) för att ge plats åt nya autentiseringsmetoder och förbättra användbarheten. Under inloggningen avgör Azure AD var en användare behöver autentisera. Azure AD fattar intelligenta beslut genom att läsa organisations- och användarinställningar för användarnamnet som anges på inloggningssidan. Detta är ett steg mot en lösenordsfri framtid som möjliggör ytterligare autentiseringsuppgifter som FIDO 2.0.

## <a name="home-realm-discovery-behavior"></a>Beteende för upptäckt av hemsfär

Historiskt sett styrdes identifiering av hemsfärer av domänen som tillhandahålls vid inloggning eller av en Home Realm Discovery-princip för vissa äldre program. I vårt identifieringsbeteende kan till exempel en Azure Active Directory-användare skriva fel användarnamn men ändå komma fram till organisationens samlingsskärm för autentiseringsuppgifter. Detta inträffar när användaren korrekt anger organisationens domännamn "contoso.com". Med det här beteendet kan inte granulariteten anpassa upplevelser för en enskild användare.

Azure Active Directorys beteende för användarnamn under inloggningsprocessen uppdateras för att stödja ett bredare utbud av autentiseringsuppgifter och öka användbarheten. Det nya beteendet fattar intelligenta beslut genom att läsa inställningar för klient- och användarnivå baserat på användarnamnet som anges på inloggningssidan. För att göra detta möjligt kommer Azure Active Directory att kontrollera om användarnamnet som anges på inloggningssidan finns i deras angivna domän eller omdirigerar användaren för att ange sina autentiseringsuppgifter.

Ytterligare en fördel med detta arbete är förbättrade felmeddelanden. Här är några exempel på förbättrade felmeddelanden när du loggar in på ett program som endast stöder Azure Active Directory-användare.

- Användarnamnet är felskriven eller användarnamnet har ännu inte synkroniserats till Azure AD:
  
    ![användarnamnet är felskriven eller hittades inte](./media/signin-realm-discovery/typo-username.png)
  
- Domännamnet är felskrivningat:
  
    ![domännamnet är felskriven eller hittades inte](./media/signin-realm-discovery/typo-domain.png)
  
- Användaren försöker logga in med en känd konsumentdomän:
  
    ![logga in med en känd konsumentdomän](./media/signin-realm-discovery/consumer-domain.png)
  
- Lösenordet är felskriven men användarnamnet är korrekt:  
  
    ![lösenord är felskriven med bra användarnamn](./media/signin-realm-discovery/incorrect-password.png)
  
> [!IMPORTANT]
> Den här funktionen kan påverka federerade domäner som förlitar sig på den gamla home realm-identifieringen på domännivå för att tvinga federationen. Uppdateringar om när federerad domänsupport läggs till finns i [Identifiering av Hemsfär under inloggning för Microsoft 365-tjänster](https://azure.microsoft.com/updates/signin-hrd/). Under tiden har vissa organisationer utbildat sina anställda att logga in med ett användarnamn som inte finns i Azure Active Directory men som innehåller rätt domännamn, eftersom domännamnet dirigerar användare som för närvarande till organisationens domänslutpunkt. Det nya inloggningsbeteendet tillåter inte detta. Användaren meddelas för att korrigera användarnamnet och de får inte logga in med ett användarnamn som inte finns i Azure Active Directory.
>
> Om du eller din organisation har metoder som är beroende av det gamla beteendet är det viktigt för organisationsadministratörer att uppdatera dokumentationen för medarbetares inloggning och autentisering och att utbilda anställda att använda sitt Azure Active Directory-användarnamn för att logga in.
  
Om du har problem med det nya beteendet lämnar du dina kommentarer i avsnittet **Feedback** i den här artikeln.  

## <a name="next-steps"></a>Nästa steg

[Anpassa ditt inloggningsmärke](../fundamentals/add-custom-domain.md)
