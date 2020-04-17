---
title: ta med fil
description: ta med fil
services: app-service\mobile
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 04/15/2020
ms.author: crdun
ms.custom: include file
ms.openlocfilehash: b5b6389491fd91b716a05e7c57c98addb6352bd9
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461840"
---
1. Ladda ner klientens SDK-snabbstarter för följande plattformar:
    
    [iOS (Mål-C)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/iOS)  
    [iOS (Swift)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/iOS-Swift)  
    [Android (Java)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/android)  
    [Xamarin.iOS](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.iOS)  
    [Xamarin.Android](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.android)  
    [Xamarin.Forms](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.forms)  
    [Cordova](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/cordova)  
    [Windows (C#)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/windows-uwp-cs)  

    > [!NOTE]
    > Om du använder iOS-projektet måste du hämta "azuresdk-iOS-\*.zip" från [senaste GitHub-versionen](https://github.com/Azure/azure-mobile-apps-ios-client/releases/latest). Packa upp och `MicrosoftAzureMobile.framework` lägg till filen i projektets rot.
    >

2. Du måste lägga till en databasanslutning eller ansluta till en befintlig anslutning. Ta först reda på om du ska skapa ett datalager eller använda ett befintligt.

    - **Skapa ett nytt datalager**: Om du ska skapa ett datalager använder du följande snabbstart:

        [Snabbstart: Komma igång med enskilda databaser i Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-quickstart-guide)

    - **Befintlig datakälla**: Följ instruktionerna nedan om du vill använda en befintlig databasanslutning

        1. Format för SQL-databasanslutningssträng -`Data Source=tcp:{your_SQLServer},{port};Initial Catalog={your_catalogue};User ID={your_username};Password={your_password}`

           **{your_SQLServer}** Namnet på servern, detta finns i översiktssidan för databasen och är oftast i form av "server_name.database.windows.net".
            **{port}** vanligtvis 1433.
            **{your_catalogue}** Databasens namn.
            **{your_username}** Användarnamn för att komma åt databasen.
            **{your_password}** Lösenord för att komma åt databasen.

            [Läs mer om SQL Connection String-format](https://docs.microsoft.com/dotnet/framework/data/adonet/connection-string-syntax#sqlclient-connection-strings)

        2. Lägg till anslutningssträngen i **mobilappen** I App Service kan du hantera anslutningssträngar för ditt program med alternativet **Konfiguration** på menyn.

            Så här lägger du till en anslutningssträng:

            1. Klicka på fliken **Programinställningar.**

            2. Klicka på **[+] Ny anslutningssträng**.

            3. Du måste ange **Namn,** **Värde** och **Typ** för anslutningssträngen.

            4. Skriv **namn** som`MS_TableConnectionString`

            5. Värdet ska vara den anslutningssträng som du skapade i steget innan.

            6. Om du lägger till en anslutningssträng i en SQL Azure-databas väljer du **SQLAzure** under **typ**.

3. Azure Mobile Apps har SDK:er för .NET och Node.js backends.

   - **Nod.js backend**
    
     Om du ska använda appen Node.js quickstart följer du instruktionerna nedan.
     
        1. Skapa ett nytt API - Du kan antingen göra ändringar direkt i Azure-portalen eller ändra koden lokalt i utvecklingsmiljön och sedan publicera till Azure. Klicka på `App Service Editor (Preview)` `Development Tools` undermenyn som ger en redigeringsupplevelse i webbläsaren för din appkod.
        
        2. Klicka `Go` på och när App Service Editor öppnas har du full kontroll över källkoden. Förutsatt att du redan har installerat express- och azure-mobile-apps-paket med npm-installationskommandot klickar du på api-mappen under WWWROOT för att skapa eller redigera anpassat API. Gör ändringarna i kodfilen så sparas ändringarna automatiskt.
        
        3. Du har full kontroll över Azure SQL Database som används för att lagra programdata. Du kan enkelt skapa nya tabeller i databasen.
 
   - **.NET-serverdelen**
    
        Om du ska använda .NET quickstart-appen följer du instruktionerna nedan.

        1. Ladda ned serverprojektet För Azure Mobile Apps .NET från [databasen azure-mobile-apps-quickstarts](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/backend/dotnet/Quickstart).

        2. Skapa .NET-serverprojektet lokalt i Visual Studio.

        3. Öppna Solution Explorer i Visual Studio, `ZUMOAPPNAMEService` högerklicka på projektet, klicka `Publish to App Service` på **Publicera,** ett fönster. Om du arbetar på Mac kan du läsa andra sätt att distribuera appen [här](https://docs.microsoft.com/azure/app-service/deploy-local-git).
        
           ![Visuell studio publicering](./media/app-service-mobile-configure-new-backend/visual-studio-publish.png)

        4. Välj **Apptjänst** som publiceringsmål och klicka sedan på **Välj befintlig**och klicka sedan på knappen **Publicera** längst ned i fönstret.

        5. Du måste logga in på Visual Studio med din Azure-prenumeration först. Välj `Subscription`, `Resource Group`och välj sedan namnet på appen. När du är klar klickar du på **OK**, detta distribuerar .NET-serverprojektet som du har lokalt i apptjänstens serverdel. När distributionen är klar omdirigeras `http://{zumoappname}.azurewebsites.net/` du till i webbläsaren.                   
