---
title: "Azure AD Cordova komma igång | Microsoft Docs"
description: "Hur du skapar en Cordova-App som kan integreras med Azure AD för inloggning och anropar Azure AD-skyddade API: er med hjälp av OAuth."
services: active-directory
documentationcenter: 
author: vibronet
manager: mtillman
editor: 
ms.assetid: b1a8d7bd-7ad6-44d5-8ccb-5255bb623345
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 11/30/2017
ms.author: vittorib
ms.custom: aaddev
ms.openlocfilehash: b489add83a462d1d3902831d63be0b70e2443718
ms.sourcegitcommit: 234c397676d8d7ba3b5ab9fe4cb6724b60cb7d25
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/20/2017
---
# <a name="azure-ad-cordova-getting-started"></a>Azure AD Cordova komma igång
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Du kan använda Apache Cordova för att utveckla HTML5 eller JavaScript-program som kan köras på mobila enheter som fullständiga program. Med Azure Active Directory (Azure AD), kan du lägga till funktioner för autentisering i företagsklass Cordova-program.

Ett plugin-programmet Cordova radbryts Azure AD plattformsspecifika SDK: er på iOS, Android, Windows Store och Windows Phone. Genom att använda att plugin-program, kan du utöka ditt program med stöd för inloggning till Windows Server Active Directory-konton för dina användare kan få åtkomst till Office 365 och Azure API: er och även skydda anrop till dina egna anpassade webb-API.

I den här självstudiekursen kommer använder vi i Apache Cordova plugin-programmet för Active Directory Authentication Library (ADAL) för att förbättra en enkel app genom att lägga till följande funktioner:

* Autentisera en användare och få en token med bara några få rader med kod.
* Använd den token för att anropa Graph API för att fråga katalogen och visa resultatet.  
* Använd ADAL token-cache för att minimera autentisering varje gång användaren.

Om du vill göra dessa förbättringar, måste du:

1. Registrera ett program med Azure AD.
2. Lägg till kod i din app att begära token.
3. Lägg till kod för att använda token för att fråga efter Graph-API och visa resultat.
4. Skapa projekt för Cordova-distribution med alla plattformar som du vill rikta, lägga till den Cordova ADAL plugin-program och testa lösningen i emulatorerna.

## <a name="prerequisites"></a>Förutsättningar
För att slutföra den här kursen behöver du:

* En Azure AD-klient där du har ett konto med app development rättigheter.
* En utvecklingsmiljö som är konfigurerad för att använda Apache Cordova.  

Om du redan har gjort både ställa in, fortsätta direkt till steg 1.

Om du inte har en Azure AD-klient kan använda den [instruktioner om hur du skaffa en](active-directory-howto-tenant.md).

Om du inte har Apache Cordova ställa in på din dator kan du installera följande:

* [Git](http://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
* [Node.js](https://nodejs.org/download/)
* [Cordova CLI](https://cordova.apache.org/) (enkelt kan installeras via NPM Pakethanteraren: `npm install -g cordova`)

De föregående installationerna bör fungera både på datorn och på Mac.

Varje målplattform har olika förutsättningar:

* Att skapa och köra en app för Windows Surfplattors eller Windows Phone:
  * Installera [Visual Studio 2013 för Windows med Update 2 eller senare](http://www.visualstudio.com/downloads/download-visual-studio-vs#d-express-windows-8) (Express eller en annan version) eller [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs#d-community).

* Att skapa och köra en app för iOS:

  * Installera Xcode 6.x eller senare. Ladda ner det från den [Apple-utvecklare plats](http://developer.apple.com/downloads) eller [Mac App Store](http://itunes.apple.com/us/app/xcode/id497799835?mt=12).
  * Installera [ios-sim](https://www.npmjs.org/package/ios-sim). Du kan använda den för att starta iOS-appar i iOS-simulatorn från kommandoraden. (Du kan installera den via terminalen: `npm install -g ios-sim`.)
* Att skapa och köra en app för Android:

  * Installera [Java Development Kit (JDK) 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) eller senare. Kontrollera att `JAVA_HOME` (miljövariabeln) är korrekt inställd enligt JDK installationssökvägen (till exempel C:\Program Files\Java\jdk1.7.0_75).
  * Installera [Android SDK](http://developer.android.com/sdk/installing/index.html?pkg=tools) och lägga till den `<android-sdk-location>\tools` plats (till exempel C:\tools\Android\android-sdk\tools) att din `PATH` miljövariabeln.
  * Öppna Android SDK Manager (till exempel via terminalen: `android`) och installera:
    * *Android 5.0.1 (API 21)* platform SDK
    * *Android SDK Build Tools* version 19.1.0 eller senare
    * *Stöd för Android databasen* (tillägg)

  Android SDK ger inte några emulatorn standardinstansen. Skapa en genom att köra `android avd` från terminalen och sedan välja **skapa**, om du vill köra Android-appen på en emulator. Vi rekommenderar en API-nivå för 19 eller högre. Läs mer om alternativen för Android-emulatorn och skapa [AVD Manager](http://developer.android.com/tools/help/avd-manager.html) på webbplatsen för Android.

## <a name="step-1-register-an-application-with-azure-ad"></a>Steg 1: Registrera ett program med Azure AD
Det här steget är valfritt. Den här självstudiekursen innehåller företablerad värden som du kan använda för att se exemplet i åtgärden utan att behöva göra några allokering i din egen klient. Vi rekommenderar dock att du utför det här steget och bekanta dig med processen, eftersom den är obligatorisk när du skapar dina egna program.

Azure AD utfärdar tokens till bara kända program. Innan du kan använda Azure AD från din app, måste du skapa en post för den i din klient. Att registrera ett nytt program i din klient:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Klicka på ditt konto på den översta raden. I den **Directory** Välj Azure AD-klient som du vill registrera ditt program.
3. Klicka på **fler tjänster** i det vänstra fönstret och välj sedan **Azure Active Directory**.
4. Klicka på **App registreringar**, och välj sedan **Lägg till**.
5. Följ anvisningarna och skapa en **internt klientprogram**. (Men Cordova-appar är HTML-baserad, vi skapar en här native client-program. Den **internt klientprogram** alternativet måste väljas eller programmet fungerar inte.)
  * **Namnet** beskriver ditt program till användare.
  * **Omdirigerings-URI** är den URI som används för att returnera token till appen. Ange **http://MyDirectorySearcherApp**.

När du har slutfört registreringen tilldelar ett unikt ID i Azure AD till din app. Du behöver det här värdet i nästa avsnitt. Du hittar den på programfliken nyligen skapade appen.

Att köra `DirSearchClient Sample`, tilldela behörigheten nyligen skapade appen till frågor till Azure AD Graph-API:

1. Från den **inställningar** väljer **nödvändiga behörigheter**, och välj sedan **Lägg till**.  
2. Azure Active Directory-program, Välj **Microsoft Graph** som API och Lägg till den **åtkomst till katalogen som den inloggade användaren** behörighet under **delegerade behörigheter**.  Detta gör att programmet kan fråga Graph API för användare.

## <a name="step-2-clone-the-sample-app-repository"></a>Steg 2: Klona lagringsplatsen exempel app
Skriv följande kommando från din gränssnitt eller en kommandorad:

    git clone -b skeleton https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-Cordova.git

## <a name="step-3-create-the-cordova-app"></a>Steg 3: Skapa Cordova-appen
Det finns flera sätt att skapa Cordova-program. I den här kursen använder vi Cordova-kommandoradsgränssnittet (CLI).

1. Skriv följande kommando från din gränssnitt eller en kommandorad:

        cordova create DirSearchClient

   Detta kommando skapar mappstrukturen och scaffold-teknik för Cordova-projektet.

2. Gå till mappen DirSearchClient:

        cd .\DirSearchClient

3. Kopiera innehållet i projektet starter i undermappen www med hjälp av en Filhanteraren eller följande kommando i gränssnittet:

  * Windows:`xcopy ..\NativeClient-MultiTarget-Cordova\DirSearchClient www /E /Y`
  * Mac:`cp -r  ../NativeClient-MultiTarget-Cordova/DirSearchClient/* www`

4. Lägg till godkända plugin-programmet. Detta är nödvändigt för att anropa Graph API.

        cordova plugin add cordova-plugin-whitelist

5. Lägg till alla plattformar som du vill stödja. Du måste köra minst en av följande kommandon om du vill ha ett exempel på en fungerande. Observera att du inte kommer att kunna emulera iOS i Windows eller emulera Windows på en Mac.

        cordova platform add android
        cordova platform add ios
        cordova platform add windows

6. Lägg till ADAL för Cordova plugin-programmet i projektet:

        cordova plugin add cordova-plugin-ms-adal

## <a name="step-4-add-code-to-authenticate-users-and-obtain-tokens-from-azure-ad"></a>Steg 4: Lägg till kod för att autentisera användare och hämta token från Azure AD
Det program som du utvecklar i den här kursen får du en enkel directory sökfunktionen. Användaren kan ange alias för alla användare i katalogen och visualisera vissa grundläggande attribut. Start-projektet innehåller definitionen av det grundläggande användargränssnittet i appen (i www/index.html) och scaffold-teknik som dra kablar in grundläggande app händelse cykler användaren gränssnittet bindningar och resultatet visas logik (i www/js/index.js). Aktiviteten bara kvar för du är att lägga till kod som implementerar identitet uppgifter.

Det första du behöver göra i koden är införa protocol-värden som Azure AD används för att identifiera din app och resurser som du mål. Dessa värden används för att skapa token-förfrågningar vid ett senare tillfälle. Infoga följande kodavsnitt längst upp i filen index.js:

```javascript
var authority = "https://login.microsoftonline.com/common",
    redirectUri = "http://MyDirectorySearcherApp",
    resourceUri = "https://graph.windows.net",
    clientId = "a5d92493-ae5a-4a9f-bcbf-9f1d354067d3",
    graphApiVersion = "2013-11-08";
```

Den `redirectUri` och `clientId` värden måste matcha värden som beskriver din app i Azure AD. Du hittar dem från den **konfigurera** fliken i Azure-portalen, enligt beskrivningen i steg 1 tidigare i den här kursen.

> [!NOTE]
> Om du valt för att registrera en ny app inte i en egen klient kan du bara klistra in förinställda värden som är. Du se exemplet körs, men du bör alltid skapa din egen post för dina appar som är avsedda för produktion.

Lägg sedan till tokenbegäran koden. Infoga följande kodavsnitt mellan den `search` och `renderData` definitioner:

```javascript
    // Shows the user authentication dialog box if required
    authenticate: function (authCompletedCallback) {

        app.context = new Microsoft.ADAL.AuthenticationContext(authority);
        app.context.tokenCache.readItems().then(function (items) {
            if (items.length > 0) {
                authority = items[0].authority;
                app.context = new Microsoft.ADAL.AuthenticationContext(authority);
            }
            // Attempt to authorize the user silently
            app.context.acquireTokenSilentAsync(resourceUri, clientId)
            .then(authCompletedCallback, function () {
                // We require user credentials, so this triggers the authentication dialog box
                app.context.acquireTokenAsync(resourceUri, clientId, redirectUri)
                .then(authCompletedCallback, function (err) {
                    app.error("Failed to authenticate: " + err);
                });
            });
        });

    },
```
Låt oss nu undersöka som fungerar genom att bryta ned den i sina två delar.
Det här exemplet är avsedd att fungera med innehavare, till skillnad från är knutna till en viss. Slutpunkten ”/ vanliga”, som används att ange något konto vid autentisering och dirigerar begäran till klienten där det hör används.

Den här första delen av metoden kontrollerar om ADAL cacheminnet för att se om en token lagras redan. I så fall, använder metoden hyresgäster var token kom från för att ADAL. Detta är nödvändigt för att undvika extra prompter eftersom användning av ”/ vanliga” alltid resulterar i att be användaren att ange ett nytt konto.

```javascript
        app.context = new Microsoft.ADAL.AuthenticationContext(authority);
        app.context.tokenCache.readItems().then(function (items) {
            if (items.length > 0) {
                authority = items[0].authority;
                app.context = new Microsoft.ADAL.AuthenticationContext(authority);
            }
```
Den andra delen av metoden utför rätt tokenbegäran. Den `acquireTokenSilentAsync` metoden ber ADAL att returnera en token för den angivna resursen utan att visa alla UX. Som kan inträffa om cacheminnet innehåller redan en lämplig åtkomst-token som lagras, eller om en uppdateringstoken kan användas för att hämta en ny åtkomsttoken utan att visa prompten. Om som försöker misslyckas vi återställs `acquireTokenAsync`--som synligt uppmanas användaren att autentisera.

```javascript
            // Attempt to authorize the user silently
            app.context.acquireTokenSilentAsync(resourceUri, clientId)
            .then(authCompletedCallback, function () {
                // We require user credentials, so this triggers the authentication dialog box
                app.context.acquireTokenAsync(resourceUri, clientId, redirectUri)
                .then(authCompletedCallback, function (err) {
                    app.error("Failed to authenticate: " + err);
                });
            });
```
Nu när vi har token kan vi slutligen anropa Graph API och utföra frågan som vi vill. Infoga följande kodavsnitt nedan i `authenticate` definition:

```javascript
// Makes an API call to receive the user list
    requestData: function (authResult, searchText) {
        var req = new XMLHttpRequest();
        var url = resourceUri + "/" + authResult.tenantId + "/users?api-version=" + graphApiVersion;
        url = searchText ? url + "&$filter=mailNickname eq '" + searchText + "'" : url + "&$top=10";

        req.open("GET", url, true);
        req.setRequestHeader('Authorization', 'Bearer ' + authResult.accessToken);

        req.onload = function(e) {
            if (e.target.status >= 200 && e.target.status < 300) {
                app.renderData(JSON.parse(e.target.response));
                return;
            }
            app.error('Data request failed: ' + e.target.response);
        };
        req.onerror = function(e) {
            app.error('Data request failed: ' + e.error);
        }

        req.send();
    },

```
Filerna som startpunkt tillhandahålls en enkel UX för att ange alias för en användare i en textruta. Den här metoden använder värdet för att skapa en fråga, kombinera det med den åtkomst-token, skicka den till Microsoft Graph och tolka resultaten. Den `renderData` metoden som redan finns i filen startpunkt tar hand om visualisera resultat.

## <a name="step-5-run-the-app"></a>Steg 5: Kör app
Appen är slutligen klart att köras. Den är enkel: när appen startar, anger du namnet på den användare du vill leta upp och klicka sedan på knappen. Du uppmanas för autentisering. Attributen för eftersökt användare visas på lyckad autentisering och lyckad sökning.

Efterföljande körningar utför sökningen utan att visa prompten tack vare förekomst av det tidigare förvärvade token i cacheminnet.

Konkret stegen för att köra appen varierar beroende på plattform.

### <a name="windows-10"></a>Windows 10
   Surfplattors:`cordova run windows --archs=x64 -- --appx=uap`

   Mobile (kräver en Windows 10 Mobile-enhet som är ansluten till en dator):`cordova run windows --archs=arm -- --appx=uap --phone`

   > [!NOTE]
   > Under den första körningen kan du bli ombedd att logga in för utvecklarlicens. Mer information finns i [Developer licens](https://msdn.microsoft.com/library/windows/apps/hh974578.aspx).

### <a name="windows-81-tabletpc"></a>Windows 8.1 Surfplattors
   `cordova run windows`

   > [!NOTE]
   > Under den första körningen kan du bli ombedd att logga in för utvecklarlicens. Mer information finns i [Developer licens](https://msdn.microsoft.com/library/windows/apps/hh974578.aspx).

### <a name="windows-phone-81"></a>Windows Phone 8.1
   Att köra på en ansluten enhet:`cordova run windows --device -- --phone`

   Att köra på standard-emulatorn:`cordova emulate windows -- --phone`

   Använd `cordova run windows --list -- --phone` att se alla tillgängliga mål och `cordova run windows --target=<target_name> -- --phone` att köra programmet på en viss enhet eller emulator (till exempel `cordova run windows --target="Emulator 8.1 720P 4.7 inch" -- --phone`).

### <a name="android"></a>Android
   Att köra på en ansluten enhet:`cordova run android --device`

   Att köra på standard-emulatorn:`cordova emulate android`

   Kontrollera att du har skapat en instans av emulatorn med AVD Manager, enligt beskrivningen tidigare i avsnittet ”förutsättningar”.

   Använd `cordova run android --list` att se alla tillgängliga mål och `cordova run android --target=<target_name>` att köra programmet på en viss enhet eller emulator (till exempel `cordova run android --target="Nexus4_emulator"`).

### <a name="ios"></a>iOS
   Att köra på en ansluten enhet:`cordova run ios --device`

   Att köra på standard-emulatorn:`cordova emulate ios`

   > [!NOTE]
   > Kontrollera att du har den `ios-sim` paketet installeras för att köras på emulatorn. Mer information finns i avsnittet ”förutsättningar”.

    Use `cordova run ios --list` to see all available targets and `cordova run ios --target=<target_name>` to run the application on specific device or emulator (for example, `cordova run android --target="iPhone-6"`).

    Use `cordova run --help` to see additional build and run options.

## <a name="next-steps"></a>Nästa steg
För referens anger det slutförda exemplet (utan dina konfigurationsvärden) är tillgängligt i [GitHub](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-Cordova/tree/complete/DirSearchClient).

Du kan nu gå vidare till mer avancerade (och mer intressant) scenarier. Du kanske vill prova: [skydda en Node.js-webb-API med Azure AD](active-directory-devquickstarts-webapi-nodejs.md).

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
