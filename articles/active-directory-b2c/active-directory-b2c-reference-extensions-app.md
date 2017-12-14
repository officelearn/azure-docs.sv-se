---
title: "Tillägg app – Azure AD B2C | Microsoft Docs"
description: "Återställa b2c-tillägg-app"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: mtillman
editor: parakhj
ms.assetid: f0392e32-0771-473c-a799-81438ca2bcff
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 9/06/2017
ms.author: parja
ms.openlocfilehash: b28a1bb6287a0e30eda21d9a7c03abbf14b5d8d9
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="azure-ad-b2c-extensions-app"></a>Azure AD B2C: Tillägg app

När en Azure AD B2C-katalog har skapats kan en app kallas `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.` skapas automatiskt i den nya katalogen. Den här appen, kallas de **b2c-tillägg-app**, visas i *App registreringar*. Den används av tjänsten Azure AD B2C för att lagra information om användare och anpassade attribut. Om appen tas bort, Azure AD B2C kommer inte att fungera på rätt sätt och produktionsmiljön kommer att påverkas.

> [!IMPORTANT]
> Ta inte bort b2c-tillägg-app om du planerar att direkt ta bort din klient. Appen är borttagna mer än 30 dagar, kommer användarinformation att gå förlorade.

## <a name="verifying-that-the-extensions-app-is-present"></a>Verifiera att appen tillägg finns

Kontrollera att b2c-tillägg-appen är tillgänglig:

1. Klicka på på i din Azure AD B2C-klient **fler tjänster** i den vänstra navigeringsmenyn.
1. Söka efter och öppna **App registreringar**.
1. Leta efter en app som börjar med **b2c-tillägg-app**

## <a name="recover-the-extensions-app"></a>Återställa tillägg appen

Om du av misstag tas bort b2c-tillägg-app har 30 dagar att återställa den. Du kan återställa appen med Graph-API:

1. Bläddra till [https://graphexplorer.azurewebsites.net/](https://graphexplorer.azurewebsites.net/).
1. Logga in till webbplatsen som en global administratör för Azure AD B2C-katalog som du vill återställa den borttagna appen för. Den här global administratör måste ha en e-postadress som liknar följande: `username@{yourTenant}.onmicrosoft.com`.
1. Utfärda en HTTP GET mot URL: en `https://graph.windows.net/myorganization/deletedApplications` = med api-version 1.6. Den här åtgärden visar en lista över alla program som har tagits bort under de senaste 30 dagarna.
1. Sök efter programmet i listan var namnet börjar med ”b2c-tillägg-app” och kopiera dess `objectid` egenskapsvärde.
1. Utfärda en HTTP POST mot URL: en `https://graph.windows.net/myorganization/deletedApplications/{OBJECTID}/restore`. Ersätt den `{OBJECTID}` del av URL: en med den `objectid` från föregående steg. 

Du bör nu kunna [ser återställda appen](#verifying-that-the-extensions-app-is-present) i Azure-portalen.

> [!NOTE]
> Ett program kan endast återställas om det har tagits bort under de senaste 30 dagarna. Om det är mer än 30 dagar, blir data förlorade permanent. Filen ett supportärende för mer hjälp.