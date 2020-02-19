---
title: Lägg till autentisering på Apache Cordova
description: Lär dig hur du använder Mobile Apps i Azure App Service för att autentisera användare av din Apache Cordova-app med identitets leverantörer som Google, Facebook, Twitter och Microsoft.
ms.assetid: 10dd6dc9-ddf5-423d-8205-00ad74929f0d
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 3714ce2a8098608851991115aa82afdc00d08a47
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/19/2020
ms.locfileid: "77459396"
---
# <a name="add-authentication-to-your-apache-cordova-app"></a>Lägg till autentisering i din Apache Cordova-app
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="summary"></a>Sammanfattning
I den här självstudien lägger du till autentisering i ToDoList snabb starts projekt på Apache Cordova med hjälp av en identitets leverantör som stöds. Den här självstudien baseras på självstudien [Kom igång med Mobile Apps] , som du måste slutföra först.

## <a name="register"></a>Registrera din app för autentisering och konfigurera App Service
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

[Titta på en video som visar liknande steg](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-8-Azure-authentication)

## <a name="permissions"></a>Begränsa behörigheter till autentiserade användare
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Nu kan du kontrol lera att anonym åtkomst till Server delen har inaktiverats. I Visual Studio:

* Öppna projektet som du skapade när du slutförde självstudien [Kom igång med Mobile Apps].
* Kör ditt program i **Google Android-emulatorn**.
* Kontrol lera att ett oväntat anslutnings fel visas när appen har startats.

Uppdatera sedan appen för att autentisera användare innan du begär resurser från Server delen för mobilappar.

## <a name="add-authentication"></a>Lägg till autentisering i appen
1. Öppna projektet i **Visual Studio**och öppna sedan `www/index.html`-filen för redigering.
2. Leta upp taggen `Content-Security-Policy` meta i head-avsnittet.  Lägg till OAuth-värden i listan över tillåtna källor.

   | Leverantör | Namn på SDK-Provider | OAuth-värd |
   |:--- |:--- |:--- |
   | Azure Active Directory | AAD | https://login.microsoftonline.com |
   | Facebook | facebook | https://www.facebook.com |
   | Google | Google | https://accounts.google.com |
   | Microsoft | microsoftaccount | https://login.live.com |
   | Twitter | Twitter | https://api.twitter.com |

    Ett exempel på en innehålls-säkerhets princip (implementerad för Azure Active Directory) är följande:

        <meta http-equiv="Content-Security-Policy" content="default-src 'self'
            data: gap: https://login.microsoftonline.com https://yourapp.azurewebsites.net; style-src 'self'">

    Ersätt `https://login.microsoftonline.com` med OAuth-värden från tabellen ovan.  Mer information om meta-taggen för innehåll – säkerhet och principer finns i [Dokumentation om säkerhets principer].

    Vissa autentiseringsproviders kräver inte innehåll-säkerhets princip ändringar när de används på lämpliga mobila enheter.  Till exempel krävs inget innehåll – säkerhets princip ändringar när du använder Google-autentisering på en Android-enhet.

3. Öppna `www/js/index.js`-filen för redigering, leta upp `onDeviceReady()`-metoden och Lägg till följande kod under klient skapande koden:

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

    Den här koden ersätter den befintliga koden som skapar tabell referensen och uppdaterar användar gränssnittet.

    Metoden login () påbörjar autentisering med providern. Metoden login () är en async-funktion som returnerar ett JavaScript-löfte.  Resten av initieringen placeras i löftes svaret så att det inte utförs förrän metoden login () har slutförts.

4. I den kod som du just har lagt till ersätter du `SDK_Provider_Name` med namnet på din inloggnings leverantör. Använd till exempel `client.login('aad')`för Azure Active Directory.
5. Kör ditt projekt.  När projektet har initierats visar programmet sidan OAuth-inloggning för den valda autentiseringsprovidern.

## <a name="next-steps"></a>Nästa steg
* Läs mer [om autentisering] med Azure App Service.
* Fortsätt självstudien genom att lägga till [push-meddelanden] till din Apache Cordova-app.

Lär dig hur du använder SDK: er.

* [Apache Cordova-SDK]
* [ASP.NET Server-SDK]
* [Node.js Server-SDK]

<!-- URLs. -->
[Kom igång med Mobile Apps]: app-service-mobile-cordova-get-started.md
[Dokumentation om säkerhets principer]: https://cordova.apache.org/docs/en/latest/guide/appdev/whitelist/index.html
[Push-meddelanden]: app-service-mobile-cordova-get-started-push.md
[Om autentisering]: app-service-mobile-auth.md
[Apache Cordova-SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server-SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server-SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
