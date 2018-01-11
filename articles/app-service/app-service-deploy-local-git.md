---
title: "Lokal Git-distribution till Azure Apptjänst"
description: "Lär dig hur du aktiverar lokal Git-distribution till Azure App Service."
services: app-service
documentationcenter: 
author: dariagrigoriu
manager: erikre
editor: mollybos
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2016
ms.author: dariagrigoriu
ms.openlocfilehash: 19681564ef48649d4d44da1d0a2a65044f494ce4
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2018
---
# <a name="local-git-deployment-to-azure-app-service"></a>Lokal Git-distribution till Azure Apptjänst
Den här kursen visar hur du distribuerar appen till [Azure Web Apps](app-service-web-overview.md) från en Git-lagringsplats på den lokala datorn. Apptjänst har stöd för den här metoden med den **lokala Git** distributionsalternativ i den [Azure Portal].  
Många av Git-kommandon som beskrivs i den här artikeln ska utföras automatiskt när du skapar en Apptjänst-app med den [Azure-kommandoradsgränssnittet] enligt [här](app-service-web-get-started-dotnet.md).

## <a name="prerequisites"></a>Förutsättningar
För att slutföra den här kursen behöver du:

* Git. Du kan hämta binära installationen [här](http://www.git-scm.com/downloads).  
* Grundläggande kunskaper om Git.
* Ett Microsoft Azure-konto. Om du inte har ett konto kan du [registrera dig för en kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial) eller [aktivera Visual Studio-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details).

> [!NOTE]
> Om du vill komma igång med Azure App Service innan du registrerar dig för ett Azure-konto går du till [prova App Service](https://azure.microsoft.com/try/app-service/), där kan du direkt skapa en tillfällig startapp i App Service. Inga kreditkort krävs. Inga åtaganden.  
> 
> 

## <a name="Step1"></a>Steg 1: Skapa en lokal databas
Utför följande uppgifter för att skapa en ny Git-lagringsplats.

1. Starta ett kommandoradsverktyg, till exempel **GitBash** (Windows) eller **Bash** (Unix Shell). Du hittar kommandoradsverktyget via för OS X-datorer i **Terminal** program.
2. Gå till den katalog där innehållet för att distribuera skulle hittas.
3. Använd följande kommando för att initiera en ny Git-lagringsplats:

```bash  
git init
```

## <a name="Step2"></a>Steg 2: Bekräfta ditt innehåll
Apptjänst stöder program som skapats i en mängd olika programmeringsspråk. 

1. Om databasen innehåller redan innehåll hoppa över detta och flytta peka 2 nedan. Om databasen inte redan innehåller innehåll bara fylla i med en statisk HTML-fil på följande sätt: 
   
   * Med hjälp av en textredigerare, skapa en ny fil med namnet **index.html** i roten på Git-lagringsplats
   * Lägg till följande text som innehållet för index.html filen och spara den: *Hello Git!*
2. Kontrollera att du är under roten för Git-lagringsplats på kommandoraden. Sedan använder du följande kommando för att lägga till filer i databasen:

```bash  
git add -A
```
3. Spara ändringarna i databasen med hjälp av följande kommando:

```bash  
git commit -m "Hello Azure App Service"
```  

## <a name="Step3"></a>Steg 3: Aktivera databasen för Apptjänst-app
Utför följande steg om du vill aktivera en Git-lagringsplats för din Apptjänst-app.

1. Logga in på [Azure Portal].
2. I bladet för din Apptjänst-app, klickar du på **Inställningar > distributionskälla**. Klicka på **Välj källa**, klicka på **lokal Git-lagringsplats**, och klicka sedan på **OK**.  
   
    ![Lokal Git-lagringsplats](./media/app-service-deploy-local-git/local_git_selection.png)
3. Om det här är första gången du upp en databas i Azure måste du skapa autentiseringsuppgifter för inloggning för den. Du använder dem för att logga in på Azure-databasen och push ändringarna från din lokala Git-lagringsplats. Från din app-bladet, klickar du på **Deployment > autentiseringsuppgifter för distribution**, konfigurera distributionen användarnamn och lösenord. När du är klar klickar du på **spara**.
   
    ![](./media/app-service-deploy-local-git/deployment_credentials.png)

## <a name="Step4"></a>Steg 4: Distribuera ditt projekt
Använd följande steg för att publicera appen i App Service med lokal Git.

1. I bladet för din app i Azure-portalen klickar du på **Inställningar > Egenskaper** för den **URL för Git**.
   
    ![](./media/app-service-deploy-local-git/git_url.png)
   
    **URL för Git** är fjärransluten referens för distribution till från din lokala lagringsplats. I följande steg ska du använda denna URL.
2. Använda kommandoraden, kontrollera att du är i roten av en lokal Git-lagringsplats.
3. Använd `git remote` att lägga till den fjärranslutna referens som anges i **URL för Git** från steg 1. Kommandot ser ut ungefär så här:
   
        git remote add azure https://<username>@localgitdeployment.scm.azurewebsites.net:443/localgitdeployment.git         
   > [!NOTE]
   > Den **remote** kommando lägger till en namngiven referens till en fjärransluten lagringsplats. I det här exemplet skapas en referens med namnet ”azure” för ditt webbprogram lagringsplats.
   > 
   > 
4. Push-ditt innehåll till App Service med den nya **azure** remote du nyss skapade.

```bash  
git push azure master
```
    You will be prompted for the password you created earlier when you reset your deployment credentials in the Azure Portal. Enter the password (note that Gitbash does not echo asterisks to the console as you type your password). 
5. Gå tillbaka till din app i Azure Portal. En loggpost för din senaste push ska visas i den **distributioner** bladet. 
   
    ![](./media/app-service-deploy-local-git/deployment_history.png)
6. Klicka på den **Bläddra** längst upp i appens bladet för att kontrollera att innehållet har distribuerats. 

## <a name="Step5"></a>Felsökning
Följande är fel eller problem som uppstår ofta när Git för att publicera till en Apptjänst-app i Azure:

- - -
**Symtom**: Det gick inte att få åtkomst till [siteURL]: Det gick inte att ansluta till [scmAddress]

**Orsak**: det här felet kan inträffa om appen inte är igång.

**Lösning**: starta appen i Azure-portalen. Git-distribution fungerar inte om programmet körs. 

- - -
**Symtom**: Det gick inte att matcha host värdnamn

**Orsak**: det här felet kan inträffa om den adressinformation som anges när du skapar i azure remote var felaktig.

**Lösning**: Använd den `git remote -v` kommando för att lista alla fjärrkontroller tillsammans med den associerade URL: en. Kontrollera att URL: en för ”azure” remote är korrekt. Om det behövs, ta bort och återskapa det här remote med rätt URL.

- - -
**Symtom**: inga refs i gemensamma och inget har angetts; gör ingenting. Du måste kanske ange en gren till exempel 'master'.

**Orsak**: det här felet kan inträffa om du inte anger en gren när du utför en git push-åtgärd och har värdet inte push.default används av Git.

**Lösning**: utföra push igen, ange mastergrenen. Exempel:

```bash  
git push azure master
```
- - -
**Symtom**: src refspec [branchname] matchar inte något.

**Orsak**: det här felet kan inträffa om du försöker att skicka till en gren Master på azure remote.

**Lösning**: utföra push igen, ange mastergrenen. Exempel:

```bash  
git push azure master
```
- - -
**Symtom**: Det gick inte att RPC; leda = 22, HTTP-felkod = 502.

**Orsak**: det här felet kan inträffa om du försöker att skicka en stor git-lagringsplats via HTTPS.

**Lösning**: ändra git-konfigurationen på den lokala datorn för att göra postBuffer större

```bash  
git config --global http.postBuffer 524288000
```
- - -
**Symtom**: fel - ändringar som allokerats till fjärrdatabasen men ditt webbprogram som inte har uppdaterats.

**Orsak**: det här felet kan inträffa om du distribuerar ett Node.js-app med en package.json-fil som anger ytterligare moduler som krävs.

**Lösning**: ytterligare meddelanden som innehåller 'npm fel ”! bör vara inloggade före felet och kan ge ytterligare sammanhang om felet. Följande är kända orsaker till felet och den motsvarande 'npm fel ”! meddelande:

* **Felaktig package.json filen**: npm fel! Det gick inte att läsa beroenden.
* **Ursprunglig modul som inte har en binär distribution för Windows**:
  
  * npm fel! \`cmd ”/ c” ”nod gyp återskapa”\` misslyckades med 1
    
      ELLER
  * npm fel! [modulename@version] förinstallera: \`Se || gmake\`

## <a name="additional-resources"></a>Ytterligare resurser
* [Git-dokumentation](http://git-scm.com/documentation)
* [Projektet Kudu-dokumentation](https://github.com/projectkudu/kudu/wiki)
* [Kontinuerlig distribution till Azure App Service](app-service-continuous-deployment.md)
* [Använda PowerShell för Azure](/powershell/azure/overview)
* [Hur du använder Azure-kommandoradsgränssnittet](../cli-install-nodejs.md)

[Azure Developer Center]: http://www.windowsazure.com/en-us/develop/overview/
[Azure Portal]: https://portal.azure.com
[Git website]: http://git-scm.com
[Installing Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[Azure-kommandoradsgränssnittet]: https://azure.microsoft.com/en-us/documentation/articles/xplat-cli-azure-resource-manager/

[Using Git with CodePlex]: http://codeplex.codeplex.com/wikipage?title=Using%20Git%20with%20CodePlex&referringTitle=Source%20control%20clients&ProjectName=codeplex
[Quick Start - Mercurial]: http://mercurial.selenic.com/wiki/QuickStart
