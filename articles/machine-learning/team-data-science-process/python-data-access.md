---
title: Få tillgång till datauppsättningar med Python-klientbiblioteket - Team Data Science Process
description: Installera och använd Python-klientbiblioteket för att komma åt och hantera Azure Machine Learning-data på ett säkert sätt från en lokal Python-miljö.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 93ec5e740ac6acf9420a9d980092ed772ac1618e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720987"
---
# <a name="access-datasets-with-python-using-the-azure-machine-learning-python-client-library"></a>Åtkomst till datauppsättningar med Python med hjälp av Python-klientbiblioteket i Azure Machine Learning
Förhandsversionen av Microsoft Azure Machine Learning Python-klientbiblioteket kan möjliggöra säker åtkomst till dina Azure Machine Learning-datauppsättningar från en lokal Python-miljö och gör det möjligt att skapa och hantera datauppsättningar på en arbetsyta.

Det här avsnittet innehåller instruktioner om hur du:

* installera Machine Learning Python-klientbiblioteket
* komma åt och ladda upp datauppsättningar, inklusive instruktioner om hur du får auktorisering för att komma åt Azure Machine Learning-datauppsättningar från din lokala Python-miljö
* komma åt mellanliggande datauppsättningar från experiment
* använda Python-klientbiblioteket för att räkna upp datauppsättningar, komma åt metadata, läsa innehållet i en datauppsättning, skapa nya datauppsättningar och uppdatera befintliga datauppsättningar

## <a name="prerequisites"></a><a name="prerequisites"></a>Krav
Python-klientbiblioteket har testats under följande miljöer:

* Windows, Mac och Linux
* Python 2.7, 3.3 och 3.4

Den är beroende av följande paket:

* Begäranden
* python-dateutil
* Pandor

Vi rekommenderar att du använder en Python-distribution som [Anaconda](http://continuum.io/downloads#all) eller [Canopy](https://store.enthought.com/downloads/), som levereras med Python, IPython och de tre paket som anges ovan installerade. Även om IPython inte är strikt krävs, är det en bra miljö för att manipulera och visualisera data interaktivt.

### <a name="how-to-install-the-azure-machine-learning-python-client-library"></a><a name="installation"></a>Så här installerar du Azure Machine Learning Python-klientbiblioteket
Installera Azure Machine Learning Python-klientbiblioteket för att slutföra de uppgifter som beskrivs i det här avsnittet. Det här biblioteket är tillgängligt från [Python Package Index](https://pypi.python.org/pypi/azureml). Om du vill installera den i python-miljön kör du följande kommando från din lokala Python-miljö:

    pip install azureml

Alternativt kan du hämta och installera från källorna på [GitHub](https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python).

    python setup.py install

Om du har git installerat på datorn kan du använda pip för att installera direkt från git-databasen:

    pip install git+https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python.git


## <a name="use-code-snippets-to-access-datasets"></a><a name="datasetAccess"></a>Använda kodavsnitt för att komma åt datauppsättningar
Python-klientbiblioteket ger dig programmatisk åtkomst till dina befintliga datauppsättningar från experiment som har körts.

Från webbgränssnittet i Azure Machine Learning Studio (klassiskt) kan du generera kodavsnitt som innehåller all nödvändig information för att hämta och deserialisera datauppsättningar som pandas DataFrame-objekt på din lokala dator.

### <a name="security-for-data-access"></a><a name="security"></a>Säkerhet för dataåtkomst
Kodavsnitten som tillhandahålls av Azure Machine Learning Studio (klassisk) för användning med Python-klientbiblioteket innehåller ditt arbetsområdes-ID och auktoriseringstoken. Dessa ger fullständig åtkomst till din arbetsyta och måste skyddas, som ett lösenord.

Av säkerhetsskäl är kodavsnittsfunktionen endast tillgänglig för användare som har sin roll som **ägare** för arbetsytan. Din roll visas i Azure Machine Learning Studio (klassisk) på sidan **ANVÄNDARE** under **Inställningar**.

![Säkerhet][security]

Om din roll inte anges som **Ägare**kan du antingen begära att få ett återinspelat som ägare eller be arbetsytans ägare att förse dig med kodavsnittet.

Om du vill hämta auktoriseringstoken kan du välja något av följande alternativ:

* Be om en token från en ägare. Ägare kan komma åt sina auktoriseringstoken från sidan Inställningar på sin arbetsyta i Azure Machine Learning Studio (klassisk). Välj **Inställningar** i den vänstra rutan och klicka på **AUKTORISERINGSTOKENS** för att se de primära och sekundära token. Även om antingen de primära eller sekundära auktoriseringstoken kan användas i kodavsnittet, rekommenderas att ägarna endast delar de sekundära auktoriseringstoken.

   ![Auktoriseringstoken](./media/python-data-access/ml-python-access-settings-tokens.png)

* Be om att bli befordrad till ägarroll: en aktuell ägare av arbetsytan måste först ta bort dig från arbetsytan och sedan bjuda in dig igen som ägare.

När utvecklare har fått arbetsyte-ID och auktoriseringstoken kan de komma åt arbetsytan med hjälp av kodavsnittet oavsett deras roll.

Auktoriseringstoken hanteras på sidan **AUKTORISERINGSTOKENS** under **INSTÄLLNINGAR**. Du kan återskapa dem, men den här proceduren återkallar åtkomsten till föregående token.

### <a name="access-datasets-from-a-local-python-application"></a><a name="accessingDatasets"></a>Få tillgång till datauppsättningar från ett lokalt Python-program
1. I Machine Learning Studio (klassisk) klickar du på **DATASETS** i navigeringsfältet till vänster.
2. Välj den datauppsättning som du vill komma åt. Du kan välja någon av datauppsättningarna i **listan MINA DATASETS** eller i LISTAN **EXEMPEL.**
3. Klicka på Generera **dataåtkomstkod**i det nedre verktygsfältet . Om data är i ett format som inte är kompatibelt med Python-klientbiblioteket inaktiveras den här knappen.
   
    ![Datauppsättningar][datasets]
4. Markera kodavsnittet i fönstret som visas och kopiera det till Urklipp.
   
    ![Knappen Generera dataåtkomstkod][dataset-access-code]
5. Klistra in koden i anteckningsboken för ditt lokala Python-program.
   
    ![Klistra in kod i anteckningsboken][ipython-dataset]

## <a name="access-intermediate-datasets-from-machine-learning-experiments"></a><a name="accessingIntermediateDatasets"></a>Få tillgång till mellanliggande datauppsättningar från Machine Learning-experiment
När ett experiment körs i Machine Learning Studio (klassisk) är det möjligt att komma åt mellanliggande datauppsättningar från utdatanoderna för moduler. Mellanliggande datauppsättningar är data som har skapats och använts för mellanliggande steg när ett modellverktyg har körts.

Mellanliggande datauppsättningar kan nås så länge dataformatet är kompatibelt med Python-klientbiblioteket.

Följande format stöds (konstanter för dessa format `azureml.DataTypeIds` finns i klassen):

* PlainText
* GeneriskCSV
* GenericTSV
* GeneriskCSVNoHeader
* GenericTSVNoHeader

Du kan bestämma formatet genom att hovra över en modulutdatanod. Det visas tillsammans med nodnamnet, i en verktygstips.

Några av modulerna, till exempel [modulen Dela,][split] matas ut till ett format med namnet `Dataset`, som inte stöds av Python-klientbiblioteket.

![Format för datauppsättning][dataset-format]

Du måste använda en konverteringsmodul, till exempel [Konvertera till CSV,][convert-to-csv]för att få en utdata till ett format som stöds.

![GenericCSV-format][csv-format]

Följande steg visar ett exempel som skapar ett experiment, kör det och kommer åt mellanliggande datauppsättning.

1. Skapa ett nytt experiment.
2. Infoga en **datauppsättning för binär klassificering av vuxenräkningsinkomst.**
3. Sätt i en [Split-modul][split] och anslut indata till datauppsättningsmodulutdata.
4. Infoga en [Konvertera till CSV-modul][convert-to-csv] och anslut indata till en av [splitmodulutgångarna.][split]
5. Spara experimentet, kör det och vänta tills jobbet är klart.
6. Klicka på utdatanoden i modulen [Konvertera till CSV.][convert-to-csv]
7. När snabbmenyn visas väljer du **Generera dataåtkomstkod**.
   
    ![Snabbmeny][experiment]
8. Markera kodavsnittet och kopiera det till Urklipp från fönstret som visas.
   
    ![Generera åtkomstkod från snabbmenyn][intermediate-dataset-access-code]
9. Klistra in koden i anteckningsboken.
   
    ![Klistra in kod i anteckningsboken][ipython-intermediate-dataset]
10. Du kan visualisera data med matplotlib. Detta visas i ett histogram för ålderskolumnen:
    
    ![Histogram][ipython-histogram]

## <a name="use-the-machine-learning-python-client-library-to-access-read-create-and-manage-datasets"></a><a name="clientApis"></a>Använda Machine Learning Python-klientbiblioteket för att komma åt, läsa, skapa och hantera datauppsättningar
### <a name="workspace"></a>Arbetsyta
Arbetsytan är startpunkten för Python-klientbiblioteket. Ge `Workspace` klassen ditt arbetsområdes-ID och auktoriseringstoken för att skapa en instans:

    ws = Workspace(workspace_id='4c29e1adeba2e5a7cbeb0e4f4adfb4df',
                   authorization_token='f4f3ade2c6aefdb1afb043cd8bcf3daf')


### <a name="enumerate-datasets"></a>Räkna upp datauppsättningar
Så här räknar du upp alla datauppsättningar på en viss arbetsyta:

    for ds in ws.datasets:
        print(ds.name)

Så här räknar du upp bara de användarskapade datauppsättningarna:

    for ds in ws.user_datasets:
        print(ds.name)

Så här räknar du upp bara exempeldatauppsättningarna:

    for ds in ws.example_datasets:
        print(ds.name)

Du kan komma åt en datauppsättning efter namn (som är skiftlägeskänslig):

    ds = ws.datasets['my dataset name']

Eller så kan du komma åt den efter index:

    ds = ws.datasets[0]


### <a name="metadata"></a>Metadata
Datauppsättningar har metadata, förutom innehåll. (Mellanliggande datauppsättningar är ett undantag från den här regeln och har inga metadata.)

Vissa metadatavärden tilldelas av användaren vid skapande:

    print(ds.name)
    print(ds.description)
    print(ds.family_id)
    print(ds.data_type_id)

Andra är värden som tilldelats av Azure ML:

    print(ds.id)
    print(ds.created_date)
    print(ds.size)

Se `SourceDataset` klassen för mer information om tillgängliga metadata.

### <a name="read-contents"></a>Läs innehåll
Kodavsnitten från Machine Learning Studio (klassisk) hämtar och avserialiserar datauppsättningen automatiskt till ett pandasDataFrame-objekt. Detta görs med `to_dataframe` metoden:

    frame = ds.to_dataframe()

Om du föredrar att hämta rådata och utföra deserialiseringen själv är det ett alternativ. För närvarande är detta det enda alternativet för format som "ARFF", som Python-klientbiblioteket inte kan avserialisera.

Så här läser du innehållet som text:

    text_data = ds.read_as_text()

Så här läser du innehållet som binärt:

    binary_data = ds.read_as_binary()

Du kan också bara öppna en ström till innehållet:

    with ds.open() as file:
        binary_data_chunk = file.read(1000)


### <a name="create-a-new-dataset"></a>Skapa en ny datauppsättning
Med Python-klientbiblioteket kan du ladda upp datauppsättningar från ditt Python-program. Dessa datauppsättningar är sedan tillgängliga för användning på arbetsytan.

Om du har dina data i en pandas DataFrame använder du följande kod:

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_dataframe(
        dataframe=frame,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

Om dina data redan är serialiserade kan du använda:

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_raw_data(
        raw_data=raw_data,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

Python-klientbiblioteket kan serialisera en pandas DataFrame till följande format (konstanter för dessa är i `azureml.DataTypeIds` klassen):

* PlainText
* GeneriskCSV
* GenericTSV
* GeneriskCSVNoHeader
* GenericTSVNoHeader

### <a name="update-an-existing-dataset"></a>Uppdatera en befintlig datauppsättning
Om du försöker ladda upp en ny datauppsättning med ett namn som matchar en befintlig datauppsättning bör du få ett konfliktfel.

Om du vill uppdatera en befintlig datauppsättning måste du först hämta en referens till den befintliga datauppsättningen:

    dataset = ws.datasets['existing dataset']

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Använd `update_from_dataframe` sedan för att serialisera och ersätta innehållet i datauppsättningen på Azure:

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(frame2)

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Om du vill serialisera data till ett annat format `data_type_id` anger du ett värde för den valfria parametern.

    from azureml import DataTypeIds

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        data_type_id=DataTypeIds.GenericTSV,
    )

    print(dataset.data_type_id) # 'GenericTSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Du kan också ange en ny beskrivning genom `description` att ange ett värde för parametern.

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        description='data up to feb 2015',
    )

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to feb 2015'

Du kan också ange ett nytt namn genom `name` att ange ett värde för parametern. Från och med nu hämtar du datauppsättningen med det nya namnet. Följande kod uppdaterar data, namn och beskrivning.

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        name='existing dataset v2',
        description='data up to feb 2015',
    )

    print(dataset.data_type_id)                    # 'GenericCSV'
    print(dataset.name)                            # 'existing dataset v2'
    print(dataset.description)                     # 'data up to feb 2015'

    print(ws.datasets['existing dataset v2'].name) # 'existing dataset v2'
    print(ws.datasets['existing dataset'].name)    # IndexError

Parametrarna `data_type_id`och `name` parametrarna är valfria och som standard för deras tidigare värde. `description` Parametern `dataframe` krävs alltid.

Om dina data redan är `update_from_raw_data` serialiserade använder du i stället för `update_from_dataframe`. Om du bara `raw_data` passerar `dataframe`in istället för , det fungerar på ett liknande sätt.

<!-- Images -->
[security]:./media/python-data-access/security.png
[dataset-format]:./media/python-data-access/dataset-format.png
[csv-format]:./media/python-data-access/csv-format.png
[datasets]:./media/python-data-access/datasets.png
[dataset-access-code]:./media/python-data-access/dataset-access-code.png
[ipython-dataset]:./media/python-data-access/ipython-dataset.png
[experiment]:./media/python-data-access/experiment.png
[intermediate-dataset-access-code]:./media/python-data-access/intermediate-dataset-access-code.png
[ipython-intermediate-dataset]:./media/python-data-access/ipython-intermediate-dataset.png
[ipython-histogram]:./media/python-data-access/ipython-histogram.png


<!-- Module References -->
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

