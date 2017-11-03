---
title: "Hur du skapar DSVM och HDI som beräkna mål för Azure ML"
description: "Skapa DSVM och HDI Spark-kluster som compute mål för Azure ML experiment."
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/26/2017
ms.openlocfilehash: 9ce1d32a2785bec1164d2a89dea9946fe113cb33
ms.sourcegitcommit: 3e3a5e01a5629e017de2289a6abebbb798cec736
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/27/2017
---
# <a name="create-dsvm-and-hdi-spark-cluster-as-compute-targets"></a>Skapa DSVM och HDI Spark-kluster som compute mål

Du kan enkelt skala upp eller ut machine learning-experiment genom att lägga till ytterligare beräknings-mål, till exempel Ubuntu-baserade DSVM (datavetenskap virtuell dator) och Apache Spark för Azure HDInsight-kluster. Den här artikeln får du hjälp med att skapa dessa compute mål i Azure. Mer information om Azure ML beräkning mål avser [översikt över Azure Machine Learning-experiment tjänsten](experimentation-service-configuration.md).

>[!NOTE]
>Du måste kontrollera att du har rätt behörighet för att skapa resurser, till exempel virtuell dator och HDI-kluster i Azure innan du fortsätter. Båda dessa resurser kan också använda många beräkning kärnor beroende på din konfiguration. Kontrollera att din prenumeration har tillräckligt med kapacitet för de virtuella CPU-kärnorna. Du kan alltid få kontakt med Azure-supporten att öka det maximala antalet kärnor i prenumerationen.

## <a name="create-an-ubuntu-dsvm-in-azure-portal"></a>Skapa en Ubuntu DSVM i Azure-portalen

Du kan skapa en DSVM från Azure-portalen. 

1. Logga in på Azure-portalen från https://portal.azure.com
2. Klicka på den **+ ny** länk, och Sök efter ”datavetenskap virtuell dator för Linux”.
    ![Ubuntu](media/how-to-create-dsvm-hdi/ubuntu_dsvm.png)
4. Välj **datavetenskap virtuell dator för Linux (Ubuntu)** i listan och följ de på skärmen instruktioner för att skapa DSVM.

>[!IMPORTANT]
>Se till att välja **lösenord** som den _autentiseringstyp_.

![Använd pwd](media/how-to-create-dsvm-hdi/use_pwd.png)

## <a name="create-an-ubuntu-dsvm-using-azure-cli"></a>Skapa en Ubuntu DSVM med hjälp av azure cli

Du kan också använda en mall för Azure-resurs management för att distribuera en DSVM.

>[!NOTE]
>Alla följande kommandon antas vara utfärdat från rotmappen för ett Azure ML-projekt.

Skapa först en `mydsvm.json` filen med hjälp av valfri textredigerare i den `docs` mapp. (Om du inte har en `docs` mapp i rotmappen projekt skapar du en.) Vi använder den här filen för att konfigurera några grundläggande parametrar för management-mall för Azure-resurs. 

Kopiera och klistra in JSON följande kodavsnitt i den `mydsvm.json` filen och Fyll i lämpliga värden:

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

För den _vmSize_ fältet som du kan använda alla de stöds VM-storlek som anges i den [Ubuntu DSVM Azure resource management-mallen](https://github.com/Azure/DataScienceVM/blob/master/Scripts/CreateDSVM/Ubuntu/multiazuredeploywithext.json). Vi rekommenderar att du använder någon av de nedan storlekar som beräkna mål för Azure ML. 

- Standard_DS2_v2 
- Standard_DS3_v2 
- Standard_DS4_v2 
- Standard_DS12_v2 
- Standard_DS13_v2 
- Standard_DS14_v2 
- Standard_NC6 
- Standard_NC12 
- Standard_NC24 
 
>[!TIP]
> Storlek på Virtuella datorer igång med ”NC” är de utrustade med GPU.

Läs mer om dessa [storlekar för virtuella Linux-datorer i Azure](../../virtual-machines/linux/sizes.md) och deras [prisinformation](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

Öppna CLI fönster från Azure ML-arbetsstationen appen genom att klicka på **filen** --> **öppnar du kommandotolken**, eller **öppna PowerShell** menyalternativet. 

>[!NOTE]
>Du kan också göra detta i en kommandoradsmiljö där du har installerat az-cli.

I fönstret för att ange den nedan kommandon:

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
## <a name="attach-a-dsvm-compute-target"></a>Koppla ett DSVM beräknings-mål
När du har skapat DSVM kan nu du koppla den till din Azure ML-projekt.

```azurecli
# attach the DSVM compute target
# it is a good idea to use FQDN in case the IP address changes after you deallocate the VM and restart it
$ az ml computetarget attach --name <compute target name> --address <ip address or FQDN> --username <admin username> --password <admin password> --type remotedocker

# prepare the Docker image on the DSVM 
$ az ml experiment prepare -c <compute target name>
```
Nu bör du vara redo att köra experiment på den här DSVM.

## <a name="deallocate-a-dsvm-and-restart-it-later"></a>Frigöra en DSVM och starta om senare
När du är klar beräknings-uppgifter från Azure ML frigör du DSVM. Detta stänger av den virtuella datorn startar om beräkningsresurserna, men den bevarar de virtuella diskarna. Du debiteras inte för beräkning kostnaden när den virtuella datorn har frigjorts.

Att frigöra en virtuell dator:

```azurecli
$ az vm deallocate -g <resource group name> -n <vm name>
```

Om du vill ge den virtuella datorn tillbaka liv, använda den `az ml start` kommando:

```azurecli
$ az vm start -g <resource group name> -n <vm name>
```

## <a name="expand-the-dsvm-os-disk"></a>Expandera DSVM OS-disk
Linux VM i Azure kommer vanligtvis med en 30 GB för operativsystemets disk. När det används som beräkna mål för Azure ML, den både passar snabbt genom att dra nedåt Docker-bilder och bygga conda lager ovanpå det Docker-motorn. Det är en bra idé att expandera OS-disken till en större storlek (till exempel 200 GB) för att undvika fel ”disken är full” när du är mitt i en körning. Referens [så att utöka virtuella hårddiskar på en Linux-VM med Azure CLI](../../virtual-machines/linux/expand-disks.md) att lära dig hur du gör det enkelt från azure cli. 

## <a name="create-an-apache-spark-for-azure-hdinsight-cluster-in-azure-portal"></a>Skapa ett Apache Spark i Azure HDInsight-kluster i Azure-portalen

Om du vill köra skalbar Spark jobb, måste du skapa ett Apache Spark i Azure HDInsight-kluster i Azure-portalen.

1. Logga in på Azure-portalen från https://portal.azure.com
2. Klicka på den **+ ny** länk, och Sök efter ”HDInsight”.

    ![hitta hdi](media/how-to-create-dsvm-hdi/hdi.png)
    
3. Välj **HDInsight** i listan och klicka på den **skapa** knappen.
4. I den **grunderna** skärm för konfiguration av **kluster typen** inställningar, se till att välja **Spark** som den _kluster typen_, **Linux** som den _operativsystemet_, och **Spark 2.1.0 (HDI 3,6)** som _Version.

    ![Konfigurera hdi](media/how-to-create-dsvm-hdi/configure_hdi.png)

    >[!IMPORTANT]
    >Meddelande i skärmbilden ovan klustret har en _klustret inloggning användarnamn_ fältet och en _SSH (Secure Shell) användarnamn_ fältet. Det här är två olika användaridentiteter trots att du kan ange samma lösenord för båda inloggningar i informationssyfte. Den _klustret inloggning användarnamn_ används för att logga in på management webbgränssnittet för HDI-klustret. Den _SSH-inloggning användarnamn_ används för att logga in till huvudnod i klustret, och det här är vad som behövs för Azure ML att skicka Spark jobb.

5. Välj klusterstorleken och nodstorlek du behöver och slutför guiden Skapa. Det kan ta upp till 30 minuter för att klustret ska Slutför etablering. 

## <a name="attach-an-hdi-spark-cluster-compute-target"></a>Koppla ett HDI Spark-kluster beräknings-mål

När Spark HDI-klustret har skapats kan koppla du nu den till din Azure ML-projekt.

```azurecli
# attach the HDI compute target
$ az ml computetarget attach --name <compute target name> --address <cluster name, such as myhdicluster123.azurehdinsight.net> --username <ssh username> --password <ssh password> --type cluster

# prepare the conda environment on HDI
$ az ml experiment prepare -c <compute target name>
```
Nu bör du vara redo att köra experiment på Spark-kluster.

## <a name="next-steps"></a>Nästa steg

Läs mer om:
- [Översikt över tjänsten för Azure Machine Learning-experiment](experimentation-service-configuration.md)
- [Azure Machine Learning arbetsstationen experiment service configuration-filer](experimentation-service-configuration-reference.md)
- [Apache Spark i Azure HDInsight-kluster](https://azure.microsoft.com/services/hdinsight/apache-spark/)
- [Datavetenskap virtuell dator](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)
