---
title: Översikt över Azure blockchain service-utveckling
description: Introduktion till utveckling av lösningar i Azure blockchain-tjänsten.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/11/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: 2990917243507d93cb6a86da11cf771f3a42eebd
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298427"
---
# <a name="azure-blockchain-service-development-overview"></a>Översikt över Azure blockchain service-utveckling

Med Azure blockchain-tjänsten kan du skapa konsortier blockchain-nätverk för att möjliggöra företags scenarier som till gångs spårning, digital token, lojalitet och belöning, försörjnings kedjans ekonomi och härkomst. Den här artikeln är en introduktion till Azure blockchain service-utveckling och viktiga ämnen för att implementera blockchain for Enterprise.

## <a name="client-connection-to-azure-blockchain-service"></a>Klient anslutning till Azure blockchain-tjänsten

Det finns olika typer av klienter för blockchain-nätverk, inklusive fullständiga noder, lätta noder och fjärrklienter. Azure blockchain service skapar ett blockchain-nätverk som innehåller noder. Du kan använda olika klienter som din gateway till Azure blockchain-tjänsten för blockchain-utveckling. Azure blockchain-tjänsten erbjuder grundläggande autentisering eller åtkomst nyckel som en utvecklings slut punkt. Följande är populära klienter som du kan använda Connect.

### <a name="metamask"></a>MetaMask

MetaMask är en webbläsarbaserad plån boks tjänst (fjärrklient), RPC-klient och grundläggande kontrakts Utforskare. Till skillnad från andra webb läsar-plåner, infogar MetaMask en Web3-instans i webbläsarens JavaScript-kontext, som fungerar som en RPC-klient som ansluter till en rad olika Ethereum Blockchains (*mainnet*, *Ropsten*testnet, *Kovan testnet*, Local RPC Node, osv.). Du kan enkelt konfigurera anpassade RPC för att ansluta till Azure blockchain-tjänsten och starta blockchain-utveckling med Remix.

### <a name="geth"></a>Geth

Geth är kommando rads gränssnittet för att köra en fullständig Ethereum-nod som implementerats i go. Du behöver inte köra fullständig nod, men kan starta den interaktiva konsolen som tillhandahåller en Java Script runtime-miljö som visar ett JavaScript-API för att interagera med Azure blockchain-tjänsten.

## <a name="development-framework-configuration"></a>Konfiguration av utvecklings ramverk

För att utveckla avancerade lösningar för företags blockchain krävs ett utvecklings ramverk för att ansluta till olika blockchain-nätverk, hantera livs cykel för smart kontrakt, automatisera testning, distribuera Smart kontrakt med skript och utrusta en interaktiv konsol.

Truffle är ett populärt blockchain Development Framework för att skriva, kompilera, distribuera och testa decentraliserade program på Ethereum Blockchains. Du kan också tänka på Truffle som ett ramverk som försöker sömlöst integrera Smart kontrakt utveckling och traditionell webb utveckling.

Även det minsta projektet interagerar med minst två blockchain-noder: en på Developer-datorn och den andra som representerar nätverket där utvecklaren distribuerar sitt program. Till exempel det huvudsakliga offentliga Ethereum-nätverket eller Azure blockchain-tjänsten. Truffle tillhandahåller ett system för hantering av kompilator-och distributions artefakter för varje nätverk och gör det på ett sätt som fören klar den slutliga program distributionen. Mer information finns i [snabb start: använda Truffle för att ansluta till ett Azure blockchain service-nätverk](connect-truffle.md).

## <a name="ethereum-quorum-private-transaction"></a>Privat transaktion för Ethereum-kvorum

Kvorum är ett Ethereum distribuerat redovisnings protokoll med transaktion plus avtals sekretess och nya enighets metoder. Viktiga förbättringar för go-Ethereum inkluderar:

* Sekretess-kvorum stöder privata transaktioner och privata kontrakt via offentlig och privat delstats delning och använder sig av peer-to-peer-krypterade meddelande utbyten för riktad överföring av privata data till nätverks deltagare.
* Alternativa vedertagna mekanismer – utan behov av bevis på arbete eller bevis på ändringar i ett beviljat nätverk. Kvorum har flera sammanställda mekanismer som är utformade för konsortier som RAFT och IBFT.  Azure blockchain Services använder sig av IBFT enighet-mekanismen.

* Peer-behörighet – nod-och peer-behörighet med smarta kontrakt, vilket garanterar att endast kända parter kan ansluta till nätverket
* Högre prestanda – kvorum ger högre prestanda än offentliga Geth

Se [självstudie: skicka en transaktion med Azure blockchain-tjänsten](send-transaction.md) för ett exempel på en privat transaktion.

## <a name="block-explorers"></a>Blockera Explorer

Blockera Explorer är online blockchain webbläsare som visar enskilda block innehåll, transaktions adress data och historik. Grundläggande block information är tillgänglig via Azure Monitor i Azure blockchain-tjänsten, men om du behöver mer detaljerad information under utvecklingen kan det vara praktiskt att blockera Explorer.  Det finns populära block Explorer med öppen källkod som du kan använda. Följande är en lista över blockerade Utforskare som fungerar med Azure blockchain-tjänsten:

* [Epirus Azure blockchain service Explorer](https://azuremarketplace.microsoft.com/marketplace/apps/blk-technologies.azure-blockchain-explorer-template?tab=Overview) från Web3 Labs
* [BlockScout](https://github.com/Azure-Samples/blockchain/blob/master/ledger/template/ethereum-on-azure/technology-samples/blockscout/README.md)

## <a name="tps-measurement"></a>TPS-mått

Eftersom blockchain används i fler företags scenarier är det viktigt att du använder TPS-hastighet för att undvika Flask halsar och system ineffektivitet. Hög transaktions taxa kan vara svårt att underhålla inom en decentraliserad blockchain. En korrekt TPS-mätning kan påverkas av olika faktorer, till exempel Server tråd, storlek på transaktionskö, nätverks fördröjning och säkerhet. Om du behöver mäta TPS-hastigheten under utvecklingen är ett populärt verktyg för öppen källkod [ChainHammer](https://github.com/drandreaskrueger/chainhammer).

## <a name="next-steps"></a>Nästa steg

[Snabb start: använda Truffle för att ansluta till ett Azure blockchain service-nätverk](connect-truffle.md)
