---
title: 'Kör Python-skript: modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen kör Python-skript i Azure Machine Learning för att köra python-kod.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.custom: tracking-python
author: likebupt
ms.author: keli19
ms.date: 04/27/2020
ms.openlocfilehash: d25a738a76c955ee11f091bb0f8861bd21cc9f1d
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/09/2020
ms.locfileid: "84555869"
---
# <a name="execute-python-script-module"></a>Köra Python-skript modul

I den här artikeln beskrivs en modul i Azure Machine Learning designer (för hands version).

Använd den här modulen för att köra python-kod. Mer information om arkitektur och design principer för python finns i [följande artikel](https://docs.microsoft.com/azure/machine-learning/machine-learning-execute-python-scripts).

Med python kan du utföra uppgifter som för närvarande inte stöds av befintliga moduler, till exempel:

+ Visualisera data med`matplotlib`
+ Använda python-bibliotek för att räkna upp data uppsättningar och modeller i din arbets yta
+ Läsning, inläsning och manipulering av data från källor som inte stöds av modulen [Importera data](./import-data.md)
+ Kör din egen djup inlärnings kod 


Azure Machine Learning använder Anaconda-distributionen av python, som innehåller många vanliga verktyg för data bearbetning. Vi kommer att uppdatera Anaconda-versionen automatiskt. Aktuell version är:
 -  Anaconda 4.5 + distribution för python 3,6 

De förinstallerade paketen är:
-    ADAL = = 1.2.2
-    applicationinsights = = 0.11.9
-    attr = = 19.3.0
-    Azure-common = = 1.1.25
-    Azure-core = = 1.3.0
-    Azure-graphrbac = = 0.61.1
-    Azure-Identity = = 1.3.0
-    Azure-MGMT-Authorization = = 0.60.0
-    Azure-MGMT-containerregistry = = 2.8.0
-    Azure-MGMT-nyckel valv = = 2.2.0
-    Azure-MGMT-Resource = = 8.0.1
-    Azure-MGMT-Storage = = 8.0.0
-    Azure-Storage-BLOB = = 1.5.0
-    Azure-Storage – common = = 1.4.2
-    azureml-core = = 1.1.5.5
-    azureml-nu-Native = = 14.1.0
-    azureml-nu = = 1.3.5
-    azureml-standardvärden = = 1.1.5.1
-    azureml-designer-klassisk-modules = = 0.0.118
-    azureml-designer-Core = = 0.0.31
-    azureml-designer – Internal = = 0.0.18
-    azureml-Model-Management-SDK = = 1.0.1 B6. post1
-    azureml-pipeline-core = = 1.1.5
-    azureml-telemetri = = 1.1.5.3
-    backports. tempfile = = 1.0
-    backports. weakref = = 1.0. post1
-    boto3 = = 1.12.29
-    botocore = = 1.15.29
-    cachetools = = 4.0.0
-    certifiera = = 2019.11.28
-    cffi = = 1.12.3
-    chardet = = 3.0.4
-    Klicka på = = 7.1.1
-    cloudpickle = = 1.3.0
-    ConfigParser = = 3.7.4
-    contextlib2 = = 0.6.0. post1
-    kryptografi = = 2,8
-    cykel = = 0.10.0
-    dill = = 0.3.1.1
-    distribution = = 1.4.0
-    Docker = = 4.2.0
-    docutils = = 0.15.2
-    dotnetcore2 = = 2.1.13
-    kolv = = 1.0.3
-    fusepy = = 3.0.1
-    gensim = = 3.8.1
-    Google-API-core = = 1.16.0
-    Google-auth = = 1.12.0
-    Google-Cloud-core = = 1.3.0
-    Google-Cloud – Storage = = 1.26.0
-    Google-återupptagande-Media = = 0.5.0
-    googleapis – common-prot = = 1.51.0
-    gunicorn = = 19.9.0
-    IDNA = = 2.9
-    obalanserat-lär = = 0.4.3
-    isodate = = 0.6.0
-    itsdangerous = = 1.1.0
-    jeepney = = 0.4.3
-    jinja2 = = 2.11.1
-    jmespath = = 0.9.5
-    joblib = = 0.14.0
-    JSON-loggning – py = = 0,2
-    jsonpickle = = 1.3
-    jsonschema = = 3.0.1
-    kiwisolver = = 1.1.0
-    liac-arff = = 2.4.0
-    lightgbm = = 2.2.3
-    markupsafe = = 1.1.1
-    matplotlib = = 3.1.3
-    Mer-itertools = = 6.0.0
-    msal-Extensions = = 0.1.3
-    msal = = 1.1.0
-    msrest = = 0.6.11
-    msrestazure = = 0.6.3
-    NDG-httpsclient = = 0.5.1
-    nimbusml = = 1.6.1
-    numpy = = 1.18.2
-    oauthlib = = 3.1.0
-    Pandas = = 0.25.3
-    pathspec = = 0.7.0
-    PIP = = 20.0.2
-    portalocker = = 1.6.0
-    protobuf = = 3.11.3
-    pyarrow = = 0.16.0
-    pyasn1-modules = = 0.2.8
-    pyasn1 = = 0.4.8
-    pycparser = = 2.20
-    pycryptodomex = = 3.7.3
-    pyjwt = = 1.7.1
-    pyopenssl = = 19.1.0
-    pyparsing = = 2.4.6
-    pyrsistent = = 0.16.0
-    python-dateutil = = 2.8.1
-    pytz = = 2019.3
-    begär Anden-oauthlib = = 1.3.0
-    begär Anden = = 2.23.0
-    RSA = = 4.0
-    ruamel. yaml = = 0.15.89
-    s3transfer = = 0.3.3
-    scikit – lära = = 0.22.2
-    scipy = = 1.4.1
-    secretstorage = = 3.1.2
-    installations verktyg = = 46.1.1. post20200323
-    sex = = 1.14.0
-    Smart-öppen = = 1.10.0
-    urllib3 = = 1.25.8
-    WebSocket-Client = = 0.57.0
-    werkzeug = = 0.16.1
-    Wheel = = 0.34.2

 Om du vill installera andra paket som inte finns i listan i förväg, till exempel *scikit-misc*, lägger du till följande kod i skriptet: 

 ```python
import os
os.system(f"pip install scikit-misc")
```
> [!NOTE]
> Om din pipeline innehåller flera köra Python-skript moduler och behöver samma paket som inte finns i den förinstallerade listan, måste du installera paketen i varje modul. 

## <a name="upload-files"></a>Överföra filer
**Execute python-skriptet** stöder överföring av filer med hjälp av [Azure Machine Learning python SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py#upload-file-name--path-or-stream-).

I följande exempel visas hur du laddar upp en avbildnings fil i modulen **Kör Python-skript** :

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

När pipeline-körningen är färdig kan du förhandsgranska bilden i den högra panelen i modulen

> [!div class="mx-imgBorder"]
> ![Överförd bild](media/module/upload-image-in-python-script.png)

## <a name="how-to-configure-execute-python-script"></a>Så här konfigurerar du kör Python-skript

**Execute Python-skript** module innehåller exempel på python-kod som du kan använda som start punkt. Om du vill konfigurera **execute Python-skript** -modulen anger du en uppsättning indata och python-kod som ska köras i text rutan **Python-skript** .

1. Lägg till modulen **Kör Python-skript** i din pipeline.

2. Lägg till och Anslut på **Dataset1** alla data uppsättningar från designern som du vill använda för indata. Referera till den här data uppsättningen i python-skriptet som **DataFrame1**.

    Användningen av en data uppsättning är valfri, om du vill generera data med python eller använda python-kod för att importera data direkt till modulen.

    Den här modulen stöder tillägg av en andra data uppsättning på **Dataset2**. Referera till den andra data mängden i python-skriptet som DataFrame2.

    Data uppsättningar som lagras i Azure Machine Learning konverteras automatiskt till **Pandas** -data. bild rutor när de läses in med den här modulen.

    ![Köra python-indatamängds karta](media/module/python-module.png)

4. Om du vill inkludera nya python-paket eller-kod lägger du till den zippade filen som innehåller de här anpassade resurserna i **skript paket**. Indata till **skript paket** måste vara en zippad fil som överförs till din arbets yta som en fil typs data uppsättning. Du kan ladda upp data uppsättningen på sidan **data uppsättningar** till gång och du kan dra och släppa data uppsättnings modulen från listan **mina data uppsättningar** i den vänstra modulens träd design sida. 

    Alla filer som finns i det överförda zippade arkivet kan användas under pipeline-körningen. Om arkivet innehåller en katalog struktur bevaras strukturen, men du måste lägga en katalog med namnet **src** till sökvägen.

5. I text rutan **Python-skript** skriver eller klistrar du in giltigt Python-skript.

    > [!NOTE]
    > Var noga med att skriva skriptet och se till att det inte finns några syntaxfel, till exempel att använda ej deklarerade objekt eller ej importerade moduler. Betala också extra uppmärksamhet till listan förinstallerad modul. Om du vill importera moduler som inte finns med i listan installerar du motsvarande paket i skriptet, till exempel
    >  ``` Python
    > import os
    > os.system(f"pip install scikit-misc")
    > ```
    
    Text rutan **Python-skript** fylls i automatiskt med instruktioner i kommentarer och exempel kod för data åtkomst och utdata. Du måste redigera eller ersätta den här koden. Se till att följa python-konventioner om indrag och Skift läge.

    + Skriptet måste innehålla en funktion som heter `azureml_main` som start punkt för den här modulen.
    + Start punkt funktionen måste ha två indataargument: `Param<dataframe1>` och `Param<dataframe2>` , även om dessa argument inte används i skriptet.
    + Zippade filer som är anslutna till den tredje Indataporten är zippade och lagras i katalogen, `.\Script Bundle` som också läggs till i python `sys.path` . 

    Om din zip-fil innehåller kan du därför `mymodule.py` Importera den med hjälp av `import mymodule` .

    + Två data uppsättningar kan returneras till designern, som måste vara en sekvens av typen `pandas.DataFrame` . Du kan skapa andra utdata i python-koden och skriva dem direkt till Azure Storage.

6. Skicka pipelinen eller Välj modulen och klicka på **Kör valt** för att bara köra python-skriptet.

    Alla data och all kod läses in i en virtuell dator och körs med den angivna python-miljön.

## <a name="results"></a>Resultat

Resultaten av alla beräkningar som utförs av den inbäddade python-koden måste anges som Pandas. DataFrame, som automatiskt konverteras till det Azure Machine Learning data uppsättnings formatet, så att du kan använda resultatet med andra moduler i pipelinen.

Modulen returnerar två data uppsättningar:  
  
+ **Resultat data uppsättning 1**, som definieras av den första returnerade Pandas-Dataframe i python-skriptet

+ **Resultat data uppsättning 2**, som definieras av den andra returnerade Pandas-Dataframe i python-skriptet


## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 
