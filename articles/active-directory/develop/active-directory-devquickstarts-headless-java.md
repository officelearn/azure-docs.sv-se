---
title: "Azure AD-Java-kommandoraden komma igång | Microsoft Docs"
description: "Hur du skapar en Java-kommandoradsapp som loggar användarna in för att få åtkomst till en API."
services: active-directory
documentationcenter: java
author: navyasric
manager: mbaldwin
editor: 
ms.assetid: 51e1a8f9-6ff0-4643-a350-0ba794e26fd1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 01/23/2017
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 91e4a7b2ac454465d5cce4948a4d5f0b542d2b55
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="using-java-command-line-app-to-access-an-api-with-azure-ad"></a>Använder appen för Java-kommandoraden för att komma åt ett API med Azure AD
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Azure AD gör det lätt att flytta ut ditt webbprogram för Identitetshantering, att tillhandahålla enkel inloggning och utloggning med bara några få rader med kod.  I Java-webbappar, kan du göra detta med hjälp av Microsofts implementering av community-driven ADAL4J.

  Vi använder här ADAL4J till:

* Logga in användaren till appen med Azure AD som identitetsleverantören.
* Visa information om användaren.
* Logga ut från appen användaren.

För att kunna göra detta måste du:

1. Registrera ett program med Azure AD
2. Konfigurera din app att använda ADAL4J-biblioteket.
3. Använd ADAL4J-biblioteket för att utfärda inloggnings- och utloggningsförfrågningar till Azure AD.
4. Skriva ut data om användaren.

Du kommer igång [ladda ned stommen app](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect/archive/skeleton.zip) eller [hämta det slutförda exemplet](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect\\/archive/complete.zip).  Du måste också en Azure AD-klient att registrera ditt program.  Om du inte redan har en [Lär dig hur du skaffa en](active-directory-howto-tenant.md).

## <a name="1--register-an-application-with-azure-ad"></a>1.  Registrera ett program med Azure AD
Om du vill aktivera din app för att autentisera användare, behöver du först registrera ett nytt program i din klient.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Klicka på den översta raden på ditt konto och under den **Directory** Välj Active Directory-klient som du vill registrera ditt program.
3. Klicka på **fler tjänster** i den vänstra nav och välj **Azure Active Directory**.
4. Klicka på **App registreringar** och välj **Lägg till**.
5. Följ anvisningarna och skapa en ny **webbprogram och/eller WebAPI**.
  * Den **namn** av programmet beskriva programmet till slutanvändare
  * Den **inloggnings-URL** är den grundläggande Webbadressen för din app.  Den stommen standardvärdet är `http://localhost:8080/adal4jsample/`.
6. När du har slutfört registreringen tilldelas AAD appen ett unikt program-ID.  Du behöver det här värdet i nästa avsnitt så kopiera den från programfliken.
7. Från den **inställningar** -> **egenskaper** för programmet, uppdatera App-ID-URI. Den **App-ID URI** är en unik identifierare för programmet.  Konventionen är att använda `https://<tenant-domain>/<app-name>`, t.ex. `http://localhost:8080/adal4jsample/`.

Skapa en gång för din app i portalen en **nyckeln** från den **inställningar** för programmet och kopiera ned.  Du behöver den snart.

## <a name="2-set-up-your-app-to-use-adal4j-library-and-prerequisites-using-maven"></a>2. Konfigurera din app att använda ADAL4J bibliotek och förutsättningar med Maven
Här kan konfigurerar vi ADAL4J för att använda autentiseringsprotokollet OpenID Connect.  ADAL4J används för att utfärda inloggnings- och utloggningsförfrågningar, hantera användarens session och få information om användare, bland annat.

* I rotkatalogen för ditt projekt, öppna/skapa `pom.xml` och leta upp den `// TODO: provide dependencies for Maven` och Ersätt med följande:

```Java
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>public-client-adal4j-sample</artifactId>
    <packaging>jar</packaging>
    <version>0.0.1-SNAPSHOT</version>
    <name>public-client-adal4j-sample</name>
    <url>http://maven.apache.org</url>
    <properties>
        <spring.version>3.0.5.RELEASE</spring.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>adal4j</artifactId>
            <version>1.1.2</version>
        </dependency>
        <dependency>
            <groupId>com.nimbusds</groupId>
            <artifactId>oauth2-oidc-sdk</artifactId>
            <version>4.5</version>
        </dependency>
        <dependency>
            <groupId>org.json</groupId>
            <artifactId>json</artifactId>
            <version>20090211</version>
        </dependency>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>3.0.1</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
            <version>1.7.5</version>
        </dependency>
</dependencies>
    <build>
        <finalName>public-client-adal4j-sample</finalName>
        <plugins>
                <plugin>
            <groupId>org.codehaus.mojo</groupId>
            <artifactId>exec-maven-plugin</artifactId>
            <version>1.2.1</version>
            <configuration>
                <mainClass>PublicClient</mainClass>
            </configuration>
        </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>1.7</source>
                    <target>1.7</target>
                    <encoding>UTF-8</encoding>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-dependency-plugin</artifactId>
                <executions>
                    <execution>
                        <id>install</id>
                        <phase>install</phase>
                        <goals>
                            <goal>sources</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-resources-plugin</artifactId>
                <version>2.5</version>
                <configuration>
                    <encoding>UTF-8</encoding>
                </configuration>
            </plugin>
            <plugin>
        <artifactId>maven-assembly-plugin</artifactId>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>single</goal>
            </goals>
          </execution>
        </executions>
        <configuration>
          <descriptorRefs>
            <descriptorRef>jar-with-dependencies</descriptorRef>
          </descriptorRefs>
        </configuration>
      </plugin>
      <plugin>
  <groupId>org.apache.maven.plugins</groupId>
  <artifactId>maven-assembly-plugin</artifactId>
  <configuration>
    <archive>
      <manifest>
        <mainClass>PublicClient</mainClass>
      </manifest>
    </archive>
  </configuration>
</plugin>
        </plugins>
    </build>

</project>


```



## <a name="3-create-the-java-publicclient-file"></a>3. Skapa filen Java PublicClient
Enligt ovan, kommer vi att använda Graph-API och hämta data om den inloggade användaren. För att det ska vara enkelt för att vi ska vi skapa både en fil som representerar en **katalogobjekt** och en enskild fil som representerar den **användaren** så att OO mönstret för Java kan användas.

* Skapa en fil med namnet `DirectoryObject.java` som ska användas för att lagra grundläggande information om alla DirectoryObject (du kan passa på att använda detta senare för andra diagrammet frågor kan du göra). Du kan klipp ut och klistra in det här nedan:

```Java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;

import javax.naming.ServiceUnavailableException;

import com.microsoft.aad.adal4j.AuthenticationContext;
import com.microsoft.aad.adal4j.AuthenticationResult;

public class PublicClient {

    private final static String AUTHORITY = "https://login.microsoftonline.com/common/";
    private final static String CLIENT_ID = "2a4da06c-ff07-410d-af8a-542a512f5092";

    public static void main(String args[]) throws Exception {

        try (BufferedReader br = new BufferedReader(new InputStreamReader(
                System.in))) {
            System.out.print("Enter username: ");
            String username = br.readLine();
            System.out.print("Enter password: ");
            String password = br.readLine();

            AuthenticationResult result = getAccessTokenFromUserCredentials(
                    username, password);
            System.out.println("Access Token - " + result.getAccessToken());
            System.out.println("Refresh Token - " + result.getRefreshToken());
            System.out.println("ID Token - " + result.getIdToken());
        }
    }

    private static AuthenticationResult getAccessTokenFromUserCredentials(
            String username, String password) throws Exception {
        AuthenticationContext context = null;
        AuthenticationResult result = null;
        ExecutorService service = null;
        try {
            service = Executors.newFixedThreadPool(1);
            context = new AuthenticationContext(AUTHORITY, false, service);
            Future<AuthenticationResult> future = context.acquireToken(
                    "https://graph.windows.net", CLIENT_ID, username, password,
                    null);
            result = future.get();
        } finally {
            service.shutdown();
        }

        if (result == null) {
            throw new ServiceUnavailableException(
                    "authentication result was null");
        }
        return result;
    }
}


```


## <a name="compile-and-run-the-sample"></a>Kompilera och köra exemplet
Ändra tillbaka till din rotkatalog och kör följande kommando för att bygga exemplet du fört in tillsammans med `maven`. Det här använder den `pom.xml` filen som du skrev för beroenden.

`$ mvn package`

Du bör nu ha en `adal4jsample.war` filen i din `/targets` directory. Du kan distribuera som i Tomcat-behållaren och besöker Webbadressen 

`http://localhost:8080/adal4jsample/`

> [!NOTE]
> Det är mycket enkelt att distribuera en WAR-fil med de senaste Tomcat-servrarna. Helt enkelt navigera till `http://localhost:8080/manager/` och följ instruktionerna på Överför din '' adal4jsample.war-filen. Det kommer autodeploy du med korrekt slutpunkt.
> 
> 

## <a name="next-steps"></a>Nästa steg
Grattis! Nu har du en fungerande Java-program som har möjlighet att autentisera användare på ett säkert sätt anropa webb-API: er med hjälp av OAuth 2.0 och få grundläggande information om användaren.  Om du inte redan gjort nu är det dags att fylla i din klient med vissa användare.

För referens anger det slutförda exemplet (utan dina konfigurationsvärden) [har angetts som en .zip här](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect/archive/complete.zip), eller kan du klona den från GitHub:

```git clone --branch complete https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect.git```

