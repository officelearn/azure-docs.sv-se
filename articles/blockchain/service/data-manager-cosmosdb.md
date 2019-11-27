---
title: Använd blockchain Data Manager för att uppdatera Azure Cosmos DB-Azure blockchain-tjänsten
description: Använd blockchain Data Manager för Azure blockchain-tjänsten för att skicka blockchain-data till Azure Cosmos DB
ms.date: 11/04/2019
ms.topic: tutorial
ms.reviewer: chroyal
ms.openlocfilehash: 497652f91d46592212a17a0a22832c02a696df62
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326250"
---
# <a name="tutorial-use-blockchain-data-manager-to-send-data-to-azure-cosmos-db"></a>Självstudie: Använd blockchain Data Manager för att skicka data till Azure Cosmos DB

I den här självstudien använder du blockchain Data Manager för Azure blockchain-tjänsten för att registrera blockchain transaktions data i Azure Cosmos DB. Blockchain Data Manager fångar, transformerar och levererar blockchain-redovisningsinformation till Azure Event Grid ämnen. Från Azure Event Grid använder du en Azure Logic app-anslutning för att skapa dokument i en Azure Cosmos DB-databas. När du är klar med självstudien kan du utforska blockchain-transaktions data i Azure Cosmos DB Datautforskaren.

[information om ![blockchain-transaktioner](./media/data-manager-cosmosdb/raw-msg.png)](./media/data-manager-cosmosdb/raw-msg.png#lightbox)

I den här kursen har du:

> [!div class="checklist"]
> * Skapa en blockchain Data Manager instans
> * Lägg till ett blockchain-program för att avkoda transaktions egenskaper och händelser
> * Skapa ett Azure Cosmos DB konto och en databas för att lagra transaktions data
> * Skapa en Azure Logic-app för att ansluta ett Azure Event Grid ämne till Azure Cosmos DB
> * Skicka en transaktion till en blockchain-redovisning
> * Visa de avkodade transaktions data i Azure Cosmos DB

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

* Slutför [snabb start: skapa en blockchain-medlem med hjälp av Azure Portal](create-member.md) eller [snabb start: skapa en Azure blockchain service blockchain-medlem med Azure CLI](create-member-cli.md)
* Slutför [snabb start: Använd Visual Studio Code för att ansluta till ett Azure blockchain service Consortium-nätverk](connect-vscode.md). Snabb starten guidar dig när du installerar [Azure blockchain Development Kit för Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) och ställer in din blockchain Development-miljö.
* Fullständig [självstudie: Använd Visual Studio Code för att skapa, bygga och distribuera smarta kontrakt](send-transaction.md). I själv studie kursen får du hjälp med att skapa ett smart exempel kontrakt.
* Skapa ett [Event Grid ämne](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)
* Lär dig mer om [händelse hanterare i Azure Event Grid](../../event-grid/event-handlers.md)

## <a name="create-instance"></a>Skapa instans

En blockchain Data Manager-instans ansluter och övervakar en Azure blockchain service Transaction-nod. En instans fångar alla RAW-block och rå transaktions data från Transaction-noden. En utgående anslutning skickar blockchain-data till Azure Event Grid. Du konfigurerar en enda utgående anslutning när du skapar instansen.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Gå till den Azure blockchain service-medlem som du skapade i den nödvändiga [snabb starten: skapa en blockchain-medlem med hjälp av Azure Portal](create-member.md). Välj **Blockchain Data Manager**.
1. Välj **Lägg till**.

    ![Lägg till blockchain Data Manager](./media/data-manager-cosmosdb/add-instance.png)

    Ange följande uppgifter:

    Inställning | Exempel | Beskrivning
    --------|---------|------------
    Name | Watcher | Ange ett unikt namn för en ansluten blockchain Data Manager.
    Transaction Node | myblockchainmember | Välj noden standard transaktion för den Azure blockchain service-medlem som du skapade i förutsättningen.
    Anslutningsnamn | cosmosdb | Ange ett unikt namn på den utgående anslutningen där blockchain transaktions data skickas.
    Event Grid-slutpunkt | Avsnittet | Välj ett event Grid-ämne som du skapade i förutsättningen. Obs! Data Manager blockchain-instansen och avsnittet Event Grid måste vara i samma prenumeration.

1. Välj **OK**.

    Det tar mindre än en minut att skapa en blockchain Data Manager-instans. När instansen har distribuerats startas den automatiskt. En körning av en blockchain Data Manager-instans fångar blockchain-händelser från Transaction-noden och skickar data till Event Grid.

## <a name="add-application"></a>Lägga till ett program

Lägg till **helloblockchain** blockchain-programmet så att blockchain Data Manager avkodar händelse-och egenskaps tillstånd. Blockchain Data Manager kräver det smarta kontraktet ABI och bytekod-filen för att lägga till programmet.

### <a name="get-contract-abi-and-bytecode"></a>Hämta kontrakts ABI och bytekod

ABI för kontrakt definierar de smarta kontrakts gränssnitten. Den beskriver hur du interagerar med det smarta kontraktet. Du kan använda [Azure blockchain Development Kit för Ethereum-tillägget](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) för att kopiera kontrakts ABI till Urklipp.

1. I fönstret Visual Studio Code Explorer expanderar du mappen **build/** Contracts i det **helloblockchain** -täcknings projekt som du skapade i den nödvändiga [självstudien: Använd Visual Studio Code för att skapa, bygga och distribuera smarta kontrakt](send-transaction.md).
1. Högerklicka på JSON-filen för kontraktets metadata. Fil namnet är det smarta kontrakt namnet följt av **. JSON** -tillägget.
1. Välj **kopiera kontrakt ABI**.

    ![Fönstret Visual Studio Code med alternativet kopiera kontrakt ABI](./media/data-manager-cosmosdb/abi-devkit.png)

    Kontraktets ABI kopieras till Urklipp.

1. Spara **ABI** -matrisen som en JSON-fil. Till exempel *ABI. JSON*. Du använder filen i ett senare steg.

Blockchain-Data Manager kräver distribuerad bytekod för det smarta kontraktet. Den distribuerade bytekod-filen skiljer sig från det smarta kontraktets bytekod. Du kan hämta den distribuerade bytekod-filen från den kompilerade kontraktets metadatafil.

1. Öppna den kontrakts ETA data fil som finns i mappen **build/kontrakt** i ditt projekt. Fil namnet är det smarta kontrakt namnet följt av **. JSON** -tillägget.
1. Hitta **deployedBytecode** -ELEMENTET i JSON-filen.
1. Kopiera det hexadecimala värdet utan citat tecken.

    ![Visual Studio Code-fönstret med bytekod i metadata](./media/data-manager-portal/bytecode-metadata.png)

1. Spara **bytekod** -värdet som en JSON-fil. Till exempel, *bytekod. JSON*. Du använder filen i ett senare steg.

I följande exempel visas *ABI. JSON* och *bytekod. JSON* -filer öppna i vs Code-redigeraren. Dina filer bör se ut ungefär så här.

![Exempel på ABI. JSON och bytekod. JSON-filer](./media/data-manager-cosmosdb/contract-files.png)

### <a name="create-contract-abi-and-bytecode-url"></a>Skapa kontrakts ABI och bytekod-URL

Blockchain Data Manager kräver att kontrakts ABI och bytekod-filerna kan nås av en URL när du lägger till ett program. Du kan använda ett Azure Storage-konto för att tillhandahålla en privat tillgänglig URL.

#### <a name="create-storage-account"></a>Skapa lagringskonto

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

#### <a name="upload-contract-files"></a>Ladda upp kontraktmallar

1. Skapa en ny behållare för lagrings kontot. Välj **behållare > behållare**.

    ![Skapa en lagrings konto behållare](./media/data-manager-cosmosdb/create-container.png)

    | Inställning | Beskrivning |
    |---------|-------------|
    | Name  | Namnge behållaren. Till exempel *smartcontract* |
    | Offentlig åtkomst nivå | Välj *privat (ingen anonym åtkomst)* |

1. Klicka på **OK** för att skapa containern.
1. Välj behållaren och välj sedan **Ladda upp**.
1. Välj båda JSON-filerna som du skapade i avsnittet [Hämta kontrakt ABI och bytekod](#get-contract-abi-and-bytecode) .

    ![Ladda upp BLOB](./media/data-manager-cosmosdb/upload-blobs.png)

    Välj **Överför**.

#### <a name="generate-url"></a>Generera URL

Generera en signatur för delad åtkomst för varje blob.

1. Välj ABI JSON-blobben.
1. Välj **generera SAS**
1. Ange önskat förfallo datum för signaturen **och välj sedan generera BLOB SAS-token och URL**.

    ![Generera SAS-token](./media/data-manager-cosmosdb/generate-sas.png)

1. Kopiera **URL: en för BLOB SAS** och spara den för nästa avsnitt.
1. Upprepa stegen för att [generera URL: er](#generate-url) för BYTEKOD-JSON-blobben.

### <a name="add-helloblockchain-application-to-instance"></a>Lägg till helloblockchain-program till instans

1. Välj din blockchain-Data Manager instans från instans listan.
1. Välj **blockchain-program**.
1. Välj **Lägg till**.

    ![Lägg till ett blockchain-program](./media/data-manager-cosmosdb/add-application.png)

    Ange namnet på blockchain-programmet och URL: en för smart kontrakt ABI och bytekod.

    Inställning | Beskrivning
    --------|------------
    Name | Ange ett unikt namn för blockchain-programmet som ska spåras.
    Kontrakt ABI | URL-sökväg till kontraktets ABI-fil. Mer information finns i [skapa kontrakts ABI och BYTEKOD URL](#create-contract-abi-and-bytecode-url).
    Kontraktets bytekod | URL-sökväg till bytekod-fil. Mer information finns i [skapa kontrakts ABI och BYTEKOD URL](#create-contract-abi-and-bytecode-url).

1. Välj **OK**.

    När programmet har skapats visas programmet i listan över blockchain-program.

    ![Lista över blockchain program](./media/data-manager-cosmosdb/artifact-list.png)

Du kan ta bort Azure Storage kontot eller använda det för att konfigurera fler blockchain-program. Om du vill ta bort Azure Storages kontot kan du ta bort resurs gruppen. När du tar bort resursgruppen raderas även det kopplade lagringskontot och eventuella andra resurser som är kopplade till resursgruppen.

## <a name="create-azure-cosmos-db"></a>Skapa Azure Cosmos DB

[!INCLUDE [cosmos-db-create-storage-account](../../../includes/cosmos-db-create-dbaccount.md)]

### <a name="add-a-database-and-container"></a>Lägg till en databas och behållare

Du kan använda Datautforskaren i Azure Portal för att skapa en databas och behållare.

1. Välj **datautforskaren** i det vänstra navigerings fältet på ditt Azure Cosmos DB konto och välj sedan **ny behållare**.
1. I fönstret **Lägg till behållare** anger du inställningarna för den nya behållaren.

    ![Lägg till behållar inställningar](./media/data-manager-cosmosdb/add-container.png)

    | Inställning | Beskrivning
    |---------|-------------|
    | Databas-id | Ange **blockchain-data** som namn på den nya databasen. |
    | Dataflöde | Lämna data flödet på **400** enheter för programbegäran per sekund (ru/s). Du kan skala upp dataflödet senare om du vill minska svarstiden.|
    | Container-ID | Ange **meddelanden** som namn på den nya behållaren. |
    | Partitionsnyckeln | Använd **/MessageType** som partitionsnyckel. |

1. Välj **OK**. Datautforskaren visar den nya databasen och den behållare som du skapade.

## <a name="create-logic-app"></a>Skapa en logikapp

Azure Logic Apps hjälper dig att schemalägga och automatisera affärs processer och arbets flöden när du behöver integrera system och tjänster. Du kan använda en Logic app för att ansluta Event Grid till Azure Cosmos DB.

1. På [Azure-portalen](https://portal.azure.com) väljer du **Skapa en resurs** > **Integrering** > **Logikapp**.
1. Ange information om var du vill skapa din Logic app. När du är klar väljer du **skapa**.

    Mer information om hur du skapar Logic Apps finns i [skapa automatiserade arbets flöden med Azure Logic Apps](../../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. När Azure har distribuerat din app väljer du din Logic app-resurs.
1. I Logic Apps designer väljer du **Tom Logic app**under **mallar**.

### <a name="add-event-grid-trigger"></a>Lägg till Event Grid-utlösare

Varje logikapp måste börja med en utlösare som utlöses när en specifik händelse sker eller när ett särskilt villkor uppfylls. Varje gång utlösaren körs skapar Logic Apps-motorn en logikappinstans som startar och kör ditt arbetsflöde. Använd en Azure Event Grid-utlösare för att skicka blockchain transaktions data från Event Grid till Cosmos DB.

1. I Logic Apps designer söker du efter och väljer **Azure Event Grid** -anslutningen.
1. Välj **när en resurs händelse inträffar**från fliken **utlösare** .
1. Skapa en API-anslutning till Event Grid ämnet.

    ![Inställningar för Event Grid-utlösare](./media/data-manager-cosmosdb/event-grid-trigger.png)

    | Inställning | Beskrivning
    |---------|-------------|
    | Prenumeration | Välj den prenumeration som innehåller Event Grid ämnet. |
    | Resurstyp | Välj **Microsoft. EventGrid. ämnen**. |
    | Resursnamn | Välj namnet på Event Grid ämnet där blockchain Data Manager skickar transaktions data meddelanden. |

### <a name="add-cosmos-db-action"></a>Lägg till Cosmos DB åtgärd

Lägg till en åtgärd för att skapa ett dokument i Cosmos DB för varje transaktion. Använd transaktions meddelande typen som partitionsnyckel för att kategorisera meddelandena.

1. Välj **Nytt steg**.
1. I **Välj en åtgärd**söker du efter **Azure Cosmos DB**.
1. Välj **Azure Cosmos DB > åtgärder > skapa eller uppdatera dokument**.
1. Skapa en API-anslutning till Cosmos DB databasen.

    ![Cosmos DB anslutnings inställningar](./media/data-manager-cosmosdb/cosmosdb-connection.png)

    | Inställning | Beskrivning
    |---------|-------------|
    | Anslutningsnamn | Välj den prenumeration som innehåller Event Grid ämnet. |
    | DocumentDB-konto | Välj det DocumentDB-konto som du skapade i avsnittet [skapa Azure Cosmos DB konto](#create-azure-cosmos-db) . |

1. Ange **databas-ID** och **samlings-ID** för Azure Cosmos DB som du skapade tidigare i avsnittet [Lägg till en databas och container](#add-a-database-and-container) .

1. Välj **dokument** inställningen. I popup-fönstret *Lägg till dynamiskt innehåll* väljer du **uttryck** och kopiera och klistra in följande uttryck:

    ```
    addProperty(triggerBody()?['data'], 'id', utcNow())
    ```

    Uttrycket hämtar data delen av meddelandet och anger ID: t till ett tidsstämpel-värde.

1. Välj **Lägg till ny parameter** och välj **partitionerings nyckel värde**.
1. Ange **värdet för partitionsnyckel** till `"@{triggerBody()['data']['MessageType']}"`. Värdet måste omges av dubbla citat tecken.

    ![Logic Apps designer med Cosmos DB inställningar](./media/data-manager-cosmosdb/create-action.png)

    Värdet anger partitionsnyckel till typ av transaktions meddelande.

1. Välj **Spara**.

Den logiska appen övervakar Event Grid ämnet. När ett nytt transaktions meddelande skickas från blockchain Data Manager, skapar Logic-appen ett dokument i Cosmos DB.

## <a name="send-a-transaction"></a>Skicka en transaktion

Skicka sedan en transaktion till blockchain-redovisningen för att testa det du har skapat. Använd det **SendRequest. js** -skript som du skapade i den nödvändiga [självstudien: Använd Visual Studio Code för att skapa, bygga och distribuera smarta kontrakt](send-transaction.md).

I rutan för VS-kodens terminalfönster använder du Truffle för att köra skriptet i ditt konsortium blockchain-nätverk. I meny raden i terminalfönstret väljer du fliken **Terminal** och **PowerShell** i list rutan.

``` PowerShell
truffle exec sendrequest.js --network <blockchain network>
```

Ersätt \<blockchain Network\> med namnet på blockchain-nätverket som definierats i **Truffle-config. js**.

![Skicka transaktion](./media/data-manager-cosmosdb/send-request.png)

## <a name="view-transaction-data"></a>Visa transaktions data

Nu när du har anslutit dina blockchain-Data Manager till Azure Cosmos DB kan du Visa blockchain transaktions meddelanden i Cosmos DB Datautforskaren.

1. Gå till vyn Cosmos DB Datautforskaren. Till exempel **cosmosdb-blockchain > Datautforskaren > blockchain – Data > meddelanden > objekt**.

    ![Cosmos DB Datautforskaren](./media/data-manager-cosmosdb/data-explorer.png)

    Datautforskaren listar de blockchain-datameddelanden som skapades i Cosmos DB-databasen.

1. Bläddra igenom meddelandena genom att välja objekt-ID och hitta meddelandet med den matchande transaktionens hash.

    [information om ![blockchain-transaktioner](./media/data-manager-cosmosdb/raw-msg.png)](./media/data-manager-cosmosdb/raw-msg.png#lightbox)

    Meddelandet rå transaktion innehåller information om transaktionen. Men egenskaps informationen är krypterad.

    Eftersom du har lagt till det smarta HelloBlockchain-kontraktet till blockchain Data Manager-instansen, skickas även en **ContractProperties** meddelande typ som innehåller avkodad egenskaps information.

1. Hitta **ContractProperties** -meddelandet för transaktionen. Det ska vara nästa meddelande i listan.

    [information om ![blockchain-transaktioner](./media/data-manager-cosmosdb/properties-msg.png)](./media/data-manager-cosmosdb/properties-msg.png#lightbox)

    **DecodedProperties** -matrisen innehåller egenskaperna för transaktionen.

Grattis! Du har skapat en transaktions meddelande Utforskare med blockchain Data Manager och Azure Cosmos DB.

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs kan du ta bort de resurser och resurs grupper som du använde för den här självstudien. Så här tar du bort en resurs grupp:

1. I Azure Portal navigerar du till **resurs grupp** i det vänstra navigerings fönstret och väljer den resurs grupp som du vill ta bort.
1. Välj **Ta bort resursgrupp**. Verifiera borttagning genom att ange resurs gruppens namn och välj **ta bort**.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om integrering med blockchain-redovisning.

> [!div class="nextstepaction"]
> [Använda Ethereum blockchain-anslutningen med Azure Logic Apps](ethereum-logic-app.md)
