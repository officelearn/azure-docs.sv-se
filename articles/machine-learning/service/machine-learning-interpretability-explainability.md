---
title: Modelltolkning
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder Azure Machine Learning Interpretability SDK för att förklara varför din modell förutsägelser. Den kan användas under utbildnings- och inferensjobb för att förstå hur din modell förutsägelser.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: larryfr
ms.date: 04/04/2019
ms.openlocfilehash: f72923b80751f16ece128ced209679bbc325226c
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2019
ms.locfileid: "59051809"
---
# <a name="azure-machine-learning-interpretability-sdk"></a>Azure Machine Learning Interpretability SDK

I den här artikeln får du lära dig att förklara varför din modell gjort förutsägelserna det gjorde med hjälp av Azure Machine Learning Interpretability SDK. Att kunna förklarar din modell är viktigt av följande skäl:

* Kunder och intressenter vill veta **om de kan känna sig trygga förutsägelserna din modell gör**.
* Som en dataexpert du vill förstå **hur du frågar modellen om du vill söka efter insikter**. Du måste också verktyg för att fatta välgrundade beslut om **hur vi kan förbättra din modell**.
* Som ett företag, måste du förstå **beteendet för modellen med olika indata distributioner** och **hur modellen beter sig vid analys av specifika indata**.

Machine learning interpretability är viktigt i två faser av machine learning-utvecklingscykeln: **utbildning** tid och **inferensjobb** tid:

* Under **utbildning**: Modellen designers och bedömare kräver interpretability verktyg för att förklara utdata från en modell för intressenter att skapa förtroendet. De måste också insikter om modellen så att de kan felsöka modellen och fatta beslut rörande om beteendet matchar sina mål. Slutligen måste se till att modellen inte prioriterar.
* Under **inferensjobb**: Förutsägelser måste vara explainable till personer som använder din modell. Till exempel varför modellen neka en lånet eller förutsäga att en investeringsportfölj innebär en högre risk?

Azure Machine Learning Interpretability SDK innehåller tekniker som utvecklats av Microsoft och beprövade bibliotek från tredje part (till exempel FORMDATA och grön). SDK: N skapar ett gemensamt API över bibliotek som är integrerade och integrerar Azure Machine Learning-tjänster. Med detta SDK kan du förklara maskininlärningsmodeller **globalt på alla data**, eller **lokalt på en viss datapunkt** med för avancerade tekniker i ett enkelt att använda och skalbart sätt.

## <a name="how-does-it-work"></a>Hur fungerar det?

Azure Machine Learning Interpretability kan användas för att förstå modellens globala beteende eller en specifik förutsägelse. Den tidigare versionen är globala förklaring och det senare kallas lokala förklaring.

Azure Machine Learning Interpretability metoder kan kategoriseras även baserat på om metoden är oberoende av modellen eller specifika modellen. Vissa metoder rikta in vissa typer av modeller. Till exempel gäller Formdatas trädet förklaring endast för trädet-baserade modellen. Vissa metoder behandlar modellen som en svart ruta, till exempel mimic förklaring eller Formdatas kernel förklaring. Azure Machine Learning Interpretability SDK utnyttjar dessa olika sätt utifrån datauppsättningar, modelltyper och användningsfall.

Azure Machine Learning Interpretability returnerar en uppsättning information på hur en modell är dess förutsägelse. Informationen omfattar objekt som:

* Global/lokal relativa funktionen prioritet
* Global/lokal funktion och förutsägelser relation
* Interaktiva visualiseringar som visar förutsägelser, funktionen funktion och förutsägelse relationen och relativt vikten värden globalt och lokalt

## <a name="architecture"></a>Arkitektur

Azure Machine Learning Interpretability SDK är strukturerad i två Python-paket:

* [azureml.Explain.Model](https://docs.microsoft.com/python/api/azureml-explain-model/?view=azure-ml-py) -det största paket som innehåller funktioner som stöds av Microsoft.
* `azureml.contrib.explain.model` -Förhandsversion och experimentella funktioner som du kan prova.

    > [!IMPORTANT]
    > Saker i contrib stöds inte fullt ut. När de experimentella funktionerna blir mogen, flyttas de gradvis till det huvudsakliga paketet.

### <a name="explainers"></a>Explainers

Azure Machine Learning Interpretability SDK innehåller två uppsättningar explainers: Dirigera Explainers och Meta Explainers.

__Dirigera explainers__ kommer från integrerade bibliotek. SDK: N omsluter alla explainers så att de exponera ett gemensamt API och utdataformat. Här följer en lista över de direkta explainers som är tillgängliga i SDK:

> [!TIP]
> Om du är bekvämare direkt med hjälp av dessa explainers kan anropa du dem direkt i stället för med vanliga API och utdataformat.

* **Trädet förklaring**: Formdatas trädet förklaring, som fokuserar på polynom snabb FORMDATA värdet uppskattning av algoritmen time specifika för träd och -ensembler av träd.
* **Djupgående förklaring**: Baserat på förklaring från FORMDATA djup förklaring ”är en snabb approximativ algoritm för FORMDATA värdena i modeller för djup maskininlärning som bygger på en anslutning med DeepLIFT som beskrivs i dokumentet FORMDATA NIPS. TensorFlow-modeller och Keras med TensorFlow-serverdelen som stöds (det finns också preliminär stöd för PyTorch) ”.
* **Kernel förklaring**: Formdatas Kernel förklaring använder ett särskilt viktad lokala linjär regression för att beräkna FORMDATA värden för alla modeller.
* **Efterlikna förklaring**: Mimic förklaring baseras på idén om global surrogate modeller. En global surrogate modell är en tack interpretable modell som har tränats för att beräkna ungefär förutsägelser för en svart ruta modell så noggrant som möjligt. Dataexpert kan tolka surrogate-modellen för att dra slutsatser om modellen svart ruta.
* **GRÖN förklaring**: Baserat på grön, används grön förklaring enligt modellen-oberoende förklaringar i den senaste lokala interpretable (grön) algoritmen för att skapa lokala surrogate modeller. Till skillnad från de globala surrogate modellerna fokuserar grön på utbildning modeller för lokala surrogate att förklara enskilda förutsägelser.
* **HAN Text förklaring**: HAN Text förklaring använder ett hierarkisk uppmärksamhet nätverk för att hämta modellen förklaringar från textdata för en viss svart ruta text-modell. Vi tränar HAN surrogate på en viss lärare modell förväntade utdata. Vi har lagt till ett fine-tune steg för ett visst dokument för att kunna förbättra förklaringarna efter utbildning globalt över texten Kristi. HAN använder en dubbelriktad RNN med två uppmärksamhet lager för mening och word uppmärksamhet. När DNN har tränats på modellen för lärare och finjusterat på ett visst dokument, kan vi extrahera word importances från uppmärksamhet-lager. Vi har hittat HAN ska exaktare än grön eller FORMDATA för textdata men dyrare i villkoren i utbildning samt tid. Men har vi gjort förbättringar till dess att utbildning genom att ge användaren kan initiera nätverk med GloVe ordinbäddningar, även om det är fortfarande långsamt. Utbildningstid kan förbättras avsevärt genom att köra HAN på en fjärransluten Azure GPU VM. Implementeringen av HAN beskrivs i ”hierarkisk uppmärksamhet nätverk för Dokumentklassificering (Yang et al., 2016)” ([https://www.cs.cmu.edu/~diyiy/docs/naacl16.pdf](https://www.cs.cmu.edu/~diyiy/docs/naacl16.pdf)).

__Meta explainers__ automatiskt välja en lämplig direkt förklaring och generera den bästa förklaring information baserat på den angivna modellen och datauppsättningar. Meta-explainers utnyttja alla bibliotek (FORMDATA, grön, GA2M, avbildningen osv.) som vi har integrerat eller utvecklas. Följande är de meta explainers som är tillgängliga i SDK:

* **Tabular förklaring**: Använda med tabelldatauppsättningar.
* **Text förklaring**: Används med text datauppsättningar.
* **Bild förklaring** används med bild datauppsättningar.

Dessutom till meta välja av direkt explainers, meta explainers utveckla ytterligare funktioner ovanpå de underliggande bibliotek och förbättras direkt explainers hastighet och skalbarhet.

För närvarande `TabularExplainer` använder följande logik för att anropa direkt Explainers:

1. Om det är ett träd-baserade modellen `TreeExplainer`, annat
2. Om det är en DNN-modell `DeepExplainer`, annat
3. Behandlar det som en svart ruta modell och tillämpa `KernelExplainer`

Intelligens är inbyggt i `TabularExplainer` kommer allt mer sofistikerade som ytterligare bibliotek är integrerade i SDK: N och vi gå igenom- och nackdelar med varje förklaring.

`TabularExplainer` har även gjort betydande förbättringar för funktionen och prestanda över direkt Explainers:

* **Sammanfattning av datauppsättningen som initieringen**. I fall där hastigheten på förklaring är viktigast vi sammanfatta initieringen datauppsättningen och skapa en liten uppsättning representativa mängder som påskyndar både globala och lokala förklaring.
* **Sampling datauppsättningen utvärdering**. Om användaren skickar i en stor uppsättning utvärdering exempel men inte verkligen behöver dem som ska utvärderas alla, kan parametern sampling anges som SANT för att snabba upp globala förklaring.
* **KNN snabb förklaring**. En KNN-metoden kan användas i fall där förklaring måste vara lika snabbt som en enda bedömning/förutsägelse. Under global förklaring bevaras både initieringen exemplen och motsvarande funktioner som top k. För att generera en förklaring för varje utvärdering exempel KNN-metoden som används för att hitta mest liknande exemplet från initieringen exemplen och mest liknande exemplet top k funktioner returneras som top k-funktionerna för utvärdering.

Följande diagram visar relationen mellan två uppsättningar med direct och meta explainers.

[![Machine Learning Interpretability arkitektur](./media/machine-learning-interpretability-explainability/interpretability-architecture.png)](./media/machine-learning-interpretability-explainability/interpretability-architecture.png#lightbox)

### <a name="models-supported"></a>Modeller som stöds

Alla modeller som är tränade på datauppsättningar i Python `numpy.array`, `pandas.DataFrame`, `iml.datatypes.DenseData`, eller `scipy.sparse.csr_matrix` format som stöds av Machine Learning Interpretability SDK.

Förklaring-funktioner innehålla både modeller och pipelines som indata. Om en modell har angetts och modellen måste implementera förutsägelsefunktionen `predict` eller `predict_proba` som bekräftar att Scikit-konventionen. Om en pipeline (namnet på skriptet pipeline) tillhandahålls förutsätter funktionen förklaring att köra pipeline-skriptet returnerar en förutsägelse.

### <a name="local-and-remote-compute-target"></a>Lokal och fjärransluten beräkningsmål

Machine Learning Interpretability SDK är utformat för att arbeta med både lokala och fjärranslutna beräkningsmål. Om du kör lokalt SDK-funktioner inte att kontakta Azure-tjänster. Du kan köra förklaring via fjärranslutning på beräkning av Azure Machine Learning och logga in informationen som förklaring på Azure Machine Learning kör historik Services. Rapporter och visualiseringar från en förklaring finns tillgängliga på Azure Machine Learning-arbetsyta Portal för Användaranalys när den här informationen loggas.

## <a name="train-and-explain-locally"></a>Träna och förklarar lokalt

1. Träna din modell i en lokal Jupyter-anteckningsbok. 

    ``` python
    # load breast cancer dataset, a well-known small dataset that comes with scikit-learn
    from sklearn.datasets import load_breast_cancer
    from sklearn import svm
    from sklearn.model_selection import train_test_split
    breast_cancer_data = load_breast_cancer()
    classes = breast_cancer_data.target_names.tolist()
    # Split data into train and test
    from sklearn.model_selection import train_test_split
    x_train, x_test, y_train, y_test = train_test_split(breast_cancer_data.data, breast_cancer_data.target, test_size=0.2, random_state=0)
    clf = svm.SVC(gamma=0.001, C=100., probability=True)
    model = clf.fit(x_train, y_train)
    ```

2. Anropa förklaring: Om du vill initiera en förklaring-objekt, måste du skicka modell, träningsdata, funktionerna i intresse (valfritt) och klassnamn för utdata (om klassificering) till förklaring. Här är att skapa en instans av en förklaring objekt med hjälp av [TabularExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.tabularexplainer?view=azure-ml-py), [MimicExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.mimic.mimicexplainer?view=azure-ml-py), och `LimeExplainer` lokalt. `TabularExplainer` anropar någon av tre explainers under (`TreeExplainer`, `DeepExplainer`, eller `KernelExplainer`), och automatiskt välja det passar bäst för ditt användningsområde. Men du kan anropa var och en av dess tre underliggande explainers direkt.

    ```python
    from azureml.explain.model.tabular_explainer import TabularExplainer
    explainer = TabularExplainer(model, x_train, features=breast_cancer_data.feature_names, classes=classes)
    or
    from azureml.explain.model.mimic.mimic_explainer import MimicExplainer
    from azureml.explain.model.mimic.models.lightgbm_model import LGBMExplainableModel
    explainer = MimicExplainer(model, x_train, LGBMExplainableModel, features=breast_cancer_data.feature_names, classes=classes)
    or
    from azureml.contrib.explain.model.lime.lime_explainer import LIMEExplainer
    explainer = LIMEExplainer(model, x_train, features=breast_cancer_data.feature_names, classes=classes)
    ```

3. Hämta funktionen globala vikten värden.

    ```python
    # You can use the training data or the test data here. 
    global_explanation = explainer.explain_global(x_train)
    # Sorted feature importance values and feature names
    sorted_global_importance_values = global_explanation.get_ranked_global_values()
    sorted_global_importance_names = global_explanation.get_ranked_global_names()
    dict(zip(sorted_global_importance_names, sorted_global_importance_values))
    ```

4. Lokal funktion vikten värden: Använd följande funktionsanrop för att förklara en enskild instans eller en grupp av instanser.

    ```python
    # explain the first data point in the test set
    local_explanation = explainer.explain_local(x_test[0,:])
    or
    # explain the first five data points in the test set
    local_explanation = explainer.explain_local(x_test[0:4,:])
    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```

## <a name="train-and-explain-remotely"></a>Träna och förklarar via en fjärranslutning

Medan du kan träna på olika beräkningsmål som stöds av Azure Machine Learning-tjänsten, visar exemplet i det här avsnittet hur du gör detta med hjälp av AMLCompute.

1. Skapa ett inlärningsskript i en lokal Jupyter-anteckningsbok (till exempel run_explainer.py).

    ``` python  
    run = Run.get_context()
    client = ExplanationClient.from_run(run)
    
    breast_cancer_data = load_breast_cancer()
    X_train, X_test, y_train, y_test = train_test_split(breast_cancer_data.data, breast_cancer_data.target, test_size = 0.2, random_state = 0)
    data = {
        "train":{"X": X_train, "y": y_train},        
        "test":{"X": X_test, "y": y_test}
    }
    clf = svm.SVC(gamma=0.001, C=100., probability=True)
    model = clf.fit(data['train']['X'], data['train']['y'])
    joblib.dump(value = clf, filename = 'model.pkl')
    # explain predictions on your local machine    
    explainer = TabularExplainer(model, x_train, features=breast_cancer_data.feature_names, classes=classes)
    # explain overall model predictions (global explanation)
    global_explanation = explainer.explain_global(data["test"]["X"])
    # explain local data points (individual instances)
    local_explanation = explainer.explain_local(data["test"]["X"][0,:])
    # upload global and local explanation objects to Run History
    upload_model_explanation(run, local_explanation, top_k=2, comment='local explanation: top 2 features')
    # Uploading global model explanation data for storage or visualization in webUX
    # The explanation can then be downloaded on any compute
    # Multiple explanations can be uploaded
    client.upload_model_explanation(global_explanation, comment='global explanation: all features')
    # Or you can only upload the explanation object with the top k feature info
    #client.upload_model_explanation(global_explanation, top_k=2, comment='global explanation: Only top 2 features')
    ```

2. Följ anvisningarna på [konfigurera beräkningsmål för modellträning](how-to-set-up-training-targets.md#amlcompute) mer information om hur du konfigurerar en Azure Machine Learning Compute som din beräkningsmål och skicka in din utbildning-körning.

3. Hämta förklaring i din lokal Jupyter-anteckningsbok. 

    ``` python
    from azureml.contrib.explain.model.explanation.explanation_client import ExplanationClient
    # Get model explanation data
    client = ExplanationClient.from_run(run)
    explanation = client.download_model_explanation()
    local_importance_values = explanation.local_importance_values
    expected_values = explanation.expected_values
    # Or you can use the saved run.id to retrive the feature importance values
    client = ExplanationClient.from_run_id(ws, experiment_name, run.id)
    explanation = client.download_model_explanation()
    local_importance_values = explanation.local_importance_values
    expected_values = explanation.expected_values
    # Get the top k (e.g., 4) most important features with their importance values
    explanation = client.download_model_explanation(top_k=4)
    global_importance_values = explanation.get_ranked_global_values()
    global_importance_names = explanation.get_ranked_global_names()
    print('global importance values: {}'.format(global_importance_values))
    print('global importance names: {}'.format(global_importance_names))
    ```

## <a name="next-steps"></a>Nästa steg

En samling med Jupyter-anteckningsböcker som visar anvisningarna ovan finns i den [exempelanteckningsböcker som Azure Machine Learning Interpretability](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model).