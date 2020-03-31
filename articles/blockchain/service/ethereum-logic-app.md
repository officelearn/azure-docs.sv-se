---
title: Använda Ethereum Blockchain-anslutning med Azure Logic Apps - Azure Blockchain Service
description: Använd Ethereum Blockchain-kopplingen med Azure Logic Apps för att utlösa smarta kontraktsfunktioner och svara på smarta kontraktshändelser.
ms.date: 10/14/2019
ms.topic: article
ms.reviewer: chrisseg
ms.openlocfilehash: 4a9acfd6098ed45fd92c7e3047b5d1446eeddbd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74325225"
---
# <a name="use-the-ethereum-blockchain-connector-with-azure-logic-apps"></a>Använda Ethereum Blockchain-kopplingen med Azure Logic Apps

Använd [Ethereum Blockchain-kopplingen](https://docs.microsoft.com/connectors/blockchainethereum/) med [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/) för att utföra smarta kontraktsåtgärder och svara på smarta kontraktshändelser. Anta till exempel att du vill skapa en REST-baserad mikrotjänst som returnerar information från en blockchain-redovisning. Med hjälp av en logikapp kan du acceptera HTTP-begäranden som frågar information som lagras i en blockchain-redovisning.

## <a name="prerequisites"></a>Krav

Slutför den valfria nödvändiga [snabbstarten: Använd Visual Studio-kod för att ansluta till ett Azure Blockchain Service-konsortiumnätverk](connect-vscode.md). Snabbstarten hjälper dig att installera [Azure Blockchain Development Kit för Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) och konfigurera din blockchain-utvecklingsmiljö.

## <a name="create-a-logic-app"></a>Skapa en logikapp

Azure Logic Apps hjälper dig att schemalägga och automatisera affärsprocesser och arbetsflöden när du behöver integrera system och tjänster. Först skapar du en logik som använder Ethereum Blockchain-kopplingen.

1. På [Azure-portalen](https://portal.azure.com) väljer du **Skapa en resurs** > **Integrering** > **Logikapp**.
1. Under **Skapa logikapp**anger du information om var logikappen ska skapas. När du är klar väljer du **Skapa**.

    Mer information om hur du skapar logikappar finns i [Skapa automatiserade arbetsflöden med Azure Logic Apps](../../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. När Azure har distribuerat din app väljer du din logikappresurs.
1. Välj **Tom logikapp**under **Mallar**i Logic Apps Designer.

Varje logikapp måste börja med en utlösare som utlöses när en specifik händelse sker eller när ett särskilt villkor uppfylls. Varje gång utlösaren körs skapar Logic Apps-motorn en logikappinstans som startar och kör ditt arbetsflöde.

Ethereum Blockchain-kontakten har en utlösare och flera åtgärder. Vilken utlösare eller åtgärd du använder beror på ditt scenario.

Om arbetsflödet:

* Utlösare när en händelse inträffar på [blockkedjan, Använd händelseutlösaren](#use-the-event-trigger).
* Frågor eller distribuerar ett smart kontrakt, [Använd åtgärder](#use-actions).
* Följer ett vanligt scenario, [Generera ett arbetsflöde med hjälp av utvecklarpaketet](#generate-a-workflow).

## <a name="use-the-event-trigger"></a>Använd händelseutlösaren

Använd Ethereum Blockchain-händelseutlösare när du vill att en logikapp ska köras när en smart kontraktshändelse inträffar. Du vill till exempel skicka ett e-postmeddelande när en smart kontraktsfunktion anropas.

1. Välj Ethereum Blockchain-kopplingen i Logic Apps Designer.
1. Välj **När en smart kontraktshändelse inträffar på**fliken **Utlösare** .
1. Ändra eller [skapa en API-anslutning](#create-an-api-connection) till Azure Blockchain Service.
1. Ange information om det smarta kontrakt som du vill söka efter händelser.

    ![Logic Apps Designer med egenskaper för händelseutlösare](./media/ethereum-logic-app/event-properties.png)

    | Egenskap | Beskrivning |
    |----------|-------------|
    | **Kontrakt ABI** | Det binära gränssnittet för kontraktsprogrammet (ABI) definierar gränssnitten för smarta kontrakt. Mer information finns [i Hämta kontraktet ABI](#get-the-contract-abi). |
    | **Smart kontraktsadress** | Kontraktsadressen är den smarta kontraktsadressen på Ethereum blockchain. Mer information finns i [Hämta kontraktsadressen](#get-the-contract-address). |
    | **Händelsenamn** | Välj en smart kontraktshändelse att kontrollera. Händelsen utlöser logikappen. |
    | **Intervall** och **frekvens** | Välj hur ofta du vill söka efter händelsen. |

1. Välj **Spara**.

Om du vill slutföra logikappen kan du lägga till ett nytt steg som utför en åtgärd baserat på Ethereum Blockchain-händelseutlösaren. Skicka till exempel ett e-postmeddelande.

## <a name="use-actions"></a>Använda åtgärder

Använd Ethereum Blockchain-åtgärderna när du vill ha en logikapp för att utföra en åtgärd i blockchain-redovisningen. Du vill till exempel skapa en REST-baserad mikrotjänst som anropar en smart kontraktsfunktion när en HTTP-begäran görs till en logikapp.

Anslutningsåtgärder kräver en utlösare. Du kan använda en Ethereum Blockchain-anslutningsåtgärd som nästa steg efter en utlösare, till exempel en HTTP-begärandeutlösare för en mikrotjänst.

1. Välj **Nytt steg** efter en utlösare i Logic Apps Designer.
1. Välj Ethereum Blockchain-kopplingen.
1. Välj en av de tillgängliga åtgärderna på fliken **Åtgärder.**

    ![Logic Apps Designer med egenskaper för Åtgärder](./media/ethereum-logic-app/action-properties.png)

1. Ändra eller [skapa en API-anslutning](#create-an-api-connection) till Azure Blockchain Service.
1. Ange följande information om din smarta kontraktsfunktion beroende på vilken åtgärd du har valt.

    | Egenskap | Beskrivning |
    |----------|-------------|
    | **Kontrakt ABI** | Kontraktet ABI definierar smarta kontraktsgränssnitt. Mer information finns [i Hämta kontraktet ABI](#get-the-contract-abi). |
    | **Bytekod för kontrakt** | Den kompilerade smart kontrakt bytecode. Mer information finns i [Hämta kontraktsbytekoden](#get-the-contract-bytecode). |
    | **Smart kontraktsadress** | Kontraktsadressen är den smarta kontraktsadressen på Ethereum blockchain. Mer information finns i [Hämta kontraktsadressen](#get-the-contract-address). |
    | **Namn på smart kontraktsfunktion** | Välj namnet på funktionen för smarta kontrakt för åtgärden. Listan fylls i från detaljerna i kontraktet ABI. |

    När du har valt ett namn på en smart kontraktsfunktion kan obligatoriska fält för funktionsparametrar visas. Ange de värden eller dynamiskt innehåll som krävs för ditt scenario.

Nu kan du använda logikappen. När logikapphändelsen utlöses körs åtgärden Ethereum Blockchain. En HTTP-begärandeutlösare kör till exempel en Ethereum blockchain-åtgärd för att fråga ett värde för smart kontraktstillstånd. Den här frågan resulterar i ett HTTP-svar som returnerar värdet.

## <a name="generate-a-workflow"></a>Generera ett arbetsflöde

Azure Blockchain Development Kit för Ethereum Visual Studio-kodtillägg kan generera logikapparbetsflöden för vanliga scenarier. Fyra scenarier är tillgängliga:

* Datapublicering till en Azure SQL Database-instans
* Händelsepublicering till en instans av Azure Event Grid eller Azure Service Bus
* Rapportpublicering
* REST-baserad mikrotjänst

 Azure Blockchain Development Kit använder Tryffel för att förenkla blockchain-utveckling. För att skapa en logikapp baserad på ett smart kontrakt behöver du en tryffellösning för det smarta kontraktet. Du behöver också en anslutning till ditt Azure Blockchain Service-konsortiumnätverk. Mer information finns i [Använda Visual Studio-kod för att ansluta till en snabbstart för Azure Blockchain Service-konsortienätverk](connect-vscode.md).

Följande steg genererar till exempel en REST-baserad logikapp för mikrotjänster baserat på snabbstarten **helloblockchain** smart contract:

1. Expandera **kontraktmappen** i lösningen i sidofältet i Visual Studio Code Explorer.
1. Högerklicka på **HelloBlockchain.sol** och välj **Generera mikrotjänster för smarta kontrakt** på menyn.

    ![Fönstret Generera mikrotjänster för smarta kontrakt i Visual Studio-kod](./media/ethereum-logic-app/generate-logic-app.png)

1. Välj **Logic App**i kommandopaletten .
1. Ange **kontraktsadressen**. Mer information finns i [Hämta kontraktsadressen](#get-the-contract-address).
1. Välj Azure-prenumerations- och resursgruppen för logikappen.

    Logikappkonfigurationen och kodfilerna genereras i **den genereradeLogicApp-katalogen.**

1. Visa katalogen **generatedLogicApp/HelloBlockchain.** Det finns en logiska app JSON-fil för varje smart kontraktsfunktion, händelse och egenskap.
1. Öppna **egenskapen generatedLogicApp/HelloBlockchain/Service/Property. RequestMessage.logicapp.json** fil och kopiera innehållet.

    ![JSON-fil med kod att kopiera](./media/ethereum-logic-app/requestmessage.png)

1. I logikappen väljer du **Logikappkodsvy**. Ersätt den befintliga JSON med den genererade logikappen JSON.

    ![Vyn Logikappkod med ny ersatt appkod](./media/ethereum-logic-app/code-view.png)

1. Välj **Designer** om du vill växla till designervyn.
1. Logikappen innehåller de grundläggande stegen för scenariot. Du måste dock uppdatera konfigurationsinformationen för Ethereum Blockchain-kopplingen.
1. Välj steget **Anslutningar** och ändra eller [skapa en API-anslutning](#create-an-api-connection) till Azure Blockchain Service.

    ![Designervy med markeringen Anslutningar](./media/ethereum-logic-app/microservice-logic-app.png)

1. Nu kan du använda logikappen. Om du vill testa den REST-baserade mikrotjänsten utfärdar du en HTTP POST-begäran till URL:en för begäran om logikapp. Kopiera **HTTP POST-URL-innehållet** från steget **När en HTTP-begäran tas emot.**

    ![Fönstret Logic Apps Designer med HTTP POST-URL:en](./media/ethereum-logic-app/post-url.png)

1. Använd cURL för att skapa en HTTP POST-begäran. Ersätt platshållartexten * \<\> HTTP POST URL* med URL:en från föregående steg.

    ``` bash
    curl -d "{}" -H "Content-Type: application/json" -X POST "<HTTP POST URL>"
    ```

    Kommandot cURL returnerar ett svar från logikappen. I det här fallet är svaret utdata från funktionen **RequestMessage** smart contract.

    ![Kodutdata från funktionen RequestMessage smart contract](./media/ethereum-logic-app/curl.png)

Mer information om hur du använder utvecklingspaketet finns på [wiki-sidan Azure Blockchain Development Kit for Ethereum](https://github.com/Microsoft/vscode-azure-blockchain-ethereum/wiki).

## <a name="create-an-api-connection"></a>Skapa en API-anslutning

En API-anslutning till en blockchain krävs för Ethereum Blockchain-anslutningen. Du kan använda API-kopplingen för flera logikappar. Vissa egenskaper krävs och andra är beroende av ditt scenario.

> [!IMPORTANT]
> En privat nyckel eller kontoadress och lösenord krävs för att skapa transaktioner på en blockchain. Endast en form av autentisering behövs. Du behöver inte ange både privat nyckel och kontoinformation. Att fråga kontrakt kräver ingen transaktion. Om du använder åtgärder som frågar kontraktstillstånd krävs inte den privata nyckeln eller kontoadressen och lösenordet.

För att hjälpa dig att konfigurera en anslutning till en Azure Blockchain Service-medlem har följande lista möjliga egenskaper som du kan behöva beroende på ditt scenario.

| Egenskap | Beskrivning |
|----------|-------------|
|**Anslutningsnamn** | Namn på API-anslutningen. Krävs. |
|**Slutpunkt för Ethereum RPC** | HTTP-adressen för transaktionsnoden för Azure Blockchain-tjänsten. Krävs. Mer information finns [i Hämta RPC-slutpunkten](#get-the-rpc-endpoint). |
|**Privat nyckel** | Ethereum konto privat nyckel. Privat nyckel- eller kontoadress och lösenord krävs för transaktioner. Mer information finns i [Hämta den privata nyckeln](#get-the-private-key). |
|**Kontoadress** | Azure Blockchain-tjänstmedlemskontoadress. Privat nyckel- eller kontoadress och lösenord krävs för transaktioner. Mer information finns i [Hämta kontoadressen](#get-the-account-address). |
|**Lösenord för konto** | Kontolösenordet ställs in när du skapar medlemmen. Information om hur du återställer lösenordet finns i [Ethereum-konto](consortium.md#ethereum-account).|

## <a name="get-the-rpc-endpoint"></a>Hämta RPC-slutpunkten

Azure Blockchain Service RPC-slutpunktsadressen krävs för att ansluta till ett blockchain-nätverk. Du kan hämta slutpunktsadressen med hjälp av Azure Blockchain Development Kit för Ethereum eller Azure-portalen.

**Så här använder du utvecklingspaketet:**

1. Högerklicka på konsortiet under **Azure Blockchain Service** i Visual Studio Code.
1. Välj **Kopiera RPC-slutpunktsadress**.

    ![Fönstret Visual Studio-kod som visar konsortiet med markeringen Kopiera RPC-slutpunktsadress](./media/ethereum-logic-app/devkit-rpc.png)

    RPC-slutpunkten kopieras till Urklipp.

**Så här använder du Azure-portalen:**

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Gå till din Azure Blockchain Service-medlem. Välj **Transaktionsnoder** och standardlänken för transaktionsnoder.

    ![Sidan Transaktionsnoder med markeringen (standardnod)](./media/ethereum-logic-app/transaction-nodes.png)

1. Välj **Anslutningssträngar** > **Åtkomstnycklar**.
1. Kopiera slutpunktsadressen från **HTTPS (Access-nyckel 1)** eller **HTTPS (Access-tangent 2)**.

    ![Azure-portal med åtkomstnycklar för anslutningssträngen](./media/ethereum-logic-app/connection-string.png)

    RPC-slutpunkten är HTTPS-URL:en, som innehåller adress- och åtkomstnyckeln för din Azure Blockchain-tjänstmedlemstransaktionsnod.

## <a name="get-the-private-key"></a>Hämta den privata nyckeln

Du kan använda Ethereum-kontots privata nyckel för att autentisera när du skickar en transaktion till blockchain. Ditt Ethereum-kontos offentliga och privata nycklar genereras från en 12-ords mnemonic. Azure Blockchain Development Kit för Ethereum genererar ett mnemonic när du ansluter till en Azure Blockchain Service-konsortiummedlem. Du kan hämta slutpunktsadressen med hjälp av tillägget för utvecklingspaket.

1. Öppna kommandopaletten (F1) i Visual Studio-kod.
1. Välj **Azure Blockchain: Hämta privat nyckel**.
1. Välj den mnemonic du sparade när du ansluter till konsortiemedlemmen.

    ![Kommandopalett med möjlighet att välja mnemonic](./media/ethereum-logic-app/private-key.png)

    Den privata nyckeln kopieras till Urklipp.

## <a name="get-the-account-address"></a>Hämta kontoadressen

Du kan använda medlemskontot och lösenordet för att autentisera när du skickar en transaktion till blockchain. Lösenordet ställs in när du skapar medlemmen.

1. Gå till översiktssidan för Azure Blockchain Service i Azure-portalen.
1. Kopiera adress för **medlemskontot.**

    ![Översiktssida med medlemskontoadressen](./media/ethereum-logic-app/member-account.png)

Mer information om kontoadress och lösenord finns i [Ethereum-konto](consortium.md#ethereum-account).

## <a name="get-the-contract-abi"></a>Få kontraktet ABI

Kontraktet ABI definierar smarta kontraktsgränssnitt. Den beskriver hur man interagerar med det smarta kontraktet. Du kan få kontraktet ABI genom att använda Azure Blockchain Development Kit för Ethereum. Du kan också hämta den från kontraktsmetadatafilen som skapats av soliditetskompilatorn.

**Så här använder du utvecklingspaketet:**

Om du använde utvecklingspaketet eller Tryffel för att skapa ditt smarta kontrakt kan du använda tillägget för att kopiera kontraktet ABI till Urklipp.

1. Expandera mappen **build/contracts** i solidity-projektet i fönstret Visual Studio-kodutforskaren.
1. Högerklicka på kontraktsmetadatafilen JSON. Filnamnet är det smarta kontraktsnamnet följt av **.json-tillägget.**
1. Välj **Kopiera kontrakt ABI**.

    ![Fönstret Visual Studio-kod med markeringen Kopiera kontrakt ABI](./media/ethereum-logic-app/abi-devkit.png)

    Kontraktet ABI kopieras till Urklipp.

**Så här använder du kontraktsmetadatafilen:**

1. Öppna kontraktsmetadatafilen i mappen **build/contracts** i solidity-projektet. Filnamnet är det smarta kontraktsnamnet följt av **.json-tillägget.**
1. Leta reda på **avsnittet abi** i JSON-filen.
1. Kopiera **abi** JSON-matrisen.

    ![ABI-kod i kontraktsmetadatafilen](./media/ethereum-logic-app/abi-metadata.png)

## <a name="get-the-contract-bytecode"></a>Hämta kontraktsbytekoden

Kontraktsbytekoden är det kompilerade smarta kontraktet som utförs av den virtuella Ethereum-datorn. Du kan hämta kontraktsbytekoden med hjälp av Azure Blockchain Development Kit för Ethereum. Du kan också få det från Solidity kompilatorn.

**Så här använder du utvecklingspaketet:**

Om du använde utvecklingspaketet eller Tryffel för att skapa ditt smarta kontrakt kan du använda tillägget för att kopiera kontraktsbytekoden till Urklipp.

1. Expandera mappen **build/contracts** i solidity-projektet i fönstret Visual Studio-kodutforskaren.
1. Högerklicka på kontraktsmetadatafilen JSON. Filnamnet är det smarta kontraktsnamnet följt av **.json-tillägget.**
1. Välj **Kopiera kontraktsbytekod**.

    ![Fönstret Kopia studiokod med markeringen Kopiera kontraktsbytekod](./media/ethereum-logic-app/bytecode-devkit.png)

    Kontraktsbytekoden kopieras till Urklipp.

**Så här använder du kontraktsmetadatafilen:**

1. Öppna kontraktsmetadatafilen i mappen **build/contracts** i solidity-projektet. Filnamnet är det smarta kontraktsnamnet följt av **.json-tillägget.**
1. Leta reda på **bytekodelementet** i JSON-filen.
1. Kopiera **bytekodvärdet.**

    ![Fönstret Visual Studio-kod med bytekod i metadata](./media/ethereum-logic-app/bytecode-metadata.png)

**Så här använder du soliditetskompilatorn:**

Använd kommandot `solc --bin <smart contract>.sol` för att generera kontraktsbytekoden.

## <a name="get-the-contract-address"></a>Hämta kontraktsadressen

Kontraktsadressen är den smarta kontraktsadressen på Ethereum blockchain. Du använder den här adressen för att skicka en transaktion eller frågetillstånd för ett smart kontrakt. Du kan hämta kontraktsadressen från tryffelmigreringsutdata eller kontraktmetadatafilen.

**Så här använder du tryffelflyttningsutdata:**

Tryffel visar kontraktsadressen efter distribution av det smarta kontraktet. Kopiera **kontraktsadressen** från utdata.

![Utdata för tryffelmigrering med kontraktsadressen i Visual Studio-kod](./media/ethereum-logic-app/contract-address-truffle.png)

**Så här använder du kontraktsmetadatafilen:**

1. Öppna kontraktsmetadatafilen i mappen **build/contracts** i solidity-projektet. Filnamnet är det smarta kontraktsnamnet följt av **.json-tillägget.**
1. Leta **networks** reda på nätverksavsnittet i JSON-filen.
1. Privata nätverk identifieras av ett heltalsnätverks-ID. Leta reda på adressvärdet i nätverksavsnittet.
1. Kopiera **adressvärdet.**

![Metadata med adressvärdet i Visual Studio-kod](./media/ethereum-logic-app/contract-address-metadata.png)

## <a name="next-steps"></a>Nästa steg

Titta på vanliga scenarier i videon [Gör mer med Logic Apps](https://channel9.msdn.com/Shows/Blocktalk/Doing-more-with-Logic-Apps?term=logic%20apps%20blockchain&lang-en=true).
