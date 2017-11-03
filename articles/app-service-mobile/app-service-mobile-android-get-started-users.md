---
title: "Lägg till autentisering på Android med Mobilappar | Microsoft Docs"
description: "Lär dig mer om att använda funktionen Mobilappar i Azure App Service du autentiserar användare i din Android-app via en mängd olika identitetsleverantörer, inklusive Google, Facebook, Twitter och Microsoft."
services: app-service\mobile
documentationcenter: android
author: ggailey777
manager: syntaxc4
editor: 
ms.assetid: 1fc8e7c1-6c3c-40f4-9967-9cf5e21fc4e1
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 10/01/2016
ms.author: glenga
ms.openlocfilehash: 81331142aa6110d4e29e6fb30a90ce6e3a853439
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="add-authentication-to-your-android-app"></a>Lägg till autentisering i din Android-app
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="summary"></a>Sammanfattning
I den här självstudiekursen du lägger till autentisering i todolist quickstart projektet i Android med hjälp av en stöds identitetsleverantör. Den här kursen är baserad på den [Kom igång med Mobile Apps] självstudien måste du slutföra först.

## <a name="register"></a>Registrera din app för autentisering och konfigurera Azure App Service
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Lägg till din app i tillåtna externa omdirigerings-URL

Säker autentisering måste du definiera en ny URL-schema för din app. Detta gör att autentiseringssystemet kan omdirigera tillbaka till din app när autentiseringen är klar. I den här självstudiekursen kommer vi använda URL-schemat _appname_ i hela. Du kan dock använda alla URL-schema som du väljer. Det bör vara unikt för din mobila program. Du vill aktivera omdirigering på serversidan:

1. Välj din Apptjänst i [Azure-portalen].

2. Klicka på den **autentisering / auktorisering** menyalternativet.

3. I den **tillåtna externa omdirigerings-URL: er**, ange `appname://easyauth.callback`.  Den _appname_ i den här strängen är URL-schemat för din mobila program.  Det bör följa den normala URL specifikation för ett protokoll (Använd bokstäver och siffror och börja med en bokstav).  Du bör anteckna den sträng som du väljer när du behöver justera mobilprogram koden med URL-schemat på flera platser.

4. Klicka på **OK**.

5. Klicka på **Spara**.

## <a name="permissions"></a>Begränsa behörighet för autentiserade användare
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

* Öppna projektet du slutfört med kursen i Android Studio [Kom igång med Mobile Apps]. Från den **kör** -menyn klickar du på **kör app**, och kontrollera att ett undantag med en statuskod 401 (obehörig) aktiveras när appen startar.

     Detta undantag beror på att appen försöker komma åt serverdel som en oautentiserad användare men *TodoItem* tabellen nu kräver autentisering.

Därefter uppdaterar du appen för att autentisera användare innan du begär resurser från Mobile Apps-serverdel. 

## <a name="add-authentication-to-the-app"></a>Lägg till autentisering i appen
[!INCLUDE [mobile-android-authenticate-app](../../includes/mobile-android-authenticate-app.md)]



## <a name="cache-tokens"></a>Cache-autentiseringstoken på klienten
[!INCLUDE [mobile-android-authenticate-app-with-token](../../includes/mobile-android-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Nästa steg
Nu när du har slutfört den här självstudiekursen för grundläggande autentisering, Överväg fortsätter in på något av följande kurser:

* [Lägg till push-meddelanden i appen för Android](app-service-mobile-android-get-started-push.md).
  Lär dig hur du konfigurerar Mobile Apps-serverdel för att du använder Azure notification hubs för att skicka push-meddelanden.
* [Aktivera offlinesynkronisering av din Android-app](app-service-mobile-android-get-started-offline-data.md).
  Lär dig hur du lägger till offlinestöd i appen med hjälp av en Mobile Apps-serverdel. Med offlinesynkronisering, användare kan interagera med en mobil app&mdash;visa, lägga till eller ändra data&mdash;även om det inte finns någon nätverksanslutning.

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #cache-tokens
[Refresh expired tokens]: #refresh-tokens
[Next Steps]:#next-steps


<!-- URLs. -->
[Kom igång med Mobile Apps]: app-service-mobile-android-get-started.md
