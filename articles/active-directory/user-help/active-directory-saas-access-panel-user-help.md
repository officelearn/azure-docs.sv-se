---
title: Få hjälp med åtkomst till och med MyApps-portalen i Azure Active Directory | Microsoft Docs
description: Få hjälp med att logga in på och utföra vanliga uppgifter i åtkomstpanelen.
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: c67cd675-b567-41e1-8bc2-e06fe0b38d3b
ms.service: active-directory
ms.component: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: lizross
ms.reviewer: japere
ms.openlocfilehash: 4af0b9cf50d643fda2341e791753e3bdfc8166b1
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54812581"
---
# <a name="troubleshoot-issues-with-accessing-and-using-the-myapps-portal"></a>Felsöka problem med åtkomst till och med hjälp av MyApps-portalen

Om du upplever problem med att logga in på eller använda MyApps-portalen, försök med dessa felsökningstips innan du kontaktar supportavdelningen eller administratören om du behöver hjälp.

## <a name="i-am-having-trouble-signing-into-the-myapps-portal"></a>Jag har problem med att logga in på MyApps-portalen

Prova de här Allmänna tips:

- Och kontrollera att se om du använder rätt Webbadress [ https://myapps.microsoft.com ](https://myapps.microsoft.com).
- Försök att lägga till URL: en i webbläsarens betrodda platser.
- Kontrollera att lösenordet är korrekt och inte har upphört att gälla. Mer information finns i [återställa lösenordet för arbets- eller skolkonto](active-directory-passwords-update-your-own-password.md).
- Kontrollera att din kontaktinformation för autentisering är uppdaterade och inte förhindrar åtkomst. Mer information finns i [vad Azure Multi-Factor Authentication innebär för mig?](https://docs.microsoft.com/azure/multi-factor-authentication/end-user/multi-factor-authentication-end-user).
- Försöka ta bort cookies i webbläsaren och försök sedan att logga in igen.

Kontakta administratören om det fortfarande uppstår problem när du försöker logga in.

## <a name="i-seem-to-be-having-password-issues"></a>Jag verkar ha problem med att lösenord

Om du har glömt ditt lösenord, aldrig fått något från IT-personalen, utelåst från ditt konto eller vill du ändra ditt lösenord, se [hjälp, jag har glömt mitt Azure AD-lösenord](active-directory-passwords-update-your-own-password.md).

## <a name="i-need-to-register-for-password-reset"></a>Jag behöver registrera för återställning av lösenord

Du kan återställa ditt lösenord eller låsa upp ditt konto utan att behöva tala med någon genom att använda lösenordsåterställning via självbetjäning (SSPR). Innan du kan använda den här funktionen, måste du registrera dina autentiseringsmetoder eller bekräfta de fördefinierade autentiseringsmetoderna som administratören kräver. Mer information finns i [registrera dig för lösenordsåterställning via självbetjäning](active-directory-passwords-reset-register.md).

## <a name="i-am-having-trouble-installing-the-my-apps-secure-sign-in-extension"></a>Jag har problem med att installera den Mina appar skyddat Inloggningstillägg

MyApps-portalen kräver en webbläsare som stöder JavaScript och CSS aktiverat. Om du använder lösenordsbaserad enkel inloggning för appar, måste tillhörande tillägget installeras också. Det här tillägget laddas ned automatiskt när du startar ett program som har konfigurerats för lösenordsbaserad enkel inloggning för appar.

Kontrollera att du uppfyller följande webbläsarkrav:

- **Microsoft Edge**: på Windows 10 Anniversary Edition eller senare.
- **Chrome**: på Windows 7 eller senare, och i Mac OS X eller senare.
- **Firefox 26.0 eller senare**: på Windows XP SP2 eller senare, och på Mac OS X 10.6 eller senare.
- **Internet Explorer 11**: på Windows 7 eller senare (begränsat stöd).

Du kan också hämta tillägget direkt från följande platser:

- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176)
- [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)

Om du har installerat tillägget och fortfarande har problem kan du prova följande:

- Kontrollera inställningarna för webbläsaren tillägget för att kontrollera att tillägget är aktiverat.
- Starta om webbläsaren och logga in på MyApps-portalen.
- Rensa cookies i webbläsaren och logga in på MyApps-portalen.
- Åtkomst till ett verktyg för diagnostik och stegvisa instruktioner om hur du konfigurerar tillägget för Internet Explorer finns [Felsök Access Panel-tillägg för Internet Explorer](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting).

## <a name="use-the-my-apps-secure-sign-in-extension"></a>Använd den Mina appar skyddat Inloggningstillägg
* Om du använder en Mina appar-URL än `https://myapps.microsoft.com`, konfigurera standard-URL genom att göra följande:
   1. När du arbetar *inte* inloggad på tillägget, högerklicka på tilläggsikonen.
   2. På menyn, Välj **Mina appar URL**.
   3. Välj standard-URL.
   4. Välj tillägg.
   5. För att logga in till tillägget, Välj **logga in att komma igång**.

* För att logga in direkt i en app i webbläsaren, gör du följande:
   1. När du installerar tillägget, loggar du in till den genom att välja **logga in att komma igång**.
   2. Logga in på appen med inloggnings URL.  
       Inloggnings URL är vanligtvis URL: en för den app som visar formuläret logga in.
      Tillägget bör ändra tillstånd och att du vet att det finns ett lösenord.
   3. Välj tillägg för att logga in.

* Starta en app från tillägget genom att göra följande:
   1. När du installerar tillägget, loggar du in till den genom att välja **logga in att komma igång**.
   2. Välj tillägg till menyn öppnas.
   3. Sök efter en app som är tillgänglig i MyApps-portalen.
   4. Välj appen i listan med sökresultat.  
       De tre sista appar som du har använt visas i den **nyligen använda** genväg lista.

> [!NOTE]
> Dessa alternativ är endast tillgängligt för Microsoft Edge, Chrome och Firefox.

## <a name="how-do-i-add-a-new-app"></a>Hur lägger jag till en ny app?

1.  På den **appar** väljer **Lägg till App**.
2.  Sök efter den app som du vill lägga till och välj sedan **Lägg till**.

   > [!NOTE]
   > * Du kan komma åt det här alternativet endast om din administratör har aktiverat för ditt konto.
   > * Om appen kräver behörighet, kan du behöva vänta på administratörsgodkännande.

## <a name="how-do-i-manage-my-group-memberships"></a>Hur hanterar jag mina medlemskap i grupper?

Välj den **grupper** panelen och gör sedan något av följande:
* Skapa en grupp under **grupper som jag äger**väljer **Skapa grupp**, och följ sedan instruktionerna.
* Att ansluta till en grupp under **grupper som jag i**väljer **ansluter till en grupp**, och följ sedan instruktionerna.

   > [!NOTE]
   > * Du kan komma åt det här alternativet endast om din administratör har aktiverat för ditt konto.
   > * Om du är medlem i en grupp kan du visa information och lämna gruppen.
   > * Om du är ägare till en grupp kan du visa information, lägga till eller ta bort medlemmar och lämna gruppen.