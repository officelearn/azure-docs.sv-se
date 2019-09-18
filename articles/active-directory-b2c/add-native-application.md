---
title: Lägg till ett internt klient program – Azure Active Directory B2C | Microsoft Docs
description: Lär dig hur du lägger till ett internt klient program till din Active Directory B2C-klient.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: conceptual
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 6fc953d5c6109fbc6eacbd946ecd112db4639fa5
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064580"
---
# <a name="add-a-native-client-application-to-your-azure-active-directory-b2c-tenant"></a>Lägga till ett internt klient program till din Azure Active Directory B2C-klient

Interna klient resurser måste registreras i din klient organisation innan ditt program kan kommunicera med Azure Active Directory B2C.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din klient.
3. Välj **Alla tjänster** på menyn uppe till vänster i Azure Portal. Sök sedan efter och välj **Azure AD B2C**.
1. Välj **Program** och därefter **Lägg till**.
2. Ange ett namn på programmet. Till exempel *nativeapp1*.
3. För **Ta med webbapp/webb-API** väljer du **Nej**.
4. För **Ta med intern klient** väljer du **Ja**.
5. För **Omdirigerings-URI** anger du en giltig omdirigerings-URI med ett anpassat schema. Det finns två viktiga saker att tänka på när du väljer omdirigerings-URI:

    - **Unicitet** – schemat för omdirigerings-URI:n måste vara unikt för varje program. I det här exemplet är `com.onmicrosoft.contoso.appname://redirect/path`, `com.onmicrosoft.contoso.appname` schemat. Du måste följa det här mönstret. Om två program delar samma schema får användaren välja program. Inloggningen misslyckas om användaren väljer fel.
    - **Fullständighet** – omdirigerings-URI:n måste ha ett schema och en sökväg. Sökvägen måste innehålla minst ett snedstreck efter domänen. Exempelvis så fungerar `//contoso/` medan `//contoso` misslyckas. Se till att omdirigerings-URI:n inte innehåller några specialtecken, som understreck.

6. Klicka på **Skapa**.
7. På sidan Egenskaper registrerar du det program-ID som du ska använda när du konfigurerar det interna klient programmet.
