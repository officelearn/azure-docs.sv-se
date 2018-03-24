---
title: Distribuera en Machine Learning-webbtjänst | Microsoft Docs
description: Hur du konverterar en träningsexperiment till en prediktivt experiment, förbereda för distribution och sedan distribuera det som en Azure Machine Learning-webbtjänst.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: 73a3e9c6-00d0-41d4-8cf1-2ec87713867e
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/06/2017
ms.openlocfilehash: 3ce4104040e90a4740442d7692b3bf4a0789bde0
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="deploy-an-azure-machine-learning-web-service"></a>Distribuera en Azure Machine Learning-webbtjänst
Azure Machine Learning kan du skapa, testa och distribuera förutsägelseanalyslösningar.

Detta görs från en punkt-för-översikt, i tre steg:

* **[Skapa en träningsexperiment]**  -Azure Machine Learning Studio är en gemensam visual utvecklingsmiljö som används för att träna och testa en förutsägelseanalysmodell med hjälp av utbildningsdata som du anger.
* **[Konvertera den till en prediktivt experiment]**  -när modellen har tränats med befintliga data och du är redo att använda den för att samla in nya data kan du förbereda och förenkla experimentet för förutsägelser.
* **[Distribuera den som en webbtjänst]**  -du kan distribuera din prediktivt experiment som en [nya] eller [klassiska] Azure-webbtjänsten. Användare kan skicka data till din modell och ta emot din modell förutsägelser.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="create-a-training-experiment"></a>Skapa ett experiment utbildning
För att träna en förutsägelseanalysmodell använder du Azure Machine Learning Studio för att skapa en träningsexperiment där du kan inkludera olika moduler för att läsa in träningsdata, förbereda data vid behov, tillämpa maskininlärningsalgoritmer och utvärdera resultaten. Du kan iterera ett experiment och prova olika maskininlärningsalgoritmer för att jämföra och utvärdera resultaten.

Processen för att skapa och hantera utbildning experiment beskrivs mer i detalj någon annanstans. Mer information finns i dessa artiklar:

* [Skapa ett enkelt experiment i Azure Machine Learning Studio](create-experiment.md)
* [Utveckla en förutsägelselösning med Azure Machine Learning](walkthrough-develop-predictive-solution.md)
* [Importera utbildningsdata till Azure Machine Learning Studio](import-data.md)
* [Hantera iterationer av experiment i Azure Machine Learning Studio](manage-experiment-iterations.md)

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Omvandla utbildning experimentet till prediktivt experiment
När du har tränat modellen, är du redo att konvertera experimentet utbildning till prediktivt experiment att poängsätta nya data.

Genom att konvertera till en prediktivt experiment du håller på att din tränad modell redo att distribueras som en bedömningsprofil webbtjänst. Användare av webbtjänsten kan skicka inkommande data till din modell och din modell skickar tillbaka förutsägelse resultaten. När du konverterar till en prediktivt experiment, Tänk på hur du förväntar dig din modell som ska användas av andra.

Om du vill konvertera experimentet utbildning till prediktivt experiment, klickar du på **kör** längst ned i arbetsytan för experimentet klickar du på **konfigurera Web Service**och välj **förutsägande webbtjänsten**.

![Omvandla till bedömningen experiment](./media/publish-a-machine-learning-web-service/figure-1.png)

Mer information om hur du utför den här konverteringen finns [hur du förbereder din modell för distribution i Azure Machine Learning Studio](convert-training-experiment-to-scoring-experiment.md).

Följande steg beskriver hur du distribuerar en prediktivt experiment som en ny webbtjänst. Du kan också distribuera experimentet som klassisk webbtjänst.

## <a name="deploy-it-as-a-web-service"></a>Distribuera den som en webbtjänst

Du kan distribuera prediktivt experiment som en ny webbtjänst eller som en klassisk webbtjänst.

### <a name="deploy-the-predictive-experiment-as-a-new-web-service"></a>Distribuera prediktivt experiment som en ny webbtjänst
Nu när prediktivt experiment har förberetts, kan du distribuera den som en ny Azure-webbtjänst. Med hjälp av webbtjänsten användare kan skicka data till din modell och dess förutsägelser returneras i modellen.

För att distribuera din prediktivt experiment, klickar du på **kör** längst ned i arbetsytan för experimentet. När experimentet har slutförts klickar du på **distribuera webbtjänsten** och välj **distribuera webbtjänsten [ny]**.  Sidan distribution av Machine Learning-webbtjänst portalen öppnas.

> [!NOTE] 
> Om du vill distribuera en ny webbtjänst måste du ha tillräckliga behörigheter i prenumerationen som du distribuerar webbtjänsten. Mer information finns i [hantera en webbtjänst med hjälp av Azure Machine Learning-webbtjänster portal](manage-new-webservice.md). 

#### <a name="machine-learning-web-service-portal-deploy-experiment-page"></a>Machine Learning-webbtjänst portal distribuera Experiment sida
Ange ett namn för webbtjänsten på sidan distribuera Experiment.
Välj en prisnivå plan. Om du har en befintlig prisavtal kan du välja det måste annars du skapa en ny prisplan för tjänsten.

1. I den **prisplan** listrutan väljer du ett befintligt schema eller den **Välj ny plan** alternativet.
2. I **schemanamn**, Skriv ett namn som identifierar planen på fakturan.
3. Välj en av de **månatliga planera nivåer**. Planera nivåerna standard att planer för standardregion och webbtjänsten har distribuerats till den regionen.

Klicka på **distribuera** och **Quickstart** öppnas sidan för webbtjänsten.

Sidan Snabbstart service ger dig åtkomst och riktlinjer för de vanligaste uppgifterna som du utför när du har skapat en webbtjänst. Härifrån kan du enkelt kan komma åt både den Test och förbruka sidan.

<!-- ![Deploy the web service](./media/publish-a-machine-learning-web-service/figure-2.png)-->

#### <a name="test-your-new-web-service"></a>Testa din nya webbtjänsten
Om du vill testa nya webbtjänsten klickar du på **testa webbtjänsten** under vanliga uppgifter. På sidan Test kan du testa webbtjänsten som ett svar på begäranden tjänsten (RR) eller en Batch Execution service (BES).

RR testsida visar indata, utdata och eventuella globala parametrar som du har definierat för experimentet. Om du vill testa webbtjänsten du manuellt ange lämpliga värden för indata eller ange en fil med kommaavgränsade värden (CSV) formaterad fil som innehåller testvärden.

Testa med RRS, ange lämpliga värden för indata från läget för listan och klicka på **testa begäran och svar**. Förutsägelse resultat visas i utdatakolumnen till vänster.

![Distribuera webbtjänsten](./media/publish-a-machine-learning-web-service/figure-5-test-request-response.png)

Testa din BES, klicka på **Batch**. Klicka på Bläddra under dina indata på testsida Batch och välj en CSV-fil som innehåller rätt exempelvärden. Om du inte har en CSV-fil och du har skapat din prediktivt experiment med Machine Learning Studio, kan du hämta datauppsättningen för experimentet förutsägbara och använda den.

Öppna Machine Learning Studio om du vill hämta datauppsättningen. Öppna din prediktivt experiment och högerklicka på indata för experimentet. På snabbmenyn Välj **dataset** och välj sedan **hämta**.

![Distribuera webbtjänsten](./media/publish-a-machine-learning-web-service/figure-7-mls-download.png)

Klicka på **Test**. Batch Execution-jobbets status visas till höger under **Test batchjobb**.

![Distribuera webbtjänsten](./media/publish-a-machine-learning-web-service/figure-6-test-batch-execution.png)

<!--![Test the web service](./media/publish-a-machine-learning-web-service/figure-3.png)-->

På den **CONFIGURATION** sidan kan du ändra beskrivningen, avdelning, uppdatera lagringskontots åtkomstnyckel och aktivera exempeldata för webbtjänsten.

![Konfigurera webbtjänsten](./media/publish-a-machine-learning-web-service/figure-8-arm-configure.png)

När du har distribuerat webbtjänsten, kan du:

* **Åtkomst** det via web service API.
* **Hantera** det via Azure Machine Learning web services-portalen.
* **Uppdatera** det om din modell ändras.

#### <a name="access-your-new-web-service"></a>Få åtkomst till din nya webbtjänsten
När du distribuerar webbtjänsten från Machine Learning Studio, kan du skicka data till tjänsten och ta emot svar programmässigt.

Den **förbruka** innehåller all information du behöver åtkomst till webbtjänsten. Till exempel tillhandahålls API-nyckeln så att auktoriserad åtkomst till tjänsten.

Mer information om åtkomst till en Machine Learning-webbtjänst finns [använda en Azure Machine Learning-webbtjänst](consume-web-services.md).

#### <a name="manage-your-new-web-service"></a>Hantera nya webbtjänsten
Du kan hantera nya tjänster Machine Learning-webbtjänster webbportalen. Från den [huvudsidan för Företagsportalen](https://services.azureml-test.net/), klickar du på **Web Services**. Du kan ta bort eller kopiera en tjänst från sidan web services. Om du vill övervaka en specifik tjänst klickar du på tjänsten och klicka sedan på **instrumentpanelen**. Om du vill övervaka batchjobb som är kopplade till webbtjänsten klickar du på **Batch begära loggen**.

### <a name="deploy-the-predictive-experiment-as-a-classic-web-service"></a>Distribuera prediktivt experiment som ett klassiskt-webbtjänst

Nu när prediktivt experiment tillräckligt har förberetts, kan du distribuera den som en klassiska Azure-webbtjänst. Med hjälp av webbtjänsten användare kan skicka data till din modell och dess förutsägelser returneras i modellen.

För att distribuera din prediktivt experiment, klickar du på **kör** längst ned i experimentet arbetsytan och klicka sedan på **distribuera webbtjänsten**. Webbtjänsten har konfigurerats och du placeras i instrumentpanelen för web service.

![Distribuera webbtjänsten](./media/publish-a-machine-learning-web-service/figure-2.png)

#### <a name="test-your-classic-web-service"></a>Testa din klassiska-webbtjänst

Du kan testa webbtjänsten i Machine Learning-webbtjänster portalen eller Machine Learning Studio.

Testa Request Response-webbtjänsten, klicka på den **testa** knappen web service-instrumentpanelen. En dialogruta som öppnas uppmanas du ange indata för tjänsten. Dessa är de kolumner som förväntades av bedömningsprofil experimentet. Ange en uppsättning data och klicka sedan på **OK**. Resultaten som genereras av webbtjänsten visas längst ned i instrumentpanelen.

Du kan klicka på den **testa** preview länken om du vill testa tjänsten i Azure Machine Learning-webbtjänster portal som visas tidigare i avsnittet nya web service.

Testa Batch Execution Service genom att klicka på **testa** preview länk. Klicka på Bläddra under dina indata på testsida Batch och välj en CSV-fil som innehåller rätt exempelvärden. Om du inte har en CSV-fil och du har skapat din prediktivt experiment med Machine Learning Studio, kan du hämta datauppsättningen för experimentet förutsägbara och använda den.

![Testa webbtjänsten](./media/publish-a-machine-learning-web-service/figure-3.png)

På den **CONFIGURATION** kan du ändra visningsnamnet för tjänsten och en beskrivning. Namn och beskrivning visas i den [Azure-portalen](https://portal.azure.com/) där du hanterar din webbtjänster.

Du kan ange en beskrivning för indata, utdata och web Serviceparametrar genom att ange en sträng för varje kolumn under **INMATNINGSSCHEMAT**, **UTDATASCHEMA**, och **Web SERVICE PARAMETERN**. Dessa beskrivningar används i koden dokumentationen prov för webbtjänsten.

Du kan aktivera loggning för att diagnostisera eventuella fel som det uppstår vid åtkomst av webbtjänsten. Mer information finns i [aktivera loggning för Machine Learning-webbtjänster](web-services-logging.md).

![Konfigurera webbtjänsten](./media/publish-a-machine-learning-web-service/figure-4.png)

Du kan också konfigurera slutpunkter för webbtjänsten i Azure Machine Learning-webbtjänster portal liknar det sätt som visas tidigare i avsnittet nya web service. Alternativen är olika, kan du lägga till eller ändra beskrivning, aktivera loggning och aktivera exempeldata för testning.

#### <a name="access-your-classic-web-service"></a>Åtkomst till webbtjänsten klassisk
När du distribuerar webbtjänsten från Machine Learning Studio, kan du skicka data till tjänsten och ta emot svar programmässigt.

Instrumentpanelen innehåller all information du behöver åtkomst till webbtjänsten. Till exempel API-nyckeln tillhandahålls så att auktoriserad åtkomst till tjänsten och API-artiklar som hjälper dig att komma igång skriva koden.

Mer information om åtkomst till en Machine Learning-webbtjänst finns [använda en Azure Machine Learning-webbtjänst](consume-web-services.md).

#### <a name="manage-your-classic-web-service"></a>Hantera webbtjänsten klassisk
Det finns olika åtgärder du kan utföra att övervaka en webbtjänst. Du kan uppdatera och ta bort den. Du kan också lägga till ytterligare slutpunkter till en klassisk webbtjänst förutom standardslutpunkten som skapas när du distribuerar den.

Mer information finns i [hantera en Azure Machine Learning-arbetsytan](manage-workspace.md) och [hantera en webbtjänst med hjälp av Azure Machine Learning-webbtjänster portal](manage-new-webservice.md).

<!-- When this article gets published, fix the link and uncomment
For more information on how to manage Azure Machine Learning web service endpoints using the REST API, see **Azure machine learning web service endpoints**.
-->

## <a name="update-the-web-service"></a>Uppdatera webbtjänsten
Du kan göra ändringar i webbtjänsten, till exempel uppdaterar modellen med ytterligare utbildningsdata, och distribuera den igen att skriva över den ursprungliga webbtjänsten.

Om du vill uppdatera webbtjänsten, öppna den ursprungliga prediktivt experiment som du använde för att distribuera webbtjänsten och göra en redigerbar kopia genom att klicka på **Spara som**. Gör ändringarna och klicka sedan på **distribuera webbtjänsten**.

Eftersom du har distribuerat experimentet innan tillfrågas du om du vill skriva över (klassiska webbtjänst) eller uppdatera (ny webbtjänst) befintliga. Klicka på **Ja** eller **uppdatering** stoppar befintlig webbtjänst och distribuerar den nya prediktivt experiment distribueras i dess ställe.

> [!NOTE]
> Om du har gjort konfigurationsändringar i ursprungliga webbtjänsten exempelvis måste att ange ett nytt namn eller beskrivning, du ange dessa värden igen.
> 
> 

Ett alternativ för att uppdatera webbtjänsten är att träna om modellen programmässigt. Mer information finns i [Träna om Machine Learning-modeller via programmering](retrain-models-programmatically.md).

<!-- internal links -->
[Skapa en träningsexperiment]: #create-a-training-experiment
[Konvertera den till en prediktivt experiment]: #convert-the-training-experiment-to-a-predictive-experiment
[Distribuera den som en webbtjänst]: #deploy-it-as-a-web-service
[nya]: #deploy-the-predictive-experiment-as-a-new-Web-service
[klassiska]: #deploy-the-predictive-experiment-as-a-new-Web-service
[Access]: #access-the-Web-service
[Manage]: #manage-the-Web-service-in-the-azure-management-portal
[Update]: #update-the-Web-service
