---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/21/2019
ms.author: alkohli
ms.openlocfilehash: 8c87e14071b3bb40421ab655c172df739570e295
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2019
ms.locfileid: "56660483"
---
Följande villkor gäller för data som flyttas till Azure.

- Vi rekommenderar att mer än en enhet inte bör skriva till samma behållare.
- Om du har ett befintligt Azure objekt (till exempel en blob eller en fil) i molnet med samma namn som det objekt som ska kopieras skrivs enhet till filen i molnet.
- En tom katalog-hierarki (utan några filer) som skapats under dela mappar inte laddas upp till blob-behållare.
- Du kan kopiera data med dra och släppa med Utforskaren eller via kommandoraden. Om den sammanlagda storleken på filerna kopieras är större än 10 GB, rekommenderar vi du använder ett program som bulk copy program, till exempel Robocopy eller rsync. Verktyg för bulk copy försök kopieringsåtgärden för tillfälliga fel och ger ytterligare återhämtning.
- Om resursen som är associerade med Azure-lagringsbehållare överför BLOB-objekt som inte matchar typ av BLOB-objekt som definierats för resursen när den skapas, uppdateras inte sådan BLOB-objekt. Exempelvis kan skapa du en block blob-resurs på enheten. Koppla resursen till en befintlig cloud-behållare som har sidblobar. Uppdatera resursen för att hämta filerna. Ändra några av de uppdatera filer som redan lagras som sidblobar i molnet. Du kommer att se uppladdningsfel.
