---
title: Snabb start för Microsoft Identity Platform Java Web Apps | Azure
description: Lär dig hur du implementerar Microsoft-inloggning på en Java-webbapp med OpenID Connect
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
ms.openlocfilehash: ed105ce6bd1d7d8980799049649b8d5b95dcb761
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "81536122"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-java-web-app"></a>Snabb start: lägga till inloggning med Microsoft i en Java-webbapp

I den här snabb starten får du lära dig hur du integrerar ett Java-webbprogram med Microsoft Identity Platform. Din app kommer att logga in en användare, hämta en åtkomsttoken för att anropa Microsoft Graph-API: et och göra en begäran till Microsoft Graph API.

När du har slutfört den här snabb starten kommer ditt program att godkänna inloggningar av personliga Microsoft-konton (inklusive outlook.com, live.com och andra) och arbets-eller skol konton från alla företag eller organisationer som använder Azure Active Directory. (Se [hur exemplet fungerar](#how-the-sample-works) för en illustration.)

## <a name="prerequisites"></a>Krav

Om du vill köra det här exemplet behöver du:

- [Java Development Kit (JDK)](https://openjdk.java.net/) 8 eller senare och [maven](https://maven.apache.org/).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrera och ladda ned snabbstartsappen
> Det finns två alternativ för att starta ditt snabb starts program: Express (alternativ 1) eller manuell (alternativ 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Alternativ 1: Registrera och konfigurera appen automatiskt och ladda sedan ned ditt kodexempel
>
> 1. Gå till snabb starts upplevelsen för [Azure Portal-Appregistreringar](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/JavaQuickstartPage/sourceType/docs) .
> 1. Ange ett namn för programmet och välj **Registrera**.
> 1. Följ anvisningarna i portalens snabb starts miljö för att ladda ned den automatiskt konfigurerade program koden.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Alternativ 2: Registrera och konfigurera programmet och kodexemplet
>
> #### <a name="step-1-register-your-application"></a>Steg 1: Registrera ditt program
>
> Följ dessa steg om du vill registrera ditt program och manuellt lägga till appens registrerings information i programmet:
>
> 1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
> 1. Om ditt konto ger dig tillgång till fler än en klientorganisation väljer du ditt konto i det övre högra hörnet och ställer in din portalsession på önskad Azure AD-klientorganisation.
>
> 1. Gå till sidan Microsoft Identity Platform för utvecklare [Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) .
> 1. Välj **ny registrering**.
> 1. När sidan **Registrera ett program** visas anger du programmets registreringsinformation:
>    - I avsnittet **Namn** anger du ett beskrivande programnamn som ska visas för appens användare, till exempel `java-webapp`.
>    - Välj **Registrera**.
> 1. På sidan **Översikt** hittar du program- **ID: t** och **katalogens ID-** värden för programmet. Kopiera dessa värden för senare.
> 1. Välj **autentiseringen** på menyn och Lägg till följande information:
>    - Lägg till **webb** plattforms konfigurationen.  Lägg till `https://localhost:8080/msal4jsample/secure/aad` dessa `https://localhost:8080/msal4jsample/graph/me` och som **omdirigerings-URI: er**..
>    - Välj **Spara**.
> 1. Välj **certifikaten & hemligheter** på menyn och klicka på **ny klient hemlighet**i avsnittet **klient hemligheter** :
>
>    - Ange en nyckel Beskrivning (för instansens program hemlighet).
>    - Välj en nyckel varaktighet **i ett år**.
>    - Nyckelvärdet visas när du väljer **Lägg till**.
>    - Kopiera värdet för nyckeln för senare. Det här nyckelvärdet visas inte igen eller kan inte hämtas på något annat sätt, så du kan registrera det så snart det visas från Azure Portal.
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Steg 1: Konfigurera din app i Azure-portalen
>
> För att kod exemplet för den här snabb starten ska fungera måste du:
>
> 1. Lägg till svars `https://localhost:8080/msal4jsample/secure/aad` - `https://localhost:8080/msal4jsample/graph/me`URL: er som och.
> 1. Skapa en klient hemlighet.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Gör ändringarna åt mig]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Redan konfigurerad](media/quickstart-v2-aspnet-webapp/green-check.png) Programmet konfigureras med de här attributen.

#### <a name="step-2-download-the-code-sample"></a>Steg 2: Hämta kod exemplet
> [!div renderon="docs"]
> [Ladda ned kod exemplet](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Hämta projektet och extrahera zip-filen till en lokal mapp närmare rotmappen – till exempel **C:\Azure-samples**
>
> Om du vill använda https med localhost, fyller du i egenskaperna Server. SSL. nyckel. Om du vill skapa ett självsignerat certifikat använder du verktyget för verktyg (ingår i JRE).
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
>   Placera den genererade nyckel lagrings filen i mappen "resurser".

> [!div renderon="portal" id="autoupdate" class="nextstepaction"]
> [Ladda ned kod exemplet](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-code-sample"></a>Steg 3: Konfigurera kod exemplet
> 1. Extrahera zip-filen till en lokal mapp.
> 1. Om du använder en Integrated Development Environment öppnar du exemplet i din favorit IDE (valfritt).
> 1. Öppna filen Application. properties, som finns i src/main/Resources/Folder och Ersätt värdet för fälten *AAD. clientId*, *AAD. Authority* och *AAD. SecretKey* med respektive värde för **program-ID**, **klient-ID** och **klient hemlighet** som följande:
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
> - `Enter_the_Client_Secret_Here`– är den **klient hemlighet** som du skapade i **certifikat & hemligheter** för det program som du har registrerat.
> - `Enter_the_Tenant_Info_Here`-är **katalog-ID-** värdet för det program som du har registrerat.
> 1. Om du vill använda https med localhost, fyller du i egenskaperna Server. SSL. nyckel. Om du vill skapa ett självsignerat certifikat använder du verktyget för verktyg (ingår i JRE).
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
>   Placera den genererade nyckel lagrings filen i mappen "resurser".


> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-run-the-code-sample"></a>Steg 3: kör kod exemplet
> [!div renderon="docs"]
> #### <a name="step-4-run-the-code-sample"></a>Steg 4: kör kod exemplet

Om du vill köra projektet kan du antingen:

Kör den direkt från din IDE genom att använda den inbäddade våren-startservern eller paketera den till en WAR-fil med hjälp av [maven](https://maven.apache.org/plugins/maven-war-plugin/usage.html) och distribuera den till en J2EE container-lösning som [Apache Tomcat](http://tomcat.apache.org/).

##### <a name="running-from-ide"></a>Köra från IDE

Om du kör webb programmet från en IDE klickar du på Kör och navigerar sedan till projektets start sida. För det här exemplet är https://localhost:8080standard start sidans URL.

1. På den första sidan väljer du knappen **Logga in** för att omdirigera till Azure Active Directory och uppmana användaren att ange sina autentiseringsuppgifter.

1. När användaren har autentiserats omdirigeras de till *https://localhost:8080/msal4jsample/secure/aad*. De är nu inloggade och sidan visar information om det inloggade kontot. Exempel gränssnittet har följande knappar:
    - *Logga ut*: loggar den aktuella användaren från programmet och dirigerar om dem till start sidan.
    - *Visa användar information*: hämtar en token för Microsoft Graph och anropar Microsoft Graph med en begäran som innehåller token, som returnerar grundläggande information om den inloggade användaren.

##### <a name="running-from-tomcat"></a>Körs från Tomcat

Om du vill distribuera webb exemplet till Tomcat måste du göra några ändringar i käll koden.

1. Öppna MS-Identity-Java-webapp/Pom. XML
    - Under `<name>msal-web-sample</name>` Lägg till`<packaging>war</packaging>`
    - Lägg till beroende:

         ```xml
         <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-tomcat</artifactId>
          <scope>provided</scope>
         </dependency>
         ```

2. Öppna MS-Identity-Java-webapp/src/main/Java/com. Microsoft. Azure. msalwebsample/MsalWebSampleApplication

    - Ta bort alla käll koder och Ersätt med följande:

   ```Java
    package com.microsoft.azure.msalwebsample;

    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.boot.builder.SpringApplicationBuilder;
    import org.springframework.boot.web.servlet.support.SpringBootServletInitializer;

    @SpringBootApplication
    public class MsalWebSampleApplication extends SpringBootServletInitializer {

     public static void main(String[] args) {
      SpringApplication.run(MsalWebSampleApplication.class, args);
     }

     @Override
     protected SpringApplicationBuilder configure(SpringApplicationBuilder builder) {
      return builder.sources(MsalWebSampleApplication.class);
     }
    }
   ```

3. Öppna en kommando tolk, gå till rotmappen för projektet och kör`mvn package`
    - Då skapas en `msal-web-sample-0.1.0.war` fil i/Targets-katalogen.
    - Byt namn på filen till`ROOT.war`
    - Distribuera den här War-filen med Tomcat eller någon annan J2EE container-lösning.
        - Om du vill distribuera i Tomcat-behållaren kopierar du War-filen till mappen webapps under installationen av Tomcat och startar sedan Tomcat-servern.

Denna WAR kommer automatiskt att finnas på https://localhost:8080/.

> [!IMPORTANT]
> Det här snabbstartsprogrammet använder en klienthemlighet för att identifiera sig som en konfidentiell klient. Eftersom klient hemligheten läggs till som oformaterad text till dina projektfiler, rekommenderar vi av säkerhets skäl att du använder ett certifikat i stället för en klient hemlighet innan du överväger programmet som produktions program. Mer information om hur du använder ett certifikat finns i [autentiseringsuppgifter för certifikat för programautentisering](https://docs.microsoft.com/azure/active-directory/develop/active-directory-certificate-credentials).

## <a name="more-information"></a>Mer information

### <a name="how-the-sample-works"></a>Så här fungerar exemplet
![Visar hur exempel appen som genereras av den här snabb starten fungerar](media/quickstart-v2-java-webapp/java-quickstart.svg)

### <a name="getting-msal"></a>Hämtar MSAL

MSAL for Java (MSAL4J) är det Java-bibliotek som används för att logga in användare och begära token som används för att få åtkomst till ett API som skyddas av Microsoft Identity Platform.

Lägg till MSAL4J i ditt program genom att använda maven eller Gradle för att hantera dina beroenden genom att göra följande ändringar i programmets Pom. XML-fil (maven) eller build. Gradle (Gradle).

I Pom. XML:

```XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>msal4j</artifactId>
    <version>1.0.0</version>
</dependency>
```

I build. gradle:

```$xslt
compile group: 'com.microsoft.azure', name: 'msal4j', version: '1.0.0'
```

### <a name="msal-initialization"></a>MSAL-initiering

Lägg till en referens till MSAL för Java genom att lägga till följande kod överst i filen där du kommer att använda MSAL4J:

```Java
import com.microsoft.aad.msal4j.*;
```

## <a name="next-steps"></a>Nästa steg

Lär dig mer om behörigheter och medgivande:

> [!div class="nextstepaction"]
> [Behörigheter och tillstånd](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)

Om du vill veta mer om auth-flödet för det här scenariot, se OAuth 2,0 Authorization Code Flow:

> [!div class="nextstepaction"]
> [Auktoriseringskod OAuth-flöde](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
