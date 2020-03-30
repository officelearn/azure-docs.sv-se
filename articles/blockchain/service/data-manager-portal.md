---
title: Konfigurera Blockchain Data Manager med Azure Portal - Azure Blockchain Service
description: Skapa och hantera Blockchain Data Manager för Azure Blockchain Service med Hjälp av Azure-portalen.
ms.date: 11/04/2019
ms.topic: article
ms.reviewer: chroyal
ms.openlocfilehash: 03c22a7a23f1579a846746f21ce048b3425399c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273168"
---
# <a name="configure-blockchain-data-manager-using-the-azure-portal"></a>Konfigurera Blockchain Data Manager med Azure-portalen

Konfigurera Blockchain Data Manager för Azure Blockchain Service för att samla in blockchain-data och skicka dem till ett Azure Event Grid-ämne.

Om du vill konfigurera en Blockchain Data Manager-instans:

* Skapa en Blockchain Data Manager-instans för en azure blockchain-tjänsttransaktionsnod
* Lägg till dina blockchain-program

## <a name="prerequisites"></a>Krav

* Fullständig [snabbstart: Skapa en blockchain-medlem med Azure-portalen](create-member.md) eller [Snabbstart: Skapa en Blockchain Blockchain-tjänst-blockkedjemedlem med Azure CLI](create-member-cli.md)
* Skapa ett [ämne för händelserutnät](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)
* Lär dig mer om [händelsehanterare i Azure Event Grid](../../event-grid/event-handlers.md)

## <a name="create-instance"></a>Skapa instans

En Blockchain Data Manager-instans ansluter och övervakar en Azure Blockchain Service-transaktionsnod. Endast användare med åtkomst till transaktionsnoden kan skapa en anslutning. En instans samlar in alla råblock- och råtransaktionsdata från transaktionsnoden.

En utgående anslutning skickar blockchain-data till Azure Event Grid. Du konfigurerar en enda utgående anslutning när du skapar instansen. Blockchain Data Manager stöder utgående anslutningar för flera utgående eventrutnät för en viss Blockchain Data Manager-instans. Du kan skicka blockchain-data till en enda destination eller skicka blockchain-data till flera destinationer. Om du vill lägga till ett annat mål lägger du bara till ytterligare utgående anslutningar i instansen.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Gå till Azure Blockchain Service-medlemmen som du vill ansluta till Blockchain Data Manager. Välj **Blockchain Data Manager**.
1. Välj **Lägg till**.

    ![Lägg till Blockchain Data Manager](./media/data-manager-portal/add-instance.png)

    Ange följande uppgifter:

    Inställning | Beskrivning
    --------|------------
    Namn | Ange ett unikt namn för en ansluten Blockchain Data Manager. Namnet blockchain Data Manager kan innehålla gemener och siffror och har en maximal längd på 20 tecken.
    Transaktionsnod | Välj en transaktionsnod. Endast transaktionsnoder som du har läsbehörighet visas.
    Anslutningsnamn | Ange ett unikt namn på den utgående anslutningen där blockchain-transaktionsdata skickas.
    Slutpunkt för händelserutnät | Välj ett ämne för händelserutnät i samma prenumeration som Blockchain Data Manager-instansen.

1. Välj **OK**.

    Det tar mindre än en minut att skapa en Blockchain Data Manager-instans. När instansen har distribuerats startas den automatiskt. En blockchain Data Manager-instans som körs samlar in blockchain-händelser från transaktionsnoden och skickar data till de utgående anslutningarna.

    Den nya instansen visas i listan över Blockchain Data Manager-instanser för Azure Blockchain Service-medlemmen.

    ![Lista över instanser av blockchain-datamedlem](./media/data-manager-portal/instance-list.png)

## <a name="add-blockchain-application"></a>Lägg till blockchain-program

Om du lägger till ett blockchain-program avkodar Blockchain Data Manager händelse- och egenskapstillstånd för programmet. Annars skickas endast råblock- och råtransaktionsdata. Blockchain Data Manager identifierar också kontraktsadresser när kontraktet distribueras. Du kan lägga till flera blockchain-program i en Blockchain Data Manager-instans.

> [!IMPORTANT]
> För närvarande stöds inte blockchain-program som deklarerar [soliditetsmatristyper](https://solidity.readthedocs.io/en/v0.5.12/types.html#arrays) eller [mappningstyper.](https://solidity.readthedocs.io/en/v0.5.12/types.html#mapping-types) Egenskaper som deklarerats som matris- eller mappningstyper kommer inte att avkodas i *ContractPropertiesMsg-* eller *DecodedContractEventsMsg-meddelanden.*

Blockchain Data Manager kräver ett smart kontrakt ABI och distribuerad bytecode-fil för att lägga till programmet.

### <a name="get-contract-abi-and-bytecode"></a>Hämta ABI-kontrakt och bytecode

Kontraktet ABI definierar smarta kontraktsgränssnitt. Den beskriver hur man interagerar med det smarta kontraktet. Du kan använda [Azure Blockchain Development Kit för Ethereum-tillägget för](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) att kopiera kontraktet ABI till Urklipp.

1. Expandera mappen **build/contracts** i solidity-projektet i fönstret Visual Studio-kodutforskaren.
1. Högerklicka på kontraktsmetadatafilen JSON. Filnamnet är det smarta kontraktsnamnet följt av **.json-tillägget.**
1. Välj **Kopiera kontrakt ABI**.

    ![Fönstret Visual Studio-kod med markeringen Kopiera kontrakt ABI](./media/data-manager-portal/abi-devkit.png)

    Kontraktet ABI kopieras till Urklipp.

1. Spara **abi-matrisen** som en JSON-fil. Till exempel *abi.json*. Du använder filen i ett senare steg.

Blockchain Data Manager kräver den distribuerade bytekoden för det smarta kontraktet. Den distribuerade bytekoden skiljer sig från den smarta kontraktsbytekoden. Du använder tillägget Azure blockchain-utvecklingspaket för att kopiera bytekoden till Urklipp.

1. Expandera mappen **build/contracts** i solidity-projektet i fönstret Visual Studio-kodutforskaren.
1. Högerklicka på kontraktsmetadatafilen JSON. Filnamnet är det smarta kontraktsnamnet följt av **.json-tillägget.**
1. Välj **Kopiera transaktionsbytekod**.

    ![Fönstret Kopia transaktionskod med markeringen Kopiera transaktionsbytekod](./media/data-manager-portal/bytecode-devkit.png)

    Bytekoden kopieras till Urklipp.

1. Spara **bytekodvärdet** som en JSON-fil. Till exempel *bytecode.json*. Du använder filen i ett senare steg.

I följande exempel visas *filer abi.json* och *bytecode.json* som är öppna i VS-kodredigeraren. Dina filer ska se likadana ut.

![Exempel på abi.json- och bytecode.json-filer](./media/data-manager-portal/contract-files.png)

### <a name="create-contract-abi-and-bytecode-url"></a>Skapa URL för kontrakt ABI och bytecode

Blockchain Data Manager kräver att abi- och bytekodfilerna är tillgängliga via en URL när du lägger till ett program. Du kan använda ett Azure Storage-konto för att ange en privat tillgänglig URL.

#### <a name="create-storage-account"></a>Skapa lagringskonto

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

#### <a name="upload-contract-files"></a>Ladda upp kontraktsfiler

1. Skapa en ny behållare för lagringskontot. Välj **Behållare > behållare**.

    ![Skapa en lagringskontobehållare](./media/data-manager-portal/create-container.png)

    | Field | Beskrivning |
    |-------|-------------|
    | Namn  | Ge containern ett namn. Till exempel *smartcontract* |
    | Offentlig åtkomstnivå | Välj *Privat (ingen anonym åtkomst)* |

1. Klicka på **OK** för att skapa containern.
1. Markera behållaren och välj sedan **Ladda upp**.
1. Välj båda JSON-filer som du har skapat i avsnittet [Hämta kontrakt ABI och bytecode.](#get-contract-abi-and-bytecode)

    ![Ladda upp blob](./media/data-manager-portal/upload-blobs.png)

    Välj **Överför**.

#### <a name="generate-url"></a>Generera URL

För varje blob genererar du en signatur för delad åtkomst.

1. Välj ABI JSON-bloben.
1. Välj **Generera SAS**
1. Ange önskad åtkomstsignatur förfallodatum och välj sedan **Generera blob SAS-token och URL**.

    ![Generera SAS-token](./media/data-manager-portal/generate-sas.png)

1. Kopiera **SAS-URL:en** för Blob och spara den för nästa avsnitt.
1. Upprepa stegen [Generera URL](#generate-url) för json-bloben för bytekoden.

### <a name="add-application-to-instance"></a>Lägga till program i instans

1. Välj din Blockchain Data Manager-instans i instanslistan.
1. Välj **Blockchain-program**.
1. Välj **Lägg till**.

    ![Lägga till ett blockchain-program](./media/data-manager-portal/add-application.png)

    Ange namnet på blockchain-programmet och ABI-url:erna för smart kontrakt och bytekod.

    Inställning | Beskrivning
    --------|------------
    Namn | Ange ett unikt namn som blockchain-programmet ska spåra.
    Kontrakt ABI | URL-sökvägen till ABI-filen för kontrakt. Mer information finns i [Skapa kontrakt ABI och bytecode URL](#create-contract-abi-and-bytecode-url).
    Bytekod för kontrakt | URL-sökvägen till bytekodfil. Mer information finns i [Skapa kontrakt ABI och bytecode URL](#create-contract-abi-and-bytecode-url).

1. Välj **OK**.

    När programmet har skapats visas programmet i listan över blockchain-program.

    ![Programlista för Blockchain](./media/data-manager-portal/artifact-list.png)

Du kan ta bort Azure Storage-kontot eller använda det för att konfigurera fler blockchain-program. Om du vill ta bort Azure Storage-kontot kan du ta bort resursgruppen. När du tar bort resursgruppen raderas även det kopplade lagringskontot och eventuella andra resurser som är kopplade till resursgruppen.

## <a name="stop-instance"></a>Stoppa instans

Stoppa Blockchain Manager-instansen när du vill sluta fånga blockchain-händelser och skicka data till utgående anslutningar. När instansen stoppas debiteras inga avgifter för Blockchain Data Manager. Mer information finns i [prissättning](https://azure.microsoft.com/pricing/details/blockchain-service).

1. Gå till **Översikt** och välj **Stoppa**.

    ![Stoppa instans](./media/data-manager-portal/stop-instance.png)

## <a name="next-steps"></a>Nästa steg

Prova nästa självstudiekurs när du skapar en transaktionsutforskare med Blockchain Data Manager och Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Använda Blockchain Data Manager för att skicka data till Azure Cosmos DB](data-manager-cosmosdb.md)