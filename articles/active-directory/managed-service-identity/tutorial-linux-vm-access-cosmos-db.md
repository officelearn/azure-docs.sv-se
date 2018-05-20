---
title: Använd en virtuell Linux-MSI för att komma åt Azure Cosmos DB
description: En självstudiekurs som vägleder dig genom processen att använda en System-Assigned hanteras Service identitet (MSI) på en Linux-VM för att komma åt Azure Cosmos DB.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/09/2018
ms.author: skwan
ms.openlocfilehash: b84426ad28dbc2264bd9b28fe0697a88390bb58d
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
---
# <a name="tutorial-use-a-linux-vm-msi-to-access-azure-cosmos-db"></a>Självstudier: Använda en virtuell Linux-MSI för att komma åt Azure Cosmos DB 

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]


Den här kursen visar hur du skapar och använder en Linux VM MSI. Lär dig att:

> [!div class="checklist"]
> * Skapa en Linux VM med MSI-aktiverad
> * Skapa ett Cosmos DB-konto
> * Skapa en samling i Cosmos-DB-konto
> * Ge MSI-åtkomst till en Azure DB som Cosmos-instans
> * Hämta den `principalID` av de Linux-VM MSI
> * Hämta en åtkomst-token och använda den för att anropa Azure Resource Manager
> * Hämta åtkomstnycklar från Azure Resource Manager för att ringa Cosmos DB

## <a name="prerequisites"></a>Förutsättningar

Om du inte redan har ett Azure-konto [registrera dig för ett kostnadsfritt konto](https://azure.microsoft.com) innan du fortsätter.

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

Om du vill köra CLI skript exemplen i den här kursen har du två alternativ:

- Använd [Azure Cloud Shell](~/articles/cloud-shell/overview.md) från Azure-portalen, eller via den **prova** knapp som finns i det övre högra hörnet av varje kodblock.
- [Installera den senaste versionen av CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 eller senare) om du föredrar att använda den lokala CLI-konsolen.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Skapa en virtuell Linux-dator i en ny resursgrupp

Den här självstudiekursen skapar du en ny MSI aktiverat Linux VM.

Skapa en MSI-aktiverad virtuell dator:

1. Om du använder Azure CLI i den lokala konsolen först logga in på Azure med hjälp av [az inloggningen](/cli/azure/reference-index#az_login). Använd ett konto som är associerade med Azure-prenumeration som du vill distribuera den virtuella datorn:

   ```azurecli-interactive
   az login
   ```

2. Skapa en [resursgruppen](../../azure-resource-manager/resource-group-overview.md#terminology) för inneslutning och distribution av den virtuella datorn och dess relaterade resurser med hjälp av [az gruppen skapa](/cli/azure/group/#az_group_create). Du kan hoppa över det här steget om du redan har resursgrupp som du vill använda i stället:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Skapa en virtuell dator med hjälp av [az vm skapa](/cli/azure/vm/#az_vm_create). I följande exempel skapas en virtuell dator med namnet *myVM* med en MSI som begärs av den `--assign-identity` parameter. Den `--admin-username` och `--admin-password` parametrarna anger administrativa namnet och lösenordet användarkontot för virtuell dator-inloggning. Uppdatera dessa värden som passar din miljö: 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

## <a name="create-a-cosmos-db-account"></a>Skapa ett Cosmos DB-konto 

Om du inte redan har ett, skapa ett Cosmos-DB-konto. Du kan hoppa över detta steg och Använd ett befintligt Cosmos-DB-konto. 

1. Klicka på den **+/ Skapa ny tjänst** knapp hittades i det övre vänstra hörnet i Azure-portalen.
2. Klicka på **databaser**, sedan **Azure Cosmos DB**, och ett nytt ”nya konto” panelen visar.
3. Ange en **ID** för Cosmos-DB-konto som du använder senare.  
4. **API** ska vara inställd på ”SQL”. Den metod som beskrivs i den här kursen kan användas med andra tillgängliga typer API, men stegen i den här kursen är för SQL-API.
5. Se till att den **prenumeration** och **resursgruppen** matchar de som du angav när du skapade den virtuella datorn i föregående steg.  Välj en **plats** där Cosmos DB är tillgänglig.
6. Klicka på **Skapa**.

## <a name="create-a-collection-in-the-cosmos-db-account"></a>Skapa en samling i Cosmos-DB-konto

Lägg till en datainsamling på Cosmos-DB-kontot som du kan fråga i senare steg.

1. Navigera till ditt nyligen skapade Cosmos-DB-konto.
2. På den **översikt** klickar du på den **+/ lägga till samlingen** knapp och en ”Lägg till samling” panelen bilder ut.
3. Ge samlingen ett databas-ID, Samlings-ID, Välj lagringskapacitet, ange en partitionsnyckel, ange ett värde för genomflöde och sedan på **OK**.  Den här kursen räcker att använda ”Test” som databas-ID och Samlings-ID, Välj en fast lagringskapacitet och lägsta dataflöde (400 RU/s).  

## <a name="retrieve-the-principalid-of-the-linux-vms-msi"></a>Hämta den `principalID` för Linux-VM MSI

För att få åtkomst till åtkomstnycklarna för Cosmos-DB-konto från Resource Manager i följande avsnitt, måste du hämta den `principalID` Linux VM MSI.  Se till att ersätta den `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` (resursgrupp som du VM finns), och `<VM NAME>` parametervärden med egna värden.

```azurecli-interactive
az resource show --id /subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAMe> --api-version 2017-12-01
```
Svaret innehåller information om automatiskt tilldelade MSI-filerna (Observera principalID eftersom den används i nästa avsnitt):

```bash  
{
    "id": "/subscriptions/<SUBSCRIPTION ID>/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAMe>",
  "identity": {
    "principalId": "6891c322-314a-4e85-b129-52cf2daf47bd",
    "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533f8",
    "type": "SystemAssigned"
 }

```
## <a name="grant-your-linux-vm-msi-access-to-the-cosmos-db-account-access-keys"></a>Ger dina Linux VM MSI tillgång till åtkomstnycklarna för Cosmos-DB-konto

Cosmos DB stöder inte Azure AD authentication internt. Dock kan du använda en MSI för att hämta en Cosmos-DB åtkomst från Resource Manager sedan använder för att komma åt Cosmos DB. I det här steget kan du ge din MSI tillgång till nycklarna på Cosmos-DB-kontot.

Om du vill ge MSI identitet åtkomst till Cosmos-DB-konto i Azure Resource Manager med hjälp av Azure CLI, uppdatera värdena för `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, och `<COSMOS DB ACCOUNT NAME>` för din miljö. Ersätt `<MSI PRINCIPALID>` med den `principalId` egenskap som returneras av den `az resource show` i [hämta principalID Linux VM MSI](#retrieve-the-principalID-of-the-linux-VM's-MSI).  Cosmos DB stöder två detaljnivåer när du använder åtkomstnycklar: full åtkomst till kontot och skrivskyddad åtkomst till kontot.  Tilldela den `DocumentDB Account Contributor` roll om du vill hämta läsning och skrivning nycklar för kontot eller tilldela den `Cosmos DB Account Reader Role` roll om du vill ha skrivskyddad nycklar för kontot:

```azurecli-interactive
az role assignment create --assignee <MSI PRINCIPALID> --role '<ROLE NAME>' --scope "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMODS DB ACCOUNT NAME>"
```

Svaret innehåller information för rolltilldelning skapas:

```
{
  "id": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMOS DB ACCOUNT>/providers/Microsoft.Authorization/roleAssignments/5b44e628-394e-4e7b-bbc3-d6cd4f28f15b",
  "name": "5b44e628-394e-4e7b-bbc3-d6cd4f28f15b",
  "properties": {
    "principalId": "c0833082-6cc3-4a26-a1b1-c4b5f90a981f",
    "roleDefinitionId": "/subscriptions/<SUBSCRIPTION ID>/providers/Microsoft.Authorization/roleDefinitions/fbdf93bf-df7d-467e-a4d2-9458aa1360c8",
    "scope": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMOS DB ACCOUNT>"
  },
  "resourceGroup": "<RESOURCE GROUP>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="get-an-access-token-using-the-linux-vms-msi-and-use-it-to-call-azure-resource-manager"></a>Få en åtkomsttoken med hjälp av Linux VM MSI och använda den för att anropa Azure Resource Manager

Resten av kursen fungera i från den virtuella datorn skapade tidigare.

Du behöver en SSH-klient för att slutföra de här stegen. Om du använder Windows, kan du använda SSH-klienten i den [Windows undersystem för Linux](https://msdn.microsoft.com/commandline/wsl/install_guide). Om du behöver hjälp konfigurerar SSH-klientens nycklar, se [hur du använder SSH-nycklar med Windows på Azure](../../virtual-machines/linux/ssh-from-windows.md), eller [hur du skapar och använder ett SSH offentliga och privata nyckelpar för Linux virtuella datorer i Azure](../../virtual-machines/linux/mac-create-ssh-keys.md).

1. I Azure-portalen går du till **virtuella datorer**, gå till din Linux virtuella dator, sedan från den **översikt** klickar **Anslut** längst upp. Kopiera strängen som ska ansluta till den virtuella datorn. 
2. Ansluta till den virtuella datorn med hjälp av SSH-klienten.  
3. Därefter måste du uppmanas att ange i din **lösenord** du har lagt till när du skapar den **Linux VM**. Du bör sedan vara loggat in.  
4. Använd CURL för att hämta ett åtkomsttoken för Azure Resource Manager: 
     
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true   
    ```
 
    > [!NOTE]
    > Värdet för ”resurser”-parametern måste vara en exakt matchning för vad som förväntas av Azure AD i den tidigare begäranden. När du använder Azure Resource Manager resurs-ID, måste du inkluderar det avslutande snedstrecket på URI.
    > I följande svaret access_token elementet som kortats planeringsaspekter.
    
    ```bash
    {"access_token":"eyJ0eXAiOi...",
     "expires_in":"3599",
     "expires_on":"1518503375",
     "not_before":"1518499475",
     "resource":"https://management.azure.com/",
     "token_type":"Bearer",
     "client_id":"1ef89848-e14b-465f-8780-bf541d325cd5"}
     ```
    
## <a name="get-access-keys-from-azure-resource-manager-to-make-cosmos-db-calls"></a>Hämta åtkomstnycklar från Azure Resource Manager för att ringa Cosmos DB  

Använda CURL för att anropa Resource Manager med den åtkomst-token som hämtades i föregående avsnitt för att hämta Cosmos DB åtkomstnyckel. När vi har snabbtangent fråga vi Cosmos DB. Se till att ersätta den `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, och `<COSMOS DB ACCOUNT NAME>` parametervärden med egna värden. Ersätt den `<ACCESS TOKEN>` värde med den åtkomsttoken som du hämtade tidigare.  Om du vill hämta nycklar för läsning och skrivning använda viktiga åtgärdstypen `listKeys`.  Om du vill hämta skrivskyddade nycklar använder typ av nyckel åtgärd `readonlykeys`:

```bash 
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMOS DB ACCOUNT NAME>/<KEY OPERATION TYPE>?api-version=2016-03-31' -X POST -d "" -H "Authorization: Bearer <ACCESS TOKEN>" 
```

> [!NOTE]
> Texten i den tidigare URL: en är skiftlägeskänsligt, så kontrollera om du använder övre gemena för resursgrupperna återspeglar den därför. Dessutom är det viktigt att du vet att det här är en POST-begäran inte en GET-begäran och se till att du skickar ett värde för att avbilda en längdbegränsningen med -d som kan vara NULL.  

Svaret CURL ger listan med nycklar.  Till exempel om du får skrivskyddade nycklar:  

```bash 
{"primaryReadonlyMasterKey":"bWpDxS...dzQ==",
"secondaryReadonlyMasterKey":"38v5ns...7bA=="}
```

Nu när du har åtkomstnyckeln för kontot Cosmos DB du skickar den till en Cosmos DB SDK och gör anrop för att komma åt kontot.  Du kan överföra snabbtangenten för ett enkelt exempel till Azure CLI.  Du kan hämta den <COSMOS DB CONNECTION URL> från den **översikt** fliken på bladet Cosmos-DB-konto i Azure-portalen.  Ersätt den <ACCESS KEY> med värdet som du fick ovan:

```bash
az cosmosdb collection show -c <COLLECTION ID> -d <DATABASE ID> --url-connection "<COSMOS DB CONNECTION URL>" --key <ACCESS KEY>
```

Det här kommandot CLI returnerar information om samlingen:

```bash
{
  "collection": {
    "_conflicts": "conflicts/",
    "_docs": "docs/",
    "_etag": "\"00006700-0000-0000-0000-5a8271e90000\"",
    "_rid": "Es5SAM2FDwA=",
    "_self": "dbs/Es5SAA==/colls/Es5SAM2FDwA=/",
    "_sprocs": "sprocs/",
    "_triggers": "triggers/",
    "_ts": 1518498281,
    "_udfs": "udfs/",
    "id": "Test",
    "indexingPolicy": {
      "automatic": true,
      "excludedPaths": [],
      "includedPaths": [
        {
          "indexes": [
            {
              "dataType": "Number",
              "kind": "Range",
              "precision": -1
            },
            {
              "dataType": "String",
              "kind": "Range",
              "precision": -1
            },
            {
              "dataType": "Point",
              "kind": "Spatial"
            }
          ],
          "path": "/*"
        }
      ],
      "indexingMode": "consistent"
    }
  },
  "offer": {
    "_etag": "\"00006800-0000-0000-0000-5a8271ea0000\"",
    "_rid": "f4V+",
    "_self": "offers/f4V+/",
    "_ts": 1518498282,
    "content": {
      "offerIsRUPerMinuteThroughputEnabled": false,
      "offerThroughput": 400
    },
    "id": "f4V+",
    "offerResourceId": "Es5SAM2FDwA=",
    "offerType": "Invalid",
    "offerVersion": "V2",
    "resource": "dbs/Es5SAA==/colls/Es5SAM2FDwA=/"
  }
}
```

## <a name="next-steps"></a>Nästa steg

- En översikt över MSI finns [hanteras Service identitet (MSI) för Azure-resurser](overview.md).

