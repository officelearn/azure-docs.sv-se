---
title: 'Självstudie: träna din första Machine Learning-modell – python'
titleSuffix: Azure Machine Learning
description: Del 3 i Azure Machine Learning kom igång-serien visar hur du tränar en Machine Learning-modell.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 09/15/2020
ms.custom: devx-track-python
ms.openlocfilehash: f3ba5751e7a0c2369d505535896bbb4ff7523c02
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93314569"
---
# <a name="tutorial-train-your-first-machine-learning-model-part-3-of-4"></a>Självstudie: träna din första Machine Learning-modell (del 3 av 4)

Den här självstudien visar hur du tränar en maskin inlärnings modell i Azure Machine Learning.

Den här självstudien är *del tre i en själv studie serie i fyra delar* där du får lära dig grunderna i Azure Machine Learning och fullständiga jobbbaserade Machine Learning-uppgifter i Azure. Den här självstudien bygger på det arbete som du avslutade i [del 1: konfiguration](tutorial-1st-experiment-sdk-setup-local.md) och [del 2: kör "Hello World!"](tutorial-1st-experiment-hello-world.md) i serien.

I den här självstudien tar du nästa steg genom att skicka ett skript som tågen en maskin inlärnings modell. I det här exemplet får du hjälp att förstå hur Azure Machine Learning fören klar konsekvent beteende mellan lokal fel sökning och fjärrkörning.

I den här kursen får du:

> [!div class="checklist"]
> * Skapa ett träningsskript.
> * Använd Conda för att definiera en Azure Machine Learnings miljö.
> * Skapa ett kontroll skript.
> * Förstå Azure Machine Learning klasser ( `Environment` , `Run` , `Metrics` ).
> * Skicka in och kör ditt utbildnings skript.
> * Visa kodens utdata i molnet.
> * Logga mått till Azure Machine Learning.
> * Visa dina mått i molnet.

## <a name="prerequisites"></a>Förutsättningar

* Slut för ande av [del 2](tutorial-1st-experiment-hello-world.md) av serien.
* Introduktions kunskap om python-språket och Machine Learning-arbetsflöden.
* Lokal utvecklings miljö, till exempel Visual Studio Code, Jupyter eller pycharm med.
* Python (version 3,5 till 3,7).

## <a name="create-training-scripts"></a>Skapa utbildnings skript

Först definierar du nätverks arkitekturen neurala i en `model.py` fil. All din utbildnings kod kommer att ingå i under `src` katalogen, inklusive `model.py` .

Följande kod hämtas från [det här introduktions exemplet](https://pytorch.org/tutorials/beginner/blitz/cifar10_tutorial.html) från PyTorch. Observera att Azure Machine Learning begreppen gäller för alla Machine Learning-koder, inte bara PyTorch.

```python
# tutorial/src/model.py
import torch.nn as nn
import torch.nn.functional as F


class Net(nn.Module):
    def __init__(self):
        super(Net, self).__init__()
        self.conv1 = nn.Conv2d(3, 6, 5)
        self.pool = nn.MaxPool2d(2, 2)
        self.conv2 = nn.Conv2d(6, 16, 5)
        self.fc1 = nn.Linear(16 * 5 * 5, 120)
        self.fc2 = nn.Linear(120, 84)
        self.fc3 = nn.Linear(84, 10)

    def forward(self, x):
        x = self.pool(F.relu(self.conv1(x)))
        x = self.pool(F.relu(self.conv2(x)))
        x = x.view(-1, 16 * 5 * 5)
        x = F.relu(self.fc1(x))
        x = F.relu(self.fc2(x))
        x = self.fc3(x)
        return x
```

Sedan definierar du övnings skriptet. Det här skriptet laddar ned CIFAR10-datauppsättningen med hjälp av PyTorch `torchvision.dataset` -API: er, konfigurerar nätverket som definierats i `model.py` och tågen det för två epoker genom att använda standard SGD och kors-entropi.

Skapa ett `train.py` skript i under `src` katalogen:

```python
# tutorial/src/train.py
import torch
import torch.optim as optim
import torchvision
import torchvision.transforms as transforms

from model import Net

# download CIFAR10 data
trainset = torchvision.datasets.CIFAR10(
    root="./data",
    train=True,
    download=True,
    transform=torchvision.transforms.ToTensor(),
)
trainloader = torch.utils.data.DataLoader(
    trainset, batch_size=4, shuffle=True
)

if __name__ == "__main__":

    # define convolutional network
    net = Net()

    # set up pytorch loss /  optimizer
    criterion = torch.nn.CrossEntropyLoss()
    optimizer = optim.SGD(net.parameters(), lr=0.001, momentum=0.9)

    # train the network
    for epoch in range(2):

        running_loss = 0.0
        for i, data in enumerate(trainloader, 0):
            # unpack the data
            inputs, labels = data

            # zero the parameter gradients
            optimizer.zero_grad()

            # forward + backward + optimize
            outputs = net(inputs)
            loss = criterion(outputs, labels)
            loss.backward()
            optimizer.step()

            # print statistics
            running_loss += loss.item()
            if i % 2000 == 1999:
                loss = running_loss / 2000
                print(f"epoch={epoch + 1}, batch={i + 1:5}: loss {loss:.2f}")
                running_loss = 0.0

    print("Finished Training")

```

Nu har du följande katalog struktur:

```txt
tutorial
└──.azureml
|  └──config.json
└──src
|  └──hello.py
|  └──model.py
|  └──train.py
└──01-create-workspace.py
└──02-create-compute.py
└──03-run-hello.py
```

## <a name="create-a-python-environment"></a>Skapa en python-miljö

I demonstrations syfte kommer vi att använda en Conda-miljö. (Stegen för en virtuell pip-miljö är nästan identiska.)

Skapa en fil `pytorch-env.yml` som heter i den `.azureml` dolda katalogen:

```yml
# tutorial/.azureml/pytorch-env.yml
name: pytorch-env
channels:
    - defaults
    - pytorch
dependencies:
    - python=3.6.2
    - pytorch
    - torchvision
```

Den här miljön har alla beroenden som ditt modell-och utbildnings skript kräver. Observera att det inte finns något beroende på Azure Machine Learning SDK för python.

## <a name="test-locally"></a>Testa lokalt

Använd följande kod för att testa att skriptet körs lokalt i den här miljön:

```bash
conda env create -f .azureml/pytorch-env.yml    # create conda environment
conda activate pytorch-env                      # activate conda environment
python src/train.py                             # train model
```

När du har kört skriptet ser du de data som hämtats till en katalog med namnet `tutorial/data` .

## <a name="create-the-control-script"></a>Skapa kontroll skriptet

Skillnaden mellan följande kontroll skript och det som du använde för att skicka "Hello World!" är att du lägger till ett par extra rader för att ställa in miljön.

Skapa en ny python-fil i `tutorial` katalogen `04-run-pytorch.py` :

```python
# tutorial/04-run-pytorch.py
from azureml.core import Workspace
from azureml.core import Experiment
from azureml.core import Environment
from azureml.core import ScriptRunConfig

if __name__ == "__main__":
    ws = Workspace.from_config()
    experiment = Experiment(workspace=ws, name='day1-experiment-train')
    config = ScriptRunConfig(source_directory='src', script='train.py', compute_target='cpu-cluster')

    # set up pytorch environment
    env = Environment.from_conda_specification(name='pytorch-env', file_path='.azureml/pytorch-env.yml')
    config.run_config.environment = env

    run = experiment.submit(config)

    aml_url = run.get_portal_url()
    print(aml_url)
```

### <a name="understand-the-code-changes"></a>Förstå kod ändringarna

:::row:::
   :::column span="":::
      `env = ...`
   :::column-end:::
   :::column span="2":::
      Azure Machine Learning ger en [miljö](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py) som kan representera en reproducerbar, version av python-miljö för att köra experiment. Det är enkelt att skapa en miljö från en lokal Conda eller pip-miljö.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config.run_config.environment = env`
   :::column-end:::
   :::column span="2":::
      Lägger till miljön i [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py).
   :::column-end:::
:::row-end:::

## <a name="submit-the-run-to-azure-machine-learning"></a>Skicka in körningen till Azure Machine Learning

Om du har växlat lokala miljöer ska du se till att växla tillbaka till en miljö där Azure Machine Learning SDK för python har installerats. 

Kör sedan:

```bash
python 04-run-pytorch.py
```

>[!NOTE] 
> Första gången du kör det här skriptet kommer Azure Machine Learning att skapa en ny Docker-avbildning från din PyTorch-miljö. Hela körningen kan ta 5 till 10 minuter att slutföra. 
>
> Du kan se Docker-build-loggarna i Azure Machine Learning Studio. Följ länken till Studio, Välj fliken **utdata + loggar** och välj sedan `20_image_build_log.txt` .
>
> Den här avbildningen kommer att återanvändas i framtida körningar så att de körs snabbare.

När avbildningen har skapats väljer `70_driver_log.txt` du för att visa resultatet av ditt utbildnings skript.

```txt
Downloading https://www.cs.toronto.edu/~kriz/cifar-10-python.tar.gz to ./data/cifar-10-python.tar.gz
...
Files already downloaded and verified
epoch=1, batch= 2000: loss 2.19
epoch=1, batch= 4000: loss 1.82
epoch=1, batch= 6000: loss 1.66
epoch=1, batch= 8000: loss 1.58
epoch=1, batch=10000: loss 1.52
epoch=1, batch=12000: loss 1.47
epoch=2, batch= 2000: loss 1.39
epoch=2, batch= 4000: loss 1.38
epoch=2, batch= 6000: loss 1.37
epoch=2, batch= 8000: loss 1.33
epoch=2, batch=10000: loss 1.31
epoch=2, batch=12000: loss 1.27
Finished Training
```

> [!WARNING]
> Om du ser ett fel finns `Your total snapshot size exceeds the limit` `data` katalogen i det `source_directory` värde som används i `ScriptRunConfig` .
>
> Flytta `data` utanför `src` .

Miljöer kan registreras på en arbets yta med `env.register(ws)` . De kan sedan enkelt delas, återanvändas och versions hantering. Miljöer gör det enkelt att återskapa tidigare resultat och att samar beta med ditt team.

Azure Machine Learning också ha en samling av granskade miljöer. Dessa miljöer behandlar vanliga scenarier för maskin inlärning och backas upp av cachelagrade Docker-avbildningar. Cachelagrade Docker-avbildningar gör den första fjärrkörningen snabbare.

I korthet kan du spara tid med registrerade miljöer! Läs om [hur du använder miljöer](./how-to-use-environments.md) för mer information.

## <a name="log-training-metrics"></a>Logga utbildnings mått

Nu när du har en modell utbildning i Azure Machine Learning börjar du spåra vissa prestanda mått.

Det aktuella tränings skriptet skriver ut mått till terminalen. Azure Machine Learning tillhandahåller en mekanism för att logga mått med fler funktioner. Genom att lägga till några rader med kod får du möjlighet att visualisera mått i Studio och jämföra mått mellan flera körningar.

### <a name="modify-trainpy-to-include-logging"></a>Ändra `train.py` för att inkludera loggning

Ändra `train.py` skriptet så att det innehåller två rader med kod:

```python
# train.py
import torch
import torch.optim as optim
import torchvision
import torchvision.transforms as transforms

from model import Net
from azureml.core import Run


# ADDITIONAL CODE: get Azure Machine Learning run from the current context
run = Run.get_context()

# download CIFAR10 data
trainset = torchvision.datasets.CIFAR10(
    root="./data",
    train=True,
    download=True,
    transform=torchvision.transforms.ToTensor(),
)
trainloader = torch.utils.data.DataLoader(
    trainset, batch_size=4, shuffle=True, num_workers=2
)

if __name__ == "__main__":

    # define convolutional network
    net = Net()

    # set up pytorch loss /  optimizer
    criterion = torch.nn.CrossEntropyLoss()
    optimizer = optim.SGD(net.parameters(), lr=0.001, momentum=0.9)

    # train the network
    for epoch in range(2):

        running_loss = 0.0
        for i, data in enumerate(trainloader, 0):
            # unpack the data
            inputs, labels = data

            # zero the parameter gradients
            optimizer.zero_grad()

            # forward + backward + optimize
            outputs = net(inputs)
            loss = criterion(outputs, labels)
            loss.backward()
            optimizer.step()

            # print statistics
            running_loss += loss.item()
            if i % 2000 == 1999:
                loss = running_loss / 2000
                run.log('loss', loss) # ADDITIONAL CODE: log loss metric to Azure Machine Learning
                print(f'epoch={epoch + 1}, batch={i + 1:5}: loss {loss:.2f}')
                running_loss = 0.0

    print('Finished Training')
```

#### <a name="understand-the-additional-two-lines-of-code"></a>Förstå ytterligare två rader med kod

I `train.py` får du åtkomst till kör-objektet _i_ själva övnings skriptet med hjälp av `Run.get_context()` metoden och använder det för att logga mått:

```python
# in train.py
run = Run.get_context()

...

run.log('loss', loss)
```

Mått i Azure Machine Learning:

- Organiseras genom att experimentera och köra, så det är enkelt att hålla koll på och jämföra mått.
- Utrustad med ett användar gränssnitt så att du kan visualisera inlärnings prestanda i Studio.
- Utformad för skalning, så du behåller dessa förmåner även om du kör hundratals experiment.

### <a name="update-the-conda-environment-file"></a>Uppdatera miljö filen för Conda

`train.py`Skriptet tog bara ett nytt beroende av `azureml.core` . Uppdatera `pytorch-env.yml` för att avspegla den här ändringen:

```yaml
# tutorial/.azureml/pytorch-env.yml
name: pytorch-env
channels:
    - defaults
    - pytorch
dependencies:
    - python=3.6.2
    - pytorch
    - torchvision
    - pip
    - pip:
        - azureml-sdk
```

### <a name="submit-the-run-to-azure-machine-learning"></a>Skicka in körningen till Azure Machine Learning
Skicka skriptet en gång till:

```bash
python 04-run-pytorch.py
```

När du besöker Studio går du till fliken **mått** där du nu kan se live-uppdateringar om modell inlärnings förlusten!

:::image type="content" source="media/tutorial-1st-experiment-sdk-train/logging-metrics.png" alt-text="Diagram över kurs förlust på fliken mått.":::

## <a name="next-steps"></a>Nästa steg

I den här sessionen har du uppgraderat från en grundläggande "Hello World!" skript till ett mer realistiskt utbildnings skript som kräver att en speciell python-miljö körs. Du såg hur du tar en lokal Conda-miljö till molnet med Azure Machine Learning miljöer. Slutligen såg du hur du på några rader kod kan logga mått till Azure Machine Learning.

Det finns andra sätt att skapa Azure Machine Learning miljöer, inklusive [från en pip requirements.txt](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py#from-pip-requirements-name--file-path-) -fil eller [från en befintlig lokal Conda-miljö](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py#from-existing-conda-environment-name--conda-environment-name-).

I nästa session får du se hur du arbetar med data i Azure Machine Learning genom att ladda upp data uppsättningen CIFAR10 till Azure.

> [!div class="nextstepaction"]
> [Självstudie: ta med dina egna data](tutorial-1st-experiment-bring-data.md)

>[!NOTE] 
> Kom ihåg att [rensa dina resurser](tutorial-1st-experiment-bring-data.md#clean-up-resources)om du vill slutföra själv studie serien här och inte förloppet till nästa steg.