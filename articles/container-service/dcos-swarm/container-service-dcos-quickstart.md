---
title: Azure Container Service Quickstart - distribuera DC/OS-kluster | Microsoft Docs
description: Azure Container Service Quickstart - distribuera DC/OS-klustret
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: Docker, Containers, Micro-services, Kubernetes, DC/OS, Azure
ms.assetid: 
ms.service: container-service
ms.devlang: azurecli
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/04/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 8070d224fe6281e61f67483d4f1dd905a2ab99eb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-a-dcos-cluster"></a>Distribuera ett DC/OS-kluster

DC/OS är en distribuerad plattform för moderna och körning av program. Med Azure Container Service är etablering av en produktion redo DC/OS-kluster snabbt och enkelt. Det här Snabbstart beskriver de grundläggande steg som krävs för att distribuera ett DC/OS kluster och kör grundläggande arbetsbelastning.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

För den här självstudien krävs Azure CLI-version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="log-in-to-azure"></a>Logga in på Azure 

Logga in på Azure-prenumerationen med kommandot [az login](/cli/azure/#login) och följ anvisningarna på skärmen.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#create). En Azure-resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras. 

I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-dcos-cluster"></a>Skapa DC/OS-klustret

Skapa ett DC/OS-kluster med den [az acs skapa](/cli/azure/acs#create) kommando.

I följande exempel skapas ett DC/OS-kluster med namnet *myDCOSCluster* och skapar SSH-nycklar, om de inte redan finns. Om du vill använda en specifik uppsättning nycklar använder du alternativet `--ssh-key-value`.  

```azurecli
az acs create --orchestrator-type dcos --resource-group myResourceGroup --name myDCOSCluster --generate-ssh-keys
```

I vissa fall, som vid en begränsad utvärderingsversion, har en Azure-prenumeration begränsad åtkomst till Azure-resurser. Om distributionen misslyckas på grund av begränsade tillgängliga kärnor minskar du antalet standardagenter genom att lägga till `--agent-count 1` till kommandot [az acs create](/cli/azure/acs#create). 

Om några minuter kommandot har slutförts och returnerar information om distributionen.

## <a name="connect-to-dcos-cluster"></a>Ansluta till DC/OS-kluster

När ett DC/OS-klustret har skapats, kan det vara åtkomst via en SSH-tunnel. Kör följande kommando för att returnera den offentliga IP-adressen för DC/OS-hanteraren. Den här IP-adressen lagras i en variabel och används i nästa steg.

```azurecli
ip=$(az network public-ip list --resource-group myResourceGroup --query "[?contains(name,'dcos-master')].[ipAddress]" -o tsv)
```

Kör följande kommando för att skapa SSH-tunnel, och följa den på skärmen instruktioner. Om port 80 är redan används, misslyckas kommer kommandot. Uppdatera tunnel port till en som inte används, exempelvis `85:localhost:80`. 

```azurecli
sudo ssh -i ~/.ssh/id_rsa -fNL 80:localhost:80 -p 2200 azureuser@$ip
```

SSH-tunnel kan testas genom att bläddra till `http://localhost`. Om en port andra att 80 har använts, justera platsen så att den matchar. 

Om SSH-tunnel har skapats, returneras DC/OS-portalen.

![DC/OS-GRÄNSSNITTET](./media/container-service-dcos-quickstart/dcos-ui.png)

## <a name="install-dcos-cli"></a>Installera DC/OS CLI

DC/OS-kommandoradsgränssnittet används för att hantera ett DC/OS-kluster från kommandoraden. Installera DC/OS cli med den [az installera acs-DC/OS-cli](/azure/acs/dcos#install-cli) kommando. Om du använder Azure CloudShell har DC/OS CLI redan installerats. 

Om du använder Azure CLI på macOS- eller Linux kan du behöva köra med sudo.

```azurecli
az acs dcos install-cli
```

Innan CLI kan användas med klustret, måste den konfigureras för att använda SSH-tunnel. Gör du genom att köra följande kommando, justera porten om det behövs.

```azurecli
dcos config set core.dcos_url http://localhost
```

## <a name="run-an-application"></a>Köra ett program

Standard schemaläggning mekanism för en ACS DC/OS-klustret är Marathon. Marathon används för att starta ett program och hantera status för programmet på DC/OS-klustret. Om du vill schemalägga ett program via Marathon, skapar du en fil med namnet *marathon app.json*, och kopiera följande innehåll till den. 

```json
{
  "id": "demo-app",
  "cmd": null,
  "cpus": 1,
  "mem": 32,
  "disk": 0,
  "instances": 1,
  "container": {
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        {
          "containerPort": 80,
          "hostPort": 80,
          "protocol": "tcp",
          "name": "80",
          "labels": null
        }
      ]
    },
    "type": "DOCKER"
  },
  "acceptedResourceRoles": [
    "slave_public"
  ]
}
```

Kör följande kommando för att schemalägga att programmet ska köras på DC/OS-klustret.

```azurecli
dcos marathon app add marathon-app.json
```

Kör följande kommando för att se distributionsstatusen för appen.

```azurecli
dcos marathon app list
```

När den **väntar på** kolumnvärde växlar från *SANT* till *FALSKT*, programdistribution har slutförts.

```azurecli
ID     MEM  CPUS  TASKS  HEALTH  DEPLOYMENT  WAITING  CONTAINER  CMD   
/test   32   1     1/1    ---       ---      False      DOCKER   None
```

Hämta offentlig IP-adressen för agenter för DC/OS-klustret.

```azurecli
az network public-ip list --resource-group myResourceGroup --query "[?contains(name,'dcos-agent')].[ipAddress]" -o tsv
```

Bläddra till den här adressen returnerar NGINX standardwebbplatsen.

![NGINX](./media/container-service-dcos-quickstart/nginx.png)

## <a name="delete-dcos-cluster"></a>Ta bort DC/OS-klustret

När du inte längre behövs kan du använda den [ta bort grupp az](/cli/azure/group#delete) kommando för att ta bort resursgruppen, DC/OS-kluster och alla relaterade resurser.

```azurecli
az group delete --name myResourceGroup --no-wait
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstartsguide du har distribuerat ett DC/OS-kluster och har kört en enkel dockerbehållare på klustret. Om du vill veta mer om Azure Container Service kan fortsätta att ACS-självstudier.

> [!div class="nextstepaction"]
> [Hantera en ACS DC/OS-klustret](container-service-dcos-manage-tutorial.md)