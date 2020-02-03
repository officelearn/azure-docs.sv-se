---
title: Distribuera ett Azure Stream Analytics jobb med CI/CD NPM-paket
description: Den här artikeln beskriver hur du använder Azure Stream Analytics CI/CD NPM-paket för att konfigurera en kontinuerlig integrering och distributions process.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: deb6c2439cc84f196b7f42fd9f49d3ebfd057cbb
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2020
ms.locfileid: "76962229"
---
# <a name="deploy-an-azure-stream-analytics-job-using-cicd-npm-package"></a>Distribuera ett Azure Stream Analytics jobb med CI/CD NPM-paket 

Du kan använda Azure Stream Analytics CI/CD NPM-paketet för att skapa en kontinuerlig integrerings-och distributions process för dina Stream Analytics jobb. Den här artikeln beskriver hur du använder NPM-paketet i allmänhet med valfritt CI/CD-system, samt detaljerade instruktioner för distribution med Azure-pipeliner.

Mer information om hur du distribuerar med PowerShell finns i [distribuera med en Resource Manager-mallfil och Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). Du kan också lära dig mer om hur du [använder ett objekt som en parameter i en Resource Manager-mall](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters).

## <a name="build-the-vs-code-project"></a>Bygg VS Code-projektet

Du kan aktivera kontinuerlig integrering och distribution för Azure Stream Analytics jobb med hjälp av NPM **-paketet ASA-streamanalytics-cicd** . NPM-paketet innehåller verktyg för att skapa Azure Resource Manager mallar för [Stream Analytics Visual Studio Code-projekt](quick-create-vs-code.md). Den kan användas i Windows, macOS och Linux utan att installera Visual Studio Code.

Du kan [Ladda ned paketet](https://www.npmjs.com/package/azure-streamanalytics-cicd) direkt eller installera det [globalt](https://docs.npmjs.com/downloading-and-installing-packages-globally) via `npm install -g azure-streamanalytics-cicd` kommandot. Detta är den rekommenderade metoden, som också kan användas i en PowerShell-eller Azure CLI-skript för en build-pipeline i **Azure-pipelines**.

När du har installerat paketet använder du följande kommando för att skriva ut Azure Resource Manager-mallarna. Argumentet **scriptPath** är den absoluta sökvägen till **asaql** -filen i projektet. Kontrol lera att filerna asaproj. JSON och JobConfig. JSON finns i samma mapp som skript filen. Om **outputPath** inte har angetts placeras mallarna i mappen **Deploy** i mappen **bin** för projektet.

```powershell
azure-streamanalytics-cicd build -scriptPath <scriptFullPath> -outputPath <outputPath>
```
Exempel (på macOS)
```powershell
azure-streamanalytics-cicd build -scriptPath "/Users/roger/projects/samplejob/script.asaql" 
```

När ett Stream Analytics Visual Studio Code-projekt skapas, genererar det följande två Azure Resource Manager mallfiler under mappen **bin/[debug/återförsäljarversion]/Deploy** : 

*  Resource Manager-mallfil

       [ProjectName].JobTemplate.json 

*  Parameter fil för Resource Manager

       [ProjectName].JobTemplate.parameters.json   

Standard parametrarna i filen Parameters. JSON är från inställningarna i ditt Visual Studio Code-projekt. Om du vill distribuera till en annan miljö ersätter du parametrarna på motsvarande sätt.

> [!NOTE]
> Standardvärdena anges till null för alla autentiseringsuppgifter. Du **måste** ange värdena innan du distribuerar till molnet.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```

## <a name="deploy-with-azure-pipelines"></a>Distribuera med Azure-pipeline

I det här avsnittet beskrivs hur du skapar en pipeline för att [bygga](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav) och [lansera](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts) en Azure-pipeline med hjälp av NPM.

Öppna en webbläsare och gå till ditt Azure Stream Analytics Visual Studio Code-projekt.

1. Under **pipeliner** i den vänstra navigerings menyn väljer du **versioner**. Välj sedan **ny pipeline**

   ![Skapa ny Azure-pipeline](./media/setup-cicd-vs-code/new-pipeline.png)

2. Välj **Använd den klassiska redigeraren** för att skapa en pipeline utan yaml.

3. Välj käll typ, team projekt och lagrings plats. Välj sedan **Fortsätt**.

   ![Välj Azure Stream Analytics projekt](./media/setup-cicd-vs-code/select-repo.png)

4. På sidan **Välj en mall** väljer du **tomt jobb**.

### <a name="add-npm-task"></a>Lägg till NPM-uppgift

1. På sidan **aktiviteter** väljer du plus tecknet bredvid **Agent jobb 1**. Ange "NPM" i uppgifts sökningen och välj **NPM**.

   ![Välj NPM-aktivitet](./media/setup-cicd-vs-code/search-npm.png)

2. Ge uppgiften ett **visnings namn**. Ändra **kommando** alternativet till *Custom* och ange följande kommando i **kommando och argument**. Lämna kvar de återstående standard alternativen.

   ```cmd
   install -g azure-streamanalytics-cicd
   ```

   ![Ange konfigurationer för NPM-aktivitet](./media/setup-cicd-vs-code/npm-config.png)

### <a name="add-command-line-task"></a>Lägg till kommando rads aktivitet

1. På sidan **aktiviteter** väljer du plus tecknet bredvid **Agent jobb 1**. Sök efter **kommando rad**.

2. Ge uppgiften ett **visnings namn** och ange följande skript. Ändra skriptet med ditt databas namn och projekt namn.

   ```cmd
   azure-streamanalytics-cicd build -scriptPath $(Build.SourcesDirectory)/myASAProject/myASAProj.asaql
   ```

   ![Ange konfigurationer för kommando rads aktivitet](./media/setup-cicd-vs-code/commandline-config.png)

### <a name="add-copy-files-task"></a>Lägg till aktiviteten Kopiera filer

1. På sidan **aktiviteter** väljer du plus tecknet bredvid **Agent jobb 1**. Sök efter **Kopiera filer**. Ange sedan följande konfigurationer.

   |Parameter|Indata|
   |-|-|
   |Visningsnamn|Kopiera filer till: $ (build. artifactstagingdirectory)|
   |Källmapp|`$(system.defaultworkingdirectory)`| 
   |Innehåll| `**\Deploy\**` |
   |Målmapp| `$(build.artifactstagingdirectory)`|

   ![Ange konfigurationer för kopierings uppgift](./media/setup-cicd-vs-code/copy-config.png)

### <a name="add-publish-build-artifacts-task"></a>Lägg till aktiviteten publicera Bygg artefakter

1. På sidan **aktiviteter** väljer du plus tecknet bredvid **Agent jobb 1**. Sök efter **publicera versions artefakter** och välj alternativet med den svarta pilens ikon. 

2. Ändra inte standard konfigurationerna.

### <a name="save-and-run"></a>Spara och kör

När du har lagt till åtgärderna NPM, kommando rad, kopiera filer och publicera Bygg artefakter väljer du **spara & kö**. När du uppmanas till det anger du en Spara kommentar och väljer **Spara och kör**.

## <a name="release-with-azure-pipelines"></a>Version med Azure-pipeline

Öppna en webbläsare och gå till ditt Azure Stream Analytics Visual Studio Code-projekt.

1. Under **pipeliner** i den vänstra navigerings menyn väljer du **versioner**. Välj sedan **ny pipeline**.

2. Välj **starta med ett tomt jobb**.

3. I rutan **artefakter** väljer du **+ Lägg till en artefakt**. Under **källa**väljer du den pipeline för build som du nyss skapade och väljer **Lägg till**.

   ![Ange artefakt för bygg pipeline](./media/setup-cicd-vs-code/build-artifact.png)

4. Ändra namnet på **steg 1** för att **distribuera jobb till test miljön**.

5. Lägg till ett nytt stadium och namnge det **för att distribuera jobb till produktions miljön**.

### <a name="add-tasks"></a>Lägg till aktiviteter

1. I list rutan aktiviteter väljer **du distribuera jobb för att testa miljön**. 

2. Välj **+** bredvid **Agent jobb** och Sök efter *Azure Resource Group-distribution*. Ange följande parametrar:

   |Inställning|Värde|
   |-|-|
   |Visningsnamn| *Distribuera myASAJob*|
   |Azure-prenumeration| Välj din prenumeration.|
   |Åtgärd| *Skapa eller uppdatera resurs grupp*|
   |Resursgrupp| Välj ett namn för den test resurs grupp som ska innehålla ditt Stream Analytics jobb.|
   |Location|Välj platsen för test resurs gruppen.|
   |Mallens plats| *Länkad artefakt*|
   |Mall| $ (Build. ArtifactStagingDirectory) \drop\myASAJob.JobTemplate.json |
   |Mallparametrar|($ (Build. ArtifactStagingDirectory) \drop\myASAJob.JobTemplate.parameters.json|
   |Åsidosätt mallparametrar|– Input_IoTHub1_iotHubNamespace $ (test_eventhubname)|
   |Distributions läge|Inkrementell|

3. I list rutan aktiviteter väljer **du distribuera jobb till produktions miljön**.

4. Välj **+** bredvid **Agent jobb** och Sök efter *Azure Resource Group-distribution*. Ange följande parametrar:

   |Inställning|Värde|
   |-|-|
   |Visningsnamn| *Distribuera myASAJob*|
   |Azure-prenumeration| Välj din prenumeration.|
   |Åtgärd| *Skapa eller uppdatera resurs grupp*|
   |Resursgrupp| Välj ett namn för den produktions resurs grupp som ska innehålla ditt Stream Analytics jobb.|
   |Location|Välj platsen för produktions resurs gruppen.|
   |Mallens plats| *Länkad artefakt*|
   |Mall| $ (Build. ArtifactStagingDirectory) \drop\myASAJob.JobTemplate.json |
   |Mallparametrar|($ (Build. ArtifactStagingDirectory) \drop\myASAJob.JobTemplate.parameters.json|
   |Åsidosätt mallparametrar|-Input_IoTHub1_iotHubNamespace $ (eventhubname)|
   |Distributions läge|Inkrementell|

### <a name="create-release"></a>Skapa version

Om du vill skapa en version väljer du **Skapa version** i det övre högra hörnet.

![Skapa en version med hjälp av Azure-pipeliner](./media/setup-cicd-vs-code/create-release.png)

## <a name="additional-resources"></a>Ytterligare resurser

Om du vill använda hanterad identitet för Azure Data Lake Store gen1 som utgående mottagare måste du ge åtkomst till tjänstens huvud namn med hjälp av PowerShell innan du distribuerar till Azure. Lär dig mer om hur du [distribuerar ADLS gen1 med hanterad identitet med Resource Manager-mall](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment).


## <a name="next-steps"></a>Nästa steg

* [Snabb start: skapa ett Azure Stream Analytics moln jobb i Visual Studio Code (för hands version)](quick-create-vs-code.md)
* [Testa Stream Analytics frågor lokalt med Visual Studio Code (för hands version)](visual-studio-code-local-run.md)
* [Utforska Azure Stream Analytics med Visual Studio Code (för hands version)](visual-studio-code-explore-jobs.md)
