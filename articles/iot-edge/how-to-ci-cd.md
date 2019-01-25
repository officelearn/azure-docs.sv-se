---
title: Kontinuerlig integrering och kontinuerlig distribution – Azure IoT Edge | Microsoft Docs
description: Konfigurera kontinuerlig integrering och kontinuerlig distribution – Azure IoT Edge med Azure DevOps, Azure-Pipelines
author: shizn
manager: philmea
ms.author: xshi
ms.date: 01/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 196d08f47ddfdbb86b8e96ae0e5ca3d3e3e5917e
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54886772"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge"></a>Kontinuerlig integrering och kontinuerlig distribution till Azure IoT Edge

Du kan enkelt implementera DevOps med Azure IoT Edge-program med inbyggda Azure IoT Edge-uppgifter i Azure-Pipelines. Den här artikeln visar hur du kan använda den kontinuerliga integreringen och funktioner för kontinuerlig distribution av Azure Pipelines för att bygga, testa och distribuera program snabbt och effektivt till Azure IoT Edge. 

I den här artikeln får du lära dig hur du använder de inbyggda Azure IoT Edge-uppgifterna för Azure-Pipelines för att skapa två pipelines för din IoT Edge-lösning. Först tar för din kod och bygger lösningen och push-överför modul-avbildningar på ditt behållarregister och skapa ett manifest för distribution. Andra distribuerar dina moduler till IoT Edge-målenheter.  

![Diagram - CI och CD grenar för utveckling och produktion](./media/how-to-ci-cd/cd.png)


## <a name="prerequisites"></a>Förutsättningar

* En lagringsplats för Azure-databaser. Om du inte har något, kan du [skapa en ny Git-lagringsplats i projektet](https://docs.microsoft.com/azure/devops/repos/git/create-new-repo?view=vsts&tabs=new-nav).
* En IoT Edge-lösning allokeras och skickas till din lagringsplats. Om du vill skapa en ny lösning för exemplet för att testa den här artikeln följer du stegen i [utveckla och felsöka moduler i Visual Studio Code](how-to-vs-code-develop-module.md) eller [utveckla och felsöka C# moduler i Visual Studio](how-to-visual-studio-develop-csharp-module.md).
   * I den här artikeln är allt du behöver lösningsmappen som skapats av IoT Edge-mallar i Visual Studio Code eller Visual Studio. Du behöver inte skapa, skicka, distribuera eller felsöka den här koden innan du fortsätter. Du konfigurerar dessa processer i Azure-Pipelines. 
   * Om du skapar en ny lösning kan du klona lagringsplatsen lokalt först. När du skapar lösningen välja du sedan att skapa den direkt i databasmappen. Du kan enkelt genomför och push-överför de nya filerna därifrån. 
* Ett behållarregister där du kan skicka modulen avbildningar. Du kan använda [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) eller en tredje parts-registret. 
* En aktiv [IoT-hubb](../iot-hub/iot-hub-create-through-portal.md) med minst IoT Edge-enheter för att testa de separata stegen för testning och produktion distribution. Du kan följa artiklarna i snabbstarten för att skapa en IoT Edge-enhet på [Linux](quickstart-linux.md) eller [Windows](quickstart.md)


Mer information om hur du använder Azure-databaser finns i [dela din kod med Visual Studio och Azure-lagringsplatser](https://docs.microsoft.com/azure/devops/repos/git/share-your-code-in-git-vs?view=vsts)

## <a name="configure-continuous-integration"></a>Konfigurera kontinuerlig integrering
I det här avsnittet skapar du en ny build-pipeline. Konfigurera pipeline kan köras automatiskt när du checka in ändringarna till exempel IoT Edge-lösning och publicera build-loggar.

>[!NOTE]
>Den här artikeln använder Azure DevOps visuell designer. Inaktivera förhandsversionsfunktionen för den nya upplevelsen för YAML pipeline skapas innan du följer stegen i det här avsnittet. 
>1. I Azure DevOps, väljer din profilikonen och välj sedan **Förhandsversionsfunktioner**.
>2. Aktivera **upplevelse för nya YAML pipeline när du skapar** av. 
>
>Mer information finns i [skapa en build-pipeline](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav#create-a-build-pipeline).

1. Logga in på din Azure DevOps-organisation ( **https://dev.azure.com/{your organisation} /**) och öppna projektet som innehåller din lagringsplats för IoT Edge-lösning.

   För den här artikeln har vi skapat en databas som heter **IoTEdgeRepo**. Databasen innehåller **IoTEdgeSolution** som har kod för en modul med namnet **filtermodule**. 

   ![Öppna din DevOps-projekt](./media/how-to-ci-cd/init-project.png)

2. Gå till Azure Pipelines i projektet. Öppna den **bygger** fliken och markera **ny pipeline**. Om du redan har skapandet av pipelines, väljer du den **New** knappen. Välj sedan **buildu pipeline**.

    ![Skapa en ny bygg-pipeline](./media/how-to-ci-cd/add-new-build.png)

3. Följ anvisningarna för att skapa din pipeline. 

   1. Ange källinformation för din nya build-pipeline. Välj **Azure lagringsplatser Git** som källa, välj sedan projektet, lagringsplatsen och grenen som där koden IoT Edge-lösning finns. Välj **Fortsätt**. 

      ![Välj din pipelinekälla](./media/how-to-ci-cd/pipeline-source.png)

   2. Välj **tom jobbet** i stället för en mall. 

      ![Börja med en tom process](./media/how-to-ci-cd/start-with-empty.png)

4. När du har skapat din pipeline, tas du till pipeline-redigeringsprogrammet. Välj rätt agentpoolen baserat på din målplattform i din pipeline-beskrivning: 
    
    * Om du vill bygga dina moduler i plattformen amd64 för Linux-behållare kan du välja **finns Ubuntu 1604**

    * Om du vill bygga dina moduler i plattformen amd64 för Windows 1809 behållare kan du behöva [ställer in lokal agent på Windows](https://docs.microsoft.com/azure/devops/pipelines/agents/v2-windows?view=vsts).

    * Om du vill bygga dina moduler i plattformen arm32v7 för Linux-behållare kan du behöva [ställer in lokal agent på Linux](https://blogs.msdn.microsoft.com/iotdev/2018/11/13/setup-azure-iot-edge-ci-cd-pipeline-with-arm-agent/).
    
    ![Konfigurera build-agentpoolen](./media/how-to-ci-cd/configure-env.png)

5. Din pipeline är förkonfigurerad med ett jobb som heter **agentjobbet 1**. Klicka på plustecknet (**+**) att lägga till tre uppgifter i jobbet: **Azure IoT Edge** två gånger, och **publicera skapa artefakter** när. (Håll muspekaren över namnet på varje uppgift att se den **Lägg till** knappen.)

   ![Lägg till Azure IoT Edge-aktivitet](./media/how-to-ci-cd/add-iot-edge-task.png)

   När alla tre aktiviteter läggs Agent-jobbet som ser ut som i följande exempel:
    
   ![Tre aktiviteter i pipelinen build](./media/how-to-ci-cd/add-tasks.png)

6. Välj först **Azure IoT Edge** aktiviteten för att redigera den. Den här uppgiften skapar alla moduler i lösningen med målplattform att du anger, genereras även den **deployment.json** -filen som talar om hur du konfigurerar distributionen för din IoT Edge-enheter.

   * **Visningsnamn**: Acceptera standardvärdet **Azure IoT Edge - Build-modulen avbildningar**.
   * **Åtgärd**: Acceptera standardvärdet **skapa modulen avbildningar**. 
   * **. filen template.json**: Välj ellipsen (**...** ) och gå till den **deployment.template.json** filen i databasen som innehåller din IoT Edge-lösning. 
   * **Standard-plattformen**: Välj lämplig plattform för dina moduler baserat på målet IoT Edge-enhet. 
   * **Utdata variabler**: Variablerna utdata innehåller en referensnamn som du kan använda för att konfigurera sökvägen till filen där deployment.json filen kommer att genereras. Ange referensnamnet på något egen som **edge**. 

7. Välj andra **Azure IoT Edge** aktiviteten för att redigera den. Den här uppgiften skickar alla modulen avbildningar till behållarregistret som du väljer. Autentiseringsuppgifterna för container registry att läggs även den **deployment.json** filen så att din IoT Edge-enhet kan komma åt modul-avbildningar. 

   * **Visningsnamn**: Visningsnamnet uppdateras automatiskt när fältet åtgärd ändras. 
   * **Åtgärd**: Använd listrutan för att välja **skicka modulen avbildningar**. 
   * **Behållaren registertyp**: Välj typ av behållarregister som används för att lagra dina avbildningar för modulen. Beroende på vilken typ av registret som du använder, formuläret ändras. Om du väljer **Azure Container Registry**, Använd listrutorna för att välja Azure-prenumeration och namnet på ditt behållarregister. Om du väljer **allmän Behållarregister**väljer **New** att skapa en anslutning för registry-tjänsten. 
   * **. filen template.json**: Välj ellipsen (**...** ) och gå till den **deployment.template.json** filen i databasen som innehåller din IoT Edge-lösning. 
   * **Standard-plattformen**: Välj samma plattform som bilderna inbyggd modul.

   Om du har flera behållarregister som värd för dina modul-avbildningar, måste du duplicera den här uppgiften, Välj olika behållarregister och använda **kringgå modulen eller modulerna** i avancerade inställningar för att kringgå de avbildningar som inte är för detta specifika register.

8. Välj den **publicera skapa artefakter** aktiviteten för att redigera den. Ange sökvägen till distributionsfilen som skapats av denna build task. Ange den **sökvägen till publicera** värde som matchar variabeln utdata som du angav i uppgift för build-modulen. Till exempel `$(edge.DEPLOYMENT_FILE_PATH)`. Lämna de andra värdena som standard. 

9. Öppna den **utlösare** fliken och markerar kryssrutan **möjliggöra kontinuerlig integration**. Se till att grenen som innehåller koden ingår.

    ![Aktivera utlösaren för kontinuerlig integrering](./media/how-to-ci-cd/configure-trigger.png)

10. Spara den nya build-pipelinen med **spara** knappen.

Den här pipelinen har nu konfigurerats för att köras automatiskt när du skickar ny kod till din lagringsplats. Den sista aktiviteten, publicerar pipeline-artefakter utlöser en releasepipeline. Fortsätt till nästa avsnitt för att skapa versionspipelinen. 

## <a name="configure-continuous-deployment"></a>Konfigurera kontinuerlig distribution
I det här avsnittet skapar du en pipeline för versionen som är konfigurerad för att köras automatiskt när build-pipeline sjunker artefakter och distributionsloggarna visas i Azure-Pipelines.

I det här avsnittet skapar du två olika faser, en testdistributioner och en för Produktionsdistribution. 

### <a name="create-test-stage"></a>Skapa test steg

Skapa en ny pipeline och konfigurera sin första steget för kvalitet assurance (kvalitetskontroll) distributioner. 

1. I den **versioner** fliken **+ ny pipeline**. Eller, om du redan har releaser kan välja den **+ ny** och välj **+ ny viktig pipeline**.  

    ![Lägg till releasepipeline](./media/how-to-ci-cd/add-release-pipeline.png)

2. När du uppmanas att välja en mall, välja att börja med en **tom jobbet**.

    ![Börja med en tom jobb](./media/how-to-ci-cd/start-with-empty-job.png)

3. Din nya releasepipeline initierar med ett steg som kallas **steg 1**. Byt namn på steg 1 till **QA** och behandlar det som en testmiljö. Pipelines för kontinuerlig distribution har vanligtvis flera faser. Du kan skapa fler utifrån dina DevOps-metoder. Stäng fönstret scenen information när den har bytt namn. 

    ![Skapa test-miljö steg](./media/how-to-ci-cd/QA-env.png)

4. Länka versionen till byggartefakterna som publiceras av build-pipelinen. Klicka på **Lägg till** i artefakter område.

   ![Lägg till artefakter](./media/how-to-ci-cd/add-artifacts.png)  
    
5. I **Lägg till en artefakt sida**, Välj typ av datakälla **skapa**. Markera projektet och build-pipeline som du skapade. Välj sedan **Lägg till**.

   ![Lägg till en byggesartefakt](./media/how-to-ci-cd/add-an-artifact.png)

6. Öppna artefakt-utlösare och välj växlingsknappen för att aktivera utlösare av kontinuerlig distribution. En ny version kommer nu att skapas varje gång en ny version är tillgänglig.

   ![Konfigurera utlösare av kontinuerlig distribution](./media/how-to-ci-cd/add-a-trigger.png)

7. Den **QA** scenen är förkonfigurerad med en uppgift och noll uppgifter. Pipeline-menyn väljer **uppgifter** Välj sedan den **QA** steg.  Välj antalet jobb- och för att konfigurera aktiviteterna i det här steget.

    ![Konfigurera QA uppgifter](./media/how-to-ci-cd/view-stage-tasks.png)

8. I steget QA du bör se en standard **agentjobbet**. Du kan konfigurera information om agent-jobbet, men aktiviteten distribution är skiftlägesokänsligt plattform så att du kan använda antingen **finns VS2017** eller **finns Ubuntu 1604** i den **agentpoolen**(eller andra agent som hanteras av dig själv). 

9. Klicka på plustecknet (**+**) att lägga till en aktivitet. Sök efter och lägga till **Azure IoT Edge**. 

    ![Lägg till aktiviteter för QA](./media/how-to-ci-cd/add-task-qa.png)

10. Välj den nya Azure IoT Edge-aktiviteten och konfigurera den med följande värden:

   * **Visningsnamn**: Visningsnamnet uppdateras automatiskt när fältet åtgärd ändras. 
   * **Åtgärd**: Använd listrutan för att välja **distribuera till IoT Edge-enhet**. Visningsnamn för aktiviteten så att den matchar uppdateras automatiskt när du ändrar Åtgärdsvärdet för.
   * **Azure-prenumeration**: Välj den prenumeration som innehåller din IoT-hubb.
   * **IoT-hubbnamn**: Välj din IoT-hubb. 
   * **Välj en eller flera enhet**: Välj om du vill releasepipeline ska distribueras till en eller flera enheter. 
      * Om du distribuerar till en enda enhet, anger du den **enhets-ID för IoT Edge**. 
      * Om du distribuerar till flera enheter, kan du ange enheten **rikta villkor**. Målvillkoret har ett filter för att matcha en uppsättning Edge-enheter i IoT Hub. Om du vill använda taggar för enheten som villkoret måste du uppdatera dina motsvarande enheter taggar med enhetstvillingen i IoT Hub. Uppdatera den **distributions-ID för IoT Edge** och **IoT Edge-distributionsprioritet** i de avancerade inställningarna. Läs mer om hur du skapar en distribution för flera enheter, [automatisk förstå IoT Edge-distributioner](module-deployment-monitoring.md).

11. Välj **spara** att spara dina ändringar i den nya releasepipeline. Gå tillbaka till vyn pipelinen genom att välja **Pipeline** på menyn. 

### <a name="create-production-stage"></a>Skapa tillverkningen

Skapa en andra steget i din releasepipeline för Produktionsdistribution. 

1. Se en andra steget för produktion genom att klona QA-steget. Håll markören över QA scenen och välj sedan knappen Kopiera. 

    ![Klona steg](./media/how-to-ci-cd/clone-stage.png)

2. Välj det nya steget kallas **kopia av QA**, för att öppna dess egenskaper. Ändra namnet på scenen **PROD**, för produktion. Stäng fönstret scenen egenskaper. 

3. Om du vill öppna PROD scenen uppgifter, Välj **uppgifter** pipeline-menyn väljer den **PROD** steg. 

4. Välj Azure IoT Edge-uppgiften att konfigurera om för din produktionsmiljö. Distributionsinställningarna är förmodligen samma för QA och PROD, förutom att du vill använda en annan enhet eller uppsättning enheter i produktionen. Uppdatera enhetens ID-fältet eller villkor och distribution ID målfälten för dina produktionsenheter. 

5. Spara den med den **spara** knappen. Välj sedan **Pipeline** att gå tillbaka till vyn pipeline.
    
6. På sätt som den här releasepipeline har konfigurerats, byggesartefakten utlöser den **QA** steget och sedan **PROD** mellanlagra varje gång en ny version är klar. Men vill vanligtvis integrera vissa testfall på QA-enheter och godkänna manuellt distributionen för produktion. Använd följande steg för att skapa ett villkor för godkännande för PROD steg:

    1. Öppna den **före villkor** inställningspanelen.

        ![Öppna före villkor](./media/how-to-ci-cd/pre-deploy-conditions.png)    

    2. Visa/Dölj de **före godkännanden** villkoret till **aktiverad**. Lägga till en eller flera användare eller grupper i den **godkännare** fältet och anpassa andra godkännande principer som du vill. Stäng panelen före villkor för att spara ändringarna.
    
       ![Ange villkor](./media/how-to-ci-cd/set-pre-deployment-conditions.png)


7. Spara din releasepipeline med den **spara** knappen. 

    
## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>Verifiera IoT Edge CI/CD med build and release-pipelines

Om du vill utlösa ett skapandejobb du skickar något till lagringsplatsen för källkod eller utlösa den manuellt. I det här avsnittet ska utlösa du manuellt CI/CD-pipeline för att testa att det fungerar. Kontrollera att distributionen är klar.

1. Gå till build-pipeline som du skapade i början av den här artikeln. 

2. Du kan utlösa en build-jobb i build-pipeline genom att välja den **kö** knappen som i följande skärmbild.

    ![Manuell utlösare](./media/how-to-ci-cd/manual-trigger.png)

3. Välj build-jobb kan du titta på förloppet. Om build pipelinen har slutförts, utlöser en version till **QA** steg. 

    ![Arbetsversionsloggar](./media/how-to-ci-cd/build-logs.png)

4. Distributionen till **QA** steget utlöser en avisering till godkännaren. Kontrollera att modulerna som har distribuerats på enheten eller enheter som du har valt med QA-steg. Gå sedan för att frigöra pipeline och ge godkännande för versionen att gå till PROD steg genom att välja den **PROD** och sedan välja **Godkänn**. 

    ![Väntar på godkännande](./media/how-to-ci-cd/pending-approval.png)

5. När godkännaren har godkänt den här ändringen, den kan bara distribueras till **PROD**.

## <a name="next-steps"></a>Nästa steg

* Förstå IoT Edge-distribution i [förstå IoT Edge-distributioner för enskilda enheter eller i stor skala](module-deployment-monitoring.md)
* Gå igenom stegen för att skapa, uppdatera eller ta bort en distribution i [distribuera och övervaka IoT Edge-moduler i stor skala](how-to-deploy-monitor.md).
