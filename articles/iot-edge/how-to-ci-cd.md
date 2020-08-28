---
title: Kontinuerlig integrering & kontinuerlig distribution – Azure IoT Edge
description: Konfigurera kontinuerlig integrering och kontinuerlig distribution – Azure IoT Edge med Azure-DevOps, Azure-pipeline
author: shizn
manager: philmea
ms.author: xshi
ms.date: 08/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 398cf947f0a2d250c3cd0ed73a75bc3c091e5f7a
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2020
ms.locfileid: "89047538"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge"></a>Kontinuerlig integrering och kontinuerlig distribution till Azure IoT Edge

Du kan enkelt införa DevOps med dina Azure IoT Edge-program med de inbyggda Azure IoT Edge uppgifterna i Azure-pipelines. Den här artikeln visar hur du kan använda funktionerna för kontinuerlig integrering och kontinuerlig distribution i Azure pipelines för att bygga, testa och distribuera program snabbt och effektivt till din Azure IoT Edge.

![Diagram – CI-och CD-grenar för utveckling och produktion](./media/how-to-ci-cd/cd.png)

I den här artikeln får du lära dig hur du använder de inbyggda Azure IoT Edge uppgifterna för Azure pipelines för att skapa två pipeliner för din IoT Edge-lösning. Det finns fyra åtgärder som kan användas i Azure IoT Edge uppgifter.

* **Azure IoT Edge-avbildning av modul** tar din IoT Edge lösnings kod och skapar behållar avbildningarna.
* **Azure IoT Edge-push module-avbildningar** pushar modul avbildningar till det behållar register som du har angett.
* **Azure IoT Edge-generera distributions manifestet** tar en deployment.template.jspå filen och variablerna och genererar sedan den slutliga IoT Edge distributions manifest filen.
* **Azure IoT Edge-Deploy till IoT Edge-enheter** hjälper dig att skapa IoT Edge-distributioner till en eller flera IoT Edge enheter.

## <a name="prerequisites"></a>Krav

* En Azure databaser-lagringsplats. Om du inte har någon kan du [skapa en ny git-lagrings platsen i projektet](https://docs.microsoft.com/azure/devops/repos/git/create-new-repo?view=vsts&tabs=new-nav).
* En IoT Edge lösning har allokerats och skickas till din lagrings plats. Om du vill skapa en ny exempel lösning för att testa den här artikeln följer du stegen i [utveckla och felsöka moduler i Visual Studio Code](how-to-vs-code-develop-module.md) eller [utveckla och felsöka C#-moduler i Visual Studio](how-to-visual-studio-develop-csharp-module.md).

   Allt du behöver i den här artikeln är mappen Solution som skapats av IoT Edge mallar i antingen Visual Studio Code eller Visual Studio. Du behöver inte bygga, skicka, distribuera eller Felsöka den här koden innan du fortsätter. Du ställer in dessa processer i Azure-pipeliner.

   Om du skapar en ny lösning ska du klona din lagrings plats lokalt först. När du sedan skapar lösningen kan du välja att skapa den direkt i databasmappen. Du kan enkelt bekräfta och skicka de nya filerna därifrån.

* Ett behållar register där du kan push-modul avbildningar. Du kan använda [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) eller ett register från en tredje part.
* En aktiv [IoT-hubb](../iot-hub/iot-hub-create-through-portal.md) med minst IoT Edge enheter för att testa de separata distributions faserna för test och produktion. Du kan följa snabb starts artiklarna för att skapa en IoT Edge-enhet i [Linux](quickstart-linux.md) eller [Windows](quickstart.md)

Mer information om hur du använder Azure-databaser finns i [dela din kod med Visual Studio och Azure databaser](https://docs.microsoft.com/azure/devops/repos/git/share-your-code-in-git-vs?view=vsts)

## <a name="configure-continuous-integration"></a>Konfigurera kontinuerlig integrering

I det här avsnittet skapar du en ny versions pipeline. Konfigurera pipelinen så att den körs automatiskt när du checkar in eventuella ändringar i exempel IoT Edge lösning och publicera build-loggar.

>[!NOTE]
>I den här artikeln används den visuella designern för Azure DevOps. Innan du följer stegen i det här avsnittet stänger du av förhands gransknings funktionen för den nya YAML-upplevelsen för pipeline.
>
>1. I Azure DevOps väljer du din profil ikon och väljer sedan för **hands versions funktioner**.
>2. Sätt igång **nya yaml-pipeline för att skapa pipelinen** .
>
>Mer information finns i [skapa en pipeline för bygge](https://docs.microsoft.com/azure/devops/pipelines/create-first-pipeline).

1. Logga in på din Azure DevOps-organisation (**https: \/ /dev.Azure.com/{Your Organization}/**) och öppna projektet som innehåller din IoT Edge lösnings lagrings plats.

   I den här artikeln har vi skapat ett lagrings lager med namnet **IoTEdgeRepo**. Databasen innehåller **IoTEdgeSolution** som har koden för en modul med namnet **filtermodule**.

   ![Öppna ditt DevOps-projekt](./media/how-to-ci-cd/init-project.png)

2. Navigera till Azure-pipeliner i ditt projekt. Öppna fliken **builds** och välj **ny pipeline**. Om du redan har skapat pipelines väljer du knappen **nytt** . Välj sedan **ny versions pipeline**.

    ![Skapa en ny bygg-pipeline](./media/how-to-ci-cd/add-new-build.png)

3. Följ anvisningarna för att skapa din pipeline.

   1. Ange käll information för den nya bygg pipelinen. Välj **Azure databaser git** som källa och välj sedan projektet, databasen och grenen där din IoT Edge lösnings kod finns. Välj sedan **Fortsätt**.

      ![Välj din pipeline-källa](./media/how-to-ci-cd/pipeline-source.png)

   2. Välj **tomt jobb** i stället för en mall.

      ![Börja med en tom process](./media/how-to-ci-cd/start-with-empty.png)

4. När din pipeline har skapats, kommer du till pipeline-redigeraren. I din pipeline-Beskrivning väljer du rätt agent-pool baserat på din mål plattform:

   * Om du vill bygga dina moduler i plattform amd64 för Linux-behållare väljer du **värdbaserad Ubuntu 1604**

   * Om du vill skapa moduler i plattforms-amd64 för Windows 1809-behållare måste du konfigurera en lokal [agent i Windows](https://docs.microsoft.com/azure/devops/pipelines/agents/v2-windows?view=vsts).

   * Om du vill skapa moduler i plattforms-arm32v7 eller arm64 för Linux-behållare måste du konfigurera en lokal [agent på Linux](https://blogs.msdn.microsoft.com/iotdev/2018/11/13/setup-azure-iot-edge-ci-cd-pipeline-with-arm-agent/).

     ![Konfigurera bygga Gent pool](./media/how-to-ci-cd/configure-env.png)

5. Din pipeline är förkonfigurerad med ett jobb som kallas **Agent jobb 1**. Välj plus tecknet ( **+** ) för att lägga till tre aktiviteter i jobbet: **Azure IoT Edge** två gånger, **Kopiera filer** en gång och **publicera Bygg artefakter** en gång. (Hovra över namnet på varje uppgift för att se knappen **Lägg till** .)

   ![Lägg till Azure IoT Edge uppgift](./media/how-to-ci-cd/add-iot-edge-task.png)

   När alla fyra aktiviteter läggs till ser ditt Agent jobb ut som i följande exempel:

   ![Tre uppgifter i build-pipeline](./media/how-to-ci-cd/add-tasks.png)

6. Välj den första **Azure IoT Edge** uppgiften för att redigera den. Den här uppgiften skapar alla moduler i lösningen med den mål plattform som du anger.

   * **Visnings namn**: acceptera standard **avbildningarna för Azure IoT Edge-build-modulen**.
   * **Åtgärd**: Godkänn standard **avbildningarna för build-modulen**.
   * **.template.jspå fil**: Välj ellipsen (**...**) och navigera till **deployment.template.jspå** filen i lagrings platsen som innehåller din IoT Edge lösning.
   * **Standard plattform**: Välj lämplig plattform för dina moduler baserat på din mål IoT Edge enhet.
   * **Utmatnings**variabler: utdata-variablerna innehåller ett referens namn som du kan använda för att konfigurera fil Sök vägen där deployment.jsfilen ska skapas. Ange referens namnet till något som du kan komma ihåg som **Edge**.


   Dessa konfigurationer använder avbildnings lagrings platsen och taggen som definieras i `module.json` filen för att namnge och tagga avbildningen av modulen. Med hjälp av **module-avbildningar** kan du även ersätta variablerna med det exakta värdet som du definierar i `module.json` filen. I Visual Studio eller Visual Studio Code anger du det faktiska värdet i en `.env` fil. I Azure-pipeline ställer du in värdet på fliken **pipeline-variabler** . Välj fliken **variabler** och konfigurera namn och värde enligt följande:

    * **ACR_ADDRESS**: din Azure Container Registry adress. 

    Om du har andra variabler i ditt projekt kan du ange namn och värde på den här fliken. **Bygg avbildningar** identifierar endast variabler i `${VARIABLE}` format. Se till att du använder det här formatet i dina `**/module.json` filer.
    
7. Välj den andra **Azure IoT Edge** uppgiften för att redigera den. Den här uppgiften push-överför alla modulblad till det behållar register som du väljer.

   * **Visnings namn**: visnings namnet uppdateras automatiskt när åtgärds fältet ändras.
   * **Åtgärd**: Använd List rutan för att välja **push module-avbildningar**.
   * **Behållarens register typ**: Välj den typ av behållar register som du använder för att lagra dina modulblad. Formuläret ändras beroende på vilken register typ du väljer. Om du väljer **Azure Container Registry**använder du List rutan för att välja Azure-prenumerationen och namnet på behållar registret. Om du väljer **allmän container Registry**väljer du **ny** för att skapa en anslutning till en register tjänst.
   * **.template.jspå fil**: Välj ellipsen (**...**) och navigera till **deployment.template.jspå** filen i lagrings platsen som innehåller din IoT Edge lösning.
   * **Standard plattform**: Välj samma plattform som de inbyggda modul avbildningarna.

   Om du har flera behållar register som ska vara värdar för dina modulblad, måste du duplicera den här uppgiften, välja ett annat behållar register och använda **bypass-modulen** i de avancerade inställningarna för att kringgå avbildningarna som inte är för det aktuella registret.

8. Välj aktiviteten **Kopiera filer** för att redigera den. Använd den här aktiviteten för att kopiera filer till en artefakt uppsamlings katalog.

   * **Visnings namn**: kopiera filer till: släpp mapp.
   * **Innehåll**: Lägg till två rader i det här avsnittet `deployment.template.json` och `**/module.json` . Dessa två typer av filer är de indata som genereras för att generera IoT Edge distributions manifestet. Måste kopieras till utsamlings platsen för artefakter och publiceras för versions pipelinen.
   * **Målmapp**: Lägg till variabeln `$(Build.ArtifactStagingDirectory)` . Se [build-variabler](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#build-variables) för att lära dig mer om beskrivningen.

9. Välj aktiviteten **publicera Bygg artefakter** för att redigera den. Tillhandahåll katalog Sök vägen för artefakt mellanlagring till aktiviteten så att sökvägen kan publiceras i en versions pipeline.

   * **Visnings namn**: publicera artefakt: släpp.
   * **Sökväg att publicera**: Lägg till variabeln `$(Build.ArtifactStagingDirectory)` . Se [build-variabler](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#build-variables) för att lära dig mer om beskrivningen.
   * **Artefakt namn**: drop.
   * **Artefakt publicerings plats**: Azure-pipeliner.

10. Öppna fliken **utlösare** och markera kryss rutan för att **aktivera kontinuerlig integrering**. Se till att grenen som innehåller din kod ingår.

    ![Aktivera kontinuerlig integrations utlösare](./media/how-to-ci-cd/configure-trigger.png)

11. Spara den nya bygg pipelinen med knappen **Spara** .

Den här pipelinen har nu kon figurer ATS för att köras automatiskt när du push-överför ny kod till din lagrings platsen. Den senaste aktiviteten, publicering av pipeline-artefakter, utlöser en versions pipeline. Fortsätt till nästa avsnitt för att bygga lanserings pipelinen.

## <a name="configure-continuous-deployment"></a>Konfigurera kontinuerlig distribution

I det här avsnittet skapar du en versions pipeline som är konfigurerad för att köras automatiskt när din Bygg pipeline tappar ut artefakter och det visar distributions loggar i Azure-pipelines.

Skapa en ny pipeline och Lägg till ett nytt stadium

1. På fliken **utgåvor** väljer du **+ ny pipeline**. Eller, om du redan har versions pipeliner, väljer du knappen **+ ny** och väljer **+ ny versions pipeline**.  

    ![Lägg till versions pipeline](./media/how-to-ci-cd/add-release-pipeline.png)

2. När du uppmanas att välja en mall väljer du att starta med ett **tomt jobb**.

    ![Starta med ett tomt jobb](./media/how-to-ci-cd/start-with-empty-job.png)

3. Din nya versions pipeline initieras med en fas, som kallas **steg 1**. Byt namn på steg 1 till **dev** och behandla den som en test miljö. Vanligt vis har kontinuerliga distributions pipeliner flera steg, inklusive **utveckling**, **mellanlagring** och **Prod**. Du kan skapa mer baserat på din DevOps-praxis. Stäng fönstret steg information när det har bytt namn.

4. Länka versionen till de versions artefakter som publiceras av bygg pipelinen. Klicka på **Lägg till** i artefakter.

   ![Lägg till artefakter](./media/how-to-ci-cd/add-artifacts.png)  

5. På **sidan Lägg till en artefakt**väljer **du typ av**källtyp. Välj sedan projektet och den build-pipeline som du skapade. Välj sedan **Lägg till**.

   ![Lägg till en bygg-artefakt](./media/how-to-ci-cd/add-an-artifact.png)

6. Öppna artefakt utlösare och välj växla för att aktivera den kontinuerliga distributions utlösaren. Nu skapas en ny version varje gång en ny version är tillgänglig.

   ![Konfigurera utlösare för kontinuerlig distribution](./media/how-to-ci-cd/add-a-trigger.png)

7. **Dev** -fasen är förkonfigurerad med ett jobb och noll uppgifter. Från pipeline-menyn väljer du **uppgifter** och sedan **dev** -fasen.  Välj jobb-och aktivitets antal för att konfigurera uppgifterna i det här steget.

    ![Konfigurera dev-aktiviteter](./media/how-to-ci-cd/view-stage-tasks.png)

8. I **dev** -fasen bör du se ett standard **Agent jobb**. Du kan konfigurera information om Agent jobbet, men distributions aktiviteten är plattforms okänslig så att du kan använda antingen **värdbaserade VS2017** eller **värdbaserad Ubuntu 1604** i **agenten** (eller någon annan agent som hanteras av dig själv).

9. Välj plus tecknet ( **+** ) för att lägga till två aktiviteter. Sök efter och Lägg till **Azure IoT Edge** två gånger.

    ![Lägg till aktiviteter för dev](./media/how-to-ci-cd/add-task-qa.png)

10. Välj den första **Azure IoT Edge** aktiviteten och konfigurera den med följande värden:

    * **Visnings namn**: visnings namnet uppdateras automatiskt när åtgärds fältet ändras.
    * **Åtgärd**: Använd List rutan för att välja **generera distributions manifest**. Om du ändrar åtgärd svärdet uppdateras även uppgifts visnings namnet så att det matchar.
    * **.template.jspå fil**: Lägg till sökvägen `$(System.DefaultWorkingDirectory)/Drop/drop/deployment.template.json` . Sökvägen publiceras från build-pipeline.
    * **Standard plattform**: Välj samma värde när du skapar module-avbildningarna.
    * **Sökväg för utdata**: Lägg till sökvägen `$(System.DefaultWorkingDirectory)/Drop/drop/configs/deployment.json` . Den här sökvägen är den slutliga IoT Edge distributions manifest filen.

    De här konfigurationerna ersätter modulens bild-URL: er i `deployment.template.json` filen. Med **generera distributions manifestet** kan du också ersätta variablerna med det exakta värdet som du definierade i `deployment.template.json` filen. I VS/VS Code anger du det faktiska värdet i en `.env` fil. I Azure-pipeliner ställer du in värdet på fliken släpp pipeline-variabler. Flytta till fliken variabler och konfigurera namnet och värdet enligt följande.

    * **ACR_ADDRESS**: din Azure Container Registry adress.
    * **ACR_PASSWORD**: ditt Azure Container Registry lösen ord.
    * **ACR_USER**: ditt Azure Container Registry användar namn.

    Om du har andra variabler i projektet kan du ange namn och värde i den här fliken. Det **genererade distributions manifestet** kan bara identifiera variablerna som är i `${VARIABLE}` smak, se till att du använder dem i dina `*.template.json` filer.

    ![Konfigurera variabler för versions pipeline](./media/how-to-ci-cd/configure-variables.png)

11. Välj den andra **Azure IoT Edge** aktiviteten och konfigurera den med följande värden:

    * **Visnings namn**: visnings namnet uppdateras automatiskt när åtgärds fältet ändras.
    * **Åtgärd**: Använd List rutan för att välja **distribuera till IoT Edge enheter**. Om du ändrar åtgärd svärdet uppdateras även uppgifts visnings namnet så att det matchar.
    * **Azure-prenumeration**: Välj den prenumeration som innehåller IoT Hub.
    * **IoT Hub namn**: Välj din IoT Hub.
    * **Välj en/flera enhet**: Välj om du vill att versions pipelinen ska distribueras till en enhet eller flera enheter.
      * Om du distribuerar till en enda enhet anger du **IoT Edge enhets-ID**.
      * Om du distribuerar till flera enheter anger du enhetens **mål villkor**. Mål villkoret är ett filter som matchar en uppsättning IoT Edge enheter i IoT Hub. Om du vill använda enhets koder som villkor måste du uppdatera motsvarande enhets koder med IoT Hub enhets-till-enhet. Uppdatera **IoT Edge distributions-ID** och **IoT Edge distributions prioritet** i de avancerade inställningarna. Mer information om hur du skapar en distribution för flera enheter finns i [förstå IoT Edge automatiska distributioner](module-deployment-monitoring.md).
    * Expandera avancerade inställningar, Välj **IoT Edge distributions-ID**, och Lägg variabeln `$(System.TeamProject)-$(Release.EnvironmentName)` . Detta mappar projektet och versions namnet med ditt IoT Edge-distributions-ID.

>[!NOTE]
>Om du vill använda **lager distributioner** i pipelinen stöds inte lager distributioner ännu i Azure IoT Edge uppgifter i Azure dataDevOpss.
>
>Du kan dock använda en [Azure CLI-uppgift i Azure DevOps](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-cli) för att skapa distributionen som en lager distribution. För det **infogade skript** svärdet kan du använda [kommandot AZ IoT Edge Deployment Create](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment):
>
>   ```azurecli-interactive
>   az iot edge deployment create -d {deployment_name} -n {hub_name} --content modules_content.json --layered true
>   ```

12. Välj **Spara** för att spara ändringarna i den nya versions pipelinen. Gå tillbaka till vyn pipeline genom att välja **pipeline** på menyn.

## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>Verifiera IoT Edge CI/CD med pipelinen build och release

Om du vill utlösa ett build-jobb kan du antingen skicka ett genomförande till käll kods databasen eller aktivera det manuellt. I det här avsnittet utlöser du CI/CD-pipeline manuellt för att testa att den fungerar. Kontrol lera sedan att distributionen lyckades.

1. Navigera till den pipeline för build som du skapade i början av den här artikeln.

2. Du kan utlösa ett build-jobb i din build-pipeline genom att välja knappen **kö** som i följande skärm bild.

    ![Manuell utlösare](./media/how-to-ci-cd/manual-trigger.png)

3. Välj build-jobbet för att se hur det fortskrider. Om build-pipeline har slutförts utlöses en version till **dev** -fasen.

    ![Build-loggar](./media/how-to-ci-cd/build-logs.png)

4. Den framgångs rik **dev** -versionen skapar IoT Edge distribution till mål IoT Edge enheter.

    ![Släpp till dev](./media/how-to-ci-cd/pending-approval.png)

5. Klicka på **dev** Stage för att se versions loggarna.

    ![Lanseringsloggar](./media/how-to-ci-cd/release-logs.png)

## <a name="next-steps"></a>Nästa steg

* IoT Edge DevOps Best Practices-exempel i [Azure DevOps-projekt för IoT Edge](how-to-devops-project.md)
* Förstå IoT Edge distribution i [förstå IoT Edge distributioner för enskilda enheter eller i stor skala](module-deployment-monitoring.md)
* Gå igenom stegen för att skapa, uppdatera eller ta bort en distribution i [distribuera och övervaka IoT Edge moduler i stor skala](how-to-deploy-at-scale.md).
