---
title: Använda en systemtilldelad hanterad identitet för en virtuell Linux-dator för åtkomst till Azure Cosmos DB
description: En självstudie som steg för steg beskriver hur du använder en systemtilldelad hanterad identitet för en virtuell Linux-dator för att få åtkomst till Azure Cosmos DB.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/09/2018
ms.author: daveba
ms.openlocfilehash: 64ccc115c227f625c163a6831ea71a41ae940981
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/31/2018
ms.locfileid: "43337715"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-cosmos-db"></a>Självstudie: Använda en systemtilldelad hanterad identitet för en virtuell Linux-dator för åtkomst till Azure Cosmos DB 

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]


I den här självstudien lär du dig hur du använder en systemtilldelad hanterad identitet för en virtuell Linux-dator för att komma åt Azure Cosmos DB. Lär dig att:

> [!div class="checklist"]
> * Skapa ett Cosmos DB-konto
> * Skapa en samling i Cosmos DB-kontot
> * Ge den systemtilldelade hanterade identiteten åtkomst till en Azure Cosmos DB-instans
> * Hämta `principalID` för den virtuella Linux-datorns systemtilldelade hanterade identitet
> * Hämta en åtkomsttoken och använd den för att anropa Azure Resource Manager
> * Hämta åtkomstnycklar från Azure Resource Manager för att göra Cosmos DB-anrop

## <a name="prerequisites"></a>Nödvändiga komponenter

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

- [Logga in på Azure-portalen](https://portal.azure.com)

- [Skapa en virtuell Linux-dator](/azure/virtual-machines/linux/quick-create-portal)

- [Aktivera systemtilldelade hanterade identiteter på din virtuella dator](/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm#enable-system-assigned-identity-on-an-existing-vm)

Om du vill köra CLI-exempelskripten i den här självstudien har du två alternativ:

- Använd [Azure Cloud Shell](~/articles/cloud-shell/overview.md) antingen från Azure Portal eller via knappen **Prova**, som du hittar i det övre högra hörnet av varje kodblock.
- [Installera den senaste versionen av CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 eller senare) om du föredrar att använda en lokal CLI-konsol.

## <a name="create-a-cosmos-db-account"></a>Skapa ett Cosmos DB-konto 

Om du inte redan har ett Cosmos DB-konto skapar du ett. Du kan hoppa över det här steget och använda ett befintligt Cosmos DB-konto. 

1. Klicka på knappen **+/Skapa ny tjänst** som finns i det övre vänstra hörnet på Azure Portal.
2. Klicka på **Databaser** och sedan på **Azure Cosmos DB**. Panelen ”Nytt konto” visas.
3. Ange ett **ID** för Cosmos DB-kontot som du ska använda senare.  
4. **API** bör ha värdet ”SQL”. Metoden som beskrivs i den här självstudien kan användas med de andra tillgängliga API-typerna, men stegen i den här självstudien gäller för SQL-API:et.
5. Kontrollera att informationen under **Prenumeration** och **Resursgrupp** matchar informationen som du angav när du skapade den virtuella datorn i föregående steg.  Välj en **plats** där Cosmos DB är tillgänglig.
6. Klicka på **Skapa**.

## <a name="create-a-collection-in-the-cosmos-db-account"></a>Skapa en samling i Cosmos DB-kontot

Lägg till en datasamling till Cosmos DB-kontot som du kan skicka frågor mot i senare steg.

1. Gå till Cosmos DB-kontot som du precis skapat.
2. Klicka på knappen **+/Lägg till samling** på fliken **Översikt**. Panelen ”Lägg till samling” visas.
3. Tilldela samlingen ett databas-ID och ett samlings-ID, välj en lagringskapacitet, ange en partitionsnyckel, ange ett värde för dataflöde och klicka sedan på **OK**.  I den här självstudien räcker det att du använder ”Test” som databas-ID och samlings-ID, väljer en fast lagringskapacitet och det lägsta dataflödet (400 RU/s).  

## <a name="retrieve-the-principalid-of-the-linux-vms-system-assigned-managed-identity"></a>Hämta `principalID` för den virtuella Linux-datorns systemtilldelade hanterade identitet

För att få åtkomst till åtkomstnycklarna för Cosmos DB-kontot från Resource Manager i följande avsnitt måste du hämta `principalID` för den virtuella Linux-datorns systemtilldelade hanterade identitet.  Ersätt parametervärdena `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` (resursgruppen som den virtuella datorn finns i) och `<VM NAME>` med dina egna värden.

```azurecli-interactive
az resource show --id /subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAMe> --api-version 2017-12-01
```
Svaret innehåller information om den systemtilldelade hanterade identiteten (notera principalID eftersom du behöver det i nästa avsnitt):

```bash  
{
    "id": "/subscriptions/<SUBSCRIPTION ID>/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAMe>",
  "identity": {
    "principalId": "6891c322-314a-4e85-b129-52cf2daf47bd",
    "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533f8",
    "type": "SystemAssigned"
 }

```
## <a name="grant-your-linux-vms-system-assigned-identity-access-to-the-cosmos-db-account-access-keys"></a>Ge den virtuella Linux-datorns systemtilldelade identitet åtkomst till åtkomstnycklarna för Cosmos DB-kontot

Cosmos DB har inte inbyggt stöd för Azure AD-autentisering. Du kan emellertid använda en hanterad identitet för att hämta en åtkomstnyckel för Cosmos DB från Resource Manager och sedan använda nyckeln för att komma åt Cosmos DB. I det här steget ger du den systemtilldelade hanterade identiteten åtkomst till nycklarna till Cosmos DB-kontot.

Om du ska ge den systemtilldelade hanterade identiteten åtkomst till Cosmos DB-kontot i Azure Resource Manager med hjälp av Azure CLI uppdaterar du värdet för `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` och `<COSMOS DB ACCOUNT NAME>` för din miljö. Ersätt `<MI PRINCIPALID>` med `principalId`-egenskapen som returnerades av kommandot `az resource show` i [Hämta principalID för den virtuella Linux-datorns MI](#retrieve-the-principalID-of-the-linux-VM's-system-assigned-identity).  Cosmos DB stöder två detaljnivåer när åtkomstnycklar används: läs- och skrivåtkomst till kontot samt skrivskyddad åtkomst till kontot.  Tilldela rollen `DocumentDB Account Contributor` om du vill använda läs-/skrivnycklar för kontot, eller rollen `Cosmos DB Account Reader Role` om du vill använda skrivskyddade nycklar för kontot:

```azurecli-interactive
az role assignment create --assignee <MI PRINCIPALID> --role '<ROLE NAME>' --scope "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMODS DB ACCOUNT NAME>"
```

Svaret innehåller information om rolltilldelningen som skapats:

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

## <a name="get-an-access-token-using-the-linux-vms-system-assigned-managed-identity-and-use-it-to-call-azure-resource-manager"></a>Hämta en åtkomsttoken med hjälp av den virtuella Linux-datorns systemtilldelade hanterade identitet och använd den för att anropa Azure Resource Manager

Under resten av den här självstudien ska du arbeta från den virtuella datorn som skapades tidigare.

För att slutföra de här stegen behöver du en SSH-klient. Om du använder Windows kan du använda SSH-klienten i [Windows-undersystemet för Linux](https://msdn.microsoft.com/commandline/wsl/install_guide). Om du behöver hjälp att konfigurera SSH-klientens nycklar läser du [Så här använder du SSH-nycklar med Windows i Azure](../../virtual-machines/linux/ssh-from-windows.md) eller [How to create and use an SSH public and private key pair for Linux VMs in Azure](../../virtual-machines/linux/mac-create-ssh-keys.md) (Skapa och använda SSH-nyckelpar med privata och offentliga nycklar för virtuella Linux-datorer i Azure).

1. Gå till **Virtuella datorer** på Azure Portal, gå till den virtuella Linux-datorn och klicka sedan längst upp på **Anslut** på sidan **Översikt**. Kopiera strängen för anslutning till din virtuella dator. 
2. Anslut till den virtuella datorn med hjälp av SSH-klienten.  
3. Nu uppmanas du att ange **lösenordet** som du lade till när du skapade **den virtuella Linux-datorn**. Därefter bör du loggas in.  
4. Använd CURL för att hämta en åtkomsttoken för Azure Resource Manager: 
     
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true   
    ```
 
    > [!NOTE]
    > I föregående begäran måste värdet för ”resource”-parametern vara en exakt matchning av vad som förväntas av Azure AD. När du använder resurs-ID:t för Azure Resource Manager måste du ta med det avslutande snedstrecket i URI:n.
    > I följande svar har access_token-elementet kortats ned.
    
    ```bash
    {"access_token":"eyJ0eXAiOi...",
     "expires_in":"3599",
     "expires_on":"1518503375",
     "not_before":"1518499475",
     "resource":"https://management.azure.com/",
     "token_type":"Bearer",
     "client_id":"1ef89848-e14b-465f-8780-bf541d325cd5"}
     ```
    
## <a name="get-access-keys-from-azure-resource-manager-to-make-cosmos-db-calls"></a>Hämta åtkomstnycklar från Azure Resource Manager för att göra Cosmos DB-anrop  

Använd CURL och anropa Resource Manager med hjälp av den åtkomsttoken som du fick i föregående avsnitt för att hämta åtkomstnyckeln för Cosmos DB-kontot. När vi väl har åtkomstnyckeln kan vi skicka frågor mot Cosmos DB. Ersätt parametervärdena `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` och `<COSMOS DB ACCOUNT NAME>` med dina egna värden. Ersätt värdet `<ACCESS TOKEN>` med den åtkomsttoken som du hämtade tidigare.  Om du vill hämta läs- och skrivnycklar använder du nyckelåtgärdstypen `listKeys`.  Om du vill hämta skrivskyddade nycklar använder du nyckelåtgärdstypen `readonlykeys`:

```bash 
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMOS DB ACCOUNT NAME>/<KEY OPERATION TYPE>?api-version=2016-03-31' -X POST -d "" -H "Authorization: Bearer <ACCESS TOKEN>" 
```

> [!NOTE]
> Eftersom texten i den tidigare URL:en är skiftlägeskänslig måste du använda samma skiftläge för dina resursgrupper. Dessutom är det viktigt att du är medveten om att det här är en POST-begäran, inte en GET-begäran, samt att du skickar ett värde för att registrera en längdbegränsning med -d som kan vara NULL.  

CURL-svaret returnerar listan med nycklar.  Om du till exempel hämtar de skrivskyddade nycklarna:  

```bash 
{"primaryReadonlyMasterKey":"bWpDxS...dzQ==",
"secondaryReadonlyMasterKey":"38v5ns...7bA=="}
```

Nu när du har åtkomstnyckeln för Cosmos DB-kontot kan du skicka den till en Cosmos DB-SDK och göra anrop för att komma åt kontot.  Som ett enkelt exempel kan du skicka åtkomstnyckeln till Azure CLI.  Du kan hämta <COSMOS DB CONNECTION URL> från fliken **Översikt** på bladet för Cosmos DB-kontot på Microsoft Azure-portalen.  Ersätt <ACCESS KEY> med värdet som returnerades ovan:

```bash
az cosmosdb collection show -c <COLLECTION ID> -d <DATABASE ID> --url-connection "<COSMOS DB CONNECTION URL>" --key <ACCESS KEY>
```

Det här CLI-kommandot returnerar information om samlingen:

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

I den här självstudien har du lärt dig hur du använder en systemtilldelad hanterad identitet på en virtuell Linux-dator för att få åtkomst till Cosmos DB.  Mer information om Cosmos DB finns här:

> [!div class="nextstepaction"]
>[Översikt över Azure Cosmos DB](/azure/cosmos-db/introduction)

