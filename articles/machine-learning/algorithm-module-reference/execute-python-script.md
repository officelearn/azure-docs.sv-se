---
title: 'Kör Python-skript: Modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen Kör Python-skript i Azure Machine Learning för att köra Python-kod.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/10/2020
ms.openlocfilehash: 79dc1b188e91028a98f43dc24972228f2d2101be
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684734"
---
# <a name="execute-python-script-module"></a>Kör Python-skriptmodul

I den här artikeln beskrivs en modul i Azure Machine Learning designer (förhandsversion).

Använd den här modulen för att köra Python-kod. Mer information om Pythons arkitektur- och designprinciper finns [i följande artikel](https://docs.microsoft.com/azure/machine-learning/machine-learning-execute-python-scripts).

Med Python kan du utföra uppgifter som för närvarande inte stöds av befintliga moduler, till exempel:

+ Visualisera data med hjälp av`matplotlib`
+ Använda Python-bibliotek för att räkna upp datauppsättningar och modeller på arbetsytan
+ Läsa, läsa in och manipulera data från källor som inte stöds av modulen [Importera data](./import-data.md)
+ Kör din egen deep learning-kod 


Azure Machine Learning använder Anaconda-distributionen av Python, som innehåller många vanliga verktyg för databearbetning. Vi kommer att uppdatera Anaconda version automatiskt. Nuvarande version är:
 -  Anaconda 4,5+ distribution för Python 3.6 

De förinstallerade paketen är:
-    adal==1.2.2
-    applicationinsights==0.11.9
-    attrs==19.3.0
-    azure-common==1.1.25
-    azure-core==1.3.0
-    azure-graphrbac==0.61.1
-    azure-identity==1.3.0
-    azure-mgmt-authorization==0.60.0
-    azure-mgmt-containerregistry==2.8.0
-    azurblå-mgmt-keyvault==2.2.0
-    azure-mgmt-resource==8.0.1
-    azure-mgmt-storage==8.0.0
-    azure-storage-blob==1.5.0
-    azure-storage-common==1.4.2
-    azureml-core==1.1.5.5
-    azureml-dataprep-native==14.1.0
-    azureml-dataprep==1.3.5
-    azureml-defaults==1.1.5.1
-    azureml-designer-classic-modules==0.0.118
-    azureml-designer-core==0.0.31
-    azureml-designer-intern==0.0.18
-    azureml-modell-management-sdk==1.0.1b6.post1
-    azureml-pipeline-core==1.1.5
-    azureml-telemetri==1.1.5.3
-    backports.tempfile==1,0
-    backports.weakref==1.0.post1
-    boto3==1.12.29
-    botocore==1.15.29
-    cachetools==4.0.0
-    certifi==2019.11.28
-    cffi==1.12.3
-    chardet==3.0.4
-    klicka på==7.1.1
-    cloudpickle==1.3.0
-    configparser==3.7.4
-    contextlib2==0.6.0.post1
-    kryptografi==2,8
-    cykel==0,10,0
-    dill==0.3.1.1
-    distro==1.4.0
-    docker==4.2.0
-    docutils==0.15.2
-    dotnetcore2==2.1.13
-    kolv==1.0.3
-    fusepy==3.0.1
-    gensim==3.8.1
-    google-api-core==1.16.0
-    google-auth==1.12.0
-    google-cloud-core==1.3.0
-    google-moln-lagring==1.26.0
-    google-resumable-media==0.5.0
-    googleapis-common-protos==1.51.0
-    gunicorn ==19.9.0
-    idna==2,9
-    obalanserad-learn==0.4.3
-    isodate==0.6.0
-    itsdangerous==1.1.0
-    jeepney==0.4.3
-    jinja2==2.11.1
-    jmespath==0.9.5
-    joblib==0.14.0
-    json-loggning-py==0,2
-    jsonpickle==1,3
-    jsonschema==3.0.1
-    kiwisolver==1.1.0
-    liac-arff==2.4.0
-    lightgbm==2.2.3
-    markupsafe==1.1.1
-    matplotlib==3.1.3
-    mer itertools==6.0.0
-    msal-extensions==0.1.3
-    msal==1.1.0
-    msrest==0.6.11
-    msrestazure==0.6.3
-    ndg-httpsclient==0.5.1
-    nimbusml==1.6.1
-    numpy==1.18.2
-    oauthlib==3.1.0
-    pandor==0.25.3
-    pathspec==0.7.0
-    pip==20.0.2
-    portalocker==1.6.0
-    protobuf==3.11.3
-    pyarrow==0.16.0
-    pyasn1-moduler==0.2.8
-    pyasn1==0,4,8
-    pycparser==2,20
-    pycryptodomex==3.7.3
-    pyjwt==1.7.1
-    pyopenssl==19.1.0
-    pyparsing==2.4.6
-    pyrsistent==0.16.0
-    python-dateutil==2.8.1
-    pytz==2019.3
-    begäran-oauthlib==1.3.0
-    begäranden==2.23.0
-    rsa==4,0
-    ruamel.yaml==0.15.89
-    s3transfer==0.3.3
-    scikit-learn==0.22.2
-    scipy ==1.4.1
-    förstoring==3.1.2
-    setuptools==46.1.1.post20200323
-    sex==1.14.0
-    smart-open==1.10.0
-    urllib3==1.25.8
-    websocket-client==0.57.0
-    werkzeug==0.16.1
-    hjul==0.34.2

 Om du vill installera andra paket som inte finns i den förinstallerade listan, till exempel *scikit-misc,* lägger du till följande kod i skriptet: 

 ```python
import os
os.system(f"pip install scikit-misc")
```

## <a name="upload-files"></a>Överföra filer
**Kör Python-skriptet** stöder överföring av filer med [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py#upload-file-name--path-or-stream-).

I följande exempel visas hur du laddar upp en bildfil i modulen **Kör Python-skript:**

```Python

# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# imports up here can be used to
import pandas as pd

# The entry point function must have two input arguments:
#   Param<dataframe1>: a pandas.DataFrame
#   Param<dataframe2>: a pandas.DataFrame
def azureml_main(dataframe1 = None, dataframe2 = None):

    # Execution logic goes here
    print(f'Input pandas.DataFrame #1: {dataframe1}')

    from matplotlib import pyplot as plt
    plt.plot([1, 2, 3, 4])
    plt.ylabel('some numbers')
    img_file = "line.png"
    plt.savefig(img_file)

    from azureml.core import Run
    run = Run.get_context(allow_offline=True)
    run.upload_file(f"graphics/{img_file}", img_file)

    # Return value must be of a sequence of pandas.DataFrame
    # E.g.
    #   -  Single return value: return dataframe1,
    #   -  Two return values: return dataframe1, dataframe2
    return dataframe1,
}
```

När pipelinekörningen är klar kan du förhandsgranska bilden på modulens högra panel

> [!div class="mx-imgBorder"]
> ![Uppladdad bild](media/module/upload-image-in-python-script.png)

## <a name="how-to-configure-execute-python-script"></a>Konfigurera Kör Python-skript

Modulen **Kör Python-skript** innehåller exempel på Python-kod som du kan använda som utgångspunkt. Om du vill konfigurera modulen **Kör Python-skript** tillhandahåller du en uppsättning indata och Python-kod som ska köras i textrutan **Python-skript.**

1. Lägg till modulen **Kör Python-skript** i pipelinen.

2. Lägg till och anslut **datauppsättning1** alla datauppsättningar från designern som du vill använda för indata. Referera till den här datauppsättningen i Python-skriptet som **DataFrame1**.

    Det är valfritt att använda en datauppsättning om du vill generera data med Python eller använda Python-kod för att importera data direkt till modulen.

    Den här modulen stöder tillägg av en andra datauppsättning på **Dataset2**. Referera till den andra datauppsättningen i Python-skriptet som DataFrame2.

    Datauppsättningar som lagras i Azure Machine Learning konverteras automatiskt till **pandas** data.frames när de läses in med den här modulen.

    ![Kör Python-indatakarta](media/module/python-module.png)

4. Om du vill inkludera nya Python-paket eller kod lägger du till den zippade filen som innehåller dessa anpassade resurser i **skriptpaketet**. Indata till **skriptpaketet** måste vara en zippad fil som laddas upp till arbetsytan som datauppsättning av filtyp. Du kan ladda upp datauppsättningen på sidan **Datamängdstillgång** och du kan dra och släppa datauppsättningsmodulen från listan **Mina datauppsättningar** i det vänstra modulträdet på sidan designerförfattande. 

    Alla filer som finns i det uppladdade zippade arkivet kan användas under pipelinekörningen. Om arkivet innehåller en katalogstruktur bevaras strukturen, men du måste förbereda en katalog som kallas **src** till sökvägen.

5. Skriv eller klistra in giltigt **Python-skript** i textrutan Python.

    > [!NOTE]
    > Var mycket försiktig när du skriver skriptet och se till att det inte finns något syntaxfel, till exempel att använda ett odeklarerat objekt eller en icke-importerad modul. Också ägna extra uppmärksamhet åt den förinstallerade modullistan. Om du vill importera moduler som inte finns med i listan installerar du motsvarande paket i skriptet, till exempel
    >  ``` Python
    > import os
    > os.system(f"pip install scikit-misc")
    > ```
    
    Textrutan **Python-skript** fylls i i flera instruktioner i kommentarerna och exempelkod för dataåtkomst och utdata. Du måste redigera eller ersätta den här koden. Var noga med att följa Python-konventioner om indrag och hölje.

    + Skriptet måste innehålla `azureml_main` en funktion som heter som startpunkt för den här modulen.
    + Startpunktsfunktionen måste ha två `Param<dataframe1>` `Param<dataframe2>`indataargument: och även om dessa argument inte används i skriptet.
    + Zippade filer som är anslutna till den tredje indataporten packas upp och lagras i katalogen, `.\Script Bundle`som också läggs till i Python `sys.path`. 

    Om zip-filen innehåller `mymodule.py`importerar du `import mymodule`den därför med .

    + Två datauppsättningar kan returneras till designern, som `pandas.DataFrame`måste vara en sekvens av typ . Du kan skapa andra utdata i python-koden och skriva dem direkt till Azure-lagring.

6. Skicka pipelinen eller välj modulen och klicka på **Kör markerad** för att bara köra Python-skriptet.

    Alla data och kod läses in på en virtuell dator och körs med den angivna Python-miljön.

## <a name="results"></a>Resultat

Resultaten av alla beräkningar som utförs av den inbäddade Python-koden måste tillhandahållas som pandor. DataFrame, som automatiskt konverteras till Azure Machine Learning-datauppsättningsformatet, så att du kan använda resultaten med andra moduler i pipelinen.

Modulen returnerar två datauppsättningar:  
  
+ **ResultatDatauppsättning 1**, definierad av den första returnerade pandas-dataramen i Python-skriptet

+ **Resultatdatauppsättning 2**, definierad av den andra returnerade pandas-dataramen i Python-skriptet


## <a name="next-steps"></a>Nästa steg

Se uppsättningen [moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 