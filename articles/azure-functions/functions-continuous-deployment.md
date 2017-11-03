---
title: "Kontinuerlig distribution för Azure Functions | Microsoft Docs"
description: "Använd kontinuerlig distribution av Azure App Service för att publicera dina Azure-funktioner."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/25/2016
ms.author: glenga
ms.openlocfilehash: 35a0b0faa61cf4b42ba1d8696c85f5724ff73f23
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="continuous-deployment-for-azure-functions"></a>Löpande distribution för Azure Functions
Azure Functions gör det enkelt att distribuera appen funktionen med hjälp av Apptjänst kontinuerlig integration. Functions kan integreras med BitBucket, Dropbox, GitHub eller Visual Studio Team Services VSTS (). Detta gör att ett arbetsflöde där Funktionskoden uppdateringar genom att använda en av dessa integrerade tjänster utlösaren distribution till Azure. Om du har använt Azure Functions, börja med [översikt över Azure Functions](functions-overview.md).

Kontinuerlig distribution är ett bra alternativ för projekt där flera och ofta återkommande bidrag integreras. Du kan även Underhåll källkontrollen på koden funktioner. Följande distribution källor stöds:

* [Bitbucket](https://bitbucket.org/)
* [Dropbox](https://www.dropbox.com/)
* Extern lagringsplats (Git eller ett)
* [Lokal Git-lagringsplats](../app-service/app-service-deploy-local-git.md)
* [GitHub](https://github.com)
* [OneDrive](https://onedrive.live.com/)
* [Visual Studio Team Services](https://www.visualstudio.com/team-services/)

Distributioner konfigureras på grundval av per funktion app. Efter kontinuerlig distribution har aktiverats, åtkomst till Funktionskoden i portalen anges till *skrivskyddad*.

## <a name="continuous-deployment-requirements"></a>Krav för kontinuerlig distribution

Du måste ha din distributionskälla konfigurerad och funktioner koden i distributionskälla innan du kan ställa in kontinuerlig distribution. I en viss funktion appdistribution bor varje funktion i en namngiven underkatalog där katalognamnet är namnet på funktionen.  

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

## <a name="set-up-continuous-deployment"></a>Konfigurera kontinuerlig distribution
Använd den här proceduren för att konfigurera kontinuerlig distribution för en befintlig funktionsapp. Dessa steg visar integrering med en GitHub-databas, men liknande steg gäller för Visual Studio Team Services eller andra deployment services.

1. I funktionen appen i den [Azure-portalen](https://portal.azure.com), klickar du på **plattformsfunktioner** och **distributionsalternativ**. 
   
    ![Konfigurera kontinuerlig distribution](./media/functions-continuous-deployment/setup-deployment.png)
 
2. I den **distributioner** bladet och klickar på **installationsprogrammet**.
 
    ![Konfigurera kontinuerlig distribution](./media/functions-continuous-deployment/setup-deployment-1.png)
   
2. I den **distributionskälla** bladet, klickar du på **Välj källa**, Fyll i informationen för din valda distributionskälla och klickar på **OK**.
   
    ![Välj distributionskälla](./media/functions-continuous-deployment/choose-deployment-source.png)

När kontinuerlig distribution har konfigurerats, alla ändringar i din distributionskälla kopieras till appen med funktionen och en fullständig webbplatsdistribution utlöses. Platsen är omdistribueras när filer i källinnehållet uppdateras.

## <a name="deployment-options"></a>Distributionsalternativ

Följande är några vanliga distributionsscenarier:

- [Skapa en fristående distribution](#staging)
- [Flytta befintliga funktioner för kontinuerlig distribution](#existing)

<a name="staging"></a>
### <a name="create-a-staging-deployment"></a>Skapa en fristående distribution

Funktionen appar stöder inte ännu distributionsplatser. Du kan dock fortfarande hantera separata mellanlagring och produktion distributioner med hjälp av kontinuerlig integrering.

Hur du konfigurerar och arbetar med en fristående distribution ser vanligtvis ut så här:

1. Skapa två funktionen appar i din prenumeration, en för produktion och en för Förproduktion. 

2. Skapa en distributionskälla om du inte redan har ett. Det här exemplet används [GitHub].

3. Om appen produktion funktionen Slutför de föregående stegen i **ställa in kontinuerlig distribution** och ange distribution gren till mastergrenen för GitHub-lagringsplats.
   
    ![Välj distributionen gren](./media/functions-continuous-deployment/choose-deployment-branch.png)

4. Upprepa det här steget för mellanlagring funktionsapp, men välja grenen mellanlagring i stället i GitHub-lagringsplatsen. Om din distribution-källan inte stöder förgrening, använder du en annan mapp.
    
5. Göra uppdateringar i din kod i fristående branch eller mapp och sedan kontrollera att ändringarna återspeglas i fristående distributionen.

6. När du testar, sammanfoga ändringarna från den fristående grenen i mastergrenen. Den här kopplingen utlöser distribution till funktionen produktionsprogrammet. Om din distribution-källan inte stöder filialer, kan du skriva över filer i mappen produktion med filer från den tillfälliga mappen.

<a name="existing"></a>
### <a name="move-existing-functions-to-continuous-deployment"></a>Flytta befintliga funktioner för kontinuerlig distribution
Om du har befintliga funktioner som du har skapat och underhålls i portalen, måste du hämta din befintliga funktionen kodfiler med FTP eller lokal Git-lagringsplats innan du kan ställa in kontinuerlig distribution som beskrivs ovan. Du kan göra detta i App Service-inställningar för din funktionsapp. När filerna har hämtats, du kan ladda upp dem till valda kontinuerlig distribution-källa.

> [!NOTE]
> När du konfigurerar kontinuerlig integration kan att du inte längre kunna redigera källfilerna i Functions-portalen.

- [Så här: Konfigurera autentiseringsuppgifter för distribution](#credentials)
- [Så här: hämta filer med FTP](#downftp)
- [Så här: hämta filer med hjälp av lokal Git-lagringsplats](#downgit)

<a name="credentials"></a>
#### <a name="how-to-configure-deployment-credentials"></a>Så här: Konfigurera autentiseringsuppgifter för distribution
Innan du kan hämta filer från din funktion med FTP- eller lokal Git-lagringsplats, måste du konfigurera dina autentiseringsuppgifter för att komma åt webbplatsen. Autentiseringsuppgifterna är inställda på funktionen app-nivå. Använd följande steg för att ange autentiseringsuppgifter för distribution i Azure-portalen:

1. I funktionen appen i den [Azure-portalen](https://portal.azure.com), klickar du på **plattformsfunktioner** och **distributionsbehörigheterna**.
   
    ![Ange autentiseringsuppgifter för lokal distribution](./media/functions-continuous-deployment/setup-deployment-credentials.png)

2. Ange ett användarnamn och lösenord och klicka sedan på **spara**. Du kan nu använda autentiseringsuppgifterna för åtkomst till appen funktionen från FTP- eller inbyggda Git-lagringsplatsen.

<a name="downftp"></a>
#### <a name="how-to-download-files-using-ftp"></a>Så här: hämta filer med FTP

1. I funktionen appen i den [Azure-portalen](https://portal.azure.com), klickar du på **plattformsfunktioner** och **egenskaper**, kopiera värdena för **FTP/distribution användaren**, **värdnamn för FTP-**, och **FTPS värdnamn**.  

    **FTP-/ Distributionsanvändare användaren** måste anges som visas i portalen, inklusive namnet på appen, för att ge rätt kontext för FTP-servern.
   
    ![Hämta information om distribution](./media/functions-continuous-deployment/get-deployment-credentials.png)

2. Använd informationen från FTP-klient som samlats in för att ansluta till din app och ladda ned källfilerna för dina funktioner.

<a name="downgit"></a>
#### <a name="how-to-download-files-using-a-local-git-repository"></a>Så här: hämta filer med hjälp av en lokal Git-lagringsplats

1. I funktionen appen i den [Azure-portalen](https://portal.azure.com), klickar du på **plattformsfunktioner** och **distributionsalternativ**. 
   
    ![Konfigurera kontinuerlig distribution](./media/functions-continuous-deployment/setup-deployment.png)
 
2. I den **distributioner** bladet och klickar på **installationsprogrammet**.
 
    ![Konfigurera kontinuerlig distribution](./media/functions-continuous-deployment/setup-deployment-1.png)
   
2. I den **distributionskälla** bladet, klickar du på **lokal Git-lagringsplats** och klicka sedan på **OK**.

3. I **plattformsfunktioner**, klickar du på **egenskaper** och anteckna värdet för URL för Git. 
   
    ![Konfigurera kontinuerlig distribution](./media/functions-continuous-deployment/get-local-git-deployment-url.png)

4. Klona databasen på den lokala datorn med hjälp av Kommandotolken Git-medveten eller ditt favoritprogram Git-verktyget. Kommandot Git-klon ser ut så här:
   
        git clone https://username@my-function-app.scm.azurewebsites.net:443/my-function-app.git

5. Hämta filer från appen funktionen klonen på den lokala datorn, som i följande exempel:
   
        git pull origin master
   
    Om det krävs ange din [konfigurerat autentiseringsuppgifter för distribution](#credentials).  

[GitHub]: https://github.com/

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Metodtips för Azure Functions](functions-best-practices.md)
