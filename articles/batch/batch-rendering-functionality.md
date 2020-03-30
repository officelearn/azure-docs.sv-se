---
title: Renderingsfunktioner – Azure Batch
description: Standard Azure Batch-funktioner används för att köra renderingsarbetsbelastningar och appar. Batch innehåller specifika funktioner för att stödja renderingsarbetsbelastningar.
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 697e2640b7215e0bbb9202c672f936535831eb99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75449713"
---
# <a name="azure-batch-rendering-capabilities"></a>Funktioner för Azure Batch-rendering

Standard Azure Batch-funktioner används för att köra renderingsarbetsbelastningar och program. Batch innehåller också specifika funktioner för att stödja renderingsarbetsbelastningar.

En översikt över batchbegrepp, inklusive pooler, jobb och uppgifter, finns i [den här artikeln](https://docs.microsoft.com/azure/batch/batch-api-basics).

## <a name="batch-pools"></a>Batch pooler

### <a name="rendering-application-installation"></a>Installation av renderingsprogram

En Azure Marketplace-rendering av VM kan anges i poolkonfigurationen om bara de förinstallerade programmen behöver användas.

Det finns en Windows 2016-bild och en CentOS-avbildning.  I [Azure Marketplace](https://azuremarketplace.microsoft.com)kan vm-avbildningar hittas genom att söka efter "batchrendering".

En exempelpoolkonfiguration finns i [azure CLI-renderingsguiden](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli).  Azure-portalen och Batch Explorer tillhandahåller GUI-verktyg för att välja en avbildning av återgivnings-VM när du skapar en pool.  Om du använder ett batch-API anger du följande egenskapsvärden för [ImageReference](https://docs.microsoft.com/rest/api/batchservice/pool/add#imagereference) när du skapar en pool:

| Utgivare | Erbjudande | Sku | Version |
|---------|---------|---------|--------|
| batch | rendering-centos73 | Rendering | senaste |
| batch | rendering-windows2016 | Rendering | senaste |

Andra alternativ är tillgängliga om ytterligare program krävs på pool-datorerna:

* En anpassad bild från det delade bildgalleriet:
  * Med det här alternativet kan du konfigurera din virtuella dator med de exakta program och specifika versioner du behöver. Mer information finns i [Skapa en pool med det delade bildgalleriet](batch-sig-images.md). Autodesk och Chaos Group har ändrat Arnold respektive V-Ray för att validera mot en Azure Batch-licensieringstjänst. Se till att du har versionerna av dessa program med det här stödet, annars fungerar inte licensieringen för betalning per användning. Aktuella versioner av Maya eller 3ds Max kräver inte en licensserver när du kör huvudlös (i batch/kommandoradsläge). Kontakta Azure-supporten om du inte är säker på hur du går vidare med det här alternativet.
* [Ansökningspaket:](https://docs.microsoft.com/azure/batch/batch-application-packages)
  * Paketera programfilerna med en eller flera ZIP-filer, ladda upp via Azure-portalen och ange paketet i poolkonfigurationen. När pool-datorer skapas hämtas ZIP-filerna och filerna extraheras.
* Resursfiler:
  * Programfiler överförs till Azure blob storage och du anger filreferenser i [poolstartuppgiften](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask). När pool-virtuella datorer skapas hämtas resursfilerna till varje virtuell dator.

### <a name="pay-for-use-licensing-for-pre-installed-applications"></a>Pay-for-use-licensiering för förinstallerade program

De program som ska användas och har en licensavgift måste anges i poolkonfigurationen.

* Ange `applicationLicenses` egenskapen när [du skapar en pool](https://docs.microsoft.com/rest/api/batchservice/pool/add#request-body).  Följande värden kan anges i matrisen med strängar - "vray", "arnold", "3dsmax", "maya".
* När du anger ett eller flera program läggs kostnaden för dessa program till kostnaden för de virtuella datorerna.  Programpriser visas på [prissidan för Azure Batch](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering).

> [!NOTE]
> Om du i stället ansluter till en licensserver för `applicationLicenses` att använda renderingsprogrammen ska du inte ange egenskapen.

Du kan använda Azure-portalen eller Batch Explorer för att välja program och visa programpriserna.

Om ett försök görs att använda ett program, men programmet `applicationLicenses` inte har angetts i egenskapen för poolkonfigurationen eller inte når en licensserver, misslyckas programkörningen med ett licensfel och icke-noll-stängningskod.

### <a name="environment-variables-for-pre-installed-applications"></a>Miljövariabler för förinstallerade program

För att kunna skapa kommandoraden för renderingsuppgifter måste installationsplatsen för renderingsprogrammets körbara filer anges.  Systemmiljövariabler har skapats på Azure Marketplace VM-avbildningar, som kan användas i stället för att behöva ange faktiska sökvägar.  Dessa miljövariabler är utöver de [standardvariabler batchmiljö som skapas](https://docs.microsoft.com/azure/batch/batch-compute-node-environment-variables) för varje aktivitet.

|Program|Programmet körbar|Miljövariabel|
|---------|---------|---------|
|Autodesk 3ds Max 2018|3dsmaxcmdio.exe|3DSMAX_2018_EXEC|
|Autodesk 3ds Max 2019|3dsmaxcmdio.exe|3DSMAX_2019_EXEC|
|Autodesk Maya 2017|render.exe|MAYA_2017_EXEC|
|Autodesk Maya 2018|render.exe|MAYA_2018_EXEC|
|Kaos Grupp V-Ray Fristående|vray.exe (på annat sätt)|VRAY_3.60.4_EXEC|
Arnold 2017 kommandorad|kick.exe (på/på)|ARNOLD_2017_EXEC|
|Arnold 2018 kommandorad|kick.exe (på/på)|ARNOLD_2018_EXEC|
|Mixer|blender.exe|BLENDER_2018_EXEC|

### <a name="azure-vm-families"></a>Azure VM-familjer

Precis som med andra arbetsbelastningar varierar kraven på programsystem och prestandakraven varierar för jobb och projekt.  Ett stort antal vm-familjer är tillgängliga i Azure beroende på dina behov – lägsta kostnad, bästa pris/prestanda, bästa prestanda och så vidare.
Vissa renderingsprogram, till exempel Arnold, är CPU-baserade. andra såsom V-Ray och Blender Cycles kan använda processorer och /eller GPU: er.
En beskrivning av tillgängliga vm-familjer och VM-storlekar [finns i VM-typer och vm-storlekar](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).

### <a name="low-priority-vms"></a>Lågprioriterade virtuella datorer

Precis som med andra arbetsbelastningar kan virtuella datorer med låg prioritet användas i batchpooler för rendering.  Virtuella datorer med låg prioritet utför samma som vanliga dedikerade virtuella datorer men använder överskott av Azure-kapacitet och är tillgängliga för en stor rabatt.  Avvägningen för att använda virtuella datorer med låg prioritet är att dessa virtuella datorer kanske inte är tillgängliga för att tilldelas eller kan föregripas när som helst, beroende på tillgänglig kapacitet. Därför kommer virtuella datorer med låg prioritet inte att vara lämpliga för alla renderingsjobb. Om det till exempel tar många timmar att rendera om det är troligt att återgivningen av dessa bilder avbryts och startas om på grund av att virtuella datorer som föregrips inte skulle vara acceptabelt.

Mer information om egenskaperna för virtuella datorer med låg prioritet och de olika sätten att konfigurera dem med Batch finns i [Använda virtuella datorer med låg prioritet med Batch](https://docs.microsoft.com/azure/batch/batch-low-pri-vms).

## <a name="jobs-and-tasks"></a>Jobb och uppgifter

Inget renderingsspecifikt stöd krävs för jobb och aktiviteter.  Huvudkonfigurationsobjektet är aktivitetskommandoraden, som måste referera till det nödvändiga programmet.
När Azure Marketplace VM-avbildningar används är det bästa sättet att använda miljövariablerna för att ange sökvägen och programmets körbara.

## <a name="next-steps"></a>Nästa steg

För exempel på Batch rendering prova de två tutorials:

* [Rendering med Hjälp av Azure CLI](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli)
* [Rendering med hjälp av Batch Explorer](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender)
