---
title: Kontinuerlig integrering & kontinuerlig distribution – Azure IoT Edge
description: Konfigurera kontinuerlig integrering och kontinuerlig distribution – Azure IoT Edge med Azure DevOps, Azure Pipelines
author: shizn
manager: philmea
ms.author: xshi
ms.date: 08/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4b99e83a8e71b13183c76321c7076b85a212f021
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76510982"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge"></a>Kontinuerlig integrering och kontinuerlig distribution till Azure IoT Edge

Du kan enkelt använda DevOps med dina Azure IoT Edge-program med de inbyggda Azure IoT Edge-uppgifterna i Azure Pipelines. Den här artikeln visar hur du kan använda funktionerna för kontinuerlig integrering och kontinuerlig distribution i Azure Pipelines för att skapa, testa och distribuera program snabbt och effektivt till din Azure IoT Edge.

![Diagram - CI- och CD-filialer för utveckling och produktion](./media/how-to-ci-cd/cd.png)

I den här artikeln får du lära dig hur du använder de inbyggda Azure IoT Edge-uppgifterna för Azure Pipelines för att skapa två pipelines för din IoT Edge-lösning. Det finns fyra åtgärder som kan användas i Azure IoT Edge-uppgifter.

* **Azure IoT Edge - Build Module avbildningar** tar din IoT Edge-lösningskod och bygger behållaravbildningarna.
* **Azure IoT Edge - Push Module avbildningar** skickar modulavbildningar till behållarregistret som du angav.
* **Azure IoT Edge - Generera distributionmanifest tar** en deployment.template.json-fil och variablerna och genererar sedan den slutliga IoT Edge-distributionsmanipuleringsfilen.
* **Azure IoT Edge - Distribuera till IoT Edge-enheter** hjälper till att skapa IoT Edge-distributioner till enstaka/flera IoT Edge-enheter.

## <a name="prerequisites"></a>Krav

* En Azure Repos-databas. Om du inte har någon kan du [skapa en ny Git-repa i projektet](https://docs.microsoft.com/azure/devops/repos/git/create-new-repo?view=vsts&tabs=new-nav).
* En IoT Edge-lösning som har bekräftats och drivits till din databas. Om du vill skapa en ny exempellösning för att testa den här artikeln följer du stegen i [Utveckla och felsöka moduler i Visual Studio-kod](how-to-vs-code-develop-module.md) eller [Utveckla och felsöka C#-moduler i Visual Studio](how-to-visual-studio-develop-csharp-module.md).

   För den här artikeln behöver du bara den lösningsmapp som skapas av IoT Edge-mallarna i Visual Studio-koden eller Visual Studio. Du behöver inte skapa, skicka, distribuera eller felsöka den här koden innan du fortsätter. Du ställer in dessa processer i Azure Pipelines.

   Om du skapar en ny lösning klonar du databasen lokalt först. När du sedan skapar lösningen kan du välja att skapa den direkt i databasmappen. Du kan enkelt begå och driva de nya filerna därifrån.

* Ett behållarregister där du kan skicka modulavbildningar. Du kan använda [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) eller ett register från tredje part.
* En aktiv [IoT-hubb](../iot-hub/iot-hub-create-through-portal.md) med minst IoT Edge-enheter för testning av separata test- och produktionsdistributionsfaser. Du kan följa snabbstartsartiklarna för att skapa en IoT Edge-enhet på [Linux](quickstart-linux.md) eller [Windows](quickstart.md)

Mer information om hur du använder Azure Repos finns i [Dela din kod med Visual Studio och Azure Repos](https://docs.microsoft.com/azure/devops/repos/git/share-your-code-in-git-vs?view=vsts)

## <a name="configure-continuous-integration"></a>Konfigurera kontinuerlig integrering

I det här avsnittet skapar du en ny version pipeline. Konfigurera pipelinen så att den körs automatiskt när du checkar in eventuella ändringar i exempel-IoT Edge-lösningen och publicerar byggloggar.

>[!NOTE]
>Den här artikeln använder den visuella designern Azure DevOps. Innan du följer stegen i det här avsnittet stänger du av förhandsgranskningsfunktionen för den nya yaml-pipelinens upplevelse.
>
>1. I Azure DevOps väljer du din profilikon och väljer sedan **Förhandsgranska funktioner**.
>2. Inaktivera **ny yaml-pipeline-upplevelse.**
>
>Mer information finns i [Skapa en byggpipeline](https://docs.microsoft.com/azure/devops/pipelines/create-first-pipeline).

1. Logga in på din Azure DevOps-organisation (**\/https: /dev.azure.com/{your organization}/**) och öppna projektet som innehåller din IoT Edge-lösningslagringsplats.

   I den här artikeln har vi skapat en databas som heter **IoTEdgeRepo**. Förvaret innehåller **IoTEdgeSolution** som har koden för en modul med namnet **filtermodul**.

   ![Öppna ditt DevOps-projekt](./media/how-to-ci-cd/init-project.png)

2. Navigera till Azure Pipelines i projektet. Öppna fliken **Bygg** och välj **Ny pipeline**. Om du redan har bygga pipelines väljer du knappen **Nytt.** Välj sedan **Nybyggd pipeline**.

    ![Skapa en ny bygg-pipeline](./media/how-to-ci-cd/add-new-build.png)

3. Följ anvisningarna för att skapa pipelinen.

   1. Ange källinformation för den nya byggpipelinen. Välj **Azure Repos Git** som källa och välj sedan projektet, databasen och grenen där IoT Edge-lösningskoden finns. Välj sedan **Fortsätt**.

      ![Välj pipelinekälla](./media/how-to-ci-cd/pipeline-source.png)

   2. Välj **Tomt jobb** i stället för en mall.

      ![Börja med en tom process](./media/how-to-ci-cd/start-with-empty.png)

4. När pipelinen har skapats tas du till pipeline-redigeraren. I din pipelinebeskrivning väljer du rätt agentpool baserat på din målplattform:

   * Om du vill bygga dina moduler i plattform amd64 för Linux-behållare, välj **Hosted Ubuntu 1604**

   * Om du vill bygga dina moduler i plattformen amd64 för Windows 1809 behållare, måste du [ställa in självvärderade agent på Windows](https://docs.microsoft.com/azure/devops/pipelines/agents/v2-windows?view=vsts).

   * Om du vill bygga dina moduler i plattform arm32v7 eller arm64 för Linux-behållare, måste du [ställa in självvärderade agent på Linux](https://blogs.msdn.microsoft.com/iotdev/2018/11/13/setup-azure-iot-edge-ci-cd-pipeline-with-arm-agent/).

     ![Konfigurera build agent-pool](./media/how-to-ci-cd/configure-env.png)

5. Pipelinen är förkonfigurerad med ett jobb som heter **Agent jobb 1**. Välj plustecknet**+**( ) om du vill lägga till tre uppgifter i jobbet: **Azure IoT Edge** två gånger, **Kopiera filer** en gång och **Publicera build-artefakter** en gång. (Hovra över namnet på varje uppgift för att se knappen **Lägg till.)**

   ![Lägga till Azure IoT Edge-uppgift](./media/how-to-ci-cd/add-iot-edge-task.png)

   När alla fyra aktiviteter läggs till ser agent-jobbet ut som följande exempel:

   ![Tre aktiviteter i byggpipeline](./media/how-to-ci-cd/add-tasks.png)

6. Välj den första **Azure IoT Edge-uppgiften** för att redigera den. Den här uppgiften bygger alla moduler i lösningen med målplattformen som du anger.

   * **Visningsnamn**: Acceptera **standardavbildningarna för Azure IoT Edge - Build.Display**name : Accept the default Azure IoT Edge - Build module images .
   * **Åtgärd**: Acceptera **standardversionsmodulavbildningar**.
   * **.template.json-fil**: Välj ellipsen (**...**) och navigera till **filen deployment.template.json** i databasen som innehåller IoT Edge-lösningen.
   * **Standardplattform:** Välj lämplig plattform för dina moduler baserat på din mål-IoT Edge-enhet.
   * **Utdatavariabler:** Utdatavariablerna innehåller ett referensnamn som du kan använda för att konfigurera filsökvägen där filen deployment.json ska genereras. Ange referensnamnet till något minnesvärt som **kant**.

7. Välj den andra **Azure IoT Edge-uppgiften** för att redigera den. Den här uppgiften skickar alla modulavbildningar till behållarregistret som du väljer.

   * **Visningsnamn**: Visningsnamnet uppdateras automatiskt när åtgärdsfältet ändras.
   * **Åtgärd**: Använd listrutan för att välja **Push-modulbilder**.
   * **Registertyp för behållare:** Välj den typ av behållarregister som du använder för att lagra modulavbildningarna. Beroende på vilken registertyp du väljer ändras formuläret. Om du väljer **Azure Container Registry**använder du listrutorna för att välja Azure-prenumerationen och namnet på behållarregistret. Om du väljer **Allmänt behållarregister**väljer du **Ny** för att skapa en registertjänstanslutning.
   * **.template.json-fil**: Välj ellipsen (**...**) och navigera till **filen deployment.template.json** i databasen som innehåller IoT Edge-lösningen.
   * **Standardplattform:** Välj samma plattform som dina inbyggda modulavbildningar.

   Om du har flera behållarregister som värd för modulavbildningarna måste du duplicera den här uppgiften, välja olika behållarregister och använda **Bypass-moduler** i de avancerade inställningarna för att kringgå avbildningarna som inte är avsedda för det här specifika registret.

8. Markera uppgiften **Kopiera filer för** att redigera den. Använd den här uppgiften om du vill kopiera filer till katalogen för artefaktdscensättning.

   * **Visningsnamn**: Kopiera filer till: Släpp mapp.
   * **Innehåll**: Placera två rader `deployment.template.json` `**/module.json`i det här avsnittet och . Dessa två typer av filer är indata för att generera IoT Edge-distributionsmanifest. Måste kopieras till artefakt mellanlagringsmappen och publiceras för utgivningspipeline.
   * **Målmapp:** Placera `$(Build.ArtifactStagingDirectory)`variabeln . Se [Skapa variabler](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#build-variables) om du vill veta mer om beskrivningen.

9. Välj aktiviteten **Publicera byggartefakter** om du vill redigera den. Ange katalogsökväg för artefaktdscensättning till aktiviteten så att sökvägen kan publiceras för att frigöra pipeline.

   * **Visningsnamn**: Publicera artefakt: släpp.
   * **Sökväg att publicera** `$(Build.ArtifactStagingDirectory)`: Placera variabeln . Se [Skapa variabler](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#build-variables) om du vill veta mer om beskrivningen.
   * **Artefaktnamn**: drop.
   * **Artefaktpubliceringsplats**: Azure Pipelines.

10. Öppna fliken **Utlösare** och markera kryssrutan Aktivera **kontinuerlig integrering**. Kontrollera att grenen som innehåller koden ingår.

    ![Aktivera utlösare för kontinuerlig integrering](./media/how-to-ci-cd/configure-trigger.png)

11. Spara den nya byggpipelinen med **knappen Spara.**

Den här pipelinen är nu konfigurerad för att köras automatiskt när du skickar ny kod till din repo. Den sista uppgiften, som publicerar pipelineartefakterna, utlöser en versionspipeline. Fortsätt till nästa avsnitt för att skapa versionspipelinen.

## <a name="configure-continuous-deployment"></a>Konfigurera kontinuerlig distribution

I det här avsnittet skapar du en versionspipeline som är konfigurerad för att köras automatiskt när din build pipeline släpper artefakter och den visar distributionsloggar i Azure Pipelines.

Skapa en ny pipeline och lägg till en ny fas

1. Välj + Ny **pipeline**på fliken **Versioner** . Om du redan har versionspipelor väljer du knappen **+ Ny** och väljer + **Ny versionspipeline**.  

    ![Lägga till releasepipeline](./media/how-to-ci-cd/add-release-pipeline.png)

2. När du uppmanas att välja en mall väljer du att börja med ett **tomt jobb**.

    ![Börja med ett tomt jobb](./media/how-to-ci-cd/start-with-empty-job.png)

3. Din nya versionspipeline initieras med ett steg, som kallas **steg 1.** Byt namn på steg 1 för att **utveckla** och behandla den som en testmiljö. Vanligtvis har pipelines för kontinuerlig distribution flera steg, inklusive **dev,** **mellanlagring** och **prod**. Du kan skapa mer baserat på din DevOps-övning. Stäng fönstret sceninformation när det har bytt namn.

4. Länka versionen till byggartefakterna som publiceras av byggpipelinen. Klicka på **Lägg till** i artefaktområdet.

   ![Lägga till artefakter](./media/how-to-ci-cd/add-artifacts.png)  

5. Välj källtyp **Bygg**i **Lägg till en artefaktsida**. Välj sedan projektet och byggpipelinen som du skapade. Välj sedan **Lägg till**.

   ![Lägg till en bygg-artefakt](./media/how-to-ci-cd/add-an-artifact.png)

6. Öppna artefaktutlösare och välj växlingsknappen för att aktivera den kontinuerliga distributionsutlösaren. Nu skapas en ny version varje gång en ny version är tillgänglig.

   ![Konfigurera utlösare för kontinuerlig distribution](./media/how-to-ci-cd/add-a-trigger.png)

7. **Utvecklingsfasen** är förkonfigurerad med ett jobb och noll aktiviteter. Välj **Uppgifter** på pipeline-menyn och välj sedan **utvecklingsfasen.**  Välj jobb- och aktivitetsantalet för att konfigurera aktiviteterna i det här steget.

    ![Konfigurera utvecklingsuppgifter](./media/how-to-ci-cd/view-stage-tasks.png)

8. I **utvecklingsfasen** bör du se ett **standardagentjobb**. Du kan konfigurera information om agentjobbet, men distributionsuppgiften är plattformsokänslig så att du kan använda antingen **Hosted VS2017** eller **Hosted Ubuntu 1604** i **agentpoolen** (eller någon annan agent som hanteras av dig själv).

9. Markera plustecknet**+**( ) om du vill lägga till två aktiviteter. Sök efter och lägg till **Azure IoT Edge** två gånger.

    ![Lägga till uppgifter för utveckling](./media/how-to-ci-cd/add-task-qa.png)

10. Välj den första **Azure IoT Edge-uppgiften** och konfigurera den med följande värden:

    * **Visningsnamn**: Visningsnamnet uppdateras automatiskt när åtgärdsfältet ändras.
    * **Åtgärd**: Använd listrutan för att välja **Generera distributionsmanifest**. Om du ändrar åtgärdsvärdet uppdateras också aktivitetsvisningsnamnet så att det matchar.
    * **.template.json-fil**: `$(System.DefaultWorkingDirectory)/Drop/drop/deployment.template.json`Sätt sökvägen . Sökvägen publiceras från byggpipeline.
    * **Standardplattform:** Välj samma värde när du skapar modulavbildningarna.
    * **Utdatabana:** Sätt `$(System.DefaultWorkingDirectory)/Drop/drop/configs/deployment.json`banan . Den här sökvägen är den slutliga IoT Edge-distributionsmanipuleringsfilen.

    Dessa konfigurationer hjälper till att ersätta `deployment.template.json` url:erna för modulavbildningen i filen. **Distributionsmanifestet Generera** hjälper också till att ersätta `deployment.template.json` variablerna med det exakta värdet som du definierade i filen. I VS/VS-kod anger du det faktiska `.env` värdet i en fil. I Azure Pipelines anger du värdet på fliken Släpp pipelinevariabler.

    * **ACR_ADDRESS**: Din Azure-behållarregisteradress.
    * **ACR_PASSWORD**: Lösenordet för Azure Container-behållarregistret.
    * **ACR_USER**: Användarnamnet för Azure Container-registret.

    Om du har andra variabler i projektet kan du ange namn och värde på den här fliken. Generera **distribution manifestet** kan bara känna `${VARIABLE}` igen variablerna är i `*.template.json` smak, se till att du använder detta i dina filer.

    ![Konfigurera variabler för utgivningspipeline](./media/how-to-ci-cd/configure-variables.png)

11. Välj den andra **Azure IoT Edge-uppgiften** och konfigurera den med följande värden:

    * **Visningsnamn**: Visningsnamnet uppdateras automatiskt när åtgärdsfältet ändras.
    * **Åtgärd**: Använd listrutan för att välja **Distribuera till IoT Edge-enheter**. Om du ändrar åtgärdsvärdet uppdateras också aktivitetsvisningsnamnet så att det matchar.
    * **Azure-prenumeration:** Välj den prenumeration som innehåller din IoT Hub.
    * **IoT Hub namn**: Välj din IoT-hubb.
    * **Välj en/flera enheter:** Välj om du vill att versionspipelinen ska distribueras till en enhet eller flera enheter.
      * Om du distribuerar till en enda enhet anger du **IoT Edge-enhets-ID**.
      * Om du distribuerar till flera enheter anger du enhetens **målvillkor**. Målvillkoret är ett filter som matchar en uppsättning IoT Edge-enheter i IoT Hub. Om du vill använda Enhetstaggar som villkor måste du uppdatera motsvarande enhetstaggar med IoT Hub-enhetstvilling. Uppdatera **distributions-ID:n för distribution i IoT Edge** och **distributionsprioriteten för IoT Edge** i de avancerade inställningarna. Mer information om hur du skapar en distribution för flera enheter finns i [Förstå IoT Edge automatiska distributioner](module-deployment-monitoring.md).
    * Expandera avancerade inställningar, välj **IoT Edge-distributions-ID**, placera variabeln `$(System.TeamProject)-$(Release.EnvironmentName)`. Detta mappar projektet och utgivningsnamnet med ditt IoT Edge-distributions-ID.

12. Välj **Spara** om du vill spara ändringarna i den nya versionspipelinen. Återgå till pipeline-vyn genom att välja **Pipeline** på menyn.

## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>Verifiera IoT Edge CI/CD med bygg- och utgivningspipelorna

Om du vill utlösa ett byggjobb kan du antingen skicka ett åtagande till källkodsdatabasen eller utlösa det manuellt. I det här avsnittet utlöser du ci/cd-pipelinen manuellt för att testa att den fungerar. Kontrollera sedan att distributionen lyckas.

1. Navigera till den byggpipeline som du skapade i början av den här artikeln.

2. Du kan utlösa ett byggjobb i din byggpipeline genom att välja **köknappen** som i följande skärmbild.

    ![Manuell utlösare](./media/how-to-ci-cd/manual-trigger.png)

3. Välj byggjobbet för att titta på dess framsteg. Om build-pipelinen har slutförts utlöses **dev** en version till utvecklingsfasen.

    ![Skapa loggar](./media/how-to-ci-cd/build-logs.png)

4. Den lyckade **utvecklingsversionen** skapar IoT Edge-distribution för att rikta IoT Edge-enheter.

    ![Släpp till dev](./media/how-to-ci-cd/pending-approval.png)

5. Klicka på **utvecklingsstadiet** för att se utgivningsloggar.

    ![Lanseringsloggar](./media/how-to-ci-cd/release-logs.png)

## <a name="next-steps"></a>Nästa steg

* Exempel på metodtips för IoT Edge DevOps i [Azure DevOps Project for IoT Edge](how-to-devops-project.md)
* Förstå IoT Edge-distributionen i [Förstå IoT Edge-distributioner för enskilda enheter eller i stor skala](module-deployment-monitoring.md)
* Gå igenom stegen för att skapa, uppdatera eller ta bort en distribution i [Distribuera och övervaka IoT Edge-moduler i stor skala](how-to-deploy-monitor.md).
