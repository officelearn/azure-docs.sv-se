---
title: ta med fil
description: ta med fil
services: app-service\mobile
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/06/2019
ms.author: crdun
ms.custom: include file
ms.openlocfilehash: a7c994f85d90e94d514bb4e4f91a6644ed45432c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66455137"
---
1. Ladda ned klienten SDK snabbstarter för följande plattformar:
    
    [iOS (Objective-C)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/iOS)  
    [iOS (Swift)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/iOS-Swift)  
    [Android (Java)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/android)  
    [Xamarin.iOS](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.iOS)  
    [Xamarin.Android](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.android)  
    [Xamarin.Forms](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.forms)  
    [Cordova](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/cordova)  
    [Windows (C#)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/windows-uwp-cs)  

    > [!NOTE]
    > Om du använder iOS-projektet måste du ladda ned ”azuresdk-iOS -\*.zip” från [senaste GitHub-versionen](https://github.com/Azure/azure-mobile-apps-ios-client/releases/latest). Packa upp och lägga till den `MicrosoftAzureMobile.framework` filen till projektets rot.
    >

2. Du måste lägga till en databasanslutning eller ansluta till en befintlig anslutning. Först bestämma om du ska skapa ett datalager eller Använd en befintlig.

    - **Skapa ett nytt datalager**: Om du ska skapa ett datalager, använder du följande Snabbstart:

        [Snabbstart: Komma igång med en enskild databas i Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-quickstart-guide)

    - **Befintlig datakälla**: Följ anvisningarna nedan om du vill använda en befintlig databasanslutning

        1. Anslutningssträngen för SQL Database-format: `Data Source=tcp:{your_SQLServer},{port};Initial Catalog={your_catalogue};User ID={your_username};Password={your_password}`

           **{your_SQLServer}**  Namnet på servern, denna finns i översiktssidan för databasen och är vanligtvis i form av ”server_name.database.windows.net”.
            **{port}**  vanligtvis 1433.
            **{your_catalogue}**  Namnet på databasen.
            **{your_username}**  Användarnamn för att komma åt databasen.
            **{your_password}**  Lösenord för åtkomst till din databas.

            [Mer information om format för SQL-anslutningssträng](https://docs.microsoft.com/dotnet/framework/data/adonet/connection-string-syntax#sqlclient-connection-strings)

        2. Lägg till anslutningssträng till din **mobilappen** i App Service du kan hantera anslutningssträngar för ditt program med hjälp av den **Configuration** alternativ på menyn.

            Lägga till en anslutningssträng:

            1. Klicka på den **programinställningar** fliken.

            2. Klicka på **[+] ny anslutningssträng**.

            3. Du måste ange **namn**, **värdet** och **typ** för anslutningssträngen.

            4. Typ **namn** som `MS_TableConnectionString`

            5. Värdet bör vara den anslutande strängen du formaterat i steget innan.

            6. Om du lägger till en anslutningssträng till en SQL Azure-databas väljer **SQLAzure** under **typ**.

3. Azure Mobile Apps har SDK: er för .NET och Node.js-serverdelen.

   - **Node.js-serverdelen**
    
     Om du ska använda appen Node.js-Snabbstart, följer du anvisningarna nedan.

     1. I Azure-portalen går du till **enkla tabeller**, visas den här skärmen.
      
        ![Noden enkla tabeller](./media/app-service-mobile-configure-new-backend/node-easy-tables.png)

     2. Kontrollera att SQL-anslutningssträng har redan lagts till i den **Configuration** fliken. Markera kryssrutan på **bekräftar jag att allt webbinnehåll skrivs** och klicka på den **skapa TodoItem-tabell** knappen.
     
        ![Enkla tabeller nodkonfiguration](./media/app-service-mobile-configure-new-backend/node-easy-tables-configuration.png)

     3. I **enkla tabeller**, klickar du på den **+ Lägg till** knappen.
    
        ![Noden enkla tabeller Lägg till knapp](./media/app-service-mobile-configure-new-backend/node-easy-tables-add.png)

     4. Skapa en `TodoItem` tabell med anonym åtkomst.
      
        ![Noden enkla tabeller Lägg till tabell](./media/app-service-mobile-configure-new-backend/node-easy-tables-table-add.png)

   - **.NET-serverdelen**
    
        Om du ska använda appen för .NET-Snabbstart, följer du anvisningarna nedan.

        1. Ladda ned Azure Mobile Apps .NET server-projekt från den [lagringsplatsen för azure-mobile-appar – snabbstarter](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/backend/dotnet/Quickstart).

        2. Skapa .NET serverprojektet lokalt i Visual Studio.

        3. Öppna i Visual Studio Solution Explorer, högerklicka på `ZUMOAPPNAMEService` projektet, klicka på **publicera**, visas en `Publish to App Service` fönster. Om du arbetar på Mac kan du kolla andra sätt att distribuera appen [här](https://docs.microsoft.com/azure/app-service/deploy-local-git).
        
           ![Visual studio-publicering](./media/app-service-mobile-configure-new-backend/visual-studio-publish.png)

        4. Välj **Apptjänst** som publicerar mål, sedan klickar du på **Välj befintligt**, klicka sedan på den **publicera** knappen längst ned i fönstret.

        5. Du måste logga in på Visual Studio med din Azure-prenumeration först. Välj den `Subscription`, `Resource Group`, och välj sedan namnet på din app. När du är klar klickar du på **OK**, detta distribuerar .NET server-projektet som du har lokalt till App Service-serverdelen. När distributionen är klar kommer du att omdirigeras till `http://{zumoappname}.azurewebsites.net/` i webbläsaren.
        
           ![Serverdelen är igång](./media/app-service-mobile-configure-new-backend/backend-is-up.png)
