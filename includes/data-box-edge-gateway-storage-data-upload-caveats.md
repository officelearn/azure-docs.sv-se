---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/21/2019
ms.author: alkohli
ms.openlocfilehash: 8c87e14071b3bb40421ab655c172df739570e295
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67187798"
---
Följande varningar gäller för data när de flyttas till Azure.

- Vi föreslår att mer än en enhet inte ska skriva till samma behållare.
- Om du har ett befintligt Azure-objekt (till exempel en blob eller en fil) i molnet med samma namn som objektet som kopieras, kommer enheten att skriva över filen i molnet.
- En tom kataloghierarki (utan några filer) som skapas under resursmappar överförs inte till blob-behållarna.
- Du kan kopiera data med dra och släpp med Utforskaren eller via kommandoraden. Om den sammanlagda storleken på filer som kopieras är större än 10 GB rekommenderar vi att du använder ett masskopieringsprogram som Robocopy eller rsync. Masskopieringsverktygen försöker igen kopieringen för intermittenta fel och ger ytterligare återhämtning.
- Om resursen som är associerad med Azure-lagringsbehållaren överför blobbar som inte matchar den typ av blobbar som definierats för resursen vid tidpunkten för skapandet, uppdateras inte sådana blobbar. Du kan till exempel skapa en blockblolobresurs på enheten. Associera resursen med en befintlig molnbehållare som har sidblobar. Uppdatera den resursen för att hämta filerna. Ändra några av de uppdaterade filer som redan har lagrats som sidblobar i molnet. Du kommer att se uppladdningsfel.
