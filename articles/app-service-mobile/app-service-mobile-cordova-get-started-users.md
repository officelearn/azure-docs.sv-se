---
title: Lägga till autentisering på Apache Cordova
description: Lär dig hur du använder mobilappar i Azure App Service för att autentisera användare av din Apache Cordova-app med identitetsleverantörer som Google, Facebook, Twitter och Microsoft.
ms.assetid: 10dd6dc9-ddf5-423d-8205-00ad74929f0d
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 3714ce2a8098608851991115aa82afdc00d08a47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77459396"
---
# <a name="add-authentication-to-your-apache-cordova-app"></a>Lägga till autentisering i din Apache Cordova-app
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="summary"></a>Sammanfattning
I den här självstudien lägger du till autentisering i snabbstartsprojektet för todolist på Apache Cordova med hjälp av en identitetsprovider som stöds. Den här självstudien baseras på [självstudien Kom igång med mobilappar,] som du måste slutföra först.

## <a name="register-your-app-for-authentication-and-configure-the-app-service"></a><a name="register"></a>Registrera appen för autentisering och konfigurera App-tjänsten
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

[Titta på en video som visar liknande steg](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-8-Azure-authentication)

## <a name="restrict-permissions-to-authenticated-users"></a><a name="permissions"></a>Begränsa behörigheter till autentiserade användare
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Nu kan du kontrollera att anonym åtkomst till din backend har inaktiverats. I Visual Studio:

* Öppna projektet som du skapade när du slutförde självstudien [Kom igång med mobilappar].
* Kör ditt program i **Google Android Emulator**.
* Kontrollera att ett oväntat anslutningsfel visas när appen har startats.

Uppdatera sedan appen för att autentisera användare innan du begär resurser från mobile app-backend.

## <a name="add-authentication-to-the-app"></a><a name="add-authentication"></a>Lägga till autentisering i appen
1. Öppna projektet i **Visual Studio** `www/index.html` och öppna sedan filen för redigering.
2. Leta `Content-Security-Policy` reda på metataggen i huvudavsnittet.  Lägg till OAuth-värden i listan över tillåtna källor.

   | Leverantör | Namn på SDK-provider | OAuth-värd |
   |:--- |:--- |:--- |
   | Azure Active Directory | Aad | https://login.microsoftonline.com |
   | Facebook | Facebook | https://www.facebook.com |
   | Google | Google | https://accounts.google.com |
   | Microsoft | microsoftaccount (microsoftaccount) | https://login.live.com |
   | Twitter | Twitter | https://api.twitter.com |

    Ett exempel på Content-Security-Policy (implementerad för Azure Active Directory) är följande:

        <meta http-equiv="Content-Security-Policy" content="default-src 'self'
            data: gap: https://login.microsoftonline.com https://yourapp.azurewebsites.net; style-src 'self'">

    Ersätt `https://login.microsoftonline.com` med OAuth-värden från tabellen föregående.  Mer information om metataggen innehållssäkerhetsprincip finns i [dokumentationen för Content-Security-Policy].

    Vissa autentiseringsleverantörer kräver inte ändringar av Content-Security-Policy när de används på lämpliga mobila enheter.  Till exempel krävs inga ändringar av content-security-policy när du använder Googles autentisering på en Android-enhet.

3. Öppna `www/js/index.js` filen för redigering, `onDeviceReady()` leta reda på metoden och lägg till följande kod under klientverktyget:

        // Login to the service
        client.login('SDK_Provider_Name')
            .then(function () {

                // BEGINNING OF ORIGINAL CODE

                // Create a table reference
                todoItemTable = client.getTable('todoitem');

                // Refresh the todoItems
                refreshDisplay();

                // Wire up the UI Event Handler for the Add Item
                $('#add-item').submit(addItemHandler);
                $('#refresh').on('click', refreshDisplay);

                // END OF ORIGINAL CODE

            }, handleError);

    Den här koden ersätter den befintliga koden som skapar tabellreferensen och uppdaterar användargränssnittet.

    Metoden login() börjar autentisering med providern. Metoden login() är en asynkron funktion som returnerar ett JavaScript-löfte.  Resten av initieringen placeras inuti löftets svar så att det inte körs förrän metoden login() har slutförts.

4. Ersätt `SDK_Provider_Name` med namnet på din inloggningsleverantör i koden som du just har lagt till. Använd till exempel `client.login('aad')`för Azure Active Directory .
5. Kör ditt projekt.  När projektet har initierats visar programmet inloggningssidan för OAuth för den valda autentiseringsleverantören.

## <a name="next-steps"></a><a name="next-steps"></a>Nästa steg
* Läs mer [om autentisering] med Azure App Service.
* Fortsätt självstudien genom att lägga till [push-meddelanden] i din Apache Cordova-app.

Lär dig hur du använder SDK: er.

* [Apache Cordova-SDK]
* [ASP.NET Server-SDK]
* [Node.js Server-SDK]

<!-- URLs. -->
[Komma igång med mobilappar]: app-service-mobile-cordova-get-started.md
[Dokumentation av content-security-policy]: https://cordova.apache.org/docs/en/latest/guide/appdev/whitelist/index.html
[Push-meddelanden]: app-service-mobile-cordova-get-started-push.md
[Om autentisering]: app-service-mobile-auth.md
[Apache Cordova-SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server-SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server-SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
