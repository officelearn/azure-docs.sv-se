---
title: Import/Export-Data i web services - Azure Machine Learning Studio | Microsoft Docs
description: Lär dig hur du använder modulerna importera Data och exportera Data för att skicka och ta emot data från en webbtjänst.
services: machine-learning
documentationcenter: ''
author: xiaoharper
ms.custom: seodec18
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: 3a7ac351-ebd3-43a1-8c5d-18223903d08e
ms.service: machine-learning
ms.subservice: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: 28d16bce6dbb5063c085e8c4393777ee9d152768
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60345149"
---
# <a name="deploy-azure-machine-learning-studio-web-services-that-use-data-import-and-data-export-modules"></a>Distribuera Azure Machine Learning Studio-webbtjänster som använder moduler för dataimport och dataexport

När du skapar ett förutsägbart experiment kan du vanligtvis lägga till en web service indata och utdata. När du distribuerar experimentet kan konsumenter skicka och ta emot data från webbtjänsten via in- och utdata. För vissa program, en konsument data finnas i en datafeed eller redan finns i en extern datakälla, till exempel Azure Blob storage. I dessa fall kan behöver de inte läsa och skriva data med hjälp av web service indata och utdata. De kan i stället använda BES Batch Execution Service () för att läsa data från datakällan med hjälp av en modul för importera Data och skriva bedömnings resultaten till en annan plats med hjälp av en modul för Exportera Data.

Importera Data och exportera data moduler kan läsa från och skriva till olika data platser, till exempel en URL för via HTTP, en Hive-fråga, en Azure SQL-databas, Azure Table storage, Azure Blob storage, en Datafeed ger eller en lokal SQL-databas.

Det här avsnittet använder det ”exempel 5: Träna, testa, utvärdera för binär klassificering: Vuxet datauppsättningen ”exempel och antar datauppsättningen har redan lästs in i en Azure SQL-tabell som heter censusdata.

## <a name="create-the-training-experiment"></a>Skapa träningsexperimentet
När du öppnar den ”exempel 5: Träna, testa, utvärdera för binär klassificering: Vuxet datauppsättningen ”exempel vuxet insamlade binära Intäktsklassificering exempeldatauppsättningen används. Och experiment i arbetsytan ser ut ungefär som följande bild:

![Inledande konfiguration av experimentet.](./media/web-services-that-use-import-export-modules/initial-look-of-experiment.png)

Att läsa data från Azure SQL-tabell:

1. Ta bort modulen datauppsättning.
2. Skriv i sökrutan komponenter import.
3. Lägg till från listan med resultat en *importdata* modulen till experimentarbetsytan.
4. Ansluta utdata från den *importdata* modulen indata för den *Rensa Data som saknas* modulen.
5. I egenskapsfönstret väljer **Azure SQL Database** i den **datakälla** listrutan.
6. I den **Databasservernamnet**, **databasnamn**, **användarnamn**, och **lösenord** fält, anger du informationen som krävs för din -databasen.
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
8. Längst ned på arbetsytan för experimentet klickar du på **kör**.

## <a name="create-the-predictive-experiment"></a>Skapa förutsägbart experiment
Därefter konfigurerar du förutsägbart experiment som du distribuerar din webbtjänst.

1. Längst ned på arbetsytan för experimentet klickar du på **konfigurera Web Service** och välj **förutsägande webbtjänst (rekommenderas)**.
2. Ta bort den *Webbtjänstindata* och *Web Service utdata moduler* från förutsägbart experiment.
3. Skriv i sökrutan komponenter export.
4. Lägg till från listan med resultat en *exportera Data* modulen till experimentarbetsytan.
5. Ansluta utdata från den *Poängmodell* modulen indata för den *exportera Data* modulen.
6. I egenskapsfönstret väljer **Azure SQL Database** i listrutan för data-mål.
7. I den **Databasservernamnet**, **databasnamn**, **Server användarkontonamn**, och **Server lösenord** fält, anger du den rätt information för din databas.
8. I den **kommaavgränsad lista över kolumner som ska sparas** skriver poängsatta etiketter.
9. I den **Data tabell namnfältet**, Skriv dbo. ScoredLabels. Om tabellen inte finns skapas den när experimentet har körts eller webbtjänsten anropas.
10. I den **kommaavgränsad lista över datatable kolumner** anger ScoredLabels.

När du skriver ett program som anropar den slutliga webbtjänsten du anger en annan frågan eller måltabellen vid körning. Om du vill konfigurera dessa indata och utdata, använder du funktionen Webbtjänstparametrar att ställa in den *importera Data* modulen *datakälla* egenskapen och *exportera Data* läge data mål-egenskapen.  Mer information om Webbtjänstparametrar finns i den [Azure Machine Learning studio Webbtjänstparametrar post](https://blogs.technet.microsoft.com/machinelearning/2014/11/25/azureml-web-service-parameters/) på Cortana Intelligence och Machine Learning Blog.

Konfigurera Webbtjänstparametrar för import-frågan och måltabellen:

1. I egenskapsfönstret för den *importera Data* modulen, klickar du på ikonen längst upp höger på den **databasfråga** fältet och välj **som web service parametern**.
2. I egenskapsfönstret för den *exportera Data* modulen, klickar du på ikonen längst upp höger på den **Data tabellnamn** fältet och välj **som web service parametern**.
3. Längst ned på den *exportera Data* modulen egenskapsrutan i den **Webbtjänstparametrar** , på databasfråga och Byt namn på den frågan.
4. Klicka på **Data tabellnamn** och Byt namn på den **tabell**.

När du är klar bör experimentet likna följande bild:

![Sista utseende med experimentet.](./media/web-services-that-use-import-export-modules/experiment-with-import-data-added.png)

Du kan nu distribuera experimentet som en webbtjänst.

## <a name="deploy-the-web-service"></a>Distribuera webbtjänsten
Du kan distribuera till en klassisk eller en ny webbtjänst.

### <a name="deploy-a-classic-web-service"></a>Distribuera en klassiska webbtjänst
Du distribuerar som en klassiska webbtjänst och skapar ett program att använda den:

1. Klicka på Kör längst ned på arbetsytan för experimentet.
2. När körningen har slutförts klickar du på **distribuera webbtjänsten** och välj **distribuera webbtjänsten [klassisk]**.
3. På instrumentpanelen för webbtjänsten, letar du upp din API-nyckel. Kopiera och spara den för senare användning.
4. I den **standard-slutpunkt** tabellen, klickar du på den **batchkörning** länk för att öppna API-hjälpsidan.
5. I Visual Studio skapar du en C# konsolapp: **Ny** > **projekt** > **Visual C#**   >  **Windows Classic Desktop**  >   **Konsolprogram (.NET Framework)**.
6. På sidan API hitta den **exempelkoden** avsnittet längst ned på sidan.
7. Kopiera och klistra in den C# exempelkoden i filen Program.cs och ta bort alla referenser till blob-lagringen.
8. Uppdatera värdet för den *apiKey* variabeln med API-nyckel som sparats tidigare.
9. Leta reda på begäran-deklarationen och uppdaterar värdet för Webbtjänstparametrar som skickas till den *importdata* och *exportera Data* moduler. I det här fallet du använda den ursprungliga frågan, men definiera ett nytt tabellnamn.

        var request = new BatchExecutionRequest()
        {
            GlobalParameters = new Dictionary<string, string>() {
                { "Query", @"select [age], [workclass], [fnlwgt], [education], [education-num], [marital-status], [occupation], [relationship], [race], [sex], [capital-gain], [capital-loss], [hours-per-week], [native-country], [income] from dbo.censusdata" },
                { "Table", "dbo.ScoredTable2" },
            }
        };
10. Kör appen.

Körningen har slutförts, är en ny tabell läggs till i databasen som innehåller bedömnings resultatet.

### <a name="deploy-a-new-web-service"></a>Distribuera en ny webbtjänst

> [!NOTE]
> Om du vill distribuera en ny webbtjänst måste du ha tillräcklig behörighet i prenumerationen som du distribuerar webbtjänsten. Mer information finns i [hantera en webbtjänst med hjälp av Azure Machine Learning Web Services-portalen](manage-new-webservice.md).

Distribuera som en ny webbtjänst och skapa ett program att använda den:

1. Längst ned på arbetsytan för experimentet klickar du på **kör**.
2. När körningen har slutförts klickar du på **distribuera webbtjänsten** och välj **distribuera webbtjänsten [nyhet]**.
3. Ange ett namn för din webbtjänst på sidan distribuera Experiment och en prisplanen och sedan klicka på **distribuera**.
4. På den **snabbstarten** klickar du på **förbruka**.
5. I den **exempelkoden** klickar du på **Batch**.
6. I Visual Studio skapar du en C# konsolapp: **Ny** > **projekt** > **Visual C#**   >  **Windows Classic Desktop**  >   **Konsolprogram (.NET Framework)**.
7. Kopiera och klistra in den C# exempelkoden i filen Program.cs.
8. Uppdatera värdet för den *apiKey* variabeln med den **primärnyckel** finns i den **grundläggande förbrukning info** avsnittet.
9. Leta upp den *scoreRequest* deklarationen och uppdaterar värdet för Webbtjänstparametrar som skickas till den *importdata* och *exportera Data* moduler. I det här fallet du använda den ursprungliga frågan, men definiera ett nytt tabellnamn.

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

