---
title: Skapa en CI/CD-pipeline för en PWA med GatsbyJS och Azure DevOps-projekt
description: DevOps Projects gör det enkelt att komma igång med Azure. Det hjälper dig att starta en app på en Azure-tjänst med några enkla få steg.
ms.prod: devops
ms.technology: devops-cicd
services: vsts
documentationcenter: vs-devops-build
author: arob98
manager: angrobe
editor: ''
ms.assetid: ''
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 02/24/2020
ms.author: angrobe
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: 508a61d6bbb00692855e09601aed67ab3be9cc8d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "78209075"
---
#  <a name="quickstart-create-a-cicd-pipeline-in-azure-pipelines-for-nodejs-with-azure-devops-projects"></a>Snabbstart: Skapa en CI/CD-pipeline i Azure Pipelines for Node.js med Azure DevOps-projekt
I den här snabbstarten skapar du en NodeJS progressiv webbapp (PWA) med [GatsbyJS](https://www.gatsbyjs.org/) och den förenklade Azure DevOps-projektskapande upplevelsen. När du är klar har du en kontinuerlig integrationspipel (CI) och cd-pipeline (continuous delivery) för din PWA i Azure Pipelines. Azure DevOps-projekt ställer in vad du behöver för att utveckla, distribuera och övervaka.

## <a name="prerequisites"></a>Krav

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto gratis](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- En [Azure DevOps-organisation.](https://azure.microsoft.com/services/devops/)

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

DevOps Projects skapar en CI/CD-pipeline i Azure Pipelines. Du kan skapa en ny Azure DevOps-organisation eller använda en befintlig organisation. DevOps Projects skapar även Azure-resurser i den Azure-prenumeration som du väljer.

1. Logga in på [Azure-portalen](https://portal.azure.com)och välj **Skapa en resurs**i den vänstra rutan . 

   ![Skapa en Azure-resurs i Azure-portalen](_img/azure-devops-project-nodejs/create-azure-resource.png)

2. Sök efter och välj **DevOps-projekt**och välj sedan **Skapa**.

 ![Skapa ett DevOps-projekt](_img/azure-devops-project-nodejs/create-devops-project.png) 

## <a name="select-a-sample-application-and-azure-service"></a>Välj ett exempelprogram och en Azure-tjänst

1. Välj Node.js-exempelprogrammet.   

 ![Välj exemplet Node.js](_img/azure-devops-project-nodejs/select-nodejs-devops-project.png) 

2. Exempelramverket som är standard är **Express.js**. Ändra markeringen till **Appen Enkel nod.js** och välj sedan **Nästa**. 

 ![Välj appen Enkel nod.js](_img/azure-devops-project-nodejs/simple-nodejs-project.png) 

3. De distributionsmål som är tillgängliga i det här steget styrs av det programramverk som valts i steg 2.  I det här exemplet är **Windows Web App** standarddistributionsmålet.  Lämna **webbapp för behållare** inställda och välj **Nästa**.

 ![Välj distributionsmål](_img/azure-devops-project-nodejs/select-web-server.png) 

## <a name="configure-a-project-name-and-an-azure-subscription"></a>Konfigurera ett projektnamn och en Azure-prenumeration

1. I det sista steget i arbetsflödet för att skapa DevOps-projekt tilldelar du ett projektnamn, väljer en Azure-prenumeration och väljer **Klar**.  

 ![Tilldela ett projektnamn och välj en prenumeration](_img/azure-devops-project-nodejs/assign-project-name.png) 

2. En sammanfattningssida visas medan projektet skapas och ditt program distribueras till Azure. Efter en kort period skapas ett projekt i din [Azure DevOps-organisation](https://dev.azure.com/) som innehåller en git-repo, en Kanban-anslagstavla, en distributionspipeline, testplaner och de artefakter som krävs av din app.  

## <a name="managing-your-project"></a>Hantera ditt projekt

1. Navigera till **alla resurser** och hitta ditt DevOps-projekt. Välj **ditt DevOps-projekt**.

![Instrumentpanelen för Azure DevOps i resurslistan](_img/azure-devops-project-nodejs/azure-devops-project-in-resource-list.png)

2. Du dirigeras till en instrumentpanel som ger insyn i projektets startsida, koddatabas, CI/CD-pipeline och en länk till din app som körs. Välj **startsidan** för Project om du vill visa ditt program i Azure DevOps och välj **programslutpunkten** på en annan webbläsarflik för att visa appen live.Select the Project Homepage to view your application in **Azure DevOps** and, in another browser tab, select the Application Endpoint to view the live sample app.  Vi ändrar det här exemplet senare för att använda GatsbyJS genererade PWA.

![Instrumentpanelen för Azure DevOps](_img/azure-devops-project-nodejs/devops-projects-dashboard.png) 

3. Från ditt Azure DevOps-projekt kan du bjuda in gruppmedlemmar att samarbeta och upprätta en Kanban-anslagstavla för att börja spåra ditt arbete.  Mer information finns [här](https://docs.microsoft.com/azure/devops/user-guide/what-is-azure-devops?view=azure-devops).

![Översikt över Azure DevOps](_img/azure-devops-project-nodejs/azure-devops-overview.png)

## <a name="clone-the-repo-and-install-your-gatsby-pwa"></a>Klona repo och installera din Gatsby PWA

DevOps-projekt skapar en git-databas i Azure Repos eller GitHub. Det här exemplet har skapat en Azure Repo.  Nästa steg är att klona reporäntan och göra ändringar.

1. Välj **Repos** från **DevOps-projektet** och klicka sedan på **Klona**.  Det finns olika mekanismer för att klona git repo till skrivbordet.  Välj den som passar din utvecklingsupplevelse.  

![Klona lagringsplatsen](_img/azure-devops-project-nodejs/clone-the-repo.png)

2. När repoen har klonats till skrivbordet gör du några ändringar i startmallen. Börja med att installera GatsbyJS CLI från din terminal.
```powershell
npm install -g gatsby
```

3. Från terminalen, navigera till roten av din repo. Den bör innehålla tre mappar som ser ut så här:
```powershell
Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-----        2/23/2020  10:42 PM                Application
d-----        2/23/2020   3:05 PM                ArmTemplates
d-----        2/23/2020   3:05 PM                Tests
```

4. Vi vill inte ha alla filer i mappen Ansökan eftersom vi kommer att ersätta den med en Gatsby starter. Kör följande kommandon, i följd, för att trimma ned det.
```powershell
cp .\Application\Dockerfile .
rmdir Application
```

5. Använd Gatsby CLI för att generera ett prov PWA. Kör `gatsby new` från terminalen för att påbörja `gatsby-starter-blog` PWA-guiden och välj för startmallen. Det bör likna detta prov:
```powershell
c:\myproject> gatsby new
√ What is your project called? ... my-gatsby-project
? What starter would you like to use? » - Use arrow-keys. Return to submit.
    gatsby-starter-default
    gatsby-starter-hello-world
>   gatsby-starter-blog
    (Use a different starter)
```

6. Du har nu `my-gatsby-project`en mapp med namnet . Byt namn `Application` till `Dockerfile` och kopiera till den.
```powershell
mv my-gatsby-project Application
mv Dockerfile Application
```

7. Öppna Dockerfile i din favoritredigerare `FROM node:8` och `FROM node:12`ändra den första raden från till . Den här ändringen säkerställer att behållaren använder Node.js version 12.x i stället för version 8.x. GatsbyJS kräver modernare versioner av Node.js.

8. Öppna sedan filen package.json i mappen Program och redigera [skriptfältet](https://docs.npmjs.com/files/package.json#scripts) för att säkerställa att utvecklings- och produktionsservrar lyssnar på alla tillgängliga nätverksgränssnitt (till exempel 0.0.0.0) och port 80. Utan dessa inställningar kan behållarapptjänsten inte dirigera trafik till nod.js-appen som körs i behållaren. Fältet `scripts` ska likna vad som finns nedan. Du vill ändra `develop`, `serve`och `start` mål från standardvärdena.
```json
  "scripts": {
    "build": "gatsby build",
    "develop": "gatsby develop  -H 0.0.0.0 -p 80",
    "format": "prettier --write \"**/*.{js,jsx,json,md}\"",
    "start": "npm run serve",
    "serve": "npm run build && gatsby serve -H 0.0.0.0 -p 80",
    "clean": "gatsby clean",
    "test": "echo \"Write tests! -> https://gatsby.dev/unit-testing\" && exit 1"
  }
```

## <a name="edit-your-cicd-pipelines"></a>Redigera CI/CD-pipelines

1. Innan du genomför koden i föregående avsnitt gör du några ändringar i dina bygg- och utgivningspipelor. Redigera din "Bygg pipeline" och uppdatera noduppgiften för att använda Nod.js version 12.x. Ange fältet **Aktivitetsversion** till 1.x och fältet **Version** till 12.x.
![Uppdatera nod.js till 12.x](_img/azure-devops-project-nodejs/build-pipeline-update-node.png)

2. I den här snabbstarten skapar vi inte enhetstester och vi inaktiverar dessa steg i vår byggpipeline. När du skriver tester kan du återaktivera dessa steg. Högerklicka för att välja de aktiviteter som heter **Installera testberoenden** och **Kör enhetstester** och inaktivera dem.

![Inaktivera byggtester](_img/azure-devops-project-nodejs/disable-build-unittests.png)

3. Redigera din versionspipeline.
![Redigera utgivningspipelinen](_img/azure-devops-project-nodejs/edit-release-pipeline.png)

4. Precis som med byggpipelinen ändrar du nodaktiviteten så att den använder 12.x och inaktiverar de två testaktiviteterna. Din release bör likna den här skärmdumpen.

![Pipeline för slutförd utgivning](_img/azure-devops-project-nodejs/release-pipeline-complete.png)

1. På vänster sida i webbläsaren går du till filen **views/index.pug**.

1. Välj **Redigera** och gör en ändring i h2-rubriken.  
    Ange till exempel **Kom igång direkt med Azure DevOps-projekt** eller gör någon annan ändring.

1. Välj **Incheckning** och spara sedan ändringarna.

1. I webbläsaren går du till DevOps Projects-instrumentpanelen.   
Du bör nu se att en version håller på att skapas. De ändringar du har gjort skapas och distribueras automatiskt via en CI/CD-pipeline.

## <a name="commit-your-changes-and-examine-the-azure-cicd-pipeline"></a>Genomför dina ändringar och undersök Azure CI/CD-pipelinen

I de två föregående stegen har du lagt till en Gatsby genererad PWA i din git repo och redigerade dina pipelines för att bygga och distribuera koden. Vi kan begå koden och se den gå vidare genom bygg- och utgivningspipelinen.

1. Från roten till projektets git-repo i en terminal kör du följande kommandon för att skicka koden till ditt Azure DevOps-projekt:
```powershell
git add .
git commit -m "My first Gatsby PWA"
git push
```

2. En version startas `git push` så snart den är klar. Du kan följa förloppet från **Azure DevOps Dashboard**.

![Instrumentpanelen för Azure DevOps i resurslistan](_img/azure-devops-project-nodejs/azure-devops-project-in-resource-list.png)

3. Efter några minuter bör dina bygg- och frigöringspipelines avslutas och din PWA ska distribueras till en behållare. Klicka på **länken Programslutpunkt** från instrumentpanelen ovan och du bör se ett Gatsby startprojekt för bloggar.



## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort Azure App Service och andra relaterade resurser som du skapade när du inte behöver resurserna längre. Använd funktionen **Ta bort** på DevOps Projects-instrumentpanelen.


## <a name="next-steps"></a>Nästa steg

När du konfigurerar CI/CD-processen skapas och frigörs pipelines automatiskt. Du kan ändra dessa bygg- och utgivningspipelor för att uppfylla behoven hos ditt team. Mer information om CI/CD-pipelinen finns i:

> [!div class="nextstepaction"]
> [Anpassa CD-process](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

