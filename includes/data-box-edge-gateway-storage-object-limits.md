---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/04/2019
ms.author: alkohli
ms.openlocfilehash: 563849d3875ed0156d81770f58340633d90d515b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67187796"
---
Här är storleken på Azure-objekt som kan skrivas. Kontrollera att alla filer som överförs överensstämmer med dessa gränser.

| Azure-objekttyp | Överladdningsgräns                                             |
|-------------------|-----------------------------------------------------------|
| Block Blob        | ~ 4,75 TB                                                 |
| Sidblob         | 1 TB <br> Varje fil som laddas upp i Sidblobbformat måste vara 512 byte justerad (en integrerad multipel), annars misslyckas överföringen. <br> DEN VHD och VHDX är 512 byte justerat. |
| Azure Files         | 1 TB <br> Varje fil som laddas upp i Sidblobbformat måste vara 512 byte justerad (en integrerad multipel), annars misslyckas överföringen. <br> DEN VHD och VHDX är 512 byte justerat. |

> [!IMPORTANT]
> Skapande av filer (oavsett lagringstyp) tillåts upp till 5 TB. Om du däremot skapar en fil vars storlek är större än överföringsgränsen som definierats i föregående tabell, överförs inte filen. Du måste manuellt ta bort filen för att frigöra utrymmet.