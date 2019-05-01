---
title: Multi-Factor Authentication i Azure Active Directory B2C | Microsoft Docs
description: Hur du aktiverar Multifaktorautentisering i konsumentinriktade program som skyddas av Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 6cd9cdd2a63b91d0b72a73ba65f4fff87d2a3922
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64715746"
---
# <a name="enable-multi-factor-authentication-in-azure-active-directory-b2c"></a>Aktivera Multi-Factor authentication i Azure Active Directory B2C

Azure Active Directory (Azure AD) B2C integreras direkt med [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md) så att du kan lägga till ett andra säkerhetslager till registrering och inloggning upplevelser i dina program. Du kan aktivera multifaktorautentisering för utan att behöva skriva en enda rad kod. Om du redan har skapat logga in och logga in användaren flöden, kan du fortfarande aktivera multifaktorautentisering.

Den här funktionen hjälper dig att hantera scenarier som följande program:

- Du behöver inte multifaktorautentisering för åtkomst till ett program, men du behöver komma åt en annan. Kunden kan logga in på ett automatiskt försäkring program med ett socialt eller lokala men måste kontrollera telefonnumret innan åtkomst till hemnätverk försäkring program registrerade i samma katalog.
- Du behöver inte multifaktorautentisering komma åt ett program i allmänhet, men du behöver komma åt känsliga delar i den. Exempelvis kan kunden kan logga in på en metodparameter med en social eller lokalt konto och kontrollera vilket konto balansera, men måste verifiera telefonnumret innan du försöker en banköverföring.

## <a name="set-multi-factor-authentication"></a>Ange multifaktorautentisering

När du skapar ett användarflöde, har du möjlighet att aktivera Multi-Factor authentication.

![Ange multifaktorautentisering](./media/active-directory-b2c-reference-mfa/add-policy.png)

Ange **multifaktorautentisering** till **aktiverad**.

Du kan använda **kör användarflödet** att verifiera upplevelsen. Bekräfta följande scenario:

Ett kundkonto skapas i din klient innan steget multifaktorautentisering sker. Under steget uppmanas kunden att ange ett telefonnummer och kontrollera att den. Om verifieringen lyckas är nummer som kopplat till kontot för senare användning. Även om kunden avbryter eller släpper, kan kunden bli ombedd att verifiera ett telefonnummer igen under nästa inloggningen med aktiverat multifaktorautentisering.

## <a name="add-multi-factor-authentication"></a>Lägg till Multi-Factor authentication

Det är möjligt att aktivera multifaktorautentisering för ett användarflöde som du skapade tidigare. 

För att aktivera multifaktorautentisering:

1. Öppna användarflödet och välj sedan **egenskaper**. 
2. Bredvid **multifaktorautentisering**väljer **aktiverad**.
3. Klicka på **Spara** längst upp på sidan.


