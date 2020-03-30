---
title: Utvecklingsöversikt för Azure Blockchain-tjänsten
description: Introduktion om hur du utvecklar lösningar på Azure Blockchain Service.
ms.date: 03/26/2020
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 4a98e5ea025894303bc5c77bba0c6154a08315f2
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348385"
---
# <a name="azure-blockchain-service-development-overview"></a>Utvecklingsöversikt för Azure Blockchain-tjänsten

Med Azure Blockchain Service kan du skapa blockchain-nätverk för konsortiet som möjliggör företagsscenarier som tillgångsspårning, digital token, lojalitet och belöning, ekonomi i leveranskedjan och härkomst. I följande avsnitt introduceras Azure Blockchain Service-utveckling för implementering av företagskedjelösningar.

## <a name="connecting-to-azure-blockchain-service"></a>Ansluta till Azure Blockchain-tjänsten

Det finns olika typer av klienter för blockchain-nätverk, inklusive fullständiga noder, ljusnoder och fjärrklienter. Azure Blockchain Service bygger ett blockchain-nätverk som innehåller noder. Du kan använda olika klienter som din gateway till Azure Blockchain Service för blockchain-utveckling. Azure Blockchain Service erbjuder grundläggande autentiserings- eller åtkomstnyckel som en utvecklingsslutpunkt. Följande är populära klienter som du kan använda connect.

### <a name="visual-studio-code"></a>Visual Studio-koden

Du kan ansluta till konsortiemedlemmar med azure blockchain development kit Visual Studio Code-tillägget. När du är ansluten till ett konsortium kan du kompilera, skapa och distribuera smarta kontrakt till en Azure Blockchain Service-konsortiummedlem.

För att utveckla sofistikerade företags blockchain-lösningar behövs ett utvecklingsramverk för att ansluta till olika blockchain-nätverk och hantera smarta kontraktslivscykler. De flesta projekt interagerar med minst två blockchain-noder. Utvecklare använder en lokal blockchain under utveckling. När programmet är klart för test eller publicering distribueras utvecklaren till ett blockchain-nätverk. Till exempel det huvudsakliga offentliga Ethereum-nätverket eller Azure Blockchain Service. Azure Blockchain Development Kit for Ethereum-tillägget i Visual Studio Code använder Tryffel. Tryffel är ett populärt blockchain-utvecklingsramverk för att skriva, sammanställa, distribuera och testa decentraliserade applikationer på Ethereum-blockkedjor. Du kan också se Tryffel som ett ramverk som försöker integrera smart kontraktsutveckling och traditionell webbutveckling.

Mer information finns i [Snabbstart: Använd Visual Studio-kod för att ansluta till ett Azure Blockchain Service-konsortiumnätverk](connect-vscode.md).

### <a name="metamask"></a>Metamask (metamask)

MetaMask är en webbläsarbaserad plånbok (fjärrklient), RPC-klient och grundläggande kontraktsutforskaren. Till skillnad från andra webbläsare plånböcker, MetaMask injicerar en web3 instans i webbläsaren JavaScript sammanhang, som fungerar som en RPC-klient som ansluter till en mängd Ethereum blockchains *(mainnet*, *Ropsten testnät*, *Kovan testnät*, lokala RPC-nod, etc.). Du kan enkelt konfigurera anpassad RPC för att ansluta till Azure Blockchain Service och starta blockchain-utveckling med Remix.

Mer information finns i [Snabbstart: Använd MetaMask för att ansluta och distribuera ett smart kontrakt](connect-metamask.md)

### <a name="geth"></a>Geth (av )

Geth är kommandoradsgränssnittet för att köra en fullständig Ethereum-nod som implementeras i Go. Du behöver inte köra hela noden men kan starta sin interaktiva konsol som tillhandahåller en JavaScript-körningsmiljö som exponerar ett JavaScript-API för att interagera med Azure Blockchain Service.

Mer information finns i [Snabbstart: Använd Geth för att koppla till en Azure Blockchain Service-transaktionsnod](connect-geth.md).

## <a name="ethereum-quorum-private-transactions"></a>Ethereum Quorum privata transaktioner

Quorum är ett Ethereum-baserat distribuerat redovisningsprotokoll med transaktion plus kontraktsekretess och nya konsensusmekanismer. Viktiga förbättringar jämfört med Go-Ethereum inkluderar:

* **Sekretess** - Quorum stöder privata transaktioner och privata kontrakt genom offentlig och privat stat separation och använder peer-to-peer krypterade meddelandeutbyten för riktad överföring av privata data till nätverksdeltagare.
* **Alternativa konsensusmekanismer** - bevis på arbete eller bevis på att man har ett samförstånd om insatsen behövs inte för ett nätverk som har tillstånd. Quorum erbjuder flera konsensusmekanismer som är utformade för konsortiumkedjor som RAFT och IBFT.Azure Blockchain Service använder IBFT-konsensusmekanismen.
* **Peer-behörighet** - nod och peer-behörighet med hjälp av smarta kontrakt säkerställer att endast kända parter kan ansluta till nätverket.
* **Högre prestanda** - Quorum erbjuder högre prestanda än offentliga Geth.

## <a name="block-explorers"></a>Blockera upptäcktsresande

Block explorers är online blockchain webbläsare som visar enskilda blockera innehåll, transaktionsadress data och historik. Grundläggande blockeringsinformation är tillgänglig via Azure Monitor i Azure Blockchain Service. Men om du behöver mer detaljerad information under utvecklingen kan blockera upptäcktsresande vara användbart.  Följande blockutforskare fungerar med Azure Blockchain Service:

* [Epirus Azure Blockchain Service Explorer](https://azuremarketplace.microsoft.com/marketplace/apps/blk-technologies.azure-blockchain-explorer-template?tab=Overview) från Web3 Labs
* [BlockScout (blockscout)](https://github.com/Azure-Samples/blockchain/blob/master/ledger/template/ethereum-on-azure/technology-samples/blockscout/README.md)

Du kan också skapa din egen blockutforskare med Blockchain Data Manager och Azure Cosmos DB, se [Självstudiekurs: Använd Blockchain Data Manager för att skicka data till Azure Cosmos DB](data-manager-cosmosdb.md).

## <a name="tps-measurement"></a>TPS-mätning

Eftersom blockchain används i fler företagsscenarier är transaktioner per sekund (TPS) hastighet viktigt för att undvika flaskhalsar och system ineffektivitet. Höga transaktionspriser kan vara svåra att underhålla inom en decentraliserad blockchain. En korrekt TPS-mätning kan påverkas av olika faktorer som servertråd, transaktionsköstorlek, nätverksfördröjning och säkerhet. Om du behöver mäta TPS hastighet under utveckling, en populär öppen källkod verktyg är [ChainHammer](https://github.com/drandreaskrueger/chainhammer).

## <a name="next-steps"></a>Nästa steg

Prova en snabbstart med Azure Blockchain Development Kit för Ethereum för att ansluta till ett konsortium på Azure Blockchain Service.

> [!div class="nextstepaction"]
> [Använda Visual Studio-kod för att ansluta till Azure Blockchain Service](connect-vscode.md)