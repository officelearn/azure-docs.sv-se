---
title: Lägga till autentisering på Android
description: Lär dig hur du använder Azure App Service för att autentisera användare av din Android-app med identitetsleverantörer som Google, Facebook, Twitter och Microsoft.
ms.assetid: 1fc8e7c1-6c3c-40f4-9967-9cf5e21fc4e1
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 705ebb5809840155e6bbf3f8eef091eb95f63e63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461648"
---
# <a name="add-authentication-to-your-android-app"></a>Lägga till autentisering i din Android-app
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="summary"></a>Sammanfattning
I den här självstudien lägger du till autentisering i snabbstartsprojektet för todolist på Android med hjälp av en identitetsprovider som stöds. Den här självstudien baseras på [självstudien Kom igång med mobilappar,] som du måste slutföra först.

## <a name="register-your-app-for-authentication-and-configure-azure-app-service"></a><a name="register"></a>Registrera din app för autentisering och konfigurera Azure App Service
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="add-your-app-to-the-allowed-external-redirect-urls"></a><a name="redirecturl"></a>Lägga till appen i url:erna för tillåtna externa omdirigering

Säker autentisering kräver att du definierar ett nytt URL-schema för din app. Detta gör det möjligt för autentiseringssystemet att omdirigera tillbaka till din app när autentiseringsprocessen är klar. I den här självstudien använder vi _url-schemats appnamn_ hela tiden. Du kan dock använda vilket URL-schema du vill. Den ska vara unik för din mobilapplikation. Så här aktiverar du omdirigering på serversidan:

1. Välj din App-tjänst i [Azure-portalen.]

2. Klicka på **menyalternativet Autentisering/auktorisering.**

3. Ange i **url:erna Tillåten extern omdirigering** `appname://easyauth.callback`.  _Appnamnet_ i den här strängen är URL-schemat för ditt mobilapplikation.  Den bör följa normal URL-specifikation för ett protokoll (använd endast bokstäver och siffror och börja med en bokstav).  Du bör anteckna strängen som du väljer eftersom du måste justera din mobilprogramkod med URL-schemat på flera ställen.

4. Klicka på **OK**.

5. Klicka på **Spara**.

## <a name="restrict-permissions-to-authenticated-users"></a><a name="permissions"></a>Begränsa behörigheter till autentiserade användare
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

* Öppna projektet som du slutförde med självstudien [Kom igång med Mobilappar]i Android Studio. Klicka på **Kör-appen**på **Menyn Kör** och kontrollera att ett ohanterat undantag med statuskoden 401 (Obehörig) utlöses när appen har startats.

     Det här undantaget beror på att appen försöker komma åt serverdelen som en oautentiserade användare, men *tabellen TodoItem* kräver nu autentisering.

Därefter uppdaterar du appen för att autentisera användare innan du begär resurser från sluten start för mobilappar.

## <a name="add-authentication-to-the-app"></a>Lägga till autentisering i appen
[!INCLUDE [mobile-android-authenticate-app](../../includes/mobile-android-authenticate-app.md)]



## <a name="cache-authentication-tokens-on-the-client"></a><a name="cache-tokens"></a>Cacheautentiseringstoken på klienten
[!INCLUDE [mobile-android-authenticate-app-with-token](../../includes/mobile-android-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Nästa steg
Nu när du har slutfört den här grundläggande autentiseringsstudien bör du fortsätta till någon av följande självstudier:

* [Lägg till push-meddelanden i din Android-app](app-service-mobile-android-get-started-push.md).
  Lär dig hur du konfigurerar din bärbara mobilapp för att använda Azure-meddelandehubbar för att skicka push-meddelanden.
* [Aktivera offlinesynkronisering för din Android-app](app-service-mobile-android-get-started-offline-data.md).
  Läs om hur du lägger till offlinesupport i appen med hjälp av en backend för mobilappar. Med offlinesynkronisering kan användare&mdash;interagera med en mobilapp&mdash;som visar, lägger till eller ändrar data även när det inte finns någon nätverksanslutning.

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #cache-tokens
[Refresh expired tokens]: #refresh-tokens
[Next Steps]:#next-steps


<!-- URLs. -->
[Komma igång med mobilappar]: app-service-mobile-android-get-started.md
[Azure-portal]: https://portal.azure.com/
