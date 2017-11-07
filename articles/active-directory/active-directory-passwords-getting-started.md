---
title: 'Snabbstart: Azure AD SSPR | Microsoft Docs'
description: "Distribuera återställning av lösenord för självbetjäning i Azure AD snabbt"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: bde8799f-0b42-446a-ad95-7ebb374c3bec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 7d97fad04a0aa549d0e3a182282f898302e8e41a
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2017
---
# <a name="azure-ad-self-service-password-reset-rapid-deployment"></a>Snabb distribution av återställning av lösenord för självbetjäning i Azure AD

> [!IMPORTANT]
> **Är du här eftersom du har problem med att logga in?** I så fall är det [här som du ser hur du kan ändra och återställa ditt eget lösenord](active-directory-passwords-update-your-own-password.md).

Återställning av lösenord för självbetjäning (SSPR) erbjuder ett enkelt sätt för IT-administratörer att låta användarna återställa eller låsa upp sina lösenord eller sina konton. Systemet innehåller detaljerade rapporter för att spåra när användare använder systemet tillsammans med aviseringar som informerar om missbruk.

I den här handboken förutsätts att du redan har en aktiv utvärderingsversion eller licensierad Azure AD-klient. Om du behöver hjälp med att konfigurera Azure AD kan du läsa artikeln [Komma igång med Azure AD](https://azure.microsoft.com/trial/get-started-active-directory/).

## <a name="enable-sspr-for-your-azure-ad-tenant"></a>Aktivera SSPR för din Azure AD-klientorganisation

1. Från din befintliga Azure AD-klient väljer du **"Återställning av lösenord"**

2. På skärmen **"Egenskaper"**, under alternativet "Återställning av lösenord via självbetjäning har aktiverats" väljer du något av följande:
    * Ingen – Ingen kan använda SSPR-funktionen.
    * Valda – Endast medlemmar av en viss Azure AD-grupp som du väljer kan använda SSPR-funktionen. Vi rekommenderar att du definierar en grupp användare och använder den här inställningen när du distribuerar det för ett proof of concept (POC).
    * Alla – Alla användare med konton i din Azure AD-klient kan använda SSPR-funktionen. Vi rekommenderar att du använder den här inställningen när du är redo att distribuera den här funktionen till hela din klientorganisation när du har slutfört ett proof of concept (POC).

3. Från skärmen **"Autentiseringsmetoder"** väljer du
    * Antal metoder som krävs för återställning – Vi stöder minst en eller högst två
    * Metoder som finns tillgängliga för användare – Vi behöver minst en, men det skadar inte att ha ett tillgängligt alternativ
        * **E-post** skickar ett e-postmeddelande med en kod till användarens konfigurerade e-postadress för autentisering
        * **Mobiltelefon** ger användaren möjlighet att ta emot ett samtal eller SMS med en kod till hans/hennes konfigurerade mobiltelefonnummer
        * **Arbetstelefon** ringer användaren med en kod till användarens konfigurerade arbetstelefonnummer
        * Med **Säkerhetsfrågor** kan du välja
            * Antal frågor som krävs för registrering – Minimum för en lyckad registrering, vilket betyder att en användare kan välja att besvara flera för att skapa en pool med frågor att hämta från. Det här alternativet går att ställa in från 3 till 5 och måste vara större än eller lika med antalet frågor som krävs för att återställa.
                * Det går att lägga till anpassade frågor genom att klicka på knappen ”Custom” (Anpassad) när du väljer säkerhetsfrågor
            * Antal frågor som krävs för återställning – Kan ställas in från 3 till 5 frågor som ska besvaras korrekt innan en användares lösenord kan återställas eller låsas upp.
            
    ![Autentisering][Authentication]

4. REKOMMENDERAT: **"Anpassning"** gör att du kan ändra länken "Kontakta administratören" så att den pekar på en sida eller e-postadress som du definierar. Vi rekommenderar att du ställer in den här länken så att den dirigerar till något som en e-postadress eller en webbplats som användarna är vana vid att använda för support.

5. VALFRITT: Skärmen **"Registrering"** ger administratörer alternativ för:
    * Kräv att användare registrerar sig vid inloggning
    * Antal dagar innan användare uppmanas att bekräfta sin autentiseringsinformation

6. VALFRITT: Skärmen **"Meddelande"** ger administratörer alternativ för att:
    * Meddela användare om lösenordsåterställning
    * Meddela alla administratörer när andra administratörer återställer sina lösenord

**Nu har du konfigurerat SSPR för din Azure AD-klient**. Användarna kan nu använda instruktionerna i artiklarna [Registrera för återställning av lösenord för självbetjäning](active-directory-passwords-reset-register.md) och [Återställ eller ändra ditt lösenord](active-directory-passwords-update-your-own-password.md) för att uppdatera sina lösenord utan inblandning av administratören. Du kan avbryta här om du endast använder molnet eller fortsätta att konfigurera synkroniseringen av lösenord till en lokal AD-domän.

> [!IMPORTANT]
> Testa SSPR med en användare och inte en administratör eftersom Microsoft tillämpar starka autentiseringskrav för Azure-konton av administratörstyp. Mer information om lösenordsprinciper för administratörer finns i vår [artikel om lösenordsprinciper](active-directory-passwords-policy.md#administrator-password-policy-differences).

## <a name="configure-synchronization-to-existing-identity-source"></a>Konfigurera synkronisering till befintlig identitetskälla

För att aktivera lokal identitetssynkronisering till Azure AD måste du installera och konfigurera [Azure AD Connect](./connect/active-directory-aadconnect.md) på en server i din organisation. Det här programmet hanterar synkronisering av användare och grupper från din befintliga identitetskälla till Azure AD-klienten.

* [Uppgradera från DirSync eller AD Sync till Azure AD Connect](./connect/active-directory-aadconnect-dirsync-deprecated.md)
* [Komma igång med Azure AD Connect med standardinställningar](./connect/active-directory-aadconnect-get-started-express.md)
* [Konfigurera tillbakaskrivning av lösenord](active-directory-passwords-writeback.md#configuring-password-writeback) för att skriva lösenord från Azure AD tillbaka till din lokala katalog.

### <a name="on-premises-policy-change"></a>Ändring av lokala principer

Om du synkroniserar användare från en lokal Active Directory-domän och vill tillåta att användarna återställer sina lösenord direkt kan du göra följande ändring i din lokala lösenordsprincip:

**Datorkonfiguration** > **Principer** > **Windowsinställningar** > **Säkerhetsinställningar**  >  **Kontoprinciper** > **Lösenordsprincip**

**Lägsta ålder för lösenord** – 0 dagar

Den här säkerhetsinställningen anger tidsperioden (i dagar) som ett lösenord måste användas innan användaren kan ändra det. Genom att sätta värdet på **0 dagar** kan användare använda SSPR om deras lösenord ändrats av supportteamen.

![Princip][Policy]

## <a name="disabling-self-service-password-reset"></a>Inaktivera självbetjäningsfunktionen för återställning av lösenord

Om du vill inaktivera självbetjäningsfunktionen för återställning av lösenord öppnar du Azure AD-klienten och går till **Återställning av lösenord > Egenskaper** > och väljer **Ingen** under **Återställning av lösenord via självbetjäning har aktiverats**

### <a name="learn-more"></a>Läs mer
Följande länkar ger ytterligare information om lösenordsåterställning med Azure AD

* [Hur gör jag för att slutföra en lyckad distribution av SSPR?](active-directory-passwords-best-practices.md)
* [Återställ eller ändra ditt lösenord](active-directory-passwords-update-your-own-password.md).
* [Registrera för återställning av lösenord för självbetjäning](active-directory-passwords-reset-register.md).
* [Har du en fråga om licensiering?](active-directory-passwords-licensing.md)
* [Vilka data används av SSPR och vilka data bör du fylla i för dina användare?](active-directory-passwords-data.md)
* [Vilka autentiseringsmetoder är tillgängliga för användarna?](active-directory-passwords-how-it-works.md#authentication-methods)
* [Vilka principalternativ finns för SSPR?](active-directory-passwords-policy.md)
* [Vad är tillbakaskrivning av lösenord och vad är intresserat med det?](active-directory-passwords-writeback.md)
* [Hur gör jag för att rapportera på aktivitet i SSPR?](active-directory-passwords-reporting.md)
* [Vad är alla alternativ i SSPR och vad betyder de?](active-directory-passwords-how-it-works.md)
* [Jag tror att något har gått sönder. Hur gör jag för att felsöka SSPR?](active-directory-passwords-troubleshoot.md)
* [Jag har en fråga som inte besvarades någon annanstans](active-directory-passwords-faq.md)

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du ställer in självbetjäning för återställning av lösenord för användarna. Följ länken nedan till Azure Portal om du vill fortsätta att följa de här anvisningarna.

> [!div class="nextstepaction"]
> [Aktivera lösenordsåterställning via självbetjäning](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset)

[Authentication]: ./media/active-directory-passwords-getting-started/sspr-authentication-methods.png "Azure AD-autentiseringsmetoder som är tillgängliga och kvantitet som krävs"
[Policy]: ./media/active-directory-passwords-getting-started/password-policy.png "Lokal grupprincip för lösenord angivet till 0 dagar"
