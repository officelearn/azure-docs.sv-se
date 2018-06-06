---
title: Multi-Factor Authentication i Azure Active Directory B2C | Microsoft Docs
description: Hur du aktiverar Multifaktorautentisering i konsumentinriktade program som skyddas av Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 12/06/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 85592275c13719fec0f40eb76a9bce48890a59d2
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34710201"
---
# <a name="azure-active-directory-b2c-enable-multi-factor-authentication-in-your-consumer-facing-applications"></a>Azure Active Directory B2C: Aktivera Multi-Factor Authentication i dina konsumentinriktade program
Azure Active Directory (AD Azure) B2C integreras direkt med [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md) så att du kan lägga till ett andra säkerhetslager upplevelserna registrering och inloggning i dina konsumentinriktade program. Och du kan göra detta utan att skriva en enda rad kod. Vi stöder för närvarande telefonsamtal och SMS-verifiering. Du kan aktivera Multifaktorautentisering även om du redan har skapat principer för registrering och inloggning.

> [!NOTE]
> Multifaktorautentisering kan även aktiveras när du skapar principer för registrering och inloggning, inte bara genom att redigera befintliga principer.
> 
> 

Den här funktionen kan hantera scenarier, till exempel följande program:

* Du behöver Multi-Factor Authentication att få åtkomst till ett program, men du behöver komma åt en annan. Konsumenten kan logga in på ett insurance program automatiskt med ett sociala eller lokala konto men måste kontrollera telefonnumret innan åtkomst till programmet hem insurance registrerade i samma katalog.
* Du behöver Multifaktorautentisering komma åt ett program i allmänhet, men du behöver komma åt känsliga delar i den. Konsumenten kan logga in på en metodparameter med sociala eller lokala konto och kontrollera kontosaldo men telefonnumret måste kontrollera innan du försöker utföra en överföring-överföring.

## <a name="modify-your-sign-up-policy-to-enable-multi-factor-authentication"></a>Ändra principen om du vill aktivera Multifaktorautentisering för registrering
1. [Följ dessa steg för att gå till B2C-funktionsbladet på Azure portal](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
2. Klicka på **Registreringsprinciper**.
3. Klicka på principen för registrering (till exempel ”B2C_1_SiUp”) för att öppna den.
4. Klicka på **multifaktorautentisering** och aktivera den **tillstånd** till **på**. Klicka på **OK**.
5. Klicka på **spara** längst upp på bladet.

Du kan använda funktionen ”Kör nu” för principen för att verifiera användarfunktioner. Kontrollera följande:

Ett konsumentkonto skapas i katalogen innan steget Multifaktorautentisering sker. Under steget blir konsumenten ombedd att ange sitt telefonnummer och verifiera den. Om verifieringen lyckas är telefonnumret kopplad till konsumentkonto för senare användning. Även om användaren avbryter eller minskar, kan han eller hon bli ombedd att verifiera ett telefonnummer igen under den nästa inloggningen (med Multi-Factor Authentication har aktiverats).

## <a name="modify-your-sign-in-policy-to-enable-multi-factor-authentication"></a>Ändra din princip för att aktivera Multi-Factor Authentication
1. [Följ dessa steg för att gå till B2C-funktionsbladet på Azure portal](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
2. Klicka på **inloggning principer**.
3. Klicka på din inloggningsprincip (till exempel ”B2C_1_SiIn”) för att öppna den. Klicka på **redigera** längst upp på bladet.
4. Klicka på **multifaktorautentisering** och aktivera den **tillstånd** till **på**. Klicka på **OK**.
5. Klicka på **spara** längst upp på bladet.

Du kan använda funktionen ”Kör nu” för principen för att verifiera användarfunktioner. Kontrollera följande:

När användaren loggar in (med ett sociala eller lokala konto), om verifierade telefonnummer kopplas till kontot konsumenten kan han eller hon blir ombedd att verifiera den. Om inget telefonnummer är kopplad blir konsumenten ombedd att ange en och kontrollera den. Om verifieringen lyckas, är telefonnumret kopplad till konsumentkonto för senare användning.

## <a name="multi-factor-authentication-on-other-policies"></a>Multifaktorautentisering på andra principer
Enligt beskrivningen för registrering och inloggning i principer som ovan, det är också möjligt att aktivera multifaktorautentisering för registrering eller inloggning principer och lösenord för återställning av principer. Den blir tillgänglig snart på profilen ändra principer.

