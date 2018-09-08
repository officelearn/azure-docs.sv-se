---
title: Kontinuerlig distribution för Azure Functions | Microsoft Docs
description: Använda kontinuerlig distribution funktioner i Azure App Service för att publicera dina Azure-funktioner.
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
ms.openlocfilehash: 4561b343fa15346388572a70616840be0dd06679
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44095495"
---
# <a name="continuous-deployment-for-azure-functions"></a>Löpande distribution för Azure Functions
Azure Functions gör det enkelt att distribuera funktionsappen med kontinuerlig integrering för App Service. Functions kan integreras med BitBucket, Dropbox, GitHub och Visual Studio Team Services (VSTS). På så sätt kan ett arbetsflöde där Funktionskoden uppdaterar genom att använda en av dessa integrerade tjänster utlösaren distribution till Azure. Om du är nybörjare på Azure Functions, börja med [översikt över Azure Functions](functions-overview.md).

Kontinuerlig distribution är ett bra alternativ för projekt där flera och ofta återkommande bidrag integreras. Du kan också behålla källkontroll på din functions-kod. Följande distributionskällor stöds för närvarande:

* [Bitbucket](https://bitbucket.org/)
* [Dropbox](https://www.dropbox.com/)
* Extern lagringsplats (Git- eller Mercurial)
* [Lokal Git-lagringsplats](../app-service/app-service-deploy-local-git.md)
* [GitHub](https://github.com)
* [OneDrive](https://onedrive.live.com/)
* [Visual Studio Team Services](https://www.visualstudio.com/team-services/)

Distributioner är konfigurerade på basis av per funktion app. När kontinuerlig distribution har aktiverats, att Funktionskoden i portalen har angetts till *skrivskyddad*.

## <a name="continuous-deployment-requirements"></a>Krav för kontinuerlig distribution

Du måste ha din distributionskälla som konfigurerats och din functions-kod i distributionskälla innan du kan ställa in kontinuerlig distribution. Varje funktion finns i en namngiven underkatalog, där katalognamnet är namnet på funktionen i en angiven funktion app-distribution.  

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

För att kunna distribuera från VSTS, måste du först länka ditt VSTS-konto med Azure-prenumerationen. Mer information finns i [ställer in faktureringen för ditt VSTS-konto](https://docs.microsoft.com/vsts/billing/set-up-billing-for-your-account-vs?view=vsts#set-up-billing-via-the-azure-portal).

## <a name="set-up-continuous-deployment"></a>Konfigurera kontinuerlig distribution
Använd den här proceduren för att konfigurera kontinuerlig distribution för en befintlig funktionsapp. De här stegen visar integrering med en GitHub-lagringsplats, men liknande steg som gäller för Visual Studio Team Services eller andra distributionstjänster.

1. I funktionsappen i den [Azure-portalen](https://portal.azure.com), klickar du på **plattformsfunktioner** och **distributionsalternativ**. 
   
    ![Konfigurera kontinuerlig distribution](./media/functions-continuous-deployment/setup-deployment.png)
 
2. I den **distributioner** bladet klickar du på **installationsprogrammet**.
 
    ![Konfigurera kontinuerlig distribution](./media/functions-continuous-deployment/setup-deployment-1.png)
   
2. I den **distributionskälla** bladet klickar du på **Välj källa**och fyller sedan i informationen för din valda distributionskälla Klicka **OK**.
   
    ![Välj distributionskälla](./media/functions-continuous-deployment/choose-deployment-source.png)

När kontinuerlig distribution har konfigurerats kan alla filändringar i din distributionskälla kopieras till funktionsappen och en fullständig implementering utlöses. Platsen är omdistribueras när filerna i källan uppdateras.

## <a name="deployment-options"></a>Distributionsalternativ

Här följer några vanliga scenarier:

- [Skapa en mellanlagrings-distribution](#staging)
- [Flytta befintliga funktioner för kontinuerlig distribution](#existing)

<a name="staging"></a>
### <a name="create-a-staging-deployment"></a>Skapa en mellanlagrings-distribution

Funktionen appar stöder inte ännu distributionsplatser. Du kan dock fortfarande hantera separata distributioner för mellanlagring och produktion med hjälp av kontinuerlig integrering.

Processen för att konfigurera och arbeta med en mellanlagrings distribution ser vanligtvis ut så här:

1. Skapa två funktionsappar i din prenumeration, en för produktionskod och en för mellanlagring. 

2. Skapa en distributionskälla om du inte redan har ett. Det här exemplet används [GitHub].

3. För funktionsappen produktion Slutför föregående stegen i **konfigurera kontinuerlig distribution** och konfigurera distribution-gren till mastergrenen av GitHub-lagringsplats.
   
    ![Välj gren för distribution](./media/functions-continuous-deployment/choose-deployment-branch.png)

4. Upprepa det här steget för mellanlagring funktionsappen, men i stället välja grenen mellanlagring i GitHub-databasen. Om din distributionskällan inte stöder branchning, kan du använda en annan mapp.
    
5. Gör uppdateringar i din kod i den tillfälliga gren eller mappen, och sedan kontrollera att dessa ändringar återspeglas i mellanlagrings-distributionen.

6. När du testar, sammanfoga ändringarna från den tillfälliga grenen i huvudgrenen. Den här kopplingen utlöser distribution till produktion funktionsappen. Om din distributionskälla inte stöder grenar kan du skriva över filer i mappen produktion med filer från den tillfälliga mappen.

<a name="existing"></a>
### <a name="move-existing-functions-to-continuous-deployment"></a>Flytta befintliga funktioner för kontinuerlig distribution
När du har befintliga funktioner som du har skapat och underhålls i portalen, måste du hämta din befintliga funktionen kodfiler med FTP eller lokal Git-lagringsplats innan du kan ställa in kontinuerlig distribution enligt beskrivningen ovan. Du kan göra detta i App Service-inställningarna för din funktionsapp. När filerna har hämtats, kan du överföra dem till valda kontinuerlig distributionskällan.

> [!NOTE]
> När du konfigurerar kontinuerlig integrering, kommer du inte längre att kunna redigera källfilerna i Functions-portalen.

- [Så här: Konfigurera autentiseringsuppgifter för distribution](#credentials)
- [Så här: hämta filer med FTP](#downftp)
- [Så här: ladda ned filer med hjälp av den lokala Git-lagringsplatsen](#downgit)

<a name="credentials"></a>
#### <a name="how-to-configure-deployment-credentials"></a>Så här: Konfigurera autentiseringsuppgifter för distribution
Innan du kan ladda ned filer från din funktionsapp med FTP eller lokal Git-lagringsplats, måste du konfigurera dina autentiseringsuppgifter för att komma åt webbplatsen. Autentiseringsuppgifterna är inställda på funktionen app-nivå. Använd följande steg för att ange autentiseringsuppgifter för distribution i Azure portal:

1. I funktionsappen i den [Azure-portalen](https://portal.azure.com), klickar du på **plattformsfunktioner** och **distributionsbehörigheterna**.
   
    ![Ange autentiseringsuppgifter för lokal distribution](./media/functions-continuous-deployment/setup-deployment-credentials.png)

2. Ange ett användarnamn och lösenord och klicka sedan på **spara**. Du kan nu använda dessa autentiseringsuppgifter för åtkomst till din funktionsapp från FTP- eller inbyggd Git-lagringsplats.

<a name="downftp"></a>
#### <a name="how-to-download-files-using-ftp"></a>Så här: hämta filer med FTP

1. I funktionsappen i den [Azure-portalen](https://portal.azure.com), klickar du på **plattformsfunktioner** och **egenskaper**, kopiera värdena för **FTP/Distributionsanvändare**, **FTP-värdnamn**, och **FTPS-värdnamn**.  

    **FTP/Distributionsanvändare** måste anges som visas i portalen, inklusive appnamn, för att tillhandahålla tillräcklig kontext för FTP-servern.
   
    ![Hämta distributionsinformation om din](./media/functions-continuous-deployment/get-deployment-credentials.png)

2. Från FTP-klienten använder du informationen du samlade in för att ansluta till din app och ladda ned källfilerna för dina funktioner.

<a name="downgit"></a>
#### <a name="how-to-download-files-using-a-local-git-repository"></a>Så här: ladda ned filer med hjälp av en lokal Git-lagringsplats

1. I funktionsappen i den [Azure-portalen](https://portal.azure.com), klickar du på **plattformsfunktioner** och **distributionsalternativ**. 
   
    ![Konfigurera kontinuerlig distribution](./media/functions-continuous-deployment/setup-deployment.png)
 
2. I den **distributioner** bladet klickar du på **installationsprogrammet**.
 
    ![Konfigurera kontinuerlig distribution](./media/functions-continuous-deployment/setup-deployment-1.png)
   
2. I den **distributionskälla** bladet klickar du på **lokala Git-lagringsplats** och klicka sedan på **OK**.

3. I **plattformsfunktioner**, klickar du på **egenskaper** och anteckna värdet för Git-URL. 
   
    ![Konfigurera kontinuerlig distribution](./media/functions-continuous-deployment/get-local-git-deployment-url.png)

4. Klona databasen på den lokala datorn med en Git-medvetna kommandotolk eller din favorit Git-verktyg. Kloningskommandot Git ser ut så här:
   
        git clone https://username@my-function-app.scm.azurewebsites.net:443/my-function-app.git

5. Hämta filer från din funktionsapp till klonen på din lokala dator, som i följande exempel:
   
        git pull origin master
   
    Om så krävs, ange din [konfigurerat autentiseringsuppgifter för distribution](#credentials).  

[GitHub]: https://github.com/

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Metodtips för Azure Functions](functions-best-practices.md)
