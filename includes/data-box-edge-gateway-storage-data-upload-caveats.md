---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/14/2019
ms.author: alkohli
ms.openlocfilehash: 8bc4b78d262cf5f30076e90b40b92c4f3237924a
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/16/2019
ms.locfileid: "56334023"
---
Följande villkor gäller för data som flyttas till Azure.

- Vi rekommenderar att mer än en enhet inte bör skriva till samma behållare.
- Om du har ett befintligt Azure objekt (till exempel en blob eller en fil) i molnet med samma namn som det objekt som ska kopieras skrivs enhet till filen i molnet.
- En tom katalog-hierarki (utan några filer) som skapats under dela mappar inte laddas upp till blob-behållare.
- För stora filer rekommenderar vi att du använder robocopy eftersom ett nytt försök görs kopieringsåtgärden för tillfälliga fel.
- Om resursen som är associerade med Azure-lagringsbehållare överför BLOB-objekt som inte matchar typ av BLOB-objekt som definierats för resursen när den skapas, uppdateras inte sådan BLOB-objekt. Exempelvis kan skapa du en block blob-resurs på enheten. Koppla resursen till en befintlig cloud-behållare som har sidblobar. Uppdatera resursen för att hämta filerna. Ändra några av de uppdatera filer som redan lagras som sidblobar i molnet. Du kommer att se uppladdningsfel.
