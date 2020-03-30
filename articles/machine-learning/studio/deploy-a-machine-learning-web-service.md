---
title: Distribuera en webbtjänst
titleSuffix: ML Studio (classic) - Azure
description: Konvertera ett utbildningsexperiment till ett förutsägelseexperiment, förbereda det för distribution och distribuera det sedan som en klassisk webbtjänst för Azure Machine Learning Studio (klassiskt).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 01/06/2017
ms.openlocfilehash: 6c81e50560de69f7702e852d4602680fde7f01f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218133"
---
# <a name="deploy-an-azure-machine-learning-studio-classic-web-service"></a>Distribuera en webbkameratjänst för Azure Machine Learning Studio (klassiskt)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Med Azure Machine Learning Studio (klassisk) kan du skapa och testa en förutsägande analytisk lösning. Sedan kan du distribuera lösningen som en webbtjänst.

Machine Learning Studio (klassiska) webbtjänster tillhandahåller ett gränssnitt mellan ett program och en Machine Learning Studio (klassisk) arbetsflödesbedömningsmodell. Ett externt program kan kommunicera med en Machine Learning Studio (klassisk) arbetsflödesbedömningsmodell i realtid. Ett anrop till en Machine Learning Studio -webbtjänst (klassisk) returnerar förutsägelseresultat till ett externt program. Om du vill göra ett anrop till en webbtjänst skickar du en API-nyckel som skapades när du distribuerade webbtjänsten. En Machine Learning Studio (klassisk) webbtjänst är baserad på REST, ett populärt arkitekturval för webbprogrammeringsprojekt.

Azure Machine Learning Studio (klassisk) har två typer av webbtjänster:

* RRS (Request-Response Service): En låg latens, mycket skalbar tjänst som gör en enda datapost.
* BES (Batch Execution Service): En asynkron tjänst som betygsätter en bunt dataposter.

Indata för BES är som de indata som används av RRS. Den största skillnaden är att BES läser ett block med poster från olika källor, till exempel Azure Blob Storage, Azure Table Storage, Azure SQL Database, HDInsight (hive-fråga) och HTTP-källor.

Från en hög nivå point-of-view distribuerar du din modell i tre steg:

* **[Skapa ett träningsexperiment]** – I Studio (klassiskt) kan du träna och testa en modell för prediktiv analys med hjälp av träningsdata som du tillhandahåller, med hjälp av en stor uppsättning inbyggda maskininlärningsalgoritmer.
* **[Konvertera den till ett förutsägelseexperiment]** – När din modell har tränats med befintliga data och du är redo att använda den för att få nya data förbereder och effektiviserar du experimentet för förutsägelser.
* **Distribuera** den som en **[ny webbtjänst]** eller en **[klassisk webbtjänst]** – När du distribuerar ditt förutsägelseexperiment som en Azure-webbtjänst kan användare skicka data till din modell och ta emot modellens förutsägelser.

## <a name="create-a-training-experiment"></a>Skapa ett utbildningsexperiment

För att träna en modell för prediktiv analys använder du Azure Machine Learning Studio (klassiskt) för att skapa ett utbildningsexperiment där du inkluderar olika moduler för att läsa in träningsdata, förbereda data efter behov, tillämpa maskininlärningsalgoritmer och utvärdera Resultat. Du kan iterera på ett experiment och prova olika maskininlärningsalgoritmer för att jämföra och utvärdera resultaten.

Processen att skapa och hantera utbildningsexperiment behandlas mer grundligt på andra håll. Mer information finns i dessa artiklar:

* [Skapa ett enkelt experiment i Azure Machine Learning Studio (klassiskt)](create-experiment.md)
* [Utveckla en prediktiv lösning med Azure Machine Learning Studio (klassisk)](tutorial-part1-credit-risk.md)
* [Importera dina träningsdata till Azure Machine Learning Studio (klassisk)](import-data.md)
* [Hantera experimentiterationer i Azure Machine Learning Studio (klassisk)](manage-experiment-iterations.md)

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Konvertera träningsexperimentet till ett förutsägelseexperiment

När du har tränat din modell är du redo att konvertera ditt träningsexperiment till ett förutsägelseexperiment för att få nya data.

Genom att konvertera till ett förutsägelseexperiment gör du din tränade modell redo att distribueras som en bedömningswebbtjänst. Användare av webbtjänsten kan skicka indata till din modell och din modell skickar tillbaka förutsägelseresultaten. När du konverterar till ett prediktivt experiment bör du tänka på hur du förväntar dig att din modell ska användas av andra.

Om du vill konvertera träningsexperimentet till ett förutsägelseexperiment klickar du på **Kör** längst ned på experimentarbetsytan, klickar på **Konfigurera webbtjänst**och väljer sedan **Förutsägande webbtjänst**.

![Konvertera till bedömningsexperiment](./media/publish-a-machine-learning-web-service/figure-1.png)

Mer information om hur du utför den här konverteringen finns i Så här förbereder du [din modell för distribution i Azure Machine Learning Studio (klassiskt).](convert-training-experiment-to-scoring-experiment.md)

I följande steg beskrivs distribution av ett förutsägelseexperiment som en ny webbtjänst. Du kan också distribuera experimentet som klassisk webbtjänst.

## <a name="deploy-it-as-a-new-web-service"></a>Distribuera den som en ny webbtjänst

Nu när förutsägelseexperimentet har förberetts kan du distribuera det som en ny (Resource Manager-baserad) Azure-webbtjänst. Med hjälp av webbtjänsten kan användare skicka data till din modell och modellen returnerar sina förutsägelser.

Om du vill distribuera förutsägelseexperimentet klickar du på **Kör** längst ned på experimentarbetsytan. När experimentet har körts klickar du på **Distribuera webbtjänst** och väljer **Distribuera webbtjänst [ny]**.  Distributionssidan för machine learning studio-portalen (klassisk) öppnas.

> [!NOTE] 
> Om du vill distribuera en ny webbtjänst måste du ha tillräcklig behörighet i prenumerationen som du distribuerar webbtjänsten till. Mer information finns i [Hantera en webbtjänst med hjälp av Portalen för Azure Machine Learning Web Services](manage-new-webservice.md). 

### <a name="web-service-portal-deploy-experiment-page"></a>Experimentsida för distribution av webbtjänstportal

Ange ett namn på webbtjänsten på sidan Distribuera experiment.
Välj en prisplan. Om du har en befintlig prisplan kan du välja den, annars måste du skapa en ny prisplan för tjänsten.

1. I listrutan **Prisplan** väljer du en befintlig plan eller väljer alternativet **Välj ny plan.**
2. Skriv ett namn som identifierar planen på fakturan i **Plannamn.**
3. Välj en av de **månatliga plannivåerna**. Planen får som standard nivåerna för planer i din standardregion och din webbtjänst distribueras till den regionen.

Klicka på **Distribuera** så öppnas **snabbstartssidan** för webbtjänsten.

På snabbstartssidan för webbtjänsten får du tillgång till och vägledning om de vanligaste uppgifterna du utför när du har skapat en webbtjänst. Härifrån kan du enkelt komma åt både testsidan och sidan Förbruka.

<!-- ![Deploy the web service](./media/publish-a-machine-learning-web-service/figure-2.png)-->

### <a name="test-your-new-web-service"></a>Testa din nya webbtjänst

Om du vill testa den nya webbtjänsten klickar du på **Testa webbtjänst** under vanliga uppgifter. På sidan Test kan du testa webbtjänsten som en RRS (Request-Response Service) eller en batchkörningstjänst (BES).

RRS-testsidan visar indata, utdata och alla globala parametrar som du har definierat för experimentet. Om du vill testa webbtjänsten kan du manuellt ange lämpliga värden för indata eller ange en CSV-formaterad fil (comma separated value) som innehåller testvärdena.

Om du vill testa med RRS anger du lämpliga värden för ingångarna från listvyläget och klickar på **Testa begäran-svar**. Dina förutsägelseresultat visas i utdatakolumnen till vänster.

![Ange lämpliga värden för att testa webbtjänsten](./media/publish-a-machine-learning-web-service/figure-5-test-request-response.png)

Om du vill testa din BES klickar du på **Batch**. Klicka på Bläddra under indata på sidan Batchtest och välj en CSV-fil som innehåller lämpliga exempelvärden. Om du inte har en CSV-fil och du har skapat ditt förutsägelseexperiment med Machine Learning Studio (klassisk) kan du hämta datauppsättningen för ditt förutsägelseexperiment och använda den.

Om du vill hämta datauppsättningen öppnar du Machine Learning Studio (klassisk). Öppna ditt förutsägelseexperiment och högerklicka på indata för experimentet. Välj **datauppsättning** på snabbmenyn och välj sedan **Hämta**.

![Ladda ner datauppsättningen från studioduken (klassisk)](./media/publish-a-machine-learning-web-service/figure-7-mls-download.png)

Klicka på **Testa**. Statusen för batchkörningsjobbet visas till höger under **Test batchjobb**.

![Testa ditt batchkörningsjobb med webbtjänstportalen](./media/publish-a-machine-learning-web-service/figure-6-test-batch-execution.png)

<!--![Test the web service](./media/publish-a-machine-learning-web-service/figure-3.png)-->

På **sidan KONFIGURATION** kan du ändra beskrivning, titel, uppdatering av lagringskontonyckeln och aktivera exempeldata för webbtjänsten.

![Konfigurera webbtjänsten](./media/publish-a-machine-learning-web-service/figure-8-arm-configure.png)

### <a name="access-your-new-web-service"></a>Få tillgång till din nya webbtjänst

När du har distribuerat webbtjänsten från Machine Learning Studio (klassiskt) kan du skicka data till tjänsten och få svar programmässigt.

På sidan **Förbruka** finns all information du behöver för att komma åt din webbtjänst. API-nyckeln tillhandahålls till exempel för att tillåta auktoriserad åtkomst till tjänsten.

Mer information om hur du använder en webbkameratjänst (klassisk) webbtjänst för Machine Learning Studio finns i [Så här använder du en klassisk webbtjänst för Azure Machine Learning Studio](consume-web-services.md)(

### <a name="manage-your-new-web-service"></a>Hantera din nya webbtjänst

Du kan hantera dina nya webbtjänster med machine learning studio-portalen (klassiskt). Klicka på **Webbtjänster**på [huvudportalsidan.](https://services.azureml.net/) På sidan med webbtjänster kan du ta bort eller kopiera en tjänst. Om du vill övervaka en viss tjänst klickar du på tjänsten och sedan på **Instrumentpanelen**. Om du vill övervaka batchjobb som är associerade med webbtjänsten klickar du på **Batchbegäranslogg**.

### <a name="deploy-your-new-web-service-to-multiple-regions"></a><a id="multi-region"></a>Distribuera den nya webbtjänsten till flera regioner

Du kan enkelt distribuera en ny webbtjänst till flera regioner utan att behöva flera prenumerationer eller arbetsytor.

Prissättning är regionspecifikt, så du måste definiera en faktureringsplan för varje region där du ska distribuera webbtjänsten.

#### <a name="create-a-plan-in-another-region"></a>Skapa en plan i en annan region

1. Logga in på [Microsoft Azure Machine Learning Web Services](https://services.azureml.net/).
2. Klicka på menyalternativet **Planer.**
3. Klicka på **Nytt**på sidan Planer över vyn .
4. Välj den prenumeration där det nya abonnemanget ska finnas i i listrutan **Prenumeration.**
5. Välj en region för den nya planen i listrutan **Region.** I avsnittet Planera alternativ för det valda området visas de i avsnittet **Abonnemangsalternativ** på sidan.
6. Välj en resursgrupp för planen i listrutan **Resursgrupp.** Mer information om resursgrupper finns i [översikt över Azure Resource Manager](../../azure-resource-manager/management/overview.md).
7. I **Plannamn** skriver du namnet på planen.
8. Klicka på faktureringsnivån för det nya abonnemanget under **Abonnemangsalternativ.**
9. Klicka på **Skapa**.

#### <a name="deploy-the-web-service-to-another-region"></a>Distribuera webbtjänsten till en annan region

1. Klicka på menyalternativet **Web Services** på sidan Microsoft Azure Machine Learning Web Services.
2. Välj den webbtjänst som du distribuerar till en ny region.
3. Klicka på **Kopiera**.
4. Skriv ett nytt namn för webbtjänsten i **Webbtjänstnamn.**
5. Skriv en beskrivning för webbtjänsten i **webbtjänstbeskrivningen.**
6. Välj den prenumeration där den nya webbtjänsten ska finnas i listrutan **Prenumeration.**
7. Välj en resursgrupp för webbtjänsten i listrutan **Resursgrupp.** Mer information om resursgrupper finns i [översikt över Azure Resource Manager](../../azure-resource-manager/management/overview.md).
8. Välj i listrutan **Region** där webbtjänsten ska distribueras.
9. Välj ett **lagringskonto** där webbtjänsten ska lagras i listrutan Lagringskonto.
10. Välj en plan i det område som du valde i steg 8 i listrutan **Prisplan.**
11. Klicka på **Kopiera**.

## <a name="deploy-it-as-a-classic-web-service"></a>Distribuera den som en klassisk webbtjänst

Nu när förutsägelseexperimentet har förberetts tillräckligt kan du distribuera det som en klassisk Azure-webbtjänst. Med hjälp av webbtjänsten kan användare skicka data till din modell och modellen returnerar sina förutsägelser.

Om du vill distribuera förutsägelseexperimentet klickar du på **Kör** längst ned på experimentarbetsytan och sedan på **Distribuera webbtjänst**. Webbtjänsten är konfigurerad och du placeras i webbtjänstens instrumentpanel.

![Distribuera webbtjänsten från Studio (klassisk)](./media/publish-a-machine-learning-web-service/figure-2.png)

### <a name="test-your-classic-web-service"></a>Testa din klassiska webbtjänst

Du kan testa webbtjänsten i antingen Machine Learning Studio -portalen (klassiskt) webbtjänster eller Machine Learning Studio (klassisk).

Om du vill testa webbtjänsten Begär svar klickar du på knappen **Testa** på webbtjänstens instrumentpanel. En dialogruta visas för att be dig om indata för tjänsten. Det här är de kolumner som förväntas av bedömningsexperimentet. Ange en uppsättning data och klicka sedan på **OK**. De resultat som genereras av webbtjänsten visas längst ned på instrumentpanelen.

Du kan klicka på länken **Testa** förhandsgranskning för att testa din tjänst i Azure Machine Learning Studio -portalen (klassisk) som tidigare i avsnittet Ny webbtjänst.

Om du vill testa batchkörningstjänsten klickar du på **Länken Testa** förhandsgranskning . Klicka på Bläddra under indata på sidan Batchtest och välj en CSV-fil som innehåller lämpliga exempelvärden. Om du inte har en CSV-fil och du har skapat ditt förutsägelseexperiment med Machine Learning Studio (klassisk) kan du hämta datauppsättningen för ditt förutsägelseexperiment och använda den.

![Testa webbtjänsten](./media/publish-a-machine-learning-web-service/figure-3.png)

På **sidan KONFIGURATION** kan du ändra tjänstens visningsnamn och ge den en beskrivning. Namnet och beskrivningen visas i [Azure-portalen](https://portal.azure.com/) där du hanterar dina webbtjänster.

Du kan ange en beskrivning av indata, utdata och webbtjänstparametrar genom att ange en sträng för varje kolumn under **INDATASCHEMA,** **UTDATASCHEMA**och **WEB SERVICE PARAMETER**. Dessa beskrivningar används i exempelkoddokumentationen som tillhandahålls för webbtjänsten.

Du kan aktivera loggning för att diagnostisera eventuella fel som du ser när webbtjänsten används. Mer information finns i [Aktivera loggning för Machine Learning Studio (klassiska) webbtjänster](web-services-logging.md).

![Aktivera inloggning i webbtjänstportalen](./media/publish-a-machine-learning-web-service/figure-4.png)

Du kan också konfigurera slutpunkterna för webbtjänsten i Azure Machine Learning Web Services-portalen som liknar den procedure som tidigare i avsnittet Ny webbtjänst. Alternativen är olika, du kan lägga till eller ändra tjänstbeskrivningen, aktivera loggning och aktivera exempeldata för testning.

### <a name="access-your-classic-web-service"></a>Få tillgång till din klassiska webbtjänst

När du distribuerar din webbtjänst från Azure Machine Learning Studio (klassisk) kan du skicka data till tjänsten och få svar programmässigt.

Instrumentpanelen innehåller all information du behöver för att komma åt din webbtjänst. API-nyckeln tillhandahålls till exempel för att tillåta auktoriserad åtkomst till tjänsten, och API-hjälpsidor tillhandahålls för att hjälpa dig att komma igång med att skriva din kod.

Mer information om hur du använder en webbkameratjänst (klassisk) webbtjänst för Machine Learning Studio finns i [Så här använder du en klassisk webbtjänst för Azure Machine Learning Studio](consume-web-services.md)(

### <a name="manage-your-classic-web-service"></a>Hantera din klassiska webbtjänst

Det finns olika åtgärder som du kan utföra för att övervaka en webbtjänst. Du kan uppdatera den och ta bort den. Du kan också lägga till ytterligare slutpunkter i en klassisk webbtjänst utöver standardslutpunkten som skapas när du distribuerar den.

Mer information finns i [Hantera en arbetsyta i Azure Machine Learning Studio (klassisk)](manage-workspace.md) och Hantera en [webbtjänst med hjälp av portalen Azure Machine Learning Studio (klassiskt) Webbtjänst .](manage-new-webservice.md)

## <a name="update-the-web-service"></a>Uppdatera webbtjänsten
Du kan göra ändringar i webbtjänsten, till exempel uppdatera modellen med ytterligare träningsdata, och distribuera den igen och skriva över den ursprungliga webbtjänsten.

Om du vill uppdatera webbtjänsten öppnar du det ursprungliga förutsägelseexperiment som du använde för att distribuera webbtjänsten och gör en redigerbar kopia genom att klicka på **SPARA SOM**. Gör ändringarna och klicka sedan på **Distribuera webbtjänst**.

Eftersom du har distribuerat det här experimentet tidigare tillfrågas du om du vill skriva över (Klassisk webbtjänst) eller uppdatera (Ny webbtjänst) den befintliga tjänsten. Om du klickar på **JA** eller **Uppdatera** stoppas den befintliga webbtjänsten och det nya förutsägelseexperimentet distribueras i dess ställe.

> [!NOTE]
> Om du har gjort konfigurationsändringar i den ursprungliga webbtjänsten, till exempel att ange ett nytt visningsnamn eller en ny beskrivning, måste du ange dessa värden igen.

Ett alternativ för att uppdatera webbtjänsten är att omskola modellen programmässigt. Mer information finns i [Träna om machine learning studio (klassiska) modeller programmässigt](/azure/machine-learning/studio/retrain-machine-learning-model).

## <a name="next-steps"></a>Nästa steg

* Mer teknisk information om hur distribution fungerar finns i [Hur en maskininlärningsstudio (klassisk) modell fortskrider från ett experiment till en operationaliserad webbtjänst](model-progression-experiment-to-web-service.md).

* Mer information om hur du gör din modell redo att distribuera finns i Så här förbereder du [din modell för distribution i Azure Machine Learning Studio (klassiskt).](convert-training-experiment-to-scoring-experiment.md)

* Du kan använda REST-API:et och komma åt webbtjänsten på flera sätt. Se [Hur du använder en azure Machine Learning Studio (klassisk) webbtjänst](consume-web-services.md).

<!-- internal links -->
[Skapa ett utbildningsexperiment]: #create-a-training-experiment
[Konvertera den till ett prediktivt experiment]: #convert-the-training-experiment-to-a-predictive-experiment
[Ny webbtjänst]: #deploy-it-as-a-new-web-service
[Klassisk webbtjänst]: #deploy-it-as-a-classic-web-service
[Nya]: #deploy-it-as-a-new-web-service
[classic]: #deploy-the-predictive-experiment-as-a-classic-web-service
[Access]: #access-the-Web-service
[Manage]: #manage-the-Web-service-in-the-azure-management-portal
[Update]: #update-the-Web-service
