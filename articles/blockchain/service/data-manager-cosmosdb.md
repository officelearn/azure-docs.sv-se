---
title: Använd Blockchain Data Manager för att uppdatera Azure Cosmos DB - Azure Blockchain Service
description: Använd Blockchain Data Manager för Azure Blockchain Service för att skicka blockchain-data till Azure Cosmos DB
ms.date: 03/08/2020
ms.topic: tutorial
ms.reviewer: chroyal
ms.openlocfilehash: 483a5246274f63549dfb2914361ede6aa001e02e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79533189"
---
# <a name="tutorial-use-blockchain-data-manager-to-send-data-to-azure-cosmos-db"></a>Självstudiekurs: Använd Blockchain Data Manager för att skicka data till Azure Cosmos DB

I den här självstudien använder du Blockchain Data Manager för Azure Blockchain Service för att registrera transaktionsdata för blockkedjor i Azure Cosmos DB. Blockchain Data Manager samlar in, transformerar och levererar blockchain-redovisningsdata till Azure Event Grid Topics. Från Azure Event Grid använder du en Azure Logic App-anslutning för att skapa dokument i en Azure Cosmos DB-databas. När du är klar med självstudiekursen kan du utforska blockchain-transaktionsdata i Azure Cosmos DB Data Explorer.

[![Information om blockchain-transaktion](./media/data-manager-cosmosdb/raw-msg.png)](./media/data-manager-cosmosdb/raw-msg.png#lightbox)

I den här kursen får du:

> [!div class="checklist"]
> * Skapa en Blockchain Data Manager-instans
> * Lägga till ett blockchain-program för att avkoda transaktionsegenskaper och händelser
> * Skapa ett Azure Cosmos DB-konto och databas för att lagra transaktionsdata
> * Skapa en Azure Logic App för att ansluta ett Azure Event Grid-ämne till Azure Cosmos DB
> * Skicka en transaktion till en blockchain-redovisning
> * Visa de avkodade transaktionsdata i Azure Cosmos DB

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Krav

* Fullständig [snabbstart: Skapa en blockchain-medlem med Azure-portalen](create-member.md) eller [Snabbstart: Skapa en Blockchain Blockchain-tjänst-blockkedjemedlem med Azure CLI](create-member-cli.md)
* Fullständig [snabbstart: Använd Visual Studio-kod för att ansluta till ett Azure Blockchain Service-konsortiumnätverk](connect-vscode.md). Snabbstarten hjälper dig att installera [Azure Blockchain Development Kit för Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) och konfigurera din blockchain-utvecklingsmiljö.
* Fullständig [självstudiekurs: Använd Visual Studio-kod för att skapa, skapa och distribuera smarta kontrakt](send-transaction.md). Självstudien går igenom att skapa ett exempel smart kontrakt.
* Skapa ett [ämne för händelserutnät](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)
* Lär dig mer om [händelsehanterare i Azure Event Grid](../../event-grid/event-handlers.md)

## <a name="create-instance"></a>Skapa instans

En Blockchain Data Manager-instans ansluter och övervakar en Azure Blockchain Service-transaktionsnod. En instans samlar in alla råblock- och råtransaktionsdata från transaktionsnoden. En utgående anslutning skickar blockchain-data till Azure Event Grid. Du konfigurerar en enda utgående anslutning när du skapar instansen.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Gå till Azure Blockchain Service-medlemmen som du skapade i den nödvändiga [snabbstarten: Skapa en blockchain-medlem med Azure-portalen](create-member.md). Välj **Blockchain Data Manager**.
1. Välj **Lägg till**.

    ![Lägg till Blockchain Data Manager](./media/data-manager-cosmosdb/add-instance.png)

    Ange följande uppgifter:

    Inställning | Exempel | Beskrivning
    --------|---------|------------
    Namn | mywatcher (på) | Ange ett unikt namn för en ansluten Blockchain Data Manager.
    Transaktionsnod | myblockchainmember | Välj standardtransaktionsnoden för Azure Blockchain Service-medlemmen som du skapade i förutsättningen.
    Anslutningsnamn | kosmosdb | Ange ett unikt namn på den utgående anslutningen där blockchain-transaktionsdata skickas.
    Slutpunkt för händelserutnät | myTopic (på)myTopic) | Välj ett ämne för händelserutnät som du skapade i förutsättningen. Blockchain Data Manager-instansen och händelserutnätets ämne måste finnas i samma prenumeration.

1. Välj **OK**.

    Det tar mindre än en minut att skapa en Blockchain Data Manager-instans. När instansen har distribuerats startas den automatiskt. En blockchain Data Manager-instans som körs samlar in blockchain-händelser från transaktionsnoden och skickar data till händelserutnätet.

## <a name="add-application"></a>Lägga till ett program

Lägg till **helloblockchain** blockchain-programmet så att Blockchain Data Manager avkodar händelse- och egenskapstillstånd. Blockchain Data Manager kräver smart kontrakt ABI och bytecode fil för att lägga till programmet.

### <a name="get-contract-abi-and-bytecode"></a>Hämta kontrakt ABI och bytecode

Kontraktet ABI definierar smarta kontraktsgränssnitt. Den beskriver hur man interagerar med det smarta kontraktet. Du kan använda [Azure Blockchain Development Kit för Ethereum-tillägget för](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) att kopiera kontraktet ABI till Urklipp.

1. I fönstret Utforskare för Visual Studio-kod expanderar du mappen **bygg/kontrakt** i projektet **helloblockchain** Solidity som du skapade i den nödvändiga [självstudien: Använd Visual Studio-kod för att skapa, skapa och distribuera smarta kontrakt](send-transaction.md).
1. Högerklicka på kontraktsmetadatafilen JSON. Filnamnet är det smarta kontraktsnamnet följt av **.json-tillägget.**
1. Välj **Kopiera kontrakt ABI**.

    ![Fönstret Visual Studio-kod med markeringen Kopiera kontrakt ABI](./media/data-manager-cosmosdb/abi-devkit.png)

    Kontraktet ABI kopieras till Urklipp.

1. Spara **abi-matrisen** som en JSON-fil. Till exempel *abi.json*. Du använder filen i ett senare steg.

Blockchain Data Manager kräver den distribuerade bytekoden för det smarta kontraktet. Den distribuerade bytekoden skiljer sig från den smarta kontraktsbytekoden. Du använder tillägget Azure blockchain-utvecklingspaket för att kopiera bytekoden till Urklipp.

1. Expandera mappen **build/contracts** i solidity-projektet i fönstret Visual Studio-kodutforskaren.
1. Högerklicka på kontraktsmetadatafilen JSON. Filnamnet är det smarta kontraktsnamnet följt av **.json-tillägget.**
1. Välj **Kopiera transaktionsbytekod**.

    ![Fönstret Kopia transaktionskod med markeringen Kopiera transaktionsbytekod](./media/data-manager-cosmosdb/bytecode-devkit.png)

    Bytekoden kopieras till Urklipp.

1. Spara **bytekodvärdet** som en JSON-fil. Till exempel *bytecode.json*. Du använder filen i ett senare steg.

I följande exempel visas *filer abi.json* och *bytecode.json* som är öppna i VS-kodredigeraren. Dina filer ska se likadana ut.

![Exempel på abi.json- och bytecode.json-filer](./media/data-manager-cosmosdb/contract-files.png)

### <a name="create-contract-abi-and-bytecode-url"></a>Skapa URL för kontrakt ABI och bytecode

Blockchain Data Manager kräver att abi- och bytekodfilerna är tillgängliga via en URL när du lägger till ett program. Du kan använda ett Azure Storage-konto för att ange en privat tillgänglig URL.

#### <a name="create-storage-account"></a>Skapa lagringskonto

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

#### <a name="upload-contract-files"></a>Ladda upp kontraktsfiler

1. Skapa en ny behållare för lagringskontot. Välj **Behållare > behållare**.

    ![Skapa en lagringskontobehållare](./media/data-manager-cosmosdb/create-container.png)

    | Inställning | Beskrivning |
    |---------|-------------|
    | Namn  | Ge containern ett namn. Till exempel *smartcontract* |
    | Offentlig åtkomstnivå | Välj *Privat (ingen anonym åtkomst)* |

1. Klicka på **OK** för att skapa containern.
1. Markera behållaren och välj sedan **Ladda upp**.
1. Välj båda JSON-filer som du har skapat i avsnittet [Hämta kontrakt ABI och bytecode.](#get-contract-abi-and-bytecode)

    ![Ladda upp blob](./media/data-manager-cosmosdb/upload-blobs.png)

    Välj **Överför**.

#### <a name="generate-url"></a>Generera URL

För varje blob genererar du en signatur för delad åtkomst.

1. Välj ABI JSON-bloben.
1. Välj **Generera SAS**
1. Ange önskad åtkomstsignatur förfallodatum och välj sedan **Generera blob SAS-token och URL**.

    ![Generera SAS-token](./media/data-manager-cosmosdb/generate-sas.png)

1. Kopiera **SAS-URL:en** för Blob och spara den för nästa avsnitt.
1. Upprepa stegen [Generera URL](#generate-url) för json-bloben för bytekoden.

### <a name="add-helloblockchain-application-to-instance"></a>Lägga till helloblockchain-program i instans

1. Välj din Blockchain Data Manager-instans i instanslistan.
1. Välj **Blockchain-program**.
1. Välj **Lägg till**.

    ![Lägga till ett blockchain-program](./media/data-manager-cosmosdb/add-application.png)

    Ange namnet på blockchain-programmet och ABI-url:erna för smart kontrakt och bytekod.

    Inställning | Beskrivning
    --------|------------
    Namn | Ange ett unikt namn som blockchain-programmet ska spåra.
    Kontrakt ABI | URL-sökvägen till ABI-filen för kontrakt. Mer information finns i [Skapa kontrakt ABI och bytecode URL](#create-contract-abi-and-bytecode-url).
    Bytekod för kontrakt | URL-sökvägen till bytekodfil. Mer information finns i [Skapa kontrakt ABI och bytecode URL](#create-contract-abi-and-bytecode-url).

1. Välj **OK**.

    När programmet har skapats visas programmet i listan över blockchain-program.

    ![Programlista för Blockchain](./media/data-manager-cosmosdb/artifact-list.png)

Du kan ta bort Azure Storage-kontot eller använda det för att konfigurera fler blockchain-program. Om du vill ta bort Azure Storage-kontot kan du ta bort resursgruppen. När du tar bort resursgruppen raderas även det kopplade lagringskontot och eventuella andra resurser som är kopplade till resursgruppen.

## <a name="create-azure-cosmos-db"></a>Skapa Azure Cosmos DB

[!INCLUDE [cosmos-db-create-storage-account](../../../includes/cosmos-db-create-dbaccount.md)]

### <a name="add-a-database-and-container"></a>Lägga till en databas och behållare

Du kan använda Data Explorer i Azure-portalen för att skapa en databas och behållare.

1. Välj **Data Explorer** från den vänstra navigeringen på din Azure Cosmos DB-kontosida och välj sedan Ny **behållare**.
1. Ange inställningarna för den nya behållaren i fönstret **Lägg till behållare.**

    ![Lägga till behållarinställningar](./media/data-manager-cosmosdb/add-container.png)

    | Inställning | Beskrivning
    |---------|-------------|
    | Databas-id | Ange **blockchain-data** som namn på den nya databasen. |
    | Dataflöde | Lämna dataflödet på **400** begäranheter per sekund (RU/s). Du kan skala upp dataflödet senare om du vill minska svarstiden.|
    | Container-ID | Ange **meddelanden** som namn på den nya behållaren. |
    | Partitionsnyckeln | Använd **/MessageType** som partitionsnyckel. |

1. Välj **OK**. Datautforskaren visar den nya databasen och behållaren som du skapade.

## <a name="create-logic-app"></a>Skapa en logikapp

Azure Logic Apps hjälper dig att schemalägga och automatisera affärsprocesser och arbetsflöden när du behöver integrera system och tjänster. Du kan använda en logikapp för att ansluta Event Grid till Azure Cosmos DB.

1. På [Azure-portalen](https://portal.azure.com) väljer du **Skapa en resurs** > **Integrering** > **Logikapp**.
1. Ange information om var du kan skapa logikappen. När du är klar väljer du **Skapa**.

    Mer information om hur du skapar logikappar finns i [Skapa automatiserade arbetsflöden med Azure Logic Apps](../../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. När Azure har distribuerat din app väljer du din logikappresurs.
1. Välj **Tom logikapp**under **Mallar**i Logic Apps Designer.

### <a name="add-event-grid-trigger"></a>Lägga till utlösare för händelserutnät

Varje logikapp måste börja med en utlösare som utlöses när en specifik händelse sker eller när ett särskilt villkor uppfylls. Varje gång utlösaren körs skapar Logic Apps-motorn en logikappinstans som startar och kör ditt arbetsflöde. Använd en Azure Event Grid-utlösare för att skicka transaktionsdata för blockkedjor från Event Grid till Cosmos DB.

1. Sök efter och välj Azure Event Grid-anslutningsappen i Logic Apps Designer. **Azure Event Grid**
1. Välj **När en resurshändelse inträffar**på fliken **Utlösare** .
1. Skapa en API-anslutning till ämnet Händelserutnät.

    ![Inställningar för utlösare av händelserutnät](./media/data-manager-cosmosdb/event-grid-trigger.png)

    | Inställning | Beskrivning
    |---------|-------------|
    | Prenumeration | Välj den prenumeration som innehåller ämnet Händelserutnät. |
    | Resurstyp | Välj **Microsoft.EventGrid.Topics**. |
    | Resursnamn | Välj namnet på avsnittet Event Grid där Blockchain Data Manager skickar transaktionsdatameddelanden. |

### <a name="add-cosmos-db-action"></a>Lägg till åtgärden Cosmos DB

Lägg till en åtgärd för att skapa ett dokument i Cosmos DB för varje transaktion. Använd transaktionsmeddelandetypen som partitionsnyckel för att kategorisera meddelandena.

1. Välj **Nytt steg**.
1. Sök efter **Azure Cosmos DB**i **Välj en åtgärd**.
1. Välj **Azure Cosmos DB > åtgärder > Skapa eller uppdatera dokument**.
1. Skapa en API-anslutning till cosmos DB-databasen.

    ![Cosmos DB-anslutningsinställningar](./media/data-manager-cosmosdb/cosmosdb-connection.png)

    | Inställning | Beskrivning
    |---------|-------------|
    | Anslutningsnamn | Välj den prenumeration som innehåller ämnet Händelserutnät. |
    | DocumentDB-konto | Välj det DocumentDB-konto som du skapade i avsnittet [Skapa Azure Cosmos DB-konto.](#create-azure-cosmos-db) |

1. Ange **databas-ID** och **insamlings-ID** för din Azure Cosmos DB som du skapade tidigare i avsnittet [Lägg till en databas och behållare.](#add-a-database-and-container)

1. Markera **dokumentinställningen.** I popup-programmet *Lägg till dynamiskt innehåll* väljer du **Uttryck** och kopierar och klistrar in följande uttryck:

    ```
    addProperty(triggerBody()?['data'], 'id', utcNow())
    ```

    Uttrycket hämtar datadelen av meddelandet och anger ID:n till ett tidsstämpelvärde.

1. Välj **Lägg till ny parameter** och välj **Partitionsnyckelvärde**.
1. Ange **värdet för partitionsnyckeln** till `"@{triggerBody()['data']['MessageType']}"`. Värdet måste omges av dubbla citattecken.

    ![Logic Apps Designer med Cosmos DB-inställningar](./media/data-manager-cosmosdb/create-action.png)

    Värdet anger partitionsnyckeln till transaktionsmeddelandetypen.

1. Välj **Spara**.

Logikappen övervakar ämnet Händelserutnät. När ett nytt transaktionsmeddelande skickas från Blockchain Data Manager skapar logikappen ett dokument i Cosmos DB.

## <a name="send-a-transaction"></a>Skicka en transaktion

Skicka sedan en transaktion till blockchain-redovisningen för att testa vad du har skapat. Använd **HelloBlockchain-kontraktets** **SendRequest-funktion** som du skapade i den nödvändiga [självstudiekursen: Använd Visual Studio-kod för att skapa, skapa och distribuera smarta kontrakt](send-transaction.md).

1. Använd sidan smart kontraktsinteraktion i Azure Blockchain-paketet för att anropa **funktionen SendRequest.** Högerklicka på **HelloBlockchain.sol** och välj **Visa sidan För interaktion med smarta kontrakt** på menyn.

    ![Välj Visa sidan Visa interaktion med smart kontrakt på menyn](./media/data-manager-cosmosdb/contract-interaction.png)

1. Välj **SendRequest** kontrakt åtgärder och ange **Hello, Blockchain!** för parametern **requestMessage.** Välj **Kör om** du vill anropa funktionen **SendRequest** via en transaktion.

    ![Utför SendRequest-åtgärd](./media/data-manager-cosmosdb/sendrequest-action.png)

Funktionen SendRequest anger fälten **RequestMessage** och **State.** Det aktuella tillståndet för **RequestMessage** är argumentet du skickade **Hello, Blockchain**. Fältet **Tillståndsvärde** förblir **begäran**.

## <a name="view-transaction-data"></a>Visa transaktionsdata

Nu när du har anslutit din Blockchain Data Manager till Azure Cosmos DB kan du visa blockchain-transaktionsmeddelanden i Cosmos DB Data Explorer.

1. Gå till vyn Cosmos DB Data Explorer. **Cosmosdb-blockchain-> Data Explorer > till exempel blockchain-data > Meddelanden > objekt**.

    ![Utforskare för Fmo DB-data](./media/data-manager-cosmosdb/data-explorer.png)

    Data Explorer visar de blockchain-datameddelanden som har skapats i Cosmos DB-databasen.

1. Bläddra igenom meddelandena genom att välja artikel-ID och hitta meddelandet med den matchande transaktionshhen.

    [![Information om blockchain-transaktion](./media/data-manager-cosmosdb/raw-msg.png)](./media/data-manager-cosmosdb/raw-msg.png#lightbox)

    Meddelandet om råtransaktioner innehåller information om transaktionen. Egenskapsinformationen är dock krypterad.

    Eftersom du har lagt till smart HelloBlockchain-kontraktet i Blockchain Data Manager-instansen skickas även en **ContractProperties-meddelandetyp** som innehåller avkodad egenskapsinformation.

1. Leta reda på meddelandet **ContractProperties** för transaktionen. Det bör vara nästa meddelande i listan.

    [![Information om blockchain-transaktion](./media/data-manager-cosmosdb/properties-msg.png)](./media/data-manager-cosmosdb/properties-msg.png#lightbox)

    Matrisen **Avkodadeegenskaper** innehåller transaktionens egenskaper.

Grattis! Du har skapat en transaktionsmeddelandeutforskare med Blockchain Data Manager och Azure Cosmos DB.

## <a name="clean-up-resources"></a>Rensa resurser

När det inte längre behövs kan du ta bort de resurser och resursgrupper som du använde för den här självstudien. Så här tar du bort en resursgrupp:

1. I Azure-portalen navigerar du till **resursgruppen** i det vänstra navigeringsfönstret och väljer den resursgrupp som du vill ta bort.
1. Välj **Ta bort resursgrupp**. Verifiera borttagning genom att ange resursgruppsnamnet och välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg

Läs mer om att integrera med blockchain-liggare.

> [!div class="nextstepaction"]
> [Använda Ethereum Blockchain-kopplingen med Azure Logic Apps](ethereum-logic-app.md)
