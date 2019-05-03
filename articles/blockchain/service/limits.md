---
title: Azure Blockchain gränser
description: Översikt över tjänsten och funktionella gränser i Azure Blockchain-tjänsten
services: azure-blockchain
keywords: blockchain
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: 169ec7a8ef407af3f754046aa8e3b06793a7e962
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028177"
---
# <a name="limits-in-azure-blockchain-service"></a>Begränsningar i Azure Blockchain-tjänst

Azure Blockchain Service har tjänsten och funktionella begränsningar, till exempel antalet noder som en medlem kan ha consortium begränsningar och lagring belopp.

## <a name="pricing-tier"></a>Prisnivå

Övre gräns för transaktioner och verifieraren noder beror på om du etablerar Azure Blockchain-tjänsten på Basic eller Standard prisnivåer.

| Prisnivå | Maximalt antal transaktioner noder | Max verifieraren noder |
|:---|:---:|:---:|
| Basic | 10 | 1 |
| Standard | 10 | 2 |

Ändra prisnivån mellan Basic och Standard efter skapande av medlem inte stöds.

## <a name="storage-capacity"></a>Lagringskapacitet

Den maximala mängden lagringsutrymme som används per nod för redovisningsdata och loggar är 1 terabyte.

Minska lagringsstorlek redovisning och log stöds inte.

## <a name="consortium-limits"></a>Consortium gränser

* **Consortium och medlemmen måste vara unika** från alla andra consortium och medlem i Azure Blockchain-tjänsten.

* **Medlemmen och consortium namn kan inte ändras**

* **Alla medlemmar i ett konsortium måste finnas i samma prisnivå**

* **Alla medlemmar som deltar i ett konsortium måste finnas i samma region**

    Den första medlemmen som skapats i ett konsortium avgör regionen. Inbjudna medlemmar att consortium måste finnas i samma region som den första medlemmen. Begränsa alla medlemmar samma region säkerställer nätverk konsensus inte påverkas negativt.

* **Ett konsortium måste ha minst en administratör**

    Om det finns bara en administratör i ett konsortium, kan inte de ta bort själva från consortium eller ta bort sina medlemmar tills en annan administratör har lagts till eller framhävs i consortium.

* **Medlemmar tas bort från consortium kan inte läggas till igen**

    I stället måste de bjudas in att delta consortium och skapa en ny medlem. Deras befintlig medlem resurs tas inte bort om du vill bevara historiska transaktioner.

* **Alla medlemmar i ett konsortium måste använda samma transaktionsregister version**

    Mer information om uppdatering, uppdateringar och redovisning-versioner som är tillgängliga i Azure Blockchain-tjänsten finns i [uppdatering, uppdateringar och versioner](ledger-versions.md).

## <a name="next-steps"></a>Nästa steg

* [Uppdatering, uppdateringar och versioner](ledger-versions.md)
