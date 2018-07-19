---
title: Distributionsguide för återställning av lösenord för självbetjäning – Azure Active Directory
description: Tips för en lyckad distribution av Azure AD:s självbetjäningsfunktion för återställning av lösenord
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: get-started-article
ms.date: 01/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 3e14c51d644a29985e759da7c8a29927680d3891
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39048959"
---
# <a name="how-to-successfully-roll-out-self-service-password-reset"></a>Distribuera självbetjäning för återställning av lösenord

De flesta kunder genomför följande steg för att säkerställa en smidig distribution av funktionen med självbetjäning för återställning av lösenord (SSPR) i Azure Active directory (Azure AD):

> [!VIDEO https://www.youtube.com/embed/OZn5btP6ZXw]

1. [Aktivera lösenordsåterställning i katalogen](quickstart-sspr.md).
2. [Konfigurera lokala Active Directory-behörigheter för tillbakaskrivning av lösenord](howto-sspr-writeback.md#active-directory-permissions).
3. [Konfigurera tillbakaskrivning av lösenord](howto-sspr-writeback.md#configure-password-writeback) för att skriva lösenord från Azure AD tillbaka till din lokala katalog.
4. [Tilldela och verifiera nödvändiga licenser](concept-sspr-licensing.md).
5. Avgör om du vill genomföra en stegvis distribution. Om du vill distribuera SSPR gradvis kan du begränsa åtkomsten till en grupp användare så att du först kan testa programmet med en viss grupp. Om du vill distribuera till en specifik grupp ställer du in **Återställning av lösenord via självbetjäning har aktiverats** på **Valt** och väljer den säkerhetsgrupp som du vill ska kunna använda lösenordsåterställningen.  Kapsling av säkerhetsgrupper stöds här.
6. Fyll i [autentiseringsdata](howto-sspr-authenticationdata.md) som krävs för att användarna ska kunna registreras, till exempel deras arbetstelefon, mobiltelefon och alternativa e-postadress.
7. [Anpassa Azure AD-inloggningen för att inkludera din företagsanpassning](concept-sspr-customization.md).
8. Lär användarna hur de använder SSPR. Skicka anvisningar till dem om hur de ska registrera sig och återställa sina lösenord.
9. Fastställ när du vill framtvinga registrering. Du kan välja att framtvinga registrering när som helst. Du kan också kräva att användarna bekräftar sin autentiseringsinformation igen efter en viss tidsperiod.
10. Använd rapporteringsfunktionen. Med tiden kan du granska användarnas registrering och användning med hjälp av [rapporteringsfunktionen i Azure AD](howto-sspr-reporting.md).
11. Aktivera lösenordsåterställning. När du är redo kan du aktivera återställning av lösenord för alla användare genom att ställa in **Återställning av lösenord via självbetjäning har aktiverats** på **Alla**. 

   > [!NOTE]
   > Befintliga autentiseringsdata som en användare har registrerat som en del i en testgrupp blir inte ogiltiga om det här alternativet ändras från en vald grupp till Alla. Användare som har konfigurerats och har giltiga autentiseringsdata registrerade kommer fortfarande fungera.

12. [Aktivera lösenordsåterställning på inloggningsskärmen för Windows 10-användare](tutorial-sspr-windows.md).

   > [!IMPORTANT]
   > Testa SSPR med en användare och inte en administratör eftersom Microsoft tillämpar starka autentiseringskrav för administratörskonton i Azure. Mer information om lösenordsprinciper för administratörer finns i vår artikel om [lösenordsprinciper](concept-sspr-policy.md#administrator-password-policy-differences).

## <a name="email-based-rollout"></a>E-postbaserad distribution

Många kunder tycker att det enklaste sättet att få användarna att använda SSPR är att skicka ut e-post med enkla anvisningar. [Vi har skapat tre enkla e-postmeddelanden som du kan använda som mall när du ska distribuera](https://www.microsoft.com/download/details.aspx?id=56768):

* **Kommer snart**: Den här e-postmallen används några veckor eller dagar innan distributionen äger rum, så att användarna uppmärksammas på att en åtgärd krävs.
* **Tillgänglig nu**: Den här e-postmallen används samma dag som distributionen äger rum för att leda användarna till att registrera sig och bekräfta sina autentiseringsdata. Om användarna registrerar sig nu är SSPR tillgängligt för dem när de behöver det.
* **Påminnelse om registrering**: En e-postmall som används under några dagar eller veckor efter distributionen för att påminna användarna om att de måste registrera sig och bekräfta sina autentiseringsdata.

![E-post][Email]

## <a name="create-your-own-password-portal"></a>Skapa en egen lösenordsportal

Många kunder väljer att själva agera värd för webbsidan och skapa en DNS-rot, till exempel https://passwords.contoso.com. De fyller den här sidan med länkar till följande information:

* [Portal för Azure AD-lösenordsåterställning – https://aka.ms/sspr](https://aka.ms/sspr)
* [Registreringsportal för Azure AD-lösenordsåterställning – https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)
* [Portal för Azure AD-lösenordsändring – https://account.activedirectory.windowsazure.com/ChangePassword.aspx](https://account.activedirectory.windowsazure.com/ChangePassword.aspx)
* Annan information som är specifik för organisationen

De kan då skicka ut e-post eller flygblad med en egen URL som är lätt att komma ihåg, och dit användarna kan vända sig när de behöver använda tjänsterna. Vi har skapat en [exempelsida för lösenordsåterställning](https://github.com/ajamess/password-reset-page) som du kan använda och anpassa efter din organisations behov.

## <a name="step-by-step-deployment-plan"></a>Distributionsplan steg för steg

Azure Active Directory-produktgruppen har skapat en [stegvis distributionsplan](https://aka.ms/SSPRDeploymentPlan) som kan användas parallellt med dokumentationen på den här webbplatsen, för att organisationen ska kunna skapa en affärsstudie och planera för distribution av självåterställning av lösenord.

## <a name="use-enforced-registration"></a>Använd tvingande registrering

Om du vill att användarna ska registrera sig innan de får återställa lösenordet kan du kräva att de registrerar sig när de loggar in med Azure AD. Du kan aktivera det här alternativet från katalogens fönster **Återställning av lösenord**. Aktivera alternativet **Kräv att användare registrerar vid inloggning?** på fliken **Registrering**.

Administratörer kan kräva att användarna registrerar sig igen efter en viss tidsperiod. Alternativet **Ange antal dagar innan användare uppmanas att bekräfta sin autentiseringsinformation** kan ställas in på 0 till 730 dagar.

När du aktiverar det här alternativet får alla användare som loggar in ett meddelande om att administratören kräver att de verifierar sin autentiseringsinformation.

## <a name="populate-authentication-data"></a>Fylla i autentiseringsdata

Du bör [fylla i autentiseringsdata för dina användare](howto-sspr-authenticationdata.md). På så sätt behöver inte användarna registrera sig för lösenordsåterställning innan de kan använda SSPR. Om användarna har angivit autentiseringsdata som uppfyller den princip för återställning av lösenord som du har definierat så kan de återställa sina egna lösenord.

## <a name="disable-self-service-password-reset"></a>Inaktivera lösenordsåterställning via självbetjäning

Det är enkelt att inaktivera lösenordsåterställning via självbetjäning. Öppna Azure AD-klienten och gå till **Återställning av lösenord** > **Egenskaper**. Välj sedan **Ingen** under **Återställning av lösenord via självbetjäning har aktiverats**.

## <a name="next-steps"></a>Nästa steg

* [Återställ eller ändra ditt lösenord](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registrera för återställning av lösenord för självbetjäning](../user-help/active-directory-passwords-reset-register.md)
* [Har du en fråga om licensiering?](concept-sspr-licensing.md)
* [Vilka data används av SSPR och vilka data bör du fylla i för dina användare?](howto-sspr-authenticationdata.md)
* [Vilka principalternativ finns för SSPR?](concept-sspr-policy.md)
* [Vad är tillbakaskrivning av lösenord och vad är intresserat med det?](howto-sspr-writeback.md)
* [Hur gör jag för att rapportera på aktivitet i SSPR?](howto-sspr-reporting.md)
* [Vad är alla alternativ i SSPR och vad betyder de?](concept-sspr-howitworks.md)
* [Jag tror att något har gått sönder. Hur gör jag för att felsöka SSPR?](active-directory-passwords-troubleshoot.md)
* [Jag har en fråga som inte besvarades någon annanstans](active-directory-passwords-faq.md)

[Email]: ./media/howto-sspr-deployment/sspr-emailtemplates.png "Anpassa de här e-postmallarna så att de passar din organisations krav"
