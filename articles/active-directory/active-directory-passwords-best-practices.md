---
title: Distribution av Azure AD SSPR | Microsoft Docs
description: "Tips för en lyckad distribution av Azure AD:s självbetjäningsfunktion för återställning av lösenord"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: gahug
ms.assetid: f8cd7e68-2c8e-4f30-b326-b22b16de9787
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/25/2017
ms.author: joflore
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: 09f24fa2b55d298cfbbf3de71334de579fbf2ecd
ms.openlocfilehash: 5f3900aef2b432527454da72f3ff15e533543758
ms.contentlocale: sv-se
ms.lasthandoff: 06/07/2017


---
# Distribuera lösenordsåterställning till användare
<a id="roll-out-password-reset-for-users" class="xliff"></a>

Merparten av kunderna följer helt enkelt stegen nedan för att säkerställa en smidig distribution av självbetjäningsfunktionen för återställning av lösenord (SSPR).

1. [Aktivera lösenordsåterställning i katalogen](active-directory-passwords-getting-started.md)
2. [Konfigurera lokala AD-behörigheter för tillbakaskrivning av lösenord](active-directory-passwords-how-it-works.md#active-directory-permissions)
3. [Konfigurera tillbakaskrivning av lösenord](active-directory-passwords-writeback.md#configuring-password-writeback) för att skriva lösenord från Azure AD tillbaka till din lokala katalog
4. [Tilldela och verifiera nödvändiga licenser](active-directory-passwords-licensing.md)
5. Om du vill distribuera tjänsten gradvis kan du välja att begränsa lösenordsåterställning till en viss grupp användare och sedan utöka gruppen långsamt över tid. Gör detta genom att gå till **Återställning av lösenord via självbetjäning har aktiverats** och byta inställning från **Alla** till **En grupp**. Välj en säkerhetsgrupp för aktivering av lösenordsåterställning. Alla medlemmarna i den här gruppen måste ha fått licenser tilldelade. Det här är ett ypperligt tillfälle att prova [gruppbaserad licensiering](active-directory-passwords-licensing.md#enable-group-or-user-based-licensing).
6. Fyll i [autentiseringsdata](active-directory-passwords-data.md) i den utsträckning som era principer kräver.
7. Lär gärna era användare hur de gör för att använda SSPR – skicka ut anvisningar för hur man registrerar sig och återställer lösenord.
    > [!NOTE]
    > Testa SSPR med en användare och inte en administratör eftersom Microsoft tillämpar starka autentiseringskrav för Azure-konton av administratörstyp. Mer information om lösenordsprinciper för administratörer finns i vår [djupgående artikel](active-directory-passwords-how-it-works.md).

8. Du kan välja att framtvinga registrering när som helst. Användaren måste då bekräfta sin autentiseringsinformation efter en viss tidsperiod. Om du inte vill att användarna ska behöva registrera sig kan du [distribuera lösenordsåterställning utan registrerad slutanvändare](active-directory-passwords-data.md).
9. Om du vill granska användarnas registreringar och användning kan du gå igenom [rapporterna från Azure AD](active-directory-passwords-reporting.md).

## E-postbaserad distribution
<a id="email-based-rollout" class="xliff"></a>

Många kunder tycker att det enklaste sättet att få användarna att använda SSPR är att skicka ut e-post med enkla anvisningar. [Vi har skapat tre enkla e-postmeddelanden som du kan använda som mall när du ska distribuera.](https://onedrive.live.com/?authkey=%21AD5ZP%2D8RyJ2Cc6M&id=A0B59A91C740AB16%2125063&cid=A0B59A91C740AB16)

* **Coming Soon** (Kommer snart) – Den här e-postmallen används några veckor eller dagar före distributionen äger rum, så att användarna uppmärksammas på att en åtgärd krävs.
* **Available Now** (Nu tillgänglig) – Den här e-postmallen används samma dag som distributionen äger rum för att tvinga användarna att registrera sig och bekräfta sina autentiseringsdata, så att de kan använda SSPR vid behov.
* **Sign up Reminder** (Påminnelse om registrering) – Den här e-postmallen används under några dagar eller veckor efter distributionen för att påminna användarna om att de måste registrera sig och bekräfta sina autentiseringsdata.

## Skapa din egen lösenordsportal
<a id="creating-your-own-password-portal" class="xliff"></a>

Många av våra större kunder väljer att själva agera värd för webbsidan och skapar en DNS-rot, till exempel https://passwords.contoso.com. De fyller vanligtvis sidan med länkar till återställning av Azure AD-lösenord, registrering inför lösenordsåterställning och annan företagsspecifik information. De kan då skicka ut e-post eller flygblad med en egen URL som är lätt att komma ihåg, och dit användarna kan vända sig när de behöver använda tjänsterna.

* Portal för lösenordsåterställning – https://passwordreset.microsoftonline.com/
* Portal för registrering inför lösenordsåterställning – http://aka.ms/ssprsetup
* Portal för ändring av lösenord – https://account.activedirectory.windowsazure.com/ChangePassword.aspx

## Använda tvingade registrering
<a id="using-enforced-registration" class="xliff"></a>

Om du vill att användarna ska registrera sig innan de får återställa lösenordet kan du tvinga dem att registrera dig när de loggar in med Azure AD. Du kan aktivera det här alternativet från din katalogs blad för **återställning av lösenord**. Aktivera alternativet **Kräv att användare registrerar vid inloggning?** på **registreringsfliken**.

Administratörer kan kräva att användarna registrerar sig igen efter en viss tid genom att ange ett värde på 0–730 dagar för alternativet **Antal dagar innan användare ombeds att återbekräfta sin autentiseringsinformation**.

När du aktiverar det här alternativet får alla användare som loggar in ett meddelande om att administratören kräver att de verifierar sin autentiseringsinformation.

## Fylla i autentiseringsdata
<a id="populate-authentication-data" class="xliff"></a>

Om du [fyller i autentiseringsdata för dina användare](active-directory-passwords-data.md) behöver inte användarna registrera sig för lösenordsåterställning innan de kan använda SSPR. Så länge som användarna har definierade autentiseringsdata som uppfyller den princip för återställning av lösenord som du har definierat kan användarna återställa sina egna lösenord.

## Inaktivera självbetjäningsfunktionen för återställning av lösenord
<a id="disabling-self-service-password-reset" class="xliff"></a>

Om du vill inaktivera självbetjäningsfunktionen för återställning av lösenord öppnar du din Azure AD-klient och går till **Återställning av lösenord**, **Egenskaper** och väljer **Ingen** under **Återställning av lösenord via självbetjäning har aktiverats**

## Nästa steg
<a id="next-steps" class="xliff"></a>

Följande länkar ger ytterligare information om lösenordsåterställning med Azure AD

* [**Snabbstart** ](active-directory-passwords-getting-started.md) – Kom igång med självbetjäningsfunktionen för återställning av lösenord i Azure AD 
* [**Licensiering**](active-directory-passwords-licensing.md) – Konfigurera Azure AD-licensiering
* [**Data**](active-directory-passwords-data.md) – Förstå de data som krävs och hur de används för lösenordshantering
* [**Anpassa**](active-directory-passwords-customize.md) – Anpassa utseendet för företagets SSPR-funktion.
* [**Princip**](active-directory-passwords-policy.md) – Förstå och ange principer för Azure AD-lösenord
* [**Tillbakaskrivning av lösenord**](active-directory-passwords-writeback.md) – Hur fungerar tillbakaskrivning av lösenord tillsammans med din lokala katalog?
* [**Rapportering**](active-directory-passwords-reporting.md) – Identifiera om, när och var dina användare kommer åt SSPR-funktioner
* [**Teknisk djupdykning** ](active-directory-passwords-how-it-works.md) – Ta en titt bakom kulisserna för att förstå hur det hela fungerar
* [**Vanliga frågor och svar**](active-directory-passwords-faq.md) – Hur gör man? Varför? Vad? Var? Vem? När? – Svar på allt du någonsin velat fråga
* [**Felsökning** ](active-directory-passwords-troubleshoot.md) – Lär dig att lösa vanliga problem med SSPR
