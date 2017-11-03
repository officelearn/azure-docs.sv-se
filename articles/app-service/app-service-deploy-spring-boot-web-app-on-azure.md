---
title: "Distribuera ett program för start av källan till Azure App Service | Microsoft Docs"
description: "Den här kursen hjälper utvecklare stegen för att distribuera webbprogram källan Start komma igång till Azure App Service."
services: app-service\web
documentationcenter: java
author: rmcmurray
manager: cfowler
editor: 
ms.assetid: 
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 08/04/2017
ms.author: asirveda;robmcm
ms.openlocfilehash: 8776142d5452bf5057990702c89aa1a541382ffc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-a-spring-boot-application-to-the-azure-app-service"></a>Distribuera ett Spring Boot-program till Azure App Service

Den  **[Vårversionen Framework]**  är en öppen källkod som hjälper Java-utvecklare som skapar program på företagsnivå och en av de mer populära projekt som bygger på den plattformen är [Vårversionen Start], vilket ger en förenklad metod för att skapa fristående Java-program.

Den här kursen får du dock skapa exempel källan Start komma igång-webbapp och distribuera den till [Azure App Service].

### <a name="prerequisites"></a>Krav

För att kunna slutföra stegen i den här självstudiekursen, måste du ha följande:

* En Azure-prenumeration; Om du inte redan har en Azure-prenumeration, kan du aktivera din [MSDN-prenumerantförmåner] eller registrera dig för en [kostnadsfritt Azure-konto].
* En uppdaterad [Java Developer Kit (JDK)].
* Apache's [Maven] skapa verktyget (Version 3).
* En [Git] klienten.

## <a name="create-the-spring-boot-getting-started-web-app"></a>Skapa ett webbprogram källan Start komma igång

Följande steg hjälper dig igenom de steg som krävs för att skapa ett enkelt källan Start-webbprogram och testa den lokalt.

1. Öppna en kommandotolk och skapa en lokal katalog för att hålla ditt program och ändra till katalogen; Exempel:
   ```
   md C:\SpringBoot
   cd C:\SpringBoot
   ```
   -- eller--
   ```
   md /users/robert/SpringBoot
   cd /users/robert/SpringBoot
   ```

1. Klona den [Vårversionen Start komma igång] exempelprojektet till den katalog som du just skapat, till exempel:
   ```
   git clone https://github.com/spring-guides/gs-spring-boot.git
   ```

1. Ändra katalogen till slutförda projektet. Exempel:
   ```
   cd gs-spring-boot
   cd complete
   ```

1. Skapa JAR-filen med Maven; Exempel:
   ```
   mvn package
   ```

1. När webbappen har skapats, ändra katalogen till JAR-filen och starta webbprogram; Exempel:
   ```
   cd target
   java -jar gs-spring-boot-0.1.0.jar
   ```

1. Testa webbappen genom att bläddra till http://localhost: 8080 i en webbläsare, eller Använd syntax som i följande exempel om du har curl som är tillgängliga:
   ```
   curl http://localhost:8080
   ```

1. Du bör se följande meddelande visas: **helg från källan Start!**

   ![Bläddra Sample-appen][SB01]

## <a name="create-an-azure-web-app-for-use-with-java"></a>Skapa en Azure-webbapp för användning med Java

Följande steg hjälper dig igenom stegen för att skapa en Webbapp i Azure, konfigurera nödvändiga inställningar för Java och konfigurera FTP-autentiseringsuppgifter.

1. Bläddra till den [Azure-portalen] och logga in.

1. När du har loggat in ditt konto på Azure portal, klicka på menyn ikonen för **Apptjänster**:
   
   ![Azure Portal][AZ01]

1. När den **Apptjänster** visas klickar du på **+ Lägg till** att skapa en ny App-tjänst.

   ![Skapa Apptjänst][AZ02]

1. När listan över mallar för app visas, klicka på länken för den grundläggande Microsoft Web App.

   ![Mallar för App][AZ03]

1. När sidan för webbprogrammet mallen visas, klickar på **skapa**.

   ![Skapa webbapp][AZ04]

1. Ange ett unikt namn för din webbapp och ange eventuella ytterligare inställningar och sedan **skapa**.

   ![Skapa Webbprograminställningar][AZ05]

1. När webbappen har skapats, klicka på menyn ikonen för **Apptjänster**, och klicka sedan på ditt nyligen skapade webbprogram:

   ![Lista över Webbappar][AZ06]

1. När ditt webbprogram visas anger du Java-versionen med hjälp av följande steg:

   a. Klicka på den **programinställningar** menyalternativet.

   b. Välj **Java 8** för Java-version.

   c. Välj **nyaste** för den lägre versionen av Java.

   d. Välj **senaste Tomcat 8.5** för webbehållaren. (Den här behållaren faktiskt används inte; Azure använder behållare från källan Start programmet.)

   e. Klicka på **Spara**.

   ![Programinställningar][AZ07]

1. Ange dina autentiseringsuppgifter för FTP-distribution med hjälp av följande steg:

   a. Klicka på den **Distributionsbehörigheterna** menyalternativet.

   b. Ange ditt användarnamn och lösenord.

   c. Klicka på **Spara**.

   ![Ange autentiseringsuppgifter för distribution][AZ08]

1. Hämta din FTP-anslutningsinformationen med hjälp av följande steg:

   a. Klicka på den **Distributionsbehörigheterna** menyalternativet.

   b. Kopiera hela FTP-användarnamn och URL: en och spara dem för nästa avsnitt i den här kursen.

   ![FTP-URL och autentiseringsuppgifter][AZ09]

## <a name="deploy-your-spring-boot-web-app-to-azure"></a>Distribuera ditt webbprogram för vår Start till Azure

Följande steg hjälper dig igenom stegen för att distribuera ditt webbprogram för vår Start till Azure.

1. Öppna en textredigerare, till exempel Anteckningar och klistra in följande text i ett nytt dokument och spara filen som *web.config*:
   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <configuration>
     <system.webServer>
       <handlers>
         <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified" />
       </handlers>
       <httpPlatform processPath="%JAVA_HOME%\bin\java.exe"
           arguments="-Djava.net.preferIPv4Stack=true -Dserver.port=%HTTP_PLATFORM_PORT% -jar &quot;%HOME%\site\wwwroot\gs-spring-boot-0.1.0.jar&quot;">
       </httpPlatform>
     </system.webServer>
   </configuration>
   ```

1. När du har sparat den *web.config* filen till datorn, ansluta till ditt webbprogram via FTP-använder URL, användarnamn och lösenord från föregående avsnitt i den här kursen. Exempel:
   ```
   ftp
   open waws-prod-sn0-000.ftp.azurewebsites.windows.net
   user wingtiptoys-springboot\wingtiptoysuser
   pass ********
   ```

1. Ändra remote katalogen till rotmappen för ditt webbprogram (som är */platsen/wwwroot*), kopiera JAR-filen från källan Start-program och *web.config* från tidigare. Exempel:
   ```
   cd site/wwwroot
   put gs-spring-boot-0.1.0.jar
   put web.config
   ```

1. När du har distribuerat din JAR och *web.config* filer till ditt webbprogram, du måste starta om ditt webbprogram med hjälp av Azure portal:

   ![][AZ10]

1. Testa webbappen genom att bläddra till ditt webbprogram URL i en webbläsare, eller Använd syntax som i följande exempel om du har curl som är tillgängliga:
   ```
   curl http://wingtiptoys-springboot.azurewebsites.net/
   ```

1. Du bör se följande meddelande visas: **helg från källan Start!**

   ![Bläddra Sample-appen][SB02]

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder värdet startprogram i Azure finns i följande artiklar:

* [Distribuera ett program för start av värdet på Linux i Azure Container Service](../container-service/kubernetes/container-service-deploy-spring-boot-app-on-linux.md)

* [Distribuera ett program för start av värdet på ett Kubernetes kluster i Azure Container Service](../container-service/kubernetes/container-service-deploy-spring-boot-app-on-kubernetes.md)

Mer information om hur du använder Azure med Java finns på [Azure Java Developer Center] och i [Java Tools för Visual Studio Team Services].

Mer information om depoying webbappar till Azure med FTP, finns i [distribuera din app till Azure App Service med FTP/S].

Mer information om källan Start exempelprojektet finns [Vårversionen Start komma igång].

Hjälp med att komma igång med din egen källan startprogram, finns det **Vårversionen Initializr** på https://start.spring.io/.

Mer information om hur du konfigurerar ytterligare inställningar för ditt webbprogram finns [konfigurera webbappar i Azure App Service].

<!-- URL List -->

[Azure App Service]: https://azure.microsoft.com/services/app-service/
[Azure Container Service]: https://azure.microsoft.com/services/container-service/
[Azure Java Developer Center]: https://azure.microsoft.com/develop/java/
[Azure-portalen]: https://portal.azure.com/
[konfigurera webbappar i Azure App Service]: /azure/app-service/web-sites-configure
[distribuera din app till Azure App Service med FTP/S]: https://docs.microsoft.com/azure/app-service/app-service-deploy-ftp
[kostnadsfritt Azure-konto]: https://azure.microsoft.com/pricing/free-trial/
[Git]: https://github.com/
[Java Developer Kit (JDK)]: http://www.oracle.com/technetwork/java/javase/downloads/
[Java Tools för Visual Studio Team Services]: https://java.visualstudio.com/
[Maven]: http://maven.apache.org/
[MSDN-prenumerantförmåner]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Vårversionen Start]: http://projects.spring.io/spring-boot/
[Vårversionen Start komma igång]: https://github.com/spring-guides/gs-spring-boot
[Vårversionen Framework]: https://spring.io/

<!-- IMG List -->

[SB01]: ./media/app-service-deploy-spring-boot-web-app-on-azure/SB01.png
[SB02]: ./media/app-service-deploy-spring-boot-web-app-on-azure/SB02.png

[AZ01]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ01.png
[AZ02]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ02.png
[AZ03]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ03.png
[AZ04]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ04.png
[AZ05]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ05.png
[AZ06]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ06.png
[AZ07]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ07.png
[AZ08]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ08.png
[AZ09]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ09.png
[AZ10]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ10.png
