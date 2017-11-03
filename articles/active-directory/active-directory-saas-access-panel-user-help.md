---
title: "Behöver du hjälp med portalen Mina appar i Azure Active Directory | Microsoft Docs"
description: "Hämta anvisningarna för att utföra vanliga uppgifter när du arbetar med åtkomstpanelen."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: c67cd675-b567-41e1-8bc2-e06fe0b38d3b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/21/2017
ms.author: markvi
ms.reviewer: japere
ms.openlocfilehash: 9e2f8aa6ad7534ff822907285aa9fe290a4db586
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2017
---
# <a name="do-you-need-help-with-the-my-apps-portal"></a>Behöver du hjälp med Mina appar portal?

Du har troligtvis nått den här sidan eftersom du körde tyvärr ett problem när du använder portalen Mina appar. Det finns fall som du behöver kontakta supportavdelningen eller administratören om du vill hämta en problem löst, är här vissa felsökning avsnitt som kan vara till hjälp först.

## <a name="i-am-having-trouble-signing-into-the-my-apps-portal"></a>Jag har problem med att logga in på portalen Mina appar

Allmänna problem med att kontrollera:

- Kontrollera om du loggar in rätt URL: [https://myapps.microsoft.com](https://myapps.microsoft.com)

- Försök att lägga till URL: en i webbläsarens betrodda platser.

- Kontrollera att lösenordet inte har upphört att gälla eller har glömt. Kontrollera [här](active-directory-passwords-update-your-own-password.md) för mer information om hur du uppdaterar lösenordet.

- Kontrollera om din kontaktinformation för autentisering är uppdaterade och inte blockerar åtkomsten. Kontrollera [här](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/end-user/multi-factor-authentication-end-user) för mer information om hur du konfigurerar autentisering-information.

- Prova att rensa cookies i webbläsaren och försök sedan logga in igen.

Om det fortfarande uppstår problem vid försök att logga in, kontaktar du administratören för ytterligare hjälp.


## <a name="how-do-i-update-my-password"></a>Hur uppdaterar lösenordet?

Om du har glömt ditt lösenord aldrig något från din IT-personal, har låsts ute från ditt konto eller vill ändra det, se [hjälp, jag har glömt mitt Azure AD-lösenord](active-directory-passwords-update-your-own-password.md) för mer information.

## <a name="how-do-i-register-for-password-reset"></a>Hur registrerar jag för återställning av lösenord

Du kan återställa ditt lösenord eller låsa upp ditt konto utan att behöva tala med en person som använder lösenordsåterställning via självbetjäning (SSPR) som en slutanvändare. Innan du kan använda den här funktionen, måste du registrera autentiseringsmetoder eller bekräfta de fördefinierade autentiseringsmetoderna som administratören har fyllt i. Mer information finns i [registrera dig för lösenordsåterställning via självbetjäning](active-directory-passwords-reset-register.md).


## <a name="i-am-having-trouble-installing-the-my-apps-portal-browser-extension"></a>Jag har problem med att installera Företagsportalen webbläsartillägget för Mina appar

Kontrollera om du uppfyller krav på webbläsare:

- Portalen kräver en webbläsare som stöder JavaScript och har aktiverat CSS. Om du använder lösenordsbaserad enkel inloggning appar installeras medföljande tillägget också. Det här tillägget laddas ned automatiskt när du startar ett program som har konfigurerats för lösenordsbaserad enkel inloggning appar.

- Webbläsarkrav för tillägget är:
    - Internet Explorer 8, 9, 10, 11 på Windows 7 eller senare
    - Kanten på Windows 10 årsdagar Edition eller senare
    - Chrome på Windows 7 eller senare, och i MacOS X eller senare
    - Firefox 26.0 eller senare på Windows XP SP2 eller senare, och på Mac OS X 10.6 eller senare

Du kan också hämta tillägget för Chrome och kanten från direkt med länkarna nedan:

- [Chrome-tillägg](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

- [Edge-tillägg](https://www.microsoft.com/store/apps/9pc9sckkzk84)

Efter installationen och försök följande steg om du upptäcker problem:

- Kontrollera i tillägget webbläsarinställningarna att filnamnstillägget är aktiverad.

- Starta om webbläsaren och logga in på Mina appar-portalen.

- Rensa cookies i webbläsaren och logga in på Mina appar-portalen.

## <a name="how-do-i-add-a-new-app"></a>Hur lägger jag till en ny app?

1.  På den **appar** klickar du på **Lägg till App**.

2.  Söka efter den app som du vill lägga till och klicka sedan på **Lägg till**.

**Anmärkning:**

- Du kan bara ha åtkomst till det här alternativet om din administratör har aktiverat detta för ditt konto.

- Om appen kräver behörighet, kan du behöva vänta på godkännande av administratören.


## <a name="how-do-i-manage-my-group-memberships"></a>Hur hanterar jag mina gruppmedlemskap

1. Klicka på panelen grupper app. 
2. Klicka på Skapa grupp om du vill skapa en grupp i grupper som jag äger och följ sedan instruktionerna.
3. Följ instruktionerna för att ansluta till en grupp i grupper som jag, ansluter till en grupp.

**Anmärkning:**

- Du kan bara ha åtkomst till det här alternativet om din administratör har aktiverat detta för ditt konto.

- Grupper som du är medlem i kan du visa information och lämna gruppen.

- Grupper som du är ägare till kan du visa information, lägga till eller ta bort medlemmar och lämna gruppen.


## <a name="next-steps"></a>Nästa steg

Relaterad information om felsökning finns [problem med att använda programmet åtkomst panelen webbplats eller mobila program](active-directory-application-access-panel-content-map.md)

