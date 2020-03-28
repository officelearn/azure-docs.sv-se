---
title: Självstudiekurs - CI/CD till Virtuella Azure-datorer med Azure Pipelines
description: I den här självstudien får du lära dig hur du konfigurerar kontinuerlig integrering (CI) och kontinuerlig distribution (CD) av en Node.js-app till Virtuella Azure-datorer med YAML-baserad Azure-pipeline.
author: ushan
tags: azure-devops-pipelines
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: azure-pipelines
ms.workload: infrastructure
ms.date: 1/3/2020
ms.author: ushan
ms.custom: devops
ms.openlocfilehash: bb7c773d02c5da5c115af79cd9e90c78e71eb6bf
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "76988336"
---
# <a name="tutorial-deploy-your-app-to-linux-virtual-machines-in-azure-using-azure-devops-services-and-azure-pipelines"></a>Självstudiekurs: Distribuera din app till virtuella Linux-datorer i Azure med Azure DevOps Services och Azure Pipelines

Kontinuerlig integrering (CI) och kontinuerlig distribution (CD) utgör en pipeline genom vilken du kan skapa, släppa och distribuera din kod efter varje kodkonset. Det här dokumentet innehåller de steg som är associerade med att konfigurera en CI/CD-pipeline för att utföra distributioner med flera datorer med Azure Pipelines.

Azure Pipelines tillhandahåller en komplett, komplett uppsättning CI/CD-automatiseringsverktyg för distributioner till virtuella datorer, både på prem eller i alla moln.

I den här självstudien ställer du in en YAML-baserad CI/CD-pipeline för att distribuera din app till en Azure [Pipelines-miljö](https://docs.microsoft.com/azure/devops/pipelines/process/environments?view=azure-devops) med Virtuella Linux-datorer som resurser, som var och en fungerar som webbservrar för att köra appen.

Lär dig att:

> [!div class="checklist"]
> * Skaffa en exempelapp.
> * Skapa en YAML-baserad Azure Pipelines CI-pipeline för att skapa exempelappen.
> * Skapa en Azure Pipelines-miljö för virtuella Azure-datorer
> * Skapa en Azure Pipelines CD-pipeline.
> * Kör manuella och CI-utlösta distributioner.

## <a name="before-you-begin"></a>Innan du börjar

* Logga in på din Azure DevOps Services-organisation (**https://dev.azure.com/**). 
  Du kan få en [kostnadsfri Azure DevOps Services-organisation](https://go.microsoft.com/fwlink/?LinkId=307137&clcid=0x409&wt.mc_id=o~msft~vscom~home-vsts-hero~27308&campaign=o~msft~vscom~home-vsts-hero~27308).

  > [!NOTE]
  > Mer information finns i avsnittet om att [ansluta till Azure DevOps Services](https://docs.microsoft.com/azure/devops/organizations/projects/connect-to-projects?view=vsts).

*  Du behöver en virtuell Linux-dator som distributionsmål.  Mer information finns i [Skapa och hantera virtuella Linux-datorer med Azure CLI](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm).

*  Öppna inkommande port 80 för den virtuella datorn. Mer information finns i avsnittet om att [skapa nätverkssäkerhetsgrupper med hjälp av Azure Portal](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic).

## <a name="get-your-sample-app-code"></a>Hämta din exempelappkod

Om du redan har en app i GitHub som du vill distribuera kan du prova att skapa en pipeline för den koden.

Men om du är en ny användare kan du få en bättre start med hjälp av vår exempelkod. I så fall gaffel denna repo i GitHub:

#### <a name="java"></a>[Java](#tab/java)

```
https://github.com/spring-projects/spring-petclinic
```

> [!NOTE]
> Petclinic är en [Java Spring Boot](https://spring.io/guides/gs/spring-boot) ansökan byggd med [Maven](https://spring.io/guides/gs/maven/).

#### <a name="javascript"></a>[Javascript](#tab/java-script)

```
https://github.com/azure-devops/fabrikam-node
```

> [!NOTE]
> Denna Node.js app byggdes genom [Yeoman](https://yeoman.io/learning/index.html). Den använder Express, Bower och Grunt. Den har även vissa npm-paket som beroenden.
> Exempelappen innehåller också ett skript som konfigurerar Nginx och distribuerar appen. Det körs på de virtuella datorerna. Vad skriptet gör:
> 1. Installerar Node, Nginx och PM2.
> 2. Konfigurerar Nginx och PM2.
> 3. Startar Node-appen.

* * * 

## <a name="prerequisites-for-the-linux-vm"></a>Förutsättningar för Linux-VM

Exempelappar som nämns ovan har testats på Ubuntu 16.04, och vi rekommenderar att du använder samma version av Linux VM för den här snabbstarten.
Följ ytterligare steg som beskrivs nedan baserat på körningsstacken som används för appen.

#### <a name="java"></a>[Java](#tab/java)

- Skapa en Virtuell Linux-dator i Azure med den [här](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu8-ubuntu-1804) mallen, som ger en fullständigt stödd OpenJDK-baserad körning.
- För distribution av Java-servlets på Tomcat-servern skapar du en Virtuell Linux-dator med Java 8 med [den här](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu8-ubuntu-1804) Azure-mallen och [konfigurerar Tomcat 9.x som en tjänst](https://tomcat.apache.org/tomcat-9.0-doc/setup.html).
- För distribution av Java EE-baserad app använder du en Azure-mall för att skapa en [Linux VM + Java + WebSphere 9.x](https://azuremarketplace.microsoft.com/marketplace/apps/midvision.websphere-application-server-nde-90) eller en Linux VM + Java + [WebLogic 12.x](https://azuremarketplace.microsoft.com/marketplace/apps/oracle.20191009-arm-oraclelinux-wls-admin) eller en [Linux VM +Java](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu8-ubuntu-1804) + WildFly/JBoss 14 

#### <a name="javascript"></a>[Javascript](#tab/java-script)

Om du vill installera en javascript-app eller en Node.js-app behöver du en virtuell Linux-dator med Nginx-webbserver för att distribuera appen.
Om du inte redan har en Virtuell Linux-dator med Nginx skapar du en nu i Azure med hjälp av stegen i [det här exemplet](/azure/virtual-machines/linux/quick-create-cli).

* * * 

## <a name="create-an-azure-pipelines-environment-with-azure-virtual-machines"></a>Skapa en Azure Pipelines-miljö med virtuella Azure-datorer

Virtuella datorer kan läggas till som resurser i [miljöer](https://docs.microsoft.com/azure/devops/pipelines/process/environments) och kan riktas för distributioner med flera datorer. Distributionshistorikvyer i miljön ger spårbarhet från virtuell dator till pipelinen och sedan till commit.

Du kan skapa en miljö i hubben "**Miljöer**" i avsnittet "**Pipelines**".
1.  Logga in på din Azure DevOps-organisation och navigera till ditt projekt.
2.  I projektet navigerar du till sidan **Pipelines.** Välj sedan **Miljöer** och klicka på **Skapa miljö**. Ange ett **namn** (obligatoriskt) för miljön och en **beskrivning**.
3.  Välj **Virtuella datorer** som en **resurs** som ska läggas till i miljön och klicka på **Nästa**.
4.  Välj Operativsystem (Windows/Linux) och **kopiera PS-registreringsskript**. 
5.  Kör nu det kopierade skriptet från en administratör PowerShell-kommandotolk på var och en av de virtuella mål-datorerna som ska registreras med den här miljön.
    > [!NOTE]
    > - Personlig åtkomsttoken för den inloggade användaren infogas i skriptet som upphör att gälla samma dag vilket gör det kopierade skriptet oanvändbart därpå.
    > - Om din virtuella dator redan har någon agent som körs på den, ange ett unikt namn för "agent" för att registrera sig med miljön.
6.  När den virtuella datorn har registrerats börjar den visas som en miljöresurs under fliken "resurser" i miljön.

    ![VMskapering](media/tutorial-deploy-vms-azure-pipelines/vm-creation.png)

7.  Om du vill lägga till fler virtuella datorer kan du visa och kopiera skriptet igen genom att klicka på "Lägg till resurs" och välja "Virtuella datorer" som resurs. Det här skriptet skulle förbli detsamma för alla virtuella datorer som ska läggas till i den här miljön. 
8.  Varje dator interagerar med Azure Pipelines för att samordna distributionen av din app.

    ![VMresource_view](media/tutorial-deploy-vms-azure-pipelines/vm-resourceview.png)

9. Du kan lägga till taggar till den virtuella datorn som en del av det interaktiva PS-registreringsskriptet (eller) du kan också lägga till/ta bort samma från resursvyn genom att klicka på trippelpunkterna i slutet av varje VM-resurs i resursvyn.

   Med taggarna som du tilldelar kan du begränsa distributionen till specifika virtuella datorer när miljön används i ett distributionsjobb. Taggar är begränsade till 256 tecken, men det finns ingen gräns för hur många taggar du kan använda.

   ![VMtags (VMtags)](media/tutorial-deploy-vms-azure-pipelines/vm-tags.png)

* * * 

## <a name="define-your-ci-build-pipeline"></a>Definiera din CI-byggpipeline

Du behöver en pipeline för kontinuerlig integrering (CI) som publicerar webbprogrammet, samt ett distributionsskript som kan köras lokalt på Ubuntu-servern. Ställ in en CI-build-pipeline baserat på den körning som du vill använda. 

1. Logga in på din Azure DevOps-organisation och navigera till ditt projekt.

1. I projektet navigerar du till sidan **Pipelines.** Välj sedan åtgärden för att skapa en ny pipeline.

1. Gå igenom stegen i guiden genom att först välja **GitHub** som plats för källkoden.

1. Du kan omdirigeras till GitHub för att logga in. Ange i så fall dina GitHub-autentiseringsuppgifter.

1. När listan över databaser visas väljer du önskad exempelappdatabas.

1. Azure Pipelines analyserar din databas och rekommenderar en lämplig pipeline-mall.

#### <a name="java"></a>[Java](#tab/java)

Välj **startmallen** och kopiera nedanstående YAML-kodavsnitt som bygger ditt Java-projekt och kör tester med Apache Maven:

```YAML
- job: Build
  displayName: Build Maven Project
  steps:
  - task: Maven@3
    displayName: 'Maven Package'
    inputs:
      mavenPomFile: 'pom.xml'
  - task: CopyFiles@2
    displayName: 'Copy Files to artifact staging directory'
    inputs:
      SourceFolder: '$(System.DefaultWorkingDirectory)'
      Contents: '**/target/*.?(war|jar)'
      TargetFolder: $(Build.ArtifactStagingDirectory)
  - upload: $(Build.ArtifactStagingDirectory)
    artifact: drop
```

Om du vill ha mer vägledning följer du stegen som nämns i [Skapa java-app med Maven](https://docs.microsoft.com/azure/devops/pipelines/ecosystems/java).

#### <a name="javascript"></a>[Javascript](#tab/java-script)

Välj **startmallen** och kopiera nedanstående YAML-kodavsnitt som skapar ett allmänt Node.js-projekt med npm.

```YAML
- stage: Build
  displayName: Build stage
  jobs:  
  - job: Build
    displayName: Build
    - task: NodeTool@0
      inputs:
        versionSpec: '10.x'
      displayName: 'Install Node.js'
    - script: |
        npm install
        npm run build --if-present
        npm run test --if-present
      displayName: 'npm install, build and test'
    - task: ArchiveFiles@2
      displayName: 'Archive files'
      inputs:
        rootFolderOrFile: '$(System.DefaultWorkingDirectory)'
        includeRootFolder: false
        archiveType: zip
        archiveFile: $(Build.ArtifactStagingDirectory)/$(Build.BuildId).zip
        replaceExistingArchive: true
    - upload: $(Build.ArtifactStagingDirectory)/$(Build.BuildId).zip
      artifact: drop
```

Om du vill ha mer vägledning följer du stegen i [Appen Skapa nod.js med gulp](https://docs.microsoft.com/azure/devops/pipelines/ecosystems/javascript).

- Ta en titt på pipelinen för att se vad den gör. Kontrollera att alla standardindata är lämpliga för koden.

- Välj **Spara och kör**och välj sedan Anslut direkt till **huvudgrenen**och välj sedan Spara och **kör** igen.

- En ny körning startas. Vänta tills körningen är klar.

* * * 

## <a name="define-cd-steps-to-deploy-to-the-linux-vm"></a>Definiera CD-steg som ska distribueras till Virtuella Linux-datorn

1. Redigera pipelinen ovan och inkludera ett [distributionsjobb](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs) genom att referera till miljön och de VM-resurser som du har tidigare med hjälp av YAML-syntaxen nedan:

   ```YAML
   jobs:  
   - deployment: VMDeploy
     displayName: web
     environment:
       name:  <environment name>
       resourceType: VirtualMachine
       tags: web1
     strategy:
   ```
2. Du kan välja specifika uppsättningar virtuella datorer från miljön för att ta emot distributionen genom att ange de **taggar** som du har definierat för varje virtuell dator i miljön.
[Här](https://docs.microsoft.com/azure/devops/pipelines/yaml-schema?view=azure-devops&tabs=schema#deployment-job) är det fullständiga YAML-schemat för distributionsjobb.

3. Du kan ange `runOnce` eithor eller `rolling` som distributionsstrategi. 

   `runOnce`är den enklaste distributionsstrategin där alla livscykelkrokar, nämligen `preDeploy` `deploy`, `routeTraffic`och `postRouteTraffic`, utförs en gång. Sedan, `on:` `success` antingen `on:` `failure` eller utförs.

   Nedan visas exemplet YAML-kodavsnitt för: `runOnce`
   ```YAML
   jobs:
   - deployment: VMDeploy
     displayName: web
     pool:
       vmImage: 'Ubuntu-16.04'
     environment:
       name: <environment name>
       resourceType: VirtualMachine
     strategy:
         runOnce:
           deploy:
             steps:
             - script: echo my first deployment
   ```

4. Nedan följer ett exempel på YAML-kodavsnittet som du kan använda för att definiera en rullande strategi för virtuella datorer som uppdaterar upp till 5 mål i varje iteration. `maxParallel`kommer att bestämma antalet mål som kan distribueras till, parallellt. Urvalet står för absolut antal eller procentandel mål som måste vara tillgängliga när som helst exklusive de mål som distribueras till. Det används också för att fastställa framgångs- och felvillkor under distributionen.

   ```YAML
   jobs: 
   - deployment: VMDeploy
     displayName: web
     environment:
       name: <environment name>
       resourceType: VirtualMachine
     strategy:
         rolling:
           maxParallel: 5  #for percentages, mention as x%
           preDeploy:
             steps:
             - download: current
               artifact: drop
             - script: echo initialize, cleanup, backup, install certs
           deploy:
             steps:
             - task: Bash@3
               inputs:
                 targetType: 'inline'
                 script: |
                   # Modify deployment script based on the app type
                   echo "Starting deployment script run"
                   sudo java -jar '$(Pipeline.Workspace)/drop/**/target/*.jar'
           routeTraffic:
             steps:
             - script: echo routing traffic
           postRouteTraffic:
             steps:
             - script: echo health check post-route traffic
           on:
             failure:
               steps:
               - script: echo Restore from backup! This is on failure
             success:
               steps:
               - script: echo Notify! This is on success
   ```

   Med varje körning av det här jobbet `<environment name>` registreras distributionshistorik mot den miljö som du har skapat och registrerat de virtuella datorerna.

## <a name="run-your-pipeline-and-get-traceability-views-in-environment"></a>Kör pipelinen och få spårbarhetsvyer i miljön
Distributioner vy av miljön ger fullständig spårbarhet av commits och arbetsobjekt, och en cross-pipeline distribution historia per miljö / resurs.

![VMDeployments_view](media/tutorial-deploy-vms-azure-pipelines/vm-deployments.png)
  
![VMjobs_view](media/tutorial-deploy-vms-azure-pipelines/vm-jobsview.png)

## <a name="next-steps"></a>Nästa steg
- Du kan fortsätta att [anpassa pipelinen](https://docs.microsoft.com/azure/devops/pipelines/customize-pipeline) som du just skapade.
- Mer information om vad du kan göra i YAML-pipelines finns i [YAML-schemareferens](https://docs.microsoft.com/azure/devops/pipelines/yaml-schema).
- Mer information om hur du distribuerar en LAMP-stack (Linux, Apache, MySQL och PHP) får du i nästa kurs.

> [!div class="nextstepaction"]
> [Distribuera LAMP-stack](tutorial-lamp-stack.md)
