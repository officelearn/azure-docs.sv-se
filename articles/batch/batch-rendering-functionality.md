---
title: Återge funktioner – Azure Batch
description: Specifika renderingsfunktioner i Azure Batch
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: be6c0f9a8874507433606903bcbd58c7723d6a8a
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62118695"
---
# <a name="azure-batch-rendering-capabilities"></a>Azure Batch rendering-funktioner

Standard Azure Batch-funktioner används för att köra arbetsbelastningar och program. Batch innehåller också specifika funktioner för att stödja arbetsbelastningar.

En översikt över Batch-begrepp, inklusive pooler, jobb och aktiviteter, finns i [i den här artikeln](https://docs.microsoft.com/azure/batch/batch-api-basics).

## <a name="batch-pools"></a>Batch-pooler

### <a name="rendering-application-installation"></a>Rendering programinstallation

En virtuell dator för Azure Marketplace återgivningsbild kan anges i poolkonfigurationen om det bara de förinstallerade programmen behöver användas.

Det finns en Windows 2016-avbildning och en CentOS-avbildning.  I den [Azure Marketplace](https://azuremarketplace.microsoft.com), VM-avbildningar kan hittas genom att söka efter batch-återgivning.

En exempelkonfiguration av poolen finns det [självstudien för Azure CLI-rendering](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli).  Azure-portalen och Batch Explorer tillhandahåller GUI-verktyg för att välja en rendering VM-avbildning när du skapar en pool.  Om du använder ett Batch-API, och ange sedan följande egenskapsvärden för [ImageReference](https://docs.microsoft.com/rest/api/batchservice/pool/add#imagereference) när du skapar en pool:

| Utgivare | Erbjudande | Sku | Version |
|---------|---------|---------|--------|
| batch | rendering-centos73 | rendering | senaste |
| batch | rendering-windows2016 | rendering | senaste |

Andra alternativ är tillgängliga om det krävs ytterligare program på poolen med virtuella datorer:

* En anpassad avbildning baserat på en standard Marketplace-avbildning:
  * Med det här alternativet kan du konfigurera din virtuella dator med de exakta program och specifika versioner du behöver. Mer information finns i [använda en anpassad avbildning för att skapa en pool med virtuella datorer](https://docs.microsoft.com/azure/batch/batch-custom-images). Autodesk och Chaos Group har ändrat Arnold och V-Ray, för att verifiera mot ett Azure Batch licensing service. Kontrollera att du har versionerna av dessa program med det här stödet, annars inte fungerar licensieringen där du betalar per användning. Aktuella versioner av Maya eller 3ds Max inte kräver någon licensserver när körs med fjärradministrering (i batch/kommandoradsläge). Kontakta Azure-supporten om du inte vet hur du fortsätter med det här alternativet.
* [Programpaket](https://docs.microsoft.com/azure/batch/batch-application-packages):
  * Paketfiler program med hjälp av en eller flera ZIP-filer, ladda upp via Azure portal och ange paketet i poolkonfigurationen. När pool med virtuella datorer skapas, ZIP-filer laddas ned och extraherade filerna.
* Resursfiler:
  * Filer överförs till Azure blob storage och du anger filreferenser i den [pool startaktivitet](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask). När poolen virtuella datorer skapas laddas resursfiler ned till varje virtuell dator.

### <a name="pay-for-use-licensing-for-pre-installed-applications"></a>Betala för det du använder licensiering för förinstallerade program

De program som ska användas och har en licensavgift måste anges i poolkonfigurationen.

* Ange den `applicationLicenses` egenskapen när [skapar en pool](https://docs.microsoft.com/rest/api/batchservice/pool/add#request-body).  Följande värden kan anges i strängmatris - ”vray”, ”arnold”, ”3dsmax”, ”maya”.
* När du anger ett eller flera program, läggs kostnaden för dessa program till kostnaden för de virtuella datorerna.  Programmet priserna anges på den [Azure Batch prissättningssidan](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering).

> [!NOTE]
> Om du ansluter i stället till en server att använda renderingsprogram, ange den `applicationLicenses` egenskapen.

Du kan använda Azure portal eller Batch Explorer för att välja program och visa program-priserna.

Om ett försök görs att använda ett program, men programmet har inte angetts i den `applicationLicenses` egenskapen för konfigurering av lagringspool eller stöder inte nå en server och sedan programmet körningen misslyckas med ett licensiering fel och noll slutkoden.

### <a name="environment-variables-for-pre-installed-applications"></a>Miljövariabler för förinstallerade program

Om du vill kunna skapa kommandoraden för renderingsuppgifter måste installationsplatsen för rendering programmet körbara filer anges.  Systemmiljövariabler har skapats på Azure Marketplace-VM-avbildningar, som kan användas i stället för att behöva ange faktiska sökvägarna.  Dessa miljövariabler är utöver den [standard Batch-miljövariablerna](https://docs.microsoft.com/azure/batch/batch-compute-node-environment-variables) skapas för varje aktivitet.

|Program|Körbara programfilen|Miljövariabel|
|---------|---------|---------|
|Autodesk 3ds Max 2018|3dsmaxcmdio.exe|3DSMAX_2018_EXEC|
|Autodesk 3ds Max 2019|3dsmaxcmdio.exe|3DSMAX_2019_EXEC|
|Autodesk Maya 2017|render.exe|MAYA_2017_EXEC|
|Autodesk Maya 2018|render.exe|MAYA_2018_EXEC|
|Chaos Group V-Ray fristående|vray.exe|VRAY_3.60.4_EXEC|
2017 Arnold-kommandorad|kick.exe|ARNOLD_2017_EXEC|
|2018 Arnold-kommandorad|kick.exe|ARNOLD_2018_EXEC|
|Blender|blender.exe|BLENDER_2018_EXEC|

### <a name="azure-vm-families"></a>Azure VM-serier

Systemkrav för rendering program varierar precis som med andra arbetsbelastningar och prestandakrav variera för jobb och projekt.  Det finns många olika VM-serier i Azure beroende på dina behov – lägst kostnad, bästa pris/prestanda, bästa prestanda och så vidare.
Vissa renderingsprogram, till exempel Arnold, är CPU-baserade; andra, till exempel V-Ray och Blender cykler kan använda processorer och/eller GPU: er.
En beskrivning av tillgängliga VM-familjer och storlekar, [finns i VM-typer och storlekar](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).

### <a name="low-priority-vms"></a>Lågprioriterade virtuella datorer

Precis som med andra arbetsbelastningar kan till exempel använda lågprioriterade virtuella datorer i Batch-pooler för återgivning.  Lågprioriterade virtuella datorer fungerar som vanliga dedikerade virtuella datorer men använder Azure överkapacitet och är tillgängliga för stora rabatter.  Nackdelen för att använda lågprioriterade virtuella datorer är att dessa virtuella datorer kanske inte tillgänglig för att allokera eller kan avbrytas när som helst, beroende på tillgänglig kapacitet. Därför kommer inte lågprioriterade virtuella datorer att vara lämplig för alla Renderingsjobb. Till exempel om avbildningar ta flera timmar att återge så är det sannolikt som återgivning av dessa avbildningar avbryts och startas om på grund av virtuella datorer som frånkopplas skulle användas.

Mer information om egenskaperna för lågprioriterade virtuella datorer och de olika sätten att konfigurera dem med hjälp av Batch, se [använda virtuella datorer med låg prioritet med Batch](https://docs.microsoft.com/azure/batch/batch-low-pri-vms).

## <a name="jobs-and-tasks"></a>Jobb och aktiviteter

Inga rendering supportavtal krävs för jobben och aktiviteterna.  Det huvudsakliga konfigurationsobjektet är aktivitetens kommandorad, som måste referera till programmet.
När Azure Marketplace-VM-avbildningar används, är det bästa sättet att använda miljövariablerna för att ange sökvägen och körbara programfilen.

## <a name="next-steps"></a>Nästa steg

Exempel på Batch rendering testa två självstudierna:

* [Rendering med hjälp av Azure CLI](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli)
* [Rendering med hjälp av Batch Explorer](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender)
