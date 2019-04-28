---
title: Lägg till autentisering på Android med Mobile Apps | Microsoft Docs
description: Lär dig hur du använder funktionen Mobile Apps i Azure App Service du autentiserar användare i din Android-app genom olika identitetsleverantörer, inklusive Google, Facebook, Twitter och Microsoft.
services: app-service\mobile
documentationcenter: android
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 1fc8e7c1-6c3c-40f4-9967-9cf5e21fc4e1
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 11/16/2017
ms.author: crdun
ms.openlocfilehash: 7b80c1148cf2716e71308d953ac445c4bb50cbc5
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62119783"
---
# <a name="add-authentication-to-your-android-app"></a>Lägg till autentisering i din Android-app
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="summary"></a>Sammanfattning
I den här självstudien får du lägga till autentisering till snabbstartsprojektet todolist på Android med hjälp av en identitetsprovider som stöds. Den här självstudien är baserad på den [Kom igång med Mobile Apps] kursen måste du slutföra först.

## <a name="register"></a>Registrera din app för autentisering och konfigurera Azure App Service
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Lägg till din app i de tillåtna externa Omdirigeringswebbadresser

Säker autentisering måste du definiera en ny URL-schema för din app. På så sätt kan autentiseringssystem att omdirigera tillbaka till din app när autentiseringen är klar. I den här självstudien använder vi URL-schema _appname_ i hela. Du kan dock använda alla URL-schema som du väljer. Det bör vara unikt för det mobila programmet. Aktivera omdirigering på serversidan:

1. I den [Azure Portal], Välj din App Service.

2. Klicka på den **autentisering / auktorisering** menyalternativ.

3. I den **tillåtna externa omdirigerings-URL: er**, ange `appname://easyauth.callback`.  Den _appname_ i den här strängen är URL-schemat för din mobilapp.  Den bör följa den normala URL specifikationen för ett protokoll (Använd bokstäver och siffror och börja med en bokstav).  Du bör anteckna den sträng som du väljer eftersom du behöver ändra programkoden mobila med URL-schema på flera platser.

4. Klicka på **OK**.

5. Klicka på **Spara**.

## <a name="permissions"></a>Begränsa behörighet för autentiserade användare
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

* Öppna projektet du slutfört med självstudien i Android Studio [Kom igång med Mobile Apps]. Från den **kör** -menyn klickar du på **kör app**, och kontrollera att ett ohanterat undantag med en statuskod 401 (obehörig) aktiveras när appen startar.

     Det här undantaget inträffar eftersom appen försöker få åtkomst till backend-server som en oautentiserad användare men *TodoItem* tabell nu kräver autentisering.

Därefter uppdaterar du appen för att autentisera användare innan du begär resurser från Mobile Apps-serverdel.

## <a name="add-authentication-to-the-app"></a>Lägg till autentisering i appen
[!INCLUDE [mobile-android-authenticate-app](../../includes/mobile-android-authenticate-app.md)]



## <a name="cache-tokens"></a>Cache-autentiseringstoken på klienten
[!INCLUDE [mobile-android-authenticate-app-with-token](../../includes/mobile-android-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Nästa steg
Nu när du har slutfört den här självstudien för grundläggande autentisering, Överväg fortsätter in på någon av följande självstudiekurser:

* [Lägg till push-meddelanden till din Android-app](app-service-mobile-android-get-started-push.md).
  Lär dig hur du konfigurerar din Mobile Apps-serverdelen så att du använder Azure notification hubs för att skicka push-meddelanden.
* [Aktivera synkronisering offline för din Android-app](app-service-mobile-android-get-started-offline-data.md).
  Lär dig hur du lägger till offlinestöd i appen med hjälp av en Mobile Apps-serverdel. Med offlinesynkronisering kan användare kan interagera med en mobil app&mdash;visa, lägga till eller ändra data&mdash;även om det inte finns någon nätverksanslutning.

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #cache-tokens
[Refresh expired tokens]: #refresh-tokens
[Next Steps]:#next-steps


<!-- URLs. -->
[Kom igång med Mobile Apps]: app-service-mobile-android-get-started.md
[Azure Portal]: https://portal.azure.com/
