---
title: Konfigurera Blockchain Data Manager med Azure CLI - Azure Blockchain Service
description: Skapa och hantera en Blockchain Data Manager för Azure Blockchain-tjänst med Azure CLI
ms.date: 03/30/2020
ms.topic: article
ms.reviewer: ravastra
ms.openlocfilehash: 5ceca96f760ab62ca7f3df9ad26139b9b4a3e5be
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529594"
---
# <a name="configure-blockchain-data-manager-using-azure-cli"></a>Konfigurera Blockchain Data Manager med Azure-portalen

Konfigurera Blockchain Data Manager för Azure Blockchain Service för att samla in blockchain-data skicka dem till ett Azure Event Grid-ämne.

Om du vill konfigurera en Blockchain Data Manager-instans:

* Skapa en Blockchain Manager-instans
* Skapa en indata till en azure blockchain-tjänsttransaktionsnod
* Skapa en utdata till ett Azure-händelserutnätsämne
* Lägga till ett blockchain-program
* Starta en instans

## <a name="prerequisites"></a>Krav

* Installera den senaste [Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) `az login`CLI och logga in med .
* Fullständig [snabbstart: Använd Visual Studio-kod för att ansluta till ett Azure Blockchain Service-konsortiumnätverk](connect-vscode.md). Azure Blockchain Service *Standard-nivå* rekommenderas när du använder Blockchain Data Manager.
* Skapa ett [ämne för händelserutnät](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)
* Lär dig mer om [händelsehanterare i Azure Event Grid](../../event-grid/event-handlers.md)

## <a name="launch-azure-cloud-shell"></a>Starta Azure Cloud Shell

Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto.

Om du vill öppna Cloud Shell väljer du bara **Prova** från det övre högra hörnet i ett kodblock. Du kan också starta Cloud Shell i [https://shell.azure.com/bash](https://shell.azure.com/bash)en separat webbläsarflik genom att gå till . Kopiera kodblocket genom att välja **Kopiera**, klistra in det i Cloud Shell och kör det genom att trycka på RETUR.

Om du föredrar att installera och använda CLI lokalt kräver den här snabbstarten Azure CLI version 2.0.51 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera läser du [installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create](https://docs.microsoft.com/cli/azure/group). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på *eastus-platsen:*

```azurecli-interactive
az group create --name myRG --location eastus
```

## <a name="create-instance"></a>Skapa instans

En Blockchain Data Manager-instans övervakar en Azure Blockchain Service-transaktionsnod. En instans samlar in alla råblock- och råtransaktionsdata från transaktionsnoden.

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Blockchain Data Manager instance name> \
                   --resource-type Microsoft.blockchain/watchers \
                   --is-full-object \
                   --properties <watcher resource properties>
```

| Parameter | Beskrivning |
|-----------|-------------|
| resource-group | Resursgruppsnamn där blockchain Data Manager-instansen ska skapas. |
| namn | Namnet på Blockchain Data Manager-instansen. |
| resurstyp | Resurstypen för en Blockchain Data Manager-instans är **Microsoft.blockchain/watchers**. |
| är-full-objekt | Anger egenskaper som innehåller alternativ för watcher-resursen. |
| properties | JSON-formaterad sträng som innehåller egenskaper för watcher-resursen. Kan skickas som en sträng eller en fil.  |

### <a name="create-instance-examples"></a>Skapa exempel på förekomst

JSON-konfigurationsexempel för att skapa en Blockchain Manager-instans i regionen **östra USA.**

``` json
{
    "location": "eastus",
    "properties": {
    }
}
```

| Element | Beskrivning |
|---------|-------------|
| location | Region där du kan skapa watcher-resursen |
| properties | Egenskaper som ska ställas in när du skapar watcher-resursen |

Skapa en Blockchain Data Manager-instans med namnet *mywatcher* med en JSON-sträng för konfiguration.

``` azurecli-interactive
az resource create \
                    --resource-group myRG \
                    --name mywatcher \
                    --resource-type Microsoft.blockchain/watchers \
                     --is-full-object \
                     --properties '{"location":"eastus"}'
```

Skapa en Blockchain Data Manager-instans med namnet *mywatcher* med hjälp av en JSON-konfigurationsfil.

``` azurecli
az resource create \
                    --resource-group myRG \
                    --name mywatcher \
                    --resource-type Microsoft.blockchain/watchers \
                    --is-full-object \
                    --properties @watcher.json
```

## <a name="create-input"></a>Skapa indata

En indata ansluter Blockchain Data Manager till en Azure Blockchain Service-transaktionsnod. Endast användare med åtkomst till transaktionsnoden kan skapa en anslutning.

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Input name> \
                   --namespace Microsoft.Blockchain \
                   --resource-type inputs \
                   --parent watchers/<Watcher name> \
                   --is-full-object \
                   --properties <input resource properties>
```

| Parameter | Beskrivning |
|-----------|-------------|
| resource-group | Resursgruppsnamn där indataresursen ska skapas. |
| namn | Namnet på indata. |
| namnområde | Använd namnområdet för **Microsoft.Blockchain-providern.** |
| resurstyp | Resurstypen för en Blockchain Data Manager-indata är **indata**. |
| parent | Sökvägen till den bevakare som indata är kopplad till. Till exempel **watchers /mywatcher**. |
| är-full-objekt | Anger egenskaper som innehåller alternativ för indataresursen. |
| properties | JSON-formaterad sträng som innehåller egenskaper för indataresursen. Kan skickas som en sträng eller en fil. |

### <a name="input-examples"></a>Exempel på inmatning

Konfiguration JSON exempel för att skapa en indataresurs \<i\>regionen *östra USA* som är ansluten till Blockchain-medlem .

``` json
{
    "location": "eastus",
    "properties": {
        "inputType": "Ethereum",
        "dataSource": {
            "resourceId": "/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/blockchainMembers/<Blockchain member>/transactionNodes/transaction-node"
        }
    }
}
```

| Element | Beskrivning |
|---------|-------------|
| location | Region där indataresursen ska skapas. |
| inputType (inputType) | Redovisningstyp för Azure Blockchain-tjänstmedlemmen. För närvarande stöds **Ethereum.** |
| resourceId | Transaktionsnod som indata är ansluten till. Ersätt \<prenumerations-ID,\> \< \<resursgrupp\> \>och Blockchain-medlem med värdena för transaktionsnodresursen. Indata ansluter till standardtransaktionsnoden för Azure Blockchain Service-medlemmen. |

Skapa en indata med namnet *myInput* för *mywatcher* med en JSON-sträng för konfiguration.

``` azurecli-interactive
az resource create \
                   --resource-group myRG \
                   --name myInput \
                   --namespace Microsoft.Blockchain \
                   --resource-type inputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties '{"location":"eastus", "properties":{"inputType":"Ethereum","dataSource":{"resourceId":"/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/BlockchainMembers/<Blockchain member>/transactionNodes/transaction-node"}}}'
```

Skapa en indata med namnet *myInput* för *mywatcher* med hjälp av en JSON-konfigurationsfil.

``` azurecli
az resource create \
                   --resource-group myRG \
                   --name input \
                   --namespace Microsoft.Blockchain \ --resource-type inputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties @input.json
```

## <a name="create-output"></a>Skapa utdata

En utgående anslutning skickar blockchain-data till Azure Event Grid. Du kan skicka blockchain-data till en enda destination eller skicka blockchain-data till flera destinationer. Blockchain Data Manager stöder utgående anslutningar för flera utgående eventrutnät för en viss Blockchain Data Manager-instans.

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Output name> \
                   --namespace Microsoft.Blockchain \
                   --resource-type outputs \
                   --parent watchers/<Watcher name> \
                   --is-full-object \
                   --properties <output resource properties>
```

| Parameter | Beskrivning |
|-----------|-------------|
| resource-group | Resursgruppsnamn där utdataresursen ska skapas. |
| namn | Namnet på utdata. |
| namnområde | Använd namnområdet för **Microsoft.Blockchain-providern.** |
| resurstyp | Resurstypen för en Blockchain Data Manager-utdata är **utdata**. |
| parent | Sökvägen till den bevakare som utdata är kopplad till. Till exempel **watchers /mywatcher**. |
| är-full-objekt | Anger egenskaper som innehåller alternativ för utdataresursen. |
| properties | JSON-formaterad sträng som innehåller egenskaper för utdataresursen. Kan skickas som en sträng eller en fil. |

### <a name="output-examples"></a>Exempel på utdata

Konfiguration JSON exempel för att skapa en utdataresurs i regionen *östra USA* som är ansluten till ett händelserutnätsämne med namnet \<händelserutnätsämne\>.

``` json
{
    "location": "eastus",
    "properties": {
        "outputType": "EventGrid",
        "dataSource": {
            "resourceId": "/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.EventGrid/topics/<event grid topic>"
        }
    }
}
```

| Element | Beskrivning |
|---------|-------------|
| location | Region där utdataresursen ska skapas. |
| outputType | Typ av utdata. För närvarande stöds **EventGrid.** |
| resourceId | Resurs som utdata är ansluten till. Ersätt \<prenumerations-ID,\> \< \<resursgrupp\> \>och Blockchain-medlem med värdena för händelserutnätsresursen. |

Skapa en utdata med namnet *myoutput* för *mywatcher* som ansluter till ett händelserutnätsämne med hjälp av en JSON-konfigurationssträng.

``` azurecli-interactive
az resource create \
                   --resource-group myRG \
                   --name myoutput \
                   --namespace Microsoft.Blockchain \
                   --resource-type outputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties '{"location":"eastus","properties":{"outputType":"EventGrid","dataSource":{"resourceId":"/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.EventGrid/topics/<event grid topic>"}}}'
```

Skapa en utdata med namnet *myoutput* för *mywatcher* som ansluter till ett händelserutnätsämne med hjälp av en JSON-konfigurationsfil.

``` azurecli
az resource create \
                   --resource-group myRG \
                   --name myoutput \
                   --namespace Microsoft.Blockchain \
                   --resource-type outputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties @output.json
```

## <a name="add-blockchain-application"></a>Lägg till blockchain-program

Om du lägger till ett blockchain-program avkodar Blockchain Data Manager händelse- och egenskapstillstånd för programmet. Annars skickas endast råblock- och råtransaktionsdata. Blockchain Data Manager identifierar också kontraktsadresser när kontraktet distribueras. Du kan lägga till flera blockchain-program i en Blockchain Data Manager-instans.


> [!IMPORTANT]
> För närvarande stöds inte blockchain-program som deklarerar [soliditetsmatristyper](https://solidity.readthedocs.io/en/v0.5.12/types.html#arrays) eller [mappningstyper.](https://solidity.readthedocs.io/en/v0.5.12/types.html#mapping-types) Egenskaper som deklarerats som matris- eller mappningstyper kommer inte att avkodas i *ContractPropertiesMsg-* eller *DecodedContractEventsMsg-meddelanden.*

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Application name> \
                   --namespace Microsoft.Blockchain \
                   --resource-type artifacts \
                   --parent watchers/<Watcher name> \
                   --is-full-object \
                   --properties <Application resource properties>
```

| Parameter | Beskrivning |
|-----------|-------------|
| resource-group | Resursgruppsnamn där programresursen ska skapas. |
| namn | Programmets namn. |
| namnområde | Använd namnområdet för **Microsoft.Blockchain-providern.** |
| resurstyp | Resurstypen för ett Blockchain Data Manager-program är **artefakter**. |
| parent | Sökvägen till den bevakare som programmet är associerat till. Till exempel **watchers /mywatcher**. |
| är-full-objekt | Anger att egenskaper innehåller alternativ för programresursen. |
| properties | JSON-formaterad sträng som innehåller egenskaper för programresursen. Kan skickas som en sträng eller en fil. |

### <a name="blockchain-application-examples"></a>Exempel på blockchain-program

Configuration JSON exempel för att skapa en programresurs i regionen *östra USA* som övervakar ett smart kontrakt som definieras av kontraktet ABI och bytecode.

``` json
{
    "location": "eastus",
    "properties": {
        "artifactType": "EthereumSmartContract",
        "content": {
            "abiFileUrl": "<ABI URL>",
            "bytecodeFileUrl": "<Bytecode URL>",
            "queryTargetTypes": [
                "ContractProperties",
                "ContractEvents"
            ]
        }
    }
}
```

| Element | Beskrivning |
|---------|-------------|
| location | Region där programresursen ska skapas. |
| artifactType (artefakttyp) | Typ av program. För närvarande stöds **EthereumSmartContract.** |
| abiFileUrl | URL för smart kontrakt ABI JSON fil. Mer information om hur du skaffar kontrakt ABI och skapar en URL finns i [Hämta ABI-kontrakt och bytekod](data-manager-portal.md#get-contract-abi-and-bytecode) och [Skapa url för kontrakt ABI och bytecode](data-manager-portal.md#create-contract-abi-and-bytecode-url). |
| bytecodeFileUrl | URL för smart kontrakt distribueras bytecode JSON fil. Mer information om hur du skaffar smart kontraktsbe distribuerad bytekod och skapar en URL finns i [Hämta ABI-kontrakt och bytekod](data-manager-portal.md#get-contract-abi-and-bytecode) och [Skapa url för kontrakt ABI och bytecode](data-manager-portal.md#create-contract-abi-and-bytecode-url). Blockchain Data Manager kräver den **distribuerade bytekoden**. |
| frågaTargetTyper | Publicerade meddelandetyper. Om du anger **ContractProperties** publiceras *Messagemg-meddelandetypen ContractPropertiesMsg.* Ange **ContractEvents** publicerar *AvkodadeContractEventsMsg* meddelandetyp. *Meddelandetyperna RawBlockAndTransactionMsg* och *RawTransactionContractCreationMsg* publiceras alltid. |

Skapa ett program med namnet *myApplication* for *mywatcher* som övervakar ett smart kontrakt som definieras av en JSON-sträng.

``` azurecli-interactive
az resource create \
                   --resource-group myRG \
                   --name myApplication \
                   --namespace Microsoft.Blockchain \
                   --resource-type artifacts \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties '{"location":"eastus","properties":{"artifactType":"EthereumSmartContract","content":{"abiFileUrl":"<ABI URL>","bytecodeFileUrl":"<Bytecode URL>","queryTargetTypes":["ContractProperties","ContractEvents"]}}}'
```

Skapa ett program med namnet *myApplication* for *mywatcher* som bevakar ett smart kontrakt som definierats med hjälp av en JSON-konfigurationsfil.

``` azurecli
az resource create \
                   --resource-group myRG \
                   --name myApplication \
                   --namespace Microsoft.Blockchain \
                   --resource-type artifacts \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties @artifact.json
```

## <a name="start-instance"></a>Starta instans

När du kör övervakar en Blockchain Manager-instans blockchain-händelser från de definierade indata och skickar data till de definierade utgångarna.

``` azurecli
az resource invoke-action \
                          --action start \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/<Watcher name>
```

| Parameter | Beskrivning |
|-----------|-------------|
| åtgärd | Använd **start** för att köra klockan. |
| Id | Watcher-resurs-ID. Ersätt \<\>prenumerations-ID, \<resursgrupp\>och \<Watcher-namn\> med värdena för watcher-resursen.|

### <a name="start-instance-example"></a>Exempel på startinstans

Starta en Blockchain Data Manager-instans med namnet *mywatcher*.

``` azurecli-interactive
az resource invoke-action \
                          --action start \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/mywatcher
```

## <a name="stop-instance"></a>Stoppa instans

Stoppa en Blockchain Data Manager-instans.

``` azurecli
az resource invoke-action \
                          --action stop \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/<Watcher name>
```

| Parameter | Beskrivning |
|-----------|-------------|
| åtgärd | Använd **stoppet** för att stoppa klockan. |
| Id | Namnet på väktaren. Ersätt \<\>prenumerations-ID, \<resursgrupp\>och \<Watcher-namn\> med värdena för watcher-resursen. |

### <a name="stop-watcher-example"></a>Exempel på stoppa watcher

Stoppa en instans med namnet *mywatcher*.

``` azurecli-interactive
az resource invoke-action \
                          --action stop \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/mywatcher
```

## <a name="delete-instance"></a>Ta bort instans

Ta bort en Blockchain Data Manager-instans.

``` azurecli
az resource delete \
                   --resource-group <Resource group> \
                   --name <Watcher name> \
                   --resource-type Microsoft.Blockchain/watchers
```

| Parameter | Beskrivning |
|-----------|-------------|
| resource-group | Resursgruppsnamnet på den bevakare som ska tas bort. |
| namn | Namnet på den bevakare som ska tas bort. |
| resurstyp | Resurstypen för en Blockchain Data Manager-watcher är **Microsoft.blockchain/watchers**. |

### <a name="delete-instance-example"></a>Exempel på ta bort instans

Ta bort en instans med namnet *mywatcher* i *resursgruppen myRG.*

``` azurecli-interactive
az resource delete \
                   --resource-group myRG \
                   --name mywatcher \
                   --resource-type Microsoft.blockchain/watchers
```

## <a name="next-steps"></a>Nästa steg

Prova nästa självstudiekurs när du skapar en transaktionsutforskare med Blockchain Data Manager och Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Använda Blockchain Data Manager för att skicka data till Azure Cosmos DB](data-manager-cosmosdb.md)
