---
title: Snabbstart för återställning av lösenord för självbetjäning – Azure Active Directory
description: Distribuera återställning av lösenord för självbetjäning i Azure AD snabbt
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: get-started-article
ms.date: 01/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 856ae994e49f809ca2ef906ba6cdc7f8624dd201
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
---
# <a name="azure-ad-self-service-password-reset-rapid-deployment"></a>Snabb distribution av återställning av lösenord för självbetjäning i Azure AD

> [!IMPORTANT]
> **Är du här eftersom du har problem med att logga in?** Om så är fallet, se [Hjälp, jag har glömt mitt Azure AD-lösenord](../active-directory-passwords-update-your-own-password.md).

Återställning av lösenord för självbetjäning (SSPR) erbjuder ett enkelt sätt för IT-administratörer att låta användarna återställa eller låsa upp sina lösenord eller sina konton. Systemet innehåller detaljerade rapporter för att spåra när användare använder systemet tillsammans med aviseringar som informerar om missbruk.

I den här handboken förutsätts att du redan har en aktiv utvärderingsversion eller licensierad Azure Active Directory-klient (Azure AD). Om du behöver hjälp med att konfigurera Azure AD kan du läsa [Komma igång med Azure AD](../get-started-azure-ad.md).

## <a name="enable-sspr-for-your-azure-ad-tenant"></a>Aktivera SSPR för din Azure AD-klientorganisation

> [!VIDEO https://www.youtube.com/embed/Pa0eyqjEjvQ]

1. På **Azure Portal** väljer du **Återställning av lösenord** under **Azure Active Directory** från din befintliga Azure AD-klient.

2. Välj något av följande på sidan **Egenskaper** under alternativet **Återställning av lösenord via självbetjäning har aktiverats**:
   * **Ingen**: Ingen kan använda funktionen SSPR.
   * **Valda**: Endast medlemmar av en viss Azure AD-grupp som du utser kan använda SSPR-funktionen. Vi rekommenderar att du definierar en grupp användare och använder den här inställningen när du distribuerar funktionen för ett ”proof of concept” (POC). Kapsling av säkerhetsgrupper stöds här.
   * **Alla**: Alla användare med konton i din Azure AD-klient kan använda SSPR-funktionen. Vi rekommenderar att du använder den här inställningen när du är redo att distribuera den här funktionen till hela din klientorganisation när du har slutfört ett ”proof of concept” (POC).

   > [!IMPORTANT]
   > De som har ett Azure-administratörskonto har alltid möjlighet att återställa sitt lösenord, oavsett vad det här alternativet är inställt på. 

3. Välj följande på sidan **Autentiseringsmetoder**:
   * **Antal metoder som krävs för återställning**: Vi stöder minst en eller högst två.
   * **Metoder som finns tillgängliga för användare**: Vi behöver minst en, men det skadar inte att ha ett extra alternativ tillgängligt.
      * **E-post**: Skickar ett e-postmeddelande med en kod till användarens konfigurerade e-postadress för autentisering.
      * **Mobiltelefon**: Ger användaren möjlighet att ta emot ett samtal eller sms med en kod till hans/hennes konfigurerade mobiltelefonnummer.
      * **Arbetstelefon**: Ringer användaren med en kod till användarens konfigurerade arbetstelefonnummer.
      * **Säkerhetsfrågor**: Du måste välja:
         * **Antal frågor som krävs för registrering**: Minimum för lyckad registrering. En användare kan välja att besvara fler frågor för att skapa en pool med frågor som funktionen kan använda. Det här alternativet går att ställa in från tre till fem frågor och måste vara större än eller lika med antalet frågor som krävs för att återställa lösenordet. Användaren kan lägga till anpassade frågor om hon/han väljer **knappen för anpassat** knappen när hon/han väljer sina säkerhetsfrågor.
         * **Antal frågor som krävs för återställning**: Kan ställas in från tre till fem frågor som ska besvaras korrekt innan en användares lösenord kan återställas eller låsas upp.
            
    ![Autentisering][Authentication]

4. Rekommenderat: **Anpassning** gör att du kan ändra länken **Kontakta administratören** så att den pekar på en sida eller e-postadress som du definierar. Vi rekommenderar att du ställer in den här länken så att den dirigerar till något som en e-postadress eller en webbplats som användarna redan använder för supportförfrågningar.

5. Valfritt: Sidan **Registrering** ger administratörer alternativ för följande:
   * Kräv att användare registrerar sig vid inloggning.
   * Ange antal dagar innan användare uppmanas att bekräfta sin autentiseringsinformation.

6. Valfritt: Sidan **Meddelande** ger administratörer alternativ för följande:
   * Meddela användare om lösenordsåterställning.
   * Meddela alla administratörer när andra administratörer återställer sina lösenord.

Nu har du konfigurerat SSPR för din Azure AD-klient. Användarna kan nu använda instruktionerna i artiklarna [Registrera för återställning av lösenord för självbetjäning](../active-directory-passwords-reset-register.md) och [Återställ eller ändra ditt lösenord](../active-directory-passwords-update-your-own-password.md) för att uppdatera sina lösenord utan inblandning av administratören. Om du använder en molnbaserad lösning kan du sluta här. Alternativt kan du fortsätta till nästa avsnitt och konfigurera synkronisering av lösenord till en lokal Active Directory-domän.

> [!TIP]
> Testa SSPR med en användare och inte en administratör eftersom Microsoft tillämpar starka autentiseringskrav för administratörskonton i Azure. Mer information om lösenordsprinciper för administratörer finns i vår artikel om [lösenordsprinciper](concept-sspr-policy.md#administrator-password-policy-differences).

## <a name="configure-synchronization-to-an-existing-identity-source"></a>Konfigurera synkronisering till en befintlig identitetskälla

För att aktivera lokal identitetssynkronisering till Azure AD måste du installera och konfigurera [Azure AD Connect](./../connect/active-directory-aadconnect.md) på en server i din organisation. Det här programmet hanterar synkronisering av användare och grupper från din befintliga identitetskälla till Azure AD-klienten. Mer information finns i:

* [Uppgradera från DirSync eller AD Sync till Azure AD Connect](./../connect/active-directory-aadconnect-dirsync-deprecated.md)
* [Komma igång med Azure AD Connect med expressinställningar](./../connect/active-directory-aadconnect-get-started-express.md)
* [Konfigurera tillbakaskrivning av lösenord](howto-sspr-writeback.md#configure-password-writeback) för att skriva lösenord från Azure AD tillbaka till din lokala katalog

### <a name="on-premises-policy-change"></a>Ändring av lokala principer

Om du synkroniserar användare från en lokal Active Directory-domän och vill tillåta att användarna återställer sina lösenord direkt kan du göra följande ändring i din lokala lösenordsprincip:

1. Gå till **Datorkonfiguration** > **Principer** > **Windows-inställningar** > **Säkerhetsinställningar**  >  **Kontoprinciper** > **Lösenordsprincip**.

2. Sätt **Lägsta ålder för lösenord** till  **0 dagar**.

Den här säkerhetsinställningen anger tidsperioden (i dagar) som ett lösenord måste användas innan användaren kan ändra det. Genom att sätta värdet till **0 dagar** kan användarna använda SSPR om deras lösenord har ändrats av de lokala supportteamen.

![Princip][Policy]

## <a name="disable-self-service-password-reset"></a>Inaktivera lösenordsåterställning via självbetjäning

Det är enkelt att inaktivera lösenordsåterställning via självbetjäning. Öppna Azure AD-klienten och gå till **Återställning av lösenord** > **Egenskaper**. Välj sedan **Ingen** under **Återställning av lösenord via självbetjäning har aktiverats**.

### <a name="learn-more"></a>Läs mer
Följande artiklar ger ytterligare information om lösenordsåterställning via Azure AD:

* [Hur gör jag för att slutföra en lyckad distribution av SSPR?](howto-sspr-deployment.md)
* [Återställ eller ändra ditt lösenord](../active-directory-passwords-update-your-own-password.md)
* [Registrera för återställning av lösenord för självbetjäning](../active-directory-passwords-reset-register.md)
* [Har du en fråga om licensiering?](concept-sspr-licensing.md)
* [Vilka data används av SSPR och vilka data bör du fylla i för dina användare?](howto-sspr-authenticationdata.md)
* [Vilka autentiseringsmetoder är tillgängliga för användarna?](concept-sspr-howitworks.md#authentication-methods)
* [Vilka principalternativ finns för SSPR?](concept-sspr-policy.md)
* [Vad är tillbakaskrivning av lösenord och vad är intresserat med det?](howto-sspr-writeback.md)
* [Hur gör jag för att rapportera på aktivitet i SSPR?](howto-sspr-reporting.md)
* [Vad är alla alternativ i SSPR och vad betyder de?](concept-sspr-howitworks.md)
* [Jag tror att något har gått sönder. Hur gör jag för att felsöka SSPR?](active-directory-passwords-troubleshoot.md)
* [Jag har en fråga som inte besvarades någon annanstans](active-directory-passwords-faq.md)

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du ställer in självbetjäning för återställning av lösenord för användarna. Fortsätta till Azure-portalen för att slutföra stegen:

> [!div class="nextstepaction"]
> [Aktivera lösenordsåterställning via självbetjäning](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset)

[Authentication]: ./media/quickstart-sspr/sspr-authentication-methods.png "Azure AD-autentiseringsmetoder som är tillgängliga och den kvantitet som krävs"
[Policy]: ./media/quickstart-sspr/password-policy.png "Lokal grupprincip för lösenord angivet till 0 dagar"

