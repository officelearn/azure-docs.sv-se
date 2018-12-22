---
title: Självstudie – Distribuerad träning med Azure Batch AI och Horovod | Microsoft Docs
description: Självstudie – Så här tränar du en distribuerad modell med Horovod med Azure Batch AI-tjänsten och Azure CLI.
services: batch-ai
author: johnwu10
manager: jeconnoc
ms.service: batch-ai
ms.topic: tutorial
ms.date: 09/03/2018
ms.author: danlep
ms.custom: mvc
ROBOTS: NOINDEX
ms.openlocfilehash: 45255845d8645391ee33471830ac2ef27870a40d
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2018
ms.locfileid: "53408637"
---
# <a name="tutorial-train-a-distributed-model-with-horovod"></a>Självstudie: Träna en distribuerad modell med Horovod

[!INCLUDE [batch-ai-retiring](../../includes/batch-ai-retiring.md)]

I den här självstudien tränar du en distribuerad modell för djupinlärning genom att köra den parallellt över flera noder i ett Batch AI-kluster. Batch AI är en hanterad tjänst för träning av modeller för maskininlärning och AI i hög skala på kluster av Azure-GPU:er. 

Den här självstudien introducerar ett vanligt Batch AI-arbetsflöde tillsammans med hur du interagerar med Batch AI-resurser via Azure CLI. Exempel på ämnen som ingår:

> [!div class="checklist"]
> * Konfigurera en arbetsyta, ett experiment och ett kluster för Batch AI
> * Konfigurera en Azure-filresurs för indata och utdata
> * Parallellisera en modell för djupinlärning med Horovod
> * Skicka ett träningsjobb
> * Övervaka jobbet
> * Hämta träningsresultaten

För den här självstudien ändras en modell för objektidentifiering för att köras parallellt med [Horovod](https://github.com/uber/horovod). Modellen tränas på [CIFAR 10-datauppsättningen](https://www.cs.toronto.edu/~kriz/cifar.html) av bilder. Träningsjobbet körs i ett kluster med 24 vCPU:er och 4 GPU:er och tar cirka 60 minuter att slutföra.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0.38 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli). 

## <a name="why-use-horovod"></a>Varför använda Horovod?

Horovod är ett ramverk för distribuerad träning för Tensorflow, Keras och PyTorch, och används för den här självstudien. Med Horovod kan du konvertera ett träningsskript som utformats för att köras på en enskild GPU till ett som körs effektivt på ett distribuerat system med bara några kodrader.

Förutom Horovod stöder Batch AI distribuerad träning med flera andra populära ramverk med öppen källkod. Kom ihåg att granska licensvillkoren för alla ramverk som du använder för att träna modeller i produktion.

## <a name="prepare-the-batch-ai-environment"></a>Förbered Batch AI-miljön

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Använd kommandot `az group create` för att skapa en resursgrupp med namnet `batchai.horovod` i regionen `eastus`. Du kan använda resursgruppen för att distribuera Batch AI-resurser.

```azurecli-interactive
az group create --name batchai.horovod --location eastus
```

### <a name="create-a-workspace"></a>Skapa en arbetsyta

Använd kommandot `az batchai workspace create` för att skapa en Batch AI-arbetsyta. En arbetsyta är en toppnivå-samling med andra Batch AI-resurser. Följande kommando skapar en arbetsyta som heter `batchaidev` i din resursgrupp.

```azurecli-interactive
az batchai workspace create --resource-group batchai.horovod --workspace batchaidev 
```

### <a name="create-an-experiment"></a>Skapa ett experiment

Ett Batch AI-experiment grupperar ett eller flera jobb som du skickar frågor till och hanterar tillsammans. Det följande kommandot `az batchai experiment create` skapar ett experiment som heter `cifar` i din arbetsyta och resursgrupp.

```azurecli-interactive
az batchai experiment create --resource-group batchai.horovod --workspace batchaidev --name cifar 
```

## <a name="set-up-a-gpu-cluster"></a>Konfigurera ett GPU-kluster

Konfigurera sedan ett GPU-kluster för att köra experimentet. Batch AI tillhandahåller olika flexibla alternativ för att anpassa kluster för specifika behov.

Det följande kommandot `az batchai cluster create` skapar ett kluster med 4 noder som heter `nc6cluster` i din arbetsyta och resursgrupp. Som standard kör de virtuella datorerna i klustret en Ubuntu Server-avbildning som har utformats för att vara värd för containerbaserade program. Klusternoderna i det här exemplet använder storleken `Standard_NC6`, som innehåller en NVIDIA Tesla K80 GPU.

```azurecli-interactive
az batchai cluster create --resource-group batchai.horovod --workspace batchaidev --name nc6cluster --vm-priority dedicated  --vm-size Standard_NC6 --target 4 --generate-ssh-keys
```

Kör kommandot `az batchai cluster show` för att visa klustrets status. Det tar vanligtvis några minuter att helt etablera klustret.

```azurecli-interactive
az batchai cluster show --name nc6cluster --workspace batchaidev --resource-group batchai.horovod --output table
```

Tidigt under processen när klustret skapas befinner sig klustret i tillståndet `resizing`. Fortsätt med följande steg när klustrets tillstånd ändras. Klustret är redo att köra träningsjobbet när tillståndet är `steady` och noderna är `idle`. Exempel:

```
Name        Resource Group    Workspace    VM Size       State      Idle    Running    Preparing    Leaving    Unusable
----------  ----------------  -----------  ------------  -------  ------  ---------  -----------  ---------  ----------
nc6cluster  batchai.horovod  batchaidev   STANDARD_NC6  steady        4          0            0          0           0
```

## <a name="set-up-storage"></a>Konfigurera lagring

Använd kommandot `az storage account create` för att skapa ett lagringskonto för att lagra dina träningsskript och dina utdata från träningen.

```azurecli-interactive
az storage account create --resource-group batchai.horovod --name mystorageaccount --location eastus --sku Standard_LRS
```

Skapa en Azure-filresurs som heter `myshare` i kontot med kommandot `az storage share create`:

```azurecli-interactive
az storage share create --name myshare --account-name mystorageaccount
```

I praktiken kan den här lagringen användas i flera jobb och experiment. Organisera materialet genom att skapa en katalog i filresursen för att lagra filer som är relaterade till det här specifika experimentet. Följande `az storage directory create`-kommando skapar en katalog som heter `cifar`.

```azurecli-interactive
az storage directory create --name cifar --share-name myshare --account-name mystorageaccount
```

Nästa steg är att förbereda det faktiska träningsskriptet som du sedan överför till den nya katalogen.

## <a name="create-the-training-script"></a>Skapa träningsskriptet

I det här experimentet kör du ett Python-skript som uppdateras med några ändringar för att köra en modell för objektsidentifiering parallellt med Horovod. I den [ursprungliga modellen](https://raw.githubusercontent.com/keras-team/keras/master/examples/cifar10_cnn.py) används Keras med en TensorFlow-serverdel. 

Använd en valfri textredigerare i en arbetskatalog i ditt gränssnitt för att skapa en fil som heter `cifar_cnn_distributed.py` och har följande innehåll. Ändringar i den ursprungliga källkoden har kommenterats med ett `HOROVOD`-prefix.

```python
from __future__ import print_function
import keras
from keras.datasets import cifar10
from keras.preprocessing.image import ImageDataGenerator
from keras.models import Sequential
from keras.layers import Dense, Dropout, Activation, Flatten
from keras.layers import Conv2D, MaxPooling2D
import tensorflow as tf
import horovod.keras as hvd
import os
from keras import backend as K
import math
import argparse 

# HOROVOD: initialize Horovod.
hvd.init()

# HOROVOD: pin GPU to be used to process local rank (one GPU per process)
config = tf.ConfigProto()
config.gpu_options.allow_growth = True
config.gpu_options.visible_device_list = str(hvd.local_rank())
K.set_session(tf.Session(config=config))

batch_size = 32
num_classes = 10
# HOROVOD: adjust number of epochs based on number of GPUs.
epochs = int(math.ceil(100.0 / hvd.size()))

data_augmentation = True
num_predictions = 20
# BATCH AI: change save directory to mounted storage path
parser = argparse.ArgumentParser()
parser.add_argument("-d", "--dir", help="directory to save model to")
args = parser.parse_args()
save_dir = os.path.join(args.dir, 'saved_models')
model_name = 'keras_cifar10_trained_model.h5'

# The data, split between train and test sets:
(x_train, y_train), (x_test, y_test) = cifar10.load_data()
print('x_train shape:', x_train.shape)
print(x_train.shape[0], 'train samples')
print(x_test.shape[0], 'test samples')

# Convert class vectors to binary class matrices.
y_train = keras.utils.to_categorical(y_train, num_classes)
y_test = keras.utils.to_categorical(y_test, num_classes)

model = Sequential()
model.add(Conv2D(32, (3, 3), padding='same',
                 input_shape=x_train.shape[1:]))
model.add(Activation('relu'))
model.add(Conv2D(32, (3, 3)))
model.add(Activation('relu'))
model.add(MaxPooling2D(pool_size=(2, 2)))
model.add(Dropout(0.25))

model.add(Conv2D(64, (3, 3), padding='same'))
model.add(Activation('relu'))
model.add(Conv2D(64, (3, 3)))
model.add(Activation('relu'))
model.add(MaxPooling2D(pool_size=(2, 2)))
model.add(Dropout(0.25))

model.add(Flatten())
model.add(Dense(512))
model.add(Activation('relu'))
model.add(Dropout(0.5))
model.add(Dense(num_classes))
model.add(Activation('softmax'))

# HOROVOD: adjust learning rate based on number of GPUs.
opt = keras.optimizers.rmsprop(lr=0.0001 * hvd.size(), decay=1e-6)

# HOROVOD: add Horovod Distributed Optimizer.
opt = hvd.DistributedOptimizer(opt)

# Let's train the model using RMSprop
model.compile(loss='categorical_crossentropy',
              optimizer=opt,
              metrics=['accuracy'])

callbacks = [
    # HOROVOD: broadcast initial variable states from rank 0 to all other processes.
    # This is necessary to ensure consistent initialization of all workers when
    # training is started with random weights or restored from a checkpoint.
    hvd.callbacks.BroadcastGlobalVariablesCallback(0),
]

# HOROVOD: save checkpoints only on worker 0 to prevent other workers from corrupting them.
if hvd.rank() == 0:
    callbacks.append(keras.callbacks.ModelCheckpoint('./checkpoint-{epoch}.h5'))

x_train = x_train.astype('float32')
x_test = x_test.astype('float32')
x_train /= 255
x_test /= 255

if not data_augmentation:
    print('Not using data augmentation.')
    model.fit(x_train, y_train,
              batch_size=batch_size,
              epochs=epochs,
              validation_data=(x_test, y_test),
              shuffle=True)
else:
    print('Using real-time data augmentation.')
    # This will do preprocessing and realtime data augmentation:
    datagen = ImageDataGenerator(
        featurewise_center=False,  # set input mean to 0 over the dataset
        samplewise_center=False,  # set each sample mean to 0
        featurewise_std_normalization=False,  # divide inputs by std of the dataset
        samplewise_std_normalization=False,  # divide each input by its std
        zca_whitening=False,  # apply ZCA whitening
        zca_epsilon=1e-06,  # epsilon for ZCA whitening
        rotation_range=0,  # randomly rotate images in the range (degrees, 0 to 180)
        width_shift_range=0.1,  # randomly shift images horizontally (fraction of total width)
        height_shift_range=0.1,  # randomly shift images vertically (fraction of total height)
        shear_range=0.,  # set range for random shear
        zoom_range=0.,  # set range for random zoom
        channel_shift_range=0.,  # set range for random channel shifts
        fill_mode='nearest',  # set mode for filling points outside the input boundaries
        cval=0.,  # value used for fill_mode = "constant"
        horizontal_flip=True,  # randomly flip images
        vertical_flip=False,  # randomly flip images
        rescale=None,  # set rescaling factor (applied before any other transformation)
        preprocessing_function=None,  # set function that will be applied on each input
        data_format=None,  # image data format, either "channels_first" or "channels_last"
        validation_split=0.0)  # fraction of images reserved for validation (strictly between 0 and 1)

    # Compute quantities required for feature-wise normalization
    # (std, mean, and principal components if ZCA whitening is applied).
    datagen.fit(x_train)

    # Fit the model on the batches generated by datagen.flow().
    model.fit_generator(datagen.flow(x_train, y_train,
                                     batch_size=batch_size),
                        epochs=epochs,
                        validation_data=(x_test, y_test),
                        workers=4)

# Save model and weights
if not os.path.isdir(save_dir):
    os.makedirs(save_dir)
model_path = os.path.join(save_dir, model_name)
model.save(model_path)
print('Saved trained model at %s ' % model_path)

# Score trained model.
scores = model.evaluate(x_test, y_test, verbose=1)
print('Test loss:', scores[0])
print('Test accuracy:', scores[1])
```

I det här exemplet behövs endast några uppdateringar av modellen för att aktivera distribuerad träning med Horovod-ramverket. 

Tänk på att det här skriptet använder en relativt liten modell och datauppsättning för demosyften – därför innebär kanske inte den här distribuerade modellen någon stor prestandaförbättring. Om du verkligen vill se hur kraftfull distribuerad träning är använder du en mycket större modell och datauppsättning.

## <a name="upload-the-training-script"></a>Överför träningsskriptet

När skriptet är klart är nästa steg att överföra det till filresurskatalogen som du skapade tidigare. Med följande kommando `az storage file upload` överför du det från den lokala arbetskatalogen till rätt plats.

```azurecli-interactive
az storage file upload --path cifar --share-name myshare --source cifar_cnn_distributed.py --account-name mystorageaccount
```

## <a name="submit-the-training-job"></a>Skicka träningsjobbet

Skapa ett träningsjobb när du har slutfört föregående steg. I Batch AI använder du en `job.json`-fil för att definiera parametrarna som anger hur ett jobb ska köras. Använd valfri textredigerare för att skapa en jobbkonfigurationsfil som heter `job.json` med följande innehåll.

```json
{
    "$schema": "https://raw.githubusercontent.com/Azure/BatchAI/master/schemas/2018-05-01/job.json",
    "properties": {
        "nodeCount": 4,
        "horovodSettings": {
            "pythonScriptFilePath": "$AZ_BATCHAI_JOB_MOUNT_ROOT/myshare/cifar/cifar_cnn_distributed.py",
            "commandLineArgs": "--dir=$AZ_BATCHAI_JOB_MOUNT_ROOT/myshare/cifar"
        },
        "stdOutErrPathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/myshare/cifar",
        "mountVolumes": {
            "azureFileShares": [
                {
                    "azureFileUrl": "https://<AZURE_BATCHAI_STORAGE_ACCOUNT>.file.core.windows.net/myshare",
                    "relativeMountPath": "myshare"
                }
            ]
        },
        "jobPreparation": {
            "commandLine": "apt update; apt install mpi-default-dev mpi-default-bin -y; pip install keras horovod"
        },
        "containerSettings": {
            "imageSourceRegistry": {
                "image": "tensorflow/tensorflow:1.8.0-gpu"
            }
        }
    }
}
```

Förklaring av egenskaper:

| Egenskap | Beskrivning |
| --------- | --------- |
| `nodeCount` | Antalet noder som ska dedikeras för jobbet. Här körs jobbet parallellt på `4` noder. |
| `horovodSettings` | Fältet `pythonScriptFilePath` definierar sökvägen till Horovod-skriptet som finns i katalogen `cifar` som skapades tidigare. Fältet `commandLineArgs` är kommandoradsargumenten för att köra skriptet. För det här experimentet är det enda obligatoriska argumentet katalogen där du vill spara modellen. `$AZ_BATCHAI_JOB_MOUNT_ROOT/myshare` är sökvägen där filresursen monterades. | 
| `stdOutErrPathPrefix` | Sökvägen för att lagra utdata och loggar för jobbet – för detta exempel är det samma `cifar`-katalog. |
| `jobPreparation` | Eventuella särskilda instruktioner som förbereder miljön för att köra jobbet. Det här skriptet kräver installation av de angivna MPI- och Horovod-paketen. |
| `containerSettings` | Inställningar för containern som jobbet körs på. Det här jobbet använder en Docker-container som skapats med `tensorflow`.

Använd konfigurationen och skapa jobbet med kommandot `az batchai job create`. Följande kommando placerar ett nytt jobb i kö som heter `cifar_distributed` med alla resurser som har angetts fram till den här tidpunkten. 

```azurecli-interactive
az batchai job create --cluster nc6cluster --name cifar_distributed --resource-group batchai.horovod --workspace batchaidev --experiment cifar --config-file job.json --storage-account-name mystorageaccount
```

Om noderna för tillfället är upptagna kan det ta en stund innan jobbet startas. Använd kommandot `az batchai job show` för att visa jobbets körningstillstånd.

```azurecli-interactive
az batchai job show --experiment cifar --name cifar_distributed --resource-group batchai.horovod --workspace batchaidev --query "executionState"
```

### <a name="visualize-the-distributed-training"></a>Visualisera den distribuerade träningen

När jobbet börjar köras kan du använda kommandot `az batchai cluster show` igen för att skicka frågor om status till klusternoderna. 

```azurecli-interactive
az batchai cluster show --name nc6cluster --workspace batchaidev --resource-group batchai.horovod --query "nodeStateCounts"
```

Utdata ska se ut ungefär så här, som visar ett tillstånd där alla fyra körs. Det här resultatet visar att alla fyra noderna för närvarande används i den distribuerade träningen.

```
{
  "idleNodeCount": 0,
  "leavingNodeCount": 0,
  "preparingNodeCount": 0,
  "runningNodeCount": 4,
  "unusableNodeCount": 0
}
```

## <a name="monitor-the-job"></a>Övervaka jobbet

### <a name="list-output-files"></a>Lista utdatafiler

Medan jobbet körs använder du kommandot `az batchai job file list` för att lista utdatafilerna som jobbet skapar.

```azurecli-interactive
az batchai job file list --experiment cifar --job cifar_distributed --resource-group batchai.horovod --workspace batchaidev --output table
```

För det här specifika experimentet ska utdata se ut ungefär så här. Jobbets sammanlagda utdata loggas i `stdout.txt` medan `stderr.txt` rapporterar eventuella fel som uppstår vid huvudkörningen. De andra filerna är loggar för utdata, fel och jobbförberedelse för varje motsvarande enskild nod.

```
Name                                                    Type       Size  Modified
------------------------------------------------------  ------  -------  -------------------------
execution-tvm-676767296_1-20180718t174802z-p.log        file       8801  2018-07-18T22:41:28+00:00
execution-tvm-676767296_2-20180718t174802z-p.log        file      15094  2018-07-18T22:41:55+00:00
execution-tvm-676767296_3-20180718t174802z-p.log        file       8801  2018-07-18T22:41:28+00:00
execution-tvm-676767296_4-20180718t174802z-p.log        file       8801  2018-07-18T22:41:28+00:00
stderr-job_prep-tvm-676767296_1-20180718t174802z-p.txt  file        238  2018-07-18T22:41:50+00:00
stderr-job_prep-tvm-676767296_2-20180718t174802z-p.txt  file        238  2018-07-18T22:41:50+00:00
stderr-job_prep-tvm-676767296_3-20180718t174802z-p.txt  file        238  2018-07-18T22:41:50+00:00
stderr-job_prep-tvm-676767296_4-20180718t174802z-p.txt  file        238  2018-07-18T22:41:50+00:00
stderr.txt                                              file       7653  2018-07-18T22:46:32+00:00
stdout-job_prep-tvm-676767296_1-20180718t174802z-p.txt  file      13651  2018-07-18T22:41:55+00:00
stdout-job_prep-tvm-676767296_2-20180718t174802z-p.txt  file      13651  2018-07-18T22:41:54+00:00
stdout-job_prep-tvm-676767296_3-20180718t174802z-p.txt  file      13651  2018-07-18T22:41:54+00:00
stdout-job_prep-tvm-676767296_4-20180718t174802z-p.txt  file      13651  2018-07-18T22:41:55+00:00
stdout.txt                                              file    2316480  2018-07-18T22:46:32+00:00
```

### <a name="stream-an-output-file"></a>Strömma en utdatafil

Använd kommandot `az batchai job file stream` för att strömma innehållet i en fil. I följande exempel strömmas den huvudsakliga utdataloggen.

```azurecli-interactive
az batchai job file stream --experiment cifar --file-name stdout.txt --job cifar_distributed --resource-group batchai.horovod --workspace batchaidev
```

När jobbet körs strömmar kommandot standardutdata för träningsjobbet, och utdata som liknar följande visas.

```
...
50000 train samples
10000 test samples
Using real-time data augmentation.
Epoch 1/25


   1/1563 [..............................] - ETA: 2:42:25 - loss: 2.3334 - acc: 0.0312   1/1563 [..............................] - ETA: 2:30:42 - loss: 2.2973 - acc: 0.0938
   1/1563 [..............................] - ETA: 30:36 - loss: 2.3175 - acc: 0.1250
   1/1563 [..............................] - ETA: 2:32:58 - loss: 2.3489 - acc: 0.0625
   2/1563 [..............................] - ETA: 1:21:59 - loss: 2.3230 - acc: 0.0625

   2/1563 [..............................]   2/1563 [..............................] - ETA: 1:16:09 - loss: 2.2913 - acc: 0.0938 - ETA: 1:17:15 - loss: 2.3147 - acc: 0.0781
   2/1563 [..............................] - ETA: 16:07 - loss: 2.3678 - acc: 0.0938
   3/1563 [..............................] - ETA: 55:05 - loss: 2.3232 - acc: 0.0938  
   3/1563 [..............................] - ETA: 51:57 - loss: 2.3185 - acc: 0.1146  
   3/1563 [..............................] - ETA: 51:12 - loss: 2.3179 - acc: 0.1042  
   3/1563 [..............................] - ETA: 11:13 - loss: 2.3504 - acc: 0.0833
   4/1563 [..............................] - ETA: 39:43 - loss: 2.3224 - acc: 0.1094
   4/1563 [..............................] - ETA: 42:09 - loss: 2.3049 - acc: 0.1250
   4/1563 [..............................] - ETA: 39:15 - loss: 2.3089 - acc: 0.1094
   4/1563 [..............................] - ETA: 9:16 - loss: 2.3316 - acc: 0.1016 
   5/1563 [..............................] - ETA: 39:51 - loss: 2.3153 - acc: 0.1125
   5/1563 [..............................] - ETA: 37:58 - loss: 2.3197 - acc: 0.1125
   5/1563 [..............................] - ETA: 37:35 - loss: 2.3148 - acc: 0.1062
   5/1563 [..............................] - ETA: 13:38 - loss: 2.3263 - acc: 0.1062
   6/1563 [..............................] - ETA: 35:48 - loss: 2.3168 - acc: 0.1198

   6/1563 [..............................]   6/1563 [..............................] - ETA: 34:13 - loss: 2.3142 - acc: 0.1198 - ETA: 33:51 - loss: 2.3162 - acc: 0.1042
   6/1563 [..............................] - ETA: 13:54 - loss: 2.3225 - acc: 0.1094
   7/1563 [..............................] - ETA: 30:53 - loss: 2.3181 - acc: 0.1071

   7/1563 [..............................]   7/1563 [..............................] - ETA: 29:32 - loss: 2.3149 - acc: 0.1161 - ETA: 29:13 - loss: 2.3140 - acc: 0.0938
   7/1563 [..............................] - ETA: 12:09 - loss: 2.3174 - acc: 0.1205
   8/1563 [..............................] - ETA: 26:04 - loss: 2.3113 - acc: 0.1133
   8/1563 [..............................] - ETA: 27:15 - loss: 2.3169 - acc: 0.1133
   8/1563 [..............................] - ETA: 10:51 - loss: 2.3152 - acc: 0.1172
...
```

Skriptet tränar fler än 25 epoker eller genomkörningar av träningsdatauppsättningen. Den här processen tar cirka 60 minuter. 

## <a name="retrieve-the-results"></a>Hämta resultaten

När skriptet har slutförts bör verifieringens noggrannhet vara cirka 70–75 % om allt har gått bra, och den tränade modellen sparas i filresursen på `cifar/saved_models/keras_cifar10_trained_model.h5`. 

Modellträning är vanligtvis en del av ett större arbetsflöde. Du kan till exempel göra den tränade modellen tillgänglig i ett annat program. Om du vill ladda ner den tränade modellen lokalt använder du kommandot `az storage file download`. 

```azurecli-interactive
az storage file download --path cifar/saved_models/keras_cifar10_trained_model.h5 --share-name myshare --account-name mystorageaccount
```
## <a name="clean-up-resources"></a>Rensa resurser

När jobben har körts klart är ett metodtips för att göra besparingar för beräkningskostnader att skala ned alla kluster till `0 nodes` så att du inte debiteras för inaktiv tid. Ange följande kommando `az batchai cluster resize`. 

```azurecli-interactive
az batchai cluster resize --name nc6cluster --resource-group batchai.horovod --target 0 --workspace batchaidev
```

Senare kan du byta till 1 eller flera noder. 

Om du inte planerar att använda arbetsytan och lagringskontot i framtiden tar du bort resursgruppen med kommandot `az group delete`. När du tar bort en resursgrupp tas alla resurser som ingår i den gruppen bort.

```azurecli-interactive
az group delete --name batchai.horovod
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig att:

> [!div class="checklist"]
> * Konfigurera en arbetsyta, ett experiment och ett kluster för Batch AI
> * Konfigurera en Azure-filresurs för indata och utdata
> * Parallellisera en modell med Horovod
> * Skicka ett träningsjobb
> * Övervaka jobbet
> * Hämta träningsresultaten

Exempel på användning av Batch AI med olika ramverk finns i recepten på GitHub.

> [!div class="nextstepaction"]
> [Batch AI-recept](https://github.com/Azure/BatchAI/tree/master/recipes)
