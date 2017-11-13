---
title: Distribution av Azure AD SSPR | Microsoft Docs
description: "Tips för en lyckad distribution av Azure AD:s självbetjäningsfunktion för återställning av lösenord"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: f8cd7e68-2c8e-4f30-b326-b22b16de9787
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 0be1616f5df915e566dc73c15dbea2e53177aa1c
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2017
---
# <a name="how-to-successfully-rollout-self-service-password-reset"></a>Så här distribuerar du självbetjäning för återställning av lösenord

Merparten av kunderna följer stegen nedan för att säkerställa en smidig distribution av självbetjäningsfunktionen för återställning av lösenord (SSPR).

1. [Aktivera lösenordsåterställning i katalogen](active-directory-passwords-getting-started.md).
2. [Konfigurera lokala AD-behörigheter för tillbakaskrivning av lösenord](active-directory-passwords-writeback.md#active-directory-permissions).
3. [Konfigurera tillbakaskrivning av lösenord](active-directory-passwords-writeback.md#configuring-password-writeback) för att skriva lösenord från Azure AD tillbaka till din lokala katalog.
4. [Tilldela och verifiera nödvändiga licenser](active-directory-passwords-licensing.md).
5. Om du vill distribuera SSPR gradvis kan du begränsa åtkomsten till en grupp användare så att du först kan testa med en viss grupp. Gör detta genom att gå till **Återställning av lösenord via självbetjäning har aktiverats**, byta inställning till **Valda** och välja säkerhetsgruppen för aktivering av lösenordsåterställning. 
6. Fyll i [Autentiseringsdata](active-directory-passwords-data.md) för användarna, till exempel deras arbetstelefon, mobiltelefon och alternativa e-postadress.
7. [Anpassa Azure AD-inloggningen för att inkludera din företagsanpassning](active-directory-passwords-customize.md).
8. Lär gärna era användare hur de gör för att använda SSPR – skicka ut anvisningar för hur man registrerar sig och återställer lösenord.
9. Du kan välja att framtvinga registrering när som helst. Användaren måste då bekräfta sin autentiseringsinformation efter en viss tidsperiod.
10. Om du vill granska användarnas registreringar och användning kan du gå igenom [rapporterna från Azure AD](active-directory-passwords-reporting.md).
11. När du är redo kan du aktivera återställning av lösenord för alla användare genom att ställa in **Återställning av lösenord via självbetjäning har aktiverats** på **Alla**. 

   > [!IMPORTANT]
   > Testa SSPR med en användare och inte en administratör eftersom Microsoft tillämpar starka autentiseringskrav för Azure-konton av administratörstyp. Mer information om lösenordsprinciper för administratörer finns i vår [artikel om lösenordsprinciper](active-directory-passwords-policy.md#administrator-password-policy-differences).

## <a name="email-based-rollout"></a>E-postbaserad distribution

Många kunder tycker att det enklaste sättet att få användarna att använda SSPR är att skicka ut e-post med enkla anvisningar. [Vi har skapat tre enkla e-postmeddelanden som du kan använda som mall när du ska distribuera.](https://onedrive.live.com/?authkey=%21AD5ZP%2D8RyJ2Cc6M&id=A0B59A91C740AB16%2125063&cid=A0B59A91C740AB16)

* **Coming Soon** (Kommer snart) – Den här e-postmallen används några veckor eller dagar före distributionen äger rum, så att användarna uppmärksammas på att en åtgärd krävs.
* **Available Now** (Nu tillgänglig) – Den här e-postmallen används samma dag som distributionen äger rum för att tvinga användarna att registrera sig och bekräfta sina autentiseringsdata, så att de kan använda SSPR vid behov.
* **Sign up Reminder** (Påminnelse om registrering) – Den här e-postmallen används under några dagar eller veckor efter distributionen för att påminna användarna om att de måste registrera sig och bekräfta sina autentiseringsdata.

![E-post][Email]

## <a name="creating-your-own-password-portal"></a>Skapa din egen lösenordsportal

Många av våra större kunder väljer att själva agera värd för webbsidan och skapar en DNS-rot, till exempel https://passwords.contoso.com. De fyller vanligtvis sidan med länkar till återställning av Azure AD-lösenord, registrering inför lösenordsåterställning och annan företagsspecifik information. De kan då skicka ut e-post eller flygblad med en egen URL som är lätt att komma ihåg, och dit användarna kan vända sig när de behöver använda tjänsterna.

* Portal för lösenordsåterställning – https://aka.ms/sspr
* Portal för registrering inför lösenordsåterställning – http://aka.ms/ssprsetup
* Portal för ändring av lösenord – https://account.activedirectory.windowsazure.com/ChangePassword.aspx

Vi har skapat en exempelsida som du kan använda och anpassa efter din organisations behov. Sidan kan hämtas från [GitHub](https://github.com/ajamess/password-reset-page).

## <a name="using-enforced-registration"></a>Använda tvingade registrering

Om du vill att användarna ska registrera sig innan de får återställa lösenordet kan du tvinga dem att registrera dig när de loggar in med Azure AD. Du kan aktivera det här alternativet från din katalogs blad för **återställning av lösenord**. Aktivera alternativet **Kräv att användare registrerar vid inloggning?** på **registreringsfliken**.

Administratörer kan kräva att användarna registrerar sig igen efter en viss tid genom att ange ett värde på 0–730 dagar för alternativet **Antal dagar innan användare ombeds att återbekräfta sin autentiseringsinformation**.

När du aktiverar det här alternativet får alla användare som loggar in ett meddelande om att administratören kräver att de verifierar sin autentiseringsinformation.

## <a name="populate-authentication-data"></a>Fylla i autentiseringsdata

Om du [fyller i autentiseringsdata för dina användare](active-directory-passwords-data.md) behöver inte användarna registrera sig för lösenordsåterställning innan de kan använda SSPR. Så länge som användarna har definierade autentiseringsdata som uppfyller den princip för återställning av lösenord som du har definierat kan användarna återställa sina egna lösenord.

## <a name="disabling-self-service-password-reset"></a>Inaktivera självbetjäningsfunktionen för återställning av lösenord

Om du vill inaktivera självbetjäningsfunktionen för återställning av lösenord öppnar du din Azure AD-klient och går till **Återställning av lösenord**, **Egenskaper** och väljer **Ingen** under **Återställning av lösenord via självbetjäning har aktiverats**

## <a name="next-steps"></a>Nästa steg

* [Återställ eller ändra ditt lösenord](active-directory-passwords-update-your-own-password.md).
* [Registrera för återställning av lösenord för självbetjäning](active-directory-passwords-reset-register.md).
* [Har du en fråga om licensiering?](active-directory-passwords-licensing.md)
* [Vilka data används av SSPR och vilka data bör du fylla i för dina användare?](active-directory-passwords-data.md)
* [Vilka principalternativ finns för SSPR?](active-directory-passwords-policy.md)
* [Vad är tillbakaskrivning av lösenord och vad är intresserat med det?](active-directory-passwords-writeback.md)
* [Hur gör jag för att rapportera på aktivitet i SSPR?](active-directory-passwords-reporting.md)
* [Vad är alla alternativ i SSPR och vad betyder de?](active-directory-passwords-how-it-works.md)
* [Jag tror att något har gått sönder. Hur gör jag för att felsöka SSPR?](active-directory-passwords-troubleshoot.md)
* [Jag har en fråga som inte besvarades någon annanstans](active-directory-passwords-faq.md)

[Email]: ./media/active-directory-passwords-best-practices/sspr-emailtemplates.png "Anpassa de här e-postmallarna så att de passar din organisations krav"