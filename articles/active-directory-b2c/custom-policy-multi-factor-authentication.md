---
title: Multi-Factor Authentication i Azure Active Directory B2C | Microsoft Docs
description: Så här aktiverar du Multi-Factor Authentication i konsumentbaserade program som skyddas av Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/30/2018
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 69096e5f650a131c5af7ec4da60b7cbca225a56f
ms.sourcegitcommit: 0820c743038459a218c40ecfb6f60d12cbf538b3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87116608"
---
# <a name="enable-multi-factor-authentication-in-azure-active-directory-b2c"></a>Aktivera Multi-Factor Authentication i Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) integreras direkt med [Azure-Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md) så att du kan lägga till ett andra säkerhets lager till inloggnings-och inloggnings upplevelser i dina program. Du aktiverar Multi-Factor Authentication utan att skriva en enda rad kod. Om du redan har skapat inloggnings-och inloggnings användar flöden kan du fortfarande aktivera Multi-Factor Authentication.

Den här funktionen hjälper program att hantera scenarier som följande:

- Du behöver inte Multi-Factor Authentication för att komma åt ett program, men du behöver det för att få åtkomst till ett annat. Kunden kan till exempel Logga in på ett program för automatisk försäkring med ett socialt eller lokalt konto, men måste kontrol lera telefonnumret innan du får åtkomst till programmet Home Insurance som är registrerat i samma katalog.
- Du behöver inte Multi-Factor Authentication för att få åtkomst till ett program i allmänhet, men du behöver det för att få åtkomst till de känsliga delarna i det. Kunden kan till exempel Logga in på ett bank program med ett socialt eller lokalt konto och kontrol lera kontots saldo, men det måste kontrol lera telefonnumret innan du försöker utföra en överföring.

## <a name="set-multi-factor-authentication"></a>Ange Multi-Factor Authentication

När du skapar ett användar flöde har du möjlighet att aktivera Multi-Factor Authentication.

![Ange Multi-Factor Authentication](./media/custom-policy-multi-factor-authentication/add-policy.png)

Ange **multifaktorautentisering** till **aktive rad**.

Du kan använda **Kör användar flöde** för att verifiera upplevelsen. Bekräfta följande scenario:

Ett kund konto skapas i din klient organisation innan Multi-Factor Authentication-steget inträffar. Under steget uppmanas kunden att ange ett telefonnummer och kontrol lera det. Om verifieringen lyckas är telefonnumret kopplat till kontot för senare användning. Även om kunden avbryter eller släpper ut, kan kunden uppmanas att verifiera ett telefonnummer igen vid nästa inloggning med Multi-Factor Authentication aktiverat.

## <a name="add-multi-factor-authentication"></a>Lägg till Multi-Factor Authentication

Det är möjligt att aktivera Multi-Factor Authentication på ett användar flöde som du skapade tidigare.

Så här aktiverar du Multi-Factor Authentication:

1. Öppna användar flödet och välj sedan **Egenskaper**.
2. Bredvid **multifaktorautentisering**väljer du **aktive rad**.
3. Klicka på **Spara** längst upp på sidan.


