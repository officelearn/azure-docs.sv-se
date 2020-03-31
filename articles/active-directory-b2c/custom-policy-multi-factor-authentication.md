---
title: Multifaktorautentisering i Azure Active Directory B2C | Microsoft-dokument
description: Aktivera multifaktorautentisering i konsumentinriktade program som skyddas av Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 25724ba82e57c5e3800fa1a989dd4f504df1c163
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189284"
---
# <a name="enable-multi-factor-authentication-in-azure-active-directory-b2c"></a>Aktivera multifaktorautentisering i Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) integreras direkt med [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md) så att du kan lägga till ett andra säkerhetslager för att registrera dig och logga in i dina program. Du aktiverar multifaktorautentisering utan att skriva en enda kodrad. Om du redan har skapat registrerings- och inloggningsanvändarflöden kan du fortfarande aktivera multifaktorautentisering.

Den här funktionen hjälper program att hantera scenarier som följande:

- Du behöver inte multifaktorautentisering för att komma åt ett program, men du behöver det för att komma åt ett annat. Kunden kan till exempel logga in på ett bilförsäkringsprogram med ett socialt eller lokalt konto, men måste verifiera telefonnumret innan du öppnar hemförsäkringsprogrammet som är registrerat i samma katalog.
- Du behöver inte multifaktorautentisering för att komma åt ett program i allmänhet, men du behöver den för att komma åt de känsliga delarna i det. Kunden kan till exempel logga in på ett bankprogram med ett socialt eller lokalt konto och kontrollera kontosaldot, men måste verifiera telefonnumret innan du försöker göra en banköverföring.

## <a name="set-multi-factor-authentication"></a>Ange multifaktorautentisering

När du skapar ett användarflöde har du möjlighet att aktivera multifaktorautentisering.

![Ange multifaktorautentisering](./media/custom-policy-multi-factor-authentication/add-policy.png)

Ange **multifaktorautentisering** till **Aktiverad**.

Du kan använda **Kör användarflöde** för att verifiera upplevelsen. Bekräfta följande scenario:

Ett kundkonto skapas i din klient innan multifaktorautentiseringssteget inträffar. Under steget uppmanas kunden att ange ett telefonnummer och verifiera det. Om verifieringen lyckas kopplas telefonnumret till kontot för senare användning. Även om kunden avbryter eller hoppar av kan kunden uppmanas att verifiera ett telefonnummer igen under nästa inloggning med multifaktorautentisering aktiverad.

## <a name="add-multi-factor-authentication"></a>Lägga till multifaktorautentisering

Det är möjligt att aktivera multifaktorautentisering på ett användarflöde som du tidigare har skapat.

Så här aktiverar du multifaktorautentisering:

1. Öppna användarflödet och välj sedan **Egenskaper**.
2. Välj **Aktiverad bredvid** **Multifactor-autentisering**.
3. Klicka på **Spara** längst upp på sidan.


