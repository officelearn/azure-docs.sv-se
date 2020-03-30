---
title: Tilläggsapp i Azure Active Directory B2C | Microsoft-dokument
description: Återställa b2c-tillägg-appen.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/06/2017
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 547b625996a65999c32c1b73699e3b408be01de3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188604"
---
# <a name="azure-ad-b2c-extensions-app"></a>Azure AD B2C: Tilläggsapp

När en Azure AD B2C-katalog `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.` skapas skapas en app som anropas automatiskt i den nya katalogen. Den här appen, som kallas **b2c-extensions-app,** visas i *Appregistreringar*. Den används av Azure AD B2C-tjänsten för att lagra information om användare och anpassade attribut. Om appen tas bort kommer Azure AD B2C inte att fungera korrekt och din produktionsmiljö påverkas.

> [!IMPORTANT]
> Ta inte bort b2c-tillägg-appen om du inte planerar att omedelbart ta bort din klient. Om appen förblir borttagen i mer än 30 dagar går användarinformationen förlorad permanent.

## <a name="verifying-that-the-extensions-app-is-present"></a>Verifiera att tilläggsappen finns

Så här kontrollerar du att b2c-extensions-appen finns:

1. I din Azure AD B2C-klient klickar du på **Alla tjänster** i menyn för vänster navigering.
1. Sök efter och öppna **Appregistreringar**.
1. Leta efter en app som börjar med **b2c-extensions-app**

## <a name="recover-the-extensions-app"></a>Återställa tilläggsappen

Om du av misstag har tagit bort b2c-extensions-appen har du 30 dagar på dig att återställa den. Du kan återställa appen med graph API:

1. Bläddra [https://graphexplorer.azurewebsites.net/](https://graphexplorer.azurewebsites.net/)till .
1. Logga in på webbplatsen som global administratör för Azure AD B2C-katalogen som du vill återställa den borttagna appen för. Den här globala administratören måste ha `username@{yourTenant}.onmicrosoft.com`en e-postadress som liknar följande: .
1. Utfärda en HTTP GET `https://graph.windows.net/myorganization/deletedApplications` mot URL:en med api-version=1.6. Den här åtgärden kommer att lista alla program som har tagits bort under de senaste 30 dagarna.
1. Hitta programmet i listan där namnet börjar med 'b2c-extension-app' och kopiera dess `objectid` egenskapsvärde.
1. Utfärda ett HTTP-INLÄGG `https://graph.windows.net/myorganization/deletedApplications/{OBJECTID}/restore`mot webbadressen . Ersätt `{OBJECTID}` den del av `objectid` URL:en som från föregående steg.

Du bör nu kunna [se den återställda appen](#verifying-that-the-extensions-app-is-present) i Azure-portalen.

> [!NOTE]
> Ett program kan bara återställas om det har tagits bort under de senaste 30 dagarna. Om det har gått mer än 30 dagar går data förlorade permanent. Om du vill ha mer hjälp kan du lämna in en supportbiljett.
