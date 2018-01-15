---
title: "Behöver du hjälp med portalen Mina appar i Azure Active Directory | Microsoft Docs"
description: "Hämta anvisningarna för att utföra vanliga uppgifter när du arbetar med åtkomstpanelen."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: c67cd675-b567-41e1-8bc2-e06fe0b38d3b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: japere
ms.openlocfilehash: 9bec51e1d49308baecc76143ec80902d2da418e8
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2018
---
# <a name="do-you-need-help-with-the-my-apps-portal"></a>Behöver du hjälp med Mina appar portal?

Du har troligtvis nått den här sidan eftersom du körde tyvärr ett problem när du använder portalen Mina appar. Det finns fall som du behöver kontakta supportavdelningen eller administratören om du vill hämta en problem löst, är här vissa felsökning avsnitt som kan vara till hjälp först.

## <a name="i-am-having-trouble-signing-into-the-my-apps-portal"></a>Jag har problem med att logga in på portalen Mina appar

Allmänna problem med att kontrollera:

- Kontrollera om du loggar in rätt URL: [https://myapps.microsoft.com](https://myapps.microsoft.com)

- Försök att lägga till URL: en i webbläsarens betrodda platser.

- Kontrollera att lösenordet inte har upphört att gälla eller har glömt. Kontrollera [här](active-directory-passwords-update-your-own-password.md) för mer information om hur du uppdaterar lösenordet.

- Kontrollera om din kontaktinformation för autentisering är uppdaterade och inte blockerar åtkomsten. Kontrollera [här](https://docs.microsoft.com/azure/multi-factor-authentication/end-user/multi-factor-authentication-end-user) för mer information om hur du konfigurerar autentisering-information.

- Prova att rensa cookies i webbläsaren och försök sedan logga in igen.

Om det fortfarande uppstår problem vid försök att logga in, kontaktar du administratören för ytterligare hjälp.


## <a name="how-do-i-update-my-password"></a>Hur uppdaterar lösenordet?

Om du har glömt ditt lösenord aldrig något från din IT-personal, har låsts ute från ditt konto eller vill ändra det, se [hjälp, jag har glömt mitt Azure AD-lösenord](active-directory-passwords-update-your-own-password.md) för mer information.

## <a name="how-do-i-register-for-password-reset"></a>Hur registrerar jag för återställning av lösenord

Du kan återställa ditt lösenord eller låsa upp ditt konto utan att behöva tala med en person som använder lösenordsåterställning via självbetjäning (SSPR) som en slutanvändare. Innan du kan använda den här funktionen, måste du registrera autentiseringsmetoder eller bekräfta de fördefinierade autentiseringsmetoderna som administratören har fyllt i. Mer information finns i [registrera dig för lösenordsåterställning via självbetjäning](active-directory-passwords-reset-register.md).


## <a name="i-am-having-trouble-installing-the-my-apps-secure-sign-in-extension"></a>Jag har problem med att installera Mina appar säker inloggning tillägget

Kontrollera om du uppfyller krav på webbläsare:

- Portalen kräver en webbläsare som stöder JavaScript och har aktiverat CSS. Om du använder lösenordsbaserad enkel inloggning appar installeras medföljande tillägget också. Det här tillägget laddas ned automatiskt när du startar ett program som har konfigurerats för lösenordsbaserad enkel inloggning appar.

- Webbläsarkrav för tillägget är:
    - Kanten på Windows 10 årsdagar Edition eller senare
    - Chrome på Windows 7 eller senare, och i MacOS X eller senare
    - Firefox 26.0 eller senare på Windows XP SP2 eller senare, och på Mac OS X 10.6 eller senare
    - Internet Explorer 8, 9, 10, 11 på Windows 7 eller senare (begränsat stöd)

Du kan också hämta tillägget för Chrome och kanten från direkt med länkarna nedan:

- [Chrome-tillägg](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

- [Edge-tillägg](https://www.microsoft.com/store/apps/9pc9sckkzk84)

Efter installationen och försök följande steg om du upptäcker problem:

- Kontrollera i tillägget webbläsarinställningarna att filnamnstillägget är aktiverad.

- Starta om webbläsaren och logga in på Mina appar-portalen.

- Rensa cookies i webbläsaren och logga in på Mina appar-portalen.
- Följ den [felsöka Access panelen-tillägg för Internet Explorer](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting) guide för åtkomst till en diagnostik och steg-för-steg-instruktioner om hur du konfigurerar tillägget för Internet Explorer.

## <a name="how-do-i-use-the-my-apps-secure-sign-in-extension"></a>Hur använder jag Mina appar säker inloggning tillägget?
Ändra Mina appar standard-URL för tillägget

Om du använder en annan Mina appar URL än https://myapps.microsoft.com måste du konfigurera standard-URL genom följande steg:
1. Medan inte loggat in på tillägget **Högerklicka** ikonen tillägg.
2. Klicka på **URL Välj appar** på menyn.
3. **Välj** standard-URL.
4. Klicka på ikonen för tillägget.
5. Logga in på tillägget genom att välja **logga in att komma igång**.

Logga in direkt i en app i webbläsaren
1. När du har installerat tillägget, logga in på tillägget genom att välja **logga in att komma igång**.
2. Navigera till den **URL inloggning** appen som du vill logga in på detta är vanligtvis Webbadressen till den app som visar inloggningsformulär.
3. Tillägget bör ändra tillstånd och gör att du vet att ett lösenord är tillgänglig, klicka på den **tillägget ikonen** att logga in

Starta en app från tillägget
1. När du har installerat tillägget, logga in på tillägget genom att välja **logga in att komma igång**.
2. Klicka på ikonen tillägget menyn öppnas.
3. **Sök** för en app som är tillgängliga i portalen Mina appar.
4. Klicka på appen från den **sökresultat** för att starta.
5. De tre sista appar startas visas också i den **nyligen använda** genvägen lista

> [!NOTE]
> Dessa alternativ är bara tillgängliga för gräns, Chrome, Firefox.

## <a name="how-do-i-add-a-new-app"></a>Hur lägger jag till en ny app?

1.  På den **appar** klickar du på **Lägg till App**.

2.  Söka efter den app som du vill lägga till och klicka sedan på **Lägg till**.

**Anmärkning:**

- Du kan bara ha åtkomst till det här alternativet om din administratör har aktiverat detta för ditt konto.

- Om appen kräver behörighet, kan du behöva vänta på godkännande av administratören.


## <a name="how-do-i-manage-my-group-memberships"></a>Hur hanterar jag mina gruppmedlemskap

1. Klicka på den **grupper** panelen. 
2. Om du vill skapa en grupp i grupper som jag äger **Skapa grupp**, och följ sedan instruktionerna.
3. Om du vill ansluta till en grupp i grupper som jag i, klickar du på **ansluter till en grupp**, och följ sedan instruktionerna.

**Anmärkning:**

- Du kan bara ha åtkomst till det här alternativet om din administratör har aktiverat detta för ditt konto.

- Grupper som du är medlem i kan du visa information och lämna gruppen.

- Grupper som du är ägare till kan du visa information, lägga till eller ta bort medlemmar och lämna gruppen.


## <a name="next-steps"></a>Nästa steg

Relaterad information om felsökning finns [problem med att använda programmet åtkomst panelen webbplats eller mobila program](active-directory-application-access-panel-content-map.md)

