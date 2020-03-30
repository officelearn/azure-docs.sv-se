---
title: Begränsningar för Azure Blockchain-tjänsten
description: Översikt över tjänsten och funktionsgränserna i Azure Blockchain Service
ms.date: 11/22/2019
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: f4001ee520f3f3136d1bac5ca047c80526fc92e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74455654"
---
# <a name="limits-in-azure-blockchain-service"></a>Begränsningar i Azure Blockchain-tjänsten

Azure Blockchain Service har tjänst- och funktionsgränser som antalet noder som en medlem kan ha, konsortiumbegränsningar och lagringsbelopp.

## <a name="pricing-tier"></a>Prisnivå

Maximala gränser för transaktioner och validerarnoder beror på om du etablerar Azure Blockchain-tjänst på grundläggande eller standardprisnivåer.

| Prisnivå | Max transaktionsnoder | Max validerare noder |
|:---|:---:|:---:|
| Basic | 10 | 1 |
| Standard | 10 | 2 |

Det går inte att ändra prisnivån mellan Basic och Standard när medlemmen har skapats.

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

    De måste i stället återinbjeras för att gå med i konsortiet och skapa en ny medlem. Deras befintliga medlemsresurs tas inte bort för att bevara historiska transaktioner.

* **Alla medlemmar i ett konsortium måste använda samma redovisningsversion**

    Mer information om korrigerings-, uppdaterings- och redovisningsversioner som är tillgängliga i Azure Blockchain Service finns i [Korrigering, uppdateringar och versioner](ledger-versions.md).

## <a name="next-steps"></a>Nästa steg

Läs mer om principer för systemkorrigering och uppgraderingar – [Korrigering, uppdateringar och versioner](ledger-versions.md).
