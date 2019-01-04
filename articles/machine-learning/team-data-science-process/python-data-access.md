---
title: Öppna datauppsättningar med Python-klientbiblioteket - Team Data Science Process
description: Installera och använda Python-klientbiblioteket för att komma åt och hantera Azure Machine Learning-data på ett säkert sätt från en lokal Python-miljö.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: f53233edd17fa7097dde67bbce16b1ece668c721
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2018
ms.locfileid: "53554868"
---
# <a name="access-datasets-with-python-using-the-azure-machine-learning-python-client-library"></a>Åtkomst till datauppsättningar med Python med hjälp av Python-klientbiblioteket i Azure Machine Learning
Förhandsversionen av Microsoft Azure Machine Learning Python-klientbiblioteket aktivera säker åtkomst till dina Azure Machine Learning-datauppsättningar från en lokal Python-miljö och möjliggör skapandet och hanteringen av datauppsättningar i en arbetsyta.

Det här avsnittet innehåller instruktioner om hur du:

* installera Machine Learning Python-klientbiblioteket
* få åtkomst till och ladda upp datauppsättningar, inklusive instruktioner för hur du hämtar åtkomstbehörighet till Azure Machine Learning datauppsättningar från den lokala miljön i Python
* åtkomst till mellanliggande datauppsättningar från experiment
* använda Python-klientbiblioteket för att räkna upp datauppsättningar, få åtkomst till metadata, läsa innehållet i en datauppsättning, skapa nya datauppsättningar och uppdatera befintliga datauppsättningar

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="prerequisites"></a>Förhandskrav
Python-klientbiblioteket har testats under följande miljöer:

* Windows, Mac och Linux
* Python 2.7, 3.3 och 3.4

Den har ett beroende på följande paket:

* Begäranden
* Python-dateutil
* pandas

Vi rekommenderar att du använder en Python-distribution som [Anaconda](http://continuum.io/downloads#all) eller [trädtak](https://store.enthought.com/downloads/), som kommer med Python, IPython och tre paketen som listas ovan installerad. IPython inte är nödvändigt, är det en perfekt miljö för hantering och visualisera data interaktivt.

### <a name="installation"></a>Så här installerar du Azure Machine Learning Python-klientbiblioteket
Azure Machine Learning Python-klientbiblioteket måste också installeras för att slutföra de uppgifter som beskrivs i det här avsnittet. Den är tillgänglig från den [Python Package Index](https://pypi.python.org/pypi/azureml). Om du vill installera appen i Python-miljön, kör du följande kommando från den lokala Python-miljön:

    pip install azureml

Du kan också hämta och installera från källor på [GitHub](https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python).

    python setup.py install

Om du måste ha git installerat på din dator kan du använda pip installera direkt från git-lagringsplatsen:

    pip install git+https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python.git


## <a name="datasetAccess"></a>Använda Studio kodfragment för att komma åt datauppsättningar
Python-klientbiblioteket ger dig programmatisk åtkomst till dina befintliga datauppsättningar från experiment som har körts.

Du kan använda webbgränssnittet Studio för att generera kodfragment som innehåller all nödvändig information för att ladda ned och deserialisera datauppsättningar som pandas-DataFrame objekt på den lokala datorn.

### <a name="security"></a>Säkerhet för åtkomst till data
Kodfragment som tillhandahålls av Studio för användning med Python-klientbiblioteket innehåller ditt arbetsyte-id och auktorisering token. Dessa ger full åtkomst till din arbetsyta och måste skyddas som ett lösenord.

Av säkerhetsskäl kod kodfragment funktioner är endast tillgänglig för användare som har sin roll som **ägare** för arbetsytan. Din roll visas i Azure Machine Learning Studio på den **användare** sidan **inställningar**.

![Säkerhet][security]

Om din roll inte har angetts som **ägare**, kan du antingen begäran om att bjudas in som ägare eller be ägaren av arbetsytan för att förse dig med kodfragmentet.

För att skaffa autentiseringstoken kan göra du något av följande:

* Be om en token från en ägare. Ägare kan komma åt sina auktoriseringstoken från inställningssidan för arbetsytan i Studio. Välj **inställningar** från den vänstra rutan och klicka på **AUKTORISERINGSTOKEN** att se de primära och sekundära token. Även om primärt eller sekundära auktoriseringstoken kan användas i kodfragmentet, rekommenderar vi att ägare bara dela de sekundära auktoriseringstoken.

![Auktoriseringstoken](./media/python-data-access/ml-python-access-settings-tokens.png)

* Be att höjas upp till rollen ägare. Då måste en aktuell ägare för arbetsytan där du först ta bort dig från arbetsytan sedan nytt bjuda in dig till den som ägare.

När utvecklare har fått arbetsytans id och auktorisering token, de kan komma åt arbetsytan med kodfragmentet oavsett deras roll.

Auktoriseringstoken som hanteras på den **AUKTORISERINGSTOKEN** sidan **inställningar**. Du kan återskapa dem, men den här proceduren återkallar tillgång till den föregående token.

### <a name="accessingDatasets"></a>Åtkomst till datauppsättningar från en lokal Python-program
1. I Machine Learning Studio, klickar du på **DATAUPPSÄTTNINGAR** i navigeringsfältet till vänster.
2. Välj den datauppsättning som du vill ha åtkomst till. Du kan välja någon av datauppsättningarna från den **Mina DATAUPPSÄTTNINGAR** lista eller från den **exempel** lista.
3. I verktygsfältet längst ned klickar du på **generera Dataåtkomstkoden**. Om data är i ett format som är inte kompatibel med Python-klientbiblioteket, är den här knappen inaktiverad.
   
    ![Datauppsättningar][datasets]
4. Välj kodfragmentet i fönstret som visas och kopiera den till Urklipp.
   
    ![Generera kod för dataåtkomst knappen][dataset-access-code]
5. Klistra in koden i anteckningsboken för ditt lokala Python-program.
   
    ![Klistra in koden i anteckningsboken][ipython-dataset]

## <a name="accessingIntermediateDatasets"></a>Åtkomst till mellanliggande datauppsättningar från Machine Learning-experiment
När det körs ett experiment i Machine Learning Studio, är det möjligt att komma åt de mellanliggande datauppsättningarna från utdata-noder i moduler. Mellanliggande datauppsättningar är data som har skapats och används för mellanliggande steg om ett modell-verktyg har körts.

Mellanliggande datauppsättningar kan nås så länge dataformatet är kompatibel med Python-klientbiblioteket.

Följande format som stöds (konstanter för dessa finns i den `azureml.DataTypeIds` klass):

* Oformaterad text
* GenericCSV
* GenericTSV
* GenericCSVNoHeader
* GenericTSVNoHeader

Du kan bestämma formatet genom att hovra över en modul utdata-nod. Den visas tillsammans med namnet på noden i en knappbeskrivning.

Några av modulerna som den [dela] [ split] modulen, utdata till ett format som heter `Dataset`, som inte stöds av Python-klientbiblioteket.

![Format för datauppsättning][dataset-format]

Du måste använda en konvertering-modul som [konvertera till CSV][convert-to-csv], för att hämta utdata till ett format som stöds.

![GenericCSV Format][csv-format]

Följande steg visar ett exempel som skapar ett experiment, kör den och har åtkomst till den mellanliggande datauppsättningen.

1. Skapa ett nytt experiment.
2. Infoga en **vuxet insamlade inkomst binär klassificering datauppsättning** modulen.
3. Infoga en [dela] [ split] -modulen och ansluta indata till datauppsättningen modulen utdata.
4. Infoga en [konvertera till CSV] [ convert-to-csv] modulen och ansluta indata till en av de [dela] [ split] modulen matar ut.
5. Spara experimentet, köra den och vänta på att den ska slutföras.
6. Klicka på noden utdata på den [konvertera till CSV] [ convert-to-csv] modulen.
7. När snabbmenyn visas väljer du **generera Dataåtkomstkoden**.
   
    ![Snabbmeny][experiment]
8. Välj kodfragmentet och kopiera den till Urklipp från fönstret som visas.
   
    ![Generera kod för dataåtkomst från snabbmenyn][intermediate-dataset-access-code]
9. Klistra in koden i anteckningsboken.
   
    ![Klistra in koden i anteckningsboken][ipython-intermediate-dataset]
10. Du kan visualisera data med matplotlib. Då visas i ett histogram för kolumnen ålder:
    
    ![Histogram][ipython-histogram]

## <a name="clientApis"></a>Använd Machine Learning Python-klientbiblioteket för att komma åt, läsa, skapa och hantera datauppsättningar
### <a name="workspace"></a>Arbetsyta
Arbetsytan är startpunkten för Python-klientbiblioteket. Ange den `Workspace` klassen med ditt arbetsyte-id och auktorisering token för att skapa en instans:

    ws = Workspace(workspace_id='4c29e1adeba2e5a7cbeb0e4f4adfb4df',
                   authorization_token='f4f3ade2c6aefdb1afb043cd8bcf3daf')


### <a name="enumerate-datasets"></a>Räkna upp datauppsättningar
Räkna upp alla datauppsättningar i en viss arbetsyta:

    for ds in ws.datasets:
        print(ds.name)

Räkna upp bara de användarskapade datauppsättningarna:

    for ds in ws.user_datasets:
        print(ds.name)

Räkna upp bara datauppsättningarna som exempel:

    for ds in ws.example_datasets:
        print(ds.name)

Du kan komma åt en datauppsättning med namnet (som är skiftlägeskänsligt):

    ds = ws.datasets['my dataset name']

Eller så kan du använda tjänsten efter index:

    ds = ws.datasets[0]


### <a name="metadata"></a>Metadata
Datauppsättningar har metadata, utöver innehåll. (Mellanliggande datauppsättningar är ett undantag till den här regeln och har inte alla metadata.)

Vissa metadatavärdena tilldelas vid tidpunkten för skapandet av användaren:

    print(ds.name)
    print(ds.description)
    print(ds.family_id)
    print(ds.data_type_id)

Andra är värden som tilldelats av Azure ML:

    print(ds.id)
    print(ds.created_date)
    print(ds.size)

Se den `SourceDataset` klass mer information om tillgängliga metadata.

### <a name="read-contents"></a>Läs innehållet
Kodfragment som tillhandahålls av Machine Learning Studio automatiskt hämta och deserialisera datauppsättningen till ett pandas-DataFrame-objekt. Detta görs med den `to_dataframe` metoden:

    frame = ds.to_dataframe()

Om du vill hämta rådata och utföra deserialiseringen själv, är ett alternativ. Det här är det enda alternativet för format, till exempel ”ARFF”, som det går inte att deserialisera Python-klientbiblioteket för tillfället.

Att läsa innehållet som text:

    text_data = ds.read_as_text()

Att läsa innehållet som binary:

    binary_data = ds.read_as_binary()

Du kan också öppna en dataström som innehållet:

    with ds.open() as file:
        binary_data_chunk = file.read(1000)


### <a name="create-a-new-dataset"></a>Skapa en ny datauppsättning
Klientbibliotek för Python kan du ladda upp datauppsättningar från Python-program. Dessa datauppsättningar är sedan tillgängliga för användning i din arbetsyta.

Om du har dina data i en pandas-DataFrame, Använd följande kod:

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_dataframe(
        dataframe=frame,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

Om dina data redan serialiseras, kan du använda:

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_raw_data(
        raw_data=raw_data,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

Python-klientbiblioteket kan serialisera en pandas-DataFrame i följande format (konstanter för dessa finns i den `azureml.DataTypeIds` klass):

* Oformaterad text
* GenericCSV
* GenericTSV
* GenericCSVNoHeader
* GenericTSVNoHeader

### <a name="update-an-existing-dataset"></a>Uppdatera en befintlig datamängd
Om du försöker ladda upp en ny datamängd med ett namn som matchar en befintlig datamängd, får du en konflikt-fel.

Om du vill uppdatera en befintlig datamängd måste du först hämta en referens till den befintliga datauppsättningen:

    dataset = ws.datasets['existing dataset']

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Använd sedan `update_from_dataframe` att serialisera och Ersätt innehållet i datauppsättningen i Azure:

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(frame2)

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Om du vill att serialisera data till ett annat format, ange ett värde för den valfria `data_type_id` parametern.

    from azureml import DataTypeIds

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        data_type_id=DataTypeIds.GenericTSV,
    )

    print(dataset.data_type_id) # 'GenericTSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Du kan också ange en ny beskrivning genom att ange ett värde för den `description` parametern.

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        description='data up to feb 2015',
    )

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to feb 2015'

Du kan också ange ett nytt namn genom att ange ett värde för den `name` parametern. Hädanefter, ska du hämta datauppsättningen med det nya namnet. Följande kod uppdaterar data, namn och beskrivning.

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

Den `data_type_id`, `name` och `description` parametrar är valfria och deras tidigare värdet som standard. Den `dataframe` parametern krävs alltid.

Om dina data redan serialiseras, använda `update_from_raw_data` i stället för `update_from_dataframe`. Om du bara skickar i `raw_data` i stället för `dataframe`, den fungerar på liknande sätt.

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

