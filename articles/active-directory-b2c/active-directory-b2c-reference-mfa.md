---
title: Multi-Factor Authentication i Azure Active Directory B2C | Microsoft Docs
description: Hur du aktiverar Multifaktorautentisering i konsumentinriktade program som skyddas av Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 3d18e1b2e45aba4e83989e29c533cfc7bf5033fc
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37442716"
---
# <a name="azure-active-directory-b2c-enable-multi-factor-authentication-in-your-consumer-facing-applications"></a>Azure Active Directory B2C: Aktivera Multi-Factor Authentication i dina konsumentinriktade program
Azure Active Directory (Azure AD) B2C integreras direkt med [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md) så att du kan lägga till ett andra säkerhetslager till registrering och inloggning upplevelser i dina konsumentinriktade program. Och du kan göra detta utan att behöva skriva en enda rad kod. Vi stöder för närvarande telefonsamtal och SMS-verifiering. Du kan aktivera Multifaktorautentisering även om du redan har skapat principer för registrering och inloggning.

> [!NOTE]
> Multifaktorautentisering kan även aktiveras när du skapar principer för registrering och inloggning, inte bara genom att redigera befintliga principer.
> 
> 

Den här funktionen hjälper dig att hantera scenarier som följande program:

* Du behöver inte Multi-Factor Authentication att få åtkomst till ett program, men du behöver komma åt en annan. Konsumenten kan logga in på ett automatiskt försäkring program med ett socialt eller lokala men måste kontrollera telefonnumret innan åtkomst till hemnätverk försäkring program registrerade i samma katalog.
* Du behöver inte Multifaktorautentisering komma åt ett program i allmänhet, men du behöver komma åt känsliga delar i den. Konsumenten kan logga in på en metodparameter med en social eller till ett lokalt konto och kontrollera saldo men måste verifiera telefonnumret innan du försöker en banköverföring.

## <a name="modify-your-sign-up-policy-to-enable-multi-factor-authentication"></a>Ändra principen för registrering om du vill aktivera Multi-Factor Authentication
1. [Följ dessa steg för att gå till B2C-funktionsbladet på Azure portal](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
2. Klicka på **Registreringsprinciper**.
3. Klicka på registreringsprincipen (till exempel ”B2C_1_SiUp”) för att öppna den.
4. Klicka på **multifaktorautentisering** och aktivera den **tillstånd** till **på**. Klicka på **OK**.
5. Klicka på **spara** överst på bladet.

Du kan använda funktionen ”Kör nu” i principen för att kontrollera hur lösenordsåterställningen går till. Kontrollera följande:

Ett konsumentkonto skapas det i din katalog innan Multi-Factor Authentication-steg sker. Under steget uppmanas användaren att ange sitt telefonnummer och kontrollera att den. Om verifieringen lyckas är nummer som kopplat till konsumentkonto för senare användning. Även om användaren avbryter eller minskar, kan han eller hon bli ombedd att verifiera ett telefonnummer igen under nästa inloggningen (med aktiverat Multifaktorautentisering).

## <a name="modify-your-sign-in-policy-to-enable-multi-factor-authentication"></a>Ändra din princip om du vill aktivera Multi-Factor Authentication
1. [Följ dessa steg för att gå till B2C-funktionsbladet på Azure portal](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
2. Klicka på **inloggningsprinciper**.
3. Klicka på din inloggningsprincip (till exempel ”B2C_1_SiIn”) för att öppna den. Klicka på **redigera** överst på bladet.
4. Klicka på **multifaktorautentisering** och aktivera den **tillstånd** till **på**. Klicka på **OK**.
5. Klicka på **spara** överst på bladet.

Du kan använda funktionen ”Kör nu” i principen för att kontrollera hur lösenordsåterställningen går till. Kontrollera följande:

När användaren loggar in (med en social eller till ett lokalt konto), om ett verifierat telefonnummer är kopplad till konsumentkonto, han eller hon uppmanas att verifiera den. Om utan telefonnummer kopplas uppmanas konsumenten att ange en och kontrollera att den. Vid lyckad kontroll är telefonnumret kopplat till konsumentkonto för senare användning.

## <a name="multi-factor-authentication-on-other-policies"></a>Multifaktorautentisering på andra principer
Enligt beskrivningen för registrering och inloggning i principer som ovan, det är också möjligt att aktivera multifaktorautentisering för registrering eller inloggningsprinciper och lösenordsåterställning principer. Den blir tillgänglig snart på profilredigeringsprinciper.

