---
title: Hur du skapar DSVM och HDI som beräkningsmål för Azure ML
description: Skapa DSVM och HDI Spark-kluster som beräkningsmål för Azure ML-experimentering.
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 09/26/2017
ms.openlocfilehash: 211f60b9c25b4bd20769f6a4840afaecf8373b9f
ms.sourcegitcommit: e2348a7a40dc352677ae0d7e4096540b47704374
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/05/2018
ms.locfileid: "43782353"
---
# <a name="create-dsvm-and-hdi-spark-cluster-as-compute-targets"></a>Skapa DSVM och HDI Spark-kluster som beräkningsmål

Du kan enkelt skala upp eller skala ut din machine learning-experiment genom att lägga till ytterligare beräkningsmål som Ubuntu-baserad DSVM (Data Science Virtual Machine) och Apache Spark för Azure HDInsight-kluster. Den här artikeln får du hjälp med att skapa dessa beräkningsmål i Azure. Mer information om Azure ML beräkningsmål som avser [översikt över Azure Machine Learning-experimentering](experimentation-service-configuration.md).

>[!NOTE]
>Du måste se till att du har behörighet att skapa resurser, till exempel virtuella datorer och HDI-kluster i Azure innan du fortsätter. Båda dessa resurser kan också använda många beräkningskärnor beroende på din konfiguration. Kontrollera att din prenumeration har tillräckligt med kapacitet för de virtuella CPU-kärnorna. Du kan alltid hitta kontakten med Azure-supporten för att öka det maximala antalet kärnor som tillåts i din prenumeration.

## <a name="create-an-ubuntu-dsvm-in-azure-portal"></a>Skapa en Ubuntu DSVM i Azure-portalen

Du kan skapa en DSVM från Azure-portalen. 

1. Logga in på Azure portal från https://portal.azure.com
2. Klicka på den **+ ny** länk och Sök efter ”data science virtual machine för Linux”.
    ![Ubuntu](media/how-to-create-dsvm-hdi/ubuntu_dsvm.png)
4. Välj **Data Science Virtual Machine för Linux (Ubuntu)** i listan och följ de anvisningarna på skärmen instruktioner för att skapa DSVM.

>[!IMPORTANT]
>Se till att välja **lösenord** som den _autentiseringstyp_.

![använda pwd](media/how-to-create-dsvm-hdi/use_pwd.png)

## <a name="create-an-ubuntu-dsvm-using-azure-cli"></a>Skapa en Ubuntu DSVM med azure cli

Du kan också använda en Azure resource management-mall för att distribuera en DSVM.

>[!NOTE]
>Alla följande kommandon antas vara utfärdat från rotmappen på ett Azure ML-projekt.

Börja med att skapa en `mydsvm.json` med valfri textredigerare på den `docs` mapp. (Om du inte har en `docs` mapp i rotmappen för projektet, skapa en.) Vi använder den här filen för att konfigurera några grundläggande parametrar för Azure resource management-mall. 

Kopiera och klistra in följande JSON-kodfragmentet i den `mydsvm.json` filen och Fyll i lämpliga värden:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "adminUsername": { "value" : "<admin username>"},
     "adminPassword": { "value" : "<admin password>"},
     "vmName": { "value" : "<vm name>"},
     "vmSize": { "value" : "<vm size>"}
  }
}
```

För den _vmSize_ fält, som du kan använda alla de stöds VM-storlekar som anges i den [Ubuntu DSVM Azure resource management-mall](https://github.com/Azure/DataScienceVM/blob/master/Scripts/CreateDSVM/Ubuntu/multiazuredeploywithext.json). Vi rekommenderar att du använder en av de nedan storlekar som beräkningsmål för Azure ML. 


>[!TIP]
> För [deep learning arbetsbelastningar](how-to-use-gpu.md) du kan distribuera till GPU-baserade virtuella datorer.

- [Generell användning virtuella datorer](../../virtual-machines/linux/sizes-general.md)
  - Standard_DS2_v2 
  - Standard_DS3_v2 
  - Standard_DS4_v2 
  - Standard_DS12_v2 
  - Standard_DS13_v2 
  - Standard_DS14_v2 
- [GPU-baserade virtuella datorer](../../virtual-machines/linux/sizes-gpu.md)
  - Standard_NC6 
  - Standard_NC12 
  - Standard_NC24 
 

Läs mer om dessa [storlekar för Linux-datorer i Azure](../../virtual-machines/linux/sizes.md) och deras [prisinformation](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

Starta CLI-fönstret från appen Azure ML Workbench genom att klicka på **filen** --> **öppna Kommandotolken**, eller **öppna PowerShell** menyalternativ. 

>[!NOTE]
>Du kan också göra detta i alla kommandoradsmiljö där du har installerat az-cli.

I fönstret Kommandotolken anger du den nedan kommandon:

```azurecli
# first make sure you have a valid Azure authentication token
$ az account get-access-token

# if you don't have a valid token, please log in to Azure first. 
# if you already do, you can skip this step.
$ az login

# list all subscriptions you have access to
$ az account list -o table

# make sure you set the subscription you want to use to create DSVM as the current subscription
$ az account set -s <subscription name or Id>

# it is always a good idea to create a resource group for the VM and associated resources to live in.
# you can use any Azure region, but it is best to create them in the region where your Azure ML Experimentation account is, e.g. eastus2, westcentralus or australiaeast.
# also, only certain Azure regions has GPU-equipped VMs available.
$ az group create -n <resource group name> -l <azure region>

# now let's create the DSVM based on the JSON configuration file you created earlier.
# note we assume the mydsvm.json config file is placed in the "docs" sub-folder.
$ az group deployment create -g <resource group name> --template-uri https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/azuredeploy.json --parameters @docs/mydsvm.json

# find the FQDN (fully qualified domain name) of the VM just created. 
# you can also use IP address from the next command if FQDN is not set.
$ az vm show -g <resource group name> -n <vm name> --query "fqdns"

# find the IP address of the VM just created
$ az vm show -g <resource group name> -n <vm name> --query "publicIps"
```
## <a name="attach-a-dsvm-compute-target"></a>Bifoga beräkningsmål DSVM
När DSVM har skapats kan koppla du nu det till ditt Azure ML-projekt.

```azurecli
# attach the DSVM compute target
# it is a good idea to use FQDN in case the IP address changes after you deallocate the VM and restart it
$ az ml computetarget attach remotedocker --name <compute target name> --address <ip address or FQDN> --username <admin username> --password <admin password> 

# prepare the Docker image on the DSVM 
$ az ml experiment prepare -c <compute target name>
```
Nu bör du vara redo att köra experiment på den här DSVM.

## <a name="deallocate-a-dsvm-and-restart-it-later"></a>Frigöra en DSVM och starta om senare
När du är klar beräknings-uppgifter från Azure ML kan du frigöra DSVM. Den här åtgärden stänger av den virtuella datorn startar om beräkningsresurserna, men den bevarar de virtuella diskarna. Du debiteras inte för beräkningskostnaden när Virtuellt datorn frigörs.

Att frigöra en virtuell dator:

```azurecli
$ az vm deallocate -g <resource group name> -n <vm name>
```

Om du vill ge den virtuella datorn tillbaka liv, använda den `az ml start` kommando:

```azurecli
$ az vm start -g <resource group name> -n <vm name>
```

## <a name="expand-the-dsvm-os-disk"></a>Expandera DSVM Operativsystemets disk
Ubuntu DSVM levereras med en OS-disk 50GB och 100GB data-disk. Docker lagrar dess bilder på datadisk, som mer utrymme finns det. När detta används som beräkningsmål för Azure ML kan kan den här disken användas av Docker-motorn dra nedåt Docker-avbildningar och skapa conda lager ovanpå den. Du kan behöva expandera disk till en större storlek (till exempel 200 GB) för att undvika ”disken är full”-fel när du är mitt i en körning. Referens [hur du expanderar virtuella hårddiskar på en Linux VM med Azure CLI](../../virtual-machines/linux/expand-disks.md) information om hur du enkelt göra detta från azure cli. 

## <a name="create-an-apache-spark-for-azure-hdinsight-cluster-in-azure-portal"></a>Skapa ett Apache Spark för Azure HDInsight-kluster i Azure portal

Om du vill köra skala ut Spark-jobb, måste du skapa ett Apache Spark för Azure HDInsight-kluster i Azure-portalen.

1. Logga in på Azure portal från https://portal.azure.com
2. Klicka på den **+ ny** länk och Sök efter ”HDInsight”.

    ![hitta hdi](media/how-to-create-dsvm-hdi/hdi.png)
    
3. Välj **HDInsight** i listan och sedan klicka på den **skapa** knappen.
4. I den **grunderna** skärm för konfiguration av **Klustertyp** inställningar, se till att välja **Spark** som den _Klustertyp_, **Linux** som den _operativsystemet_, och **Spark 2.1.0 (HDI 3.6)** som _Version.

    ![Konfigurera hdi](media/how-to-create-dsvm-hdi/configure_hdi.png)

    >[!IMPORTANT]
    >Meddelande i skärmbilden ovan klustret har en _användarnamnet för klusterinloggning_ fält och en _Secure Shell (SSH)-användarnamn_ fält. Det här är två olika användarnas identiteter, även om för att underlätta kan du ange samma lösenord för båda inloggningar. Den _användarnamnet för klusterinloggning_ används för att logga in på management-Webbgränssnittet för HDI-kluster. Den _SSH-användarnamn för klusterinloggning_ används för att logga in på huvudnoden för klustret, och det här är vad som behövs för Azure ML att skicka ut Spark-jobb.

5. Välj klusterstorlek och nodstorlek du behöver och slutför guiden Skapa. Det kan ta upp till 30 minuter innan klustret för att slutföra etableringen. 

## <a name="attach-an-hdi-spark-cluster-compute-target"></a>Koppla ett beräkningsmål för HDI Spark-kluster

När HDI Spark-klustret har skapats kan kan nu du koppla den till ditt Azure ML-projekt.

```azurecli
# attach the HDI compute target
$ az ml computetarget attach cluster --name <compute target name> --address <cluster name, such as myhdicluster123.azurehdinsight.net> --username <ssh username> --password <ssh password> 

# prepare the conda environment on HDI
$ az ml experiment prepare -c <compute target name>
```
Nu bör du vara redo att köra experiment i Spark-kluster.

## <a name="next-steps"></a>Nästa steg

Läs mer om:
- [Översikt över Azure Machine Learning-experimentering](experimentation-service-configuration.md)
- [Azure Machine Learning Workbench experimentering service configuration-filer](experimentation-service-configuration-reference.md)
- [Apache Spark för Azure HDInsight-kluster](https://azure.microsoft.com/services/hdinsight/apache-spark/)
- [Virtuell dator för datavetenskap](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)
