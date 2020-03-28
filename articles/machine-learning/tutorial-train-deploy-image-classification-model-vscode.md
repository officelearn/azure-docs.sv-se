---
title: 'Självstudiekurs: Träna och distribuera en modell med tillägget Visual Studio-kod'
titleSuffix: Azure Machine Learning
description: Lär dig hur du tränar och distribuerar en avbildningsklassificeringsmodell med TensorFlow och Azure Machine Learning Visual Studio Code Extension
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: luisquintanilla
ms.author: luquinta
ms.date: 02/24/2020
ms.openlocfilehash: ba9cd2e7dc0248aa351cb7bc4519689763f1adda
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239884"
---
# <a name="train-and-deploy-an-image-classification-tensorflow-model-using-the-azure-machine-learning-visual-studio-code-extension"></a>Träna och distribuera en Avbildningsklassificering TensorFlow-modell med Azure Machine Learning Visual Studio-kodtillägget

Lär dig hur du tränar och distribuerar en avbildningsklassificeringsmodell för att känna igen handskrivna nummer med TensorFlow och Azure Machine Learning Visual Studio Code Extension.

I den här självstudien kommer du att lära dig följande:

> [!div class="checklist"]
> * Förstå koden
> * Skapa en arbetsyta
> * Skapa ett experiment
> * Konfigurera datormål
> * Köra en konfigurationsfil
> * Träna en modell
> * Registrera en modell
> * Distribuera en modell

## <a name="prerequisites"></a>Krav

- En Azure-prenumeration. Om du inte har någon, registrera dig för att prova den [kostnadsfria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree).
- Installera [Visual Studio Code](https://code.visualstudio.com/docs/setup/setup-overview), en lätt, plattformsoberoende kodredigerare.
- Azure Machine Learning Studio Visual Studio-kodtillägg. Instruktioner för installationsprogrammet finns i [tillägget Installations-Azure Machine Learning Visual Studio Code](./tutorial-setup-vscode-extension.md)

## <a name="understand-the-code"></a>Förstå koden

Koden för den här självstudien använder TensorFlow för att träna en maskininlärningsmodell för bildklassificering som kategoriserar handskrivna siffror från 0–9. Det gör det genom att skapa ett neuralt nätverk som tar pixelvärdena på 28 px x 28 px bild som indata och matar ut en lista med 10 sannolikheter, en för var och en av de siffror som klassificeras. Nedan följer ett exempel på hur data ser ut.  

![MNIST-siffror](./media/tutorial-train-deploy-image-classification-model-vscode/digits.png)

Hämta koden för den här självstudien genom att hämta och packa upp [VS-kodverktygen för AI-databasen](https://github.com/microsoft/vscode-tools-for-ai/archive/master.zip) var som helst på datorn.

## <a name="create-a-workspace"></a>Skapa en arbetsyta

Det första du behöver göra för att skapa ett program i Azure Machine Learning är att skapa en arbetsyta. En arbetsyta innehåller resurser för att utbilda modeller samt de utbildade modellerna själva. Mer information finns i [vad som är en arbetsyta](./concept-workspace.md). 

1. I aktivitetsfältet Visual Studio-kod väljer du **Azure-ikonen** för att öppna vyn Azure Machine Learning.
1. Högerklicka på din Azure-prenumeration och välj **Skapa arbetsyta**. 
    
    > [!div class="mx-imgBorder"]
    > ![Skapa en arbetsyta](./media/tutorial-train-deploy-image-classification-model-vscode/create-workspace.png)

1. Som standard genereras ett namn som innehåller datum och tid för skapandet. Ändra namnet till "TeamWorkspace" i textinmatningsrutan och tryck på **Retur**.
1. Välj **Skapa en ny resursgrupp**. 
1. Ge resursgruppen "TeamWorkspace-rg" ett namn och tryck på **Retur**. 
1. Välj en plats för arbetsytan. Vi rekommenderar att du väljer en plats som är närmast den plats du planerar att distribuera din modell. Till exempel "Västra US 2".
1. När du uppmanas att välja typ av arbetsyta väljer du **Grundläggande** för att skapa en grundläggande arbetsyta. Mer information om olika jobbytaerbjudanden finns i [Översikt över Azure Machine Learning](./overview-what-is-azure-ml.md#sku).

Nu görs en begäran till Azure om att skapa en ny arbetsyta i ditt konto. Efter några minuter visas den nya arbetsytan i prenumerationsnoden. 

## <a name="create-an-experiment"></a>Skapa ett experiment

Ett eller flera experiment kan skapas på arbetsytan för att spåra och analysera enskilda modellutbildningskörningar. Körningar kan göras i Azure-molnet eller på din lokala dator.

1. Välj **Azure-ikonen** i aktivitetsfältet Visual Studio-kod. Vyn Azure Machine Learning visas.
1. Expandera prenumerationsnoden.
1. Expandera **TeamWorkspace-noden.** 
1. Högerklicka på noden **Experiment.**
1. Välj **Skapa experiment** på snabbmenyn.

    > [!div class="mx-imgBorder"]
    > ![Skapa ett experiment](./media/tutorial-train-deploy-image-classification-model-vscode/create-experiment.png)

1. Namnge experimentet "MNIST" och tryck på **Retur** för att skapa det nya experimentet. 

Precis som arbetsytor skickas en begäran till Azure för att skapa ett experiment med de medföljande konfigurationerna. Efter några minuter visas det nya experimentet i noden *Experiment* på arbetsytan. 

## <a name="configure-compute-targets"></a>Konfigurera beräkningsmål

Ett beräkningsmål är den datorresurs eller miljö där du kör skript och distribuerar tränade modeller. Mer information finns i [dokumentationen](./concept-compute-target.md)för Azure Machine Learning compute targets .

Så här skapar du ett beräkningsmål:

1. Välj **Azure-ikonen** i aktivitetsfältet Visual Studio-kod. Vyn Azure Machine Learning visas. 
1. Expandera prenumerationsnoden. 
1. Expandera **TeamWorkspace-noden.** 
1. Högerklicka på **beräkningsnoden** under arbetsytans nod och välj **Skapa beräkning**. 

    > [!div class="mx-imgBorder"]
    > ![Skapa ett beräkningsmål](./media/tutorial-train-deploy-image-classification-model-vscode/create-compute.png)

1. Välj **Azure Machine Learning Compute (AmlCompute)**. Azure Machine Learning Compute är en infrastruktur för hanterad beräkning som gör det möjligt för användaren att enkelt skapa en enda eller flernnn-nodsberäkning som kan användas med andra användare på arbetsytan.
1. Välj storlek på den virtuella datorn. Välj **Standard_F2s_v2** i listan med alternativ. Storleken på den virtuella datorn påverkar hur lång tid det tar att träna dina modeller. Mer information om vm-storlekar finns i [storlekar för virtuella Linux-datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes).
1. Namnge din beräkning "TeamWkspc-com" och tryck på **Retur** för att skapa din beräkning.

Efter några minuter visas det nya beräkningsmålet i *beräkningsnoden* på arbetsytan.

## <a name="create-a-run-configuration"></a>Skapa en körningskonfiguration

När du skickar en utbildningskörning till ett beräkningsmål skickar du också in den konfiguration som behövs för att köra utbildningsjobbet. Skriptet som innehåller träningskoden och Python-beroendena som behövs för att köra den.

Så här skapar du en körningskonfiguration:

1. Välj **Azure-ikonen** i aktivitetsfältet Visual Studio-kod. Vyn Azure Machine Learning visas. 
1. Expandera prenumerationsnoden. 
1. Expandera **TeamWorkspace-noden.** 
1. Högerklicka på beräkningsnoden **TeamWkspc-com** under arbetsytan och välj **Skapa körningskonfiguration**.

    > [!div class="mx-imgBorder"]
    > ![Skapa en körningskonfiguration](./media/tutorial-train-deploy-image-classification-model-vscode/create-run-configuration.png)

1. Namnge din körningskonfiguration "MNIST-rc" och tryck på **Retur** för att skapa din körningskonfiguration.
1. Välj sedan **TensorFlow Single-Node Training** som utbildningsjobbtyp.
1. Tryck på **Retur** för att bläddra i skriptfilen för att köras på beräkningen. I det här fallet är skriptet `train.py` för `vscode-tools-for-ai/mnist-vscode-docs-sample` att träna modellen filen i katalogen.
1. Ange följande i indatarutan för att ange de paket som krävs.
    
    ```text
    pip: azureml-defaults; conda: python=3.6.2, tensorflow=1.15.0
    ```
    
    En fil `MNIST-rc.runconfig` som heter visas i VS-kod med innehåll som liknar den nedan:

    ```json
    {
        "script": "train.py",
        "framework": "Python",
        "communicator": "None",
        "target": "TeamWkspc-com",
        "environment": {
            "python": {
                "userManagedDependencies": false,
                "condaDependencies": {
                    "dependencies": [
                        "python=3.6.2",
                        "tensorflow=1.15.0",
                        {
                            "pip": [
                                "azureml-defaults"
                            ]
                        }
                    ]
                }
            },
            "docker": {
                "baseImage": "mcr.microsoft.com/azureml/base:0.2.4",
                "enabled": true,
                "baseImageRegistry": {
                    "address": null,
                    "username": null,
                    "password": null
                }
            }
        },
        "nodeCount": 1,
        "history": {
            "outputCollection": true,
            "snapshotProject": false,
            "directoriesToWatch": [
                "logs"
            ]
        }
    }
    ```

1. När du är nöjd med konfigurationen öppnar du kommandopaletten genom att välja **Visa > kommandopalett**.
1. Ange följande kommando i kommandopaletten för att spara körningskonfigurationsfilen.

    ```text
    Azure ML: Save and Continue
    ```

Körningskonfigurationen `MNIST-rc` läggs till under beräkningsnoden *TeamWkspc-com.*

## <a name="train-the-model"></a>Träna modellen

Under utbildningsprocessen skapas en TensorFlow-modell genom att bearbeta träningsdata och inlärningsmönster som är inbäddade i den för var och en av de respektive siffrorna som klassificeras. 

Så här kör du ett Azure Machine Learning-experiment:

1. Välj **Azure-ikonen** i aktivitetsfältet Visual Studio-kod. Vyn Azure Machine Learning visas. 
1. Expandera prenumerationsnoden. 
1. Expandera noden **TeamWorkspace > Experiment.** 
1. Högerklicka på **MNIST-experimentet.**
1. Välj **Kör experiment**.

    > [!div class="mx-imgBorder"]
    > ![Köra ett experiment](./media/tutorial-train-deploy-image-classification-model-vscode/run-experiment.png)

1. Välj **beräkningsmålet teamWkspc-com i** listan över beräkningsmål.
1. Välj sedan konfigurationen **MNIST-rc** run.
1. Nu skickas en begäran till Azure för att köra experimentet på det valda beräkningsmålet på arbetsytan. Den här processen tar flera minuter. Hur lång tid det ska ta att köra utbildningsjobbet påverkas av flera faktorer som beräkningstypen och utbildningsdatastorleken. Om du vill spåra experimentets förlopp högerklickar du på den aktuella körningsnoden och väljer **Visa körning i Azure-portalen**.
1. När dialogrutan som begär att öppna en extern webbplats visas väljer du **Öppna**.

    > [!div class="mx-imgBorder"]
    > ![Spåra experimentets förlopp](./media/tutorial-train-deploy-image-classification-model-vscode/track-experiment-progress.png)

När modellen är klar med träningen uppdateras statusetiketten bredvid körningsnoden till "Slutförd".

## <a name="register-the-model"></a>Registrera modellen

Nu när du har tränat din modell kan du registrera den på arbetsytan. 

Så här registrerar du din modell:

1. Välj **Azure-ikonen** i aktivitetsfältet Visual Studio-kod. Vyn Azure Machine Learning visas.
1. Expandera prenumerationsnoden. 
1. Expandera **TeamWorkspace-> experiment > MNIST-noden.**
1. Få modellutdata som genereras från utbildning av modellen. Högerklicka på **noden Kör 1** och välj **Hämta utdata**. 

    > [!div class="mx-imgBorder"]
    > ![Ladda ner modellutdata](./media/tutorial-train-deploy-image-classification-model-vscode/download-outputs.png)

1. Välj katalogen för att spara de nedladdade utdata till. Som standard placeras utdata i den katalog som för närvarande öppnas i Visual Studio-kod.
1. Högerklicka på noden **Modeller** och välj **Registrera modell**.

    > [!div class="mx-imgBorder"]
    > ![Registrera en modell](./media/tutorial-train-deploy-image-classification-model-vscode/register-model.png)

1. Namnge din modell "MNIST-TensorFlow-modell" och tryck på **Retur**.
1. En TensorFlow-modell består av flera filer. Välj **Modellmapp** som modellsökvägsformat i listan med alternativ. 
1. Markera `azureml_outputs/Run_1/outputs/outputs/model` katalogen.

    En fil som innehåller modellkonfigurationerna visas i Visual Studio-kod med liknande innehåll som den nedan:

    ```json
    {
        "modelName": "MNIST-TensorFlow-model",
        "tags": {
            "": ""
        },
        "modelPath": "c:\\Dev\\vscode-tools-for-ai\\mnist-vscode-docs-sample\\azureml_outputs\\Run_1\\outputs\\outputs\\model",
        "description": ""
    }
    ```

1. När du är nöjd med konfigurationen sparar du den genom att öppna kommandopaletten och ange följande kommando:

    ```text
    Azure ML: Save and Continue
    ```

Efter några minuter visas modellen under noden *Modeller.*

## <a name="deploy-the-model"></a>Distribuera modellen

I Visual Studio Code kan du distribuera din modell som en webbtjänst till:

+ Azure Container Instances (ACI).
+ Azure Kubernetes-tjänsten (AKS).

Du behöver inte skapa en ACI-behållare för att testa i förväg, eftersom ACI-behållare skapas efter behov. Du måste dock konfigurera AKS-kluster i förväg. Mer information om distributionsalternativ finns i [distribuera modeller med Azure Machine Learning](how-to-deploy-and-where.md) .

Så här distribuerar du en webbtjänst som ACI:

1. Välj **Azure-ikonen** i aktivitetsfältet Visual Studio-kod. Vyn Azure Machine Learning visas.
1. Expandera prenumerationsnoden. 
1. Expandera noden **TeamWorkspace > Modeller.** 
1. Högerklicka på **MNIST-TensorFlow-modellen** och välj **Distribuera tjänst från registrerad modell**.

    > [!div class="mx-imgBorder"]
    > ![Distribuera modellen](./media/tutorial-train-deploy-image-classification-model-vscode/register-model.png)

1. Välj **Azure Container Instances**.
1. Namnge tjänsten "mnist-tensorflow-svc" och tryck på **Retur**.
1. Välj det skript som ska köras i behållaren genom `score.py` att trycka `mnist-vscode-docs-sample` på **Retur** i indatarutan och bläddra efter filen i katalogen.
1. Ange de beroenden som behövs för att köra skriptet genom `env.yml` att `mnist-vscode-docs-sample` trycka på **Retur** i indatarutan och bläddra efter filen i katalogen.

    En fil som innehåller modellkonfigurationerna visas i Visual Studio-kod med liknande innehåll som den nedan:

    ```json
    {
        "name": "mnist-tensorflow-svc",
        "imageConfig": {
            "runtime": "python",
            "executionScript": "score.py",
            "dockerFile": null,
            "condaFile": "env.yml",
            "dependencies": [],
            "schemaFile": null,
            "enableGpu": false,
            "description": ""
        },
        "deploymentConfig": {
            "cpu_cores": 1,
            "memory_gb": 10,
            "tags": {
                "": ""
            },
            "description": ""
        },
        "deploymentType": "ACI",
        "modelIds": [
            "MNIST-TensorFlow-model:1"
        ]
    }
    ```
1. När du är nöjd med konfigurationen sparar du den genom att öppna kommandopaletten och ange följande kommando:

    ```text
    Azure ML: Save and Continue
    ```

Nu skickas en begäran till Azure för att distribuera din webbtjänst. Den här processen tar flera minuter. När den nya tjänsten har distribuerats visas den under noden *Slutpunkter.*

## <a name="next-steps"></a>Nästa steg

* En genomgång av hur du tränar med Azure Machine Learning utanför Visual Studio Code finns i [Självstudiekurs: Träna modeller med Azure Machine Learning](tutorial-train-models-with-aml.md).
* En genomgång av hur du redigerar, kör och felsöker kod lokalt finns i [Python hello-world-självstudien](https://code.visualstudio.com/docs/Python/Python-tutorial).

