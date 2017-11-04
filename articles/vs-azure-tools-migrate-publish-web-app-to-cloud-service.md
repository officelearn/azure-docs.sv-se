---
title: "Hur du migrerar och publicera ett program till en Azure-molntjänst från Visual Studio | Microsoft Docs"
description: "Lär dig hur du migrerar och publicera ditt webbprogram till en Azure-molntjänst med hjälp av Visual Studio."
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 9394adfd-a645-4664-9354-dd5df08e8c91
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/11/2016
ms.author: kraigb
ms.openlocfilehash: d5de4f5a7357cf5adde7773867356d47ad447bab
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-migrate-and-publish-a-web-application-to-an-azure-cloud-service-from-visual-studio"></a>Så här: migrera och publicera ett program till en Azure-molntjänst från Visual Studio
Om du vill dra nytta av värd-tjänster och skalbarhet i Azure, kanske du vill migrera och publicera ditt webbprogram till en Azure-molntjänst. Du kan köra ett webbprogram i Azure med minimala ändringar till ditt befintliga program.

> [!NOTE]
> Den här artikeln handlar om hur du distribuerar till molntjänster, inte till webbplatser. Information om hur du distribuerar till webbplatser finns [distribuera en webbapp i Azure App Service](app-service/app-service-deploy-local-git.md).
>
>

En lista över specifika mallar som har stöd för både Visual C# och Visual Basic, finns i avsnittet **stöds projektmallar** senare i det här avsnittet.

Du måste först aktivera ditt webbprogram för Azure från Visual Studio. Följande bild visar viktiga steg för att publicera ditt befintliga webbprogram genom att lägga till ett Azure-projekt för distribution. Den här processen lägger till ett Azure-projekt med nödvändiga webbrollen till din lösning. Baserat på vilken typ av webbprojekt som du har uppdateras projektegenskaperna för sammansättningar också om tjänstepaketet kräver ytterligare paket för distribution.

![Publicera ett program till Microsoft Azure](./media/vs-azure-tools-migrate-publish-web-app-to-cloud-service/IC748917.png)

> [!NOTE]
> Den **konvertera**, **konvertera till Azure Cloud Service-projekt** kommandot visas bara för webbprojekt i din lösning. Till exempel är kommandot inte tillgängligt för en Silverlight-projekt i din lösning.
> När du skapar ett tjänstepaket eller publicera programmet till Azure, inträffa varningar eller fel. Dessa varningar och fel kan hjälpa dig att åtgärda problem innan du distribuerar till Azure. Exempelvis kan du få en varning om saknas en sammansättning. Läs mer om hur du behandlar alla varningar som fel [konfigurera Azure Cloud Service-projekt i Visual Studio](vs-azure-tools-configuring-an-azure-project.md). Om du skapar ditt program, köra den lokalt med hjälp av beräkningsemulatorn eller publicera den på Azure, kan du se följande fel i den **fellista** fönster: **den angivna sökvägen, filnamnet eller båda är för lång**. Detta fel uppstår eftersom längden på det fullständigt kvalificerade Azure-projekt-namnet är för långt. Längden på projektnamnet, inklusive den fullständiga sökvägen får inte vara mer än 146 tecken. Detta är exempelvis projektnamnet och sökvägen till en Azure-projekt som har skapats för ett Silverlight-program: `c:\users\<user name>\documents\visual studio 2015\Projects\SilverlightApplication4\SilverlightApplication4.Web.Azure.ccproj`. Du kan behöva flytta lösningen till en annan katalog med en kortare sökväg till att minska längden på det fullständigt kvalificerade projektnamnet.
>
>

Följ dessa steg om du vill migrera och publicera ett program till Azure från Visual Studio.

## <a name="enable-a-web-application-for-deployment-to-azure"></a>Aktivera ett program för distribution till Azure
### <a name="to-enable-a-web-application-for-deployment-to-azure"></a>Att aktivera ett program för distribution till Azure
1. Öppna snabbmenyn för ett webbprojekt i din lösning och välj Lägg till Azure distributionsprojekt för att aktivera ditt webbprogram för distribution till Azure.

    Följande åtgärder:

   * Ett Azure-projekt kallas `<name of the web project>.Azure` har lagts till i lösningen för ditt program.
   * En webbroll för webbprojektet läggs till den här Azure-projekt.
   * Den **kopiera lokala** egenskap är inställd på true för alla sammansättningar som krävs för MVC 2, 3 MVC MVC 4 och Silverlight affärsprogram. Detta lägger till dessa sammansättningar tjänstpaket som används för distribution.

   > [!IMPORTANT]
   > Om du har andra sammansättningar eller filer som krävs för det här webbprogrammet, måste du manuellt ange egenskaperna för de här filerna. Information om hur du anger dessa egenskaper finns i avsnittet **inkludera filer i tjänstpaketet** senare i den här artikeln.
   >
   > [!NOTE]
   > Om det finns redan en webbroll för en specifik webbprojekt i ett Azure-projekt i lösningen **konvertera**, **konvertera till Azure Molntjänstprojekt** visas inte på snabbmenyn för det här webbprojektet.
   >
   >

   Om du har flera webbprojekt i ditt webbprogram och du vill skapa web-roller för varje webbprojekt, utför du stegen i den här proceduren för varje webbprojekt. Detta skapar separata Azure projekt för varje webbroll. Varje webbprojekt kan publiceras separat. Du kan manuellt lägga en annan webbroll till en befintlig Azure-projekt i ditt webbprogram. Gör detta genom att öppna snabbmenyn för den **roller** väljer du mappen i projektet Azure **Lägg till**, sedan **Webbrollsprojektet i lösningen**, välj projektet för att lägga till som en webbroll och välj sedan den **OK** knappen.

## <a name="use-an-azure-sql-database-for-your-application"></a>Använda en Azure SQL-databas för programmet
Om du har en anslutningssträng för webbprogram som använder en SQL Server-databas som är lokalt, måste du ändra den här anslutningssträngen för att använda en instans av SQL-databas som är värd för Azure i stället.

> [!IMPORTANT]
> Din prenumeration måste kan du använda SQL-databas. Om du ansluter till din prenumeration från den [klassiska Azure-portalen](http://go.microsoft.com/fwlink/?LinkID=213885), kan du bestämma vilka tjänster som ger din prenumeration. Följande instruktioner gäller den utgivna [klassiska Azure-portalen](http://go.microsoft.com/fwlink/?LinkID=213885). Om du använder den [Azure-portalen](http://portal.microsoft.com), går du vidare till nästa procedur.
>
>

### <a name="to-use-a-sql-database-instance-in-your-web-role-for-your-connection-string"></a>Du använder en SQL-databasinstans i din webbroll för anslutningssträngen
1. Att skapa en instans av SQL-databasen i den [klassiska Azure-portalen](http://go.microsoft.com/fwlink/?LinkID=213885), följer du stegen i följande artikel: [skapa en SQL-databasserver](http://go.microsoft.com/fwlink/?LinkId=225109).

   > [!NOTE]
   > När du konfigurerar brandväggsregler för din instans av SQL-databas måste du välja den **ge andra Azure-tjänster åtkomst till den här servern** kryssrutan.
   >
   >
2. Om du vill skapa en instans av SQL-databas som ska användas för anslutningssträngen, följer du stegen i nästa avsnitt i följande artikel: [skapa en SQL-databas](http://go.microsoft.com/fwlink/?LinkId=225110).
3. Om du vill kopiera ADO.NET-anslutningssträngen för anslutningssträngen, utför följande steg i den [klassiska Azure-portalen](http://go.microsoft.com/fwlink/?LinkID=213885).  

   1. Välj den **databasen** knappen och öppna sedan noden för den prenumeration som du använde för att skapa en instans av SQL-databas.
   2. Om du vill visa tillgängliga instanser av SQL-databas, Välj den **SQL-databaser** nod.
   3. Välj databasen om du vill visa egenskaperna för databasen. Den **egenskaper** vyn visas.

      > [!NOTE]
      > Om den **egenskaper** vyn inte visas, måste du kanske öppna den med hjälp av avgränsaren.
      >
      >
   4. Om du vill visa anslutningssträngar, väljer du på ellipsknappen (...) bredvid Visa.

      Den **anslutningssträngar** dialogrutan visas.
   5. Om du vill kopiera ADO.NET-anslutningssträngen, markera texten och välja tangenterna Ctrl + C.
   6. Stäng dialogrutan genom att välja den **Stäng** knappen.
4. Öppna filen web.config om du vill ersätta anslutningssträngen i web.config-filen för att använda den här instansen av SQL-databas, markera den befintliga anslutning sträng posten och välj sedan tangenterna Ctrl + V. ADO.NET-anslutningssträngen för instansen av SQL-databas ersätter den befintliga anslutningssträngen.
5. Du måste också lägga till parametern `MultipleActiveResultSets=True` i anslutningssträngen. Anslutningssträngen måste ha följande format:

    ```
    connectionString=”Server=tcp:<database_server>.database.windows.net,1433;Database=<database_name>;User ID=<user_name>@<database_server>;Password=<myPassword>;Trusted_Connection=False;Encrypt=True;MultipleActiveResultSets=True"
    ```
6. (Valfritt) En alternativ metod för att ändra anslutningssträngen direkt i web.config-filen är att lägga till ett avsnitt i en web.config-transformeringsfiler, beroende på versionskonfiguration som används för att skapa ditt abonnemang. Öppna filen Web.Debug.Config eller Web.Release.Config-filen. Lägg till följande avsnitt i den här filen:

    ```
    XMLCopy<connectionStrings><addname="DefaultConnection"connectionString="Server=tcp:<database_server>.database.windows.net,1433;Database=<database_name>;User ID=<user_name>@<database_server>;Password=<myPassword>;Trusted_Connection=False;Encrypt=True;MultipleActiveResultSets=True"xdt:Transform="SetAttributes"xdt:Locator="Match(name)"/></connectionStrings>
    ```
7. Spara filen som du har ändrat och publicera programmet.

### <a name="to-use-an-instance-of-sql-database-by-using-the-azure-classic-portal"></a>Att använda en instans av SQL-databas med hjälp av den klassiska Azure-portalen
1. I den [klassiska Azure-portalen](http://go.microsoft.com/fwlink/?LinkID=213885), väljer noden SQL-databaser.

   * Om instansen av SQL-databas som du vill använda visas kan du välja att öppna den.
   * Om du inte skapat några instanser, väljer du lämplig länk och sedan skapa en instans.
2. När du öppnar eller skapa en databasinstans, välja den **anslutningssträngar** länk.
3. Längst ned på sidan Välj länken om du vill konfigurera inställningar för brandväggen och acceptera standardvärdena eller konfigurera de värden som du behöver.
4. Kopiera ADO.NET-anslutningssträngen, klistrar in den i web.config-filen över gamla anslutningssträngen för den lokala databasen och se till att lägga till `MultipleActiveResultSets=True`.

## <a name="publish-a-web-application-to-azure"></a>Publicera ett program till Azure
### <a name="to-publish-a-web-application-to-azure"></a>Publicera ett program till Azure
1. För att testa programmet i lokal utveckling-miljö med Azure compute emulator, öppna snabbmenyn för Azure-projekt för webbrollen och välj **Set as Startup Project**. Välj **felsöka**, **Start Debugging** (tangentbord: **F5**).

    Den **starta Azure-miljön felsökning** öppnas och programmet startas i webbläsaren. Mer information om hur du startar varje typ av webbprogram i beräkningsemulatorn, se tabellen i det här avsnittet.
2. Om du vill konfigurera tjänster för ditt program att publicera till Azure måste du ha ett Microsoft-konto och en Azure-prenumeration. Använda stegen i följande avsnitt för att ställa in dina tjänster: [förbereda för att publicera eller distribuera ett Azure-program från Visual Studio](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md).
3. Om du vill publicera webbappen till Azure, öppna snabbmenyn för webbprojektet och välj **publicera till Azure**.

    Den **publicera Azure-programmet** öppnas och Visual Studio startar distributionsprocessen. Mer information om hur du publicerar program finns i avsnittet **publicera ett Azure-program från Visual Studio** i [publicering av en tjänst i molnet med hjälp av Azure-verktyg](vs-azure-tools-publishing-a-cloud-service.md).

   > [!NOTE]
   > Du kan också publicera webbprogrammet från Azure-projekt. Gör detta genom att öppna snabbmenyn för Azure-projekt och välj **publicera**.
   >
   >
4. Om du vill se förloppet för distributionen kan du visa den **Azure-aktivitetsloggen** fönster. Den här loggen visas automatiskt när du startar distributionsprocessen. Du kan expandera radobjekt i aktivitetsloggen för att visa detaljerad information som visas i följande bild:

    ![VST_AzureActivityLog](./media/vs-azure-tools-migrate-publish-web-app-to-cloud-service/IC744149.png)
5. (Valfritt) Om du vill avbryta distributionsprocessen, öppna snabbmenyn för raden i aktivitetsloggen och välj **Avbryt och ta bort**. Detta stoppar distributionsprocessen och tar bort distributionsmiljö från Azure.

   > [!NOTE]
   > Om du vill ta bort den här distributionsmiljön när det har distribuerats, måste du använda den [klassiska Azure-portalen](http://go.microsoft.com/fwlink/?LinkID=213885).
   >
   >
6. (Valfritt) När dina rollinstanser har startat, visar Visual Studio automatiskt distributionsmiljön i den **Azure Compute** nod i **Cloud Explorer** eller **Server Explorer**. Härifrån kan du visa statusen för enskilda rollinstanser.

    Följande bild visar rollinstanser i **Server Explorer** medan de är fortfarande i tillståndet initierar:

    ![VST_DeployComputeNode](./media/vs-azure-tools-migrate-publish-web-app-to-cloud-service/IC744134.png)
7. Om du vill komma åt ditt program efter distribution, väljer du på pilen bredvid distributionen när statusen **slutförd** visas i den **Azure-aktivitetsloggen**. Visar URL-Adressen för ditt webbprogram i Azure. Se tabellen nedan för mer information om hur du startar en viss typ av webbprogrammet från Azure.

    I följande tabell visas information om hur starta specifika webbprogram från Azure eller att köra eller felsöka ett webbprogram lokalt med hjälp av Azure Compute Emulator:

   | Web programtyp | Kör/Debug lokalt med hjälp av Beräkningsemulatorn | körs i Azure |
   | --- | --- | --- |
   | ASP.NET-webbprogram |På menyraden, väljer du **felsöka**, **Start Debugging** (tangentbord: Välj den **F5** nyckel.). |Välj URL-hyperlänk visas i den **distribution** för den **Azure-aktivitetsloggen** att läsa in startsidan i webbläsaren. |
   | Webbprogram för ASP.NET MVC 2 |På menyraden, väljer du **felsöka**, **Start Debugging** (tangentbord: Välj den **F5** nyckel.). |Välj URL-hyperlänk visas i den **distribution** för den **Azure-aktivitetsloggen** att läsa in startsidan i webbläsaren. |
   | Webbprogram för ASP.NET MVC 3 |På menyraden, väljer du **felsöka**, **Start Debugging** (tangentbord: Välj den **F5** nyckel.). |Välj URL-hyperlänk visas i den **distribution** för den **Azure-aktivitetsloggen** att läsa in startsidan i webbläsaren. |
   | Webbprogram för ASP.NET MVC 4 |På menyraden, väljer du **felsöka**, **Start Debugging** (tangentbord: Välj den **F5** nyckel.). |Välj URL-hyperlänk visas i den **distribution** för den **Azure-aktivitetsloggen** att läsa in startsidan i webbläsaren. |
   | Tom ASP.NET-webbprogram |Du måste lägga till en .aspx-sida i ditt program som du anger som startsida för webbprojektet. På menyraden, Välj **felsöka**, **Start Debugging** (tangentbord: Välj den **F5** nyckel.). |Om du har en .aspx standardsida i ditt program, Välj länken URL som visas i den **distribution** för den **Azure-aktivitetsloggen** och den här sidan har lästs in i webbläsaren. Om du har en annan .aspx-sida måste du gå till den här specifika sidan med hjälp av följande format för URL: en:`<url for deployment>/<name of page>.aspx` |
   | Silverlight-program |På menyraden, väljer du **felsöka**, **Start Debugging** (tangentbord: Välj den **F5** nyckel.). |Måste du gå till sidan specifik för ditt program med hjälp av följande format för URL: en:`<url for deployment>/<name of page>.aspx` |
   | Silverlight affärsprogram |På menyraden, väljer du **felsöka**, **Start Debugging** (tangentbord: Välj den **F5** nyckel.). |Måste du gå till sidan specifik för ditt program med hjälp av följande format för URL: en:`<url for deployment>/<name of page>.aspx` |
   | Navigering Silverlight-program |På menyraden, väljer du **felsöka**, **Start Debugging** (tangentbord: Välj den **F5** nyckel.). |Måste du gå till sidan specifik för ditt program med hjälp av följande format för URL: en:`<url for deployment>/<name of page>.aspx` |
   | WCF-tjänstprogram |Du måste ange .svc-filen som startsida för projektet WCF-tjänst. På menyraden, Välj **felsöka**, **Start Debugging** (tangentbord: Välj den **F5** nyckel.). |Du måste gå till svc-fil för ditt program med hjälp av följande format för URL: en:`<url for deployment>/<name of service file>.svc` |
   | Tjänstprogrammet för WCF-arbetsflöde |Du måste ange .svc-filen som startsida för projektet WCF-tjänst. På menyraden, Välj **felsöka**, **Start Debugging** (tangentbord: Välj den **F5** nyckel.). |Du måste gå till svc-fil för ditt program med hjälp av följande format för URL: en:`<url for deployment>/<name of service file>.svc` |
   | ASP.NET dynamiska entiteter |På menyraden, väljer du **felsöka**, **Start Debugging** (tangentbord: Välj den **F5** nyckel.). |Du måste uppdatera anslutningssträngen (se nästa avsnitt). Du måste också gå till sidan specifik för ditt program med hjälp av följande format för URL: en:`<url for deployment>/<name of page>.aspx` |
   | ASP.NET dynamiska Data Linq to SQL |På menyraden, väljer du **felsöka**, **Start Debugging** (tangentbord: Välj den **F5** nyckel.). |Du måste följa stegen i den här proceduren: använder en SQL Azure-databas för ditt program (se tidigare avsnitt i det här avsnittet). Du måste också gå till sidan specifik för ditt program med hjälp av följande format för URL: en:`<url for deployment>/<name of page>.aspx` |

## <a name="update-a-connection-string-for-aspnet-dynamic-entities"></a>Uppdatera en anslutningssträng för ASP.NET dynamiska entiteter
### <a name="to-update-a-connection-string-for-aspnet-dynamic-entities"></a>Uppdatera en anslutningssträng för ASP.NET dynamiska entiteter
1. Om du vill skapa en SQL Azure-databas som kan användas för ett dynamiskt entiteter för ASP.NET-webbprogram följer du stegen i proceduren **använder en SQL Azure-databas för tillämpningsprogrammet** tidigare i det här avsnittet.
2. Lägg till de tabeller och fält som ska användas för den här databasen från den [klassiska Azure-portalen](http://go.microsoft.com/fwlink/?LinkID=213885).
3. Anslutningssträngen för den här typen av program har följande format i web.config-filen:  

    ```
    <addname="tempdbEntities"connectionString="metadata=res://*/Model1.csdl|res://*/Model1.ssdl|res://*/Model1.msl;provider=System.Data.SqlClient;provider connection string=&quot;data source=<server name>\SQLEXPRESS;initial catalog=<database name>;integrated security=True;multipleactiveresultsets=True;App=EntityFramework&quot;"providerName="System.Data.EntityClient"/>
    ```

    Uppdatering av *connectionString* värdet med ADO.NET-anslutningssträngen för SQL Azure-databasen på följande sätt:

    ```
    XMLCopy<addname="tempdbEntities"connectionString="metadata=res://*/Model1.csdl|res://*/Model1.ssdl|res://*/Model1.msl;provider=System.Data.SqlClient;provider connection string=&quot;Server=tcp:<SQL Azure server name>.database.windows.net,1433;Database=<database name>;User ID=<user name>;Password=<password>;Trusted_Connection=False;Encrypt=True;multipleactiveresultsets=True;App=EntityFramework&quot;"providerName="System.Data.EntityClient"/>
    ```
4. Om du vill spara web.config-filen med ändringarna som du har gjort i anslutningssträngen, på menyraden väljer **filen**, **spara web.config**.

## <a name="supported-project-templates"></a>Stöds projektmallar
Om du vill publicera ett program till Azure måste programmet använda ett av projektmallarna för C# eller Visual Basic som anges i tabellen nedan.

| Projektgruppen för mallen | Projektmall |
| --- | --- |
| Webb |ASP.NET-webbprogram |
| Webb |Webbprogram för ASP.NET MVC 2 |
| Webb |Webbprogram för ASP.NET MVC 3 |
| Webb |MVC4 för ASP.NET-webbprogram |
| Webb |Tom ASP.NET-webbprogram |
| Webb |Tomt webbprogram för ASP.NET MVC 2 |
| Webb |Webbprogram för ASP.NET dynamiska Data entiteter |
| Webb |ASP.NET dynamiska Data Linq to SQL-webbprogram |
| Silverlight |Silverlight-program |
| Silverlight |Silverlight affärsprogram |
| Silverlight |Navigering Silverlight-program |
| WCF |WCF-tjänstprogram |
| WCF |Tjänstprogrammet för WCF-arbetsflöde |
| Arbetsflöde |Tjänstprogrammet för WCF-arbetsflöde |

## <a name="next-steps"></a>Nästa steg
Mer information om publicering finns [förbereda för att publicera eller distribuera ett Azure-program från Visual Studio](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md). Se även [inställningen upp med namnet autentiseringsuppgifter](vs-azure-tools-setting-up-named-authentication-credentials.md).
