---
title: "Logga in som inträffar med Azure AD Identity Protection | Microsoft Docs"
description: "En översikt över användarens upplevelse när identitetsskydd har minskas eller åtgärdad en användare eller när multifaktorautentisering krävs av en princip."
services: active-directory
keywords: "Azure active directory identitetsskydd, cloud app discovery, hantera program, säkerhet, risk, risknivå, säkerhetsproblem och säkerhetsprincip"
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: de5bf637-75a7-4104-b6d8-03686372a319
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: f7abbab27a6794c934344c78ba1776c8dbb97088
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="sign-in-experiences-with-azure-ad-identity-protection"></a>Logga in som inträffar med Azure AD Identity Protection
Med Azure Active Directory identitetsskydd kan du:

* användaren måste registrera sig för multifaktorautentisering
* Hantera riskfyllda inloggningar och avslöjade användare

Svaret för systemet på dessa problem påverkar på en användares inloggning eftersom bara direkt logga in med ett användarnamn och lösenord inte möjligt längre. Ytterligare steg krävs för att hämta en användare på ett säkert sätt tillbaka till företag.

Det här avsnittet ger en översikt över en användares inloggning i samtliga fall som kan uppstå.

**Multi-Factor Authentication**

* Registreringen för multifaktorautentisering

**Logga in i fara**

* Återställning för riskfyllda inloggning
* Riskfyllda inloggning blockeras
* Multifaktorautentisering registrering under en riskfyllda inloggning

**Användare i fara**

* Komprometterat kontoåterställning
* Komprometterat konto blockeras

## <a name="multi-factor-authentication-registration"></a>Registreringen för multifaktorautentisering
Den bästa användarupplevelsen för både komprometterat konto recovery trafikflöde och riskfyllda inloggning-flödet är när användaren själv kan återställa. Om användarna har registrerats för multifaktorautentisering, har de redan ett telefonnummer som är associerad med ett konto som kan användas för att överföra säkerhetsutmaningar. Ingen hjälp supportavdelningen eller administratören inblandning behövs för att återställa från kontot har komprometterats. Det har alltså bör få dina användare som har registrerats för multifaktorautentisering. 

Administratörer kan:

* Ange en princip som kräver att användare skapar sina konton för ytterligare säkerhetsverifiering. 
* Tillåt hoppa över multifaktorautentisering registrering för upp till 30 dagar, om de vill ge användarna en respitperiod innan du registrerar.

**Multifaktorautentisering registreringen har tre steg:**

1. I det första steget är att användaren får ett meddelande om krav för att ange kontot för multifaktorautentisering. 
   
    ![Reparation](./media/active-directory-identityprotection-flows/140.png "reparation")
2. Om du vill konfigurera multifaktorautentisering måste du låta systemet vet hur du vill bli kontaktad.
   
    ![Reparation](./media/active-directory-identityprotection-flows/141.png "reparation")
3. Systemet skickar en uppmaning att du och du måste svara.
   
    ![Reparation](./media/active-directory-identityprotection-flows/142.png "reparation")

## <a name="risky-sign-in-recovery"></a>Återställning för riskfyllda inloggning
När en administratör har konfigurerat en princip för inloggning risker, meddelas berörda användare när de försöker att logga in. 

**Flödet för riskfyllda inloggning har två steg:** 

1. Användaren informeras om att något annorlunda identifierades om deras inloggning, till exempel loggar in från en ny plats, enhet eller app. 
   
    ![Reparation](./media/active-directory-identityprotection-flows/120.png "reparation")
2. Användaren krävs för att bevisa sin identitet genom att lösa en säkerhetskontrollen. Om användaren har registrerats för multifaktorautentisering behöver de följa med en säkerhetskod till deras telefonnummer. Eftersom det är bara en riskfyllda inloggning, inte en komprometterad konto inte användaren att ändra lösenordet i det här flödet. 
   
    ![Reparation](./media/active-directory-identityprotection-flows/121.png "reparation")

## <a name="risky-sign-in-blocked"></a>Riskfyllda inloggning blockeras
Administratörer kan också välja att ange en princip för inloggning risken att blockera användare vid inloggning beroende på vilken risknivå av. Slutanvändare måste kontakta en administratör eller supportavdelningen om du behöver blockerad, eller de kan försöka logga in från en bekant plats eller en enhet. Automatisk återställning av lösa multifaktorautentisering är inte ett alternativ i det här fallet.

![Reparation](./media/active-directory-identityprotection-flows/200.png "reparation")

## <a name="compromised-account-recovery"></a>Komprometterat kontoåterställning
När en säkerhetsprincip för användaren risk har konfigurerats kan användare som uppfyller användaren risknivå anges i principen (och därför antas komprometteras) måste gå igenom användaren röjande recovery flödet innan de kan logga in. 

**Användaren har komprometterats recovery flödet har tre steg:**

1. Användaren informeras om att deras kontosäkerhet är i fara på grund av misstänkt aktivitet eller läcka ut autentiseringsuppgifter.
   
    ![Reparation](./media/active-directory-identityprotection-flows/101.png "reparation")
2. Användaren krävs för att bevisa sin identitet genom att lösa en säkerhetskontrollen. Om användaren har registrerats för multifaktorautentisering återställa de själva från komprometteras. De måste följa med en säkerhetskod till deras telefonnummer. 
   
   ![Reparation](./media/active-directory-identityprotection-flows/110.png "reparation")
3. Slutligen, om användaren tvingas att ändra sina lösenord eftersom någon annan kan ha haft tillgång till sitt konto. 
   Skärmdumpar av det här upplevelsen är lägre än.
   
   ![Reparation](./media/active-directory-identityprotection-flows/111.png "reparation")

## <a name="compromised-account-blocked"></a>Komprometterat konto blockeras
För att få en användare som har blockerats av en användare risk säkerhetsprincip avblockerad, måste användaren kontakta administratören eller supportavdelningen. Automatisk återställning av lösa multifaktorautentisering är inte ett alternativ i det här fallet.

![Reparation](./media/active-directory-identityprotection-flows/104.png "reparation")

## <a name="reset-password"></a>Återställa lösenord
Om avslöjade användare blockeras från att logga in kan en administratör generera ett tillfälligt lösenord för dessa. Användarna måste ändra sina lösenord under en nästa inloggning.

![Reparation](./media/active-directory-identityprotection-flows/160.png "reparation")

## <a name="see-also"></a>Se även
* [Azure Active Directory Identity Protection](active-directory-identityprotection.md) 

