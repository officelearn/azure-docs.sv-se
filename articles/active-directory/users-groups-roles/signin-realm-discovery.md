---
title: Användarnamn lookup under autentiseringen inloggning – Azure Active Directory | Microsoft Docs
description: Hur anvisningarna på skärmen meddelanden visar användarnamn lookup under inloggning
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/03/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 91d0dbaf9b648f42ef535d8b491df04b2c7042d7
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/04/2019
ms.locfileid: "59007631"
---
# <a name="home-realm-discovery-during-sign-in-for-microsoft-365-services"></a>Identifiering av startsfär under inloggning för Microsoft 365-tjänster

Ändrar vi vår Azure Active Directory (Azure AD)-inloggningen beteende för att göra plats för nya autentiseringsmetoder och användbarhet. Azure AD anger under inloggning, där en användare måste autentisera. Azure AD gör smarta beslut genom att läsa organisationen och inställningar för användarnamnet som angetts på sidan logga in. Det här är ett steg mot ett lösenord är kostnadsfria framtida som gör att ytterligare autentiseringsuppgifter som FIDO 2.0.

## <a name="home-realm-discovery-behavior"></a>Beteende för identifiering av startsfär

Historiskt sett har identifiering av startsfär regleras av den domän som har angetts vid inloggning eller en princip för Home identifieringen av Startsfären för vissa äldre program. Exempelvis kan skriver sitt användarnamn en Azure Active Directory-användare i den gamla discovery-funktionen men fortfarande skulle når sin organisations autentiseringsuppgifter samling skärmen. Detta inträffar när användaren ger korrekt organisationens domän namnet ”contoso.com”. Det här beteendet tillåter inte granularitet för att anpassa upplevelser för en enskild användare.

För att stödja ett bredare spektrum av autentiseringsuppgifter och öka användbarheten, uppdateras nu Azure Active Directorys användarnamn lookup beteende under inloggningen. Det nya beteendet gör smarta beslut genom att läsa klient och användare på inställningar baserat på användarnamnet som angetts på sidan logga in. Om du vill göra detta möjligt, ska Azure Active Directory kontrollera om det användarnamn som anges på sidan logga in finns i deras angivna domänen eller omdirigerar användaren för att ange sina autentiseringsuppgifter. 

En annan fördel av detta arbete är förbättrad fel meddelanden. Här följer några exempel på förbättrade felmeddelanden när du loggar in till ett program som stöder Azure Active Directory-användare.

1. Användarnamnet är felstavad eller användarnamnet har ännu inte synkroniserats till Azure AD:
  
    ![användarnamnet är felskrivet eller hittades inte](./media/signin-realm-discovery/typo-username.png)
  
2. Domännamnet är felstavad:
  
    ![domännamnet är felskrivet eller hittades inte](./media/signin-realm-discovery/typo-domain.png)
  
3. Användaren försöker att logga in med en känd konsumentdomän:
  
    ![logga in med en känd konsumentdomän](./media/signin-realm-discovery/consumer-domain.png)
  
4. Lösenordet är felstavad men användarnamnet stämmer:  
  
    ![lösenordet är fel med bra användarnamn](./media/signin-realm-discovery/incorrect-password.png)
  
## <a name="additional-info"></a>Ytterligare information

Förutom förbättrad inloggning användarupplevelsen innehåller den här ändringen mekanismer som kan minimera missbruk av storskaliga användarnamn uppräkning.

Den här ändringen är ursprungligen avsett för hanterade domäner och börjar lanseras i maj 2019, men börjar inte distribueras till federerade domäner i slutet av 2019. De exakta lansering datum för federerade domäner beror på kundfeedback.

> [!IMPORTANT]
> Den här funktionen kommer att ha stor inverkan på federerade domäner som förlitar sig på gamla domännivå identifiering av startsfär att tvinga federation. Vissa organisationer har tränats sina anställda att logga in med ett användarnamn som inte finns i Azure Active Directory men innehåller rätt domännamn, eftersom domännamnen dirigerar användare för närvarande till organisationens domän slutpunkt. Det nya beteendet inloggning tillåter inte detta. Användaren meddelas att rätta användarnamnet och de tillåts inte att logga in med ett användarnamn som inte finns i Azure Active Directory.
>
> Om du eller din organisation har metoder som är beroende av den gamla funktionen, är det viktigt för organisationens administratörer uppdatera dokumentationen för inloggning och autentisering av medarbetare och träna anställda använder sina Azure Active Directory-användarnamn för inloggning.
  
Om du har problem med det nya beteendet kan du lämna dina kommentarer i den **Feedback** i den här artikeln.  

## <a name="next-steps"></a>Nästa steg

[Anpassa din inloggning profilering](../fundamentals/add-custom-domain.md)