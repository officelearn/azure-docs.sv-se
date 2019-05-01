---
title: Lägg till ett internt klientprogram - Azure Active Directory B2C | Microsoft Docs
description: Lär dig hur du lägger till ett internt klientprogram till din Active Directory B2C-klient.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.author: davidmu
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: conceptual
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 772b6b8a4d71b7d1c6418651ee0a4ee7b03af0cc
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64703916"
---
# <a name="add-a-native-client-application-to-your-azure-active-directory-b2c-tenant"></a>Lägg till ett internt klientprogram till din Azure Active Directory B2C-klient

Inbyggd klient resurser måste vara registrerade i klientorganisationen innan ditt program kan kommunicera med Azure Active Directory B2C.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Se till att du använder den katalog som innehåller din Azure AD B2C-klientorganisation genom att klicka på **katalog- och prenumerationsfiltret** på den översta menyn och välja katalogen som innehåller din klientorganisation.
3. Välj **Alla tjänster** på menyn uppe till vänster i Azure Portal. Sök sedan efter och välj **Azure AD B2C**.
1. Välj **Program** och därefter **Lägg till**.
2. Ange ett namn på programmet. Till exempel *nativeapp1*.
3. För **Ta med webbapp/webb-API** väljer du **Nej**.
4. För **Ta med intern klient** väljer du **Ja**.
5. För **Omdirigerings-URI** anger du en giltig omdirigerings-URI med ett anpassat schema. Det finns två viktiga saker att tänka på när du väljer omdirigerings-URI:

    - **Unicitet** – schemat för omdirigerings-URI:n måste vara unikt för varje program. I det här exemplet är `com.onmicrosoft.contoso.appname://redirect/path`, `com.onmicrosoft.contoso.appname` schemat. Du måste följa det här mönstret. Om två program delar samma schema får användaren välja program. Inloggningen misslyckas om användaren väljer fel.
    - **Fullständighet** – omdirigerings-URI:n måste ha ett schema och en sökväg. Sökvägen måste innehålla minst ett snedstreck efter domänen. Exempelvis så fungerar `//contoso/` medan `//contoso` misslyckas. Se till att omdirigerings-URI:n inte innehåller några specialtecken, som understreck.

6. Klicka på **Skapa**.
7. Anteckna program-ID som du använder när du konfigurerar native client-program på egenskapssidan.
