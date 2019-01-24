---
title: Tilläggsapp i Azure Active Directory B2C | Microsoft Docs
description: Återställer b2c-extensions-app.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 9/06/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: afb8e6fcc7c16594c1377e6b848e0f09a18298b1
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54842639"
---
# <a name="azure-ad-b2c-extensions-app"></a>Azure AD B2C: Tilläggsapp

När en Azure AD B2C-katalog skapas en app kallas `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.` skapas automatiskt i den nya katalogen. Den här appen, kallas de **b2c-extensions-app**, visas i *appregistreringar*. Den används av Azure AD B2C-tjänsten för att lagra information om användare och anpassade attribut. Om appen tas bort Azure AD B2C fungerar inte och kommer att påverkas din produktionsmiljö.

> [!IMPORTANT]
> Ta inte bort b2c-extensions-app om du planerar att omedelbart ta bort din klient. Om appen är borttagna under mer än 30 dagar, går användarinformation förlorade permanent.

## <a name="verifying-that-the-extensions-app-is-present"></a>Verifiera att tilläggsappen är tillgänglig

För att verifiera att b2c-extensions-app finns:

1. Klicka på på i din Azure AD B2C-klient **alla tjänster** i den vänstra navigeringsmenyn.
1. Sök efter och öppna **appregistreringar**.
1. Leta efter en app som börjar med **b2c-extensions-app**

## <a name="recover-the-extensions-app"></a>Återställa tilläggsappen

Om du av misstag har tagit bort b2c-extensions-app har 30 dagar att återställa den. Du kan återställa appen med Graph API:

1. Bläddra till [ https://graphexplorer.azurewebsites.net/ ](https://graphexplorer.azurewebsites.net/).
1. Logga in på webbplatsen som global administratör för Azure AD B2C-katalog som du vill återställa den borttagna appen för. Den här global administratör måste ha en e-postadress som liknar följande: `username@{yourTenant}.onmicrosoft.com`.
1. Utfärda en HTTP GET mot URL: en `https://graph.windows.net/myorganization/deletedApplications` med api-version = 1.6. Den här åtgärden visar en lista över alla program som har tagits bort under de senaste 30 dagarna.
1. Hitta programmet i listan där namnet börjar med ”b2c-tillägg-app” och kopiera dess `objectid` egenskapsvärdet.
1. Utfärda en HTTP POST mot URL: en `https://graph.windows.net/myorganization/deletedApplications/{OBJECTID}/restore`. Ersätt den `{OBJECTID}` i URL-Adressen med den `objectid` från föregående steg. 

Du bör nu kunna [finns i appen återställda](#verifying-that-the-extensions-app-is-present) i Azure-portalen.

> [!NOTE]
> Ett program kan bara återställas om det har tagits bort inom de senaste 30 dagarna. Om det är mer än 30 dagar, går data förlorade permanent. Öppna ett supportärende för mer hjälp.