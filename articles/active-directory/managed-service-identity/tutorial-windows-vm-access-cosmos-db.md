---
title: Använd en Windows VM-MSI för att komma åt Azure Cosmos DB
description: En självstudiekurs som vägleder dig genom processen att använda en System-Assigned hanteras Service identitet (MSI) på en Windows-VM för att komma åt Azure Cosmos DB.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2018
ms.author: arluca
ms.openlocfilehash: d04bfe601760dcbf832aeec0cc66d8e1eda51178
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
---
# <a name="tutorial-use-a-windows-vm-msi-to-access-azure-cosmos-db"></a>Självstudier: Använda en Windows VM-MSI för att komma åt Azure Cosmos DB

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Den här kursen visar hur du skapar och använder en Windows VM-MSI för åtkomst till Cosmos DB. Lär dig att:

> [!div class="checklist"]
> * Skapa en MSI aktiverat Windows VM 
> * Skapa ett Cosmos DB-konto
> * Bevilja Windows VM MSI tillgång till åtkomstnycklarna för Cosmos-DB-konto
> * Få en åtkomsttoken med hjälp av Windows-VM MSI för att anropa Azure Resource Manager
> * Hämta åtkomstnycklar från Azure Resource Manager för att ringa Cosmos DB

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]


## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Skapa en virtuell Windows-dator i en ny resursgrupp

Den här självstudiekursen skapar vi en ny Windows virtuell dator.  Du kan också aktivera MSI på en befintlig virtuell dator.

1. Klicka på knappen **Skapa en resurs** längst upp till vänster i Azure Portal.
2. Välj **Compute**, och välj sedan **Windows Server 2016 Datacenter**. 
3. Ange informationen för den virtuella datorn. Den **användarnamn** och **lösenord** skapade här är de autentiseringsuppgifter som du använder för att logga in på den virtuella datorn.
4. Välj rätt **prenumeration** för den virtuella datorn i listrutan.
5. Att välja en ny **resursgruppen** som du vill skapa den virtuella datorn, Välj **Skapa nytt**. När du är klar klickar du på **OK**.
6. Välj storlek för den virtuella datorn. Om du vill se fler storlekar väljer du **Visa alla** eller så ändrar du filtret för **disktyper som stöds**. Behåll standardinställningarna på sidan Inställningar och klickar på **OK**.

   ![ALT bildtext](../media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>Aktivera MSI på den virtuella datorn 

En virtuell dator MSI kan du få åtkomst-token från Azure AD utan att behöva placera autentiseringsuppgifter i din kod. Under försättsbladen, aktivera MSI på en virtuell dator via Azure portal gör två saker: registrerar den virtuella datorn med Azure AD för att skapa en hanterad identitet och konfigurerar den identiteten på den virtuella datorn.

1. Välj den **virtuella** som du vill aktivera MSI på.  
2. Klicka på det vänstra navigeringsfältet **Configuration**. 
3. Du ser **hanterade tjänstidentiteten**. För att registrera och aktivera MSI-filerna, Välj **Ja**, om du vill inaktivera det, väljer du Nej. 
4. Se till att du klickar på **spara** att spara konfigurationen.  
   ![ALT bildtext](../media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

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

## <a name="grant-windows-vm-msi-access-to-the-cosmos-db-account-access-keys"></a>Bevilja Windows VM MSI tillgång till åtkomstnycklarna för Cosmos-DB-konto

Cosmos DB stöder inte Azure AD authentication internt. Du kan dock använda en MSI för att hämta en Cosmos-DB åtkomstnyckel från Resource Manager och använder för att komma åt Cosmos DB. I det här steget kan du ge din MSI tillgång till nycklarna på Cosmos-DB-kontot.

Om du vill ge MSI identitet åtkomst till Cosmos-DB-konto i Azure Resource Manager med hjälp av PowerShell uppdatera värdena för `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, och `<COSMOS DB ACCOUNT NAME>` för din miljö. Ersätt `<MSI PRINCIPALID>` med den `principalId` egenskap som returneras av den `az resource show` i [hämta principalID Linux VM MSI](#retrieve-the-principalID-of-the-linux-VM's-MSI).  Cosmos DB stöder två detaljnivåer när du använder åtkomstnycklar: full åtkomst till kontot och skrivskyddad åtkomst till kontot.  Tilldela den `DocumentDB Account Contributor` roll om du vill hämta läsning och skrivning nycklar för kontot eller tilldela den `Cosmos DB Account Reader Role` roll om du vill ha skrivskyddad nycklar för kontot:

```azurepowershell
$spID = (Get-AzureRMVM -ResourceGroupName myRG -Name myVM).identity.principalid
New-AzureRmRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/<myStorageAcct>"
```

## <a name="get-an-access-token-using-the-windows-vms-msi-to-call-azure-resource-manager"></a>Få en åtkomsttoken med hjälp av Windows-VM MSI för att anropa Azure Resource Manager

Vi kommer att fungera från den virtuella datorn som vi skapade tidigare under resten av kursen. 

Du behöver använda Azure Resource Manager PowerShell-cmdlets i den här delen.  Om du inte har installerats, [hämta den senaste versionen](https://docs.microsoft.com/powershell/azure/overview) innan du fortsätter.

Du måste också installera den senaste versionen av [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) på din Windows-VM.

1. I Azure-portalen går du till **virtuella datorer**, gå till din Windows-dator, sedan från den **översikt** klickar **Anslut** längst upp. 
2. Ange i din **användarnamn** och **lösenord** för som du har lagt till när du skapade den virtuella Windows-datorn. 
3. Nu när du har skapat en **anslutning till fjärrskrivbord** med den virtuella datorn öppnar du PowerShell i fjärrsessionen.
4. Använder PowerShells Invoke-WebRequest, gör en begäran till den lokala MSI-slutpunkten för att hämta ett åtkomsttoken för Azure Resource Manager.

    ```powershell
        $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Method GET -Headers @{Metadata="true"}
    ```

    > [!NOTE]
    > Värdet för ”resurser”-parametern måste vara en exakt matchning för vad som förväntas av Azure AD. När du använder Azure Resource Manager resurs-ID, måste du inkluderar det avslutande snedstrecket på URI.
    
    Extrahera sedan elementet ”innehåll”, som lagras som en JavaScript Object Notation (JSON) formaterad sträng i $response-objektet. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```
    Extrahera sedan den åtkomst-token från svaret.
    
    ```powershell
    $ArmToken = $content.access_token
    ```

## <a name="get-access-keys-from-azure-resource-manager-to-make-cosmos-db-calls"></a>Hämta åtkomstnycklar från Azure Resource Manager för att ringa Cosmos DB

Nu använda PowerShell för att anropa Resource Manager med den åtkomst-token som hämtades i föregående avsnitt för att hämta Cosmos DB åtkomstnyckel. När vi har snabbtangent fråga vi Cosmos DB. Se till att ersätta den `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, och `<COSMOS DB ACCOUNT NAME>` parametervärden med egna värden. Ersätt den `<ACCESS TOKEN>` värde med den åtkomsttoken som du hämtade tidigare.  Om du vill hämta nycklar för läsning och skrivning använda viktiga åtgärdstypen `listKeys`.  Om du vill hämta skrivskyddade nycklar använder typ av nyckel åtgärd `readonlykeys`:

```powershell
Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.DocumentDb/databaseAccounts/<COSMOS DB ACCOUNT NAME>/listKeys/?api-version=2016-12-01 -Method POST -Headers @{Authorization="Bearer $ARMToken"}
```
Svaret ger listan med nycklar.  Till exempel om du får skrivskyddade nycklar:

```powershell
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

- En översikt över MSI finns [hanterade tjänstidentiteten översikt](overview.md).
