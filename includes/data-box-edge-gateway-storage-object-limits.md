---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/04/2019
ms.author: alkohli
ms.openlocfilehash: 563849d3875ed0156d81770f58340633d90d515b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "67187796"
---
Här följer storleken på de Azure-objekt som kan skrivas. Se till att alla filer som överförs följer dessa gränser.

| Objekt typ för Azure | Uppladdnings gräns                                             |
|-------------------|-----------------------------------------------------------|
| Blockera BLOB        | ~ 4,75 TB                                                 |
| Sid-BLOB         | 1 TB <br> Alla filer som överförs i Page BLOB-format måste vara 512 byte-justerade (en integral), annars Miss lyckas överföringen. <br> VHD och VHDX är 512 byte-justerade. |
| Azure Files         | 1 TB <br> Alla filer som överförs i Page BLOB-format måste vara 512 byte-justerade (en integral), annars Miss lyckas överföringen. <br> VHD och VHDX är 512 byte-justerade. |

> [!IMPORTANT]
> Att skapa filer (oberoende av lagrings typen) tillåts upp till 5 TB. Men om du skapar en fil vars storlek är större än den uppladdnings gräns som anges i tabellen ovan överförs inte filen. Du måste ta bort filen manuellt för att frigöra utrymme.