---
title: Snabbstart för Microsoft identity-plattformen Java-webbapp | Azure
description: Lär dig hur du implementerar Microsoft-inloggning på en Java Web App med OpenID Connect
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/09/2019
ms.author: sagonzal
ms.custom: aaddev, scenarios:getting-started, languages:Java
ms.openlocfilehash: 1d73f974e2452c225220bc6f610568a2cded9887
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80473638"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-java-web-app"></a>Snabbstart: Lägga till inloggning med Microsoft i en Java-webbapp

I den här snabbstarten får du lära dig hur du integrerar ett Java-webbprogram med Microsofts identitetsplattform. Din app loggar in en användare, får en åtkomsttoken för att anropa Microsoft Graph API och göra en begäran till Microsoft Graph API.

När du har slutfört den här snabbstarten accepterar ditt program inloggningar av personliga Microsoft-konton (inklusive outlook.com, live.com och andra) och arbets- eller skolkonton från alla företag eller organisationer som använder Azure Active Directory. (Se [Hur exemplet fungerar](#how-the-sample-works) för en illustration.)

## <a name="prerequisites"></a>Krav

För att kunna köra det här exemplet behöver du:

- [Java Development Kit (JDK)](https://openjdk.java.net/) 8 eller senare, och [Maven](https://maven.apache.org/).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrera och ladda ned snabbstartsappen
> Du har två alternativ för att starta snabbstartsprogrammet: express (alternativ 1) eller manuell (alternativ 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Alternativ 1: Registrera och konfigurera appen automatiskt och ladda sedan ned ditt kodexempel
>
> 1. Gå till [Azure-portalen - Appregistreringar](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps).
> 1. Ange ett namn för programmet och välj **Registrera**.
> 1. Följ instruktionerna för att hämta och konfigurera ditt nya program automatiskt.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Alternativ 2: Registrera och konfigurera programmet och kodexemplet
>
> #### <a name="step-1-register-your-application"></a>Steg 1: Registrera ditt program
>
> Du registrerar programmet och lägger till appens registreringsinformationen i lösningen manuellt med hjälp av följande steg:
>
> 1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
> 1. Om ditt konto ger dig tillgång till fler än en klientorganisation väljer du ditt konto i det övre högra hörnet och ställer in din portalsession på önskad Azure AD-klientorganisation.
>
> 1. Navigera till sidan Microsoft identity platform för utvecklare [Appregistreringar.](/azure/active-directory/develop/)
> 1. Välj **Ny registrering**.
> 1. När sidan **Registrera ett program** visas anger du programmets registreringsinformation:
>    - I avsnittet **Namn** anger du ett beskrivande programnamn som ska visas för appens användare, till exempel `java-webapp`.
>    - Lämna **Redirect URI** tom för tillfället och välj **Registrera**.
> 1. Leta reda på **programmets program-ID och** **katalog-ID-värdena för programmet** på sidan **Översikt.** Kopiera dessa värden till senare.
> 1. Välj **Autentisering** på menyn och lägg sedan till följande information:
>    - Lägg **Redirect URIs**till `https://localhost:8080/msal4jsample/secure/aad` och `https://localhost:8080/msal4jsample/graph/me`.
>    - Välj **Spara**.
> 1. Välj **certifikat & hemligheter** från menyn och i avsnittet **Klienthemligheter** klickar du på **Ny klienthemlighet:**
>
>    - Skriv en nyckelbeskrivning (till exempel apphemlighet).
>    - Välj en **nyckelvaraktighet Om 1 år**.
>    - Nyckelvärdet visas när du väljer **Lägg till**.
>    - Kopiera värdet för nyckeln för senare. Det här nyckelvärdet visas inte igen eller kan hämtas på något annat sätt, så registrera det så snart det är synligt från Azure-portalen.
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Steg 1: Konfigurera din app i Azure-portalen
>
> För att kodexemplet för snabbstart ska fungera måste du:
>
> 1. Lägg till svarsadresser som `https://localhost:8080/msal4jsample/secure/aad` och `https://localhost:8080/msal4jsample/graph/me`.
> 1. Skapa en klienthemlighet.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Gör ändringarna åt mig]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Redan konfigurerad](media/quickstart-v2-aspnet-webapp/green-check.png) Programmet konfigureras med de här attributen.

#### <a name="step-2-download-the-code-sample"></a>Steg 2: Ladda ner kodexemplet
> [!div renderon="docs"]
> [Ladda ner kodexemplet](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Hämta projektet och extrahera zip-filen till en lokal mapp närmare rotmappen , till exempel **C:\Azure-Samples**
> 
> Om du vill använda https med localhost fyller du i egenskaperna server.ssl.key. Om du vill generera ett självsignerat certifikat använder du verktyget keytool (ingår i JRE).
>
>  ```
>   Example:
>   keytool -genkeypair -alias testCert -keyalg RSA -storetype PKCS12 -keystore keystore.p12 -storepass password
>
>   server.ssl.key-store-type=PKCS12  
>   server.ssl.key-store=classpath:keystore.p12  
>   server.ssl.key-store-password=password  
>   server.ssl.key-alias=testCert
>   ```
>   Placera den genererade keystore-filen i mappen "resurser".
   
> [!div renderon="portal" id="autoupdate" class="nextstepaction"]
> [Ladda ner kodexemplet](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-code-sample"></a>Steg 3: Konfigurera kodexemplet
> 1. Extrahera zip-filen till en lokal mapp.
> 1. Om du använder en integrerad utvecklingsmiljö öppnar du exemplet i din favorit-IDE (valfritt).
> 1. Öppna filen application.properties, som finns i mappen src/main/resources/och ersätt värdet för fälten *aad.clientId*, *aad.authority* och *aad.secretKey* med respektive värden **i program-ID,** **klient-ID** och **Klienthemlighet** som följande:
>
>    ```file
>    aad.clientId=Enter_the_Application_Id_here
>    aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
>    aad.secretKey=Enter_the_Client_Secret_Here
>    aad.redirectUriSignin=https://localhost:8080/msal4jsample/secure/aad
>    aad.redirectUriGraph=https://localhost:8080/msal4jsample/graph/me
>    aad.msGraphEndpointHost="https://graph.microsoft.com/"
>    ```
> Där:
>
> - `Enter_the_Application_Id_here` – är program-Id för programmet som du har registrerat.
> - `Enter_the_Client_Secret_Here`- är **client secret** du skapade i certifikat & **hemligheter** för det program du registrerade.
> - `Enter_the_Tenant_Info_Here`- är **värdet för katalog -ID för** det program du registrerade.
> 1. Om du vill använda https med localhost fyller du i egenskaperna server.ssl.key. Om du vill generera ett självsignerat certifikat använder du verktyget keytool (ingår i JRE).
>
>  ```
>   Example:
>   keytool -genkeypair -alias testCert -keyalg RSA -storetype PKCS12 -keystore keystore.p12 -storepass password
>
>   server.ssl.key-store-type=PKCS12  
>   server.ssl.key-store=classpath:keystore.p12  
>   server.ssl.key-store-password=password  
>   server.ssl.key-alias=testCert
>   ```
>   Placera den genererade keystore-filen i mappen "resurser".


> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-run-the-code-sample"></a>Steg 3: Kör kodexemplet
> [!div renderon="docs"]
> #### <a name="step-4-run-the-code-sample"></a>Steg 4: Kör kodexemplet

Om du vill köra projektet kan du antingen:

Kör den direkt från din IDE med hjälp av den inbäddade fjäderstartservern eller paketera den till en WAR-fil med [maven](https://maven.apache.org/plugins/maven-war-plugin/usage.html) och distribuera den till en J2EE-behållarlösning som [Apache Tomcat](http://tomcat.apache.org/).

##### <a name="running-from-ide"></a>Köra från IDE

Om du kör webbprogrammet från en IDE klickar du på kör och navigerar sedan till projektets startsida. För det här exemplet är https://localhost:8080standardadressen för startsidan .

1. På första sidan väljer du **knappen Logga in** för att omdirigera till Azure Active Directory och fråga användaren om deras autentiseringsuppgifter.

1. När användaren har autentiserats omdirigeras de till *https://localhost:8080/msal4jsample/secure/aad*. De är nu inloggade och sidan visar information om det inloggade kontot. Exempelgränssnittet har följande knappar:
    - *Logga ut:* Signerar den aktuella användaren ut ur programmet och omdirigerar dem till startsidan.
    - *Visa användarinformation:* Hämtar en token för Microsoft Graph och anropar Microsoft Graph med en begäran som innehåller token, som returnerar grundläggande information om den inloggade användaren.


   
> [!IMPORTANT]
> Det här snabbstartsprogrammet använder en klienthemlighet för att identifiera sig som en konfidentiell klient. Eftersom klienthemligheten läggs till som en oformaterad text i projektfilerna rekommenderar vi av säkerhetsskäl att du använder ett certifikat i stället för en klienthemlighet innan du överväger programmet som produktionsprogram. Mer information om hur du använder ett certifikat finns i [Certifikatautentisering för programautentisering](https://docs.microsoft.com/azure/active-directory/develop/active-directory-certificate-credentials).

## <a name="more-information"></a>Mer information

### <a name="how-the-sample-works"></a>Så här fungerar exemplet
![Visar hur exempelappen som genereras av den här snabbstarten fungerar](media/quickstart-v2-java-webapp/java-quickstart.svg)

### <a name="getting-msal"></a>Få MSAL

MSAL för Java (MSAL4J) är Java-biblioteket som används för att logga in användare och begära token som används för att komma åt ett API som skyddas av Microsofts identitetsplattform.

Lägg till MSAL4J i ditt program med maven eller Gradle för att hantera dina beroenden genom att göra följande ändringar i programmets pom.xml (Maven) eller build.gradle (Gradle) fil.

```XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>msal4j</artifactId>
    <version>1.0.0</version>
</dependency>
```

```$xslt
compile group: 'com.microsoft.azure', name: 'msal4j', version: '1.0.0'
```

### <a name="msal-initialization"></a>MSAL-initiering

Lägg till en referens till MSAL för Java genom att lägga till följande kod högst upp i filen där du kommer att använda MSAL4J:

```Java
import com.microsoft.aad.msal4j.*;
```

## <a name="next-steps"></a>Efterföljande moment

Lär dig mer om behörigheter och medgivande:

> [!div class="nextstepaction"]
> [Behörigheter och samtycke](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)

Mer information om auth-flödet för det här scenariot finns i Auktoriseringskodflödet Oauth 2.0:

> [!div class="nextstepaction"]
> [Oauth-flöde för auktoriseringskod](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow)

Hjälp oss att förbättra Microsofts identitetsplattform. Berätta vad du tycker genom att fylla i en kort två-fråga undersökning.

> [!div class="nextstepaction"]
> [Undersökning av Microsofts identitetsplattform](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
