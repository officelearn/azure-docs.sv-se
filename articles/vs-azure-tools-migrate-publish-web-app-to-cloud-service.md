---
title: "Hur du migrerar och publicera ett program till en Azure-molntjänst från Visual Studio | Microsoft Docs"
description: "Lär dig hur du migrerar och publicera ditt webbprogram till en Azure-molntjänst med hjälp av Visual Studio"
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
ms.date: 11/10/2017
ms.author: kraigb
ms.openlocfilehash: d5d41ab47c17a024900efc88ba0a006da63ab246
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2017
---
# <a name="how-to-migrate-and-publish-a-web-application-to-an-azure-cloud-service-from-visual-studio"></a>Så här: migrera och publicera ett program till en Azure-molntjänst från Visual Studio

Om du vill dra nytta av värd-tjänster och skalning möjligheten för Azure, kanske du vill migrera och distribuera ditt webbprogram till en Azure-molntjänst. Bara minimala ändringar krävs. Den här artikeln beskriver hur du distribuerar till molntjänster. App Service finns [distribuera en webbapp i Azure App Service](app-service/app-service-deploy-local-git.md).

> [!Important]
> Den här migreringen stöds endast för särskilda projekt för ASP.NET, Silverlight, WCF och WCF-arbetsflöde. Det finns inte stöd för ASP.NET Core projekt. Se [stöds projektmallar](#supported-project-templates).

## <a name="migrate-a-project-to-cloud-services"></a>Migrera ett projekt till molntjänster

1. Högerklicka på webbapprojektet och välj **konvertera > Konvertera till Microsoft Azure Cloud Service-projekt**. (Att det här kommandot inte visas om du redan har ett webbprojekt roll i lösningen.)
1. Visual Studio skapar en molntjänstprojektet i lösningen som innehåller nödvändiga webbrollen. Namnet på det här projektet är samma som projektet med plus suffixet `.Azure`.
1. Visual Studio anger också de **kopiera lokala** egenskap till true för alla sammansättningar som krävs för MVC 2, MVC 3, 4 MVC och Silverlight affärsprogram. Den här egenskapen lägger till dessa sammansättningar tjänstpaket som används för distribution.

   > [!Important]
   > Om du har andra sammansättningar eller filer som krävs för det här webbprogrammet, måste du manuellt ange egenskaperna för de här filerna. Information om hur du anger dessa egenskaper finns i [inkludera filer i tjänstpaketet](#include-files-in-the-service-package).

### <a name="errors-and-warnings"></a>Fel och varningar

Några varningar eller fel som inträffar tyda på problem med att åtgärda innan du distribuerar till Azure, till exempel saknas sammansättningar.

Om du skapar ditt program, köra den lokalt med hjälp av beräkningsemulatorn eller publicera den på Azure, kan du se felet: ”den angivna sökvägen, filnamnet eller båda är för långt”. Det här felet indikerar att det fullständigt kvalificerade Azure projektnamnet längd överskrider 146 tecken. Åtgärda problemet genom att flytta din lösning till en annan mapp med en kortare sökväg.

Läs mer om hur du behandlar alla varningar som fel [konfigurera Azure Cloud Service-projekt i Visual Studio](vs-azure-tools-configuring-an-azure-project.md).

### <a name="test-the-migration-locally"></a>Testa migreringen lokalt

1. I Visual Studio **Solution Explorer**tillagda molntjänstprojektet högerklickar och väljer **Set as Startup Project**.
1. Välj **Felsök > Starta felsökning** (F5) för att starta Azure felsökningsmiljö. Den här miljön ger specifikt emulering av olika Azure-tjänster.

### <a name="use-an-azure-sql-database-for-your-application"></a>Använda en Azure SQL Database för programmet

Om du har en anslutningssträng för ditt webbprogram som använder en lokal SQL Server-databas måste du migrera din databas till Azure SQL Database i stället och uppdatera anslutningssträngen. Vägledning för den här processen finns i följande avsnitt:

- [Migrering av SQL Server-databasen till SQL-databas i molnet](sql-database/sql-database-cloud-migrate.md)
- [Använda .NET (C#) med Visual Studio för att ansluta och fråga och Azure SQL database](sql-database/sql-database-connect-query-dotnet-visual-studio.md).

## <a name="publish-the-application-to-azure-cloud-service"></a>Publicera programmet till Azure-molntjänst

1. Skapar nödvändiga cloud service och lagring i din Azure-prenumeration enligt beskrivningen på [förbereda för att publicera eller distribuera ett Azure-program från Visual Studio](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md).
1. Högerklicka på det programprojektet i Visual Studio och välj **publicera till Microsoft Azure...**  (som skiljer sig från kommandot ”Publicera...”.).
1. I den **publicera Azure-programmet** som visas, logga in med kontot med din Azure-prenumeration och välj **Nästa >**.
1. I den **Inställningar > Vanliga inställningar** , Välj molnet Måltjänsten från den **Molntjänsten** listrutan, tillsammans med din valda miljö och konfigurationer. 
1. I **Inställningar > Avancerade inställningar**, Välj lagringskontot för att använda och sedan välja **Nästa >**.
1. I **diagnostik**, Välj om du vill skicka information till Application Insights.
1. Välj **Nästa >** Välj sedan om du vill visa en sammanfattning **publicera** Inled distributionen.
1. Visual Studio öppnas en aktivitet loggen där du kan följa förloppet:

    ![VST_AzureActivityLog](./media/vs-azure-tools-migrate-publish-web-app-to-cloud-service/IC744149.png)

1. (Valfritt) Om du vill avbryta distributionsprocessen, högerklicka på radobjekt i aktivitetsloggen och välj **Avbryt och ta bort**. Det här kommandot stoppar distributionsprocessen och tar bort distributionsmiljö från Azure. Obs: Om du vill ta bort den här distributionsmiljön när det har distribuerats, måste du använda den [Azure-portalen](https://portal.azure.com).
1. (Valfritt) När dina rollinstanser har startat, visar Visual Studio automatiskt distributionsmiljön i den **Server Explorer > Cloud Services** nod. Härifrån kan du visa statusen för enskilda rollinstanser.
1. Om du vill komma åt ditt program efter distribution, väljer du på pilen bredvid distributionen när statusen **slutförd** visas i den **Azure-aktivitetsloggen** tillsammans med URL: en. Se tabellen nedan för mer information om hur du startar en viss typ av webbprogrammet från Azure.

## <a name="using-the-compute-emulator-and-starting-application-in-azure"></a>Med beräkningsemulatorn och starta programmet i Azure

Alla typer av programmet kan startas i en webbläsare som är anslutna till Visual Studio-felsökaren genom att välja **Felsök > Starta felsökning** (F5). Med ett tomt webbprogram för ASP.NET-projekt måste du först lägga till en `.aspx` i ditt program och ange som startsida för webbprojektet.

Följande tabell innehåller information om hur du startar programmet i Azure:

   | Web programtyp | körs i Azure |
   | --- | --- | --- |
   | ASP.NET-webbprogram<br/>(inklusive MVC 2, 3 MVC, MVC-4) | Välj URL-Adressen i den **distribution** för den **Azure-aktivitetsloggen**. |
   | Tom ASP.NET-webbprogram | Om du har en standard `.aspx` i ditt program, markera en URL i den **distribution** för den **Azure-aktivitetsloggen**. Ange en URL i formatet i en webbläsare för att navigera till en annan sida:`<deployment_url>/<page_name>.aspx` |
   | Silverlight-program<br/>Silverlight affärsprogram<br/>Navigering Silverlight-program | Gå till sidan specifik för ditt program med hjälp av följande URL-format:`<deployment_url>/<page_name>.aspx` |
    WCF-tjänstprogram<br/>Tjänstprogrammet för WCF-arbetsflöde | Ange den `.svc` filen som startsida för projektet WCF-tjänst. Gå till`<deployment_url>/<service_file>.svc` |
   | ASP.NET dynamiska entiteter<br/>ASP.NET dynamiska Data Linq to SQL | Uppdatera anslutningssträngen som beskrivs i nästa avsnitt. Gå sedan till `<deployment_url>/<page_name>.aspx`. Du måste använda en Azure SQL database för Linq till SQL. |

## <a name="update-a-connection-string-for-aspnet-dynamic-entities"></a>Uppdatera en anslutningssträng för ASP.NET dynamiska entiteter

1. Skapa en SQL Azure-databas för ett dynamiskt entiteter för ASP.NET-webbprogram som beskrivits tidigare i (#use-an-azuresql-database-for-your-application).
1. Lägg till de tabeller och fält som ska användas för den här databasen från Azure-portalen.
1. Ange en anslutningssträng i den `web.config` filen med följande format och spara filen:

    ```xml
    <addname="tempdbEntities"connectionString="metadata=res://*/Model1.csdl|res://*/Model1.ssdl|res://*/Model1.msl;provider=System.Data.SqlClient;provider connection string=&quot;data source=<server name>\SQLEXPRESS;initial catalog=<database name>;integrated security=True;multipleactiveresultsets=True;App=EntityFramework&quot;"providerName="System.Data.EntityClient"/>
    ```

    Uppdatering av *connectionString* värdet med ADO.NET-anslutningssträngen för SQL Azure-databasen på följande sätt:

    ```xml
    XMLCopy<addname="tempdbEntities"connectionString="metadata=res://*/Model1.csdl|res://*/Model1.ssdl|res://*/Model1.msl;provider=System.Data.SqlClient;provider connection string=&quot;Server=tcp:<SQL Azure server name>.database.windows.net,1433;Database=<database name>;User ID=<user name>;Password=<password>;Trusted_Connection=False;Encrypt=True;multipleactiveresultsets=True;App=EntityFramework&quot;"providerName="System.Data.EntityClient"/>
    ```

## <a name="supported-project-templates"></a>Stöds projektmallar

Program som kan migreras och publiceras på molntjänster måste använda en av mallarna i tabellen nedan. ASP.NET Core stöds inte.

| Mall-grupp | Projektmall |
| --- | --- |
| Webb | ASP.NET-webbprogram (.NET Framework) |
| Webb | Webbprogram för ASP.NET MVC 2 |
| Webb | Webbprogram för ASP.NET MVC 3 |
| Webb | MVC4 för ASP.NET-webbprogram |
| Webb | Tom ASP.NET-webbprogram (eller plats) |
| Webb | Tomt webbprogram för ASP.NET MVC 2 |
| Webb | Webbprogram för ASP.NET dynamiska Data entiteter |
| Webb | ASP.NET dynamiska Data Linq to SQL-webbprogram |
| Silverlight | Silverlight-program |
| Silverlight | Silverlight affärsprogram |
| Silverlight | Navigering Silverlight-program |
| WCF | WCF-tjänstprogram |
| WCF | Tjänstprogrammet för WCF-arbetsflöde |
| Arbetsflöde | Tjänstprogrammet för WCF-arbetsflöde |

## <a name="next-steps"></a>Nästa steg

- [Förbereda för att publicera eller distribuera ett Azure-program från Visual Studio](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md)
- [Konfigurerar namnet autentiseringsuppgifter](vs-azure-tools-setting-up-named-authentication-credentials.md).
