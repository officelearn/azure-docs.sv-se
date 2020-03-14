---
title: Importera/exportera data i webb tjänster
titleSuffix: ML Studio (classic) - Azure
description: Lär dig hur du använder modulerna importera Data och exportera Data för att skicka och ta emot data från en webbtjänst.
services: machine-learning
author: xiaoharper
ms.custom: seodec18
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: 3a7ac351-ebd3-43a1-8c5d-18223903d08e
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: 144a3bc0d9e0499a238e4033d37d5e4d3fa61e05
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79204074"
---
# <a name="deploy-azure-machine-learning-studio-classic-web-services-that-use-data-import-and-data-export-modules"></a>Distribuera Azure Machine Learning Studio (klassiska) webb tjänster som använder moduler för data import och data export

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

När du skapar ett förutsägbart experiment kan du vanligtvis lägga till en web service indata och utdata. När du distribuerar experimentet kan konsumenter skicka och ta emot data från webbtjänsten via in- och utdata. För vissa program, en konsument data finnas i en datafeed eller redan finns i en extern datakälla, till exempel Azure Blob storage. I dessa fall kan behöver de inte läsa och skriva data med hjälp av web service indata och utdata. De kan i stället använda BES Batch Execution Service () för att läsa data från datakällan med hjälp av en modul för importera Data och skriva bedömnings resultaten till en annan plats med hjälp av en modul för Exportera Data.

Importera Data och exportera data moduler kan läsa från och skriva till olika data platser, till exempel en URL för via HTTP, en Hive-fråga, en Azure SQL-databas, Azure Table storage, Azure Blob storage, en Datafeed ger eller en lokal SQL-databas.

Det här avsnittet använder de ”exempel 5: Train, Test, utvärdera för binär klassificering: vuxna datauppsättningen” exempel och antar datauppsättningen har redan lästs in i en Azure SQL-tabell som heter censusdata.

## <a name="create-the-training-experiment"></a>Skapa träningsexperimentet
När du öppnar den ”exempel 5: träna, testa, utvärdera för binär klassificering: vuxna datauppsättningen” exempel som är olämpligt för barn insamlade binära Intäktsklassificering exempeldatauppsättningen används. Och experiment i arbetsytan ser ut ungefär som följande bild:

![Inledande konfiguration av experimentet.](./media/web-services-that-use-import-export-modules/initial-look-of-experiment.png)

Att läsa data från Azure SQL-tabell:

1. Ta bort modulen datauppsättning.
2. Skriv i sökrutan komponenter import.
3. Lägg till en modul för att *Importera data* till arbets ytan för experimentet i resultat listan.
4. Anslut utdata från modulen *Importera data* indata för modulen *Rensa data som saknas* .
5. I fönstret Egenskaper väljer du **Azure SQL Database** i list rutan **data källa** .
6. I fälten **databas server namn**, **databas namn**, **användar namn**och **lösen ord** anger du lämplig information för databasen.
7. Ange följande fråga i fältet Database-fråga.

     Välj [ålder]

        [workclass],
        [fnlwgt],
        [education],
        [education-num],
        [marital-status],
        [occupation],
        [relationship],
        [race],
        [sex],
        [capital-gain],
        [capital-loss],
        [hours-per-week],
        [native-country],
        [income]
     från dbo.censusdata;
8. Klicka på **Kör**längst ned i experiment arbets ytan.

## <a name="create-the-predictive-experiment"></a>Skapa förutsägbart experiment
Därefter konfigurerar du förutsägbart experiment som du distribuerar din webbtjänst.

1. Klicka på **Konfigurera webb tjänst** i slutet av experiment arbets ytan och välj **förutsägbar webb tjänst [Recommended]** .
2. Ta bort modulerna för *indata för webb tjänsten* och *webb tjänstens utdata* från det förutsägande experimentet.
3. Skriv i sökrutan komponenter export.
4. Lägg till en modul för att *Exportera data* till experimentets arbets yta i resultat listan.
5. Anslut utdata från modulen *Poäng modell* indata för modulen *Exportera data* .
6. I fönstret Egenskaper väljer du **Azure SQL Database** i list rutan data mål.
7. I fälten **databas server namn**, **databas namn**, **serverns användar konto namn**och **lösen ord för serverns användar konto** anger du lämplig information för databasen.
8. Skriv betygs etiketter i den **kommaavgränsade listan över kolumner som ska sparas** .
9. I **fältet data tabell namn**skriver du dbo. ScoredLabels. Om tabellen inte finns skapas den när experimentet har körts eller webbtjänsten anropas.
10. I fältet **kommaavgränsad lista över DataTable-kolumner** skriver du ScoredLabels.

När du skriver ett program som anropar den slutliga webbtjänsten du anger en annan frågan eller måltabellen vid körning. Om du vill konfigurera dessa indata och utdata använder du funktionen för webb tjänst parametrar för att ange *data källans egenskap för data källa* i modulen *Importera data* och egenskapen *Exportera* Dataläge.  Mer information om parametrarna för webb tjänsten finns i [posten Azure Machine Learning Studio Web Service Parameters](https://blogs.technet.microsoft.com/machinelearning/2014/11/25/azureml-web-service-parameters/) på Cortana Intelligence och Machine Learning blogg.

Konfigurera Webbtjänstparametrar för import-frågan och måltabellen:

1. I rutan Egenskaper för modulen *Importera data* klickar du på ikonen längst upp till höger i fältet **databas fråga** och väljer **Ange som webb tjänst parameter**.
2. I fönstret Egenskaper för modulen *Exportera data* klickar du på ikonen längst upp till höger i fältet **data tabell namn** och väljer **Ange som webb tjänst parameter**.
3. Längst ned i fönstret Egenskaper för *Exportera* datamodul, i avsnittet **webb tjänst parametrar** , klickar du på databas fråga och byter namn på frågan.
4. Klicka på **data tabell namn** och Byt namn på **tabellen**.

När du är klar bör experimentet likna följande bild:

![Sista utseende med experimentet.](./media/web-services-that-use-import-export-modules/experiment-with-import-data-added.png)

Du kan nu distribuera experimentet som en webbtjänst.

## <a name="deploy-the-web-service"></a>Distribuera webbtjänsten
Du kan distribuera till en klassisk eller en ny webbtjänst.

### <a name="deploy-a-classic-web-service"></a>Distribuera en klassiska webbtjänst
Du distribuerar som en klassiska webbtjänst och skapar ett program att använda den:

1. Klicka på Kör längst ned på arbetsytan för experimentet.
2. När körningen har slutförts klickar du på **distribuera webb tjänst** och väljer **distribuera webb tjänst [klassisk]** .
3. På instrumentpanelen för webbtjänsten, letar du upp din API-nyckel. Kopiera och spara den för senare användning.
4. I **standard slut punkts** tabellen klickar du på länken för **batch-körning** för att öppna API-hjälp sidan.
5. I Visual Studio skapar du ett C# konsol program: **nytt** > **projekt** > **visuella C#**  > **Windows klassisk Desktop** >  **-konsol (.NET Framework)** .
6. På sidan API-hjälp hittar du avsnittet **exempel kod** längst ned på sidan.
7. Kopiera och klistra in den C# exempelkoden i filen Program.cs och ta bort alla referenser till blob-lagringen.
8. Uppdatera värdet för variabeln *apiKey* med API-nyckeln Sparad tidigare.
9. Leta upp deklarationen för begäran och uppdatera värdena för webb tjänst parametrar som skickas till modulerna *Importera data* och *Exportera data* . I det här fallet du använda den ursprungliga frågan, men definiera ett nytt tabellnamn.

        var request = new BatchExecutionRequest()
        {
            GlobalParameters = new Dictionary<string, string>() {
                { "Query", @"select [age], [workclass], [fnlwgt], [education], [education-num], [marital-status], [occupation], [relationship], [race], [sex], [capital-gain], [capital-loss], [hours-per-week], [native-country], [income] from dbo.censusdata" },
                { "Table", "dbo.ScoredTable2" },
            }
        };
10. Kör programmet.

Körningen har slutförts, är en ny tabell läggs till i databasen som innehåller bedömnings resultatet.

### <a name="deploy-a-new-web-service"></a>Distribuera en ny webbtjänst

> [!NOTE]
> Om du vill distribuera en ny webbtjänst måste du ha tillräcklig behörighet i prenumerationen som du distribuerar webbtjänsten. Mer information finns i [hantera en webb tjänst med hjälp av Azure Machine Learning Web Services-portalen](manage-new-webservice.md).

Distribuera som en ny webbtjänst och skapa ett program att använda den:

1. Klicka på **Kör**längst ned i experiment arbets ytan.
2. När körningen har slutförts klickar du på **distribuera webb tjänst** och väljer **distribuera webb tjänst [ny]** .
3. På sidan distribuera experiment anger du ett namn för din webb tjänst och väljer en pris plan och klickar sedan på **distribuera**.
4. Klicka på **förbruka**på sidan **snabb start** .
5. I avsnittet **exempel kod** klickar du på **batch**.
6. I Visual Studio skapar du ett C# konsol program: **nytt** > **projekt** > **visuella C#**  > **Windows klassisk Desktop** >  **-konsol (.NET Framework)** .
7. Kopiera och klistra in den C# exempelkoden i filen Program.cs.
8. Uppdatera värdet för variabeln *apiKey* med **primär nyckeln** som finns i avsnittet **grundläggande förbruknings information** .
9. Leta upp *ScoreRequest* -deklarationen och uppdatera värdena för webb tjänst parametrar som skickas till modulerna *Importera data* och *Exportera data* . I det här fallet du använda den ursprungliga frågan, men definiera ett nytt tabellnamn.

        var scoreRequest = new
        {
            Inputs = new Dictionary<string, StringTable>()
            {
            },
            GlobalParameters = new Dictionary<string, string>() {
                { "Query", @"select [age], [workclass], [fnlwgt], [education], [education-num], [marital-status], [occupation], [relationship], [race], [sex], [capital-gain], [capital-loss], [hours-per-week], [native-country], [income] from dbo.censusdata" },
                { "Table", "dbo.ScoredTable3" },
            }
        };
10. Kör programmet.

