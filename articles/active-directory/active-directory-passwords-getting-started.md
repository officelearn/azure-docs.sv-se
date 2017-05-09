---
title: 'Snabbstart: Azure AD SSPR | Microsoft-dokument'
description: "Distribuera återställning av lösenord för självbetjäning i Azure AD snabbt"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: bde8799f-0b42-446a-ad95-7ebb374c3bec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/26/2017
ms.author: joflore
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: d138f4135d008c746bd5501680392b80a51786fa
ms.contentlocale: sv-se
ms.lasthandoff: 05/03/2017


---
# <a name="quick-start-azure-ad-self-service-password-reset"></a>Snabbstart: återställning av lösenord för Azure AD-självbetjäning

## <a name="rapidly-deploy-self-service-password-reset"></a>Distribuera återställning av lösenord för självbetjäning snabbt

Återställning av lösenord för självbetjäning (SSPR) erbjuder ett enkelt sätt för IT-administratörer att låta användarna återställa eller låsa upp sina lösenord eller sina konton. Systemet innehåller detaljerade rapporter för att spåra när användare använder systemet tillsammans med aviseringar som informerar om missbruk.

I den här handboken förutsätts att du redan har en aktiv utvärderingsversion eller licensierad Azure AD-klient. Om du behöver hjälp med att konfigurera Azure AD kan du läsa artikeln [Komma igång med Azure AD](https://azure.microsoft.com/trial/get-started-active-directory/).

1. Från din befintliga Azure AD-klient väljer du **"Återställning av lösenord"**

2. På skärmen **"Egenskaper"**, under alternativet "Återställning av lösenord via självbetjäning har aktiverats" väljer du något av följande
    * Ingen – Ingen kan använda SSPR-funktionen
    * En grupp – Endast medlemmar av en viss Azure AD-grupp som du väljer kan använda SSPR-funktionen
    * Alla – Alla användare med konton i din Azure AD-klient kan använda SSPR-funktionen

3. Från skärmen **"Autentiseringsmetoder"** väljer du
    * "Antal metoder som krävs för återställning" – Vi stöder minst en eller högst två
    * "Metoder som finns tillgängliga för användare" – Vi behöver minst en, men det skadar inte att ha ett tillgängligt alternativ
        * **E-post** skickar ett e-postmeddelande med en kod till användarens konfigurerade e-postadress för autentisering
        * **Mobiltelefon** ger användaren möjlighet att ta emot ett samtal eller SMS med en kod till hans/hennes konfigurerade mobiltelefonnummer
        * **Arbetstelefon** ringer användaren med en kod till användarens konfigurerade arbetstelefonnummer
        * Med **Säkerhetsfrågor** kan du välja
            * "Antal frågor som krävs för registrering" är minimum för en lyckad registrering, vilket betyder att en användare kan välja att besvara flera för att skapa en pool med frågor att hämta från. Det här alternativet går att ställa in från 3 till 5 och måste vara större än eller lika med antalet frågor som krävs för att återställa.
            * "Antal frågor som krävs för återställning" kan ställas in från 3 till 5 frågor som ska besvaras korrekt innan en användares lösenord kan återställas eller låsas upp.
                * Det går att lägga till anpassade frågor genom att klicka på knappen ”Custom” (Anpassad) när du väljer säkerhetsfrågor

4. REKOMMENDERAT: **"Anpassning"** gör att du kan ändra länken "Kontakta administratören" så att den pekar på en sida eller e-postadress som du definierar

5. VALFRITT: Skärmen **"Registrering"** ger administratörer alternativ för:
    * "Kräv att användare registrerar vid inloggning"
    * "Antal dagar innan användarna uppmanas att bekräfta sin autentiseringsinformation"

6. VALFRITT: Skärmen **"Meddelande"** ger administratörer alternativ för att:
    * Meddela användare om lösenordsåterställning
    * Meddela alla administratörer när andra administratörer återställer sina lösenord

**Nu har du konfigurerat SSPR för din Azure AD-klient**. Du kan avbryta här eller fortsätta att konfigurera synkroniseringen av lösenord till en lokal AD-domän.

> [!NOTE]
> Testa SSPR med en användare och inte en administratör eftersom Microsoft tillämpar starka autentiseringskrav för Azure-konton av administratörstyp. Mer information om lösenordsprinciper för administratörer finns i vår [djupgående artikel](active-directory-passwords-how-it-works.md).

## <a name="configure-synchronization-to-existing-identity-source"></a>Konfigurera synkronisering till befintlig identitetskälla

För att aktivera lokal identitetssynkronisering till Azure AD måste du installera och konfigurera [Azure AD Connect](/connect/active-directory-aadconnect.md) på en server i din organisation. Det här programmet hanterar synkronisering av användare och grupper från din befintliga identitetskälla till Azure AD-domänen.

[Komma igång med Azure AD Connect med standardinställningar](/connect/active-directory-aadconnect-get-started-express.md)

[Uppgradera från DirSync eller AD Sync till Azure AD Connect](/connect/active-directory-aadconnect-dirsync-deprecated.md)

## <a name="disabling-self-service-password-reset"></a>Inaktivera självbetjäningsfunktionen för återställning av lösenord

Om du vill inaktivera självbetjäningsfunktionen för återställning av lösenord öppnar du din Azure AD-klient och går till **Återställning av lösenord**, **Egenskaper** och väljer **Ingen** under **Återställning av lösenord via självbetjäning har aktiverats**

## <a name="next-steps"></a>Nästa steg
Följande länkar ger ytterligare information om lösenordsåterställning med Azure AD

* [**Licensiering**](active-directory-passwords-licensing.md) – Konfigurera Azure AD-licensiering
* [**Data**](active-directory-passwords-data.md) – Förstå de data som krävs och hur de används för lösenordshantering
* [**Distribution**](active-directory-passwords-best-practices.md) – Planera och distribuera SSPR till dina användare med hjälp av informationen finns här
* [**Anpassa**](active-directory-passwords-customize.md) – Anpassa utseendet för företagets SSPR-funktion.
* [**Princip**](active-directory-passwords-policy.md) – Förstå och ange principer för Azure AD-lösenord
* [**Rapportering**](active-directory-passwords-reporting.md) – Identifiera om, när och var dina användare kommer åt SSPR-funktioner
* [**Teknisk djupdykning** ](active-directory-passwords-how-it-works.md) – Ta en titt bakom kulisserna för att förstå hur det hela fungerar
* [**Vanliga frågor och svar**](active-directory-passwords-faq.md) – Hur gör man? Varför? Vad? Var? Vem? När? – Svar på allt du någonsin velat fråga
* [**Felsökning** ](active-directory-passwords-troubleshoot.md) – Lär dig att lösa vanliga problem med SSPR

