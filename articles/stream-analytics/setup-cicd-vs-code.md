---
title: Distribuera ett Azure Stream Analytics-jobb med CI/CD npm-paketet
description: I den här artikeln beskrivs hur du använder Azure Stream Analytics CI/CD npm-paket för att konfigurera en kontinuerlig integrerings- och distributionsprocess.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: deb6c2439cc84f196b7f42fd9f49d3ebfd057cbb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76962229"
---
# <a name="deploy-an-azure-stream-analytics-job-using-cicd-npm-package"></a>Distribuera ett Azure Stream Analytics-jobb med CI/CD npm-paketet 

Du kan använda Azure Stream Analytics CI/CD npm-paketet för att konfigurera en kontinuerlig integrerings- och distributionsprocess för dina Stream Analytics-jobb. I den här artikeln beskrivs hur du använder npm-paketet i allmänhet med alla CI/CD-system, samt specifika instruktioner för distribution med Azure Pipelines.

Mer information om hur du distribuerar med Powershell finns i [distribuera med en Resource Manager-mallfil och Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). Du kan också läsa mer om hur du [använder ett objekt som en parameter i en Resource Manager-mall](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters).

## <a name="build-the-vs-code-project"></a>Skapa VS-kodprojektet

Du kan aktivera kontinuerlig integrering och distribution för Azure Stream **Analytics-jobb med asa-streamanalytics-cicd npm-paketet.** Npm-paketet innehåller verktygen för att generera Azure Resource Manager-mallar för [Stream Analytics Visual Studio Code-projekt](quick-create-vs-code.md). Den kan användas på Windows, macOS och Linux utan att installera Visual Studio Code.

Du kan [ladda ner paketet](https://www.npmjs.com/package/azure-streamanalytics-cicd) direkt eller installera det [globalt](https://docs.npmjs.com/downloading-and-installing-packages-globally) via kommandot. `npm install -g azure-streamanalytics-cicd` Det här är den rekommenderade metoden, som också kan användas i en PowerShell- eller Azure CLI-skriptuppgift för en build pipeline i **Azure Pipelines**.

När du har installerat paketet använder du följande kommando för att mata ut Azure Resource Manager-mallarna. Argumentet **scriptPath** är den absoluta sökvägen till **asaql-filen** i projektet. Kontrollera att filerna asaproj.json och JobConfig.json finns i samma mapp med skriptfilen. Om **utdatapath** inte har angetts placeras mallarna i mappen **Distribuera** under projektets **lagerplatsmapp.**

```powershell
azure-streamanalytics-cicd build -scriptPath <scriptFullPath> -outputPath <outputPath>
```
Exempel (på macOS)
```powershell
azure-streamanalytics-cicd build -scriptPath "/Users/roger/projects/samplejob/script.asaql" 
```

När ett Stream Analytics Visual Studio-kodprojekt har skapats genereras följande två Azure Resource Manager-mallfiler under mappen **bin/[Debug/Retail]/Deploy:** 

*  Mallfil för Resource Manager

       [ProjectName].JobTemplate.json 

*  Parameterfil för Resource Manager

       [ProjectName].JobTemplate.parameters.json   

Standardparametrarna i filen parameters.json kommer från inställningarna i Visual Studio-kodprojektet. Om du vill distribuera till en annan miljö ersätter du parametrarna därefter.

> [!NOTE]
> För alla autentiseringsuppgifter är standardvärdena inställda på null. Du **måste** ange värdena innan du distribuerar till molnet.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```

## <a name="deploy-with-azure-pipelines"></a>Distribuera med Azure Pipelines

I det här avsnittet beskrivs hur du skapar Azure [Pipelines-bygg-](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav) och [utgivningspipelor](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts) med npm.

Öppna en webbläsare och navigera till ditt Azure Stream Analytics Visual Studio-kodprojekt.

1. Under **Pipelines** i den vänstra **navigeringsmenyn**väljer du Byggningar . Välj sedan **Ny pipeline**

   ![Skapa ny Azure Pipeline](./media/setup-cicd-vs-code/new-pipeline.png)

2. Välj **Använd den klassiska redigeraren** för att skapa en pipeline utan YAML.

3. Välj källtyp, teamprojekt och databas. Välj sedan **Fortsätt**.

   ![Välj Azure Stream Analytics-projekt](./media/setup-cicd-vs-code/select-repo.png)

4. På sidan **Välj en mall** väljer du Tomt **jobb**.

### <a name="add-npm-task"></a>Lägga till npm-uppgift

1. På sidan **Uppgifter** väljer du plustecknet bredvid **Agentjobb 1**. Skriv "npm" i aktivitetssökningen och välj **npm**.

   ![Välj npm-uppgift](./media/setup-cicd-vs-code/search-npm.png)

2. Ge uppgiften ett **visningsnamn**. Ändra **kommandot** alternativet till *anpassade* och ange följande kommando i **Kommando och argument**. Lämna de återstående standardalternativen.

   ```cmd
   install -g azure-streamanalytics-cicd
   ```

   ![Ange konfigurationer för npm-uppgift](./media/setup-cicd-vs-code/npm-config.png)

### <a name="add-command-line-task"></a>Lägga till kommandoradsuppgift

1. På sidan **Uppgifter** väljer du plustecknet bredvid **Agentjobb 1**. Sök efter **kommandorad**.

2. Ge uppgiften ett **visningsnamn** och ange följande skript. Ändra skriptet med databasnamnet och projektnamnet.

   ```cmd
   azure-streamanalytics-cicd build -scriptPath $(Build.SourcesDirectory)/myASAProject/myASAProj.asaql
   ```

   ![Ange konfigurationer för kommandoradsuppgift](./media/setup-cicd-vs-code/commandline-config.png)

### <a name="add-copy-files-task"></a>Lägg till uppgift om kopiera filer

1. På sidan **Uppgifter** väljer du plustecknet bredvid **Agentjobb 1**. Sök efter **kopiera filer**. Ange sedan följande konfigurationer.

   |Parameter|Indata|
   |-|-|
   |Visningsnamn|Kopiera filer till: $(build.artifactstagingdirectory)|
   |Källmapp|`$(system.defaultworkingdirectory)`| 
   |Innehåll| `**\Deploy\**` |
   |Målmapp| `$(build.artifactstagingdirectory)`|

   ![Ange konfigurationer för kopieringsuppgift](./media/setup-cicd-vs-code/copy-config.png)

### <a name="add-publish-build-artifacts-task"></a>Lägga till uppgift om publicera byggartefakter

1. På sidan **Uppgifter** väljer du plustecknet bredvid **Agentjobb 1**. Sök efter **Publicera byggartefakter** och välj alternativet med den svarta pilikonen. 

2. Ändra inte någon av standardkonfigurationerna.

### <a name="save-and-run"></a>Spara och kör

När du har lagt till npm, kommandorad, kopiera filer och publicera byggartefakter väljer du **Spara & kön**. När du uppmanas att ange en spara kommentar och välj **Spara och kör**.

## <a name="release-with-azure-pipelines"></a>Släpp med Azure Pipelines

Öppna en webbläsare och navigera till ditt Azure Stream Analytics Visual Studio-kodprojekt.

1. Under Pipelines i den vänstra **navigeringsmenyn** väljer du **Släpper**. Välj sedan **Ny pipeline**.

2. Välj **Börja med ett tomt jobb**.

3. I rutan **Artefakter** väljer du **+ Lägg till en artefakt**. Under **Källa**väljer du den byggpipeline som du just skapade och väljer **Lägg till**.

   ![Ange anlöpeld för byggpipeline](./media/setup-cicd-vs-code/build-artifact.png)

4. Ändra namnet på **steg 1** till **Distribuera jobb till testmiljö**.

5. Lägg till en ny fas och namnge den **Distribuera jobb i produktionsmiljön**.

### <a name="add-tasks"></a>Lägga till uppgifter

1. Välj **Distribuera jobb för att testa miljö**i listrutan uppgifter . 

2. Välj **+** bredvid **agentjobb** och sök efter *Azure-resursgruppsdistribution*. Ange följande parametrar:

   |Inställning|Värde|
   |-|-|
   |Visningsnamn| *Distribuera myASAJob*|
   |Azure-prenumeration| Välj din prenumeration.|
   |Åtgärd| *Skapa eller uppdatera resursgrupp*|
   |Resursgrupp| Välj ett namn för testresursgruppen som ska innehålla ditt Stream Analytics-jobb.|
   |Location|Välj plats för testresursgruppen.|
   |Mallens plats| *Länkad artefakt*|
   |Mall| $(Build.artifactStagingDirectory)\drop\myASAJob.JobTemplate.json |
   |Mallparametrar|($(Build.ArtifactStagingDirectory)\drop\myASAJob.JobTemplate.parameters.json|
   |Åsidosätt mallparametrar|-Input_IoTHub1_iotHubNamespace $(test_eventhubname)|
   |Driftsättningsläge|Inkrementellt|

3. Välj **Distribuera jobb till produktionsmiljö**i listrutan uppgifter .

4. Välj **+** bredvid **agentjobb** och sök efter *Azure-resursgruppsdistribution*. Ange följande parametrar:

   |Inställning|Värde|
   |-|-|
   |Visningsnamn| *Distribuera myASAJob*|
   |Azure-prenumeration| Välj din prenumeration.|
   |Åtgärd| *Skapa eller uppdatera resursgrupp*|
   |Resursgrupp| Välj ett namn för produktionsresursgruppen som ska innehålla ditt Stream Analytics-jobb.|
   |Location|Välj plats för produktionsresursgruppen.|
   |Mallens plats| *Länkad artefakt*|
   |Mall| $(Build.artifactStagingDirectory)\drop\myASAJob.JobTemplate.json |
   |Mallparametrar|($(Build.ArtifactStagingDirectory)\drop\myASAJob.JobTemplate.parameters.json|
   |Åsidosätt mallparametrar|-Input_IoTHub1_iotHubNamespace $(eventhubname)|
   |Driftsättningsläge|Inkrementellt|

### <a name="create-release"></a>Skapa version

Om du vill skapa en version väljer du **Skapa version** i det övre högra hörnet.

![Skapa en version med Azure Pipelines](./media/setup-cicd-vs-code/create-release.png)

## <a name="additional-resources"></a>Ytterligare resurser

Om du vill använda Hanterad identitet för Azure Data Lake Store Gen1 som utdatamottagare måste du ange åtkomst till tjänstens huvudnamn med PowerShell innan du distribuerar till Azure. Läs mer om hur [du distribuerar ADLS Gen1 med hanterad identitet med Resource Manager-mall](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment).


## <a name="next-steps"></a>Nästa steg

* [Snabbstart: Skapa ett Molnjobb för Azure Stream Analytics i Visual Studio-kod (förhandsversion)](quick-create-vs-code.md)
* [Testa Stream Analytics-frågor lokalt med Visual Studio-kod (förhandsversion)](visual-studio-code-local-run.md)
* [Utforska Azure Stream Analytics med Visual Studio-kod (förhandsversion)](visual-studio-code-explore-jobs.md)
