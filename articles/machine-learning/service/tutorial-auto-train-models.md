---
title: 'Självstudie om regressionsmodell: Automatisk ML'
titleSuffix: Azure Machine Learning service
description: Lär dig att skapa en maskininlärningsmodell med automatiserad maskininlärning. Azure Machine Learning kan förbearbeta data, välja algoritm och hyperparameter på ett automatiserat sätt åt dig. Den slutliga modellen distribueras sedan med Azure Machine Learning-tjänsten.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: nacharya1
ms.author: nilesha
ms.reviewer: trbye
ms.date: 04/11/2019
ms.custom: seodec18
ms.openlocfilehash: ee024d627efc42a87d7f6b1971fa8e2e92357a00
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60760222"
---
# <a name="tutorial-use-automated-machine-learning-to-build-your-regression-model"></a>Självstudier: Använda automatiserad maskininlärning för att skapa en regressionsmodell

Självstudien är **del två i en självstudieserie i två delar**. I föregående självstudie [förberedde du NYC-taxidata för regressionsmodellering](tutorial-data-prep.md).

Nu är du redo att börja skapa modellen med Azure Machine Learning-tjänsten. I den här delen av självstudien använder du förberedda data och automatiskt generera en regressionsmodell för att förutsäga taxipriser. Genom att använda automatiska maskininlärningsfunktioner för tjänsten kan du definiera dina maskininlärningsmål och -begränsningar. Du startar den automatiska processen för maskininlärning. Låt sedan algoritmval och finjustering av hyperparameter ske. Den automatiserade maskininlärningsmetoden itererar i många kombinationer av algoritmer och hyperparametrar tills den hittar den bästa modellen utifrån dina kriterier.

![Flödesdiagram](./media/tutorial-auto-train-models/flow2.png)

I den här självstudien kommer du att lära dig följande:

> [!div class="checklist"]
> * Konfigurera en Python-miljö och importera SDK-paketen.
> * Konfigurera en Azure Machine Learning-tjänstarbetsyta.
> * Träna en regressionsmodell automatiskt.
> * Köra modellen lokalt med anpassade parametrar.
> * Utforska resultaten.

Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria versionen eller betalversionen av Azure Machine Learning-tjänsten](https://aka.ms/AMLFree) i dag.

>[!NOTE]
> Koden i den här artikeln har testats med Azure Machine Learning SDK version 1.0.0.

## <a name="prerequisites"></a>Nödvändiga komponenter

Gå vidare till [Ställ in din utvecklingsmiljö](#start) och läs igenom stegen för notebook eller följ instruktionerna nedan för att hämta din notebook och kör den på Azure Notebooks eller din egen Notebook-server. För att köra anteckningsboken behöver du:

* [Kör självstudien för dataförberedelse](tutorial-data-prep.md).
* En notebook-server för Python 3.6 med följande installerat:
    * Azure Machine Learning SDK för Python med extrafunktionerna `automl` och `notebooks`
    * `matplotlib`
* Anteckningsboken för självstudie
* En Machine Learning-arbetsyta
* Konfigurationsfilen för arbetsytan i samma katalog som anteckningsboken

Hämta alla dessa förutsättningar från något av avsnitten nedan.

* Använd [Azure Notebooks](#azure)
* Använd [din egen Notebook-server](#server)

### <a name="azure"></a>Använd Azure Notebooks: Kostnadsfria Jupyter-anteckningsböcker i molnet

Det är lätt att komma igång med Azure Notebooks! [Azure Machine Learning SDK för Python](https://aka.ms/aml-sdk) har redan installerats och konfigurerats för dig i [Azure Notebooks](https://notebooks.azure.com/). Installationen och framtida uppdateringar hanteras automatiskt via Azure-tjänster.

När du har slutfört stegen nedan kan du köra anteckningsboken **tutorials/regression-part2-automated-ml.ipynb** i ditt **Komma igång**-projekt.

[!INCLUDE [aml-azure-notebooks](../../../includes/aml-azure-notebooks.md)]

### <a name="server"></a>Använda en egen Jupyter Notebook-server

Skapa en lokal Jupyter Notebook-server på datorn enligt nedan.  Se till att du installerar `matplotlib` och `automl` och `notebooks` tillägg i din miljö.

[!INCLUDE [aml-your-server](../../../includes/aml-your-server.md)]

När du har slutfört stegen kör du anteckningsboken **tutorials/regression-part2-automated-ml.ipynb**.

## <a name="start"></a>Konfigurera din utvecklingsmiljö

All konfiguration under utvecklingsarbetet kan utföras i en Python-anteckningsbok. Konfigurationen inkluderar följande åtgärder:

* Installera SDK:n
* Importera Python-paket
* Konfigurera din arbetsyta

### <a name="install-and-import-packages"></a>Installera och importera paket

Om du följer självstudien i din egen Python-miljö använder du följande för att installera nödvändiga paket.

```shell
pip install azureml-sdk[automl,notebooks] matplotlib
```

Importera de Python-paket som du behöver i självstudien:

```python
import azureml.core
import pandas as pd
from azureml.core.workspace import Workspace
import logging
import os
```

### <a name="configure-workspace"></a>Konfigurera arbetsyta

Skapa ett arbetsyteobjekt från den befintliga arbetsytan. En [arbetsytan](https://docs.microsoft.com/en-us/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) är en klass som accepterar dina Azure-prenumeration och resursgrupp information. Den skapar också en molnresurs för att övervaka och spåra dina körningar i modellen.

`Workspace.from_config()` läser filen **config.json** och läser in informationen i ett objekt med namnet `ws`.  `ws` används i resten av koden i den här självstudien.

När du har ett objekt på arbetsytan kan du ange ett namn för experimentet. Skapa och registrera en lokal katalog med arbetsytan. Historiken över alla körningar registreras under det angivna experimentet och i [Azure Portal](https://portal.azure.com).


```python
ws = Workspace.from_config()
# choose a name for the run history container in the workspace
experiment_name = 'automated-ml-regression'
# project folder
project_folder = './automated-ml-regression'

output = {}
output['SDK version'] = azureml.core.VERSION
output['Subscription ID'] = ws.subscription_id
output['Workspace'] = ws.name
output['Resource Group'] = ws.resource_group
output['Location'] = ws.location
output['Project Directory'] = project_folder
pd.set_option('display.max_colwidth', -1)
pd.DataFrame(data=output, index=['']).T
```

## <a name="explore-data"></a>Utforska data

Använd dataflödesobjektet som skapades i föregående självstudie. Sammanfattningsvis rensade del 1 av den här självstudien NYC Taxi-data så att de kunde användas i en maskininlärningsmodell. Nu använder du olika funktioner från datamängden och tillåter en automatiserad modell att skapa relationer mellan funktionerna och priset för en taxiresa. Öppna och kör dataflödet och granska resultatet:


```python
import azureml.dataprep as dprep

file_path = os.path.join(os.getcwd(), "dflows.dprep")

dflow_prepared = dprep.Dataflow.open(file_path)
dflow_prepared.get_profile()
```

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Typ</th>
      <th>Min</th>
      <th>Max</th>
      <th>Antal</th>
      <th>Antal saknas</th>
      <th>Antal saknas inte</th>
      <th>Procent saknas</th>
      <th>Antal fel</th>
      <th>Tomt antal</th>
      <th>0,1 % kvantil</th>
      <th>1 % kvantil</th>
      <th>5 % kvantil</th>
      <th>25 % kvantil</th>
      <th>50 % kvantil</th>
      <th>75 % kvantil</th>
      <th>95 % kvantil</th>
      <th>99 % kvantil</th>
      <th>99,9 % kvantil</th>
      <th>Medelvärde</th>
      <th>Standardavvikelse</th>
      <th>Varians</th>
      <th>Snedhet</th>
      <th>Toppighet</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>vendor</th>
      <td>FieldType.STRING</td>
      <td>1</td>
      <td>VTS</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0,0</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>pickup_weekday</th>
      <td>FieldType.STRING</td>
      <td>Fredag</td>
      <td>Onsdag</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0,0</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>pickup_hour</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>23</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0,0</td>
      <td>0</td>
      <td>2,90047</td>
      <td>2,69355</td>
      <td>9,72889</td>
      <td>16</td>
      <td>19,3713</td>
      <td>22,6974</td>
      <td>23</td>
      <td>23</td>
      <td>14,2731</td>
      <td>6,59242</td>
      <td>43,46</td>
      <td>-0,693723</td>
      <td>-0,570403</td>
    </tr>
    <tr>
      <th>pickup_minute</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>59</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0,0</td>
      <td>0</td>
      <td>4,99701</td>
      <td>4,95833</td>
      <td>14,1528</td>
      <td>29,3832</td>
      <td>44,6825</td>
      <td>56,4444</td>
      <td>58,9909</td>
      <td>59</td>
      <td>29,427</td>
      <td>17,4333</td>
      <td>303,921</td>
      <td>0,0120999</td>
      <td>-1,20981</td>
    </tr>
    <tr>
      <th>pickup_second</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>59</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0,0</td>
      <td>0</td>
      <td>5,28131</td>
      <td>5</td>
      <td>14,7832</td>
      <td>29,9293</td>
      <td>44,725</td>
      <td>56,7573</td>
      <td>59</td>
      <td>59</td>
      <td>29,7443</td>
      <td>17,3595</td>
      <td>301,351</td>
      <td>-0,0252399</td>
      <td>-1,19616</td>
    </tr>
    <tr>
      <th>dropoff_weekday</th>
      <td>FieldType.STRING</td>
      <td>Fredag</td>
      <td>Onsdag</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0,0</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>dropoff_hour</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>23</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0,0</td>
      <td>0</td>
      <td>2,57153</td>
      <td>2</td>
      <td>9,58795</td>
      <td>15,9994</td>
      <td>19,6184</td>
      <td>22,8317</td>
      <td>23</td>
      <td>23</td>
      <td>14,2105</td>
      <td>6,71093</td>
      <td>45,0365</td>
      <td>-0,687292</td>
      <td>-0,61951</td>
    </tr>
    <tr>
      <th>dropoff_minute</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>59</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0,0</td>
      <td>0</td>
      <td>5,44383</td>
      <td>4,84694</td>
      <td>14,1036</td>
      <td>28,8365</td>
      <td>44,3102</td>
      <td>56,6892</td>
      <td>59</td>
      <td>59</td>
      <td>29,2907</td>
      <td>17,4108</td>
      <td>303,136</td>
      <td>0,0222514</td>
      <td>-1,2181</td>
    </tr>
    <tr>
      <th>dropoff_second</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>59</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0,0</td>
      <td>0</td>
      <td>5,07801</td>
      <td>5</td>
      <td>14,5751</td>
      <td>29,5972</td>
      <td>45,4649</td>
      <td>56,2729</td>
      <td>59</td>
      <td>59</td>
      <td>29,772</td>
      <td>17,5337</td>
      <td>307,429</td>
      <td>-0,0212575</td>
      <td>-1,226</td>
    </tr>
    <tr>
      <th>store_forward</th>
      <td>FieldType.STRING</td>
      <td>N</td>
      <td>Y</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0,0</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>pickup_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-74,0781</td>
      <td>-73,7459</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-74,0578</td>
      <td>-73,9639</td>
      <td>-73,9656</td>
      <td>-73,9508</td>
      <td>-73,9255</td>
      <td>-73,8529</td>
      <td>-73,8302</td>
      <td>-73,8238</td>
      <td>-73,7697</td>
      <td>-73,9123</td>
      <td>0,0503757</td>
      <td>0,00253771</td>
      <td>0,352172</td>
      <td>-0,923743</td>
    </tr>
    <tr>
      <th>pickup_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>40,5755</td>
      <td>40,8799</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>40,632</td>
      <td>40,7117</td>
      <td>40,7115</td>
      <td>40,7213</td>
      <td>40,7565</td>
      <td>40,8058</td>
      <td>40,8478</td>
      <td>40,8676</td>
      <td>40,8778</td>
      <td>40,7649</td>
      <td>0,0494674</td>
      <td>0,00244702</td>
      <td>0,205972</td>
      <td>-0,777945</td>
    </tr>
    <tr>
      <th>dropoff_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-74,0857</td>
      <td>-73,7209</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-74,0775</td>
      <td>-73,9875</td>
      <td>-73,9882</td>
      <td>-73,9638</td>
      <td>-73,935</td>
      <td>-73,8755</td>
      <td>-73,8125</td>
      <td>-73,7759</td>
      <td>-73,7327</td>
      <td>-73,9202</td>
      <td>0,0584627</td>
      <td>0,00341789</td>
      <td>0,623622</td>
      <td>-0,262603</td>
    </tr>
    <tr>
      <th>dropoff_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>40,5835</td>
      <td>40,8797</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>40,5973</td>
      <td>40,6928</td>
      <td>40,6911</td>
      <td>40,7226</td>
      <td>40,7567</td>
      <td>40,7918</td>
      <td>40,8495</td>
      <td>40,868</td>
      <td>40,8787</td>
      <td>40,7583</td>
      <td>0,0517399</td>
      <td>0,00267701</td>
      <td>0,0390404</td>
      <td>-0,203525</td>
    </tr>
    <tr>
      <th>passengers</th>
      <td>FieldType.DECIMAL</td>
      <td>1</td>
      <td>6</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0,0</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>5</td>
      <td>5</td>
      <td>6</td>
      <td>6</td>
      <td>2,39249</td>
      <td>1,83197</td>
      <td>3,3561</td>
      <td>0,763144</td>
      <td>-1,23467</td>
    </tr>
    <tr>
      <th>avstånd</th>
      <td>FieldType.DECIMAL</td>
      <td>0,01</td>
      <td>32,34</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0,0108744</td>
      <td>0,743898</td>
      <td>0,738194</td>
      <td>1,243</td>
      <td>2,40168</td>
      <td>4,74478</td>
      <td>10,5136</td>
      <td>14,9011</td>
      <td>21,8035</td>
      <td>3,5447</td>
      <td>3,2943</td>
      <td>10,8524</td>
      <td>1,91556</td>
      <td>4,99898</td>
    </tr>
    <tr>
      <th>cost</th>
      <td>FieldType.DECIMAL</td>
      <td>0,1</td>
      <td>88</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>2,33837</td>
      <td>5,00491</td>
      <td>5</td>
      <td>6,93129</td>
      <td>10,524</td>
      <td>17,4811</td>
      <td>33,2343</td>
      <td>50,0093</td>
      <td>63,1753</td>
      <td>13,6843</td>
      <td>9,66571</td>
      <td>93,426</td>
      <td>1,78518</td>
      <td>4,13972</td>
    </tr>
  </tbody>
</table>

Du förbereder data för experimentet genom att lägga till kolumner i `dflow_x` som funktioner för vårt modellskapande. Du definierar `dflow_y` som vårt förutsägelsevärde, **kostnad**:

```python
dflow_X = dflow_prepared.keep_columns(['pickup_weekday','pickup_hour', 'distance','passengers', 'vendor'])
dflow_y = dflow_prepared.keep_columns('cost')
```

### <a name="split-the-data-into-train-and-test-sets"></a>Dela data till uppsättningar för träning och testning

Nu delar du data till uppsättningar för träning och testning med funktionen `train_test_split` i biblioteket `sklearn`. Den här funktionen segregerar data till x, **funktioner**, datauppsättning för modellträning och y, **värden att förutsäga**, datauppsättning för testning. Parametern `test_size` anger procentandelen av data som ska allokeras till testning. Parametern `random_state` anger ett startvärde för slumpgeneratorn, så att delningarna mellan träning och testning alltid är deterministiska:

```python
from sklearn.model_selection import train_test_split

x_df = dflow_X.to_pandas_dataframe()
y_df = dflow_y.to_pandas_dataframe()

x_train, x_test, y_train, y_test = train_test_split(x_df, y_df, test_size=0.2, random_state=223)
# flatten y_train to 1d array
y_train.values.flatten()
```

Syftet med det här steget är att ha datapunkter för att testa den färdiga modell som inte har använts för att träna modellen, detta för att mäta den äkta noggrannheten. Med andra ord bör en korrekt tränad modell kunna göra noggranna förutsägelser från data som den inte redan har sett. Nu har du nödvändiga paket och data för att kunna träna din modell automatiskt.

## <a name="automatically-train-a-model"></a>Träna en modell automatiskt

För att träna en modell automatiskt gör du följande:
1. Definiera inställningar för körningen av experimentet. Koppla dina träningsdata till konfigurationen och ändra de inställningar som styr träningsprocessen.
1. Skicka experimentet för modelljustering. När experimentet har skickats itererar processen genom olika maskininlärningsalgoritmer och inställningar för hyperparametrar enligt dina definierade begränsningar. Den väljer den modell som passar bäst genom att optimera ett noggrannhetsmått.

### <a name="define-settings-for-autogeneration-and-tuning"></a>Definiera inställningar för automatisk generering och justering

Definiera experimentparametern och modellinställningarna för automatisk generering och justering. Visa hela listan med [inställningar](how-to-configure-auto-train.md). Att skicka experimentet med de här standardinställningarna tar cirka 10–15 minuter, men om du vill ha en kortare körtid kan du minska antingen `iterations` eller `iteration_timeout_minutes`.


|Egenskap | Värde i den här självstudien |Beskrivning|
|----|----|---|
|**iteration_timeout_minutes**|10|Tidsgräns i minuter för varje iteration. Minska det här värdet om du vill minska den totala körningstiden.|
|**iterationer**|30|Antal iterationer. I varje iteration tränas en ny maskininlärningsmodell med dina data. Det här är det primära värde som påverkar den totala körningstiden.|
|**primary_metric**| spearman_correlation | Mått som du vill optimera. Den modell som passar bäst väljs utifrån det här måttet.|
|**preprocess**| True | När **True** (Sant) används kan experimentet förbearbeta indata (hantering av saknade data, konvertering av text till numeriskt osv.)|
|**verbosity**| logging.INFO | Styr loggningsnivån.|
|**n_cross_validations**|5|Det antal delningar av korsvalidering som ska utföras när verifieringsdata inte har angetts.|



```python
automl_settings = {
    "iteration_timeout_minutes" : 10,
    "iterations" : 30,
    "primary_metric" : 'spearman_correlation',
    "preprocess" : True,
    "verbosity" : logging.INFO,
    "n_cross_validations": 5
}
```

Använd dina definierade träningsinställningar som parameter till ett `AutoMLConfig` objekt. Ange även dina träningsdata och modelltypen, som i det här fallet är `regression`.

```python
from azureml.train.automl import AutoMLConfig

# local compute
automated_ml_config = AutoMLConfig(task = 'regression',
                             debug_log = 'automated_ml_errors.log',
                             path = project_folder,
                             X = x_train.values,
                             y = y_train.values.flatten(),
                             **automl_settings)
```

### <a name="train-the-automatic-regression-model"></a>Träna den automatiska regressionsmodellen

Starta experimentet för körning lokalt. Skicka det definierade `automated_ml_config`-objektet till experimentet. Ställ in utdatan till `True` för att visa förloppet under experimentet:


```python
from azureml.core.experiment import Experiment
experiment=Experiment(ws, experiment_name)
local_run = experiment.submit(automated_ml_config, show_output=True)
```

De utdata som visas uppdateras direkt allt eftersom experimentet körs. För varje iteration ser du modelltypen, körningens varaktighet samt träningens noggrannhet. Fältet `BEST` spårar den bästa löpande körningspoängen utifrån din måttyp.

    Parent Run ID: AutoML_02778de3-3696-46e9-a71b-521c8fca0651
    *******************************************************************************************
    ITERATION: The iteration being evaluated.
    PIPELINE: A summary description of the pipeline being evaluated.
    DURATION: Time taken for the current iteration.
    METRIC: The result of computing score on the fitted pipeline.
    BEST: The best observed score thus far.
    *******************************************************************************************

     ITERATION   PIPELINE                                       DURATION      METRIC      BEST
             0   MaxAbsScaler ExtremeRandomTrees                0:00:08       0.9447    0.9447
             1   StandardScalerWrapper GradientBoosting         0:00:09       0.9536    0.9536
             2   StandardScalerWrapper ExtremeRandomTrees       0:00:09       0.8580    0.9536
             3   StandardScalerWrapper RandomForest             0:00:08       0.9147    0.9536
             4   StandardScalerWrapper ExtremeRandomTrees       0:00:45       0.9398    0.9536
             5   MaxAbsScaler LightGBM                          0:00:08       0.9562    0.9562
             6   StandardScalerWrapper ExtremeRandomTrees       0:00:27       0.8282    0.9562
             7   StandardScalerWrapper LightGBM                 0:00:07       0.9421    0.9562
             8   MaxAbsScaler DecisionTree                      0:00:08       0.9526    0.9562
             9   MaxAbsScaler RandomForest                      0:00:09       0.9355    0.9562
            10   MaxAbsScaler SGD                               0:00:09       0.9602    0.9602
            11   MaxAbsScaler LightGBM                          0:00:09       0.9553    0.9602
            12   MaxAbsScaler DecisionTree                      0:00:07       0.9484    0.9602
            13   MaxAbsScaler LightGBM                          0:00:08       0.9540    0.9602
            14   MaxAbsScaler RandomForest                      0:00:10       0.9365    0.9602
            15   MaxAbsScaler SGD                               0:00:09       0.9602    0.9602
            16   StandardScalerWrapper ExtremeRandomTrees       0:00:49       0.9171    0.9602
            17   SparseNormalizer LightGBM                      0:00:08       0.9191    0.9602
            18   MaxAbsScaler DecisionTree                      0:00:08       0.9402    0.9602
            19   StandardScalerWrapper ElasticNet               0:00:08       0.9603    0.9603
            20   MaxAbsScaler DecisionTree                      0:00:08       0.9513    0.9603
            21   MaxAbsScaler SGD                               0:00:08       0.9603    0.9603
            22   MaxAbsScaler SGD                               0:00:10       0.9602    0.9603
            23   StandardScalerWrapper ElasticNet               0:00:09       0.9603    0.9603
            24   StandardScalerWrapper ElasticNet               0:00:09       0.9603    0.9603
            25   MaxAbsScaler SGD                               0:00:09       0.9603    0.9603
            26   TruncatedSVDWrapper ElasticNet                 0:00:09       0.9602    0.9603
            27   MaxAbsScaler SGD                               0:00:12       0.9413    0.9603
            28   StandardScalerWrapper ElasticNet               0:00:07       0.9603    0.9603
            29    Ensemble                                      0:00:38       0.9622    0.9622

## <a name="explore-the-results"></a>Utforska resultaten

Utforska resultatet av automatisk träning med en Jupyter-widget eller genom att läsa experimenthistoriken.

### <a name="option-1-add-a-jupyter-widget-to-see-results"></a>Alternativ 1: Lägga till en Jupyter-widget för att se resultat

Om du använder en Jupyter-anteckningsbok kan du använda den här Jupyter Notebook-widgeten för att se ett diagram och en tabell med alla resultat:


```python
from azureml.widgets import RunDetails
RunDetails(local_run).show()
```

![Jupyter Widget-körningsinformation](./media/tutorial-auto-train-models/automl-dash-output.png)
![Jupyter Widget-diagram](./media/tutorial-auto-train-models/automl-chart-output.png)

### <a name="option-2-get-and-examine-all-run-iterations-in-python"></a>Alternativ 2: Hämta och granska alla körningsiterationer i Python

Du kan även hämta historiken för varje experiment och utforska enskilda mått för varje iterationskörning. Genom att undersöka RMSE (root_mean_squared_error) för varje enskild modellkörning ser du att de flesta iterationer förutsäger kostnaden för taxifärden med en rimlig marginal (3–4 dollar).

```python
children = list(local_run.get_children())
metricslist = {}
for run in children:
    properties = run.get_properties()
    metrics = {k: v for k, v in run.get_metrics().items() if isinstance(v, float)}
    metricslist[int(properties['iteration'])] = metrics

rundata = pd.DataFrame(metricslist).sort_index(1)
rundata
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
      <th>5</th>
      <th>6</th>
      <th>7</th>
      <th>8</th>
      <th>9</th>
      <th>...</th>
      <th>20</th>
      <th>21</th>
      <th>22</th>
      <th>23</th>
      <th>24</th>
      <th>25</th>
      <th>26</th>
      <th>27</th>
      <th>28</th>
      <th>29</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>explained_variance</th>
      <td>0,811037</td>
      <td>0,880553</td>
      <td>0,398582</td>
      <td>0,776040</td>
      <td>0,663869</td>
      <td>0,875911</td>
      <td>0,115632</td>
      <td>0,586905</td>
      <td>0,851911</td>
      <td>0,793964</td>
      <td>...</td>
      <td>0,850023</td>
      <td>0,883603</td>
      <td>0,883704</td>
      <td>0,880797</td>
      <td>0,881564</td>
      <td>0,883708</td>
      <td>0,881826</td>
      <td>0,585377</td>
      <td>0,883123</td>
      <td>0,886817</td>
    </tr>
    <tr>
      <th>mean_absolute_error</th>
      <td>2,189444</td>
      <td>1,500412</td>
      <td>5,480531</td>
      <td>2,626316</td>
      <td>2,973026</td>
      <td>1,550199</td>
      <td>6,383868</td>
      <td>4,414241</td>
      <td>1,743328</td>
      <td>2,294601</td>
      <td>...</td>
      <td>1,797402</td>
      <td>1,415815</td>
      <td>1,418167</td>
      <td>1,578617</td>
      <td>1,559427</td>
      <td>1,413042</td>
      <td>1,551698</td>
      <td>4,069196</td>
      <td>1,505795</td>
      <td>1,430957</td>
    </tr>
    <tr>
      <th>median_absolute_error</th>
      <td>1,438417</td>
      <td>0,850899</td>
      <td>4,579662</td>
      <td>1,765210</td>
      <td>1,594600</td>
      <td>0,869883</td>
      <td>4,266450</td>
      <td>3,627355</td>
      <td>0,954992</td>
      <td>1,361014</td>
      <td>...</td>
      <td>0,973634</td>
      <td>0,774814</td>
      <td>0,797269</td>
      <td>1,147234</td>
      <td>1,116424</td>
      <td>0,783958</td>
      <td>1,098464</td>
      <td>2,709027</td>
      <td>1,003728</td>
      <td>0,851724</td>
    </tr>
    <tr>
      <th>normalized_mean_absolute_error</th>
      <td>0,024908</td>
      <td>0,017070</td>
      <td>0,062350</td>
      <td>0,029878</td>
      <td>0,033823</td>
      <td>0,017636</td>
      <td>0,072626</td>
      <td>0,050219</td>
      <td>0,019833</td>
      <td>0,026105</td>
      <td>...</td>
      <td>0,020448</td>
      <td>0,016107</td>
      <td>0,016134</td>
      <td>0,017959</td>
      <td>0,017741</td>
      <td>0,016076</td>
      <td>0,017653</td>
      <td>0,046293</td>
      <td>0,017131</td>
      <td>0,016279</td>
    </tr>
    <tr>
      <th>normalized_median_absolute_error</th>
      <td>0,016364</td>
      <td>0,009680</td>
      <td>0,052101</td>
      <td>0,020082</td>
      <td>0,018141</td>
      <td>0,009896</td>
      <td>0,048538</td>
      <td>0,041267</td>
      <td>0,010865</td>
      <td>0,015484</td>
      <td>...</td>
      <td>0,011077</td>
      <td>0,008815</td>
      <td>0,009070</td>
      <td>0,013052</td>
      <td>0,012701</td>
      <td>0,008919</td>
      <td>0,012497</td>
      <td>0,030819</td>
      <td>0,011419</td>
      <td>0,009690</td>
    </tr>
    <tr>
      <th>normalized_root_mean_squared_error</th>
      <td>0,047968</td>
      <td>0,037882</td>
      <td>0,085572</td>
      <td>0,052282</td>
      <td>0,065809</td>
      <td>0,038664</td>
      <td>0,109401</td>
      <td>0,071104</td>
      <td>0,042294</td>
      <td>0,049967</td>
      <td>...</td>
      <td>0,042565</td>
      <td>0,037685</td>
      <td>0,037557</td>
      <td>0,037643</td>
      <td>0,037513</td>
      <td>0,037560</td>
      <td>0,037465</td>
      <td>0,072077</td>
      <td>0,037249</td>
      <td>0,036716</td>
    </tr>
    <tr>
      <th>normalized_root_mean_squared_log_error</th>
      <td>0,055353</td>
      <td>0,045000</td>
      <td>0,110219</td>
      <td>0,065633</td>
      <td>0,063589</td>
      <td>0,044412</td>
      <td>0,123433</td>
      <td>0,092312</td>
      <td>0,046130</td>
      <td>0,055243</td>
      <td>...</td>
      <td>0,046540</td>
      <td>0,041804</td>
      <td>0,041771</td>
      <td>0,045175</td>
      <td>0,044628</td>
      <td>0,041617</td>
      <td>0,044405</td>
      <td>0,079651</td>
      <td>0,042799</td>
      <td>0,041530</td>
    </tr>
    <tr>
      <th>r2_score</th>
      <td>0,810900</td>
      <td>0,880328</td>
      <td>0,398076</td>
      <td>0,775957</td>
      <td>0,642812</td>
      <td>0,875719</td>
      <td>0,021603</td>
      <td>0,586514</td>
      <td>0,851767</td>
      <td>0,793671</td>
      <td>...</td>
      <td>0,849809</td>
      <td>0,880142</td>
      <td>0,880952</td>
      <td>0,880586</td>
      <td>0,881347</td>
      <td>0,880887</td>
      <td>0,881613</td>
      <td>0,548121</td>
      <td>0,882883</td>
      <td>0,886321</td>
    </tr>
    <tr>
      <th>root_mean_squared_error</th>
      <td>4,216362</td>
      <td>3,329810</td>
      <td>7,521765</td>
      <td>4,595604</td>
      <td>5,784601</td>
      <td>3,398540</td>
      <td>9,616354</td>
      <td>6,250011</td>
      <td>3,717661</td>
      <td>4,392072</td>
      <td>...</td>
      <td>3,741447</td>
      <td>3,312533</td>
      <td>3,301242</td>
      <td>3,308795</td>
      <td>3,297389</td>
      <td>3,301485</td>
      <td>3,293182</td>
      <td>6,335581</td>
      <td>3,274209</td>
      <td>3,227365</td>
    </tr>
    <tr>
      <th>root_mean_squared_log_error</th>
      <td>0,243184</td>
      <td>0,197702</td>
      <td>0,484227</td>
      <td>0,288349</td>
      <td>0,279367</td>
      <td>0,195116</td>
      <td>0,542281</td>
      <td>0,405559</td>
      <td>0,202666</td>
      <td>0,242702</td>
      <td>...</td>
      <td>0,204464</td>
      <td>0,183658</td>
      <td>0,183514</td>
      <td>0,198468</td>
      <td>0,196067</td>
      <td>0,182836</td>
      <td>0,195087</td>
      <td>0,349935</td>
      <td>0,188031</td>
      <td>0,182455</td>
    </tr>
    <tr>
      <th>spearman_correlation</th>
      <td>0,944743</td>
      <td>0,953618</td>
      <td>0,857965</td>
      <td>0,914703</td>
      <td>0,939846</td>
      <td>0,956159</td>
      <td>0,828187</td>
      <td>0,942069</td>
      <td>0,952581</td>
      <td>0,935477</td>
      <td>...</td>
      <td>0,951287</td>
      <td>0,960335</td>
      <td>0,960195</td>
      <td>0,960279</td>
      <td>0,960288</td>
      <td>0,960323</td>
      <td>0,960161</td>
      <td>0,941254</td>
      <td>0,960293</td>
      <td>0,962158</td>
    </tr>
    <tr>
      <th>spearman_correlation_max</th>
      <td>0,944743</td>
      <td>0,953618</td>
      <td>0,953618</td>
      <td>0,953618</td>
      <td>0,953618</td>
      <td>0,956159</td>
      <td>0,956159</td>
      <td>0,956159</td>
      <td>0,956159</td>
      <td>0,956159</td>
      <td>...</td>
      <td>0,960303</td>
      <td>0,960335</td>
      <td>0,960335</td>
      <td>0,960335</td>
      <td>0,960335</td>
      <td>0,960335</td>
      <td>0,960335</td>
      <td>0,960335</td>
      <td>0,960335</td>
      <td>0,962158</td>
    </tr>
  </tbody>
</table>
<p>12 rader × 30 kolumner</p>
</div>

## <a name="retrieve-the-best-model"></a>Hämta den bästa modellen

Välj bästa pipeline från våra iterationer. Metoden `get_output` på `automl_classifier` returnerar den bästa körningen och den anpassade modellen för det senaste anpassningsanropet. Genom att använda överlagringar på `get_output` kan du hämta den bästa körningen och anpassade modellen för valfritt loggat mått eller en viss iteration:

```python
best_run, fitted_model = local_run.get_output()
print(best_run)
print(fitted_model)
```

## <a name="test-the-best-model-accuracy"></a>Testa den bästa modellens precision

Använd den bästa modellen till att köra förutsägelser på testdatamängden för att förutsäga taxipriser. Funktionen `predict` använder den bästa modellen och förutsäger värdena för y, **resekostnad**, från `x_test`-datauppsättningen. Skriv ut de 10 första förutsagda kostnadsvärdena från `y_predict`:

```python
y_predict = fitted_model.predict(x_test.values)
print(y_predict[:10])
```

Skapa ett spridningsdiagram för att visualisera värden för beräknade kostnader jämfört med värden för faktiska kostnader. I följande kod används funktionen `distance` som x-axel och `cost` för resa som y-axel. I syfte att jämföra variansen för beräknad kostnad vid varje värde för reseavstånd skapas de först 100 beräknade och faktiska värdena för kostnad som separata serier. En undersökning av diagrammet visar att relationen mellan avstånd/kostnad nästan är linjär, och värdena för beräknad kostnad är i de flesta fall nära värdena för faktisk kostnad för samma reseavstånd.

```python
import matplotlib.pyplot as plt

fig = plt.figure(figsize=(14, 10))
ax1 = fig.add_subplot(111)

distance_vals = [x[4] for x in x_test.values]
y_actual = y_test.values.flatten().tolist()

ax1.scatter(distance_vals[:100], y_predict[:100], s=18, c='b', marker="s", label='Predicted')
ax1.scatter(distance_vals[:100], y_actual[:100], s=18, c='r', marker="o", label='Actual')

ax1.set_xlabel('distance (mi)')
ax1.set_title('Predicted and Actual Cost/Distance')
ax1.set_ylabel('Cost ($)')

plt.legend(loc='upper left', prop={'size': 12})
plt.rcParams.update({'font.size': 14})
plt.show()
```

![Spridningsdiagram för förutsägelse](./media/tutorial-auto-train-models/automl-scatter-plot.png)

Beräkna `root mean squared error` för resultatet. Använd dataramen `y_test`. Konvertera den till en lista som ska jämföras med de förutsagda värdena. Funktionen `mean_squared_error` tar emot två matriser med värden och beräknar det genomsnittliga kvadratfelet mellan dem. Att ta kvadratroten ur resultatet ger ett fel i samma enheter som y-variabeln, **kostnad**. Det visar ungefär hur långt förutsägelserna om taxipriser kommer från de faktiska priserna:

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(y_actual, y_predict))
rmse
```

    3.2204936862688798

Kör följande kod för att beräkna medelabsolutfel i procent (MAPE) med hjälp av de fullständiga datauppsättningarna `y_actual` och `y_predict`. Det här måttet beräknar en absolut skillnad mellan varje förväntat och faktiskt värde och summerar alla skillnaderna. Det visar sedan denna summa som en procentandel av summan av de faktiska värdena:

```python
sum_actuals = sum_errors = 0

for actual_val, predict_val in zip(y_actual, y_predict):
    abs_error = actual_val - predict_val
    if abs_error < 0:
        abs_error = abs_error * -1

    sum_errors = sum_errors + abs_error
    sum_actuals = sum_actuals + actual_val

mean_abs_percent_error = sum_errors / sum_actuals
print("Model MAPE:")
print(mean_abs_percent_error)
print()
print("Model Accuracy:")
print(1 - mean_abs_percent_error)
```

    Model MAPE:
    0.10545153869569586

    Model Accuracy:
    0.8945484613043041

Utifrån de sista måtten för förutsägelsens noggrannhet ser du att modellen är ganska bra på att förutsäga taxipriser baserat på datamängdens egenskaper, vanligtvis inom +- 3,00 dollar. Utvecklingsprocessen för maskininlärningsmodeller är mycket resurskrävande och fordrar betydande domänkunskap och tid för att köra och jämföra resultat från flera olika modeller. Användning av automatisk maskininlärning är ett bra sätt att snabbt testa flera olika modeller för ett scenario.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

I den här automatiserade självstudiekursen om maskininlärning har du gjort följande uppgifter:

> [!div class="checklist"]
> * Konfigurerat en arbetsyta och förberett data för ett experiment.
> * Tränat med hjälp av en automatiserad regressionsmodell lokalt med anpassade parametrar.
> * Utforskat och granskat träningsresultat.

[Distribuera modellen](tutorial-deploy-models-with-aml.md) med Azure Machine Learning.
