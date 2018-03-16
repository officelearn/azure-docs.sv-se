---
title: "Använd en Användartilldelad hanteras Service identitet (MSI) på en Linux-VM för att komma åt Azure Cosmos DB"
description: "En självstudiekurs som vägleder dig genom processen att använda en User-Assigned hanteras Service identitet (MSI) på en Linux-VM för att komma åt Azure Cosmos DB."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/14/2018
ms.author: skwan
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: dbb5e9e8f9accd618599010ab2bbb4a8760e534f
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="use-a-user-assigned-managed-service-identity-msi-on-a-linux-vm-to-access-azure-cosmos-db"></a>Använd en Användartilldelad hanteras Service identitet (MSI) på en Linux-VM för att komma åt Azure Cosmos DB 

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Den här kursen visar hur du skapar och använder en Användartilldelad hanteras Service identitet (MSI) från en virtuell Linux-dator och sedan använda den för att komma åt Azure Cosmos DB. Lär dig att:

> [!div class="checklist"]
> * Skapa en användare som tilldelats hanterade tjänstidentiteten (MSI)
> * Tilldela Användartilldelad MSI-filerna till en virtuell Linux-dator
> * Ge MSI-åtkomst till en Azure DB som Cosmos-instans
> * Få en åtkomsttoken som använder Användartilldelad MSI identitet och använda den för att komma åt Azure Cosmos DB

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

Om du vill köra CLI skript exemplen i den här kursen har du två alternativ:

- Använd [Azure Cloud Shell](~/articles/cloud-shell/overview.md) från Azure-portalen, eller via den **prova** knapp som finns i det övre högra hörnet av varje kodblock.
- [Installera den senaste versionen av CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 eller senare) om du föredrar att använda den lokala CLI-konsolen.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure-portalen på [ https://portal.azure.com ](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Skapa en virtuell Linux-dator i en ny resursgrupp

Den här självstudiekursen skapar vi en ny Linux VM. Du kan också aktivera MSI på en befintlig virtuell dator.

1. Klicka på den **+ skapa en resurs** hittades på det övre vänstra hörnet i Azure-portalen.
2. Välj **Compute**, och välj sedan **Ubuntu Server 16.04 LTS VM**.
3. Ange informationen för den virtuella datorn. För **autentiseringstyp**väljer **offentliga SSH-nyckeln** eller **lösenord**. Autentiseringsuppgifterna som har skapats kan du logga in på den virtuella datorn.

    ![MSI virtuell Linux-dator](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Välj en **prenumeration** för den virtuella datorn i listrutan.
5. Under **resursgruppen**, Välj **Skapa nytt** och Skriv namnet på resursgruppen för den här virtuella datorn. När du är klar klickar du på **OK**.
6. Välj storlek för den virtuella datorn. Om du vill se mer storlekar, Välj **visa alla** eller ändra typ-filter stöds disk. I den **inställningar** bladet behålla standardinställningarna och klickar på **OK**.

## <a name="create-a-user-assigned-msi"></a>Skapa en Användartilldelad MSI

1. Om du använder CLI-konsolen (i stället för en Azure-molnet Shell-session), starta genom att logga in till Azure. Använd ett konto som är associerade med Azure-prenumeration som du vill skapa nya MSI:

    ```azurecli
    az login
    ```

2. Skapa en Användartilldelad MSI med [az identitet skapa](/cli/azure/identity#az_identity_create). Den `-g` parametern anger resursgruppen där MSI skapas och `-n` parametern anger dess namn. Se till att ersätta den `<RESOURCE GROUP>` och `<MSI NAME>` parametervärden med egna värden:

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```

    Svaret innehåller information för användaren som tilldelats MSI-filerna som skapats, liknar följande exempel (Obs den `clientId` och `id` värden för din MSI när de används i senare steg):

    ```json
    {
    "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
    "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=123444643-8088-4d70-9532-c3a0fdc190fz",
    "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>",
    "location": "westcentralus",
    "name": "<MSI NAME>",
    "principalId": "c0833082-6cc3-4a26-a1b1-c4b5f90a981f",
    "resourceGroup": "<RESOURCE GROUP>",
    "tags": {},
    "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
    "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
    }
    ```

## <a name="assign-your-user-assigned-msi-to-your-linux-vm"></a>Tilldela din Användartilldelad MSI för Linux-VM

Till skillnad från en automatiskt genererad MSI kan en Användartilldelad MSI användas av klienter på Azure-resurser. Den här kursen kan tilldela du den till en enda virtuell dator. Du kan också tilldela den till mer än en virtuell dator.

Tilldela din Linux VM som använder Användartilldelad MSI [az tilldela-identitet för](/cli/azure/vm#az_vm_assign_identity). Se till att ersätta den `<RESOURCE GROUP>` och `<VM NAME>` parametervärden med egna värden. Använd den `id` egenskapen returneras i föregående steg för den `--identities` parametervärdet:

```azurecli-interactive
az vm assign-identity -g <RESOURCE GROUP> -n <VM NAME> --identities "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>"
```

## <a name="create-a-cosmos-db-account"></a>Skapa ett Cosmos DB-konto 

Om du inte redan har ett nu skapa ett Cosmos-DB-konto. Du kan också hoppa över detta steg och Använd ett befintligt Cosmos-DB-konto om du föredrar. 

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

## <a name="grant-your-user-assigned-msi-access-to-the-cosmos-db-account-access-keys"></a>Ger dina användare tilldelade MSI tillgång till åtkomstnycklarna för Cosmos-DB-konto

Cosmos DB stöder inte Azure AD authentication internt.  Dock kan du använda en MSI för att hämta en Cosmos-DB åtkomst från Resource Manager sedan använder för att komma åt Cosmos DB.  I det här steget kan bevilja du användaren tilldelats nycklar till kontot Cosmos DB MSI-åtkomst.

Först bevilja MSI identitet åtkomst till Cosmos-DB-konto i Azure Resource Manager med hjälp av Azure CLI. Uppdatera värdena för `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, och `<COSMOS DB ACCOUNT NAME>` för din miljö. Ersätt `<MSI PRINCIPALID>` med den `principalId` egenskap som returneras av den `az identity create` i [skapa en Användartilldelad MSI](#create-a-user-assigned-msi).  Cosmos DB stöder två detaljnivåer när du använder åtkomstnycklar: full åtkomst till kontot och skrivskyddad åtkomst till kontot.  Tilldela den `DocumentDB Account Contributor` roll om du vill hämta läsning och skrivning nycklar för kontot eller tilldela den `Cosmos DB Account Reader Role` roll om du vill ha skrivskyddad nycklar för kontot:

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

## <a name="get-an-access-token-using-the-user-assigned-msi-and-use-it-to-call-azure-resource-manager"></a>Få en åtkomsttoken med hjälp av användaren tilldelas MSI-filerna och använda den för att anropa Azure Resource Manager

Resten av kursen fungera i från den virtuella datorn skapade tidigare.

Du behöver en SSH-klient för att slutföra de här stegen. Om du använder Windows, kan du använda SSH-klienten i den [Windows undersystem för Linux](https://msdn.microsoft.com/commandline/wsl/install_guide). Om du behöver hjälp konfigurerar SSH-klientens nycklar, se [hur du använder SSH-nycklar med Windows på Azure](../../virtual-machines/linux/ssh-from-windows.md), eller [hur du skapar och använder ett SSH offentliga och privata nyckelpar för Linux virtuella datorer i Azure](../../virtual-machines/linux/mac-create-ssh-keys.md).

1. I Azure-portalen går du till **virtuella datorer**, gå till din Linux virtuella dator, sedan från den **översikt** klickar **Anslut** längst upp. Kopiera strängen som ska ansluta till den virtuella datorn. 
2. Ansluta till den virtuella datorn med hjälp av SSH-klienten.  
3. Därefter måste du uppmanas att ange i din **lösenord** du har lagt till när du skapar den **Linux VM**. Du bör sedan vara loggat in.  
4. Använd CURL för att hämta ett åtkomsttoken för Azure Resource Manager.  

    CURL förfrågan och svar för åtkomst-token är under.  Ersätt <CLIENT ID> med clientId tilldelas värdet för din användare MSI: 
    
    ```bash
    curl -H Metadata:true "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com/&client_id=<MSI CLIENT ID>" 
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

Nu använder CURL för att anropa Resource Manager med den åtkomst-token som vi hämtades i föregående avsnitt, att hämta Cosmos DB åtkomstnyckel. När vi har snabbtangent fråga vi Cosmos DB. Se till att ersätta den `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, och `<COSMOS DB ACCOUNT NAME>` parametervärden med egna värden. Ersätt den `<ACCESS TOKEN>` värde med den åtkomsttoken som du hämtade tidigare.  Om du vill hämta nycklar för läsning och skrivning använda viktiga åtgärdstypen `listKeys`.  Om du vill hämta skrivskyddade nycklar använder typ av nyckel åtgärd `readonlykeys`:

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

- En översikt över MSI finns [hanteras Service identitet (MSI) för Azure-resurser](msi-overview.md).

Använd följande avsnitt för kommentarer för att ge feedback och hjälp oss att förfina och utforma innehållet.
