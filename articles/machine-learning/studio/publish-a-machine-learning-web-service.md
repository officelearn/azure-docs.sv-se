---
title: Distribuera en Machine Learning Studio-webbtjänst
titleSuffix: Azure Machine Learning Studio
description: Så här att konvertera ett träningsexperiment till ett förutsägbart experiment, förbereda för distribution och sedan distribuera den som en Azure Machine Learning Studio-webbtjänst.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 01/06/2017
ms.openlocfilehash: 5990f47a7cc1517349d85654bf5f02f6240e9baa
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/16/2019
ms.locfileid: "56327603"
---
# <a name="deploy-an-azure-machine-learning-studio-web-service"></a>Distribuera en Azure Machine Learning Studio-webbtjänst

Azure Machine Learning Studio kan du skapa och testa en lösning för förutsägande analys. Du kan sedan distribuera lösningen som en webbtjänst.

Machine Learning Studio-webbtjänster är ett gränssnitt mellan ett program och en arbetsflödesbaserad poängmodell i Machine Learning Studio. Ett externt program kan kommunicera med en Machine Learning Studio arbetsflödesbaserad poängmodell i realtid. Ett anrop till en Machine Learning Studio-webbtjänst returnerar förutsägelser till ett externt program. Om du vill göra ett anrop till en webbtjänst skickar du en API-nyckel som skapades när du distribuerade webbtjänsten. En Machine Learning Studio-webbtjänst baseras på REST, ett populärt arkitekturval för programmeringsprojekt.

Azure Machine Learning Studio har två typer av webbtjänster:

* Request-Response Service (RR): Med låg fördröjning, mycket skalbar tjänst som poängsätter en enda post.
* Batch Execution Service (BES): En asynkron tjänst som poängsätter en batch med dataposter.

Indata för BES är som de indata som används av RRS. Den största skillnaden är att BES läser ett block med poster från olika källor, till exempel Azure Blob Storage, Azure Table Storage, Azure SQL Database, HDInsight (hive-fråga) och HTTP-källor.

Från en återställningspunkt-för-översikt, kan du distribuera din modell i tre steg:

* **[Skapa ett träningsexperiment]**  -i Studio som du kan träna och testa en modell för förutsägelseanalys med hjälp av träningsdata som du anger med hjälp av en stor mängd inbyggda machine learning-algoritmer.
* **[Konvertera den till ett förutsägbart experiment]**  -när modellen har tränats med befintliga data och du är redo att använda den för att rangordna nya data kan du förbereda och effektivisera experimentet för förutsägelser.
* **Distribuera** den som en **[ny webbtjänst]** eller en **[klassisk webbtjänst]** – när du distribuerar din förutsägelseexperiment som en Azure-webbtjänst, användare kan skicka data till din modell och ta emot din modell förutsägelser.

## <a name="create-a-training-experiment"></a>Skapa ett träningsexperiment

För att träna en modell för förutsägelseanalys använda du Azure Machine Learning Studio för att skapa ett träningsexperiment där du kan inkludera olika moduler att läsa in utbildningsdata, förbereda data vid behov, använda machine learning-algoritmer och utvärdera resultaten. Du kan iterera på ett experiment och testa olika machine learning-algoritmer för att jämföra och utvärdera resultaten.

Processen för att skapa och hantera utbildning experiment beskrivs mer i detalj någon annanstans. Mer information finns i dessa artiklar:

* [Skapa ett enkelt experiment i Azure Machine Learning Studio](create-experiment.md)
* [Utveckla en förutsägande lösning med Azure Machine Learning Studio](tutorial-part1-credit-risk.md)
* [Importera dina utbildningsdata till Azure Machine Learning Studio](import-data.md)
* [Hantera iterationer av experiment i Azure Machine Learning Studio](manage-experiment-iterations.md)

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Konvertera träningsexperimentet till ett förutsägbart experiment

När du har tränat modellen, är du redo att konvertera din träningsexperiment till ett förutsägbart experiment att rangordna nya data.

Genom att konvertera till ett förutsägbart experiment, får du den tränade modellen redo att distribueras som en bedömning av webbtjänsten. Användare av webbtjänsten kan skicka indata till din modell och din modell skickar tillbaka resultatet. När du konverterar till ett förutsägbart experiment, Tänk på vad du tror att din modell som ska användas av andra.

Om du vill konvertera din träningsexperiment till ett förutsägbart experiment, klickar du på **kör** längst ned på arbetsytan för experimentet klickar du på **konfigurera Web Service**och välj sedan **förutsägande webbtjänsten**.

![Omvandla till bedömning experiment](./media/publish-a-machine-learning-web-service/figure-1.png)

Läs mer om hur du utför den här konverteringen [hur du förbereder din modell för distribution i Azure Machine Learning Studio](convert-training-experiment-to-scoring-experiment.md).

Följande steg beskriver distribuera ett förutsägelseexperiment som en ny webbtjänst. Du kan också distribuera experimentet som klassisk webbtjänst.

## <a name="deploy-it-as-a-new-web-service"></a>Distribuera den som en ny webbtjänst

Nu när förutsägbart experiment har förberetts, kan du distribuera den som en ny (Resource Manager-baserade) Azure web-tjänst. Med hjälp av webbtjänsten användare kan skicka data till din modell och modellen returnerar dess förutsägelser.

För att distribuera din förutsägelseexperiment, klickar du på **kör** längst ned på arbetsytan för experimentet. När experimentet har körts, klickar du på **distribuera webbtjänsten** och välj **distribuera webbtjänsten [Ny]**.  Distributionssidan av Machine Learning Studio-webbtjänsten portalen öppnas.

> [!NOTE] 
> Om du vill distribuera en ny webbtjänst måste du ha tillräcklig behörighet i prenumerationen som du distribuerar webbtjänsten. Mer information finns i [hantera en webbtjänst med hjälp av Azure Machine Learning Web Services-portalen](manage-new-webservice.md). 

### <a name="machine-learning-studio-web-service-portal-deploy-experiment-page"></a>Machine Learning Studio-webbtjänst portal distribuera Experiment sidan

Ange ett namn för webbtjänsten på sidan distribuera Experiment.
Välj en prisplanen. Om du har ett existerande prisplan kan du välja den, måste annars du skapa en ny prisplan för tjänsten.

1. I den **prisplan** listrutan väljer du en befintlig plan eller den **väljer ny plan** alternativet.
2. I **Plannamnet**, Skriv ett namn som identifierar prenumerationen på fakturan.
3. Välj en av de **månatliga planera nivåerna**. Plan för nivåerna standard för planer för din standardregion och din webbtjänst distribueras till den regionen.

Klicka på **distribuera** och **snabbstarten** för webbtjänsten öppnas.

Sidan Snabbstart service ger dig åtkomst och vägledning på de vanligaste uppgifterna som du utför när du har skapat en webbtjänst. Härifrån kan du enkelt komma åt både den Test och förbruka sidan.

<!-- ![Deploy the web service](./media/publish-a-machine-learning-web-service/figure-2.png)-->

### <a name="test-your-new-web-service"></a>Testa din nya webbtjänst

Om du vill testa din nya webbtjänst, klickar du på **testa webbtjänsten** under vanliga uppgifter. Du kan testa din webbtjänst som en Request-Response Service (RR) eller en BES-tjänsten i Batch Execution på sidan Test.

RRS testsidan visar indata, utdata och eventuella globala parametrar som du har definierat för experimentet. Du kan manuellt ange lämpliga värden för indata eller ange en fil med kommaavgränsade värden (CSV) formaterad fil som innehåller testvärden för att testa webbtjänsten.

Om du vill testa med RRS, ange lämpliga värden för indata från visningsläge listan och klicka på **testa Request-Response**. Din förutsagda resultaten som visas i utdatakolumnen till vänster.

![Distribuera webbtjänsten](./media/publish-a-machine-learning-web-service/figure-5-test-request-response.png)

Om du vill testa din BES, klickar du på **Batch**. På sidan Batch test klickar du på Bläddra under dina indata och välj en CSV-fil med lämpliga exempelvärden. Om du inte har en CSV-fil och du har skapat din förutsägbart experiment med Machine Learning Studio, kan du hämta datauppsättningen för din förutsägelseexperiment och använda den.

Öppna Machine Learning Studio om du vill hämta datauppsättningen. Öppna din förutsägelseexperiment och högerklicka på indata för experimentet. På snabbmenyn väljer **datauppsättning** och välj sedan **hämta**.

![Distribuera webbtjänsten](./media/publish-a-machine-learning-web-service/figure-7-mls-download.png)

Klicka på **Test**. Status för batchkörning jobbet visas till höger under **Test batchjobb**.

![Distribuera webbtjänsten](./media/publish-a-machine-learning-web-service/figure-6-test-batch-execution.png)

<!--![Test the web service](./media/publish-a-machine-learning-web-service/figure-3.png)-->

På den **CONFIGURATION** kan du kan ändra beskrivningen, rubrik, uppdatera nyckeln till lagringskontot och aktivera exempeldata för din webbtjänst.

![Konfigurera webbtjänsten](./media/publish-a-machine-learning-web-service/figure-8-arm-configure.png)

### <a name="access-your-new-web-service"></a>Få åtkomst till din nya webbtjänst

När du distribuerar din webbtjänst från Machine Learning Studio kan du skicka data till tjänsten och få svar programmässigt.

Den **förbruka** sidan innehåller all information du behöver att få åtkomst till webbtjänsten. Till exempel har API-nyckel angetts för att tillåta auktoriserad åtkomst till tjänsten.

Mer information om åtkomst till en Machine Learning Studio-webbtjänst finns i [hur man använder en Azure Machine Learning Studio Web-tjänst](consume-web-services.md).

### <a name="manage-your-new-web-service"></a>Hantera din nya webbtjänst

Du kan hantera din nya tjänster Machine Learning Studio Web Services-webbportal. Från den [huvudsakliga portalsidan](https://services.azureml-test.net/), klickar du på **webbtjänster**. Du kan ta bort eller kopiera en tjänst från sidan web services. Om du vill övervaka en specifik tjänst klickar du på tjänsten och klicka sedan på **instrumentpanelen**. Om du vill övervaka batch-jobb som är associerade med webbtjänsten klickar du på **Batch Request Log**.

### <a id="multi-region"></a> Distribuera din nya webbtjänst till flera regioner

Du kan enkelt distribuera en ny webbtjänst till flera regioner utan att behöva flera prenumerationer eller arbetsytor.

Priserna beror på region, så du måste definiera ett faktureringsavtal för varje region där du distribuerar webbtjänsten.

#### <a name="create-a-plan-in-another-region"></a>Skapa en plan i en annan region

1. Logga in på [Microsoft Azure Machine Learning-webbtjänster](https://services.azureml.net/).
2. Klicka på den **planer** menyalternativ.
3. På planer över visningssida, klickar du på **New**.
4. Från den **prenumeration** listrutan väljer du den prenumeration där den nya planen kommer att finnas.
5. Från den **Region** listrutan väljer du en region för den nya planen. Planera alternativ för den valda regionen visas i den **planera alternativ** på sidan.
6. Från den **resursgrupp** listrutan, Välj en resursgrupp för planen. För mer information om resursgrupper, se [översikt över Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).
7. I **Plannamnet** skriver du namnet på planen.
8. Under **Avtalsalternativ**, klickar du på den faktureringen för den nya planen.
9. Klicka på **Skapa**.

#### <a name="deploy-the-web-service-to-another-region"></a>Distribuera webbtjänsten till en annan region

1. Microsoft Azure Machine Learning Web Services-sidan, klicka på den **webbtjänster** menyalternativ.
2. Välj den webbtjänst som du distribuerar till en ny region.
3. Klicka på **kopiera**.
4. I **Webbtjänstnamn**, Skriv ett nytt namn för webbtjänsten.
5. I **Web tjänstbeskrivning**, ange en beskrivning för webbtjänsten.
6. Från den **prenumeration** listrutan väljer du den prenumeration där den nya webbtjänsten kommer att finnas.
7. Från den **resursgrupp** listrutan, Välj en resursgrupp för webbtjänsten. För mer information om resursgrupper, se [översikt över Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).
8. Från den **Region** listrutan väljer du den region där du vill distribuera webbtjänsten.
9. Från den **lagringskonto** listrutan väljer du en storage-konto där du vill lagra webbtjänsten.
10. Från den **prisplan** listrutan väljer du en plan i den region som du valde i steg 8.
11. Klicka på **kopiera**.

## <a name="deploy-it-as-a-classic-web-service"></a>Distribuera den som en klassisk webbtjänst

Nu när förutsägbart experiment har förberetts tillräckligt, kan du distribuera den som en klassiska Azure-webbtjänst. Med hjälp av webbtjänsten användare kan skicka data till din modell och modellen returnerar dess förutsägelser.

För att distribuera din förutsägelseexperiment, klickar du på **kör** längst ned i experimentet arbetsytan och klicka sedan på **distribuera webbtjänsten**. Webbtjänsten har ställts in och du är placerade i instrumentpanelen för webbtjänsten.

![Distribuera webbtjänsten](./media/publish-a-machine-learning-web-service/figure-2.png)

### <a name="test-your-classic-web-service"></a>Testa din klassisk webbtjänst

Du kan testa webbtjänsten i Machine Learning Studio Web Services portal eller i Machine Learning Studio.

Om du vill testa webbtjänsten svar på begäran, klickar du på den **testa** knappen i instrumentpanelen för webbtjänsten. En dialogruta visas, uppmanas du ange indata för tjänsten. Det här är de kolumner som förväntas av bedömnings experimentet. Ange en uppsättning data och klicka sedan på **OK**. Resultat som genererats av webbtjänsten visas längst ned i instrumentpanelen.

Du kan klicka på den **testa** förhandsversion länk för att testa din tjänst i portalen Azure Machine Learning Studio-webbtjänster som visas tidigare i det nya web service-avsnittet.

Om du vill testa Batch Execution Service, klickar du på **testa** förhandsversion länk. På sidan Batch test klickar du på Bläddra under dina indata och välj en CSV-fil med lämpliga exempelvärden. Om du inte har en CSV-fil och du har skapat din förutsägbart experiment med Machine Learning Studio, kan du hämta datauppsättningen för din förutsägelseexperiment och använda den.

![Testa webbtjänsten](./media/publish-a-machine-learning-web-service/figure-3.png)

På den **CONFIGURATION** kan du kan ändra visningsnamnet för tjänsten och en beskrivning. Namnet och beskrivningen visas i den [Azure-portalen](https://portal.azure.com/) där du hanterar dina webbtjänster.

Du kan ange en beskrivning för indata, utdata och web tjänstparametrar genom att ange en sträng för varje kolumn under **INMATNINGSSCHEMAT**, **UTDATASCHEMA**, och **Web SERVICE PARAMETERN**. Dessa beskrivningar som används i exemplet code-dokumentation för webbtjänsten.

Du kan aktivera loggning för att diagnostisera eventuella fel som du ser när din webbtjänst används. Mer information finns i [aktivera loggning för Machine Learning Studio-webbtjänster](web-services-logging.md).

![Konfigurera webbtjänsten](./media/publish-a-machine-learning-web-service/figure-4.png)

Du kan också konfigurera slutpunkter för webbtjänsten i portalen Azure Machine Learning Web Services liknar det sätt som visas tidigare i avsnittet nya web service. Alternativen skiljer sig, du kan lägga till eller ändra tjänstbeskrivning, aktivera loggning och aktivera exempeldata för att testa.

### <a name="access-your-classic-web-service"></a>Få åtkomst till din klassisk webbtjänst

När du distribuerar din webbtjänst från Machine Learning Studio kan du skicka data till tjänsten och få svar programmässigt.

Instrumentpanelen innehåller all information du behöver att få åtkomst till webbtjänsten. Till exempel API-nyckel har angetts för att tillåta auktoriserad åtkomst till tjänsten och API-hjälpsidor som hjälper du börjar skriva kod.

Mer information om åtkomst till en Machine Learning Studio-webbtjänst finns i [hur man använder en Azure Machine Learning Studio Web-tjänst](consume-web-services.md).

### <a name="manage-your-classic-web-service"></a>Hantera din klassisk webbtjänst

Det finns olika åtgärder du kan utföra att övervaka en webbtjänst. Du kan uppdatera och ta bort den. Du kan också lägga till ytterligare slutpunkter till en klassisk webbtjänst förutom standardslutpunkten som skapas när du distribuerar den.

Mer information finns i [hantera en Azure Machine Learning Studio-arbetsyta](manage-workspace.md) och [hantera en webbtjänst med hjälp av Azure Machine Learning Studio-webbtjänster portal](manage-new-webservice.md).

## <a name="update-the-web-service"></a>Uppdatera webbtjänsten
Du kan göra ändringar i din webbtjänst, till exempel att uppdatera modellen med ytterligare data och distribuera den igen, skriver över den ursprungliga webbtjänsten.

Om du vill uppdatera webbtjänsten, öppna den ursprungliga förutsägbart experiment som du använde för att distribuera webbtjänsten och göra en redigerbar kopia genom att klicka på **Spara som**. Gör dina ändringar och klicka sedan på **distribuera webbtjänsten**.

Eftersom du har distribuerat det här experimentet innan du kan tillfrågas du om du vill skriva över (klassiska webbtjänst) eller uppdatera (ny webbtjänst) den befintliga tjänsten. Klicka på **Ja** eller **uppdatering** stoppar befintlig webbtjänst och distribuerar den nya förutsägelseexperiment har distribuerats i dess ställe.

> [!NOTE]
> Om du har gjort ändringar i konfigurationen i ursprungliga webbtjänsten, till exempel måste att ange ett nytt namn eller beskrivning, du ange dessa värden igen.

Ett alternativ för att uppdatera din webbtjänst är att träna modellen programmässigt. Mer information finns i [träna om Machine Learning Studio-modeller via programmering](retrain-models-programmatically.md).

## <a name="next-steps"></a>Nästa steg

* Mer teknisk information om hur distributionen fungerar finns i [hur en Machine Learning Studio modellen utvecklas från ett experiment till en produktionsslutpunkt webbtjänst](model-progression-experiment-to-web-service.md).

* Mer information om hur du förbereder din modell, för att distribuera finns [hur du förbereder din modell för distribution i Azure Machine Learning Studio](convert-training-experiment-to-scoring-experiment.md).

* Du kan använda REST-API:et och komma åt webbtjänsten på flera sätt. Se [hur du använder en Azure Machine Learning Studio-webbtjänst](consume-web-services.md).


<!-- internal links -->
[Skapa ett träningsexperiment]: #create-a-training-experiment
[Konvertera den till ett förutsägbart experiment]: #convert-the-training-experiment-to-a-predictive-experiment
[Ny webbtjänst]: #deploy-it-as-a-new-web-service
[Klassisk webbtjänst]: #deploy-it-as-a-classic-web-service
[Ny]: #deploy-the-predictive-experiment-as-a-new-web-service
[classic]: #deploy-the-predictive-experiment-as-a-classic-web-service
[Access]: #access-the-Web-service
[Manage]: #manage-the-Web-service-in-the-azure-management-portal
[Update]: #update-the-Web-service
