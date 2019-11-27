---
title: Service gränser i Azure blockchain
description: Översikt över tjänster och funktionella gränser i Azure blockchain-tjänsten
ms.date: 11/22/2019
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: f4001ee520f3f3136d1bac5ca047c80526fc92e6
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455654"
---
# <a name="limits-in-azure-blockchain-service"></a>Gränser i Azure blockchain-tjänsten

Azure blockchain-tjänsten har tjänster och funktionella gränser, till exempel antalet noder som en medlem kan ha, konsortiets begränsningar och lagrings mängder.

## <a name="pricing-tier"></a>Prisnivå

De maximala gränserna för transaktioner och validator-noder beror på om du etablerar Azure blockchain service på Basic-eller standard pris nivån.

| Prisnivå | Max transaktions noder | Max verifierings-noder |
|:---|:---:|:---:|
| Basic | 10 | 1 |
| Standard | 10 | 2 |

Det finns inte stöd för att ändra pris nivån mellan Basic och standard när medlems skapande har skapats.

## <a name="storage-capacity"></a>Lagringskapacitet

Den maximala mängden lagring som kan användas per nod för redovisnings data och loggar är 1,8 terabyte.

Det finns inte stöd för minskning av redovisning och logg lagrings storlek.

## <a name="consortium-limits"></a>Konsortial gränser

* **Konsortiet och medlems namn måste vara unika** för andra konsortier och medlems namn i Azure blockchain-tjänsten.

* **Det går inte att ändra namn på medlemmar och konsortier**

* **Alla medlemmar i ett konsortium måste vara på samma pris nivå**

* **Alla medlemmar som ingår i ett konsortium måste finnas i samma region**

    Den första medlemmen som skapas i ett konsortium bestämmer regionen. Inbjudna medlemmar till konsortiet måste finnas i samma region som den första medlemmen. Att begränsa alla medlemmar till samma region bidrar till att säkerställa att nätverks konsensus inte påverkas negativt.

* **Ett konsortium måste ha minst en administratör**

    Om det bara finns en administratör i ett konsortium, kan de inte ta bort sig själva från konsortiet eller ta bort sina medlemmar förrän en annan administratör läggs till eller höjs i konsortiet.

* **Medlemmar som har tagits bort från konsortiet kan inte läggas till igen**

    De måste i stället ombjudas till att ansluta till konsortiet och skapa en ny medlem. Deras befintliga medlems resurs tas inte bort för att bevara historiska transaktioner.

* **Alla medlemmar i ett konsortium måste använda samma redovisnings version**

    Mer information om korrigeringar, uppdateringar och redovisnings versioner som är tillgängliga i Azure blockchain-tjänsten finns i [korrigering, uppdateringar och versioner](ledger-versions.md).

## <a name="next-steps"></a>Nästa steg

Lär dig mer om principer för system uppdatering och uppgraderingar – [uppdatering, uppdateringar och versioner](ledger-versions.md).
