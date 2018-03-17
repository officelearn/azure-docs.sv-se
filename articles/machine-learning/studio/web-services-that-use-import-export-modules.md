---
title: "Med hjälp av Import/Export av Data i Azure Machine Learning-webbtjänster | Microsoft Docs"
description: "Lär dig hur du använder modulerna importera Data och exportera Data för att skicka och ta emot data från en webbtjänst."
services: machine-learning
documentationcenter: 
author: aashishb
ms.author: aashishb
manager: hjerez
editor: 
ms.assetid: 3a7ac351-ebd3-43a1-8c5d-18223903d08e
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2017
ms.openlocfilehash: 310ace274649faab3c39ea89dafd2f29dea44109
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/17/2018
---
# <a name="deploying-azure-ml-web-services-that-use-data-import-and-data-export-modules"></a>Distribuera Azure ML-webbtjänster som använder moduler för dataimport och dataexport

När du skapar en prediktivt experiment kan du vanligtvis lägga till en webbtjänst och utgående. När du distribuerar experimentet kan konsumenterna skicka och ta emot data från webbtjänsten via in- och utdataenheter. För vissa program, kan en konsument vara tillgänglig från en datafeed eller redan finns i en extern datakälla, till exempel Azure Blob storage. I dessa fall kan behöver de inte läsa och skriva data med hjälp av web service in- och utdataenheter. De kan i stället använda Batch Execution Service (BES) för att läsa data från datakällan modulen importera Data och skriva bedömningsprofil resultaten till en annan plats med hjälp av en modul exportera Data.

Importera Data och exportera data moduler kan läsa från och skriva till olika uppgifter platser, till exempel en URL via HTTP, en Hive-fråga, en Azure SQL-databas, Azure Table storage, Azure Blob storage Data Feed tillhandahåller eller en lokal SQL-databas.

Det här avsnittet använder den ”exempel 5: tåg och Test, utvärdera för binär klassificering: vuxna Dataset” exempel och förutsätter datamängden har redan lästs in i en Azure SQL-tabell med namnet censusdata.

## <a name="create-the-training-experiment"></a>Skapa utbildning experiment
När du öppnar den ”exempel 5: tåg och Test, utvärdera för binär klassificering: vuxna Dataset” exempel exempel vuxna inventering intäkter binär klassificering dataset används. Och experiment på arbetsytan kommer att likna följande bild:

![Inledande konfiguration av experimentet.](./media/web-services-that-use-import-export-modules/initial-look-of-experiment.png)

Att läsa data från Azure SQL-tabellen:

1. Ta bort modulen dataset.
2. Skriv i sökrutan komponenter import.
3. Resultatlistan lägga till en *importera Data* modulen till experimentarbetsytan.
4. Ansluta utdata från den *importera Data* modulen indata för den *Rensa Data som saknas* modul.
5. Välj i egenskapsrutan, **Azure SQL Database** i den **datakällan** listrutan.
6. I den **Databasservernamnet**, **databasnamnet**, **användarnamn**, och **lösenord** ange lämplig information för din databas.
7. Ange följande fråga i fältet databasen frågan.
   
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
     from dbo.censusdata;
8. Längst ned i arbetsytan för experimentet klickar du på **kör**.

## <a name="create-the-predictive-experiment"></a>Skapa prediktivt experiment
Nästa ställa in prediktivt experiment som du distribuerar webbtjänsten.

1. Längst ned i arbetsytan för experimentet klickar du på **konfigurera Web Service** och välj **förutsägande webbtjänst (rekommenderas)**.
2. Ta bort den *webbtjänst* och *Web Service utdata moduler* från prediktivt experiment. 
3. Skriv i sökrutan komponenter export.
4. Resultatlistan lägga till en *exportera Data* modulen till experimentarbetsytan.
5. Ansluta utdata från den *Poängmodell* modulen indata för den *exportera Data* modul. 
6. Välj i egenskapsrutan, **Azure SQL Database** i den nedrullningsbara listrutan mål data.
7. I den **Databasservernamnet**, **databasnamnet**, **Server användarkontonamnet**, och **serverlösenord** anger den information om din databas.
8. I den **kommaavgränsad lista med kolumner som ska sparas** anger poängsatta etiketter.
9. I den **Data tabell namnfältet**, Skriv dbo. ScoredLabels. Om tabellen inte finns, skapas den när experimentet körs eller webbtjänsten anropas.
10. I den **kommaavgränsad lista med kolumner som datatable** anger ScoredLabels.

När du skriver ett program som anropar slutliga webbtjänsten kanske du vill ange en annan indatafrågan eller måltabellen vid körning. För att konfigurera dessa indata och utdata, använder du funktionen Webbtjänstparametrar för att ange den *importera Data* modulen *datakällan* egenskapen och *exportera Data* läge data mål-egenskap.  Mer information om Webbtjänstparametrar finns i [AzureML Webbtjänstparametrar post](https://blogs.technet.microsoft.com/machinelearning/2014/11/25/azureml-web-service-parameters/) på Cortana Intelligence och Machine Learning-bloggen.

Konfigurera Webbtjänstparametrar för import-fråga och måltabellen:

1. I egenskapsfönstret för den *importera Data* modulen, klickar du på ikonen överst i den **databasfrågan** fältet och välj **anges som parameter för web service**.
2. I egenskapsfönstret för den *exportera Data* modulen, klickar du på ikonen överst i den **Data tabellnamn** fältet och välj **anges som parameter för web service**.
3. Längst ned i den *exportera Data* egenskapsrutan modulen i den **Webbtjänstparametrar** , på databasfrågan och byta namn på den frågan.
4. Klicka på **Data tabellnamn** och Byt namn på den **tabellen**.

När du är klar bör experimentet likna följande bild:

![Sista utseendet på experimentet.](./media/web-services-that-use-import-export-modules/experiment-with-import-data-added.png)

Du kan nu distribuera experimentet som en webbtjänst.

## <a name="deploy-the-web-service"></a>Distribuera webbtjänsten
Du kan distribuera till en klassisk eller en ny webbtjänst.

### <a name="deploy-a-classic-web-service"></a>Distribuera en klassiska webbtjänst
Att distribuera som en klassiska webbtjänst och skapa ett program att använda den:

1. Klicka på Kör längst ned på arbetsytan för experimentet.
2. När körningen har slutförts klickar du på **distribuera webbtjänsten** och välj **distribuera webbtjänsten [klassisk]**.
3. Leta upp din API-nyckel på infopanelen web service. Kopiera och spara den för senare användning.
4. I den **standard Endpoint** tabell genom att klicka på den **Batch Execution** länk för att öppna sidan API.
5. Skapa ett C#-konsolprogram i Visual Studio: **ny** > **projekt** > **Visual C#** > **Windows Klassiska Desktop** > **konsolen App (.NET Framework)**.
6. På sidan API finns i **exempelkod** avsnittet längst ned på sidan.
7. Kopiera och klistra in exempelkoden C# i Program.cs-filen och ta bort alla referenser till blob storage.
8. Uppdatera värdet för den *apiKey* variabeln med API-nyckeln som sparats tidigare.
9. Leta reda på begäran-deklarationen och uppdatera värdena för Webbtjänstparametrar som skickas till den *importera Data* och *exportera Data* moduler. I så fall måste du använda den ursprungliga frågan, men definiera ett nytt tabellnamn.
   
        var request = new BatchExecutionRequest() 
        {           
            GlobalParameters = new Dictionary<string, string>() {
                { "Query", @"select [age], [workclass], [fnlwgt], [education], [education-num], [marital-status], [occupation], [relationship], [race], [sex], [capital-gain], [capital-loss], [hours-per-week], [native-country], [income] from dbo.censusdata" },
                { "Table", "dbo.ScoredTable2" },
            }
        };
10. Kör appen. 

En ny tabell har lagts till den databas som innehåller bedömningsprofil resultaten på slutförande av körningen.

### <a name="deploy-a-new-web-service"></a>Distribuera en ny webbtjänst

> [!NOTE] 
> Om du vill distribuera en ny webbtjänst måste du ha tillräckliga behörigheter i prenumerationen som du distribuerar webbtjänsten. Mer information finns i [hantera en webbtjänst med hjälp av Azure Machine Learning-webbtjänster portal](manage-new-webservice.md). 

Distribuera som en ny webbtjänst och skapa ett program att använda den:

1. Längst ned i arbetsytan för experimentet klickar du på **kör**.
2. När körningen har slutförts klickar du på **distribuera webbtjänsten** och välj **distribuera webbtjänsten [ny]**.
3. Ange ett namn för webbtjänsten, på sidan distribuera Experiment och välja en prisavtal, och klicka sedan **distribuera**.
4. På den **Quickstart** klickar du på **förbruka**.
5. I den **exempelkod** klickar du på **Batch**.
6. Skapa ett C#-konsolprogram i Visual Studio: **ny** > **projekt** > **Visual C#** > **Windows Klassiska Desktop** > **konsolen App (.NET Framework)**.
7. Kopiera och klistra in exempelkoden C# i filen Program.cs.
8. Uppdatera värdet för den *apiKey* variabeln med den **primärnyckel** finns i den **grundläggande förbrukning info** avsnitt.
9. Leta upp den *scoreRequest* deklaration och uppdatera värdena för Webbtjänstparametrar som skickas till den *importera Data* och *exportera Data* moduler. I så fall måste du använda den ursprungliga frågan, men definiera ett nytt tabellnamn.
   
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

