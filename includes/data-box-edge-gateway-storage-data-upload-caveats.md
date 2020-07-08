---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/21/2019
ms.author: alkohli
ms.openlocfilehash: 8c87e14071b3bb40421ab655c172df739570e295
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "67187798"
---
Följande varningar gäller för data när de flyttas till Azure.

- Vi rekommenderar att fler än en enhet inte skriver till samma behållare.
- Om du har ett befintligt Azure-objekt (till exempel en BLOB eller en fil) i molnet med samma namn som det objekt som ska kopieras, kommer enheten att skriva över filen i molnet.
- En tom katalogpartition (utan några filer) som skapas under delade mappar överförs inte till BLOB-behållarna.
- Du kan kopiera data med hjälp av dra och släpp med Utforskaren eller via kommando raden. Om den sammanställda storleken på filer som kopieras är större än 10 GB rekommenderar vi att du använder ett Mass kopierings program som Robocopy eller rsync. Verktyget för Mass kopiering gör om kopierings åtgärden för tillfälliga fel och ger ytterligare återhämtnings kapacitet.
- Om resursen som är kopplad till Azure Storage-behållaren överför blobbar som inte matchar den typ av blobbar som definierats för resursen vid tidpunkten för skapandet, uppdateras inte sådana blobbar. Du kan till exempel skapa en Block-Blob-resurs på enheten. Associera resursen med en befintlig moln behållare som har Page blobbar. Uppdatera den resursen för att ladda ned filerna. Ändra några av de uppdaterade filerna som redan är lagrade som Page blobbar i molnet. Du kommer att se uppladdnings felen.
