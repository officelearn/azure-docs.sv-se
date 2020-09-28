---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 09/25/2020
ms.author: alkohli
ms.openlocfilehash: 6dc201af2271909de15af9bac1a2e2bb68faed1a
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2020
ms.locfileid: "91401077"
---
Följande varningar gäller för data när de flyttas till Azure.

- Vi rekommenderar att fler än en enhet inte skriver till samma behållare.
- Om du har ett befintligt Azure-objekt (till exempel en BLOB eller en fil) i molnet med samma namn som det objekt som ska kopieras, kommer enheten att skriva över filen i molnet.
- En tom katalogpartition (utan några filer) som skapas under delade mappar överförs inte till BLOB-behållarna.
- Du kan kopiera data med hjälp av dra och släpp med Utforskaren eller via kommando raden. Om den sammanställda storleken på filer som kopieras är större än 10 GB rekommenderar vi att du använder ett Mass kopierings program som Robocopy eller rsync. Verktyget för Mass kopiering gör om kopierings åtgärden för tillfälliga fel och ger ytterligare återhämtnings kapacitet.
- Om resursen som är kopplad till Azure Storage-behållaren överför blobbar som inte matchar den typ av blobbar som definierats för resursen vid tidpunkten för skapandet, uppdateras inte sådana blobbar. Du kan till exempel skapa en Block-Blob-resurs på enheten. Associera resursen med en befintlig moln behållare som har Page blobbar. Uppdatera den resursen för att ladda ned filerna. Ändra några av de uppdaterade filerna som redan är lagrade som Page blobbar i molnet. Du kommer att se uppladdnings felen.
- När en fil har skapats i resurserna stöds inte namnbytet för filen.
- När en fil tas bort från en resurs försvinner inte posten i lagringskontot.
- Om du använder rsync för att kopiera data, `rsync -a` stöds inte alternativet.

