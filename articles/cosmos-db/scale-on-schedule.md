---
title: Skala Azure Cosmos DB enligt ett schema med hjälp av Azure Functions-timer
description: Lär dig hur du skalar ändringar i dataflödet i Azure Cosmos DB med PowerShell och Azure Functions.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: mjbrown
ms.openlocfilehash: 68ba40ea212c061fa5c8bbddc47ea0dfc6d8caa4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75935173"
---
# <a name="scale-azure-cosmos-db-throughput-by-using-azure-functions-timer-trigger"></a>Skala Azure Cosmos DB-dataflöde med hjälp av Azure Functions Timer-utlösare

Prestanda för ett Azure Cosmos-konto baseras på mängden etablerat dataflöde som uttrycks i begärandenheter per sekund (RU/s). Avsättningen sker på en andra granularitet och faktureras baserat på den högsta RU/s per timme. Den här etablerade kapacitetsmodellen gör det möjligt för tjänsten att tillhandahålla ett förutsägbart och konsekvent dataflöde, garanterad låg latens och hög tillgänglighet. De flesta produktionsarbetsbelastningar dessa funktioner. Men i utvecklings- och testmiljöer där Azure Cosmos DB endast används under arbetstid kan du skala upp dataflödet på morgonen och skala ner på kvällen efter arbetstid.

Du kan ange dataflödet via [Azure Resource Manager-mallar,](resource-manager-samples.md) [Azure CLI](cli-samples.md)och [PowerShell](powershell-samples-sql.md), för SQL-API-konton (Core) eller med hjälp av språkspecifika Azure Cosmos DB SDK:er. Fördelen med att använda Resource Manager-mallar, Azure CLI eller PowerShell är att de stöder alla Azure Cosmos DB-modell-API:er.

## <a name="throughput-scheduler-sample-project"></a>Exempelprojekt för dataflödesschemaläggare

För att förenkla processen för att skala Azure Cosmos DB enligt ett schema har vi skapat ett exempelprojekt som heter [Azure Cosmos-dataflödesschemaläggare](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler). Det här projektet är en Azure Functions-app med två timerutlösare- "ScaleUpTrigger" och "ScaleDownTrigger". Utlösarna kör ett PowerShell-skript som anger dataflödet för `resources.json` varje resurs enligt definitionen i filen i varje utlösare. ScaleUpTrigger är konfigurerad för att köras vid 8 AM UTC och ScaleDownTrigger är konfigurerad för att `function.json` köras vid 6 PM UTC och dessa tider kan enkelt uppdateras i filen för varje utlösare.

Du kan klona det här projektet lokalt, ändra det för att ange Azure Cosmos DB-resurser för att skala upp och ned och schemat som ska köras. Senare kan du distribuera den i en Azure-prenumeration och skydda den med hjälp av hanterad tjänstidentitet med [RBAC-behörigheter (Role-based Access Control)](role-based-access-control.md) med rollen "Azure Cosmos DB operator" för att ange dataflöde på dina Azure Cosmos-konton.

## <a name="next-steps"></a>Efterföljande moment

- Läs mer och hämta exemplet från [Azure Cosmos DB-dataflödesschemaläggare](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler).
