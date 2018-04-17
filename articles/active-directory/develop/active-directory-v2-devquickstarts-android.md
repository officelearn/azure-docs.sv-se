---
title: Azure Active Directory v2.0 Android-app | Microsoft Docs
description: Hur du skapar en Android-app som loggar in användare med både personliga Microsoft-konto och arbete eller skola konton och Graph API-anrop med hjälp av tredjeparts-bibliotek.
services: active-directory
documentationcenter: ''
author: danieldobalian
manager: mtillman
editor: ''
ms.assetid: 16294c07-f27d-45c9-833f-7dbb12083794
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2017
ms.author: dadobali
ms.custom: aaddev
ms.openlocfilehash: b1c30362a7b14c8f7f0c44d911c46c491b3de3c0
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2018
---
# <a name="add-sign-in-to-an-android-app-using-a-third-party-library-with-graph-api-using-the-v20-endpoint"></a>Lägga till inloggning till en Android-app med hjälp av en tredjeparts-bibliotek med Graph API: et med v2.0-slutpunkten
Microsofts identitetsplattform använder öppna standarder som OAuth2 och OpenID Connect. Utvecklare kan använda alla bibliotek som de vill integrera med våra tjänster. Vi har skrivit några genomgång som detta att demonstrera hur du konfigurerar tredjeparts-bibliotek för att ansluta till Microsoft identity-plattformen för att hjälpa utvecklare att använda vår plattform med andra bibliotek. De flesta bibliotek som implementerar [RFC6749 OAuth2-specifikationen](https://tools.ietf.org/html/rfc6749) kan ansluta till Microsoft identity-plattformen.

Med det program som skapar den här genomgången, användare logga in i organisationen och sök sedan efter sig själva i organisationen med hjälp av Graph API.

Om du har använt OAuth2 eller OpenID Connect eventuellt mycket av det här exempelkonfiguration ingen vits till dig. Vi rekommenderar att du läser [2.0 protokoll - OAuth 2.0 auktorisering kod flöda](active-directory-v2-protocols-oauth-code.md) för bakgrunden.

> [!NOTE]
> Vissa funktioner i vår plattform som har ett uttryck i OAuth2 eller OpenID Connect standarder, till exempel villkorlig åtkomst och hantering av Intune måste du använda våra Microsoft Azure identitet bibliotek med öppen källkod.
> 
> 

V2.0-slutpunkten har inte stöd för alla Azure Active Directory-scenarier och funktioner.

> [!NOTE]
> Läs mer om för att avgöra om du ska använda v2.0-slutpunkten [v2.0 begränsningar](active-directory-v2-limitations.md).
> 
> 

## <a name="download-the-code-from-github"></a>Hämta koden från GitHub
Koden för den här självstudiekursen [finns på GitHub](https://github.com/Azure-Samples/active-directory-android-native-oidcandroidlib-v2).  Om du vill följa med kan du [ladda ned appens stomme som en .zip](https://github.com/Azure-Samples/active-directory-android-native-oidcandroidlib-v2/archive/skeleton.zip) eller klona stommen:

```
git clone --branch skeleton git@github.com:Azure-Samples/active-directory-android-native-oidcandroidlib-v2.git
```

Du kan också hämta exempelfilerna och komma igång nu direkt:

```
git@github.com:Azure-Samples/active-directory-android-native-oidcandroidlib-v2.git
```

## <a name="register-an-app"></a>Registrera en app
Skapa en ny app på den [programregistreringsportalen](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), eller följer detaljerade anvisningar på [hur du registrerar en app med v2.0-slutpunkten](active-directory-v2-app-registration.md).  Se till att:

* Kopiera den **program-Id** som har tilldelats din app eftersom du behöver den snart.
* Lägg till den **Mobile** plattform för din app.

> Obs: Programregistreringsportalen ger en **omdirigerings-URI** värde. I det här exemplet måste du dock använda standardvärdet för `https://login.microsoftonline.com/common/oauth2/nativeclient`.
> 
> 

## <a name="download-the-nxoauth2-third-party-library-and-create-a-workspace"></a>Hämta NXOAuth2 från tredje part biblioteket och skapa en arbetsyta
Den här genomgången använder OIDCAndroidLib från GitHub, vilket är en OAuth2-biblioteket baserat på OpenID Connect-koden för Google. Den implementerar interna programprofilen och stöder autentiseringsslutpunkt för användaren. Detta är allt som du behöver integrera med Microsoft identity-plattformen.

Klona lagringsplatsen OIDCAndroidLib till datorn.

```
git@github.com:kalemontes/OIDCAndroidLib.git
```

![androidStudio](../media/active-directory-android-native-oidcandroidlib-v2/emotes-url.png)

## <a name="set-up-your-android-studio-environment"></a>Konfigurera din miljö för Android Studio
1. Skapa ett nytt Android Studio-projekt och acceptera standardinställningarna i guiden.
   
    ![Skapa nytt projekt i Android Studio](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample1.PNG)
   
    ![Mål Android-enheter](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample2.PNG)
   
    ![Lägga till en aktivitet till mobile](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample3.PNG)
2. Flytta klonade lagringsplatsen till projektets plats om du vill konfigurera ditt projektmoduler. Du kan också skapa projektet och klona den direkt till projektets plats.
   
    ![Projektmoduler](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample4_1.PNG)
3. Öppna projektet moduler inställningar med hjälp av snabbmenyn eller genom att använda kortkommandot Ctrl + Alt + Maj + S.
   
    ![Moduler Projektinställningar](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample4.PNG)
4. Ta bort modulen standard appen eftersom du bara vill projektinställningar för behållaren.
   
    ![Modulen standard app](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample5.PNG)
5. Importera moduler från klonade lagringsplatsen till det aktuella projektet.
   
    ![Importera gradle projekt](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample6.PNG) ![Skapa ny modulsida](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample7.PNG)
6. Upprepa dessa steg för den `oidlib-sample` modul.
7. Kontrollera oidclib-beroenden på den `oidlib-sample` modul.
   
    ![oidclib beroenden på modulen oidlib-exempel](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample8.PNG)
8. Klicka på **OK** och vänta tills gradle-synkroniseringen.
   
    Din settings.gradle bör se ut som:
   
    ![Skärmbild av settings.gradle](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample8_1.PNG)
9. Skapa sample-appen för att se till att provet körs korrekt.
   
    Du kan inte användas med Azure Active Directory ännu. Vi behöver att konfigurera vissa slutpunkter först. Detta är att se till att du inte har en Android Studio problem innan vi börjar anpassa sample-appen.
10. Skapa och köra `oidlib-sample` som mål i Android Studio.
    
    ![Förlopp för oidlib sampel build](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample9.png)
11. Ta bort den `app ` katalogen som har lämnat när modulen bort från projektet eftersom Android Studio inte bort för säkerhet.
    
    ![Filstruktur som innehåller programkatalogen](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample12.PNG)
12. Öppna den **redigera konfigurationer** du vill ta bort kör konfigurationen lämnades även när du har tagit bort modulen från projektet.
    
    ![Redigera konfigurationer menyn](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample10.PNG)
    ![kör konfigurationen av appen](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample11.PNG)

## <a name="configure-the-endpoints-of-the-sample"></a>Konfigurera slutpunkter i exempel
Nu när du har den `oidlib-sample` kör har vi att redigera vissa slutpunkter för att få den här arbeta med Azure Active Directory.

### <a name="configure-your-client-by-editing-the-oidcclientconfxml-file"></a>Konfigurera din klient genom att redigera filen oidc_clientconf.xml
1. Eftersom du använder OAuth2 flöden bara för att hämta en token och anropa Graph API angett att klienten ska göra OAuth2 enbart. OIDC kommer i ett senare exempel.
   
    ```xml
        <bool name="oidc_oauth2only">true</bool>
    ```
2. Konfigurera din klient-ID som du har fått från portalen för registrering.
   
    ```xml
        <string name="oidc_clientId">86172f9d-a1ae-4348-aafa-7b3e5d1b36f5</string>
        <string name="oidc_clientSecret"></string>
    ```
3. Konfigurera omdirigerings-URI med nedan.
   
    ```xml
        <string name="oidc_redirectUrl">https://login.microsoftonline.com/common/oauth2/nativeclient</string>
    ```
4. Konfigurera scope som du behöver för att komma åt Graph API.
   
    ```xml
        <string-array name="oidc_scopes">
            <item>openid</item>
            <item>https://graph.microsoft.com/User.Read</item>
            <item>offline_access</item>
        </string-array>
    ```

Den `User.Read` värde i `oidc_scopes` kan du läsa de grundläggande profilinformation den signerade i användare.
Du kan lära dig mer om alla tillgängliga scope på [behörighetsomfattningen för Microsoft Graph](https://graph.microsoft.io/docs/authorization/permission_scopes).

Om du vill ha information om `openid` eller `offline_access` som scope i OpenID Connect finns [2.0 protokoll - OAuth 2.0 auktorisering kod flöda](active-directory-v2-protocols-oauth-code.md).

### <a name="configure-your-client-endpoints-by-editing-the-oidcendpointsxml-file"></a>Konfigurera dina klientslutpunkter för din genom att redigera filen oidc_endpoints.xml
* Öppna den `oidc_endpoints.xml` filen och gör följande ändringar:
  
    ```xml
    <!-- Stores OpenID Connect provider endpoints. -->
    <resources>
        <string name="op_authorizationEnpoint">https://login.microsoftonline.com/common/oauth2/v2.0/authorize</string>
        <string name="op_tokenEndpoint">https://login.microsoftonline.com/common/oauth2/v2.0/token</string>
        <string name="op_userInfoEndpoint">https://www.example.com/oauth2/userinfo</string>
        <string name="op_revocationEndpoint">https://www.example.com/oauth2/revoketoken</string>
    </resources>
    ```

Dessa slutpunkter bör aldrig ändras om du använder OAuth2 som din protokoll.

> [!NOTE]
> Slutpunkterna för `userInfoEndpoint` och `revocationEndpoint` stöds inte för närvarande av Azure Active Directory. Om du lämnar dessa med example.com standardvärdet för att påminna dig att de inte är tillgängliga i exemplet :-)
> 
> 

## <a name="configure-a-graph-api-call"></a>Konfigurera ett Graph API-anrop
* Öppna den `HomeActivity.java` filen och gör följande ändringar:
  
    ```Java
       //TODO: set your protected resource url
        private static final String protectedResUrl = "https://graph.microsoft.com/v1.0/me/";
    ```

Ett enkelt Graph API-anrop returnerar här våra information.

Det här är de ändringar som du behöver göra. Kör den `oidlib-sample` program och klicka på **logga in**.

När du har autentiserats, Välj den **begära skyddade resursen** knappen för att testa samtalet för Graph API.

## <a name="get-security-updates-for-our-product"></a>Hämta säkerhetsuppdateringar för vår produkt
Vi rekommenderar att du få meddelanden om säkerhetsincidenter genom att besöka den [säkerhet TechCenter](https://technet.microsoft.com/security/dd252948) och prenumerera på Security Advisory-aviseringar.

