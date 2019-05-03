---
title: Azure Blockchain Service utvecklingsöversikt
description: Introduktion om hur du utvecklar lösningar på Azure Blockchain-tjänsten.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: 388a5d8c80c3e2462602959e9d5cbc1452974d1f
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027907"
---
# <a name="azure-blockchain-service-development-overview"></a>Azure Blockchain Service utvecklingsöversikt

Du kan skapa blockchain-nätverk för att aktivera enterprise-scenarier som spåra tillgångar, digitala token, lojalitet och trafik, finansiella leveranskedja och sändande consortium med Azure Blockchain-tjänsten. Den här artikeln är en introduktion till utveckling av Azure Blockchain tjänstöversikt och viktiga avsnitt för att implementera blockchain för företag.

## <a name="client-connection-to-azure-blockchain-service"></a>Klientanslutningen till Azure Blockchain Service

Det finns olika typer av klienter för blockchain nätverk, inklusive fullständig noder, ljus noder och fjärrklienter. Azure Blockchain Service skapar en blockchain-nätverk som omfattar noder. Du kan använda olika klienter som din gateway på Azure Blockchain-tjänsten för utveckling av blockkedjor. Azure Blockchain Service erbjuder grundläggande autentisering eller åtkomstnyckel som en slutpunkt för utveckling. Följande är de populära klienter som du kan använda ansluta.

### <a name="metamask"></a>MetaMask

MetaMask är en webbläsarbaserad wallet (fjärrklient), RPC-klienten och grundläggande kontrakt explorer. Till skillnad från andra webbläsare plånböcker MetaMask lägger in en web3-instans till samma kontext som webbläsaren JavaScript, fungerar som en RPC-klient som ansluter till en mängd olika Ethereum blockchains (*mainnet*, *Ropsten testnet*, *Kovan testnet*, lokala RPC-noden, osv.). Du kan ställa in anpassade RPC enkelt att ansluta till Azure Blockchain-tjänsten och starta utveckling av blockkedjor med Remix.

### <a name="geth"></a>Geth

Geth är kommandoradsgränssnittet för att köra en fullständig Ethereum-nod som implementerats i går. Du behöver inte köra fullständig noden men kan starta den interaktiva konsolen som ger en JavaScript-körningsmiljö exponera en JavaScript-API för att interagera med Azure Blockchain-tjänsten.

## <a name="development-framework-configuration"></a>Utveckling konfigurationer

Om du vill utveckla sofistikerade företagslösningar för blockchain, behövs ett utvecklingsramverk för att ansluta till olika blockchain nätverk, hantera livscykeln för smarta kontrakt, automatisera testning, distribuera smarta kontrakt med skript och utrusta en interaktiv konsol.

Truffle är ett ramverk för utveckling av populära blockchain att skriva, kompilera, distribuera och testa decentraliserad program på Ethereum blockkedjor. Du kan också tänka på Truffle som ett ramverk som försöker integrera sömlöst smarta kontrakt utvecklings- och traditionella webbutveckling.

Även minsta projektet interagerar med minst två blockchain-noder: En på utvecklarens dator och den andra som representerar det nätverket där utvecklare distribuerar sina program. Till exempel den huvudsakliga offentligt Ethereum-nätverk eller Azure Blockchain-tjänsten. Truffle tillhandahåller ett system för att hantera artefakter för kompilering och distribution för varje nätverk och sker på ett sätt som förenklar sista programdistribution. Mer information finns i [ Snabbstart: Använd Truffle för att ansluta till en ett nätverk med Azure Blockchain Service](connect-truffle.md).

## <a name="ethereum-quorum-private-transaction"></a>Ethereum kvorum privata transaktion

Kvorum är ett protokoll för Ethereum-baserade distribuerat transaktionsregister med transaktionen plus kontrakt sekretess- och nya konsensus-mekanismer. Viktiga förbättringar över Go-Ethereum är:

* Sekretess - kvorum har stöd för privata transaktioner och privata kontrakt via offentliga och privata tillstånd separation och använder peer-to-peer krypterade meddelandeutbyten för dirigerad överföring av privata data till nätverket deltagare.
* Alternativa konsensus mekanismer - med inget behov av bevis för arbets- eller bevis på spel konsensus i ett permissioned nätverk. Kvorum erbjuder flera konsensus mekanismer som är utformade för consortium kedjor, till exempel RADDA och IBFT.  Azure Blockchain-tjänster använder IBFT konsensus-mekanism.

* Peer-ge behörighet till - nod och peer ge behörighet till som med hjälp av smarta kontrakt, att se till att endast kända parter kan anslutas till nätverket
* Högre prestanda - kvorum ger högre prestanda än offentliga Geth

Se [självstudien: Skicka en transaktion med Azure Blockchain Service](send-transaction.md) ett exempel på privat transaktion.

## <a name="block-explorers"></a>Blockera Utforskare

Blockera Utforskare är online blockchain webbläsare som visar enskilda blockera innehåll, adress transaktionsdata och historik. Grundläggande block information är tillgänglig via Azure Monitor i Azure Blockchain-tjänsten, men om du behöver mer detaljerad information under utvecklingen, blockera Utforskare kan användas.  Det finns populära öppen källkod block Utforskare som du kan använda. Här följer en lista över block Utforskare som fungerar med Azure Blockchain-tjänsten:

* [Azure Blockchain-tjänsten Explorer](https://web3labs.com/azure-offer) från Web3 Labs
* [BlockScout](https://github.com/Azure-Samples/blockchain/blob/master/ledger/template/ethereum-on-azure/technology-samples/blockscout/README.md)

## <a name="tps-measurement"></a>TPS mätning

När blockkedjor används i fler scenarier för enterprise transaktioner per sekund (TPS) hastighet är viktigt att undvika flaskhalsar och ineffektivitet i systemet. Hög transaktioner kan vara svårt att upprätthålla inom en decentraliserad blockchain. En korrekt TPS mätning kan påverkas av olika faktorer, till exempel server tråd, transaktion köstorlek, svarstid för nätverk och säkerhet. Om du vill mäta TPS hastighet under utvecklingen, ett populärt verktyg för öppen källkod är [ChainHammer](https://github.com/drandreaskrueger/chainhammer).

## <a name="next-steps"></a>Nästa steg

[Snabbstart: Använd Truffle för att ansluta till en tjänstnätverk Azure Blockchain](connect-truffle.md)
