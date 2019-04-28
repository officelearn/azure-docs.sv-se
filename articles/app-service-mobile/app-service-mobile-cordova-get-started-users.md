---
title: Lägg till autentisering i Apache Cordova med Mobile Apps | Microsoft Docs
description: Lär dig hur du använder Mobile Apps i Azure App Service du autentiserar användare i din Apache Cordova-app genom olika identitetsleverantörer, inklusive Google, Facebook, Twitter och Microsoft.
services: app-service\mobile
documentationcenter: javascript
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 10dd6dc9-ddf5-423d-8205-00ad74929f0d
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 10/30/2016
ms.author: crdun
ms.openlocfilehash: 23b5967782cf237ed5af2b802aabbbf9c2f781e7
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62114218"
---
# <a name="add-authentication-to-your-apache-cordova-app"></a>Lägg till autentisering i din Apache Cordova-app
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="summary"></a>Sammanfattning
I den här självstudien lägger du till autentisering till snabbstartsprojektet todolist på Apache Cordova med en identitetsprovider som stöds. Den här självstudien är baserad på den [Kom igång med Mobile Apps] kursen måste du slutföra först.

## <a name="register"></a>Registrera din app för autentisering och konfigurera App Service
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

[Titta på en video som visar liknande steg](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-8-Azure-authentication)

## <a name="permissions"></a>Begränsa behörighet för autentiserade användare
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Nu kan du kontrollera att anonym åtkomst till din serverdel har inaktiverats. I Visual Studio:

* Öppna projektet som du skapade när du har slutfört självstudien [Kom igång med Mobile Apps].
* Kör ditt program i den **Google Android-emulatorn**.
* Kontrollera att ett oväntat fel i anslutningen visas när appen startar.

Därefter uppdatera appen för att autentisera användare innan du begär resurser från serverdelen för Mobilappen.

## <a name="add-authentication"></a>Lägg till autentisering i appen
1. Öppna projektet i **Visual Studio**, öppna sedan den `www/index.html` filen för redigering.
2. Leta upp den `Content-Security-Policy` meta-tagg i head-avsnittet.  Lägga till OAuth-värden i listan över tillåtna källor.

   | Leverantör | Providernamn för SDK | OAuth-värd |
   |:--- |:--- |:--- |
   | Azure Active Directory | aad | https://login.microsoftonline.com |
   | Facebook | facebook | https://www.facebook.com |
   | Google | google | https://accounts.google.com |
   | Microsoft | microsoftaccount | https://login.live.com |
   | Twitter | twitter | https://api.twitter.com |

    Ett exempel innehåll-Security-Policy (implementerat för Azure Active Directory) är följande:

        <meta http-equiv="Content-Security-Policy" content="default-src 'self'
            data: gap: https://login.microsoftonline.com https://yourapp.azurewebsites.net; style-src 'self'">

    Ersätt `https://login.microsoftonline.com` med OAuth-värden från den föregående tabellen.  Mer information om metataggen innehåll säkerhetsprincip finns i den [innehåll säkerhetsprincip dokumentation].

    Vissa autentiseringsproviders kräver inte innehåll säkerhetsprincip ändras när de används på lämplig mobila enheter.  Inget innehåll säkerhetsprincip ändringar krävs till exempel när du använder Google-autentisering på en Android-enhet.

3. Öppna den `www/js/index.js` filen för redigering, leta upp den `onDeviceReady()` metoden och under klientskapandet kod lägger du till följande kod:

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

    Den här koden ersätter den befintliga koden som skapar tabellreferensen och uppdaterar Användargränssnittet.

    Metoden login() startar autentisering med providern. Metoden login() är en async-funktion som returnerar en JavaScript-löftet.  Resten av initieringen placeras i det promise svaret så att den inte körs förrän login()-metoden har slutförts.

4. I den kod som du just lade till ersätter `SDK_Provider_Name` med namnet på din inloggningsprovider. Till exempel för Azure Active Directory, använda `client.login('aad')`.
5. Köra projektet.  När projektet har slutfört initiering, visar programmets OAuth-inloggningssidan för den valda authentication-providern.

## <a name="next-steps"></a>Nästa steg
* Läs mer [Om autentisering] med Azure App Service.
* Fortsätta självstudien genom att lägga till [Push-meddelanden] till din Apache Cordova-app.

Lär dig hur du använder SDK: er.

* [Apache Cordova-SDK]
* [ASP.NET Server-SDK]
* [Node.js Server-SDK]

<!-- URLs. -->
[Kom igång med Mobile Apps]: app-service-mobile-cordova-get-started.md
[Innehåll säkerhetsprincip dokumentation]: https://cordova.apache.org/docs/en/latest/guide/appdev/whitelist/index.html
[Push-meddelanden]: app-service-mobile-cordova-get-started-push.md
[Om autentisering]: app-service-mobile-auth.md
[Apache Cordova-SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server-SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server-SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
