---
title: "Självstudiekurs för Azure Container Service - hantera DC/OS"
description: "Självstudiekurs för Azure Container Service - hantera DC/OS"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 07/17/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 0c58bd764cf0fdacd55675f8343c6e7481a11823
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2017
---
# <a name="azure-container-service-tutorial---manage-dcos"></a>Självstudiekurs för Azure Container Service - hantera DC/OS

DC/OS är en distribuerad plattform för moderna och körning av program. Med Azure Container Service är etablering av en produktion redo DC/OS-kluster snabbt och enkelt. Den här snabbstartsguide information grundläggande steg som behövs för att distribuera ett DC/OS-klustret och köra grundläggande arbetsbelastning.

> [!div class="checklist"]
> * Skapa ett ACS DC/OS-kluster
> * Anslut till klustret
> * Installera DC/OS CLI
> * Distribuera ett program i klustret
> * Skala ett program i klustret
> * Skala noderna i DC/OS
> * Grundläggande DC/OS-hantering
> * Ta bort DC/OS-klustret

För den här självstudien krävs Azure CLI-version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="create-dcos-cluster"></a>Skapa DC/OS-klustret

Börja med att skapa en resursgrupp med det [az gruppen skapa](/cli/azure/group#create) kommando. En Azure-resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras. 

I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *westeurope*.

```azurecli
az group create --name myResourceGroup --location westeurope
```

Skapa sedan ett DC/OS-kluster med den [az acs skapa](/cli/azure/acs#create) kommando.

I följande exempel skapas ett DC/OS-kluster med namnet *myDCOSCluster* och skapar SSH-nycklar, om de inte redan finns. Om du vill använda en specifik uppsättning nycklar använder du alternativet `--ssh-key-value`.  

```azurecli
az acs create \
  --orchestrator-type dcos \
  --resource-group myResourceGroup \
  --name myDCOSCluster \
  --generate-ssh-keys
```

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

## <a name="install-dcos-cli"></a>Installera DC/OS CLI

Installera DC/OS cli med den [az installera acs-DC/OS-cli](/azure/acs/dcos#install-cli) kommando. Om du använder Azure CloudShell har DC/OS CLI redan installerats. Om du använder Azure CLI på macOS- eller Linux kan du behöva köra med sudo.

```azurecli
az acs dcos install-cli
```

Innan CLI kan användas med klustret, måste den konfigureras för att använda SSH-tunnel. Gör du genom att köra följande kommando, justera porten om det behövs.

```azurecli
dcos config set core.dcos_url http://localhost
```

## <a name="run-an-application"></a>Köra ett program

Standard schemaläggning mekanism för en ACS DC/OS-klustret är Marathon. Marathon används för att starta ett program och hantera status för programmet på DC/OS-klustret. Om du vill schemalägga ett program via Marathon, skapar du en fil med namnet **marathon app.json**, och kopiera följande innehåll till den. 

```json
{
  "id": "demo-app-private",
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
          "protocol": "tcp",
          "name": "80",
          "labels": null
        }
      ]
    },
    "type": "DOCKER"
  }
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

När den **uppgifter** kolumnvärde växlar från *0/1* till *1/1*, programdistribution har slutförts.

```azurecli
ID     MEM  CPUS  TASKS  HEALTH  DEPLOYMENT  WAITING  CONTAINER  CMD   
/test   32   1     0/1    ---       ---      False      DOCKER   None
```

## <a name="scale-marathon-application"></a>Skala Marathon program

En enda instans-programmet skapades i föregående exempel. Om du vill uppdatera den här distributionen så att det finns tre instanser av programmet, öppna den **marathon app.json** filen och uppdatera instance-egenskap till 3.

```json
{
  "id": "demo-app-private",
  "cmd": null,
  "cpus": 1,
  "mem": 32,
  "disk": 0,
  "instances": 3,
  "container": {
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        {
          "containerPort": 80,
          "protocol": "tcp",
          "name": "80",
          "labels": null
        }
      ]
    },
    "type": "DOCKER"
  }
}
```

Uppdatera program med hjälp av den `dcos marathon app update` kommando.

```azurecli
dcos marathon app update demo-app-private < marathon-app.json
```

Kör följande kommando för att se distributionsstatusen för appen.

```azurecli
dcos marathon app list
```

När den **uppgifter** kolumnvärde växlar från *1/3* till *3-1*, programdistribution har slutförts.

```azurecli
ID     MEM  CPUS  TASKS  HEALTH  DEPLOYMENT  WAITING  CONTAINER  CMD   
/test   32   1     1/3    ---       ---      False      DOCKER   None
```

## <a name="run-internet-accessible-app"></a>Kör internet tillgänglig app

ACS DC/OS-klustret består av två noder uppsättningar, en offentlig som är tillgänglig på internet och en privat som inte är tillgänglig på internet. Standardinställningen är privat noder som användes i det sista exemplet.

Om du vill att ett program tillgängliga på internet kan du distribuera dem till noduppsättningen offentliga. Det gör du genom att ge den `acceptedResourceRoles` objekt värdet `slave_public`.

Skapa en fil med namnet **nginx public.json** och kopiera följande innehåll till den.

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
dcos marathon app add nginx-public.json
```

Hämta offentlig IP-adressen för DC/OS offentliga klustret agenter.

```azurecli 
az network public-ip list --resource-group myResourceGroup --query "[?contains(name,'dcos-agent')].[ipAddress]" -o tsv
```

Bläddra till den här adressen returnerar NGINX standardwebbplatsen.

![NGINX](./media/container-service-dcos-manage-tutorial/nginx.png)

## <a name="scale-dcos-cluster"></a>Skala DC/OS-klustret

I föregående exempel kan har ett program skalas till flera instanser. DC/OS-infrastruktur kan också skalas för att ge mer eller mindre beräkningskapacitet. Detta görs med den [az acs skala]() kommando. 

Aktuellt antal DC/OS-agenterna, Använd den [az acs visa](/cli/azure/acs#show) kommando.

```azurecli
az acs show --resource-group myResourceGroup --name myDCOSCluster --query "agentPoolProfiles[0].count"
```

Om du vill öka antalet till 5, Använd den [az acs skala](/cli/azure/acs#scale) kommando. 

```azurecli
az acs scale --resource-group myResourceGroup --name myDCOSCluster --new-agent-count 5
```

## <a name="delete-dcos-cluster"></a>Ta bort DC/OS-klustret

När du inte längre behövs kan du använda den [ta bort grupp az](/cli/azure/group#delete) kommando för att ta bort resursgruppen, DC/OS-kluster och alla relaterade resurser.

```azurecli 
az group delete --name myResourceGroup --no-wait
```

## <a name="next-steps"></a>Nästa steg

I den här kursen har du lärt dig om grundläggande DC/OS-hanteringsaktivitet, inklusive följande. 

> [!div class="checklist"]
> * Skapa ett ACS DC/OS-kluster
> * Anslut till klustret
> * Installera DC/OS CLI
> * Distribuera ett program i klustret
> * Skala ett program i klustret
> * Skala noderna i DC/OS
> * Ta bort DC/OS-klustret

Gå vidare till nästa kurs vill veta mer om belastningsutjämning programmet i DC/OS på Azure. 

> [!div class="nextstepaction"]
> [Belastningsutjämna program](container-service-load-balancing.md)