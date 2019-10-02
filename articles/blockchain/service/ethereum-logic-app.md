---
title: Så här använder du Ethereum blockchain-anslutningen med Azure Logic Apps
description: Använda Ethereum blockchain-anslutningen med Azure Logic Apps för att utlösa smarta kontrakts funktioner och reagera på smarta kontrakt händelser.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 09/26/2019
ms.topic: tutorial
ms.service: azure-blockchain
ms.reviewer: chrisseg
manager: femila
ms.openlocfilehash: 48f862bea6df50480ce4f9d21993693da34cfbc9
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2019
ms.locfileid: "71720403"
---
# <a name="how-to-use-ethereum-blockchain-connector-with-azure-logic-apps"></a>Använda Ethereum blockchain Connector med Azure Logic Apps

Använd [Ethereum blockchain-anslutningen](https://docs.microsoft.com/connectors/blockchainethereum/) med [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/) för att utföra åtgärder för smarta kontrakt och reagera på smarta kontrakt händelser. Till exempel vill du skapa en REST-baserad mikrotjänst som returnerar information från en blockchain-redovisning. Med hjälp av en Logic app kan du godkänna HTTP-begäranden som efterfrågar information som lagras i en blockchain-redovisning.

## <a name="prerequisites"></a>Förutsättningar

* Slutför de valfria [Quickstart: Använd Visual Studio Code för att ansluta till ett Azure blockchain service Consortium](connect-vscode.md)-nätverk. Snabb starten guidar dig när du installerar [Azure blockchain Development Kit för Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) och ställer in din blockchain Development-miljö.

## <a name="create-a-logic-app"></a>Skapa en logikapp

Azure Logic Apps hjälpa dig att schemalägga, automatisera affärs processer och arbets flöden när du behöver integrera system och tjänster. Först skapar du en logik som använder Ethereum blockchain-anslutningen.

1. I [Azure Portal](https://portal.azure.com)väljer du **skapa en resurs > integration > Logic app**.
1. Under **skapa logisk app**, anger du information var du vill skapa din Logic app. När du är klar väljer du **skapa**.

    Mer information om hur du skapar Azure Logic Apps finns i [skapa Azure Logic Apps](../../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. När Azure har distribuerat din app väljer du din Logic app-resurs.
1. I Logic Apps designer väljer du **Tom Logic app**under **mallar**.

Varje logikapp måste börja med en utlösare som utlöses när en specifik händelse sker eller när ett särskilt villkor uppfylls. Varje gång utlösaren körs skapar Logic Apps-motorn en logikappinstans som startar och kör ditt arbetsflöde.

Ethereum blockchain-anslutningen har en utlösare och flera åtgärder. Vilken utlösare eller åtgärd du använder beror på ditt scenario.

Välj något av följande avsnitt om arbets flödet:

* Utlöses när en händelse inträffar i blockchain [med hjälp av händelse utlösaren](#use-the-event-trigger).
* Frågar eller distribuerar ett smart kontrakt, [Använd åtgärder](#use-actions).
* Följ ett vanligt scenario och [generera ett arbets flöde med Developer Kit](#generate-a-workflow).

## <a name="use-the-event-trigger"></a>Använda händelse utlösaren

Använd händelse utlösare för Ethereum blockchain när du vill att en Logic app ska köras efter att ett smart kontrakts händelse inträffar. Till exempel vill du skicka ett e-postmeddelande när ett smart kontrakts funktion anropas.

1. I Logic App Designer väljer du Ethereum blockchain-anslutningen.
1. Välj **när ett smart kontrakt händelse inträffar**på fliken **utlösare** .
1. Ändra eller [skapa en API-anslutning](#create-an-api-connection) till din Azure blockchain-tjänst.
1. Ange information om det smarta kontrakt som du vill söka efter händelser i.

    ![Egenskaper för händelse utlösare](./media/ethereum-logic-app/event-properties.png)

    | Egenskap | Beskrivning |
    |----------|-------------|
    | **Kontrakt ABI** | ABI (kontrakt programmets binära gränssnitt) definierar de smarta kontrakts gränssnitten. Så här [hämtar du kontrakts ABI](#get-contract-abi). |
    | **Smart kontrakt adress** | Kontrakt adressen är mål adressen för det smarta kontraktet på Ethereum-blockchain. Så här [hämtar du kontrakt adressen](#get-contract-address). |
    | **Händelse namn** | Välj ett smart kontrakt händelse att kontrol lera. Händelsen utlöser Logic-appen. |
    | **Intervall** och **frekvens** | Välj hur ofta du vill söka efter händelsen. |

1. Välj **Spara**.

Om du vill slutföra din Logic-app kan du lägga till ett nytt steg som utför en åtgärd baserat på händelse utlösaren Ethereum blockchain. Skicka till exempel ett e-postmeddelande.

## <a name="use-actions"></a>Använda åtgärder

Använd Ethereum blockchain-åtgärder när du vill att en Logic app ska utföra en åtgärd på blockchain-redovisningen. Du vill till exempel skapa en REST-baserad mikrotjänst som anropar ett smart kontrakts funktion när en HTTP-begäran görs till en Logic-app.

Kopplings åtgärder kräver en utlösare. Du kan använda en Ethereum blockchain Connector-åtgärd som nästa steg efter en utlösare. Till exempel en HTTP-begäran utlösare för en microsservice.

1. I Logic App Designer väljer du **nytt steg** efter en utlösare.
1. Välj Ethereum blockchain-anslutningen.
1. Välj en av de tillgängliga åtgärderna på fliken **åtgärder** .

    ![Åtgärds egenskaper](./media/ethereum-logic-app/action-properties.png)

1. Ändra eller [skapa en API-anslutning](#create-an-api-connection) till din Azure blockchain-tjänst.
1. Ange följande information om funktionen Smart kontrakt beroende på vilken åtgärd du har valt.

    | Egenskap | Beskrivning |
    |----------|-------------|
    | **Kontrakt ABI** | ABI (kontrakt programmets binära gränssnitt) definierar de smarta kontrakts gränssnitten. Så här [hämtar du kontrakts ABI](#get-contract-abi). |
    | **Kontraktets bytekod** | Det kompilerade bytet för smart kontrakt. Så här [hämtar du kontraktets bytekod](#get-contract-bytecode). |
    | **Smart kontrakt adress** | Kontrakt adressen är mål adressen för det smarta kontraktet på Ethereum-blockchain. Så här [hämtar du kontrakt adressen](#get-contract-address). |
    | **Namn på Smart kontrakts funktion** | Välj funktions namnet Smart kontraktmall för åtgärden. Listan fylls i från informationen i kontrakts ABI. |

    När du har valt ett smart kontrakts funktions namn kan du se obligatoriska fält för funktions parametrar. Ange värdena eller det dynamiska innehåll som krävs för ditt scenario.

Du kan nu använda din Logic app. När Logic app-händelsen utlöses körs åtgärden Ethereum blockchain. En HTTP-begäran-utlösare kör till exempel en Ethereum blockchain-åtgärd för att fråga ett status värde för smart kontrakt som resulterar i ett HTTP-svar som returnerar värdet.

## <a name="generate-a-workflow"></a>Skapa ett arbets flöde

Azure blockchain Development Kit för Ethereum Visual Studio Code Extension kan generera Logic app-arbetsflöden för vanliga scenarier. Det finns fyra scenarier:

* Data publicering till en Azure SQL Database
* Händelse publicering till en Azure Event Grid eller Azure Service Bus
* Rapport publicering
* REST-baserad mikrotjänst

 Azure blockchain Development Kit använder Truffle för att förenkla blockchain-utvecklingen. Om du vill generera en Logi Kap par baserat på ett smart kontrakt behöver du en Truffle-lösning för det smarta kontraktet. Du behöver också en anslutning till ditt Azure blockchain service Consortium-nätverk. Mer information finns i [använda Visual Studio Code för att ansluta till en Azure blockchain service Consortium-snabb start](connect-vscode.md).

Följande steg genererar till exempel en REST-baserad Logic-app som baseras på det smarta **HelloBlockchain** snabb starts avtal:

1. I sido rutan i VS Code-Utforskaren expanderar du mappen **kontrakt** i din lösning.
1. Högerklicka på **HelloBlockchain. sol** och välj **skapa mikrotjänster för smarta kontrakt** på menyn.

    ![Skapa en Logic app](./media/ethereum-logic-app/generate-logic-app.png)

1. I paletten kommando väljer du **Logic app**.
1. Ange **kontrakt adressen**. Mer information finns i [så här hämtar du kontrakt adressen](#get-contract-address).
1. Välj Azure-prenumeration och resurs grupp för Logic app.

    Logic app-konfigurationen och-kodfragment skapas i **generatedLogicApp** -katalogen.

1. Visa katalogen **generatedLogicApp/HelloBlockchain** . Det finns en Logic app JSON-fil för varje smart kontrakts funktion, händelse och egenskap.
1. Öppna **generatedLogicApp/HelloBlockchain/Service/Property. RequestMessage. logicapp. JSON** -fil och kopiera innehållet.

    ![JSON för egenskapen RequestMessage](./media/ethereum-logic-app/requestmessage.png)

1. I din Logic app väljer du **vyn logiskt program kod**. Ersätt den befintliga JSON-filen med den genererade Logic app-JSON.

    ![Ersätt Logic app-konfigurationen i kodvyn](./media/ethereum-logic-app/code-view.png)

1. Välj **Designer** för att växla till designvyn-vyn.
1. Logic-appen innehåller de grundläggande stegen för scenariot. Du måste dock uppdatera konfigurations informationen för Ethereum blockchain-anslutningen.
1. Välj steget **anslutningar** och ändra eller [skapa en API-anslutning](#create-an-api-connection) till din Azure blockchain-tjänst.

    ![Logic-app för mikrotjänster](./media/ethereum-logic-app/microservice-logic-app.png)

1. Du kan nu använda din Logic app. För att testa den REST-baserade mikrotjänsten skickar du en HTTP POST-begäran till URL: en för logi Kap App-begäran. Kopiera **http post-URL:** en från steget **när en http-begäran tas emot** .

    ![HTTP POST-URL](./media/ethereum-logic-app/post-url.png)

1. Använd sväng för att skapa en HTTP POST-begäran. Ersätt platshållartexten **\<HTTP post URL @ no__t-2** med URL: en från föregående steg.

    ``` bash
    curl -d "{}" -H "Content-Type: application/json" -X POST "<HTTP POST URL>"
    ```

    Kommandot spiral returnerar ett svar från Logic-appen. I det här fallet är resultatet från funktionen **RequestMessage** Smart kontrakt.

    ![RequestMessage egenskaps resultat](./media/ethereum-logic-app/curl.png)

Mer information om hur du använder Development Kit finns i [Azure blockchain Development Kit for wiki-sidan för Ethereum](https://github.com/Microsoft/vscode-azure-blockchain-ethereum/wiki) .

## <a name="create-an-api-connection"></a>Skapa en API-anslutning

En API-anslutning till en blockchain krävs för Ethereum blockchain-anslutningen. Du kan använda API-anslutningen för flera Logic Apps. Vissa egenskaper krävs och andra är beroende av ditt scenario.

> [!IMPORTANT]
> En privat nyckel eller konto adress och ett lösen ord krävs för att skapa transaktioner i en blockchain. Endast en form av autentisering krävs. Du behöver inte ange både privat nyckel och konto information. Frågande kontrakt kräver ingen transaktion. Om du använder åtgärder som efterfrågar kontrakt status krävs inte den privata nyckeln eller konto adressen och lösen ordet.

Om du vill konfigurera en anslutning till en Azure blockchain-tjänst medlem är följande lista möjliga egenskaper som du kan behöva beroende på ditt scenario.

| Egenskap | Beskrivning |
|----------|-------------|
|**Anslutningsnamn** | API-anslutningens namn. Obligatoriskt. |
|**Ethereum RPC-slutpunkt** | HTTP-adressen för Azure blockchain service Transaction-noden. Obligatoriskt. Så här [hämtar du RPC-slutpunkten](#get-rpc-endpoint). |
|**Privat nyckel** | Ethereum-kontots privata nyckel. Privat nyckel eller konto adress och lösen ord krävs för transaktioner. Så här [hämtar du den privata nyckeln](#get-private-key). |
|**Konto adress** | Medlems konto adress för Azure blockchain-tjänst. Privat nyckel eller konto adress och lösen ord krävs för transaktioner. Så här [hämtar du konto adressen](#get-account-address). |
|**Konto lösen ord** | Konto lösen ordet anges när du skapar medlemmen. Information om hur du återställer lösen ordet finns i [Ethereum-konto](consortium.md#ethereum-account).|

## <a name="get-rpc-endpoint"></a>Hämta RPC-slutpunkt

Azure blockchain-tjänstens RPC-slutpunkt krävs för att ansluta till ett blockchain-nätverk. Du kan hämta slut punkts adress med Azure blockchain Development Kit för Ethereum eller Azure Portal.

**Använda Development Kit:**

1. Under **Azure blockchain service** i Visual Studio Code högerklickar du på konsortiet.
1. Välj **Kopiera RPC-slutpunkt**.

    ![Kopiera RPC-slutpunkt](./media/ethereum-logic-app/devkit-rpc.png)

    RPC-slutpunkten kopieras till Urklipp.

**Använda Azure Portal:**

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Navigera till din Azure blockchain service-medlem. Välj **Transactions-noder** och noden standard transaktions nod.

    ![Välj noden standard transaktion](./media/ethereum-logic-app/transaction-nodes.png)

1. Välj **anslutnings strängar > åtkomst nycklar**.
1. Kopiera slut punkts adressen från **https (åtkomst nyckel 1)** eller åtkomst nyckel 2.

    ![Anslutningssträng](./media/ethereum-logic-app/connection-string.png)

    RPC-slutpunkten är HTTPS-URL: en, inklusive adressen och åtkomst nyckeln för din Azure blockchain service member Transaction-nod.

## <a name="get-private-key"></a>Hämta privat nyckel

Ethereum-kontots privata nyckel kan användas för att autentisera när en transaktion skickas till blockchain. Ditt Ethereum-kontos offentliga och privata nycklar genereras från 12 ord. Azure blockchain Development Kit för Ethereum genererar en genom gång när du ansluter till en Azure blockchain service Consortium-medlem. Du kan hämta slut punkts adress med Development Kit-tillägget.

1. Öppna Command-paletten (F1) i Visual Studio Code.
1. Välj **Azure blockchain: Hämta privat nyckel @ no__t-0.
1. Välj det välliggande du sparade när du ansluter till konsortiet medlem.

    ![Välj att använda](./media/ethereum-logic-app/private-key.png)

    Den privata nyckeln kopieras till Urklipp.

## <a name="get-account-address"></a>Hämta konto adress

Medlems kontot och lösen ordet kan användas för att autentisera när en transaktion skickas till blockchain. Lösen ordet anges när du skapar medlemmen.

1. I Azure Portal går du till översikts sidan för Azure blockchain-tjänsten.
1. Kopiera **medlems konto** adressen.

    ![Kopiera medlems konto](./media/ethereum-logic-app/member-account.png)

Mer information om konto adress och lösen ord finns i [Ethereum-konto](consortium.md#ethereum-account).

## <a name="get-contract-abi"></a>Hämta kontrakts ABI

ABI (kontrakt programmets binära gränssnitt) definierar de smarta kontrakts gränssnitten. Den beskriver hur du interagerar med det smarta kontraktet. Du kan hämta kontrakts ABI med hjälp av Azure blockchain Development Kit för Ethereum eller från filen med filen med Solidion-kontraktets metadata.

**Använda Development Kit:**

Om du har använt Development Kit eller Truffle för att bygga ditt smarta kontrakt kan du använda tillägget för att kopiera kontrakts ABI till Urklipp.

1. I fönstret i Visual Studio Code Explorer expanderar du mappen **Bygg/kontrakt** i ditt projekt.
1. Högerklicka på JSON-filen för kontraktets metadata. Fil namnet är det smarta kontrakt namnet följt av **. JSON** -tillägget.
1. Välj **kopiera kontrakt ABI**.

    ![Kopiera kontrakts ABI med DevKit](./media/ethereum-logic-app/abi-devkit.png)

    Kontraktets ABI kopieras till Urklipp.

**Använda filen med kontraktets metadata:**

1. Öppna den kontrakts ETA data fil som finns i mappen **build/kontrakt** i ditt projekt. Fil namnet är det smarta kontrakt namnet följt av **. JSON** -tillägget.
1. Hitta avsnittet **ABI** i JSON-filen.
1. Kopiera **ABI** JSON-matrisen.

    ![Avsnittet kontrakts ABI i metadata](./media/ethereum-logic-app/abi-metadata.png)

## <a name="get-contract-bytecode"></a>Hämta kontraktets bytekod

Kontraktets bytekod är det kompilerade smarta kontraktet som körs av den virtuella Ethereum-datorn. Du kan hämta kontraktets bytekod med Azure blockchain Development Kit för Ethereum eller från halvledar kompilatorn.

**Använda Development Kit:**

Om du har använt Development Kit eller Truffle för att bygga ditt smarta kontrakt kan du använda tillägget för att kopiera kontraktets bytekod till Urklipp.

1. I fönstret i Visual Studio Code Explorer expanderar du mappen **Bygg/kontrakt** i ditt projekt.
1. Högerklicka på JSON-filen för kontraktets metadata. Fil namnet är det smarta kontrakt namnet följt av **. JSON** -tillägget.
1. Välj **Kopiera kontrakts bytekod**.

    ![Kopiera kontraktets bytekod med DevKit](./media/ethereum-logic-app/bytecode-devkit.png)

    Kontraktets bytekod kopieras till Urklipp.

**Använda filen med kontraktets metadata:**

1. Öppna den kontrakts ETA data fil som finns i mappen **build/kontrakt** i ditt projekt. Fil namnet är det smarta kontrakt namnet följt av **. JSON** -tillägget.
1. Hitta elementet **bytekod** i JSON-filen.
1. Kopiera värdet för **bytekod** .

    ![Kopiera bytekod med metadata](./media/ethereum-logic-app/bytecode-metadata.png)

**Använda halvledar kompilatorn:**

Använd kommandot `solc --bin <smart contract>.sol` om du vill generera kontraktets bytekod.

## <a name="get-contract-address"></a>Hämta kontrakts adress

Kontrakt adressen är mål adressen för det smarta kontraktet på Ethereum-blockchain. Du använder den här adressen för att skicka en transaktion eller ett fråge tillstånd för ett smart kontrakt. Du kan hämta kontrakts adressen från Truffle och filen med kontraktets metadata.

**Använda Truffle migrera utdata:**

Truffle visar kontrakt adressen efter att det smarta kontraktet har distribuerats. Kopiera **kontrakt adressen** från utdata.

![Kontrakt adress från Truffle-utdata](./media/ethereum-logic-app/contract-address-truffle.png)

**Använda filen med kontraktets metadata:**

1. Öppna den kontrakts ETA data fil som finns i mappen **build/kontrakt** i ditt projekt. Fil namnet är det smarta kontrakt namnet följt av **. JSON** -tillägget.
1. Hitta avsnittet **nätverk** i JSON-filen.
1. Privata nätverk identifieras av ett heltals nätverks-ID. Hitta värdet Address i avsnittet Network.
1. Kopiera värdet för **adress** .

![Kontrakt adress från metadata](./media/ethereum-logic-app/contract-address-metadata.png)

## <a name="next-steps"></a>Nästa steg

Se vanliga [scenarier som ansluter blockchain med hjälp av Azure Logic Apps](https://channel9.msdn.com/Shows/Blocktalk/Doing-more-with-Logic-Apps?term=logic%20apps%20blockchain&lang-en=true).
