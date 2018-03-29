---
title: Åtkomst till datamängder med Machine Learning Python klientbiblioteket | Microsoft Docs
description: Installera och använda Python klientbiblioteket komma åt och hantera Azure Machine Learning data på ett säkert sätt från en lokal Python-miljö.
services: machine-learning
documentationcenter: python
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 9ab42272-c30c-4b7e-8e66-d64eafef22d0
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: bradsev
ms.openlocfilehash: 5f0823e07f0e583e32829b664a1d3d463092169e
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="access-datasets-with-python-using-the-azure-machine-learning-python-client-library"></a>Åtkomst till datauppsättningar med Python med hjälp av Python-klientbiblioteket i Azure Machine Learning
Förhandsgranskning av Microsoft Azure Machine Learning Python klientbiblioteket kan aktivera säker åtkomst till din Azure Machine Learning datamängder från en lokal Python-miljö och möjliggör skapande och hantering av datauppsättningar i en arbetsyta.

Det här avsnittet innehåller instruktioner om hur du:

* installera Machine Learning Python-klientbibliotek 
* komma åt och ladda upp datauppsättningar, inklusive instruktioner för hur du kan få behörighet att komma åt Azure Machine Learning datauppsättningar från din lokala miljö för Python
* åtkomst till mellanliggande datauppsättningar från experiment
* använda Python-klientbiblioteket för att räkna upp datauppsättningar, komma åt metadata, läsa innehållet i en datamängd, skapa nya datamängder och uppdatera befintliga datauppsättningar

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="prerequisites"></a>Förhandskrav
Klientbibliotek för Python har testats enligt följande miljöer:

* Windows, Mac och Linux
* Python 2.7, 3.3 och 3.4

Det finns ett beroende på följande paket:

* förfrågningar
* python-dateutil
* pandas

Vi rekommenderar att du använder en Python-distribution som [Anaconda](http://continuum.io/downloads#all) eller [trädtak](https://store.enthought.com/downloads/), som medföljer Python, IPython och tre paket i listan ovan installerad. Även om IPython inte är absolut nödvändigt, är det en bra miljö för hantering och visualisera data interaktivt.

### <a name="installation"></a>Så här installerar du Azure Machine Learning Python-klientbibliotek
Azure Machine Learning Python klientbiblioteket måste också installeras för att slutföra de uppgifter som beskrivs i det här avsnittet. Den är tillgänglig från den [Python Package Index](https://pypi.python.org/pypi/azureml). Om du vill installera den i din miljö för Python, kör du följande kommando från din lokala Python-miljö:

    pip install azureml

Du kan också hämta och installera från källor på [github](https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python).

    python setup.py install

Om du har git installerade på datorn kan använda du pip för att installera direkt från git-lagringsplatsen:

    pip install git+https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python.git


## <a name="datasetAccess"></a>Använd Studio kodstycken åtkomst till datauppsättningar
Klientbibliotek för Python ger programmatisk åtkomst till dina befintliga datauppsättningar från experiment som har körts.

Du kan använda webbgränssnittet Studio för att generera kodstycken som innehåller all information som behövs för att ladda ned och deserialisera datauppsättningar som Pandas DataFrame objekt på din dator för platsen.

### <a name="security"></a>Säkerhet för dataåtkomst
Kodstycken som tillhandahålls av Studio för användning med Python klientbiblioteket innehåller ditt arbetsyte-id och auktorisering för token. Dessa har fullständig åtkomst till din arbetsyta och måste skyddas som ett lösenord.

Av säkerhetsskäl kodfragment-funktionen är bara är tillgänglig för användare som har rollen som **ägare** för arbetsytan. Din roll visas i Azure Machine Learning Studio på den **användare** sidan **inställningar**.

![Säkerhet][security]

Om din roll inte har angetts som **ägare**, kan du antingen begäran om att bjudas in som ägare eller be ägaren av arbetsytan ger dig kodfragmentet.

För att få autentiseringstoken kan göra du något av följande:

* Begära en token från en ägare. Ägare kan komma åt sina auktorisering tokens från sidan Inställningar i arbetsytan i Studio. Välj **inställningar** från den vänstra rutan och klicka på **AUKTORISERING token** att se de primära och sekundära token.  Primärt eller sekundära auktorisering token kan användas i kodfragmentet, men vi rekommenderar att sekundära auktorisering token delar bara ägare.

![Auktorisering token](./media/python-data-access/ml-python-access-settings-tokens.png)

* Fråga som ska uppgraderas till rollen som ägare.  Om du vill göra detta måste en aktuell ägare i arbetsytan först tas bort från arbetsytan och sedan åter bjuda in dig till den som en ägare.

När utvecklare har fått arbetsyte-id och auktorisering token, som de har tillgång till arbetsytan med kodfragmentet oavsett deras roll.

Auktorisering token hanteras på den **AUKTORISERING token** sidan **inställningar**. Du kan återskapa dem, men den här proceduren återkallar åtkomst till föregående token.

### <a name="accessingDatasets"></a>Åtkomst datauppsättningar från ett lokalt program Python
1. I Machine Learning Studio klickar du på **DATAUPPSÄTTNINGAR** i navigeringsfältet till vänster.
2. Välj sedan datamängden som du vill komma åt. Du kan välja någon av datauppsättningarna från den **Mina DATAUPPSÄTTNINGAR** lista eller från den **exempel** lista.
3. Längst ned i verktygsfältet, klicka på **generera Data Access-koden**. Om data är i ett format som är inkompatibel med klientbibliotek för Python, inaktiveras den här knappen.
   
    ![Datauppsättningar][datasets]
4. Välj kodfragmentet i fönstret som visas och kopiera den till Urklipp.
   
    ![Kod för åtkomst][dataset-access-code]
5. Klistra in koden i anteckningsboken på ditt lokala Python-program.
   
    ![Bärbar dator][ipython-dataset]

## <a name="accessingIntermediateDatasets"></a>Åtkomst mellanliggande datauppsättningar från Machine Learning-experiment
När ett experiment körs i Machine Learning Studio, är det möjligt att komma åt den mellanliggande datauppsättningar från utdata-noder i moduler. Mellanliggande datauppsättningar är data som har skapats och används för mellanliggande steg när en modell-verktyget har körts.

Mellanliggande datauppsättningar kan nås så länge dataformatet är kompatibel med Python klientbiblioteket.

Följande format som stöds (konstanter för dessa finns i den `azureml.DataTypeIds` klassen):

* Klartext
* GenericCSV
* GenericTSV
* GenericCSVNoHeader
* GenericTSVNoHeader

Du kan bestämma formatet hovrar över en modul utdata-nod. Den visas tillsammans med nodnamn i en knappbeskrivning.

Några av modulerna som den [dela] [ split] modulen, utdata till ett format som heter `Dataset`, som inte stöds av klientbiblioteket Python.

![DataSet-Format][dataset-format]

Du måste använda en konvertering-modul som [konvertera till CSV][convert-to-csv], för att hämta utdata till ett format som stöds.

![GenericCSV Format][csv-format]

Följande steg visar ett exempel som skapar ett experiment, körs och ansluter till mellanliggande dataset.

1. Skapa ett nytt experiment.
2. Infoga en **vuxna inventering intäkter binär klassificering dataset** modul.
3. Infoga en [dela] [ split] modulen, och ansluta indata till dataset modulen utdata.
4. Infoga en [konvertera till CSV] [ convert-to-csv] modulen och ansluta indata till en av de [dela] [ split] modulen matar ut.
5. Spara experimentet, köra det och vänta på att den ska slutföras.
6. Klicka på noden utdata på den [konvertera till CSV] [ convert-to-csv] modul.
7. Välj när snabbmenyn visas **generera Data Access-koden**.
   
    ![Snabbmenyn][experiment]
8. Välj kodfragmentet och kopiera den till Urklipp i fönstret som visas.
   
    ![Kod för åtkomst][intermediate-dataset-access-code]
9. Klistra in koden i din bärbara dator.
   
    ![Bärbar dator][ipython-intermediate-dataset]
10. Du kan visualisera data med matplotlib. Då visas i ett histogram för kolumnen ålder:
    
    ![Histogram][ipython-histogram]

## <a name="clientApis"></a>Använda Machine Learning Python-klientbibliotek för att komma åt, läsa, skapa och hantera datamängder
### <a name="workspace"></a>Arbetsyta
Arbetsytan är startpunkten för klientbiblioteket Python. Ange den `Workspace` klass med ditt arbetsyte-id och auktorisering token för att skapa en instans:

    ws = Workspace(workspace_id='4c29e1adeba2e5a7cbeb0e4f4adfb4df',
                   authorization_token='f4f3ade2c6aefdb1afb043cd8bcf3daf')


### <a name="enumerate-datasets"></a>Räkna upp datauppsättningar
Räkna upp alla datauppsättningar i en viss arbetsyta:

    for ds in ws.datasets:
        print(ds.name)

Räkna upp bara att skapa användaren datauppsättningarna:

    for ds in ws.user_datasets:
        print(ds.name)

Räkna upp bara exempel datauppsättningar:

    for ds in ws.example_datasets:
        print(ds.name)

Du kan komma åt en datauppsättning med namnet (som är skiftlägeskänsligt):

    ds = ws.datasets['my dataset name']

Eller så kan du öppna det genom index:

    ds = ws.datasets[0]


### <a name="metadata"></a>Metadata
Datauppsättningar har metadata, förutom innehåll. (Mellanliggande datauppsättningar är undantag till den här regeln och har inte några metadata.)

Vissa metadatavärden tilldelas vid skapandet av användaren:

    print(ds.name)
    print(ds.description)
    print(ds.family_id)
    print(ds.data_type_id)

Andra är värden som tilldelats av Azure ML:

    print(ds.id)
    print(ds.created_date)
    print(ds.size)

Finns det `SourceDataset` klassen mer information om tillgängliga metadata.

### <a name="read-contents"></a>Läs innehållet
Kodstycken som tillhandahålls av Machine Learning Studio automatiskt hämta och deserialisera datauppsättningen till ett Pandas DataFrame-objekt. Detta görs med den `to_dataframe` metoden:

    frame = ds.to_dataframe()

Om du vill hämta rådata och utföra deserialiseringen själv, är ett alternativ. Detta är det enda alternativet för format, till exempel 'ARFF' som klientbiblioteket Python inte kan deserialisera för tillfället.

Att läsa innehållet som text:

    text_data = ds.read_as_text()

Att läsa innehållet som binary:

    binary_data = ds.read_as_binary()

Du kan också öppna en dataström som innehållet:

    with ds.open() as file:
        binary_data_chunk = file.read(1000)


### <a name="create-a-new-dataset"></a>Skapa en ny datamängd
Klientbibliotek för Python kan du överföra datauppsättningar från Python-program. Dessa data är sedan tillgängliga för användning i din arbetsyta.

Om du har dina data i en Pandas DataFrame kan du använda följande kod:

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_dataframe(
        dataframe=frame,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

Om dina data är redan serialiserad, kan du använda:

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_raw_data(
        raw_data=raw_data,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

Klientbibliotek för Python går att serialisera en Pandas DataFrame i följande format (konstanter för dessa finns i den `azureml.DataTypeIds` klassen):

* Klartext
* GenericCSV
* GenericTSV
* GenericCSVNoHeader
* GenericTSVNoHeader

### <a name="update-an-existing-dataset"></a>Uppdatera en befintlig datauppsättning
Om du försöker skicka en ny datamängd med ett namn som matchar en befintlig datauppsättning får du ett fel i konflikt.

Om du vill uppdatera en befintlig datauppsättning, måste du först hämta en referens till den befintliga datauppsättningen:

    dataset = ws.datasets['existing dataset']

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Använd sedan `update_from_dataframe` serialisera och Ersätt innehållet i dataset i Azure:

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(frame2)

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Om du vill serialisera data till ett annat format, ange ett värde för den valfria `data_type_id` parameter.

    from azureml import DataTypeIds

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        data_type_id=DataTypeIds.GenericTSV,
    )

    print(dataset.data_type_id) # 'GenericTSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Du kan du ange en ny beskrivning genom att ange ett värde för den `description` parameter.

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        description='data up to feb 2015',
    )

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to feb 2015'

Du kan du ange ett nytt namn genom att ange ett värde för den `name` parameter. Baserade på ska du hämta datauppsättning med det nya namnet. Följande kod uppdaterar data, namn och beskrivning.

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

Den `data_type_id`, `name` och `description` parametrar är valfria och deras tidigare värde som standard. Den `dataframe` parametern krävs alltid.

Om dina data är redan serialiserad, använda `update_from_raw_data` i stället för `update_from_dataframe`. Om du bara ange `raw_data` i stället för `dataframe`, fungerar på liknande sätt.

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

