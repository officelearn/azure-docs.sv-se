---
title: Begränsningar för Azure Blockchain-tjänsten
description: Översikt över tjänsten och funktionsgränserna i Azure Blockchain Service
ms.date: 04/02/2020
ms.topic: conceptual
ms.reviewer: ravastra
ms.openlocfilehash: 71e1bebf10fa0142870d03977182472da1ad031f
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80676521"
---
# <a name="limits-in-azure-blockchain-service"></a>Begränsningar i Azure Blockchain-tjänsten

Azure Blockchain Service har tjänst- och funktionsgränser som antalet noder som en medlem kan ha, konsortiumbegränsningar och lagringsbelopp.

## <a name="pricing-tier"></a>Prisnivå

Maximala gränser för transaktioner och validerarnoder beror på om du etablerar Azure Blockchain-tjänst på grundläggande eller standardprisnivåer.

| Prisnivå | Max transaktionsnoder | Max validerare noder |
|:---|:---:|:---:|
| Basic | 10 | 1 |
| Standard | 10 | 2 |

Ditt konsortienätverk bör ha minst två Standardnoder för Azure Blockchain-tjänst. Standardnivånoder innehåller två validerarnoder. Fyra validator noder krävs för att uppfylla [Istanbul bysantinska feltolerans konsensus](https://github.com/jpmorganchase/quorum/wiki/Quorum-Consensus).

Använd den grundläggande nivån är för utveckling, testning och bevis på begrepp. Använd standardnivån för distributioner av produktionsresultat. Du bör också använda *standardnivån* om du använder Blockchain Data Manager eller skickar en stor mängd privata transaktioner.

Det går inte att ändra prisnivån mellan grundläggande och standard när medlemmen har skapats.

## <a name="storage-capacity"></a>Lagringskapacitet

Den maximala mängden lagringsutrymme som kan användas per nod för redovisningsdata och loggar är 1,8 terabyte.

Det går inte att minska redovisnings- och logglagringsstorleken.
## <a name="consortium-limits"></a>Konsortiegränser

* **Konsortie- och medlemsnamn måste vara unika** från andra konsortium- och medlemsnamn i Azure Blockchain-tjänsten.

* **Medlems- och konsortienamn kan inte ändras**

* **Alla medlemmar i ett konsortium måste besätta samma prisnivå**

* **Alla medlemmar som deltar i ett konsortium måste vara bosatta i samma region**

    Den första medlemmen som skapas i ett konsortium dikterar regionen. Inbjudna medlemmar till konsortiet måste vara bosatta i samma region som den första medlemmen. Att begränsa alla medlemmar till samma region bidrar till att säkerställa att nätverkskonsensen inte påverkas negativt.

* **Ett konsortium måste ha minst en administratör**

    Om det bara finns en administratör i ett konsortium kan de inte ta bort sig själva från konsortiet eller ta bort sin medlem förrän en annan administratör läggs till eller befordras i konsortiet.

* **Medlemmar som tagits bort från konsortiet kan inte läggas till igen**

    De måste i stället återinbjeras för att gå med i konsortiet och skapa en ny medlem. Deras befintliga medlemsresurser tas inte bort för att bevara historiska transaktioner.

* **Alla medlemmar i ett konsortium måste använda samma redovisningsversion**

    Mer information om korrigerings-, uppdaterings- och redovisningsversioner som är tillgängliga i Azure Blockchain Service finns i [Korrigering, uppdateringar och versioner](ledger-versions.md).

## <a name="performance"></a>Prestanda

Använd inte *eth.estimate* gas funktion för varje transaktion inlämning. *Eth.estimate-funktionen* är minnesintensiv. Att anropa funktionen flera gånger minskar antalet transaktioner per sekund drastiskt.

Använd om möjligt ett konservativt gasvärde för att skicka in transaktioner och minimera användningen av *eth.estimate*.

## <a name="next-steps"></a>Nästa steg

Läs mer om principer för systemkorrigering och uppgraderingar – [Korrigering, uppdateringar och versioner](ledger-versions.md).
