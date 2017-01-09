---
title: Skapa en Java-webbapp i Azure App Service | Microsoft Docs
description: "I den här kursen får du lära dig hur du distribuerar en Java-webbapp till Azure App Service."
services: app-service\web
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: d6e73cc3-8b71-4742-a197-3edeabc6a289
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: get-started-article
ms.date: 12/22/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: e2785d007aa30d3ef70ce2ca6049cdd4e9733099


---
# <a name="create-a-java-web-app-in-azure-app-service"></a>Skapa en Java-webbapp i Azure App Service
[!INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

I den här kursen får du veta hur du skapar en Java-[webbapp i Azure App Service] med hjälp av [Azure Portal]. Azure Portal är ett webbgränssnitt som du kan använda till att hantera dina Azure-resurser.

> [!NOTE]
> För den här kursen behöver du ett Microsoft Azure-konto. Om du inte har ett konto kan du [aktivera Visual Studio-prenumerantförmåner] eller [registrera dig för en kostnadsfri utvärderingsversion].
> 
> Om du vill komma igång med Azure App Service innan du registrerar dig för ett Azure-konto kan du gå till [Prova App Service]. Där kan du direkt skapa en tillfällig startwebbapp i App Service. Inget kreditkort behövs och du gör inga åtaganden.
> 
> 

## <a name="java-application-options"></a>Alternativ för Java-program
Det finns flera sätt att konfigurera ett Java-program i en webbapp i App Service. 

1. Skapa en app och konfigurera **Programinställningar**.
   
    App Service tillhandahåller flera Tomcat- och Jetty-versioner med standardkonfiguration. Om appen du ska vara värd för fungerar med en av de inbyggda versionerna är det här den enklaste metoden för att skapa en webbehållare. Den är också utmärkt om du bara vill ladda upp en war-fil till en webbehållare. Med den här metoden skapar du en app i Azure Portal och går sedan till bladet **Programinställningar** för appen där du väljer din version av Java och en Java-webbehållare. När du använder den här metoden körs både Java och webbehållaren från programfilerna. Med de andra metoderna lagras webbehållaren och möjligen också JVM i diskutrymmet. När du använder den här modellen har du inte behörighet att redigera filer i den här delen av filsystemet. Det innebär att du till exempel inte kan konfigurera filen *server.xml* eller placera biblioteksfiler i mappen */lib*. Mer information finns i avsnittet [Skapa och konfigurera en Java-webbapp](#portal) senare i den här kursen.
2. Använd en mall från Azure Marketplace.
   
    Azure Marketplace innehåller mallar som automatiskt skapar och konfigurerar Java-webbappar med Tomcat- eller Jetty-webbehållare. De webbehållare som skapas med mallarna kan konfigureras. Mer information finns i avsnittet [Använda en Java-mall från Azure Marketplace](#marketplace) i den här kursen.
3. Skapa en app och sedan kopiera och redigera konfigurationsfilerna manuellt 
   
    Du kanske vill ha ett anpassat Java-program som inte distribuerar i någon av de webbehållare som tillhandahålls av App Service. Exempel:
   
   * Java-programmet kräver en version av Tomcat eller Jetty som inte direkt stöds av App Service eller tillhandahålls i galleriet.
   * Java-programmet tar emot HTTP-förfrågningar och distribuerar inte som en WAR-fil i en befintlig webbehållare.
   * Du vill själv skapa webbehållaren från grunden. 
   * Du vill använda en version av Java som inte stöds i App Service och vill ladda upp den själv.
     
     I dessa fall kan du skapa en app i Azure Portal och ange rätt körningsfiler manuellt. När du gör det räknas filerna av mot lagringskvoten för din App Service-plan. Mer information finns i [Ladda upp en anpassad Java-webbapp till Azure].

## <a name="a-nameportala-create-and-configure-a-java-web-app"></a><a name="portal"></a>Skapa och konfigurera en Java-webbapp
I det här avsnittet får du veta hur du skapar en webbapp och konfigurerar den för Java med hjälp av bladet **Programinställningar** i portalen.

1. Logga in på [Azure Portal].
2. Klicka på **Ny > Webb + mobilt > Webbapp**.
   
    ![Ny webbapp][newwebapp]
3. Ange ett namn på webbappen i rutan **Webbapp**.
   
    Det här namnet måste vara unikt i domänen azurewebsites.net eftersom webbappens webbadress är {namn}.azurewebsites.net. Om namnet inte är unikt visas ett rött utropstecken i textrutan.
4. Välj en **Resursgrupp** eller skapa en ny.
   
    Mer information om resursgrupper finns i [Översikt över Resource Manager].
5. Välj en **App Service-plan/plats** eller skapa en ny.
   
    Mer information om App Service-planer finns i [Översikt över Azure App Service-planer].
6. Klicka på **Skapa**.
   
    ![Skapa webbapp][newwebapp2]
7. När du har skapat webbappen klickar du på **Web Apps > {din webbapp}**.
   
    ![Välj webbapp][selectwebapp]
8. I bladet **Webbapp** klickar du på **Inställningar**.
9. Klicka på **Programinställningar**.
10. Välj **Java-version**. 
11. Välj **Java Minor-version**. Om du väljer **Nyaste ** kommer appen att använda den senaste Minor-version som är tillgänglig i App Service för Java Major-versionen. Objektet **Nyaste** är unikt för Java-appar som skapats via **Programinställningar**. Om du skapar en Java-app från galleriet måste du hantera din egen behållare och JVM-ändringar. 
12. Välj **Webbehållare**. Om du väljer ett behållarnamn som börjar med **Nyaste** sparas appen som den senaste versionen av den major-versionen av webbehållaren som är tillgänglig i App Service. 
    
    ![Versioner av webbehållare][versions]
13. Klicka på **Spara**.
    
    Inom en liten stund blir webbappen Java-baserad och konfigurerad för att använda webbehållaren du har valt.
14. Klicka på **Webbappar > {din nya webbapp}**.
15. Gå till den nya webbplatsen genom att klicka på **webbadressen**.
    
    På webbsidan bekräftas att du har skapat en Java-baserad webbapp.

## <a name="a-namemarketplacea-use-a-java-template-from-the-azure-marketplace"></a><a name="marketplace"></a> Använda en Java-mall från Azure Marketplace
I det här avsnittet får du veta hur du använder Azure Marketplace till att skapa en Java-webbapp. Med samma grundläggande metod kan du också skapa Java-baserade mobilappar och API Apps. 

1. Logga in på [Azure Portal]
2. Klicka på **Ny > Marketplace**.
   
    ![Ny Marketplace][newmarketplace]
3. Klicka på **Webb + mobilt**.
   
    Du kanske måste rulla åt vänster för att visa bladet **Marketplace** där du kan välja **Webb + mobilt**.
4. I sökrutan anger du namnet på Java-programservern, till exempel **Apache Tomcat** eller **Jetty**. Tryck på Retur.
5. Klicka på Java-programservern i sökresultaten.
   
    ![Webb, mobilt, Jetty][webmobilejetty]
6. I det första **Apache Tomcat**- eller **Jetty**-bladet klickar du på **Skapa**.
   
    ![Jetty-portal, blad][jettyblade]
7. I nästa **Apache Tomcat**- eller **Jetty**-blad anger du ett namn för webbappen i rutan **Webbapp**.
   
    Det här namnet måste vara unikt i domänen azurewebsites.net eftersom webbappens webbadress är {namn}.azurewebsites.net. Om namnet inte är unikt visas ett rött utropstecken i textrutan.
8. Välj en **Resursgrupp** eller skapa en ny.
   
    Mer information om resursgrupper finns i [Översikt över Resource Manager].
9. Välj en **App Service-plan/plats** eller skapa en ny.
   
    Mer information om App Service-planer finns i [Översikt över Azure App Service-planer].
10. Klicka på **Skapa**.
    
    ![Jetty-portal, skapa][jettyportalcreate2]
    
    Azure skapar snabbt den nya webbappen. Det går vanligen på mindre än en minut.
11. Klicka på **Webbappar > {din nya webbapp}**.
12. Gå till den nya webbplatsen genom att klicka på **webbadressen**.
    
    ![Jetty-URL][jettyurl]
    
    Tomcat levereras med en standarduppsättning sidor. Om du väljer Tomcat visas en sida som liknar den i följande exempel.
    
    ![Webbapp som använder Apache Tomcat][tomcat]
    
    Om du väljer Jetty visas en sida som påminner om den i följande exempel. Jetty har ingen standarduppsättning sidor så samma JSP som används för en tom Java-webbplats används också här.
    
    ![Webbapp som använder Jetty][jetty]

Nu när du har skapat webbappen med en appbehållare går du till avsnittet [Nästa steg](#next-steps) där du hittar information om hur du överför appen till webbappen.

## <a name="next-steps"></a>Nästa steg
Nu körs en Java-programserver i webbappen i Azure App Service. Information om hur du distribuerar egen kod till webbappen finns i [Lägga till ett program eller en webbsida i Java-webbappen].

Mer information om hur du utvecklar Java-program i Azure finns i [Java-utvecklingscenter].

<!-- URL List -->

[Lägga till ett program eller en webbsida i Java-webbappen]: ./web-sites-java-add-app.md
[Översikt över Azure App Service-planer]: ../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md
[Azure Portal]: https://portal.azure.com/
[aktivera Visual Studio-prenumerantförmåner]: http://go.microsoft.com/fwlink/?LinkId=623901
[registrera dig för en kostnadsfri utvärderingsversion]: http://go.microsoft.com/fwlink/?LinkId=623901
[Prova App Service]: http://go.microsoft.com/fwlink/?LinkId=523751
[webbapp i Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
[Java-utvecklingscenter]: /develop/java/
[Översikt över Resource Manager]: ../azure-resource-manager/resource-group-overview.md
[Ladda upp en anpassad Java-webbapp till Azure]: ./web-sites-java-custom-upload.md

<!-- IMG List -->

[newwebapp]: ./media/web-sites-java-get-started/newwebapp.png
[newwebapp2]: ./media/web-sites-java-get-started/newwebapp2.png
[selectwebapp]: ./media/web-sites-java-get-started/selectwebapp.png
[versions]: ./media/web-sites-java-get-started/versions.png
[newmarketplace]: ./media/web-sites-java-get-started/newmarketplace.png
[webmobilejetty]: ./media/web-sites-java-get-started/webmobilejetty.png
[jettyblade]: ./media/web-sites-java-get-started/jettyblade.png
[jettyportalcreate2]: ./media/web-sites-java-get-started/jettyportalcreate2.png
[jettyurl]: ./media/web-sites-java-get-started/jettyurl.png
[tomcat]: ./media/web-sites-java-get-started/tomcat.png
[jetty]: ./media/web-sites-java-get-started/jetty.png



<!--HONumber=Dec16_HO1-->


