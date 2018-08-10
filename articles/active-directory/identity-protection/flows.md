---
title: Logga in som inträffar med Azure AD Identity Protection | Microsoft Docs
description: Innehåller en översikt över användarens upplevelse när Identity Protection har minimerats eller åtgärdad en användare eller när multifaktorautentisering krävs av en princip.
services: active-directory
keywords: Azure active directory identity protection kan cloud app discovery, hantering av program, säkerhet, risk, risknivå, säkerhetsproblem, säkerhetsprincip
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: de5bf637-75a7-4104-b6d8-03686372a319
ms.service: active-directory
ms.component: conditional-access
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 3c1c681e2c7ccd6b5fd3eaa3639853d99cb1e0b7
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2018
ms.locfileid: "40005219"
---
# <a name="sign-in-experiences-with-azure-ad-identity-protection"></a>Logga in upplevelser med Azure AD Identity Protection
Med Azure Active Directory Identity Protection kan du:

* Kräv att användare att registrera sig för multifaktorautentisering
* Hantera riskfyllda inloggningar och komprometterade användare

Svaret hos systemet till de här problemen påverkar på en användares inloggning eftersom direkt logga in genom att ange ett användarnamn och lösenord går inte längre. Ytterligare steg krävs för att hämta en användare på ett säkert sätt tillbaka till företag.

Den här artikeln ger en översikt över en användares inloggning för samtliga fall som kan uppstå.

**Multi-Factor Authentication**

* Registrering för multifaktorautentisering

**Logga in i fara**

* Riskfylld inloggning återställning
* Riskfylld inloggning blockerad
* Registrering av multifaktorautentisering under en riskfylld inloggning

**Användare i farozonen**

* Komprometterat kontoåterställning
* Komprometterade kontot har spärrats

## <a name="multi-factor-authentication-registration"></a>Registrering för multifaktorautentisering
Den bästa användarupplevelsen för både komprometterat konto recovery flödet och riskfyllda inloggningen-flödet är när användaren kan själv återställa. Om användarna har registrerats för multifaktorautentisering, har de redan ett telefonnummer som är associerad med ett konto som kan användas för att skicka säkerhetsutmaningar. Ingen hjälp supportavdelningen eller administratören inblandning behövs för att återställa från kontot kompromettering. Vi rekommenderar därför starkt att ge användarna som har registrerats för multifaktorautentisering. 

Administratörer kan ange en princip som kräver att användare skapar sina konton för ytterligare säkerhetsverifiering. Den här principen kan du hoppa över registrering för multifaktorautentisering i upp till 14 dagar. 14 dagars respitperiod kan inte konfigureras.

**Registrering av multifaktorautentisering har tre steg:**

1. I det första steget är att användaren får ett meddelande om kravet på att ange kontot för multifaktorautentisering. 
   
    ![Reparation](./media/flows/140.png "reparation")
2. Om du vill konfigurera multifaktorautentisering behöver så att systemet vet hur du vill bli kontaktad.
   
    ![Reparation](./media/flows/141.png "reparation")
3. Systemet skickar en utmaning att du och du måste svara.
   
    ![Reparation](./media/flows/142.png "reparation")

## <a name="risky-sign-in-recovery"></a>Riskfylld inloggning återställning
När en administratör har konfigurerat en princip för inloggning risker, meddelas de berörda användarna när de försöker logga in. 

**Flödet för riskfyllda inloggningen har två steg:** 

1. Användaren informeras om att något annorlunda har identifierats om deras inloggning, till exempel loggar in från en ny plats, enhet eller app. 
   
    ![Reparation](./media/flows/120.png "reparation")
2. Användaren måste bekräfta sin identitet med att lösa en säkerhetskontroll. Om användaren har registrerats för multifaktorautentisering som de behöver för att avrunda utlösas en säkerhetskod till sina telefonnummer. Eftersom det är bara en riskfyllda inloggningar och inte ett komprometterat konto inte användaren att ändra lösenordet i det här flödet. 
   
    ![Reparation](./media/flows/121.png "reparation")

## <a name="risky-sign-in-blocked"></a>Riskfylld inloggning blockerad
Administratörer kan också välja att skapa en princip för Inloggningsrisk att blockera användare vid inloggning beroende på risknivån. För att få blockerad, måste slutanvändarna kontakta en administratör eller supporten eller de kan logga in från en bekant plats eller enhet. Automatisk återställning av lösa multifaktorautentisering är inte ett alternativ i det här fallet.

![Reparation](./media/flows/200.png "reparation")

## <a name="compromised-account-recovery"></a>Komprometterat kontoåterställning
När en riskprincip har konfigurerats kan användare som uppfyller användaren risknivå anges i principen (och därför antas komprometteras) måste gå igenom användarflödet kompromettering recovery innan de kan logga in. 

**Användarflödet kompromettering recovery har tre steg:**

1. Användaren informeras om att deras kontosäkerhet finns det risk på grund av misstänkt aktivitet eller läcka ut autentiseringsuppgifter.
   
    ![Reparation](./media/flows/101.png "reparation")
2. Användaren måste bekräfta sin identitet med att lösa en säkerhetskontroll. Om användaren har registrerats för multifaktorautentisering kan de själva återställa från äventyras. Användaren uppmanas att avrunda sätta en säkerhetskod till sina telefonnummer. 
   
   ![Reparation](./media/flows/110.png "reparation")
3. Slutligen, om användaren tvingas att ändra sina lösenord eftersom någon annan kan ha haft åtkomst till sitt konto. 
   Skärmbilder för den här finns nedan.
   
   ![Reparation](./media/flows/111.png "reparation")

## <a name="compromised-account-blocked"></a>Komprometterade kontot har spärrats
För att få en användare som har blockerats av en riskprincip avblockerad, måste du kontakta administratören eller supportavdelningen. Automatisk återställning av lösa multifaktorautentisering är inte ett alternativ i det här fallet.

![Reparation](./media/flows/104.png "reparation")

## <a name="reset-password"></a>Återställa lösenord
Om komprometterade användare blockeras från att logga in, kan en administratör skapa ett tillfälligt lösenord för dessa. Användarna måste ändra sina lösenord under en nästa inloggning.

![Reparation](./media/flows/160.png "reparation")

## <a name="see-also"></a>Se också
* [Azure Active Directory Identity Protection](../active-directory-identityprotection.md) 

