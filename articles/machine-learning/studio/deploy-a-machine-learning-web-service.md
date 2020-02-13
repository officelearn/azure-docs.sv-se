---
title: Distribuera en webbtjänst
titleSuffix: ML Studio (classic) - Azure
description: Så här konverterar du ett utbildnings experiment till ett förutsägelse experiment, förbereder det för distribution och distribuerar det som en Azure Machine Learning Studio (klassisk)-webb tjänst.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 01/06/2017
ms.openlocfilehash: 482dc0ab549ec6b9f05bc9f74a3d31ac13646ce5
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169211"
---
# <a name="deploy-an-azure-machine-learning-studio-classic-web-service"></a>Distribuera en Azure Machine Learning Studio (klassisk)-webb tjänst

Azure Machine Learning Studio (klassisk) gör det möjligt att bygga och testa en förutsägelse analys lösning. Sedan kan du distribuera lösningen som en webb tjänst.

Machine Learning Studio (klassiska) webb tjänster tillhandahåller ett gränssnitt mellan ett program och en Machine Learning Studio (klassisk) beräknings modell för arbets flöde. Ett externt program kan kommunicera med en Machine Learning Studio (klassisk) beräknings modell för arbets flöde i real tid. Ett anrop till en Machine Learning Studio (klassisk)-webb tjänst returnerar förutsägelse resultat till ett externt program. Om du vill göra ett anrop till en webbtjänst skickar du en API-nyckel som skapades när du distribuerade webbtjänsten. En Machine Learning Studio (klassisk)-webb tjänst baseras på REST, ett populärt arkitektur val för webb program projekt.

Azure Machine Learning Studio (klassisk) har två typer av webb tjänster:

* Request-Response service (resurs poster): en mycket skalbar tjänst med låg fördröjning som utvärderar en enda data post.
* BES (batch execution service): en asynkron tjänst som utvärderar en batch med data poster.

Indata för BES är som de indata som används av RRS. Den största skillnaden är att BES läser ett block med poster från olika källor, till exempel Azure Blob Storage, Azure Table Storage, Azure SQL Database, HDInsight (hive-fråga) och HTTP-källor.

Du kan distribuera din modell i tre steg från en överblick på hög nivå:

* **[Skapa ett utbildnings experiment]** – i Studio (klassisk) kan du träna och testa en förutsägelse analys modell med hjälp av utbildnings data som du tillhandahåller med hjälp av en stor uppsättning inbyggda Machine Learning-algoritmer.
* **[Konvertera det till ett förutsägande experiment]** – när din modell har tränats med befintliga data och du är redo att använda den för att skapa nya data, förbereder du och effektiviserar experimentet med förutsägelser.
* **Distribuera** den som en **[ny webb tjänst]** eller en **[klassisk webb tjänst]** – när du distribuerar ditt förutsägelse experiment som en Azure-webbtjänst kan användarna skicka data till din modell och ta emot modellens förutsägelser.

## <a name="create-a-training-experiment"></a>Skapa ett träningsexperiment

För att träna en förutsägelse analys modell använder du Azure Machine Learning Studio (klassisk) för att skapa ett utbildnings experiment där du inkluderar olika moduler för att läsa in tränings data, förbereda data vid behov, använda Machine Learning-algoritmer och utvärdera gör. Du kan iterera vidare till ett experiment och prova olika Machine Learning-algoritmer för att jämföra och utvärdera resultatet.

Processen för att skapa och hantera utbildnings experiment omfattas mer noggrant. Mer information finns i dessa artiklar:

* [Skapa ett enkelt experiment i Azure Machine Learning Studio (klassisk)](create-experiment.md)
* [Utveckla en förutsägelse lösning med Azure Machine Learning Studio (klassisk)](tutorial-part1-credit-risk.md)
* [Importera dina utbildnings data till Azure Machine Learning Studio (klassisk)](import-data.md)
* [Hantera experiment upprepningar i Azure Machine Learning Studio (klassisk)](manage-experiment-iterations.md)

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Konvertera träningsexperimentet till ett förutsägelseexperiment

När du har tränat din modell är du redo att konvertera ditt utbildnings experiment till ett förutsägelse experiment för att få nya data.

Genom att konvertera till ett förutsägelse experiment får du en utbildad modell som är redo att distribueras som en bedömnings webb tjänst. Användare av webb tjänsten kan skicka indata till din modell och din modell skickar tillbaka förutsägelse resultatet. När du konverterar till ett förutsägelse experiment bör du tänka på hur du förväntar dig att modellen ska användas av andra.

Om du vill konvertera ditt utbildnings experiment till ett förutsägande experiment klickar du på **Kör** längst ned i experiment arbets ytan, klickar på **Konfigurera webb tjänsten**och väljer sedan **förutsägbar webb tjänst**.

![Konvertera till poängsättnings experiment](./media/publish-a-machine-learning-web-service/figure-1.png)

Mer information om hur du utför den här konverteringen finns i [så här förbereder du modellen för distribution i Azure Machine Learning Studio (klassisk)](convert-training-experiment-to-scoring-experiment.md).

Följande steg beskriver hur du distribuerar ett förutsägelse experiment som en ny webb tjänst. Du kan också distribuera experimentet som den klassiska webb tjänsten.

## <a name="deploy-it-as-a-new-web-service"></a>Distribuera den som en ny webb tjänst

Nu när det förutsägande experimentet har förberetts kan du distribuera det som en ny (Resource Manager-baserad) Azure-webbtjänst. Med hjälp av webbtjänsten användare kan skicka data till din modell och modellen returnerar dess förutsägelser.

Om du vill distribuera ett förutsägelse experiment klickar du på **Kör** längst ned i experiment arbets ytan. När experimentet har körts klickar du på **distribuera webb tjänst** och väljer **distribuera webb tjänst [Nyårs]** .  Sidan distribution på webb tjänst portalen Machine Learning Studio (klassisk) öppnas.

> [!NOTE] 
> Om du vill distribuera en ny webbtjänst måste du ha tillräcklig behörighet i prenumerationen som du distribuerar webbtjänsten. Mer information finns i [hantera en webb tjänst med hjälp av Azure Machine Learning Web Services-portalen](manage-new-webservice.md). 

### <a name="web-service-portal-deploy-experiment-page"></a>Webb tjänst Portal för att distribuera experiment Sidan

På sidan distribuera experiment anger du ett namn för webb tjänsten.
Välj en pris sättnings plan. Om du har en befintlig pris plan kan du välja den, annars måste du skapa en ny pris plan för tjänsten.

1. Välj en befintlig plan i list rutan **pris plan** eller Välj alternativet **Välj ny plan** .
2. I **planerat namn**skriver du ett namn som identifierar planen på din faktura.
3. Välj en av de **månatliga plan nivåerna**. Planen får som standard nivåerna för planer i din standardregion och din webbtjänst distribueras till den regionen.

Klicka på **distribuera** och **snabb starts** sidan för din webb tjänst öppnas.

På sidan snabb start för webb tjänst får du till gång till och vägledning om de vanligaste åtgärderna som du kan utföra när du har skapat en webb tjänst. Härifrån kan du enkelt komma åt både test sidan och förbruknings sidan.

<!-- ![Deploy the web service](./media/publish-a-machine-learning-web-service/figure-2.png)-->

### <a name="test-your-new-web-service"></a>Testa din nya webb tjänst

Testa din nya webb tjänst genom att klicka på **test-webbtjänst** under vanliga uppgifter. På sidan test kan du testa din webb tjänst som en tjänst för begäran-svar (RR) eller en batch execution service (BES).

På test sidan för resurs poster visas indata, utdata och eventuella globala parametrar som du har definierat för experimentet. Om du vill testa webb tjänsten kan du manuellt ange lämpliga värden för indata eller ange en fil med kommaavgränsade värden (CSV) som innehåller test värden.

Om du vill testa att använda resurs namn, går du till listvyns visnings läge och anger lämpliga värden för indata och klickar på **test Request-Response**. Dina förutsägelse resultat visas i kolumnen utdata till vänster.

![Ange lämpliga värden för att testa din webb tjänst](./media/publish-a-machine-learning-web-service/figure-5-test-request-response.png)

Om du vill testa din BES klickar du på **batch**. På sidan batch-test klickar du på Bläddra under dina indata och väljer en CSV-fil som innehåller lämpliga exempel värden. Om du inte har en CSV-fil och du har skapat ditt förutsägelse experiment med Machine Learning Studio (klassisk) kan du hämta data uppsättningen för ditt förutsägelse experiment och använda den.

Öppna Machine Learning Studio (klassisk) om du vill hämta data uppsättningen. Öppna ditt förutsägelse experiment och högerklicka på indatamängden för experimentet. Välj **data uppsättning** på snabb menyn och välj sedan **Ladda ned**.

![Hämta data uppsättningen från Studio-arbetsytan (klassisk)](./media/publish-a-machine-learning-web-service/figure-7-mls-download.png)

Klicka på **testa**. Status för batch-körningen visas till höger under **testa batch-jobb**.

![Testa batch-körningen med webb tjänst portalen](./media/publish-a-machine-learning-web-service/figure-6-test-batch-execution.png)

<!--![Test the web service](./media/publish-a-machine-learning-web-service/figure-3.png)-->

På sidan **konfiguration** kan du ändra beskrivningen, rubriken, uppdatera lagrings konto nyckeln och aktivera exempel data för webb tjänsten.

![Konfigurera din webb tjänst](./media/publish-a-machine-learning-web-service/figure-8-arm-configure.png)

### <a name="access-your-new-web-service"></a>Få åtkomst till din nya webb tjänst

När du har distribuerat din webb tjänst från Machine Learning Studio (klassisk) kan du skicka data till tjänsten och ta emot svar program mässigt.

Sidan **förbrukning** innehåller all information som du behöver för att komma åt din webb tjänst. API-nyckeln tillhandahålls till exempel för att tillåta behörig åtkomst till tjänsten.

Mer information om hur du kommer åt en Machine Learning Studio (klassisk)-webb tjänst finns i [så här använder du en Azure Machine Learning Studio (klassisk)-webb tjänst](consume-web-services.md).

### <a name="manage-your-new-web-service"></a>Hantera din nya webb tjänst

Du kan hantera dina nya webb tjänster med hjälp av Machine Learning Studio (klassiska) webb tjänst portalen. Från [huvud Portal sidan](https://services.azureml.net/)klickar du på **webb tjänster**. På sidan webb tjänster kan du ta bort eller kopiera en tjänst. Om du vill övervaka en enskild tjänst klickar du på tjänsten och sedan på **instrument panel**. Om du vill övervaka batch-jobb som är associerade med webb tjänsten klickar du på **batch Request log**.

### <a id="multi-region"></a>Distribuera din nya webb tjänst till flera regioner

Du kan enkelt distribuera en ny webb tjänst till flera regioner utan att behöva flera prenumerationer eller arbets ytor.

Priser är landsspecifika, så du måste definiera en fakturerings plan för varje region där du ska distribuera webb tjänsten.

#### <a name="create-a-plan-in-another-region"></a>Skapa en plan i en annan region

1. Logga in på [Microsoft Azure Machine Learning-webbtjänster](https://services.azureml.net/).
2. Klicka på meny alternativet **planer** .
3. På sidan planer över vy klickar du på **ny**.
4. I list rutan **prenumeration** väljer du den prenumeration som den nya planen kommer att finnas i.
5. I list rutan **region** väljer du en region för den nya planen. Plan alternativen för den valda regionen visas i avsnittet **plan alternativ** på sidan.
6. I list rutan **resurs grupp** väljer du en resurs grupp för planen. Mer information om resurs grupper finns i [Azure Resource Manager översikt](../../azure-resource-manager/management/overview.md).
7. I **planerat namn** skriver du namnet på planen.
8. Under **plan alternativ**klickar du på fakturerings nivå för den nya planen.
9. Klicka på **Skapa**.

#### <a name="deploy-the-web-service-to-another-region"></a>Distribuera webb tjänsten till en annan region

1. På sidan Microsoft Azure Machine Learning webb tjänster klickar du på meny alternativet **webb tjänster** .
2. Välj den webbtjänst som du distribuerar till en ny region.
3. Klicka på **Kopiera**.
4. I **webb tjänstens namn**skriver du ett nytt namn för webb tjänsten.
5. I **Beskrivning av webb tjänst**anger du en beskrivning av webb tjänsten.
6. I list rutan **prenumeration** väljer du den prenumeration som den nya webb tjänsten kommer att finnas i.
7. I list rutan **resurs grupp** väljer du en resurs grupp för webb tjänsten. Mer information om resurs grupper finns i [Azure Resource Manager översikt](../../azure-resource-manager/management/overview.md).
8. I list rutan **region** väljer du den region där du vill distribuera webb tjänsten.
9. I list rutan **lagrings konto** väljer du ett lagrings konto där webb tjänsten ska lagras.
10. Välj en plan i den region som du valde i steg 8 i list rutan **pris plan** .
11. Klicka på **Kopiera**.

## <a name="deploy-it-as-a-classic-web-service"></a>Distribuera den som en klassisk webb tjänst

Nu när det förutsägande experimentet har varit tillräckligt för berett kan du distribuera det som en klassisk Azure-webbtjänst. Med hjälp av webbtjänsten användare kan skicka data till din modell och modellen returnerar dess förutsägelser.

Om du vill distribuera ett förutsägelse experiment klickar du på **Kör** längst ned i experiment arbets ytan och klickar sedan på **distribuera webb tjänst**. Webb tjänsten har kon figurer ATS och du placeras på instrument panelen för webb tjänster.

![Distribuera din webb tjänst från Studio (klassisk)](./media/publish-a-machine-learning-web-service/figure-2.png)

### <a name="test-your-classic-web-service"></a>Testa din klassiska webb tjänst

Du kan testa webb tjänsten antingen på den Machine Learning Studio (klassiska) webb tjänst portalen eller Machine Learning Studio (klassisk).

Testa webb tjänsten Request Response genom att klicka på knappen **test** på instrument panelen för webb tjänster. En dialog ruta öppnas där du uppmanas att ange indata för tjänsten. Detta är de kolumner som förväntas av poängsättnings experimentet. Ange en uppsättning data och klicka sedan på **OK**. De resultat som genereras av webb tjänsten visas längst ned på instrument panelen.

Du kan klicka på länken för att **testa** för hands versionen för att testa tjänsten på den Azure Machine Learning Studio (klassiska) webb tjänst portalen som tidigare visades i avsnittet ny webb tjänst.

Om du vill testa batch-körningen klickar du på **testa** för hands versions länk. På sidan batch-test klickar du på Bläddra under dina indata och väljer en CSV-fil som innehåller lämpliga exempel värden. Om du inte har en CSV-fil och du har skapat ditt förutsägelse experiment med Machine Learning Studio (klassisk) kan du hämta data uppsättningen för ditt förutsägelse experiment och använda den.

![Testa webbtjänsten](./media/publish-a-machine-learning-web-service/figure-3.png)

På sidan **konfiguration** kan du ändra visnings namnet för tjänsten och ge den en beskrivning. Namnet och beskrivningen visas i [Azure Portal](https://portal.azure.com/) där du hanterar dina webb tjänster.

Du kan ange en beskrivning av dina indata, utdata och webb tjänst parametrar genom att ange en sträng för varje kolumn under **inmatnings schema**, **utdata schema**och **webb tjänst parameter**. Dessa beskrivningar används i den exempel kod dokumentation som tillhandahålls för webb tjänsten.

Du kan aktivera loggning för att diagnostisera eventuella fel som du ser när din webb tjänst nås. Mer information finns i [Aktivera loggning för Machine Learning Studio (klassiska) webb tjänster](web-services-logging.md).

![Aktivera loggning i webb tjänst portalen](./media/publish-a-machine-learning-web-service/figure-4.png)

Du kan också konfigurera slut punkterna för webb tjänsten i Azure Machine Learning Web Services-portalen på samma sätt som i den nya webb tjänstens avsnitt. Alternativen skiljer sig åt, du kan lägga till eller ändra tjänst beskrivningen, aktivera loggning och aktivera exempel data för testning.

### <a name="access-your-classic-web-service"></a>Åtkomst till din klassiska webb tjänst

När du har distribuerat din webb tjänst från Azure Machine Learning Studio (klassisk) kan du skicka data till tjänsten och ta emot svar program mässigt.

Instrument panelen innehåller all information du behöver för att komma åt din webb tjänst. API-nyckeln tillhandahålls till exempel för att tillåta åtkomst till tjänsten, och API-hjälp sidor finns för att hjälpa dig att komma igång med att skriva din kod.

Mer information om hur du kommer åt en Machine Learning Studio (klassisk)-webb tjänst finns i [så här använder du en Azure Machine Learning Studio (klassisk)-webb tjänst](consume-web-services.md).

### <a name="manage-your-classic-web-service"></a>Hantera din klassiska webb tjänst

Det finns olika åtgärder som du kan utföra för att övervaka en webb tjänst. Du kan uppdatera den och ta bort den. Du kan också lägga till ytterligare slut punkter till en klassisk webb tjänst förutom standard slut punkten som skapas när du distribuerar den.

Mer information finns i [hantera en Azure Machine Learning Studio (klassisk) arbets yta](manage-workspace.md) och [hantera en webb tjänst med hjälp av webb tjänst portalen för Azure Machine Learning Studio (klassisk)](manage-new-webservice.md).

## <a name="update-the-web-service"></a>Uppdatera webbtjänsten
Du kan göra ändringar i webb tjänsten, till exempel uppdatera modellen med ytterligare utbildnings data, och distribuera den igen, och skriva över den ursprungliga webb tjänsten.

Om du vill uppdatera webb tjänsten öppnar du det ursprungliga förutsägelse experiment som du använde för att distribuera webb tjänsten och gör en redigerbar kopia genom att klicka på **Spara som**. Gör ändringarna och klicka sedan på **distribuera webb tjänst**.

Eftersom du har distribuerat det här experimentet tidigare tillfrågas du om du vill skriva över (klassisk webb tjänst) eller uppdatera (ny webb tjänst) den befintliga tjänsten. Om du klickar på **Ja** eller **Uppdatera** stoppas den befintliga webb tjänsten och det nya förutsägande experimentet distribueras på sitt ställe.

> [!NOTE]
> Om du har gjort konfigurations ändringar i den ursprungliga webb tjänsten, t. ex. genom att ange ett nytt visnings namn eller en beskrivning, måste du ange dessa värden igen.

Ett alternativ för att uppdatera din webb tjänst är att träna modellen program mässigt. Mer information finns i [omträna Machine Learning Studio (klassiska) modeller program mässigt](/azure/machine-learning/studio/retrain-machine-learning-model).

## <a name="next-steps"></a>Nästa steg

* Mer teknisk information om hur distribution fungerar finns i [hur en Machine Learning Studio (klassisk) modell går från ett experiment till en drifts webb tjänst](model-progression-experiment-to-web-service.md).

* Mer information om hur du förbereder din modell för distribution finns [i förbereda din modell för distribution i Azure Machine Learning Studio (klassisk)](convert-training-experiment-to-scoring-experiment.md).

* Du kan använda REST-API:et och komma åt webbtjänsten på flera sätt. Se [hur du använder en Azure Machine Learning Studio (klassisk)-webb tjänst](consume-web-services.md).

<!-- internal links -->
[Skapa ett utbildnings experiment]: #create-a-training-experiment
[Konvertera det till ett förutsägande experiment]: #convert-the-training-experiment-to-a-predictive-experiment
[Ny webb tjänst]: #deploy-it-as-a-new-web-service
[Klassisk webb tjänst]: #deploy-it-as-a-classic-web-service
[Nyårs]: #deploy-it-as-a-new-web-service
[classic]: #deploy-the-predictive-experiment-as-a-classic-web-service
[Access]: #access-the-Web-service
[Manage]: #manage-the-Web-service-in-the-azure-management-portal
[Update]: #update-the-Web-service
