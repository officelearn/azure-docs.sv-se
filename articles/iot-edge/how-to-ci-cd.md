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
ms.openlocfilehash: 659a6f5acaac848084ed1e9590a414191542b54a
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2019
ms.locfileid: "68414627"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge"></a>Kontinuerlig integrering och kontinuerlig distribution till Azure IoT Edge

Du kan enkelt införa DevOps med dina Azure IoT Edge-program med de inbyggda Azure IoT Edge uppgifterna i Azure-pipelines. Den här artikeln visar hur du kan använda funktionerna för kontinuerlig integrering och kontinuerlig distribution i Azure pipelines för att bygga, testa och distribuera program snabbt och effektivt till din Azure IoT Edge. 

I den här artikeln får du lära dig hur du använder de inbyggda Azure IoT Edge uppgifterna för Azure pipelines för att skapa två pipeliner för din IoT Edge-lösning. Det första tar din kod och skapar lösningen, och skickar sedan modulens avbildningar till behållar registret och skapar ett distributions manifest. Den andra distribuerar dina moduler till riktade IoT Edge enheter.  

![Diagram - CI och CD grenar för utveckling och produktion](./media/how-to-ci-cd/cd.png)


## <a name="prerequisites"></a>Förutsättningar

* En Azure databaser-lagringsplats. Om du inte har någon kan du [skapa en ny git-lagrings platsen i projektet](https://docs.microsoft.com/azure/devops/repos/git/create-new-repo?view=vsts&tabs=new-nav).
* En IoT Edge lösning har allokerats och skickas till din lagrings plats. Om du vill skapa en ny exempel lösning för att testa den här artikeln följer du stegen i [utveckla och felsöka moduler i Visual Studio Code](how-to-vs-code-develop-module.md) eller [utveckla och felsöka C# moduler i Visual Studio](how-to-visual-studio-develop-csharp-module.md).
   * Allt du behöver i den här artikeln är mappen Solution som skapats av IoT Edge mallar i antingen Visual Studio Code eller Visual Studio. Du behöver inte bygga, skicka, distribuera eller Felsöka den här koden innan du fortsätter. Du ställer in dessa processer i Azure-pipeliner. 
   * Om du skapar en ny lösning ska du klona din lagrings plats lokalt först. När du sedan skapar lösningen kan du välja att skapa den direkt i databasmappen. Du kan enkelt bekräfta och skicka de nya filerna därifrån. 
* Ett behållar register där du kan push-modul avbildningar. Du kan använda [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) eller ett register från en tredje part. 
* En aktiv [IoT-hubb](../iot-hub/iot-hub-create-through-portal.md) med minst IoT Edge enheter för att testa de separata distributions faserna för test och produktion. Du kan följa snabb starts artiklarna för att skapa en IoT Edge-enhet i [Linux](quickstart-linux.md) eller [Windows](quickstart.md)


Mer information om hur du använder Azure-databaser finns i [dela din kod med Visual Studio och Azure databaser](https://docs.microsoft.com/azure/devops/repos/git/share-your-code-in-git-vs?view=vsts)

## <a name="configure-continuous-integration"></a>Konfigurera kontinuerlig integrering
I det här avsnittet skapar du en ny versions pipeline. Konfigurera pipelinen så att den körs automatiskt när du checkar in eventuella ändringar i exempel IoT Edge lösning och publicera build-loggar.

>[!NOTE]
>I den här artikeln används den visuella designern för Azure DevOps. Innan du följer stegen i det här avsnittet stänger du av förhands gransknings funktionen för den nya YAML-upplevelsen för pipeline. 
>1. I Azure DevOps väljer du din profil ikon och väljer sedan för **hands versions funktioner**.
>2. Sätt igång **nya yaml-pipeline** för att skapa pipelinen. 
>
>Mer information finns i [skapa en pipeline](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav#create-a-build-pipeline)för bygge.

1. Logga in på din Azure DevOps-organisation (**https:\//dev.Azure.com/{Your Organization}/** ) och öppna projektet som innehåller lagrings platsen för din IoT Edge lösning.

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
    
   * Om du vill bygga dina moduler i plattformen amd64 för Linux-behållare kan du välja **finns Ubuntu 1604**

   * Om du vill skapa moduler i plattforms-amd64 för Windows 1809-behållare måste du konfigurera en lokal [agent i Windows](https://docs.microsoft.com/azure/devops/pipelines/agents/v2-windows?view=vsts).

   * Om du vill skapa moduler i plattforms-arm32v7 eller arm64 för Linux-behållare måste du konfigurera en lokal [agent på Linux](https://blogs.msdn.microsoft.com/iotdev/2018/11/13/setup-azure-iot-edge-ci-cd-pipeline-with-arm-agent/).
    
     ![Konfigurera build-agentpoolen](./media/how-to-ci-cd/configure-env.png)

5. Din pipeline är förkonfigurerad med ett jobb som kallas **Agent jobb 1**. Välj plus tecknet ( **+** ) för att lägga till tre aktiviteter i jobbet: **Azure IoT Edge** två gånger och **publicera Bygg artefakter** en gång. (Hovra över namnet på varje uppgift för att se knappen **Lägg till** .)

   ![Lägg till Azure IoT Edge uppgift](./media/how-to-ci-cd/add-iot-edge-task.png)

   När alla tre aktiviteter läggs till ser ditt Agent jobb ut som i följande exempel:
    
   ![Tre uppgifter i build-pipeline](./media/how-to-ci-cd/add-tasks.png)

6. Välj den första **Azure IoT Edge** uppgiften för att redigera den. Den här uppgiften skapar alla moduler i lösningen med den mål plattform som du anger, den genererar också filen **Deployment. JSON** som talar om för IoT Edge enheter hur distributionen ska konfigureras.

   * **Visnings namn**: Godkänn standard avbildningarna för **Azure IoT Edge-build-modulen**.
   * **Åtgärd**: Acceptera standard avbildningarna för **build-modulen**. 
   * **. mall. JSON-fil**: Välj ellipsen ( **...** ) och navigera till filen **Deployment. template. JSON** i lagrings platsen som innehåller din IoT Edge lösning. 
   * **Standard plattform**: Välj lämplig plattform för dina moduler baserat på din mål IoT Edge enhet. 
   * **Utdata-variabler**: Variablerna för utdata innehåller ett referens namn som du kan använda för att konfigurera fil Sök vägen där din Deployment. JSON-fil kommer att skapas. Ange referens namnet till något som du kan komma ihåg som **Edge**. 

7. Välj den andra **Azure IoT Edge** uppgiften för att redigera den. Den här uppgiften push-överför alla modulblad till det behållar register som du väljer. Den lägger också till dina autentiseringsuppgifter för behållar registret i filen **Deployment. JSON** så att din IoT Edge-enhet kan komma åt modul avbildningarna. 

   * **Visnings namn**: Visnings namnet uppdateras automatiskt när åtgärds fältet ändras. 
   * **Åtgärd**: Använd List rutan för att välja **push module**-avbildningar. 
   * **Behållarens register typ**: Välj den typ av behållar register som du använder för att lagra dina modulblad. Formuläret ändras beroende på vilken register typ du väljer. Om du väljer **Azure Container Registry**använder du List rutan för att välja Azure-prenumerationen och namnet på behållar registret. Om du väljer **allmän container Registry**väljer du **ny** för att skapa en anslutning till en register tjänst. 
   * **. mall. JSON-fil**: Välj ellipsen ( **...** ) och navigera till filen **Deployment. template. JSON** i lagrings platsen som innehåller din IoT Edge lösning. 
   * **Standard plattform**: Välj samma plattform som de inbyggda modul avbildningarna.

   Om du har flera behållarregister som värd för dina modul-avbildningar, måste du duplicera den här uppgiften, Välj olika behållarregister och använda **kringgå modulen eller modulerna** i avancerade inställningar för att kringgå de avbildningar som inte är för detta specifika register.

8. Välj aktiviteten **publicera Bygg artefakter** för att redigera den. Ange fil Sök vägen till den distributions fil som genererats av bygg uppgiften. Ange **sökvägen till publicering** svärdet för att matcha den utgående variabel som du anger i aktiviteten skapa modul. Till exempel `$(edge.DEPLOYMENT_FILE_PATH)`. Lämna de andra värdena som standardvärden. 

9. Öppna fliken  utlösare och markera kryss rutan för att **aktivera kontinuerlig integrering**. Se till att grenen som innehåller koden ingår.

    ![Aktivera utlösaren för kontinuerlig integrering](./media/how-to-ci-cd/configure-trigger.png)

10. Spara den nya bygg pipelinen med knappen **Spara** .

Den här pipelinen har nu kon figurer ATS för att köras automatiskt när du push-överför ny kod till din lagrings platsen. Den senaste aktiviteten, publicering av pipeline-artefakter, utlöser en versions pipeline. Fortsätt till nästa avsnitt för att bygga lanserings pipelinen. 

## <a name="configure-continuous-deployment"></a>Konfigurera kontinuerlig distribution
I det här avsnittet skapar du en versions pipeline som är konfigurerad för att köras automatiskt när din Bygg pipeline tappar ut artefakter och det visar distributions loggar i Azure-pipelines.

I det här avsnittet skapar du två olika steg, en för test distributioner och en för produktions distributioner. 

### <a name="create-test-stage"></a>Skapa test fas

Skapa en ny pipeline och konfigurera dess första Stadium för distributioner av kvalitets säkrings frågor (frågor och svar). 

1. I den **versioner** fliken **+ ny pipeline**. Eller, om du redan har versions pipeliner, väljer du knappen **+ ny** och väljer **+ ny versions pipeline**.  

    ![Lägg till releasepipeline](./media/how-to-ci-cd/add-release-pipeline.png)

2. När du uppmanas att välja en mall väljer du att starta med ett **tomt jobb**.

    ![Börja med en tom jobb](./media/how-to-ci-cd/start-with-empty-job.png)

3. Din nya versions pipeline initieras med en fas, som kallas **steg 1**. Byt namn på steg 1 till **frågor och svar** och behandla den som en test miljö. I allmänhet har kontinuerliga distributions pipeliner flera steg. Du kan skapa mer baserat på din DevOps-praxis. Stäng fönstret steg information när det har bytt namn. 

    ![Skapa test-miljö steg](./media/how-to-ci-cd/QA-env.png)

4. Länka versionen till de versions artefakter som publiceras av bygg pipelinen. Klicka på **Lägg till** i artefakter område.

   ![Lägg till artefakter](./media/how-to-ci-cd/add-artifacts.png)  
    
5. På **sidan Lägg till en artefakt**väljer **du typ av**källtyp. Välj sedan projektet och den build-pipeline som du skapade. Välj sedan **Lägg till**.

   ![Lägg till en byggesartefakt](./media/how-to-ci-cd/add-an-artifact.png)

6. Öppna artefakt utlösare och välj växla för att aktivera den kontinuerliga distributions utlösaren. Nu skapas en ny version varje gång en ny version är tillgänglig.

   ![Konfigurera utlösare av kontinuerlig distribution](./media/how-to-ci-cd/add-a-trigger.png)

7. Fasen **frågor och svar** är förkonfigurerad med ett jobb och noll uppgifter. Från pipeline-menyn väljer du **aktiviteter** och sedan frågor och **svar** -fasen.  Välj jobb-och aktivitets antal för att konfigurera uppgifterna i det här steget.

    ![Konfigurera QA uppgifter](./media/how-to-ci-cd/view-stage-tasks.png)

8. I frågor och svar-fasen bör du se ett standard **Agent jobb**. Du kan konfigurera information om Agent jobbet, men distributions aktiviteten är plattforms okänslig så att du kan använda antingen **värdbaserade VS2017** eller **värdbaserad Ubuntu 1604** i **agenten** (eller någon annan agent som hanteras av dig själv). 

9. Välj plus tecknet ( **+** ) för att lägga till en aktivitet. Sök efter och Lägg till **Azure IoT Edge**. 

    ![Lägg till aktiviteter för QA](./media/how-to-ci-cd/add-task-qa.png)

10. Välj aktiviteten ny Azure IoT Edge och konfigurera den med följande värden:

    * **Visnings namn**: Visnings namnet uppdateras automatiskt när åtgärds fältet ändras. 
    * **Åtgärd**: Använd List rutan för att välja **distribuera till IoT Edge enheten**. Om du ändrar åtgärd svärdet uppdateras även uppgifts visnings namnet så att det matchar.
    * **Azure-prenumeration**: Välj den prenumeration som innehåller din IoT Hub.
    * **IoT Hub namn**: Välj din IoT-hubb. 
    * **Välj en/flera enheter**: Välj om du vill att versions pipelinen ska distribueras till en enhet eller flera enheter. 
      * Om du distribuerar till en enda enhet anger du **IoT Edge enhets-ID**. 
      * Om du distribuerar till flera enheter anger du enhetens **mål villkor**. Målvillkoret har ett filter för att matcha en uppsättning Edge-enheter i IoT Hub. Om du vill använda taggar för enheten som villkoret måste du uppdatera dina motsvarande enheter taggar med enhetstvillingen i IoT Hub. Uppdatera **IoT Edge distributions-ID** och **IoT Edge distributions prioritet** i de avancerade inställningarna. Mer information om hur du skapar en distribution för flera enheter finns i [förstå IoT Edge automatiska distributioner](module-deployment-monitoring.md).

11. Välj **Spara** för att spara ändringarna i den nya versions pipelinen. Gå tillbaka till vyn pipeline genom att välja **pipeline** på menyn. 

### <a name="create-production-stage"></a>Skapa produktions Stadium

Skapa ett andra steg i din versions pipeline för produktions distribution. 

1. Gör ett andra Stadium för produktion genom att klona frågor och svar-fasen. Håll markören över frågor och svar-fasen och välj sedan knappen klona. 

    ![Klona steg](./media/how-to-ci-cd/clone-stage.png)

2. Välj den nya fasen, kallas **kopia av frågor och svar**, för att öppna dess egenskaper. Ändra scen namnet till **Prod**, för produktion. Stäng fönstret Egenskaper för fas. 

3. Öppna uppgifterna i produktions stadiet genom att välja **uppgifter** från pipeline-menyn och sedan välja **produktions** Stadium. 

4. Välj Azure IoT Edge uppgift för att konfigurera om för produktions miljön. Distributions inställningarna är förmodligen samma för frågor och svar och för produktion, förutom att du vill rikta in sig på en annan enhet eller uppsättning enheter i produktionen. Uppdatera fältet enhets-ID eller fälten mål villkor och distributions-ID för dina produktions enheter. 

5. Spara den med knappen **Spara** . Och välj sedan **pipeline** för att gå tillbaka till vyn pipeline.
    
6. Sättet den här versionen av pipelinen är konfigurerad för tillfället, och bygg artefakten utlöser frågor  och **svars** skedet, varje gång en ny version slutförs. Du vill dock vanligt vis integrera vissa test fall på frågor och svar och manuellt godkänna distributionen för produktion. Använd följande steg för att skapa ett godkännande villkor för produktions stadiet:

    1. Öppna panelen **för villkors inställningar för distribution** .

        ![Öppna före villkor](./media/how-to-ci-cd/pre-deploy-conditions.png)    

    2. Aktivera godkännande villkor för för **distribution** till aktive **rad**. Lägg till en eller flera användare eller grupper i fältet **god kännare** och anpassa eventuella andra godkännande principer som du vill använda. Om du vill spara ändringarna stänger du panelen villkor för för distribution.
    
       ![Ange villkor](./media/how-to-ci-cd/set-pre-deployment-conditions.png)


7. Spara din versions pipeline med knappen **Spara** . 

    
## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>Verifiera IoT Edge CI/CD med build and release-pipelines

Om du vill utlösa ett skapandejobb du skickar något till lagringsplatsen för källkod eller utlösa den manuellt. I det här avsnittet utlöser du CI/CD-pipeline manuellt för att testa att den fungerar. Kontrol lera sedan att distributionen lyckades.

1. Navigera till den pipeline för build som du skapade i början av den här artikeln. 

2. Du kan utlösa ett build-jobb i din build-pipeline genom att välja knappen **kö** som i följande skärm bild.

    ![Manuell utlösare](./media/how-to-ci-cd/manual-trigger.png)

3. Välj build-jobbet för att se hur det fortskrider. Om build-pipeline har slutförts utlöses en version till frågor och **svar** . 

    ![Skapa loggar](./media/how-to-ci-cd/build-logs.png)

4. En lyckad distribution till **frågor och svar** utlöser ett meddelande till god kännaren. Kontrol lera att modulerna har distribuerats på enheten eller enheterna som du är mål för i frågor och svars skedet. Gå sedan till versions pipeline och ge godkännande för versionen att gå till PROD-fasen genom att välja knappen **Prod** och sedan på **Godkänn**. 

    ![Väntar på godkännande](./media/how-to-ci-cd/pending-approval.png)

5. När godkännaren har godkänt den här ändringen, den kan bara distribueras till **PROD**.

## <a name="next-steps"></a>Nästa steg

* Förstå IoT Edge-distribution i [förstå IoT Edge-distributioner för enskilda enheter eller i stor skala](module-deployment-monitoring.md)
* Gå igenom stegen för att skapa, uppdatera eller ta bort en distribution i [distribuera och övervaka IoT Edge-moduler i stor skala](how-to-deploy-monitor.md).
