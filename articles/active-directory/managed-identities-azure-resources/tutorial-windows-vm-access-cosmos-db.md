---
title: 'Självstudie: Använd en hanterad identitet för att komma åt Azure Cosmos DB-Windows-Azure AD'
description: En självstudiekurs som beskriver steg för steg hur du använder en systemtilldelad hanterad identitet på en virtuell Windows-dator för att få åtkomst till Azure Cosmos DB.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4cd1fb7f33c56aefe76bc55181ae92ca3d149754
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006988"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-cosmos-db"></a>Självstudie: Använda en systemtilldelad hanterad identitet för en virtuell Windows-dator för åtkomst till Azure Cosmos DB

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

I den här självstudien lär du dig att komma åt Cosmos DB med en systemtilldelad hanterad identitet för en virtuell Windows-dator. Lär dig att:

> [!div class="checklist"]
> * Skapa ett Cosmos DB-konto
> * Ge den virtuella Windows-datorns systemtilldelade hanterade identitet åtkomst till åtkomstnycklarna för Cosmos DB-kontot
> * Hämta en åtkomsttoken med hjälp av den virtuella Windows-datorns systemtilldelade hanterade identitet för att anropa Azure Resource Manager
> * Hämta åtkomstnycklar från Azure Resource Manager för att göra Cosmos DB-anrop

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

- Installera den senaste versionen av [Azure PowerShell](/powershell/azure/install-az-ps)


## <a name="enable"></a>Aktivera

[!INCLUDE [msi-tut-enable](../../../includes/active-directory-msi-tut-enable.md)]



## <a name="grant-access"></a>Bevilja åtkomst


### <a name="create-a-cosmos-db-account"></a>Skapa ett Cosmos DB-konto 

Om du inte redan har ett Cosmos DB-konto skapar du ett. Du kan hoppa över det här steget och använda ett befintligt Cosmos DB-konto. 

1. Klicka på knappen **+/Skapa ny tjänst** som finns i övre vänstra hörnet i Azure-portalen.
2. Klicka på **Databaser** och sedan på **Azure Cosmos DB**. Panelen ”Nytt konto” visas.
3. Ange ett **ID** för Cosmos DB-kontot som du ska använda senare.  
4. **API** bör ha värdet ”SQL”. Metoden som beskrivs i den här självstudien kan användas med de andra tillgängliga API-typerna, men stegen i den här självstudien gäller för SQL-API:et.
5. Kontrollera att informationen under **Prenumeration** och **Resursgrupp** matchar informationen som du angav när du skapade den virtuella datorn i föregående steg.  Välj en **plats** där Cosmos DB är tillgänglig.
6. Klicka på **Skapa**.

### <a name="create-a-collection"></a>Skapa en samling 

Lägg till en datasamling till Cosmos DB-kontot som du kan skicka frågor mot i senare steg.

1. Gå till Cosmos DB-kontot som du precis skapat.
2. Klicka på knappen **+/Lägg till samling** på fliken **Översikt**. Panelen ”Lägg till samling” visas.
3. Tilldela samlingen ett databas-ID och ett samlings-ID, välj en lagringskapacitet, ange en partitionsnyckel, ange ett värde för dataflöde och klicka sedan på **OK**.  I den här självstudien räcker det att du använder ”Test” som databas-ID och samlings-ID, väljer en fast lagringskapacitet och det lägsta dataflödet (400 RU/s).  


### <a name="grant-access-to-the-cosmos-db-account-access-keys"></a>Bevilja åtkomst till Cosmos DB kontots åtkomst nycklar

Det här avsnittet visar hur du beviljar Windows VM-systemtilldelad åtkomst till hanterad identitet till Cosmos DB kontots åtkomst nycklar. Cosmos DB har inte inbyggt stöd för Azure AD-autentisering. Du kan emellertid använda en systemtilldelad hanterad identitet för att hämta en åtkomstnyckel för Cosmos DB från Resource Manager och sedan använda nyckeln för att komma åt Cosmos DB. I det här steget ger du den virtuella Windows-datorns systemtilldelade hanterade identitet åtkomst till nycklarna till Cosmos DB-kontot.

Om du ska ge den virtuella Windows-datorns systemtilldelade hanterade identitet åtkomst till Cosmos DB-kontot i Azure Resource Manager med hjälp av PowerShell uppdaterar du värdet för `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` och `<COSMOS DB ACCOUNT NAME>` för din miljö. Cosmos DB stöder två detaljnivåer när åtkomstnycklar används: läs- och skrivåtkomst till kontot samt skrivskyddad åtkomst till kontot.  Tilldela rollen `DocumentDB Account Contributor` om du vill hämta läs-/skrivnycklar för kontot, eller rollen `Cosmos DB Account Reader Role` om du vill hämta skrivskyddade nycklar för kontot.  För den här självstudien tilldelar du `Cosmos DB Account Reader Role`:

```azurepowershell
$spID = (Get-AzVM -ResourceGroupName myRG -Name myVM).identity.principalid
New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Cosmos DB Account Reader Role" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDb/databaseAccounts/<COSMOS DB ACCOUNT NAME>"
```

>[!NOTE]
> Tänk på att om du inte kan utföra en åtgärd kanske du inte har rätt behörighet. Om du vill ha skriv åtkomst till nycklar måste du använda en Azure-roll som DocumentDB konto deltagare eller skapa en anpassad roll. Mer information finns [i Azure rollbaserad åtkomst kontroll i Azure Cosmos DB](../../cosmos-db/role-based-access-control.md)

## <a name="access-data"></a>Åtkomst till data

Det här avsnittet visar hur du anropar Azure Resource Manager med hjälp av en åtkomsttoken för den hanterade identiteten för den virtuella Windows-datorn. Under resten av självstudiekursen arbetar vi från den virtuella datorn som vi skapade tidigare. 

Du måste installera den senaste versionen av [Azure CLI](/cli/azure/install-azure-cli) på din virtuella Windows-dator.



### <a name="get-an-access-token"></a>Hämta en åtkomsttoken

1. Gå till **Virtuella datorer** på Azure Portal, gå till din virtuella Windows-dator och klicka sedan på **Anslut** längst upp på sidan **Översikt**. 
2. Ange ditt **användarnamn** och **lösenord** som du lade till när du skapade den virtuella Windows-datorn. 
3. Nu när du har skapat en **anslutning till fjärrskrivbord** med den virtuella datorn öppnar du PowerShell i fjärrsessionen.
4. Använd PowerShells Invoke-WebRequest och skicka en begäran till den lokala slutpunkten för hanterade identiteter för Azure-resurser för att hämta en åtkomsttoken för Azure Resource Manager.

   ```powershell
   $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Method GET -Headers @{Metadata="true"}
   ```

   > [!NOTE]
   > Värdet för parametern ”resource” måste vara en exakt matchning av vad som förväntas av Azure AD. När du använder Azure Resource Manager-resurs-ID:t måste du ta med det avslutande snedstrecket i URI:n.
    
   Extrahera sedan elementet ”Content”, som lagras som en JSON-formaterad sträng (JavaScript Object Notation) i $response-objektet. 
    
   ```powershell
   $content = $response.Content | ConvertFrom-Json
   ```
   Extrahera sedan åtkomsttoken från svaret.
    
   ```powershell
   $ArmToken = $content.access_token
   ```

### <a name="get-access-keys"></a>Hämta åtkomst nycklar 

Det här avsnittet visar hur du hämtar åtkomst nycklar från Azure Resource Manager för att göra Cosmos DB-anrop. Nu använder du PowerShell för att anropa Resource Manager med hjälp av den åtkomsttoken som du fick i föregående avsnitt för att hämta åtkomstnyckeln för Cosmos DB-kontot. När vi väl har åtkomstnyckeln kan vi skicka frågor mot Cosmos DB. Ersätt parametervärdena `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` och `<COSMOS DB ACCOUNT NAME>` med dina egna värden. Ersätt värdet `<ACCESS TOKEN>` med den åtkomsttoken som du hämtade tidigare.  Om du vill hämta läs- och skrivnycklar använder du nyckelåtgärdstypen `listKeys`.  Om du vill hämta skrivskyddade nycklar använder du nyckelåtgärdstypen `readonlykeys`:

```powershell
Invoke-WebRequest -Uri 'https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.DocumentDb/databaseAccounts/<COSMOS DB ACCOUNT NAME>/listKeys/?api-version=2016-03-31' -Method POST -Headers @{Authorization="Bearer $ARMToken"}
```
Svaret returnerar listan med nycklar.  Om du till exempel hämtar de skrivskyddade nycklarna:

```powershell
{"primaryReadonlyMasterKey":"bWpDxS...dzQ==",
"secondaryReadonlyMasterKey":"38v5ns...7bA=="}
```
Nu när du har åtkomstnyckeln för Cosmos DB-kontot kan du skicka den till en Cosmos DB-SDK och göra anrop för att komma åt kontot.  Som ett enkelt exempel kan du skicka åtkomstnyckeln till Azure CLI.  Du kan hämta `<COSMOS DB CONNECTION URL>` från fliken **Översikt** på bladet för Cosmos DB-kontot på Microsoft Azure-portalen.  Ersätt `<ACCESS KEY>` med värdet som returnerades ovan:

```azurecli
az cosmosdb collection show -c <COLLECTION ID> -d <DATABASE ID> --url-connection "<COSMOS DB CONNECTION URL>" --key <ACCESS KEY>
```

Det här CLI-kommandot returnerar information om samlingen:

```output
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


## <a name="disable"></a>Inaktivera

[!INCLUDE [msi-tut-disable](../../../includes/active-directory-msi-tut-disable.md)]



## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du använder en systemtilldelad identitet för en virtuell Windows-dator för att få åtkomst till Cosmos DB.  Mer information om Cosmos DB finns här:

> [!div class="nextstepaction"]
>[Översikt över Azure Cosmos DB](../../cosmos-db/introduction.md)