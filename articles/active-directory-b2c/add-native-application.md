---
title: Lägg till ett internt klientprogram - Azure Active Directory B2C | Microsoft Docs
description: Lär dig hur du lägger till ett internt klientprogram till din Active Directory B2C-klient.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.author: davidmu
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: conceptual
ms.service: active-directory
ms.component: B2C
ms.openlocfilehash: ee3323ec23b37318dbd80c85d6dd7515ce1ce06b
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2019
ms.locfileid: "55757703"
---
# <a name="add-a-native-client-application-to-your-azure-active-directory-b2c-tenant"></a>Lägg till ett internt klientprogram till din Azure Active Directory B2C-klient

Inbyggd klient resurser måste vara registrerade i klientorganisationen innan ditt program kan kommunicera med Azure Active Directory B2C.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Kontrollera att du använder den katalog som innehåller din Azure AD B2C-klient genom att klicka på den **katalog- och prenumerationsfilter** i den översta menyn och välja den katalog som innehåller din klient.
3. Välj **alla tjänster** i det övre vänstra hörnet av Azure-portalen och Sök efter och välj **Azure AD B2C**.
1. Välj **program**, och välj sedan **Lägg till**.
2. Ange ett namn för programmet. Till exempel *nativeapp1*.
3. För **ta med webbapp / webb-API**väljer **nr**.
4. För **ta med intern klient**väljer **Ja**.
5. För **omdirigerings-URI**, ange en giltig omdirigerings-URI med ett anpassat schema. Det finns två viktiga överväganden när du väljer en omdirigerings-URI:

    - **Unikt** -schemat för omdirigerings-URI måste vara unikt för varje program. I det här exemplet `com.onmicrosoft.contoso.appname://redirect/path`, `com.onmicrosoft.contoso.appname` är schemat. Det här mönstret ska följas. Om två program delar samma schema, ges användaren kan välja ett program. Om användaren gör ett felaktigt val, misslyckas inloggningen.
    - **Fullständig** -omdirigerings-URI måste ha ett schema och en sökväg. Sökvägen måste innehålla minst ett snedstreck efter domänen. Till exempel `//contoso/` fungerar och `//contoso` misslyckas. Kontrollera att omdirigeringen-URI inte innehåller specialtecken, till exempel understreck.

6. Klicka på **Skapa**.
7. Anteckna program-ID som du använder när du konfigurerar native client-program på egenskapssidan.
