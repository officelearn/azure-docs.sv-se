---
title: Distributionsguide för återställning av lösenord för självbetjäning – Azure Active Directory
description: Tips för en lyckad distribution av Azure AD:s självbetjäningsfunktion för återställning av lösenord
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/17/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb9333845a78204ea1c6e56e17b3b6f9e5513c9c
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370694"
---
# <a name="how-to-successfully-roll-out-self-service-password-reset"></a>Distribuera självbetjäning för återställning av lösenord

De flesta kunder genomför följande steg för att säkerställa en smidig distribution av funktionen med självbetjäning för återställning av lösenord (SSPR) i Azure Active directory (Azure AD):

> [!VIDEO https://www.youtube.com/embed/OZn5btP6ZXw]

1. Slutför en pilot lansering med en liten del av din organisation.
   * Information om hur du pilotprogrammet finns i den [självstudien: Fullständig pilotprojekt för återställning av en Azure AD lösenord för självbetjäning lansera](tutorial-sspr-pilot.md).
1. Utbilda supportavdelningen.
   * Hur kommer de att användarna?
   * Tvingar du användarna att använda SSPR och inte tillåter din supportavdelning att hjälpa användarna?
   * Har du försett dem med URL: er för registrering och återställa?
      * Registrering:  https://aka.ms/ssprsetup
      * Reset: https://aka.ms/sspr
1. Förklara för användarna.
   * Det här avsnittet i det här dokumentet går över exemplet kommunikation lösenord portaler, verkställa registrering och fylla i autentiseringsdata.
   * Azure Active Directory-produktgruppen har skapat en [stegvis distributionsplan](https://aka.ms/SSPRDeploymentPlan) som kan användas parallellt med dokumentationen på den här webbplatsen, för att organisationen ska kunna skapa en affärsstudie och planera för distribution av självåterställning av lösenord.
1. Aktivera Självbetjäning för återställning av lösenord för hela organisationen.
   * När du är redo kan du aktivera återställning av lösenord för alla användare genom att ställa in **Återställning av lösenord via självbetjäning har aktiverats** på **Alla**.

## <a name="sample-communication"></a>Exempel-kommunikation

Många kunder tycker att det enklaste sättet att få användarna att använda SSPR är att skicka ut e-post med enkla anvisningar. [Vi har skapat enkla e-postmeddelanden och andra säkerheter som du kan använda som en mall för att distribuera](https://www.microsoft.com/download/details.aspx?id=56768):

* **Kommer snart**: En e-postmall som du använder några veckor eller dagar före distributionen för att informera användarna om de behöver göra något.
* **Tillgängligt för tillfället**: En e-postmall som att du använder dag som distributionen äger rum för att tvinga användarna att registrera sig och bekräfta sina autentiseringsdata. Om användarna registrerar sig nu är SSPR tillgängligt för dem när de behöver det.
* **Påminnelse om registrering**: En e-postmall för några dagar eller veckor efter distributionen för att påminna användarna registrera sig och bekräfta sina autentiseringsdata.
* **SSPR affischer**: Affischer kan du anpassa och visa runt organisationen i dagar och veckor inledande upp till och när din lansering.
* **SSPR placeringskort**: Tabellen kort som du kan placera i rummet lunch, konferensrum, eller på skrivbord att uppmana användarna att slutföra registreringen.
* **SSPR klistermärken**: Etikett-mallar som du kan anpassa och skriva ut om du vill placera bärbara datorer, Övervakare, tangentbord eller mobiltelefoner för att komma ihåg hur du kommer åt SSPR.

![SSPR-e-exempel för distributionen för användarna][Email]

## <a name="create-your-own-password-portal"></a>Skapa en egen lösenordsportal

Många kunder väljer att själva agera värd för webbsidan och skapa en DNS-rot, till exempel https://passwords.contoso.com. De fyller den här sidan med länkar till följande information:

* [Portal för Azure AD-lösenordsåterställning – https://aka.ms/sspr](https://aka.ms/sspr)
* [Registreringsportal för Azure AD-lösenordsåterställning – https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)
* [Portal för Azure AD-lösenordsändring – https://account.activedirectory.windowsazure.com/ChangePassword.aspx](https://account.activedirectory.windowsazure.com/ChangePassword.aspx)
* Annan information som är specifik för organisationen

De kan då skicka ut e-post eller flygblad med en egen URL som är lätt att komma ihåg, och dit användarna kan vända sig när de behöver använda tjänsterna. Vi har skapat en [exempelsida för lösenordsåterställning](https://github.com/ajamess/password-reset-page) som du kan använda och anpassa efter din organisations behov.

## <a name="use-enforced-registration"></a>Använd tvingande registrering

Om du vill att användarna ska registrera sig innan de får återställa lösenordet kan du kräva att de registrerar sig när de loggar in med Azure AD. Du kan aktivera det här alternativet från katalogens fönster **Återställning av lösenord**. Aktivera alternativet **Kräv att användare registrerar vid inloggning?** på fliken **Registrering**.

Administratörer kan kräva att användarna registrerar sig igen efter en viss tidsperiod. Alternativet **Ange antal dagar innan användare uppmanas att bekräfta sin autentiseringsinformation** kan ställas in på 0 till 730 dagar.

När du aktiverar det här alternativet får alla användare som loggar in ett meddelande om att administratören kräver att de verifierar sin autentiseringsinformation.

## <a name="populate-authentication-data"></a>Fylla i autentiseringsdata

Du bör överväga [förväg fylla vissa autentiseringsdata för dina användare](howto-sspr-authenticationdata.md). På så sätt behöver inte användarna registrera sig för lösenordsåterställning innan de kan använda SSPR. Om användarna har angivit autentiseringsdata som uppfyller den princip för återställning av lösenord som du har definierat så kan de återställa sina egna lösenord.

## <a name="disable-self-service-password-reset"></a>Inaktivera lösenordsåterställning via självbetjäning

Om din organisation bestämmer sig för att inaktivera lösenordsåterställning via självbetjäning är en enkel process. Öppna Azure AD-klienten och gå till **Återställning av lösenord** > **Egenskaper**. Välj sedan **Ingen** under **Återställning av lösenord via självbetjäning har aktiverats**. Användare kommer fortfarande att ha sina registrerade autentiseringsmetoder för framtida användning.

## <a name="next-steps"></a>Nästa steg

* [Återställ eller ändra ditt lösenord](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registrera för återställning av lösenord för självbetjäning](../user-help/active-directory-passwords-reset-register.md)
* [Aktivera konvergerade registrering för Azure Multi-Factor Authentication och Azure AD lösenordsåterställning via självbetjäning](concept-registration-mfa-sspr-converged.md)
* [Har du en fråga om licensiering?](concept-sspr-licensing.md)
* [Vilka data används av SSPR och vilka data bör du fylla i för dina användare?](howto-sspr-authenticationdata.md)
* [Vilka principalternativ finns för SSPR?](concept-sspr-policy.md)
* [Vad är tillbakaskrivning av lösenord och vad är intresserat med det?](howto-sspr-writeback.md)
* [Hur gör jag för att rapportera på aktivitet i SSPR?](howto-sspr-reporting.md)
* [Vad är alla alternativ i SSPR och vad betyder de?](concept-sspr-howitworks.md)
* [Jag tror att något har gått sönder. Hur gör jag för att felsöka SSPR?](active-directory-passwords-troubleshoot.md)
* [Jag har en fråga som inte besvarades någon annanstans](active-directory-passwords-faq.md)

[Email]: ./media/howto-sspr-deployment/sspr-emailtemplates.png "Anpassa de här e-postmallarna så att de passar din organisations krav"
