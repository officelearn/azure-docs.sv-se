---
title: Utvärdera ML-modeller skälighet i python (för hands version)
titleSuffix: Azure Machine Learning
description: Lär dig hur du bedömer och minimerar skälighet för dina maskin inlärnings modeller med Fairlearn och Azure Machine Learning python SDK.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: mesameki
author: mesameki
ms.reviewer: luquinta
ms.date: 11/16/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 3fbd4990fd330960bb8dbce2e2a8d1bcb578cf2a
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94701192"
---
# <a name="use-azure-machine-learning-with-the-fairlearn-open-source-package-to-assess-the-fairness-of-ml-models-preview"></a>Använd Azure Machine Learning med Fairlearn-paketet med öppen källkod för att utvärdera skälighet för ML-modeller (för hands version)

I den här instruktions guiden får du lära dig att använda python-paketet [Fairlearn](https://fairlearn.github.io/) med öppen källkod med Azure Machine Learning för att utföra följande uppgifter:

* Utvärdera skälighet för dina modell förutsägelser. Mer information om skälighet i Machine Learning finns i [artikeln skälighet i Machine Learning](concept-fairness-ml.md).
* Ladda upp, Visa och ladda ned skälighet Assessment Insights till/från Azure Machine Learning Studio.
* Se en instrument panel för skälighet utvärdering i Azure Machine Learning Studio för att interagera med din modell (er) skälighet Insights.

>[!NOTE]
> Skälighet-utvärderingen är inte en helt teknisk övning. **Det här paketet kan hjälpa dig att utvärdera skälighet för en Machine Learning-modell, men bara du kan konfigurera och fatta beslut om hur modellen ska prestera.**  Även om det här paketet hjälper till att identifiera kvantitativa mått för att utvärdera skälighet, måste utvecklare av Machine Learning-modeller också utföra en kvalitativ analys för att utvärdera skälighet för sina egna modeller.

## <a name="azure-machine-learning-fairness-sdk"></a>Azure Machine Learning skälighet SDK 

Azure Machine Learning skälighet SDK, `azureml-contrib-fairness` integrerar python-paketet med öppen källkod, [Fairlearn](http://fairlearn.github.io), i Azure Machine Learning. Om du vill veta mer om Fairlearn-integrering i Azure Machine Learning kan du titta på de här [exempel antecknings böckerna](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness). Mer information om Fairlearn finns i [exempel guiden](https://fairlearn.github.io/master/auto_examples/) och exempel på [bärbara datorer](https://github.com/fairlearn/fairlearn/tree/master/notebooks). 

Använd följande kommandon för att installera- `azureml-contrib-fairness` och- `fairlearn` paketen:
```bash
pip install azureml-contrib-fairness
pip install fairlearn==0.4.6
```



## <a name="upload-fairness-insights-for-a-single-model"></a>Ladda upp skälighet Insights för en enskild modell

I följande exempel visas hur du använder skälighet-paketet för att överföra modell skälighet Insights till Azure Machine Learning och se instrument panelen för skälighet-utvärdering i Azure Machine Learning Studio.

1. Träna en exempel modell i en Jupyter Notebook. 

    För data uppsättningen använder vi den välkända data uppsättningen från den välkända uppsättningen, som vi läser in med `shap` (för enkelhetens skull). I det här exemplet behandlar vi den här data uppsättningen som ett beslut om lån och Föreställ dig att etiketten visar om varje person återbetalade ett lån tidigare. Vi kommer att använda data för att träna en förväntare att förutse om tidigare osett personer kommer att återbetala ett lån eller inte. Antagande är att modell förutsägelserna används för att avgöra om en individ ska erbjudas ett lån.

    ```python
    from sklearn.model_selection import train_test_split
    from fairlearn.widget import FairlearnDashboard
    from sklearn.linear_model import LogisticRegression
    from sklearn.preprocessing import LabelEncoder, StandardScaler
    import pandas as pd
    import shap

    # Load the census dataset
    X_raw, Y = shap.datasets.adult()
    X_raw["Race"].value_counts().to_dict()
    

    # (Optional) Separate the "sex" and "race" sensitive features out and drop them from the main data prior to training your model
    A = X_raw[['Sex','Race']]
    X = X_raw.drop(labels=['Sex', 'Race'],axis = 1)
    X = pd.get_dummies(X)
    
    sc = StandardScaler()
    X_scaled = sc.fit_transform(X)
    X_scaled = pd.DataFrame(X_scaled, columns=X.columns)

    # Perform some standard data preprocessing steps to convert the data into a format suitable for the ML algorithms
    le = LabelEncoder()
    Y = le.fit_transform(Y)

    # Split data into train and test
    from sklearn.model_selection import train_test_split
    from sklearn.model_selection import train_test_split
    X_train, X_test, Y_train, Y_test, A_train, A_test = train_test_split(X_scaled, 
                                                        Y, 
                                                        A,
                                                        test_size = 0.2,
                                                        random_state=0,
                                                        stratify=Y)

    # Work around indexing issue
    X_train = X_train.reset_index(drop=True)
    A_train = A_train.reset_index(drop=True)
    X_test = X_test.reset_index(drop=True)
    A_test = A_test.reset_index(drop=True)

    # Improve labels
    A_test.Sex.loc[(A_test['Sex'] == 0)] = 'female'
    A_test.Sex.loc[(A_test['Sex'] == 1)] = 'male'


    A_test.Race.loc[(A_test['Race'] == 0)] = 'Amer-Indian-Eskimo'
    A_test.Race.loc[(A_test['Race'] == 1)] = 'Asian-Pac-Islander'
    A_test.Race.loc[(A_test['Race'] == 2)] = 'Black'
    A_test.Race.loc[(A_test['Race'] == 3)] = 'Other'
    A_test.Race.loc[(A_test['Race'] == 4)] = 'White'


    # Train a classification model
    lr_predictor = LogisticRegression(solver='liblinear', fit_intercept=True)
    lr_predictor.fit(X_train, Y_train)

    # (Optional) View this model in Fairlearn's fairness dashboard, and see the disparities which appear:
    from fairlearn.widget import FairlearnDashboard
    FairlearnDashboard(sensitive_features=A_test, 
                       sensitive_feature_names=['Sex', 'Race'],
                       y_true=Y_test,
                       y_pred={"lr_model": lr_predictor.predict(X_test)})
    ```

2. Logga in på Azure Machine Learning och registrera din modell.
   
    Skälighet-instrumentpanelen kan integreras med registrerade eller oregistrerade modeller. Registrera din modell i Azure Machine Learning med följande steg:
    ```python
    from azureml.core import Workspace, Experiment, Model
    import joblib
    import os
    
    ws = Workspace.from_config()
    ws.get_details()

    os.makedirs('models', exist_ok=True)
    
    # Function to register models into Azure Machine Learning
    def register_model(name, model):
        print("Registering ", name)
        model_path = "models/{0}.pkl".format(name)
        joblib.dump(value=model, filename=model_path)
        registered_model = Model.register(model_path=model_path,
                                        model_name=name,
                                        workspace=ws)
        print("Registered ", registered_model.id)
        return registered_model.id

    # Call the register_model function 
    lr_reg_id = register_model("fairness_logistic_regression", lr_predictor)
    ```

3. Precompute skälighet-mått.

    Skapa en instrument panels ord lista med Fairlearn- `metrics` paketet. `_create_group_metric_set`Metoden innehåller argument som liknar konstruktorn för instrument panelen, förutom att de känsliga funktionerna skickas som en ord lista (för att säkerställa att namnen är tillgängliga). Vi måste också ange typen av förutsägelse (binär klassificering i det här fallet) när du anropar den här metoden.

    ```python
    #  Create a dictionary of model(s) you want to assess for fairness 
    sf = { 'Race': A_test.Race, 'Sex': A_test.Sex}
    ys_pred = { lr_reg_id:lr_predictor.predict(X_test) }
    from fairlearn.metrics._group_metric_set import _create_group_metric_set

    dash_dict = _create_group_metric_set(y_true=Y_test,
                                        predictions=ys_pred,
                                        sensitive_features=sf,
                                        prediction_type='binary_classification')
    ```
4. Ladda upp de förberäknade skälighet-måtten.
    
    Nu `azureml.contrib.fairness` ska du importera paket för att utföra uppladdningen:

    ```python
    from azureml.contrib.fairness import upload_dashboard_dictionary, download_dashboard_by_upload_id
    ```
    Skapa ett experiment, sedan en körning och ladda upp instrument panelen till den:
    ```python
    exp = Experiment(ws, "Test_Fairness_Census_Demo")
    print(exp)

    run = exp.start_logging()

    # Upload the dashboard to Azure Machine Learning
    try:
        dashboard_title = "Fairness insights of Logistic Regression Classifier"
        # Set validate_model_ids parameter of upload_dashboard_dictionary to False if you have not registered your model(s)
        upload_id = upload_dashboard_dictionary(run,
                                                dash_dict,
                                                dashboard_name=dashboard_title)
        print("\nUploaded to id: {0}\n".format(upload_id))
        
        # To test the dashboard, you can download it back and ensure it contains the right information
        downloaded_dict = download_dashboard_by_upload_id(run, upload_id)
    finally:
        run.complete()
    ```
5. Kontrol lera skälighet-instrumentpanelen från Azure Machine Learning Studio

    Om du slutför de föregående stegen (laddar upp genererade skälighet insikter till Azure Machine Learning) kan du Visa skälighet-instrumentpanelen i [Azure Machine Learning Studio](https://ml.azure.com). Den här instrument panelen är samma visualiserings instrument panel som finns i Fairlearn, vilket gör att du kan analysera skillnaderna mellan den känsliga funktionens under grupper (t. ex. hane vs. hona).
    Följ någon av dessa sökvägar för att få åtkomst till instrument panelen för visualiseringar i Azure Machine Learning Studio:

    * **Experiment fönstret (förhands granskning)**
    1. Välj **experiment** i det vänstra fönstret om du vill se en lista över experiment som du har kört på Azure Machine Learning.
    1. Välj ett särskilt experiment för att visa alla körningar i experimentet.
    1. Välj en körning och sedan fliken **skälighet** till instrument panelen förklarings visualisering.


    [![Skälighet-instrumentpanel](./media/how-to-machine-learning-fairness-aml/dashboard.png)](./media/how-to-machine-learning-fairness-aml/dashboard.png#lightbox)
    
    * **Fönstret modeller**
    1. Om du har registrerat din ursprungliga modell genom att följa föregående steg kan du välja **modeller** i det vänstra fönstret för att visa den.
    1. Välj en modell och klicka sedan på fliken **skälighet** för att visa instrument panelen för förklarings visualisering.

    Om du vill veta mer om instrument panelen för visualiseringar och vad den innehåller kan du checka ut Fairlearn [Användar handbok](https://fairlearn.github.io/master/user_guide/assessment.html#fairlearn-dashboard).

## <a name="upload-fairness-insights-for-multiple-models"></a>Ladda upp skälighet Insights för flera modeller

Om du är intresse rad av att jämföra flera modeller och se hur deras skälighet-utvärdering skiljer sig åt, kan du skicka mer än en modell till instrument panelen för visualiseringar och navigera i deras skälighet-kompromisser.

1. Träna dina modeller:
    
    Förutom den tidigare logistik Regressions modellen skapar vi nu en andra klassificerare, baserat på en utvärderings version av en support Vector-dator och laddar upp en skälighet-instrumentpanel med hjälp av Fairlearn- `metrics` paketet. Observera att vi hoppar över stegen för att läsa in och Förbearbeta data och gå direkt till modell inlärnings fasen.


    ```python
    # Train your first classification model
    from sklearn.linear_model import LogisticRegression
    lr_predictor = LogisticRegression(solver='liblinear', fit_intercept=True)
    lr_predictor.fit(X_train, Y_train)

    # Train your second classification model
    from sklearn import svm
    svm_predictor = svm.SVC()
    svm_predictor.fit(X_train, Y_train)
    ```

2. Registrera dina modeller

    Nästa registrera båda modellerna i Azure Machine Learning. För att under lätta efterföljande metod anrop lagrar du resultatet i en ord lista, som mappar den `id` registrerade modellen (en sträng i `name:version` formatet) till själva för själva rollen:

    ```python
    model_dict = {}

    lr_reg_id = register_model("fairness_logistic_regression", lr_predictor)
    model_dict[lr_reg_id] = lr_predictor

    svm_reg_id = register_model("fairness_svm", svm_predictor)
    model_dict[svm_reg_id] = svm_predictor
    ```

3. Läs in Fairlearn-instrumentpanelen lokalt

    Innan du laddar upp skälighet Insights i Azure Machine Learning kan du undersöka dessa förutsägelser i en lokalt anropad Fairlearn-instrumentpanel. 



    ```python
    #  Generate models' predictions and load the fairness dashboard locally 
    ys_pred = {}
    for n, p in model_dict.items():
        ys_pred[n] = p.predict(X_test)

    from fairlearn.widget import FairlearnDashboard

    FairlearnDashboard(sensitive_features=A_test, 
                    sensitive_feature_names=['Sex', 'Race'],
                    y_true=Y_test.tolist(),
                    y_pred=ys_pred)
    ```

3. Precompute skälighet-mått.

    Skapa en instrument panels ord lista med Fairlearn- `metrics` paketet.

    ```python
    sf = { 'Race': A_test.Race, 'Sex': A_test.Sex }

    from fairlearn.metrics._group_metric_set import _create_group_metric_set

    dash_dict = _create_group_metric_set(y_true=Y_test,
                                        predictions=ys_pred,
                                        sensitive_features=sf,
                                        prediction_type='binary_classification')
    ```
4. Ladda upp de förberäknade skälighet-måtten.
    
    Nu `azureml.contrib.fairness` ska du importera paket för att utföra uppladdningen:

    ```python
    from azureml.contrib.fairness import upload_dashboard_dictionary, download_dashboard_by_upload_id
    ```
    Skapa ett experiment, sedan en körning och ladda upp instrument panelen till den:
    ```python
    exp = Experiment(ws, "Compare_Two_Models_Fairness_Census_Demo")
    print(exp)

    run = exp.start_logging()

    # Upload the dashboard to Azure Machine Learning
    try:
        dashboard_title = "Fairness Assessment of Logistic Regression and SVM Classifiers"
        # Set validate_model_ids parameter of upload_dashboard_dictionary to False if you have not registered your model(s)
        upload_id = upload_dashboard_dictionary(run,
                                                dash_dict,
                                                dashboard_name=dashboard_title)
        print("\nUploaded to id: {0}\n".format(upload_id))
        
        # To test the dashboard, you can download it back and ensure it contains the right information
        downloaded_dict = download_dashboard_by_upload_id(run, upload_id)
    finally:
        run.complete()
    ```


    Precis som i föregående avsnitt kan du följa en av Sök vägarna som beskrivs ovan (via **experiment** eller **modeller**) i Azure Machine Learning Studio för att få åtkomst till visualiserings instrument panelen och jämföra de två modellerna med skälighet och prestanda.


## <a name="upload-unmitigated-and-mitigated-fairness-insights"></a>Ladda upp minimerade och minimerade skälighet-insikter

Du kan använda Fairlearn för [mitigation algorithms](https://fairlearn.github.io/master/user_guide/mitigation.html)att jämföra deras genererade, försämrade modell (er) till den ursprungliga minimerade modellen och navigera i prestanda-/skälighet-kompromisser mellan jämförda modeller.

För att se ett exempel som demonstrerar användningen av algoritmen för [Rutnäts sökning](https://fairlearn.github.io/master/user_guide/mitigation.html#grid-search) (som skapar en samling av minimerade modeller med olika skälighet och prestanda handeln) kan du kolla in den här [exempel antecknings boken](https://github.com/Azure/MachineLearningNotebooks/blob/master/contrib/fairness/fairlearn-azureml-mitigation.ipynb). 

Att ladda upp flera modeller skälighet insikter i en enda körning möjliggör jämförelse av modeller med avseende på skälighet och prestanda. Du kan också klicka på någon av de modeller som visas i modell jämförelse diagrammet för att se detaljerade skälighet insikter om den aktuella modellen.


[![Modell jämförelse Fairlearn-instrumentpanel](./media/how-to-machine-learning-fairness-aml/multi-model-dashboard.png)](./media/how-to-machine-learning-fairness-aml/multi-model-dashboard.png#lightbox)
    

## <a name="next-steps"></a>Nästa steg

[Lär dig mer om modell skälighet](concept-fairness-ml.md)

[Kolla Azure Machine Learning skälighet-exempel Notebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness)
