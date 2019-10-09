---
title: Lägg till autentisering på Apache Cordova med Mobile Apps | Microsoft Docs
description: Lär dig hur du använder Mobile Apps i Azure App Service för att autentisera användare av din Apache Cordova-app via olika identitets leverantörer, inklusive Google, Facebook, Twitter och Microsoft.
services: app-service\mobile
documentationcenter: javascript
author: elamalani
manager: crdun
editor: ''
ms.assetid: 10dd6dc9-ddf5-423d-8205-00ad74929f0d
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: d5e124c6f73285efdaef515deba5816093a27e98
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72027561"
---
# <a name="add-authentication-to-your-apache-cordova-app"></a>Lägg till autentisering i din Apache Cordova-app
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> Visual Studio App Center stöder utveckling av mobila appar från slut punkt till slut punkt och integrerade tjänster. Utvecklare kan använda **bygge**-, **test** -och **distributions** tjänster för att konfigurera kontinuerlig integrering och leverans pipeliner. När appen har distribuerats kan utvecklare övervaka status och användning av appen med hjälp av **analys** -och **diagnos** tjänster och engagera med användare med **push** -tjänsten. Utvecklare kan också utnyttja **auth** för att autentisera sina användare och **data** tjänster för att spara och synkronisera AppData i molnet.
> Om du vill integrera moln tjänster i ditt mobil program kan du registrera dig med App Center [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) idag.

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
1. Öppna projektet i **Visual Studio**och öppna sedan filen `www/index.html` för redigering.
2. Leta upp taggen `Content-Security-Policy` i head-avsnittet.  Lägg till OAuth-värden i listan över tillåtna källor.

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

3. Öppna filen `www/js/index.js` för redigering, leta upp metoden `onDeviceReady()` och Lägg till följande kod under koden för att skapa klienter:

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

4. I den kod som du just har lagt till ersätter du `SDK_Provider_Name` med namnet på din inloggnings leverantör. Använd till exempel `client.login('aad')` för Azure Active Directory.
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
