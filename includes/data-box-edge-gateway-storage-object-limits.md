---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/04/2019
ms.author: alkohli
ms.openlocfilehash: 563849d3875ed0156d81770f58340633d90d515b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66161291"
---
Här följer storlekarna på de Azure-objekt som kan skrivas. Se till att alla filer som har laddats upp överensstämmer med de här gränserna.

| Azure objekttyp | Uppladdningsgräns                                             |
|-------------------|-----------------------------------------------------------|
| Blockblob        | ~ 4,75 TB                                                 |
| Sidblob         | 1 TB <br> Varje fil som laddats upp Sidblobb format måste vara justerad 512 byte (en integrerad flera), annars överföringen misslyckas. <br> VHD- och VHDX är 512 byte justerad. |
| Azure Files         | 1 TB <br> Varje fil som laddats upp Sidblobb format måste vara justerad 512 byte (en integrerad flera), annars överföringen misslyckas. <br> VHD- och VHDX är 512 byte justerad. |

> [!IMPORTANT]
> Upp till 5 TB är tillåtet att skapa filer (oavsett vilken lagringstyp). Men om du skapar en fil vars storlek är större än uppladdningsgräns som definierats i tabellen ovan inte få överföra filen. Du måste manuellt ta bort filen om du vill frisläppa utrymmet.