---
title: Hur du migrerar och publicera en Webbapp till en Azure-molntjänst från Visual Studio | Microsoft Docs
description: Lär dig hur du migrerar och publicera ditt webbprogram till en Azure-molntjänst med hjälp av Visual Studio
services: visual-studio-online
author: ghogen
manager: douge
ms.assetid: 9394adfd-a645-4664-9354-dd5df08e8c91
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/10/2017
ms.author: ghogen
ms.openlocfilehash: cf1e01d0165d91570c3985850b0a780b61c6b8e5
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2018
ms.locfileid: "42055626"
---
# <a name="how-to-migrate-and-publish-a-web-application-to-an-azure-cloud-service-from-visual-studio"></a>Så här: migrera och publicera en Webbapp till en Azure-molntjänst från Visual Studio

Om du vill dra nytta av värdtjänster och skala kapaciteten hos Azure, kanske du vill migrera och distribuera ditt webbprogram till en Azure-molntjänst. Endast minimala ändringar krävs. Den här artikeln beskriver distribution till molntjänster. App Service finns [distribuera en webbapp i Azure App Service](app-service/app-service-deploy-local-git.md).

> [!Important]
> Den här migreringen stöds endast för särskilda projekt för ASP.NET, Silverlight, WCF och WCF-arbetsflöde. Det finns inte stöd för ASP.NET Core-projekt. Se [stöds projektmallar](#supported-project-templates).

## <a name="migrate-a-project-to-cloud-services"></a>Migrera ett projekt till molntjänster

1. Högerklicka på programmet webbprojektet och välj **konvertera > Konvertera till Microsoft Azure-Molntjänstprojekt**. (Observera att det här kommandot inte visas om du redan har en webbrollsprojektet i lösningen.)
1. Visual Studio skapar en molntjänstprojektet i lösningen som innehåller nödvändiga webbserverrollen. Namnet på det här projektet är samma som projektet med plus suffixet `.Azure`.
1. Visual Studio anger även den **kopiera lokala** egenskapen till SANT för alla sammansättningar som krävs för MVC-2, MVC 3, MVC 4 och Silverlight affärsprogram. Den här egenskapen lägger till dessa sammansättningar tjänstpaket som används för distribution.

   > [!Important]
   > Om du har andra sammansättningar eller filer som krävs för det här webbprogrammet kan ange du egenskaperna för de här filerna manuellt. Information om hur du anger dessa egenskaper finns i [ta med filer i tjänstpaketet](#include-files-in-the-service-package).

### <a name="errors-and-warnings"></a>Fel och varningar

Eventuella varningar och fel som inträffar tyda på problem med att åtgärda innan du distribuerar till Azure, till exempel saknas sammansättningar.

Om du ett program, köra den lokalt med hjälp av compute-emulatorn eller publicera den på Azure, kan du se felet: ”den angivna sökvägen, filnamnet eller båda är för långt”. Det här felet indikerar att namnet på fullständigt kvalificerade Azure-projekt överskrider 146 tecken. Åtgärda problemet genom att flytta din lösning till en annan mapp med en kortare sökväg.

Läs mer om hur du kan hantera alla varningar som fel, [konfigurera ett Azure Cloud Service-projekt med Visual Studio](vs-azure-tools-configuring-an-azure-project.md).

### <a name="test-the-migration-locally"></a>Testa migrering lokalt

1. I Visual Studio **Solution Explorer**, högerklickar du på molntjänstprojektet har lagts till och välj **Set as Startup Project**.
1. Välj **Felsök > Starta felsökning** (F5) för att starta Azure felsökningsmiljö. Den här miljön innehåller specifikt emulering av olika Azure-tjänster.

### <a name="use-an-azure-sql-database-for-your-application"></a>Använd en Azure SQL-databas för ditt program

Om du har en anslutningssträng för ditt webbprogram som använder en lokal SQL Server-databas, måste du migrera din databas till Azure SQL Database i stället och uppdatera din anslutningssträng. Vägledning för den här processen finns i följande avsnitt:

- [Migrering av SQL Server-databas till SQL-databas i molnet](sql-database/sql-database-cloud-migrate.md)
- [Använda .NET (C#) med Visual Studio för att ansluta och fråga efter och Azure SQL-databas](sql-database/sql-database-connect-query-dotnet-visual-studio.md).

## <a name="publish-the-application-to-azure-cloud-service"></a>Publicera programmet till Azure-molntjänst

1. Skapa nödvändiga cloud service och storage-konton i din Azure-prenumeration enligt beskrivningen på [förbereda för att publicera eller distribuera ett Azure-program från Visual Studio](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md).
1. Högerklicka på programprojektet i Visual Studio och välj **publicera till Microsoft Azure...**  (vilket är detsamma som kommandot ”Publicera...”.).
1. I den **publicera Azure-program** som visas, logga in med kontot med din Azure-prenumeration och välj **Nästa >**.
1. I den **Inställningar > Vanliga inställningar** väljer du en målmolntjänst från den **molntjänst** listrutan, tillsammans med den valda miljön och konfigurationer. 
1. I **Inställningar > Avancerade inställningar**, Välj lagringskontot för att använda och välj sedan **Nästa >**.
1. I **diagnostik**, Välj om du vill skicka information till Application Insights.
1. Välj **Nästa >** om du vill visa en sammanfattning, Välj **publicera** att starta distributionen.
1. Visual Studio öppnas ett aktivitetsfönster log där du kan följa förloppet:

    ![VST_AzureActivityLog](./media/vs-azure-tools-migrate-publish-web-app-to-cloud-service/IC744149.png)

1. (Valfritt) Om du vill avbryta distributionsprocessen, högerklicka på objektet på raden i aktivitetsloggen och välj **avbryta och ta bort**. Detta kommando stoppar distributionsprocessen och tar bort distributionsmiljö från Azure. Obs: Om du vill ta bort den här distributionsmiljön när den har distribuerats, måste du använda den [Azure-portalen](https://portal.azure.com).
1. (Valfritt) När dina rollinstanser har startat, visar Visual Studio automatiskt distributionsmiljö i den **Server Explorer > molntjänster** noden. Härifrån kan du visa status för enskilda rollinstanser.
1. Om du vill komma åt ditt program efter distributionen, klicka på pilen bredvid distributionen när statusen **slutförd** visas i den **Azure-aktivitetsloggen** tillsammans med URL: en. Se tabellen nedan för mer information om hur du startar en viss typ av webbprogram från Azure.

## <a name="using-the-compute-emulator-and-starting-application-in-azure"></a>Med hjälp av compute-emulatorn och starta programmet i Azure

Alla programtyper kan startas i en webbläsare som är ansluten till Visual Studio-felsökaren genom att välja **Felsök > Starta felsökning** (F5). Med en tom ASP.NET-Webbapp-projekt måste du först lägga till en `.aspx` i ditt program och ange den som startsida för ditt projekt.

I följande tabell innehåller information om hur du startar programmet i Azure:

   | Web-programtyp | Som körs i Azure |
   | --- | --- | --- |
   | ASP.NET-webbprogram<br/>(inklusive MVC 2, MVC 3, MVC 4) | Välj URL: en i den **distribution** fliken för den **Azure-aktivitetsloggen**. |
   | Tom ASP.NET-webbprogram | Om du har en standard `.aspx` i ditt program, väljer du URL: en i den **distribution** fliken för den **Azure-aktivitetsloggen**. Gå till en annan sida genom att ange en URL följande format i en webbläsare: `<deployment_url>/<page_name>.aspx` |
   | Silverlight-program<br/>Silverlight-affärsprogram<br/>Silverlight-program för navigering | Gå till sidan specifika för ditt program med följande URL-format: `<deployment_url>/<page_name>.aspx` |
    WCF-tjänstprogram<br/>Tjänstprogrammet för WCF-arbetsflöde | Ange den `.svc` filen som startsida för projektet WCF-tjänst. Gå sedan till `<deployment_url>/<service_file>.svc` |
   | ASP.NET-Dynamic-entiteter<br/>ASP.NET dynamiska Data Linq till SQL | Uppdatera anslutningssträngen enligt beskrivningen i nästa avsnitt. Gå sedan till `<deployment_url>/<page_name>.aspx`. För Linq till SQL, måste du använda en Azure SQL database. |

## <a name="update-a-connection-string-for-aspnet-dynamic-entities"></a>Uppdatera en anslutningssträng för ASP.NET-Dynamic-entiteter

1. Skapa en SQL Azure-databas för en dynamisk entiteter för ASP.NET-webbprogram som beskrivits tidigare i (#use-an-azuresql-database-for-your-application).
1. Lägg till de tabeller och fält som ska användas för den här databasen från Azure-portalen.
1. Ange en anslutningssträng i den `web.config` fil med följande format och spara filen:

    ```xml
    <addname="tempdbEntities"connectionString="metadata=res://*/Model1.csdl|res://*/Model1.ssdl|res://*/Model1.msl;provider=System.Data.SqlClient;provider connection string=&quot;data source=<server name>\SQLEXPRESS;initial catalog=<database name>;integrated security=True;multipleactiveresultsets=True;App=EntityFramework&quot;"providerName="System.Data.EntityClient"/>
    ```

    Uppdatera den *connectionString* värde med ADO.NET-anslutningssträngen för din SQL Azure-databas på följande sätt:

    ```xml
    XMLCopy<addname="tempdbEntities"connectionString="metadata=res://*/Model1.csdl|res://*/Model1.ssdl|res://*/Model1.msl;provider=System.Data.SqlClient;provider connection string=&quot;Server=tcp:<SQL Azure server name>.database.windows.net,1433;Database=<database name>;User ID=<user name>;Password=<password>;Trusted_Connection=False;Encrypt=True;multipleactiveresultsets=True;App=EntityFramework&quot;"providerName="System.Data.EntityClient"/>
    ```

## <a name="supported-project-templates"></a>Stöds projektmallar

Program som kan migreras och publiceras till molntjänster måste använda någon av mallarna i tabellen nedan. ASP.NET Core stöds inte.

| Mallgrupp | Projektmall |
| --- | --- |
| Webb | ASP.NET-Webbtillämpningsprogram (.NET Framework) |
| Webb | ASP.NET MVC 2-webbprogram |
| Webb | ASP.NET MVC 3-webbprogram |
| Webb | Webbprogram med ASP.NET MVC4 |
| Webb | Tom ASP.NET-webbprogram (eller plats) |
| Webb | Tom ASP.NET MVC 2-webbprogram |
| Webb | ASP.NET dynamisk Dataentiteter webbprogram |
| Webb | ASP.NET dynamiska Data Linq till SQL-webbprogram |
| Silverlight | Silverlight-program |
| Silverlight | Silverlight-affärsprogram |
| Silverlight | Silverlight-program för navigering |
| WCF | WCF-tjänstprogram |
| WCF | Tjänstprogrammet för WCF-arbetsflöde |
| Arbetsflöde | Tjänstprogrammet för WCF-arbetsflöde |

## <a name="next-steps"></a>Nästa steg

- [Förbereda för att publicera eller distribuera ett Azure-program från Visual Studio](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md)
- [Konfigurera med namnet autentiseringsuppgifter](vs-azure-tools-setting-up-named-authentication-credentials.md).
