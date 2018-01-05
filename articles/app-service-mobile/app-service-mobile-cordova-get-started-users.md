---
title: "Lägg till autentisering i Apache Cordova med Mobilappar | Microsoft Docs"
description: "Lär dig hur du använder Mobile Apps i Azure App Service du autentiserar användare i din Apache Cordova-app via en mängd olika identitetsleverantörer, inklusive Google, Facebook, Twitter och Microsoft."
services: app-service\mobile
documentationcenter: javascript
author: conceptdev
manager: crdun
editor: 
ms.assetid: 10dd6dc9-ddf5-423d-8205-00ad74929f0d
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 10/30/2016
ms.author: crdun
ms.openlocfilehash: b5cce832ae7ae83552c2a5ded2f5f5bda0ac76bf
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2018
---
# <a name="add-authentication-to-your-apache-cordova-app"></a>Lägg till autentisering i din Apache Cordova-app
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="summary"></a>Sammanfattning
I kursen får du lägger till autentisering todolist snabbstartsprojekt på Apache Cordova med hjälp av en stöds identitetsleverantör. Den här kursen är baserad på den [Kom igång med Mobile Apps] självstudien måste du slutföra först.

## <a name="register"></a>Registrera din app för autentisering och konfigurera App Service
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

[Titta på en video som visar liknande steg](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-8-Azure-authentication)

## <a name="permissions"></a>Begränsa behörighet för autentiserade användare
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Nu kan du kontrollera att anonym åtkomst till din serverdel har inaktiverats. I Visual Studio:

* Öppna projektet som du skapade när du har slutfört självstudien [Kom igång med Mobile Apps].
* Kör ditt program i den **Google Android-emulatorn**.
* Kontrollera att visas ett oväntat fel i anslutningen när appen startar.

Därefter uppdatera appen för att autentisera användare innan du begär resurser från serverdelen för Mobilappen.

## <a name="add-authentication"></a>Lägg till autentisering i appen
1. Öppna projektet i **Visual Studio**sedan öppnar den `www/index.html` filen för redigering.
2. Leta upp den `Content-Security-Policy` meta-tagg i head-avsnittet.  Lägg till OAuth värden i listan över tillåtna källor.

   | Leverantör | Namn på SDK-Provider | OAuth-värden |
   |:--- |:--- |:--- |
   | Azure Active Directory | aad | https://login.microsoftonline.com |
   | Facebook | Facebook | https://www.Facebook.com |
   | Google | Google | https://accounts.Google.com |
   | Microsoft | MicrosoftAccount | https://login.live.com |
   | Twitter | Twitter | https://API.Twitter.com |

    Ett exempel innehåll--säkerhetsprincip (implementerat för Azure Active Directory) är följande:

        <meta http-equiv="Content-Security-Policy" content="default-src 'self'
            data: gap: https://login.microsoftonline.com https://yourapp.azurewebsites.net; style-src 'self'">

    Ersätt `https://login.microsoftonline.com` med OAuth-värden från den föregående tabellen.  Mer information om metataggen innehåll säkerhetsprincip finns i [innehåll säkerhetsprincip dokumentationen].

    Vissa autentiseringsproviders kräver inte innehåll säkerhetsprincip ändras när den används på lämplig mobila enheter.  Inget innehåll säkerhetsprincip ändringar krävs till exempel vid användning av Google-autentisering på en Android-enhet.

3. Öppna den `www/js/index.js` filen för redigering, leta upp den `onDeviceReady()` metoden och under skapandet klienten kod lägger du till följande kod:

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

    Metoden login() startar autentisering med providern. Metoden login() är en async-funktion som returnerar en JavaScript-Promise.  Resten av initieringen placeras inuti promise svaret så att den inte körs förrän login()-metoden.

4. I den kod som du just lagt till, ersätta `SDK_Provider_Name` med namnet på leverantören inloggningen. Till exempel för Azure Active Directory, använda `client.login('aad')`.
5. Kör projektet.  När projektet har slutfört initiering, visar programmet OAuth-inloggningssidan för valda autentiseringsprovider.

## <a name="next-steps"></a>Nästa steg
* Lär dig mer [om verifiering] med Azure App Service.
* Fortsätta kursen genom att lägga till [Push-meddelanden] i din Apache Cordova-app.

Lär dig hur du använder SDK: er.

* [Apache Cordova-SDK]
* [ASP.NET Server-SDK]
* [Node.js Server-SDK]

<!-- URLs. -->
[Kom igång med Mobile Apps]: app-service-mobile-cordova-get-started.md
[innehåll säkerhetsprincip dokumentationen]: https://cordova.apache.org/docs/en/latest/guide/appdev/whitelist/index.html
[Push-meddelanden]: app-service-mobile-cordova-get-started-push.md
[om verifiering]: app-service-mobile-auth.md
[Apache Cordova-SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server-SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server-SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
