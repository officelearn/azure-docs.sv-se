---
title: Kontinuerlig integrering och kontinuerlig distribution – Azure IoT Edge | Microsoft Docs
description: Konfigurera kontinuerlig integrering och kontinuerlig distribution – Azure IoT Edge med Azure DevOps, Azure-Pipelines
author: shizn
manager: philmea
ms.author: xshi
ms.date: 12/12/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 519b9e73e63285f167fca76061f84752ff676916
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54431511"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge"></a>Kontinuerlig integrering och kontinuerlig distribution till Azure IoT Edge

Du kan enkelt implementera DevOps med Azure IoT Edge-program med inbyggda Azure IoT Edge-uppgifter i Azure Pipelines eller [Azure IoT Edge-plugin för Jenkins](https://plugins.jenkins.io/azure-iot-edge) på Jenkins-servern. Den här artikeln visar hur du kan använda den kontinuerliga integreringen och funktioner för kontinuerlig distribution av Azure Pipelines för att bygga, testa och distribuera program snabbt och effektivt till Azure IoT Edge. 

I den här artikeln får du lära dig hur du:
* Skapa och kontrollera i ett exempel på IoT Edge-lösning.
* Konfigurera kontinuerlig integrering (CI) för att skapa lösningen.
* Konfigurera kontinuerlig distribution (CD) för att distribuera lösningen och visa svar.

![Diagram - CI och CD grenar för utveckling och produktion](./media/how-to-ci-cd/cd.png)


## <a name="create-a-sample-azure-iot-edge-solution-using-visual-studio-code"></a>Skapa en exempel Azure IoT Edge-lösning som använder Visual Studio Code

I det här avsnittet skapar du ett exempel på IoT Edge lösning som innehåller enhetstester kan du köra som en del av skapandeprocessen. Innan du följer riktlinjerna i det här avsnittet utför du stegen i [utveckla en IoT Edge-lösning med flera moduler i Visual Studio Code](how-to-develop-multiple-modules-vscode.md).

1. I VS Code kommandopalett skriver och kör kommandot **Azure IoT Edge: Ny IoT Edge-lösning**. Välj sedan din arbetsytemapp, anger du Lösningsnamnet (standardnamnet är **EdgeSolution**), och skapa en C#-modul (**FilterModule**) som den första användarmodulen i den här lösningen. Du måste också ange lagringsplatsen för Docker-avbildningar för din första modul. Standard avbildningslagringsplatsen baseras på en lokal Docker-register (`localhost:5000/filtermodule`). Ändra den till Azure Container Registry (`<your container registry address>/filtermodule`) eller Docker-hubb för ytterligare kontinuerlig integrering.

    ![Ställ in Azure Container Registry](./media/how-to-ci-cd/acr.png)

2. VS Code-fönstret läser in din arbetsyta för IoT Edge-lösning. Du kan också skriva och kör **Azure IoT Edge: Lägg till IoT Edge-modul** att lägga till flera moduler. Det finns en `modules` mapp, ett `.vscode` mappen och ett manifest mallfilen distribution i rotmappen. Alla användare modulen koder kommer att undermappar i mappen `modules`. Den `deployment.template.json` är manifestet Distributionsmall. Några av parametrarna i den här filen kommer att tolkas från den `module.json`, som finns i varje modul-mapp.

3. Din lösning för IoT Edge-exemplet är nu klar. Standard C# modulen fungerar som en modul för pipe-meddelande. I den `deployment.template.json`, visas den här lösningen innehåller två moduler. Meddelandet kommer att genereras från den `tempSensor` -modulen och kommer skickas direkt `FilterModule`, skickas sedan till din IoT-hubb.

4. Spara dessa projekt och genomför i din Azure-lagringsplatser lagringsplats.
    
> [!NOTE]
> Mer information om hur du använder Azure-databaser finns i [dela din kod med Visual Studio och Azure-lagringsplatser](https://docs.microsoft.com/azure/devops/repos/git/share-your-code-in-git-vs?view=vsts).


## <a name="configure-azure-pipelines-for-continuous-integration"></a>Konfigurera Azure Pipelines för kontinuerlig integrering
I det här avsnittet skapar du en build-pipeline som är konfigurerad för att köras automatiskt när du checkar in ändringar till exempel IoT Edge-lösningen och den visar build-loggarna i Azure-Pipelines.

1. Logga in på din Azure DevOps-organisation ( **https://dev.azure.com/{your organisation} /**) och öppna projektet där du har markerat i exempelappen.

    ![Checka in kod i Azure-Pipelines](./media/how-to-ci-cd/init-project.png)

1. I din Azure-Pipelines, öppnar den **bygger** fliken **+ ny pipeline**. Eller, om du redan har skapandet av pipelines, välja den **+ ny** knappen. Välj sedan **buildu pipeline**.

    ![Skapa en ny bygg-pipeline](./media/how-to-ci-cd/add-new-build.png)

1. Om det händer väljer du Azure-lagringsplatser för källan. Välj sedan projektet, lagringsplatsen och grenen som var koden finns. Välj **fortsätta**.

    ![Välj Azure lagringsplatser Git](./media/how-to-ci-cd/select-vsts-git.png)

    I **Välj en mall** fönstret Välj **börja med en tom process**.

    ![Börja med en tom process](./media/how-to-ci-cd/start-with-empty.png)

1. I pipeline-redigeringsprogrammet väljer du agentpoolen. 
    
    * Om du vill bygga dina moduler i plattformen amd64 för Linux-behållare kan du välja **finns Ubuntu 1604**
    * Om du vill bygga dina moduler i plattformen amd64 för Windows 1809 behållare kan du behöva [ställer in lokal agent på Windows](https://docs.microsoft.com/azure/devops/pipelines/agents/v2-windows?view=vsts).
    * Om du vill bygga dina moduler i plattformen arm32v7 för Linux-behållare kan du behöva [ställer in lokal agent på Linux](https://blogs.msdn.microsoft.com/iotdev/2018/11/13/setup-azure-iot-edge-ci-cd-pipeline-with-arm-agent/).
    
    ![Konfigurera build-agentpoolen](./media/how-to-ci-cd/configure-env.png)

1. I Agent-jobbet, öppna ”+” att lägga till tre uppgifter i build-pipeline. De första två är från **Azure IoT Edge**. Och den tredje är från **publicera skapa artefakter**
    
    ![Lägga till aktiviteter till build-pipeline](./media/how-to-ci-cd/add-tasks.png)

1. I först **Azure IoT Edge** uppgift, uppdatera den **visningsnamn** till **Azure IoT Edge - Build-modulen avbildningar**, och i den **åtgärd** listrutan Välj **skapa modulen avbildningar**. I den **. filen template.json** kontroll markerar den **deployment.template.json** -fil som beskriver din IoT Edge-lösning. Välj sedan **standard plattform**, se till att välja samma plattform som din IoT Edge-enhet. Den här uppgiften kommer att skapa alla moduler i lösningen med målplattformen som du har angett. Och generera de **deployment.json** fil, du kan hitta sökvägen till filen i utdata variabler. Ange aliaset till `edge` för den här variabeln.
    
    ![Konfigurera build modulen avbildningar](./media/how-to-ci-cd/build-and-push.png)

1. I andra **Azure IoT Edge** uppgift, uppdatera den **visningsnamn** till **Azure IoT Edge - Push-modulen avbildningar**, och i den **åtgärd** listrutan Välj **skicka modulen avbildningar**. Välj typ av Container Registry, måste du konfigurera och välj samma registret i din code(module.json). I den **. filen template.json** kontroll markerar den **deployment.template.json** -fil som beskriver din IoT Edge-lösning. Välj sedan **standard plattform**, se till att välja samma plattform för inbyggd modul-avbildningar. Den här uppgiften skickar alla modulen avbildningar till behållarregistret som du har valt. Och även lägga till autentiseringsuppgifter för container-registret i den **deployment.json** fil. Om du har flera behållarregister som värd för dina modul-avbildningar, måste du duplicera den här uppgiften, Välj olika behållarregister och använda **kringgå modulen eller modulerna** i avancerade inställningar för att kringgå de avbildningar som inte är för detta specifika register.

    ![Konfigurera push-modulen avbildningar](./media/how-to-ci-cd/push.png)

1. I **publicera skapa artefakter** uppgift, ange distribution-fil som skapats av denna build task. Ange den **sökvägen till publicera** till `$(edge.DEPLOYMENT_FILE_PATH)`.

    ![Konfigurera publicera artefakter uppgift](./media/how-to-ci-cd/publish-build-artifacts.png)

1. Öppna den **utlösare** fliken och aktivera den **kontinuerlig integrering** utlösaren. Se till att grenen som innehåller koden ingår.

    ![Aktivera utlösaren för kontinuerlig integrering](./media/how-to-ci-cd/configure-trigger.png)

    Spara den nya build-pipelinen med **spara** knappen.


## <a name="configure-azure-pipelines-for-continuous-deployment"></a>Konfigurera Azure Pipelines för kontinuerlig distribution
I det här avsnittet skapar du en releasepipeline som är konfigurerad för att köras automatiskt när build-pipeline sjunker artefakter och den visar distributionsloggar i Azure-Pipelines.

1. I den **versioner** fliken **+ ny pipeline**. Eller, om du redan har releaser kan välja den **+ ny** och välj **+ ny viktig pipeline**.  

    ![Lägg till releasepipeline](./media/how-to-ci-cd/add-release-pipeline.png)

    I **Välj en mall** fönstret Välj **börja med en tom jobbet.**

    ![Börja med en tom jobb](./media/how-to-ci-cd/start-with-empty-job.png)

2. Sedan initiera versionspipelinen med ett steg: **Steg 1**. Byt namn på den **steg 1** till **QA** och behandlar det som en testmiljö. Den finns vanligtvis flera faser i en typisk kontinuerlig distributionspipeline kan du skapa fler utifrån dina DevOps-metoder.

    ![Skapa test-miljö steg](./media/how-to-ci-cd/QA-env.png)

3. Länka versionen till byggartefakterna. Klicka på **Lägg till** i artefakter område.

    ![Lägg till artefakter](./media/how-to-ci-cd/add-artifacts.png)  
    
    I **Lägg till en artefakt sida**, välj källtyp **skapa**. Välj sedan projektet och build-pipeline som du skapade. Välj sedan **Lägg till**.

    ![Lägg till en byggesartefakt](./media/how-to-ci-cd/add-an-artifact.png)

    Öppna utlösare av kontinuerlig distribution så att nya versionen kommer att skapas varje gång en ny version är tillgänglig.

    ![Konfigurera utlösare av kontinuerlig distribution](./media/how-to-ci-cd/add-a-trigger.png)

4. Gå till **QA scenen** och konfigurera aktiviteterna i det här steget.

    ![Konfigurera QA uppgifter](./media/how-to-ci-cd/view-stage-tasks.png)

   Distributionsuppgift är plattform okänsliga, vilket innebär att du kan välja antingen **finns VS2017** eller **finns Ubuntu 1604** i den **agentpoolen** (eller andra agent som hanteras av själv). Välj ”+” och lägga till en aktivitet.

    ![Lägg till aktiviteter för QA](./media/how-to-ci-cd/add-task-qa.png)

5. I Azure IoT Edge-aktivitet, navigerar du till den **åtgärd** listrutan, väljer **distribuera till IoT Edge-enhet**. Välj din **Azure-prenumeration** och indata din **IoT-hubbnamn**. Du kan välja att distribuera till en eller flera enheter. Om du distribuerar till **flera enheter**, måste du ange enheten **rikta villkor**. Målvillkoret har ett filter för att matcha en uppsättning Edge-enheter i IoT Hub. Om du vill använda taggar för enheten som villkoret måste du uppdatera dina motsvarande enheter taggar med enhetstvillingen i IoT Hub. Uppdatera den **distributions-ID för IoT Edge** ”distribuera-qa” i avancerade inställningar. Anta att du har flera IoT Edge enheter har taggats som ”qa” uppgiftskonfigurationen bör vara som i följande skärmbild. 

    ![Distribuera till QA](./media/how-to-ci-cd/deploy-to-qa.png)

    Spara den nya versionspipelinen med de **spara** knappen. Välj sedan **Pipeline** att gå tillbaka till pipelinen.

6. Det andra steget är för din produktionsmiljö. Om du vill lägga till en ny fas ”PROD”, kan du klona scenen ”QA” och Byt namn på klonade scenen för att **PROD**,

    ![Klona steg](./media/how-to-ci-cd/clone-stage.png)

7. Konfigurera uppgifter för din produktionsmiljö. Anta att du har flera IoT Edge enheter som har taggats som ”prod', uppgift-konfigurationer uppdatering Målvillkor” prod ”och ange distributions-ID” distribuera-prod ”i avancerade inställningar. Spara den med den **spara** knappen. Välj sedan **Pipeline** att gå tillbaka till pipelinen.
    
    ![Distribuera till produktion](./media/how-to-ci-cd/deploy-to-prod.png)

7. För närvarande vår byggesartefakten ska utlösas kontinuerligt **QA** steget och sedan **PROD** steg. Men de flesta av de tider som du behöver för att integrera vissa testfall på QA-enheter och manuellt godkänna bitarna. Bits kommer senare att distribueras till PROD miljö. Ställ in ett godkännande i PROD steget som följande skärmbild.

    1. Öppna **före villkor** inställningen panelen.

        ![Öppna före villkor](./media/how-to-ci-cd/pre-deploy-conditions.png)    

    2. Ange **aktiverad** i **före godkännanden**. Och Fyll i den **godkännare** indata. Spara den med **spara** knappen.
    
        ![Ange villkor](./media/how-to-ci-cd/set-pre-deployment-conditions.png)


8. Din releasepipeline har nu konfigurerats som följande skärmbild.

    ![Releasepipeline](./media/how-to-ci-cd/release-pipeline.png)

    
## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>Verifiera IoT Edge CI/CD med build and release-pipelines

I det här avsnittet ska du utlösa en build om du vill göra CI/CD-pipeline som fungerar. Kontrollera distributionen är klar.

1. Om du vill utlösa ett skapandejobb du skickar något till lagringsplatsen för källkod eller utlösa den manuellt. Du kan utlösa en build-jobb i build-pipeline genom att välja den **kö** knappen som i följande skärmbild.

    ![Manuell utlösare](./media/how-to-ci-cd/manual-trigger.png)

2. Om build pipelinen har slutförts, utlöses en version till **QA** steg. Navigera för att skapa pipelinen loggar och du bör se följande skärmbild.

    ![Skapa loggar](./media/how-to-ci-cd/build-logs.png)

3. Distributionen till **QA** steg ska utlösa en avisering till godkännaren. Navigera för att frigöra pipeline kan du se följande skärmbild. 

    ![Väntar på godkännande](./media/how-to-ci-cd/pending-approval.png)


4. När godkännaren har godkänt den här ändringen, den kan bara distribueras till **PROD**.

    ![Distribuera om du vill prod](./media/how-to-ci-cd/approve-and-deploy-to-prod.png)


## <a name="next-steps"></a>Nästa steg

* Förstå IoT Edge-distribution i [förstå IoT Edge-distributioner för enskilda enheter eller i stor skala](module-deployment-monitoring.md)
* Gå igenom stegen för att skapa, uppdatera eller ta bort en distribution i [distribuera och övervaka IoT Edge-moduler i stor skala](how-to-deploy-monitor.md).
