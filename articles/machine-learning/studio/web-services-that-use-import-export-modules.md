---
title: Importera/exportera data i webbtjänster
titleSuffix: ML Studio (classic) - Azure
description: Lär dig hur du använder modulerna Importera data och exportera data för att skicka och ta emot data från en webbtjänst.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204074"
---
# <a name="deploy-azure-machine-learning-studio-classic-web-services-that-use-data-import-and-data-export-modules"></a>Distribuera Azure Machine Learning Studio (klassiska) webbtjänster som använder moduler för dataimport och dataexport

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

När du skapar ett förutsägelseexperiment lägger du vanligtvis till en webbtjänstinmatning och utdata. När du distribuerar experimentet kan konsumenter skicka och ta emot data från webbtjänsten via indata och utdata. För vissa program kan en konsuments data vara tillgängliga från en datafeed eller redan finnas i en extern datakälla, till exempel Azure Blob-lagring. I dessa fall behöver de inte läsa och skriva data med hjälp av webbtjänstindata och utdata. De kan i stället använda BES (Batch Execution Service) för att läsa data från datakällan med hjälp av en importdatamodul och skriva bedömningsresultaten till en annan dataplats med hjälp av en exportdatamodul.

Datamodulerna Importera data och exporterar, kan läsa från och skriva till olika dataplatser, till exempel en webb-URL via HTTP, en Hive-fråga, en Azure SQL-databas, Azure Table storage, Azure Blob storage, a Data Feed provide eller en lokal SQL-databas.

I det här avsnittet används exemplet "Exempel 5: Train, Test, Evaluate for Binary Classification: Adult Dataset" och antar att datauppsättningen redan har lästs in i en Azure SQL-tabell med namnet censusdata.

## <a name="create-the-training-experiment"></a>Skapa träningsexperimentet
När du öppnar exemplet "Prov 5: Tåg, Test, Utvärdera för binär klassificering: Vuxen datauppsättning" använder det exempel adult census inkomst binär klassificering dataset. Och experimentet på arbetsytan kommer att se ut ungefär som följande bild:

![Inledande konfiguration av experimentet.](./media/web-services-that-use-import-export-modules/initial-look-of-experiment.png)

Så här läser du data från Azure SQL-tabellen:

1. Ta bort datauppsättningsmodulen.
2. Skriv import i sökrutan komponenter.
3. Lägg till en *importdatamodul* i experimentarbetsytan i resultatlistan.
4. Anslut utdata från *modulen Importera data* indata från modulen *Rensa data som saknas.*
5. I egenskapsfönstret väljer du **Azure SQL Database** i listrutan **Datakälla.**
6. Ange lämplig information för databasen i fälten **Databasservernamn,** **Databasnamn** **,** användarnamn och **Lösenord.**
7. Ange följande fråga i frågefältet Databas.

     välj [ålder],

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
8. Klicka på **Kör**längst ned på experimentarbetsytan.

## <a name="create-the-predictive-experiment"></a>Skapa förutsägelseexperimentet
Därefter ställer du in det förutsägelseexperiment som du distribuerar webbtjänsten från.

1. Längst ned på experimentarbetsytan klickar du på **Konfigurera webbtjänst** och väljer **Förutsägande webbtjänst [Rekommenderas]**.
2. Ta bort *modulerna För inmatning* och *webbtjänstutdata* från förutsägelseexperimentet.
3. Skriv export i sökrutan komponenter.
4. Lägg till en *exportdatamodul* i experimentarbetsytan i resultatlistan.
5. Anslut utdata från *poängmodellmodulen* indata från modulen *Exportera data.*
6. I egenskapsfönstret väljer du **Azure SQL Database** i listrutan för datamål.
7. Ange lämplig information för databasen i **databasservernamnet,** **databasnamn,** **serveranvändarkontonamn**och lösenord för **serveranvändarkonto.**
8. Skriv Poängsatta etiketter i **listan Kommaavgränsad med kolumner som ska sparas.**
9. Skriv dbo i **fältet Datatabellnamn.** GjordeLabels. Om tabellen inte finns skapas den när experimentet körs eller webbtjänsten anropas.
10. Skriv ScoredLabels i **listan Kommaavgränsade med datatablekolumner.**

När du skriver ett program som anropar den slutliga webbtjänsten kanske du vill ange en annan indatafråga eller måltabell vid körning. Om du vill konfigurera dessa in- och utdata använder du funktionen Parametrar för webbtjänst för att ange egenskapen *Importera datakälla* *och* egenskapen *Export Data* Mode data destination.  Mer information om parametrar för webbtjänst finns i [posten Azure Machine Learning Studio Web Service Parameters](https://blogs.technet.microsoft.com/machinelearning/2014/11/25/azureml-web-service-parameters/) på Cortana Intelligence and Machine Learning Blog.

Så här konfigurerar du webbtjänstparametrarna för importfrågan och måltabellen:

1. Klicka på ikonen längst upp till höger i **fältet Databasfråga** i egenskapsfönstret för modulen *Importera data* och välj Ange **som webbtjänstparameter**.
2. Klicka på ikonen längst upp till höger i **fältet Datatabellnamn** i egenskapsfönstret för modulen *Exportera data* och välj Ange **som webbtjänstparameter**.
3. Klicka på Databasfråga i avsnittet **Webbtjänstparametrar** längst ned i egenskapsfönstret *Exportdatamodul* och byt namn på den.
4. Klicka på **Datatabellnamn** och byt namn på den **Tabell**.

När du är klar bör experimentet se ut ungefär som följande bild:

![Sista utseendet på experimentet.](./media/web-services-that-use-import-export-modules/experiment-with-import-data-added.png)

Nu kan du distribuera experimentet som en webbtjänst.

## <a name="deploy-the-web-service"></a>Distribuera webbtjänsten
Du kan distribuera till en klassisk eller ny webbtjänst.

### <a name="deploy-a-classic-web-service"></a>Distribuera en klassisk webbtjänst
Så här distribuerar du som en klassisk webbtjänst och skapar ett program för att använda den:

1. Klicka på Kör längst ned på experimentarbetsytan.
2. När körningen är klar klickar du på **Distribuera webbtjänst** och väljer **Distribuera webbtjänst [Klassisk]**.
3. Leta reda på API-nyckeln på instrumentpanelen för webbtjänsten. Kopiera och spara den för att använda senare.
4. Öppna **api-hjälpsidan** i tabellen **Standardslutpunkt.**
5. Skapa ett C#-konsolprogram i Visual Studio: **New** > **Project** > **Visual C#** > **Windows Classic Desktop** > **Console App (.NET Framework).**
6. Leta reda på avsnittet **Exempelkod** längst ned på sidan på API-hjälpsidan.
7. Kopiera och klistra in C#-exempelkoden i Program.cs filen och ta bort alla referenser till blob-lagringen.
8. Uppdatera värdet för *apiKey-variabeln* med API-nyckeln som sparats tidigare.
9. Leta reda på deklarationen för begäran och uppdatera värdena för webbtjänstparametrar som skickas till modulerna *Importera data* och *exportera data.* I det här fallet använder du den ursprungliga frågan, men definierar ett nytt tabellnamn.

        var request = new BatchExecutionRequest()
        {
            GlobalParameters = new Dictionary<string, string>() {
                { "Query", @"select [age], [workclass], [fnlwgt], [education], [education-num], [marital-status], [occupation], [relationship], [race], [sex], [capital-gain], [capital-loss], [hours-per-week], [native-country], [income] from dbo.censusdata" },
                { "Table", "dbo.ScoredTable2" },
            }
        };
10. Kör appen.

När körningen är klar läggs en ny tabell till i databasen som innehåller poängresultaten.

### <a name="deploy-a-new-web-service"></a>Distribuera en ny webbtjänst

> [!NOTE]
> Om du vill distribuera en ny webbtjänst måste du ha tillräcklig behörighet i prenumerationen som du distribuerar webbtjänsten till. Mer information finns i [Hantera en webbtjänst med hjälp av Portalen för Azure Machine Learning Web Services](manage-new-webservice.md).

Så här distribuerar du som en ny webbtjänst och skapar ett program för att använda den:

1. Klicka på **Kör**längst ned på experimentarbetsytan.
2. När körningen är klar klickar du på **Distribuera webbtjänst** och väljer **Distribuera webbtjänst [Ny]**.
3. På sidan Distribuera experiment anger du ett namn på webbtjänsten och väljer en prisplan och klickar sedan på **Distribuera**.
4. Klicka på **Förbruka**på sidan **Snabbstart.**
5. Klicka på **Batch**i avsnittet **Exempelkod** .
6. Skapa ett C#-konsolprogram i Visual Studio: **New** > **Project** > **Visual C#** > **Windows Classic Desktop** > **Console App (.NET Framework).**
7. Kopiera och klistra in C#-exempelkoden i Program.cs-filen.
8. Uppdatera värdet för *apiKey-variabeln* med **primärnyckeln** i avsnittet **Grundläggande förbrukningsinformation.**
9. Leta reda på *scoreRequest-deklarationen* och uppdatera värdena för webbtjänstparametrar som skickas till modulerna *Importera data* och *exportera data.* I det här fallet använder du den ursprungliga frågan, men definierar ett nytt tabellnamn.

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
10. Kör appen.

