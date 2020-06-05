---
title: Nyheter Viktig information – Azure blockchain-tjänsten
description: Lär dig vad som är nytt med Azure blockchain service, till exempel senaste versions information, versioner, kända problem och kommande ändringar.
ms.date: 06/03/2020
ms.topic: conceptual
ms.reviewer: ravastra
ms.openlocfilehash: c5316aa387de28fe1a78b336eb2e9e010c624b02
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2020
ms.locfileid: "84435431"
---
# <a name="whats-new-in-azure-blockchain-service"></a>Vad är nytt i Azure blockchain-tjänsten?

> Bli informerad om när du ska gå tillbaka till den här sidan för uppdateringar genom att kopiera och klistra in den här URL: en i din RSS feed reader- `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Blockchain+Service%22&locale=en-us` [ ![ ikon](./media/whats-new/feed-icon-16x16.png)](https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Blockchain+Service%22&locale=en-us).

Azure blockchain-tjänsten tar emot förbättringar fort löp ande. För att hålla dig uppdaterad med den senaste utvecklingen ger den här artikeln information om:

- Nya funktioner
- Versions uppgraderingar
- Kända problem

---

## <a name="may-2020"></a>Maj 2020

### <a name="version-upgrades"></a>Versions uppgraderingar

- Ubuntu-version uppgradera till 18,04
- Versions uppgradering av kvorum till 2.5.0
- Tessera version uppgradera 0.10.4

### <a name="azure-blockchain-service-supports-sending-rawprivate-transactions"></a>Azure blockchain service stöder sändning av rawPrivate-transaktioner

**Typ:** Zoomfunktionen

Kunder kan signera privata transaktioner utanför kontot på noden.

### <a name="two-phase-member-provisioning"></a>Medlems etablering i två steg

**Typ:** Höja

Två faser hjälper till att optimera scenarier där en medlem skapas i ett långt befintligt konsortium. Medlems infrastrukturen tillhandahålls i första fasen. I den andra fasen synkroniseras medlemmen med blockchain. Med hjälp av två stegs etablering kan du undvika att skapa medlemmar på grund av timeout.

## <a name="known-issues"></a>Kända problem

### <a name="mining-stops-if-fewer-than-four-validator-nodes"></a>Utvinning stoppas om färre än fyra validator-noder

Produktions nätverk måste ha minst fyra validator-noder. Kvorum rekommenderar att minst fyra validator-noder krävs för att uppfylla IBFT-krasch fel toleransen (3F + 1). Du bör ha minst två Azure blockchain-tjänstens *standard* nivå-noder för att få fyra validator-noder. En standardnod är etablerad med två verifierade noder.  

Om blockchain-nätverket på Azure blockchain-tjänsten inte har fyra validator-noder kan utvinning stoppas i nätverket. Du kan identifiera att utvinning har stoppats genom att ange en avisering på bearbetade block. I ett felfritt nätverk kommer det bearbetade blocket att vara 60 block per nod per fem minuter.

Som en minskning måste Azure blockchain Service-teamet starta om noden. Kunder måste öppna en support förfrågan för att starta om noden. Azure blockchain Service-teamet arbetar för att identifiera och åtgärda problem med gruv drift automatiskt.

Använd *standard* nivån för distributioner av produktions nivåer. Använd *Basic* -nivån för utveckling, testning och bevis på koncept. Det finns inte stöd för att ändra pris nivån mellan Basic och standard när medlems skapande har skapats.

### <a name="blockchain-data-manager-requires-standard-tier-node"></a>Blockchain-Data Manager kräver noden på standard nivån

Använd *standard* -nivån om du använder blockchain Data Manager. *Basic* -nivån har endast 4 GB minne. Därför kan det inte skalas till den användning som krävs av blockchain Data Manager och andra tjänster som körs på den.

Använd *Basic* -nivån för utveckling, testning och bevis på koncept. Det finns inte stöd för att ändra pris nivån mellan Basic och standard när medlems skapande har skapats.

### <a name="large-volume-of-unlock-account-calls-causes-geth-to-crash"></a>Stora mängder upplåsnings konto anrop orsakar Geth till krasch

En stor mängd upplåsnings konto anrop när transaktionen skickas kan orsaka att Geth kraschar på en Transaction-nod. Därför måste du sluta mata in transaktioner. Annars ökar den väntande transaktions kön.

Geth startar om automatiskt inom mindre än en minut. Synkroniseringen kan ta lång tid beroende på noden. Azure blockchain Service-teamet aktiverar en Arkiverings funktion som minskar tiden det tar att synkronisera.

Om du vill identifiera Geth-krascher kan du söka i loggar efter eventuella fel meddelanden i blockchain-meddelanden i program loggar. Du kan också kontrol lera om behandlade block minskar när väntande transaktioner ökar.

Du kan åtgärda problemet genom att skicka signerade transaktioner i stället för att skicka osignerade transaktioner med ett-kommando för att låsa upp kontot. För transaktioner som redan är signerade externt behöver du inte låsa upp kontot.

Om du vill skicka osignerade transaktioner låser du upp kontot för oändlig tid genom att skicka 0 som tids parameter i kommandot Unlock. Du kan låsa kontot igen när alla transaktioner har skickats.  

Följande är de Geth-parametrar som Azure blockchain-tjänsten använder. Du kan inte ändra parametrarna.

- Istanbul-block period: 5 sekunder
- Golv gräns för golv: 700000000
- Ceil gas gräns: 800000000

### <a name="large-volume-of-private-transactions-reduces-performance"></a>Stora mängder privata transaktioner minskar prestandan

Om du använder Azure blockchain service Basic-nivån och privata transaktioner kan Tessera krascha.

Du kan identifiera Tessera-kraschen genom att granska blockchain program loggar och söka efter meddelandet `Tessera crashed. Restarting Tessera...` .

Azure blockchain-tjänsten startar om Tessera när det uppstår en krasch. Omstart tar ungefär en minut.

Använd *standard* -nivån om du skickar en stor mängd privata transaktioner. Använd *Basic* -nivån för utveckling, testning och bevis på koncept. Det finns inte stöd för att ändra pris nivån mellan Basic och standard när medlems skapande har skapats.

### <a name="calling-ethestimate-gas-function-reduces-performance"></a>Anropar ETH. uppskatta gasens funktion minskar prestandan

Anropar funktionen *ETH. uppskattning* flera gånger minskar antalet transaktioner per sekund drastiskt. Använd inte *ETH. uppskatta* gasen för varje transaktions överföring. Funktionen *ETH. uppskattning* är minnes intensiv.

Använd om möjligt ett försiktigt gas värde för att skicka transaktioner och minimera användningen av *ETH. uppskattning*.

### <a name="unbounded-loops-in-smart-contracts-reduces-performance"></a>Obundna slingor i smarta kontrakt minskar prestandan

Undvik obundna slingor i smarta kontrakt eftersom de kan minska prestandan. Mer information finns i följande resurser:

- [Undvik obundna slingor](https://blog.b9lab.com/getting-loopy-with-solidity-1d51794622ad )
- [Metod tips för smart kontrakt säkerhet](https://github.com/ConsenSys/smart-contract-best-practices)
- [Rikt linjer för smarta kontrakt som tillhandahålls av kvorum](http://docs.goquorum.com/en/latest/Security/Framework/Decentralized%20Application/Smart%20Contracts%20Security/)
- [Rikt linjer för gasformiga gränser och slingor som tillhandahålls av Massivity](https://solidity.readthedocs.io/en/develop/security-considerations.html#gas-limit-and-loops)