---
title: (INAKTUELL) Självstudie för Azure Container Service – Hantera DC/OS
description: Självstudie för Azure Container Service – Hantera DC/OS
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 02/26/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: fe943ae5ac7894cdd8d8e104615cea670513b7eb
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "53000425"
---
# <a name="deprecated-azure-container-service-tutorial---manage-dcos"></a>(INAKTUELL) Självstudie för Azure Container Service – Hantera DC/OS

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

DC/OS tillhandahåller en distribuerad plattform för att köra moderna och containerbaserade program. Med Azure Container Service går det snabbt och enkelt att etablera ett produktionsklart DC/OS-kluster. I den här snabbstarten beskrivs de grundläggande stegen för att distribuera ett DC/OS-kluster och köra en grundläggande arbetsbelastning.

> [!div class="checklist"]
> * Skapa ett ACS DC/OS-kluster
> * Anslut till klustret
> * Installera DC/OS CLI
> * Distribuera ett program till klustret
> * Skala ut ett program i klustret
> * Skala ut DC/OS-klusternoderna
> * Grundläggande DC/OS-hantering
> * Ta bort DC/OS-klustret

För den här självstudien krävs Azure CLI-version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="create-dcos-cluster"></a>Skapa DC/OS-kluster

Skapa först en resursgrupp med kommandot [az group create](/cli/azure/group#az-group-create). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. 

I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *westeurope*.

```azurecli
az group create --name myResourceGroup --location westeurope
```

Skapa sedan ett DC/OS-kluster med kommandot [az acs create](/cli/azure/acs#az-acs-create).

Följande exempel skapar ett DC/OS-kluster som heter *myDCOSCluster* och SSH-nycklar om de inte redan finns. Om du vill använda en specifik uppsättning nycklar använder du alternativet `--ssh-key-value`.  

```azurecli
az acs create \
  --orchestrator-type dcos \
  --resource-group myResourceGroup \
  --name myDCOSCluster \
  --generate-ssh-keys
```

Om några minuter har kommandot slutförts och returnerar information om distributionen.

## <a name="connect-to-dcos-cluster"></a>Ansluta till DC/OS-kluster

När ett DC/OS-kluster har skapats går det att komma åt via en SSH-tunnel. Kör följande kommando för att returnera den offentliga IP-adressen för DC/OS-mastern. Den här IP-adressen lagras i en variabel och används i nästa steg.

```azurecli
ip=$(az network public-ip list --resource-group myResourceGroup --query "[?contains(name,'dcos-master')].[ipAddress]" -o tsv)
```

Kör följande kommando för att skapa SSH-tunneln och följ anvisningarna på skärmen. Kommandot misslyckas om port 80 redan används. Uppdatera tunnelporten till en port som inte används, t.ex. `85:localhost:80`. 

```azurecli
sudo ssh -i ~/.ssh/id_rsa -fNL 80:localhost:80 -p 2200 azureuser@$ip
```

## <a name="install-dcos-cli"></a>Installera DC/OS CLI

Installera DC/OS cli med kommandot [az acs dcos install-cli](/cli/azure/acs/dcos#az-acs-dcos-install-cli). Om du använder Azure CloudShell är DC/OS CLI redan installerat. Om du använder Azure CLI på macOS eller Linux kan du behöva köra kommandot med sudo.

```azurecli
az acs dcos install-cli
```

Innan CLI kan användas med klustret måste det konfigureras så att det använder SSH-tunneln. Det gör du genom att köra följande kommando och justera porten efter behov.

```azurecli
dcos config set core.dcos_url http://localhost
```

## <a name="run-an-application"></a>Kör ett program

Standardmekanismen för schemaläggning av ett ACS DC/OS-kluster är Marathon. Marathon används för att starta ett program och hantera programmets tillstånd på DC/OS-klustret. Om du vill schemalägga ett program via Marathon skapar du en fil med namnet **marathon-app.json** och kopierar följande innehåll till den. 

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

När kolumnvärdet **TASKS** ändras från *0/1* till *1/1* har programdistributionen slutförts.

```azurecli
ID     MEM  CPUS  TASKS  HEALTH  DEPLOYMENT  WAITING  CONTAINER  CMD   
/test   32   1     0/1    ---       ---      False      DOCKER   None
```

## <a name="scale-marathon-application"></a>Skala ut Marathon-programmet

En enda instans av programmet skapades i föregående exempel. Om du vill uppdatera den här distributionen så att det finns tre instanser av programmet öppnar du filen **marathon-app.json** och uppdaterar instance-egenskapen till 3.

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

Uppdatera programmet med kommandot `dcos marathon app update`.

```azurecli
dcos marathon app update demo-app-private < marathon-app.json
```

Kör följande kommando för att se distributionsstatusen för appen.

```azurecli
dcos marathon app list
```

När kolumnvärdet **TASKS** ändras från *1/3* till *3/1* har programdistributionen slutförts.

```azurecli
ID     MEM  CPUS  TASKS  HEALTH  DEPLOYMENT  WAITING  CONTAINER  CMD   
/test   32   1     1/3    ---       ---      False      DOCKER   None
```

## <a name="run-internet-accessible-app"></a>Kör appen som är tillgänglig från internet

ACS DC/OS-klustret består av två noduppsättningar, en offentlig som är tillgänglig på internet och en privat som inte är tillgänglig på internet. Standarduppsättningen är de privata noderna som användes i föregående exempel.

Om du vill göra ett program tillgängligt på internet kan du distribuera det till den offentliga noduppsättningen. Det gör du genom att ge `acceptedResourceRoles`-objektet värdet `slave_public`.

Skapa en fil med namnet **nginx-public.json** och kopiera följande innehåll till den.

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

Hämta den offentliga IP-adressen för de offentliga DC/OS-klusteragenterna.

```azurecli 
az network public-ip list --resource-group myResourceGroup --query "[?contains(name,'dcos-agent')].[ipAddress]" -o tsv
```

Om du bläddrar till den här adressen returneras NGINX-standardplatsen.

![NGINX](./media/container-service-dcos-manage-tutorial/nginx.png)

## <a name="scale-dcos-cluster"></a>Skala ut DC/OS-klustret

I föregående exempel skalades ett program ut till flera instanser. DC/OS-infrastrukturen kan också skalas ut för att tillhandahålla mer eller mindre beräkningskapacitet. Det gör du med kommandot [az acs scale](/cli/azure/acs#az-acs-scale). 

Om du vill se aktuellt antal DC/OS-agenter använder du kommandot [az acs show](/cli/azure/acs#az-acs-show).

```azurecli
az acs show --resource-group myResourceGroup --name myDCOSCluster --query "agentPoolProfiles[0].count"
```

Om du vill öka antalet till 5 använder du kommandot [az acs scale](/cli/azure/acs#az-acs-scale). 

```azurecli
az acs scale --resource-group myResourceGroup --name myDCOSCluster --new-agent-count 5
```

## <a name="delete-dcos-cluster"></a>Ta bort DC/OS-kluster

När den inte längre behövs kan du använda kommandot [az group delete](/cli/azure/group#az-group-delete) för att ta bort resursgruppen, DC/OS-klustret och alla relaterade resurser.

```azurecli 
az group delete --name myResourceGroup --no-wait
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig om grundläggande DC/OS-hanteringsuppgifter, bland annat följande: 

> [!div class="checklist"]
> * Skapa ett ACS DC/OS-kluster
> * Anslut till klustret
> * Installera DC/OS CLI
> * Distribuera ett program till klustret
> * Skala ut ett program i klustret
> * Skala ut DC/OS-klusternoderna
> * Ta bort DC/OS-klustret

Gå vidare till nästa självstudie om du vill lära dig om belastningsutjämning för program i DC/OS på Azure. 

> [!div class="nextstepaction"]
> [Belastningsutjämna program](container-service-load-balancing.md)
