---
title: Kontinuerlig distribution för Azure Functions | Microsoft Docs
description: Använda kontinuerlig distribution-funktioner i Azure App Service för att publicera dina funktioner.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/25/2016
ms.author: glenga
ms.openlocfilehash: cb3f3ad3bb7b42429654ea4bf9b49f7e230db1da
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64943888"
---
# <a name="continuous-deployment-for-azure-functions"></a>Löpande distribution för Azure Functions
Azure Functions gör det enkelt att distribuera funktionsappen med kontinuerlig integrering. Functions kan integreras med större koddatabaser och distributionskällor. Den här integreringen möjliggör ett arbetsflöde där Funktionskoden uppdaterar görs via en av dessa tjänster utlösaren distribution till Azure. Om du inte har använt Azure Functions kan du börja med den [översikt över Azure Functions](functions-overview.md).

Kontinuerlig distribution är ett bra alternativ för projekt där du integrerar flera och frekventa bidrag. Du kan också behålla källkontroll på funktionskoden. Azure Functions har stöd för följande distributionskällor:

* [Bitbucket](https://bitbucket.org/)
* [Dropbox](https://www.dropbox.com/)
* Extern lagringsplats (Git- eller Mercurial)
* [Lokal Git-lagringsplats](../app-service/deploy-local-git.md)
* [GitHub](https://github.com)
* [OneDrive](https://onedrive.live.com/)
* [Azure DevOps](https://azure.microsoft.com/services/devops/)

Distributioner är konfigurerade på basis av per funktion app. När kontinuerlig distribution har aktiverats, att Funktionskoden i portalen har angetts till *skrivskyddad*.

## <a name="requirements-for-continuous-deployment"></a>Krav för kontinuerlig distribution

Innan du ställer in kontinuerlig distribution måste du ha din distributionskälla som konfigurerats och Funktionskoden i distributionskälla. I en funktion i appdistribution är varje funktion i en namngiven underkatalog, där katalognamnet är namnet på funktionen.  

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

För att kunna distribuera från Azure DevOps, måste du först koppla organisationen Azure DevOps med Azure-prenumerationen. Mer information finns i [ställer in faktureringen för din Azure DevOps-organisation](https://docs.microsoft.com/azure/devops/organizations/billing/set-up-billing-for-your-organization-vs?view=vsts#set-up-billing-via-the-azure-portal).

## <a name="set-up-continuous-deployment"></a>Konfigurera kontinuerlig distribution
Använd den här proceduren för att konfigurera kontinuerlig distribution för en befintlig funktionsapp. De här stegen visar integrering med en GitHub-lagringsplats, men liknande steg som gäller för Azure DevOps- eller förhandstjänster distribution.

1. I funktionsappen i den [Azure-portalen](https://portal.azure.com)väljer **plattformsfunktioner** > **distributionsalternativ**. 
   
    ![Val för att öppna distributionsalternativ](./media/functions-continuous-deployment/setup-deployment.png)
 
1. På den **distributioner** bladet väljer **installationsprogrammet**.
 
    ![Bladet för distributioner](./media/functions-continuous-deployment/setup-deployment-1.png)
   
1. På den **distributionskälla** bladet väljer **Välj källa**. Fyll i informationen för din valda distributionskälla och välj sedan **OK**.
   
    ![Välja en distributionskälla](./media/functions-continuous-deployment/choose-deployment-source.png)

När du ställer in kontinuerlig distribution alla filändringar i din distributionskälla kopieras till funktionsappen och en fullständig implementering utlöses. Platsen är omdistribueras när filerna i källan uppdateras.

## <a name="deployment-scenarios"></a>Distributionsscenarier

Vanliga distributionsscenarier omfattar skapa en mellanlagrings-distribution och flytta befintliga funktioner för kontinuerlig distribution.

<a name="staging"></a>
### <a name="create-a-staging-deployment"></a>Skapa en mellanlagrings-distribution

Funktionsappar ännu inte har stöd distributionsplatser. Men du kan fortfarande hantera separata distributioner för mellanlagring och produktion med hjälp av kontinuerlig integrering.

Processen för att konfigurera och arbeta med en mellanlagrings distribution ser vanligtvis ut så här:

1. Skapa två funktionsappar i din prenumeration: en för produktionskod och en för mellanlagring. 

1. Skapa en distributionskälla om du inte redan har ett. Det här exemplet används [GitHub].

1. För funktionsappen produktion Slutför föregående stegen i [konfigurera kontinuerlig distribution](#set-up-continuous-deployment) och konfigurera distribution-gren till mastergrenen av GitHub-lagringsplats.
   
    ![Alternativ att välja en gren för distribution](./media/functions-continuous-deployment/choose-deployment-branch.png)

1. Upprepa steg 3 för mellanlagring funktionsappen, men välja grenen mellanlagring i stället i GitHub-databasen. Om din distributionskällan inte stöder branchning, kan du använda en annan mapp.
    
1. Gör uppdateringar i din kod i den tillfälliga gren eller en mapp och sedan kontrollera att den mellanlagrade distributionen återspeglar ändringarna.

1. När du testar, sammanfoga ändringarna från den tillfälliga grenen i huvudgrenen. Den här kopplingen utlöser distribution till produktion funktionsappen. Om din distributionskälla inte stöder grenar kan du skriva över filer i mappen produktion med filer från den tillfälliga mappen.

<a name="existing"></a>
### <a name="move-existing-functions-to-continuous-deployment"></a>Flytta befintliga funktioner för kontinuerlig distribution
När du har befintliga funktioner som du har skapat och underhålls i portalen, måste du hämta din funktion kodfiler med hjälp av FTP eller lokal Git-lagringsplats innan du kan ställa in kontinuerlig distribution enligt beskrivningen ovan. Du kan göra detta i Azure App Service-inställningarna för din funktionsapp. När du har hämtat filer kan du överföra dem till källan valda kontinuerlig distribution.

> [!NOTE]
> När du konfigurerar kontinuerlig integrering, kan du inte längre redigera källfilerna i Functions-portalen.

<a name="credentials"></a>
#### <a name="configure-deployment-credentials"></a>Konfigurera autentiseringsuppgifter för distribution
Innan du kan ladda ned filer från din funktionsapp med hjälp av FTP eller en lokal Git-lagringsplats, måste du konfigurera dina autentiseringsuppgifter för att komma åt webbplatsen. Autentiseringsuppgifterna är inställda på funktionen app-nivå. Använd följande steg för att ange autentiseringsuppgifter för distribution i Azure portal:

1. I funktionsappen i den [Azure-portalen](https://portal.azure.com)väljer **plattformsfunktioner** > **distributionsbehörigheterna**.
   
1. Ange ett användarnamn och lösenord och välj sedan **spara**. 

   ![Val för att ange autentiseringsuppgifter för lokal distribution](./media/functions-continuous-deployment/setup-deployment-credentials.png)

Du kan nu använda dessa autentiseringsuppgifter för åtkomst till din funktionsapp från FTP- eller inbyggd Git-lagringsplats.

<a name="downftp"></a>
#### <a name="download-files-by-using-ftp"></a>Ladda ned filer via FTP

1. I funktionsappen i den [Azure-portalen](https://portal.azure.com)väljer **plattformsfunktioner** > **egenskaper**. Kopiera värdena för **FTP/Distributionsanvändare**, **FTP-värdnamn**, och **FTPS-värdnamn**.  

   **FTP/Distributionsanvändare** måste anges som visas i portalen, inklusive appnamn, för att tillhandahålla tillräcklig kontext för FTP-servern.
   
   ![Val för att hämta distributionsinformation om din](./media/functions-continuous-deployment/get-deployment-credentials.png)

1. Använd anslutningsinformationen som du hämtade för att ansluta till din app och ladda ned källfilerna för dina funktioner från FTP-klienten.

<a name="downgit"></a>
#### <a name="download-files-by-using-a-local-git-repository"></a>Hämta filer med hjälp av en lokal Git-lagringsplats

1. I funktionsappen i den [Azure-portalen](https://portal.azure.com)väljer **plattformsfunktioner** > **distributionsalternativ**. 
   
    ![Val för att öppna distributionsalternativ](./media/functions-continuous-deployment/setup-deployment.png)
 
1. På den **distributioner** bladet väljer **installationsprogrammet**.
 
    ![Bladet för distributioner](./media/functions-continuous-deployment/setup-deployment-1.png)
   
1. På den **distributionskälla** bladet väljer **lokala Git-lagringsplats** > **OK**.

1. I **plattformsfunktioner**väljer **egenskaper** och anteckna värdet för Git-URL. 
   
    ![Val för att hämta Git-URL](./media/functions-continuous-deployment/get-local-git-deployment-url.png)

1. Klona databasen på den lokala datorn med hjälp av en Git-medvetna kommandotolk eller ditt favorit Git-verktyg. Kloningskommandot Git ser ut så här:
   
        git clone https://username@my-function-app.scm.azurewebsites.net:443/my-function-app.git

1. Hämta filer från din funktionsapp till klonen på din lokala dator, som i följande exempel:
   
        git pull origin master
   
    Om så krävs, ange din [konfigurerat autentiseringsuppgifter för distribution](#credentials).  

[GitHub]: https://github.com/

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Metodtips för Azure Functions](functions-best-practices.md)
