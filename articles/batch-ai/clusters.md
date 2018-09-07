---
title: Fungerar med Azure Batch AI-kluster | Microsoft Docs
description: Hur du väljer en Batch AI-klusterkonfiguration och skapa och hantera AI-kluster
services: batch-ai
documentationcenter: ''
author: johnwu10
manager: jeconnoc
editor: ''
ms.service: batch-ai
ms.topic: article
ms.date: 08/14/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 61294d8b6b84b03b1e0c8d79b4d2855452c7f0e6
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44057231"
---
# <a name="work-with-batch-ai-clusters"></a>Arbeta med Batch AI-kluster 

Den här artikeln förklarar hur du arbetar med i Azure Batch AI-kluster. Det introducerar konceptet för kluster, vilka typer av konfigurationer som är möjliga och exempel. De flesta av de exempel som att skapa och hantera ett kluster i den här artikeln använder Azure CLI. Du kan dock använda andra verktyg som Azure-portalen och Azure Batch AI-SDK: er för att arbeta med kluster.

Ett Batch AI-kluster är en av flera resurser i tjänsten. Se den [översikt över Batch AI resurser](resource-concepts.md) att förstå vilka kluster i tjänsten.

## <a name="introduction-to-clusters"></a>Introduktion till kluster

Ett Batch AI-kluster innehåller beräkningsresurser för att köra machine learning och AI-utbildningsjobb. Alla noder i ett kluster har samma storlek och OS-avbildning. Batch AI har du många alternativ för att skapa kluster som är anpassade efter olika behov. Normalt kan ställa du in ett annat kluster för varje kategori av processorkraft som krävs för att slutföra ett projekt. Du kan skala antalet noder i ett kluster upp och ned baserat på begäran och budget. Kluster kan etableras och delas mellan ett team eller var enskilda användare kan etablera sina egna kluster. 

Varje kluster som finns under en Batch AI-resurs som kallas en *arbetsytan*. Du måste ha en Batch AI-arbetsyta som ställer in innan du etablerar ett kluster. Till exempel om du använder Azure CLI kan använda den [skapa az batchai arbetsytan](/cli/azure/batchai/workspace?view=azure-cli-latest#az-batchai-workspace-create) kommando för att ställa in en arbetsyta. 

När du skapar ett kluster kan skicka du jobb en i taget till en kö. Batch AI hanterar sedan allokeringsprocessen resurs för att köra jobb på klustret. 

## <a name="cluster-configuration-options"></a>Konfigurationsalternativ för kluster

När du planerar ett kluster måste du först bestämma dina beräkningskrav. Batch AI erbjuder flexibla konfigurationsalternativen så att du kan anpassa ett kluster efter dina behov. Här följer majors-alternativ att överväga när du etablerar ett kluster:

* **VM-storlek** -välja vilken [VM-storlek](../virtual-machines/linux/sizes.md) som finns tillgänglig i en [region som stöds](https://azure.microsoft.com/global-infrastructure/services/) för klusternoderna. Varje kluster endast kan innehålla en storlek på virtuell dator, så om din uppgifter kräver flera typer av virtuella datorer, måste du etablera ett separat kluster för varje typ av beräkning krav. För att träna machine learning eller AI-modeller som utvecklats med ramverk som drar nytta av GPU: er, se den [GPU för VM-storlekar optimerade](../virtual-machines/linux/sizes-gpu.md) i Azure.
  
* **VM-prioritet** -Batch AI tillhandahåller dedikerade eller lågprioriterade virtuella datorer i ett kluster. Dedikerade virtuella datorer är reserverade för användning i klustret. Alternativet med låg prioritet allokerar oanvända Azure VM-kapaciteten på betydande kostnadsbesparingar i utbyte mot risken för jobb som återtas om Azure frigör de virtuella datorerna. Jobb som körs längre än 24 timmar på en VM med låg prioritet är också automatiskt återtas. Om budget är ett problem kan du använda lågprioriterade virtuella datorer för kort experimentering jobb. Växla sedan till dedikerade virtuella datorer när det är dags att köra längre jobb.

* **Antalet noder** -Batch AI erbjuder alternativ för manuell och automatisk skalning för antalet noder i klustret. Med det manuella alternativet kontrollerar du när du skalar ett kluster upp och ned, så att du kan hantera din egen beräkningskostnaderna. Alternativet automatisk skalning säkerställer att klustret alltid downscales när du inte använder den, så du minimera dina beräkningskostnader. 

  Om du väljer att skala klustret manuellt måste ange du det ursprungliga målet när klustret skapas. Målet är antalet noder som Batch AI allokerar från början. Senare, du kan manuellt ändra storlek på antalet noder.  

  Om du väljer alternativet automatisk skalning kan definiera du de högsta och lägsta antalet målnoder när klustret skapas. Målet kan vara mellan 0 och det maximala antalet noder som stöds av din [Batch AI-kärnkvoten](quota-limits.md). Målet för 0 kan du behålla din klusterkonfiguration utan att behöva betala för alla beräkningskostnaderna. Om du begär ett högre mål än kvot Gränsen stöder misslyckas etableringen. 

* **VM-avbildning** -Batch AI som standard etablerar virtuella datorer i klustret med en standard Ubuntu Server-avbildning som har stöd för arbetsbelastningar. Du kan välja en annan förkonfigurerade Linux-avbildning från Azure Marketplace, till exempel en [Data Science Virtual Machine](../machine-learning/data-science-virtual-machine/overview.md). 

* **Storage** -Batch AI tillhandahåller ett alternativ för automatisk lagring som du kan välja när du skapar ett kluster med Azure CLI. Det här alternativet skapar automatiskt en Azure-filen resurs- och Blob-behållare under ett nytt lagringskonto. Dessa lagringsresurser monteras till varje nod i klustret under tiden för körningen, så att filerna ska kunna nås från en lokal sökväg. Lagringskontonamn, namn på filresurs, Blob-behållarnamn och monteringssökvägar som alla har standardvärden som kan också anpassas med ytterligare parametrar när klustret skapas. 

  Om inga lagringsalternativ för definieras, måste du skapa lagringsresurserna separat och definiera dem när du skickar in jobb. Det här alternativet är användbart om ditt kluster hanteras på organisationsnivå, men lagringen hanteras på användarnivå. Läs mer om hur du överför filer till Azure Storage och komma åt dem under körningstid [Store Batch AI jobb-in- och utdata med Azure Storage](use-azure-storage.md).

* **Användaråtkomst** – Batch AI kan du generera offentliga och privata SSH nyckeln filer när du skapar ett kluster eller ange dina egna SSH-nycklar så att du kan SSH till enskilda noder. Du kan också definiera ett användarnamn (Ange som den aktuella användaren som standard) och lösenord. De här autentiseringsuppgifterna kan användas för att komma åt noderna under körning för att visa olika mått eller få ytterligare insikter till dina jobb.

* **Uppgifter för inställning av** -Batch AI kan du definiera kommandoradsargument som ska köras på varje nod om fördelning. Du kan också definiera den sökväg där filen ska loggas. Använd det här alternativet om du har ytterligare etablering åtgärder utöver basavbildningen.

* **Ytterligare konfiguration** -det finns flera mindre vanliga scenarier som kan kräva mer specialiserade konfigurationer. I det här fallet en [kluster konfigurationsfilen](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#using-cluster-configuration-file) kan kopplas med Azure CLI-kommandot för att skapa ett kluster. 

## <a name="create-the-cluster"></a>Skapa klustret

När du har bestämt om konfigurationsalternativ för klustret, använder du Azure CLI, Azure-portalen eller API: er för Batch AI för att skapa klustret. Till exempel för att skapa ett kluster med Azure CLI, du kan följa den [az batchai cluster skapa](/cli/azure/batchai/cluster?view=azure-cli-latest#az-batchai-cluster-create) dokumentationen för att skapa det kommando som ger dig de konfigurationer som du behöver. 

På den mest grundläggande konfigurationen av följande kommando bestämmelser en Standard_NC6 kluster med en nod och SSH-åtkomst. Som standard innehåller det här klustret dedikerade virtuella datorer som kör den senaste standarden Ubuntu Server-avbildning.

```azurecli-interactive
az batchai cluster create \
    --name mycluster \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> \
    --vm-size Standard_NC6 \
    --target 1 \
    --generate-ssh-keys
```

Följande exempel en Standard_NC6 kluster som skalas automatiskt från 0 till 4 noder och använder noder med låg prioritet och ett konto för automatisk lagring. Den här inställningen är en fungerande konfiguration om du behöver ett kluster som är billig och enkel att hantera. 

```azurecli-interactive
az batchai cluster create \
    --name mycluster \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> \
    --vm-size Standard_NC6 \
    --vm-priority lowpriority \
    --max 4 \
    --min 0 \
    --use-auto-storage 
```

I följande exempel etableras en Standard_NC6-kluster som innehåller en Data Science Virtual Machine-avbildning, anpassad lagring och montering alternativ, anpassade SSH-autentiseringsuppgifterna, en uppgifter för inställning av som installerar den *packa upp* paket och ett kluster konfigurationsfilen för ytterligare inställningar. Den här konfigurationen är ett exempel på ett kluster som är mer anpassade till dina egna behov.

```azurecli-interactive
az batchai cluster create \
    --name mycluster \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> \
    --vm-size Standard_NC6 \
    --image UbuntuDSVM \ 
    --config-file cluster.json \
    --setup-task 'apt install unzip -y'
    --storage-account-name <STORAGE ACCOUNT NAME> \
    --nfs-name nfsmount \
    --afs-name afsmount \
    --bfs-name bfsmount \
    --user-name adminuser \
    --ssh-key id_rsa.pub \
    --password secretpassword 
```

## <a name="monitor-the-cluster"></a>Övervaka klustret

Den [az batchai klusterlista](/cli/azure/batchai/cluster?view=azure-cli-latest#az-batchai-cluster-list), [az batchai cluster show](/cli/azure/batchai/cluster?view=azure-cli-latest#az-batchai-cluster-show), och [az batchai cluster nodlistan](/cli/azure/batchai/cluster/node?view=azure-cli-latest#az-batchai-cluster-node-list) kommandon kan användas för att övervaka olika informationen för varje kluster.

### <a name="list-all-clusters"></a>Lista över alla kluster

I följande kommando lista alla kluster i en arbetsyta.

```azurecli-interactive
az batchai cluster list \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> 
```

### <a name="show-information-about-a-cluster"></a>Visa information om ett kluster

Följande kommando visar fullständig information om ett specifikt kluster i ett tabellformat.

```azurecli-interactive
az batchai cluster show \
    --name <CLUSTER NAME> \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> \
    --output table
```

Om klustret har etablerats med hjälp av alternativet Automatisk, kommer du att hämta namnet på lagringskontot ska användas för att ladda upp skript och upplärningsjobb. Ange följande kommando:

```azurecli-interactive
az batchai cluster show \
    --name <CLUSTER NAME> \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> \
    --query "nodeSetup.mountVolumes.azureFileShares[0].{storageAccountName:accountName}"
```

Utdata ska se ut ungefär så här.

```JSON
{
  "storageAccountName": "baixxxxxxxxx"
}
```

### <a name="list-cluster-nodes"></a>Noder i listan

Om du vill ansluta till noderna i hämtar följande kommando listan över noder och anslutningsinformationen.  

```azurecli-interactive
az batchai cluster node list \
    --name <CLUSTER NAME> \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> 
 ```

Utdata för varje nod ska vara liknar följande:

```JSON
[
  {
    "ipAddress": "40.68.xxx.xxx",
    "nodeId": "tvm-xxxxxxxxxx-xxxxxxxx",
    "port": 50000.0
  }
]
```
Du kan använda den här informationen för att göra en SSH-anslutning till en nod med ett kommando som liknar följande.

```bash
ssh myusername@40.68.xxx.xxx -p 50000
``` 

## <a name="submit-jobs-to-the-cluster"></a>Skicka jobb till klustret

När du har etablerat klustret kan du sedan skicka jobb som ska köras på noderna. Se den [az batchai job](/cli/azure/batchai/job?view=azure-cli-latest) kommandot för olika sätt att förbereda, skicka och övervaka jobb med hjälp av Azure CLI. 

## <a name="downscale-cluster-for-later-use"></a>Downscale kluster för senare användning

När du är klar med att köra jobb vill du skala ned klustret. Rekommenderar vi att du alltid downscale kluster när de inte används för att spara beräkningskostnader. Downscaling ett kluster till 0 noder kan du stoppa din debiterade avgifterna när du inte behöver etablera om klustren i framtiden när du behöver skala upp igen. Om automatisk skalning har valts i klustret har skapats bör klustret automatiskt skala ned till lägsta målet. Skala ned klustret med följande kommando om manuell skalning har valts.

```azurecli-interactive
az batchai cluster resize \
    --name <CLUSTER NAME> \
    --resource-group <RESOURCE GROUP> \
    --workspace <WORKSPACE> \
    --target 0
```

## <a name="delete-a-cluster"></a>Ta bort ett kluster

När du har använt ett kluster kan använda den [az batchai cluster delete](/cli/azure/batchai/cluster?view=azure-cli-latest#az-batchai-cluster-delete) kommando för att ta bort den.

```azurecli-interactive
az batchai cluster delete \
    --name <CLUSTER NAME> \
    --resource-group <RESOURCE GROUP> \
    --workspace <WORKSPACE>
```

Tar du bort resursgruppen också automatiskt alla kluster som etablerades under resursgruppen.

```azurecli-interactive
az group delete --name <RESOURCE GROUP>
```

## <a name="next-steps"></a>Nästa steg

Fler exempel för att skapa ett Batch AI-kluster finns i den [Portal](quickstart-create-cluster-portal.md) eller [Azure CLI](quickstart-create-cluster-cli.md) snabbstarten eller [Batch AI-recept](https://github.com/Azure/BatchAI/tree/master/recipes) på GitHub.
