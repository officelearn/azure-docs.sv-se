---
title: (INAKTUELL) Snabbstart för Azure Container Service – distribuera DC/OS-kluster
description: Snabbstart för Azure Container Service – distribuera DC/OS-kluster
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: quickstart
ms.date: 02/26/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: efaf82c3f378f572c289b587dbe5df1923a58c62
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "53000524"
---
# <a name="deprecated-deploy-a-dcos-cluster"></a>(INAKTUELL) Distribuera ett DC/OS-kluster

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

DC/OS tillhandahåller en distribuerad plattform för att köra moderna och containerbaserade program. Med Azure Container Service går det snabbt och enkelt att etablera ett produktionsklart DC/OS-kluster. Den här snabbstarten beskriver de grundläggande stegen för att distribuera ett DC/OS-kluster och köra en grundläggande arbetsbelastning.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

För den här självstudien krävs Azure CLI-version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="log-in-to-azure"></a>Logga in på Azure 

Logga in på Azure-prenumerationen med kommandot [az login](/cli/azure/reference-index#az-login) och följ anvisningarna på skärmen.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#az-group-create). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. 

I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-dcos-cluster"></a>Skapa DC/OS-kluster

Skapa ett DC/OS-kluster med kommandot [az acs create](/cli/azure/acs#az-acs-create).

Följande exempel skapar ett DC/OS-kluster som heter *myDCOSCluster* och SSH-nycklar om de inte redan finns. Om du vill använda en specifik uppsättning nycklar använder du alternativet `--ssh-key-value`.  

```azurecli
az acs create --orchestrator-type dcos --resource-group myResourceGroup --name myDCOSCluster --generate-ssh-keys
```

I vissa fall, som vid en begränsad utvärderingsversion, har en Azure-prenumeration begränsad åtkomst till Azure-resurser. Om distributionen misslyckas på grund av begränsade tillgängliga kärnor minskar du antalet standardagenter genom att lägga till `--agent-count 1` till kommandot [az acs create](/cli/azure/acs#az-acs-create). 

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

SSH-tunneln kan testas genom att gå till `http://localhost`. Om någon annan port än 80 har använts ändrar du platsen efter detta. 

DC/OS-portalen returneras om SSH-tunneln har skapats.

![DC/OS-gränssnitt](./media/container-service-dcos-quickstart/dcos-ui.png)

## <a name="install-dcos-cli"></a>Installera DC/OS CLI

DC/OS-kommandoradsgränssnittet används för att hantera ett DC/OS-kluster från kommandoraden. Installera DC/OS cli med kommandot [az acs dcos install-cli](/cli/azure/acs/dcos#az-acs-dcos-install-cli). Om du använder Azure CloudShell är DC/OS CLI redan installerat. 

Om du använder Azure CLI på macOS eller Linux kan du behöva köra kommandot med sudo.

```azurecli
az acs dcos install-cli
```

Innan CLI kan användas med klustret måste det konfigureras så att det använder SSH-tunneln. Det gör du genom att köra följande kommando och justera porten efter behov.

```azurecli
dcos config set core.dcos_url http://localhost
```

## <a name="run-an-application"></a>Kör ett program

Standardmekanismen för schemaläggning av ett ACS DC/OS-kluster är Marathon. Marathon används för att starta ett program och hantera programmets tillstånd på DC/OS-klustret. Om du vill schemalägga ett program via Marathon skapar du en fil med namnet *marathon-app.json* och kopierar följande innehåll till den. 

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

När kolumnvärdet **VÄNTAR** ändras från *Sant* till *Falskt* har programdistributionen slutförts.

```azurecli
ID     MEM  CPUS  TASKS  HEALTH  DEPLOYMENT  WAITING  CONTAINER  CMD   
/test   32   1     1/1    ---       ---      False      DOCKER   None
```

Hämta den offentliga IP-adressen för DC/OS-klusteragenterna.

```azurecli
az network public-ip list --resource-group myResourceGroup --query "[?contains(name,'dcos-agent')].[ipAddress]" -o tsv
```

Om du bläddrar till den här adressen returneras NGINX-standardplatsen.

![NGINX](./media/container-service-dcos-quickstart/nginx.png)

## <a name="delete-dcos-cluster"></a>Ta bort DC/OS-kluster

När den inte längre behövs kan du använda kommandot [az group delete](/cli/azure/group#az-group-delete) för att ta bort resursgruppen, DC/OS-klustret och alla relaterade resurser.

```azurecli
az group delete --name myResourceGroup --no-wait
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du distribuerat ett DC/OS-kluster och har kört en enkel Docker-container i klustret. Om du vill veta mer om Azure Container Service kan du fortsätta till självstudiekurserna om ACS.

> [!div class="nextstepaction"]
> [Hantera ett ACS DC/OS-kluster](container-service-dcos-manage-tutorial.md)