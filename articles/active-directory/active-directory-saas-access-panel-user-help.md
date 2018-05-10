---
title: Få hjälp med att få åtkomst till och med hjälp av portalen för Mina appar i Azure Active Directory | Microsoft Docs
description: Få hjälp med att logga in till och utföra vanliga uppgifter på åtkomstpanelen.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: c67cd675-b567-41e1-8bc2-e06fe0b38d3b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: japere
ms.openlocfilehash: 681831dcc65fa74d8d2e26f58849140498843c49
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="troubleshoot-issues-with-accessing-and-using-the-my-apps-portal"></a>Felsökning av problem med hjälp av portalen Mina appar och åtkomst till

Om du har problem med att logga in på eller med hjälp av portalen Mina appar, försök dessa felsökningstips innan du kontaktar supportavdelningen eller administratören om hjälp.

## <a name="i-am-having-trouble-signing-into-the-my-apps-portal"></a>Jag har problem med att logga in på portalen Mina appar

Följande allmänna tips i åtanke:

- Första, kontrollera för att se om du använder rätt URL [ https://myapps.microsoft.com ](https://myapps.microsoft.com).
- Försök att lägga till URL: en i webbläsarens betrodda platser.
- Kontrollera att lösenordet är korrekt och inte har gått ut. Mer information finns i [återställa ditt lösenord för arbetet eller skolan](active-directory-passwords-update-your-own-password.md).
- Kontrollera att din kontaktinformation för autentisering är uppdaterade och inte blockerar åtkomsten. Mer information finns i [vad Azure Multi-Factor Authentication innebär för mig?](https://docs.microsoft.com/azure/multi-factor-authentication/end-user/multi-factor-authentication-end-user).
- Prova att rensa cookies i webbläsaren och försök sedan logga in igen.

Kontakta administratören om det fortfarande uppstår problem när du försöker logga in.


## <a name="i-seem-to-be-having-password-issues"></a>Det verkar ha lösenordsproblem med

Om du har glömt ditt lösenord, aldrig fått ett från din IT-personal, utelåst från ditt konto eller ändra ditt lösenord, se [hjälp, jag har glömt mitt Azure AD-lösenord](active-directory-passwords-update-your-own-password.md).

## <a name="i-need-to-register-for-password-reset"></a>Jag behöver registrera dig för lösenordsåterställning

Du kan återställa ditt lösenord eller låsa upp ditt konto utan att behöva tala med någon med hjälp av lösenordsåterställning via självbetjäning (SSPR). Innan du kan använda den här funktionen måste du registrera din autentiseringsmetoder eller bekräfta fördefinierade autentiseringsmetoder som kräver din administratör. Mer information finns i [registrera dig för lösenordsåterställning via självbetjäning](active-directory-passwords-reset-register.md).


## <a name="i-am-having-trouble-installing-the-my-apps-secure-sign-in-extension"></a>Jag har problem med att installera Mina appar säker inloggning tillägget

Portal för Mina appar kräver en webbläsare som stöder JavaScript och har aktiverat CSS. Om du använder lösenordsbaserad enkel inloggning appar installeras medföljande tillägget också. Det här tillägget laddas ned automatiskt när du startar ett program som har konfigurerats för lösenordsbaserad enkel inloggning appar.

Kontrollera att du uppfyller följande webbläsarkrav:
- **Edge**: på Windows 10 årsdagar Edition eller senare.
- **Chrome**: på Windows 7 eller senare, och på Mac OS X eller senare.
- **Firefox 26.0 eller senare**: Windows XP SP2 eller senare, och på Mac OS X 10.6 eller senare.
- **Internet Explorer 11**: i Windows 7 eller senare (begränsat stöd).

Du kan också hämta tillägget för Chrome och kanten direkt från följande platser:

- [Chrome-tillägg](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)
- [Edge-tillägg](https://www.microsoft.com/store/apps/9pc9sckkzk84)

Om du har installerat tillägg och fortfarande har problem kan du testa följande:

- Kontrollera tillägget webbläsarinställningarna så att tillägget är aktiverat.
- Starta om webbläsaren och logga in på Mina appar-portalen.
- Rensa cookies i webbläsaren och logga in på Mina appar-portalen.
- Åtkomst till en diagnostik och stegvisa instruktioner om hur du konfigurerar tillägget för Internet Explorer finns [felsöka Access panelen-tillägg för Internet Explorer](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting).

## <a name="use-the-my-apps-secure-sign-in-extension"></a>Använd den Mina appar säker inloggning tillägg
* Om du använder en URL för Mina appar än `https://myapps.microsoft.com`, konfigurera standard-URL genom att göra följande:
   1. När du är *inte* inloggad-tillägget, högerklicka på ikonen för tillägget.
   2. Välj på menyn **Mina appar URL**.
   3. Välj standard-URL.
   4. Välj ikonen tillägg.
   5. Om du vill logga in på tillägget, Välj **logga in att komma igång**.

* Logga in direkt till en app från webbläsaren genom att göra följande:
   1. När du installerar tillägget för inloggning till den genom att välja **logga in att komma igång**.
   2. Logga in i appen med URL för inloggning.  
       URL för inloggning är vanligtvis Webbadressen till den app som visar inloggning formuläret.
      Tillägget bör ändra tillstånd och att du vet att ett lösenord är tillgänglig.
   3. Välj ikonen tillägg för att logga in.

* Om du vill starta en app från tillägget gör du följande:
   1. När du installerar tillägget för inloggning till den genom att välja **logga in att komma igång**.
   2. Välj ikonen tillägget menyn öppnas.
   3. Sök efter en app som är tillgängliga i portalen Mina appar.
   4. I listan över sökresultat, Välj appen.  
       De tre senaste appar som du har använt visas i den **nyligen använda** genvägen lista.

> [!NOTE]
> Dessa alternativ är endast tillgängligt för gräns, Chrome och Firefox.

## <a name="how-do-i-add-a-new-app"></a>Hur lägger jag till en ny app?

1.  På den **appar** väljer **Lägg till App**.
2.  Sök efter den app som du vill lägga till och välj sedan **Lägg till**.

   > [!NOTE]
   > * Du kan använda det här alternativet endast om din administratör har aktiverat för ditt konto.
   > * Om appen kräver behörighet, kan du behöva vänta på administratörsgodkännande.
   > 

## <a name="how-do-i-manage-my-group-memberships"></a>Hur hanterar jag mina gruppmedlemskap

Välj den **grupper** panelen och gör sedan något av följande: 
* Skapa en grupp under **grupper som jag äger**väljer **Skapa grupp**, och följ sedan instruktionerna.
* Att ansluta till en grupp under **grupper jag i**väljer **ansluter till en grupp**, och följ sedan instruktionerna.

   > [!NOTE]
   > * Du kan använda det här alternativet endast om din administratör har aktiverat för ditt konto.
   > * Om du är medlem i en grupp kan du visa information och lämna gruppen.
   > * Om du är ägare till en grupp kan du visa information, lägga till eller ta bort medlemmar och lämna gruppen.
   >


## <a name="next-steps"></a>Nästa steg

Mer information om felsökning finns i [problem med att använda programmet åtkomst panelen webbplats eller mobilprogram](active-directory-application-access-panel-content-map.md).

