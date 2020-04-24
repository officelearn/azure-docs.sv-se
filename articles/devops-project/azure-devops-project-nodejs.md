---
title: Skapa en CI/CD-pipeline för en PWA med GatsbyJS och Azure DevOps Projects
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
#  <a name="quickstart-create-a-cicd-pipeline-in-azure-pipelines-for-nodejs-with-azure-devops-projects"></a>Snabb start: skapa en CI/CD-pipeline i Azure-pipelines för Node. js med Azure DevOps Projects
I den här snabb starten skapar du en NodeJS progressiv webbapp (PWA) med [GatsbyJS](https://www.gatsbyjs.org/) och den förenklade Azure DevOps-projektets skapande upplevelse. När du är färdig har du en pipeline för kontinuerlig integrering (CI) och kontinuerlig leverans (CD) för din PWA i Azure-pipelines. Azure DevOps Projects konfigurerar vad du behöver för att utveckla, distribuera och övervaka.

## <a name="prerequisites"></a>Krav

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- En [Azure DevOps](https://azure.microsoft.com/services/devops/) -organisation.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

DevOps Projects skapar en CI/CD-pipeline i Azure Pipelines. Du kan skapa en ny Azure DevOps-organisation eller använda en befintlig organisation. DevOps Projects skapar även Azure-resurser i den Azure-prenumeration som du väljer.

1. Logga in på [Azure Portal](https://portal.azure.com)och välj **skapa en resurs**i den vänstra rutan. 

   ![Skapa en Azure-resurs i Azure Portal](_img/azure-devops-project-nodejs/create-azure-resource.png)

2. Sök efter och välj **DevOps Projects**och välj sedan **skapa**.

 ![Skapa ett DevOps-projekt](_img/azure-devops-project-nodejs/create-devops-project.png) 

## <a name="select-a-sample-application-and-azure-service"></a>Välj ett exempelprogram och en Azure-tjänst

1. Välj Node.js-exempelprogrammet.   

 ![Välj Node. js-exemplet](_img/azure-devops-project-nodejs/select-nodejs-devops-project.png) 

2. Exempelramverket som är standard är **Express.js**. Ändra valet till **enkel Node. js-app** och välj sedan **Nästa**. 

 ![Välj den enkla Node. js-appen](_img/azure-devops-project-nodejs/simple-nodejs-project.png) 

3. De distributions mål som är tillgängliga i det här steget styrs av program ramverket som valts i steg 2.  I det här exemplet är **Windows Web Apps** standard distributions målet.  Lämna **Web App for containers** ange och välj **Nästa**.

 ![Välj distributions mål](_img/azure-devops-project-nodejs/select-web-server.png) 

## <a name="configure-a-project-name-and-an-azure-subscription"></a>Konfigurera ett projekt namn och en Azure-prenumeration

1. I det sista steget i arbets flödet för skapande av DevOps-projekt tilldelar du ett projekt namn, väljer en Azure-prenumeration och väljer **klar**.  

 ![Tilldela ett projekt namn och välj en prenumeration](_img/azure-devops-project-nodejs/assign-project-name.png) 

2. En sammanfattnings sida visas när projektet har skapats och programmet distribueras till Azure. Efter en kort period skapas ett projekt i din [Azure DevOps-organisation](https://dev.azure.com/) som innehåller en git-lagrings platsen, ett kanban-kort, en distributions pipeline, test planer och de artefakter som krävs av din app.  

## <a name="managing-your-project"></a>Hantera ditt projekt

1. Navigera till **alla resurser** och hitta ditt DevOps-projekt. Välj ditt **DevOps-projekt**.

![Azure DevOps-instrumentpanel i resurs lista](_img/azure-devops-project-nodejs/azure-devops-project-in-resource-list.png)

2. Du dirigeras till en instrument panel som ger insyn i projektets start sida, kod lagrings plats, CI/CD-pipeline och en länk till appen som körs. Välj **Start sidan för projektet** om du vill visa ditt program i **Azure DevOps** och i en annan webbläsare väljer du **programmets slut punkt** för att Visa Live Sample-appen.  Vi ändrar det här exemplet senare för att använda GatsbyJS-genererade PWA.

![Azure DevOps-instrumentpanel](_img/azure-devops-project-nodejs/devops-projects-dashboard.png) 

3. Från ditt Azure DevOps-projekt kan du bjuda in team medlemmar att samar beta och upprätta en Kanban-tavla för att börja spåra ditt arbete.  Mer information finns [här](https://docs.microsoft.com/azure/devops/user-guide/what-is-azure-devops?view=azure-devops).

![Översikt över Azure DevOps](_img/azure-devops-project-nodejs/azure-devops-overview.png)

## <a name="clone-the-repo-and-install-your-gatsby-pwa"></a>Klona lagrings platsen och installera din Gatsby-PWA

DevOps Projects skapar en git-lagringsplats i Azure databaser eller GitHub. I det här exemplet har du skapat en Azure-lagrings platsen.  Nästa steg är att klona lagrings platsen och göra ändringar.

1. Välj **databaser** från ditt **DevOps-projekt** och klicka sedan på **klona**.  Det finns olika mekanismer för att klona git-lagrings platsen till Skriv bordet.  Välj den som passar din utvecklings upplevelse.  

![Klona lagringsplatsen](_img/azure-devops-project-nodejs/clone-the-repo.png)

2. När lagrings platsen har klonat till Skriv bordet gör du några ändringar i Start mal len. Börja med att installera GatsbyJS CLI från terminalen.
```powershell
npm install -g gatsby
```

3. Från terminalen navigerar du till roten för din lagrings platsen. Den bör innehålla tre mappar som ser ut så här:
```powershell
Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-----        2/23/2020  10:42 PM                Application
d-----        2/23/2020   3:05 PM                ArmTemplates
d-----        2/23/2020   3:05 PM                Tests
```

4. Vi vill inte att alla filer ska finnas i programmappen eftersom vi kommer att ersätta den med en Gatsby-StartStart. Kör följande kommandon, i ordning, för att trimma det nedåt.
```powershell
cp .\Application\Dockerfile .
rmdir Application
```

5. Använd Gatsby CLI för att generera en PWA-exempel. Kör `gatsby new` från terminalen för att starta PWA-guiden och `gatsby-starter-blog` Välj för din start-mall. Det bör likna det här exemplet:
```powershell
c:\myproject> gatsby new
√ What is your project called? ... my-gatsby-project
? What starter would you like to use? » - Use arrow-keys. Return to submit.
    gatsby-starter-default
    gatsby-starter-hello-world
>   gatsby-starter-blog
    (Use a different starter)
```

6. Nu har du en mapp med `my-gatsby-project`namnet. Byt namn på `Application` den till och `Dockerfile` kopiera den till den.
```powershell
mv my-gatsby-project Application
mv Dockerfile Application
```

7. Öppna Dockerfile i din favorit redigerare och ändra den första raden från `FROM node:8` till. `FROM node:12` Den här ändringen säkerställer att din behållare använder Node. js version 12. x i stället för version 8. x. GatsbyJS kräver fler moderna versioner av Node. js.

8. Öppna sedan Package. JSON-filen i programmappen och redigera [fältet skript](https://docs.npmjs.com/files/package.json#scripts) för att se till att utvecklings-och produktions servrarna lyssnar på alla tillgängliga nätverks gränssnitt (till exempel 0.0.0.0) och port 80. Utan de här inställningarna kan behållar App Service inte dirigera trafik till din Node. js-app som körs i din behållare. `scripts` Fältet bör se ut ungefär så här. Mer specifikt vill du ändra `develop`-, `serve`-och `start` -målen från standardvärdena.
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

## <a name="edit-your-cicd-pipelines"></a>Redigera dina CI/CD-pipeliner

1. Innan du genomför koden i föregående avsnitt gör du några ändringar i pipelinen för build och release. Redigera din "Build pipeline" och uppdatera noden så att Node. js version 12. x används. Ange fältet **aktivitets version** till 1. x och **versions** fältet till 12. x.
![Uppdatera Node. js till 12. x](_img/azure-devops-project-nodejs/build-pipeline-update-node.png)

2. I den här snabb starten skapar vi inte enhets test och vi inaktiverar de här stegen i vår versions pipeline. När du skriver tester kan du återaktivera de här stegen. Högerklicka för att välja de aktiviteter som är märkta med **Installera test beroenden** och **Kör enhets test** och inaktivera dem.

![Inaktivera Bygg test](_img/azure-devops-project-nodejs/disable-build-unittests.png)

3. Redigera din versions pipeline.
![Redigera pipelinen för version](_img/azure-devops-project-nodejs/edit-release-pipeline.png)

4. Som med build-pipeline ändrar du nodens aktivitet så att 12. x används och inaktiverar de två test uppgifterna. Din utgåva bör likna den här skärm bilden.

![Pipeline för slutförd version](_img/azure-devops-project-nodejs/release-pipeline-complete.png)

1. På vänster sida i webbläsaren går du till filen **views/index.pug**.

1. Välj **Redigera** och gör en ändring i h2-rubriken.  
    Ange till exempel **Kom igång direkt med Azure DevOps Projects** eller gör några andra ändringar.

1. Välj **Incheckning** och spara sedan ändringarna.

1. I webbläsaren går du till DevOps Projects-instrumentpanelen.   
Du bör nu se att en version håller på att skapas. De ändringar du gjort skapas automatiskt och distribueras via en CI/CD-pipeline.

## <a name="commit-your-changes-and-examine-the-azure-cicd-pipeline"></a>Genomför ändringarna och granska Azure CI/CD-pipeline

I de föregående två stegen lade du till en Gatsby-genererad PWA till git-lagrings platsen och redigerade pipelines för att skapa och distribuera koden. Vi kan genomföra koden och se hur den fortskrider genom att bygga och släppa pipelinen.

1. Från roten av ditt projekts git-lagrings platsen i en Terminal kör du följande kommandon för att skicka koden till ditt Azure DevOps-projekt:
```powershell
git add .
git commit -m "My first Gatsby PWA"
git push
```

2. En version startas så snart som `git push` slutförts. Du kan följa förloppet från **Azure DevOps-instrumentpanelen**.

![Azure DevOps-instrumentpanel i resurs lista](_img/azure-devops-project-nodejs/azure-devops-project-in-resource-list.png)

3. Efter några minuter bör dina pipeliner för build och release avslutas och din PWA bör distribueras till en behållare. Klicka på länken för **program slut punkten** från instrument panelen ovan så visas ett Gatsby start projekt för Bloggar.



## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort Azure App Service och andra relaterade resurser som du har skapat när du inte behöver resurserna längre. Använd funktionen **Ta bort** på DevOps Projects-instrumentpanelen.


## <a name="next-steps"></a>Nästa steg

När du konfigurerar din CI/CD-process skapas automatiskt build-och release-pipeliner. Du kan ändra de här bygg-och release-pipelinen för att uppfylla ditt teams behov. Mer information om CI/CD-pipelinen finns i:

> [!div class="nextstepaction"]
> [Anpassa CD-process](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

