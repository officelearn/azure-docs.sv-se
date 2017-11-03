---
title: "Distribuera ElasticSearch på en virtuell dator för utveckling i Azure"
description: "Självstudiekurs – installera elastisk stacken på en utvecklings Linux VM i Azure"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: rloutlaw
manager: justhe
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 10/11/2017
ms.author: routlaw
ms.openlocfilehash: 5b0b51504478cc0d501a89760ccd60808a69ccbd
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2017
---
# <a name="install-the-elastic-stack-on-an-azure-vm"></a>Installera elastisk stacken på en virtuell dator i Azure

Den här artikeln guidar dig igenom hur du distribuerar [Elasticsearch](https://www.elastic.co/products/elasticsearch), [Logstash](https://www.elastic.co/products/logstash), och [Kibana](https://www.elastic.co/products/kibana), på en Ubuntu VM i Azure. Om du vill se elastisk stacken i praktiken kan eventuellt ansluta till Kibana och arbeta med vissa exemplet loggningsdata. 

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa en Ubuntu VM i en Azure-resursgrupp
> * Installera Elasticsearch, Logstash och Kibana på den virtuella datorn
> * Skicka exempeldata till Elasticsearch med Logstash 
> * Öppna portar och arbeta med data i konsolen Kibana


 Den här distributionen är lämplig för grundläggande utveckling med elastisk stacken. Mer information om elastisk stacken, inklusive rekommendationer för en produktionsmiljö, finns det [elastisk dokumentationen](https://www.elastic.co/guide/index.html) och [Azure arkitektur Center](/azure/architecture/elasticsearch/).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kursen krävs att du använder Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#create). En Azure-resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras. 

I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Skapa en virtuell dator med kommandot [az vm create](/cli/azure/vm#create). 

Följande exempel skapar en virtuell dator som heter *myVM*, och SSH-nycklar skapas om de inte redan finns på en standardnyckelplats. Om du vill använda en specifik uppsättning nycklar använder du alternativet `--ssh-key-value`.  

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

När den virtuella datorn har skapats visar Azure CLI information som ser ut ungefär som i följande exempel. Anteckna `publicIpAddress`. Den här adressen används för att få åtkomst till den virtuella datorn.

```azurecli-interactive 
{
  "fqdns": "",
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="ssh-into-your-vm"></a>SSH till den virtuella datorn

Om du inte redan vet offentliga IP-adressen för den virtuella datorn kan köra den [az offentliga ip-lista över](/cli/azure/network/public-ip#list) kommando:

```azurecli-interactive
az network public-ip list --resource-group myResourceGroup --query [].ipAddress
```

Använd följande kommando för att skapa en SSH-session med den virtuella datorn. Ersätt rätt offentliga IP-adressen för den virtuella datorn. I det här exemplet IP-adressen är *40.68.254.142*.

```bash
ssh azureuser@40.68.254.142
```

## <a name="install-the-elastic-stack"></a>Installera elastisk Stack

Importera Elasticsearch signeringsnyckeln och uppdatera din LGH källor lista om du vill inkludera elastisk paketdatabasen:

```bash
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
echo "deb https://artifacts.elastic.co/packages/5.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-5.x.list
```

Installera Java-virtuella på den virtuella datorn och konfigurera JAVA_HOME den här variabeln är nödvändigt för elastiska Stack-komponenter körs.

```bash
sudo apt update && sudo apt install openjdk-8-jre-headless
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
```

Kör följande kommandon för att uppdatera Ubuntu paketet källor och installera Elasticsearch, Kibana och Logstash.

```bash
sudo apt update && sudo apt install elasticsearch kibana logstash   
```

> [!NOTE]
> Detaljerade installationsanvisningar, inklusive directory layouter och den inledande konfigurationen underhålls i [Elastisks dokumentation](https://www.elastic.co/guide/en/elastic-stack/current/installing-elastic-stack.html)

## <a name="start-elasticsearch"></a>Starta Elasticsearch 

Starta Elasticsearch på den virtuella datorn med följande kommando:

```bash
sudo systemctl start elasticsearch.service
```

Det här kommandot genererar inga utdata, så kontrollera att Elasticsearch körs på den virtuella datorn med den här `curl` kommando:

```bash
curl -XGET 'localhost:9200/'
```

Om Elasticsearch körs kan se du utdata som liknar följande:

```json
{
  "name" : "w6Z4NwR",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "SDzCajBoSK2EkXmHvJVaDQ",
  "version" : {
    "number" : "5.6.3",
    "build_hash" : "1a2f265",
    "build_date" : "2017-10-06T20:33:39.012Z",
    "build_snapshot" : false,
    "lucene_version" : "6.6.1"
  },
  "tagline" : "You Know, for Search"
}
```

## <a name="start-logstash-and-add-data-to-elasticsearch"></a>Starta Logstash och lägga till data i Elasticsearch

Starta Logstash med följande kommando:

```bash 
sudo systemctl start logstash.service
```

Testa Logstash i interaktivt läge så att den fungerar korrekt:

```bash
sudo /usr/share/logstash/bin/logstash -e 'input { stdin { } } output { stdout {} }'
```

Detta är en grundläggande logstash [pipeline](https://www.elastic.co/guide/en/logstash/5.6/pipeline.html) som ekon standardindata till standardutdata. 

```output
The stdin plugin is now waiting for input:
hello azure
2017-10-11T20:01:08.904Z myVM hello azure
```

Ställ in Logstash kan vidarebefordra kernelmeddelanden från den här virtuella datorn till Elasticsearch. Skapa en ny fil i en tom katalog som kallas `vm-syslog-logstash.conf` och klistra in följande Logstash konfiguration:

```Logstash
input {
    stdin {
        type => "stdin-type"
    }

    file {
        type => "syslog"
        path => [ "/var/log/*.log", "/var/log/*/*.log", "/var/log/messages", "/var/log/syslog" ]
        start_position => "beginning"
    }
}

output {

    stdout {
        codec => rubydebug
    }
    elasticsearch {
        hosts  => "localhost:9200"
    }
}
```

Testa den här konfigurationen och skicka syslog-data till Elasticsearch:

```bash
sudo /usr/share/logstash/bin/logstash -f vm-syslog-logstash.conf
```

Du kan se sysloggposter i terminalen eko när de skickas till Elasticsearch. Använd `CTRL+C` avsluta utanför Logstash när du har skickat data.

## <a name="start-kibana-and-visualize-the-data-in-elasticsearch"></a>Starta Kibana och visualisera data i Elasticsearch

Redigera `/etc/kibana/kibana.yml` och ändra IP-adressen Kibana lyssnar på, så du kan komma åt den från din webbläsare.

```bash
server.host:"0.0.0.0"
```

Starta Kibana med följande kommando:

```bash
sudo systemctl start kibana.service
```

Öppna port 5601 från Azure CLI för att tillåta fjärråtkomst till konsolen Kibana:

```azurecli-interactive
az vm open-port --port 5601 --resource-group myResourceGroup --name myVM
```

Öppna konsolen Kibana och välj **skapa** att generera ett Standardindex baserat på syslog-data som du skickade till Elasticsearch tidigare. 

![Bläddra Syslog-händelser i Kibana](media/elasticsearch-install/kibana-index.png)

Välj **identifiera** Bläddra på konsolen Kibana att söka och filtrera genom syslog-händelser.

![Bläddra Syslog-händelser i Kibana](media/elasticsearch-install/kibana-search-filter.png)

## <a name="next-steps"></a>Nästa steg

I kursen får distribuerat du elastisk stacken till en virtuell dator i Azure-utveckling. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa en Ubuntu VM i en Azure-resursgrupp
> * Installera Elasticsearch, Logstash och Kibana på den virtuella datorn
> * Skicka exempeldata till Elasticsearch från Logstash 
> * Öppna portar och arbeta med data i konsolen Kibana