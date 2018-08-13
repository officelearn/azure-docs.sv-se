---
title: Azure Batch rendering
description: Hur du använder funktioner för Azure Batch rendering
services: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: ef2abc147049d264899d227235cffe72a1a1568c
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2018
ms.locfileid: "40034808"
---
# <a name="using-azure-batch-rendering"></a>Med hjälp av Azure Batch rendering

Det finns flera sätt att använda Azure Batch rendering:

* API: er:
  * Skriva kod med hjälp av någon av Batch-API: er.  Utvecklare kan integrera Azure Batch-funktioner i deras befintliga program eller ett arbetsflöde, om molnet eller baserat på plats.
* Kommandoradsverktyg:
  * Den [Azure-kommandorad](https://docs.microsoft.com/cli/azure/) eller [PowerShell](https://docs.microsoft.com/powershell/azure/overview) kan användas för att skriva Batch användning.
  * I synnerhet de [Batch CLI mallstöd](https://docs.microsoft.com/azure/batch/batch-cli-templates) gör det mycket enklare att skapa pooler och skicka jobb.
* Batch Explorer Användargränssnittet:
  * [Batch Explorer](https://github.com/Azure/BatchLabs) är ett plattformsoberoende klientverktyg som också kan Batch-konton kan hanteras och övervakas.
  * För var och en av renderingsprogram tillhandahålls ett antal mallar för poolen och jobbet som kan användas för att enkelt skapa pooler och skicka jobb.  En uppsättning mallar finns i programmets användargränssnitt, med mallfilerna som du ansluter från GitHub.
  * Anpassade mallar som kan skapas från grunden eller de angivna mallarna från GitHub kan kopieras och ändras.
* Klienten programmet plugin-program:
  * Plugin-program är tillgängliga som gör det Batch rendering att användas direkt i klient-design och modellapplikationer.  Plugin-programmen främst anropa Batch Explorer-program med detaljerad information om den aktuella 3D-modellen och innehåller funktioner för hantering av tillgångar.

Det bästa sättet att prova Azure Batch rendering och enklaste sättet för slutanvändare som inte är utvecklare och inte Azure-experter är att använda Batch Explorer-programmet, antingen direkt eller anropas från ett klientprogram som plugin-programmet.

## <a name="using-batch-explorer"></a>Använda Batch Explorer

En stegvis självstudie för att använda Batch Explorer för att utföra rendering se den [Blender självstudien](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender).

### <a name="download-and-install"></a>Ladda ned och installera

Batch Explorer [hämtas](https://azure.github.io/BatchExplorer/) för Windows, OSX och Linux.

### <a name="using-templates-to-create-pools-and-run-jobs"></a>Använda mallar för att skapa pooler och köra jobb

En omfattande uppsättning mallar är tillgängliga för användning med Batch Explorer som gör det enkelt att skapa pooler och skicka jobb för olika renderingsprogram utan att behöva ange alla egenskaper som krävs för att skapa pooler, jobb och uppgifter direkt med Batch.  Mallar som är tillgängliga i Batch Explorer är lagrade och synliga i [en GitHub-lagringsplats](https://github.com/Azure/BatchExplorer-data/tree/master/ncj).

![Batch Explorer-galleriet](./media/batch-rendering-using/batch-explorer-gallery.png)

Mallar tillhandahålls som serva för alla program som finns på Marketplace rendering VM-avbildningar.  För varje program finns flera mallar, inklusive pool mallar för att uppfylla pooler; för Processorn och GPU-pooler, Windows och Linux jobbmallar är fullständig ram eller sida vid sida Blender rendering och V-Ray distribuerade rendering. Mallar för angivna utökas med tiden till följd av andra Batch-funktioner, till exempel pool automatisk skalning.

Det är också möjligt för anpassade mallar ska produceras från grunden eller genom att ändra de angivna mallarna. Anpassade mallar kan användas genom att markera objektet 'Lokala mallar ”i avsnittet 'Galleri' i Batch Explorer.

### <a name="file-system-and-data-movement"></a>Filen system och data movement

Avsnittet ”Data” i Batch Explorer kan filer som ska kopieras mellan ett lokalt filsystem och Azure Storage-konton.

## <a name="client-application-plug-ins"></a>Klienten programmet plugin-program

Plugin-program är tillgängliga för några av klientprogram.  Plugin-programmen kan pooler och jobb kan skapas direkt från programmet eller anropa Batch Explorer.

* [Blender](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/blender)
* [Autodesk 3ds Max](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/3ds-max)
* [Autodesk Maya](https://github.com/Azure/azure-batch-maya)

## <a name="next-steps"></a>Nästa steg

Exempel på Batch rendering testa två självstudierna:

* [Rendering med hjälp av Azure CLI](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli)
* [Rendering med hjälp av Batch Explorer](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender)