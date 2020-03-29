---
title: Använda renderingsfunktioner - Azure Batch
description: Så här använder du Azure Batch-renderingsfunktioner. Prova att använda batchutforskaren, antingen direkt eller anropas från ett plugin-program för klientprogram.
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 03/05/2020
ms.topic: conceptual
ms.openlocfilehash: f3b2e641ab187514a7900b2ab7cc75068df00252
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672010"
---
# <a name="using-azure-batch-rendering"></a>Använda Azure Batch-rendering

Det finns flera sätt att använda Azure Batch-rendering:

* API:er:
  * Skriv kod med någon av batch-API:erna.  Utvecklare kan integrera Azure Batch-funktioner i sina befintliga program eller arbetsflöden, oavsett om de är i molnet eller lokalt.
* Kommandoradsverktyg:
  * [Azure-kommandoraden](https://docs.microsoft.com/cli/azure/) eller [PowerShell](https://docs.microsoft.com/powershell/azure/overview) kan användas för att skriptbatch använda.
  * I synnerhet batch [CLI-mallens stöd](https://docs.microsoft.com/azure/batch/batch-cli-templates) gör det mycket enklare att skapa pooler och skicka jobb.
* Användargränssnittet för batchutforskaren:
  * [Batch Explorer](https://github.com/Azure/BatchLabs) är ett plattformsoberoende klientverktyg som också gör att Batch-konton kan hanteras och övervakas.
  * För vart och ett av renderingsprogrammen tillhandahålls ett antal pool- och jobbmallar som kan användas för att enkelt skapa pooler och skicka jobb.  En uppsättning mallar visas i programgränssnittet, där mallfilerna nås från GitHub.
  * Anpassade mallar kan skapas från grunden eller så kan de medföljande mallarna från GitHub kopieras och ändras.
* Plugin-program för klientprogram:
  * Plugin-program finns tillgängliga som gör att Batch-rendering kan användas direkt från klientdesign- och modelleringsprogram.  Plugin-programmen anropar huvudsakligen Batch Explorer-programmet med kontextuell information om den aktuella 3D-modellen och innehåller funktioner som hjälper till att hantera tillgångar.

Det bästa sättet att prova Azure Batch-rendering och enklaste sättet för slutanvändare, som inte är utvecklare och inte Azure-experter, är att använda Batch Explorer-programmet, antingen direkt eller anropas från ett plugin-program för klientprogram.

## <a name="using-batch-explorer"></a>Använda batchutforskaren

En steg-för-steg-självstudiekurs för att använda Batch Explorer för att utföra rendering finns i [Blender-självstudien](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender).

### <a name="download-and-install"></a>Ladda ner och installera

Batch [Explorer-nedladdningar är tillgängliga](https://azure.github.io/BatchExplorer/) för Windows, OSX och Linux.

### <a name="using-templates-to-create-pools-and-run-jobs"></a>Använda mallar för att skapa pooler och köra jobb

En omfattande uppsättning mallar är tillgängliga för användning med Batch Explorer som gör det enkelt att skapa pooler och skicka jobb för de olika renderingsprogrammen utan att behöva ange alla egenskaper som krävs för att skapa pooler, jobb och uppgifter direkt med Batch.  Mallarna som är tillgängliga i Batch Explorer lagras och visas i [en GitHub-databas](https://github.com/Azure/BatchExplorer-data/tree/master/ncj).

![Galleriet Batch Explorer](./media/batch-rendering-using/batch-explorer-gallery.png)

Mallar tillhandahålls som tillgodoser alla program som finns på Marketplace-rendering av VM-avbildningar.  För varje program finns flera mallar, inklusive poolmallar för att tillgodose CPU- och GPU-pooler, Windows- och Linux-pooler. jobbmallar inkluderar hel bildruta eller sida vid sida Mixer rendering och V-Ray distribuerad rendering. Uppsättningen medföljande mallar kommer att utökas med tiden för att tillgodose andra batchfunktioner, till exempel automatisk skalning av poolen.

Det är också möjligt att skapa anpassade mallar, från grunden eller genom att ändra de medföljande mallarna. Anpassade mallar kan användas genom att välja objektet "Lokala mallar" i avsnittet Galleri i Batch Explorer.

### <a name="file-system-and-data-movement"></a>Filsystem och data förflyttning

Avsnittet "Data" i Batch Explorer gör att filer kan kopieras mellan ett lokalt filsystem och Azure Storage-konton.

## <a name="client-application-plug-ins"></a>Plugin-program för klientprogram

Plugin-program är tillgängliga för vissa klientprogram.  Plugin-programmen gör att pooler och jobb kan skapas direkt från programmet eller anropa Batch Explorer.

* [Mixer 2,79](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/blender)
* [Mixer 2,8+](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/blender28)
* [Autodesk 3ds Max](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/3ds-max)
* [Autodesk Maya](https://github.com/Azure/azure-batch-maya)

## <a name="next-steps"></a>Nästa steg

För exempel på Batch rendering prova de två tutorials:

* [Rendering med Hjälp av Azure CLI](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli)
* [Rendering med hjälp av Batch Explorer](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender)