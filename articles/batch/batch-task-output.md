---
title: Spara resultatet eller loggar från slutförda jobb och aktiviteter till ett datalager – Azure Batch | Microsoft Docs
description: Läs mer om olika alternativ för att spara utdata från Batch-aktiviteter och jobb. Du kan spara data till Azure Storage eller till ett annat datalager.
services: batch
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: 16e12d0e-958c-46c2-a6b8-7843835d830e
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 11/14/2018
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ff7224b342aa421c576c170f3c23ac64cad9f161
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55474353"
---
# <a name="persist-job-and-task-output"></a>Bevara jobb- och uppgiftsutdata

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Några vanliga exempel på uppgiftsutdata är:

- Filer som skapas när indata för aktiviteten-processer.
- Loggfiler som associeras med körning av aktiviteten.

Den här artikeln beskrivs olika alternativ för att bevara uppgiftsutdata.

## <a name="options-for-persisting-output"></a>Alternativ för att spara utdata

Det finns några olika metoder som du kan använda för att bevara uppgiftsutdata beroende på ditt scenario:

- [Använda API: et för Batch-tjänsten](batch-task-output-files.md).  
- [Använd Batch File Conventions-bibliotek för .NET](batch-task-output-file-conventions.md).  
- Implementera Batch File Conventions-standard i ditt program.
- Implementera en lösning för flytt av anpassad fil.

I följande avsnitt beskrivs kortfattat varje metod samt allmänna designöverväganden för bestående utdata.

### <a name="use-the-batch-service-api"></a>Använda API: et för Batch-tjänsten

Batch-tjänsten har stöd för att ange utdatafilerna i Azure Storage för uppgiftsdata när du [lägger till ett jobb](https://docs.microsoft.com/rest/api/batchservice/add-a-task-to-a-job) eller [lägga till en samling aktiviteter i ett jobb](https://docs.microsoft.com/rest/api/batchservice/add-a-collection-of-tasks-to-a-job).

Mer information om bestående uppgiftsutdata med API: et för Batch-tjänsten finns i [spara uppgiftsdata till Azure Storage med Batch service API](batch-task-output-files.md).

### <a name="use-the-batch-file-conventions-library-for-net"></a>Använd Batch File Conventions-bibliotek för .NET

Batch definierar en valfri uppsättning namnkonventionerna för uppgiften utdatafilerna i Azure Storage. Den [Batch File Conventions standard](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions) beskriver dessa konventioner. File Conventions-standard anger namnen på behållare och blobnamn målsökvägen i Azure Storage för en viss utdatafilen baserat på namnen på de jobb- och.

Det är upp till dig om du vill använda den File Conventions-standarden för namngivning av datafilerna utdata. Du kan också namnge till Målbehållaren och blob men du vill. Om du använder den File Conventions-standarden för namngivning av utdatafilerna så din utdatafilerna är tillgängliga för visning i den [Azure-portalen][portal].

Att skapa Batch-lösningar med C# och .NET-utvecklare kan använda den [File Conventions-bibliotek för .NET] [ nuget_package] till spara uppgiftsdata till Azure Storage-konto, bl.a till den [batchfil Konventioner som standard](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions). File Conventions-bibliotek hanterar glidande utdatafilerna till Azure Storage och namnge mål behållare och blobbar i ett välkänt sätt.

Mer information om bestående uppgiftens utdata med File Conventions-bibliotek för .NET finns i [bevara jobb- och data till Azure Storage med Batch File Conventions-bibliotek för .NET](batch-task-output-file-conventions.md).

### <a name="implement-the-batch-file-conventions-standard"></a>Implementera Batch File Conventions-standard

Om du använder ett annat språk än .NET kan du implementera den [Batch File Conventions standard](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions) i ditt eget program.

Du kanske vill implementera File Conventions namnstandarden själv när du vill att en beprövad namngivningsschemat, eller när du vill visa uppgiftens utdata i Azure-portalen.

### <a name="implement-a-custom-file-movement-solution"></a>Implementera en lösning för flytt av anpassad fil

Du kan också implementera en egen lösning för flytt av fullständig fil. Använd detta metod, när:

- Du kan bevara uppgiftsdata till ett datalager än Azure Storage. Om du vill ladda upp filer till ett dataarkiv som Azure SQL och Azure-DataLake kan du skapa ett anpassat skript eller en körbar fil att ladda upp till den platsen. Du kan sedan anropa det på kommandoraden när du har kört ditt primära körbar fil. Du kan till exempel anropa följande två kommandon på en Windows-nod: `doMyWork.exe && uploadMyFilesToSql.exe`
- Du vill utföra kontrollpunkter eller tidig överföring av första resultaten.
- Du vill ha kvar detaljerad kontroll över felhantering. Du kanske exempelvis vill implementera en egen lösning om du vill använda beroende Uppgiftsåtgärder vidtar särskilda åtgärder för överföringen baserat på specifika slutkoder för aktiviteter. Läs mer på beroende Uppgiftsåtgärder [skapa aktivitetssamband för att köra uppgifter som är beroende av andra aktiviteter](batch-task-dependencies.md).

## <a name="design-considerations-for-persisting-output"></a>Designöverväganden för bestående utdata

När du skapar Batch-lösning bör du beakta följande faktorer rör jobb- och utdata.

- **Compute livslängd för**: Compute-noder är ofta tillfälligt, särskilt i pooler för automatisk skalning aktiverat. Utdata från en aktivitet som körs på en nod är bara tillgänglig när noden finns, och endast inom kvarhållningsperioden för filen som har angetts för aktiviteten. Om en aktivitet producerar utdata som kan behövas när aktiviteten har slutförts, uppgiften måste ladda upp sina utdatafiler till robust lagring som Azure Storage.

- **Utgående lagring**: Azure Storage rekommenderas som ett datalager för uppgiftens utdata, men du kan använda någon beständig lagring. Skriva uppgiftens utdata till Azure Storage är integrerad i API: et för Batch-tjänsten. Om du använder en annan form av beständig lagring, måste du skriva programlogiken för att bevara uppgiften utdata själv.

- **Utdata hämtning**: Du kan hämta aktivitetsutdata direkt från beräkningsnoderna i din pool eller från Azure Storage eller ett annat datalager, om du har gjorts beständiga uppgiftsutdata. Om du vill hämta en aktivitets utdata direkt från en beräkningsnod, behöver du filnamnet och dess utdata plats på noden. Om du bevara uppgiftens utdata till Azure Storage, måste den fullständiga sökvägen till filen i Azure Storage för att ladda ned utdatafiler med Azure Storage SDK.

- **Visa utdata**: När du navigerar till en Batch-aktiviteter i Azure-portalen och välj **filer på noden**, visas alla filer som är associerade med aktiviteten, inte bara utdatafilerna som du är intresserad av. Igen, filer på compute-noder är tillgängliga när noden finns och att endast inom kvarhållningstid för filer som har angetts för aktiviteten. Om du vill visa uppgiftsutdata som du har gjorts beständiga i Azure Storage, du kan använda Azure-portalen eller ett Azure Storage-klientprogram som den [Azure Storage Explorer][storage_explorer]. Om du vill visa utdata i Azure Storage med portalen eller något annat verktyg, måste du känner till filens plats och navigera till den direkt.

## <a name="next-steps"></a>Nästa steg

- Utforska med de nya funktionerna i API: et för Batch-tjänsten för att spara uppgiftsdata i [spara uppgiftsdata till Azure Storage med Batch service API](batch-task-output-files.md).
- Läs om hur du använder Batch File Conventions-bibliotek för .NET i [bevara jobb- och data till Azure Storage med Batch File Conventions-bibliotek för .NET](batch-task-output-file-conventions.md).
- Se den [PersistOutputs] [ github_persistoutputs] exempelprojektet på GitHub, som visar hur du använder både av Batch-klientbiblioteket för .NET och File Conventions-bibliotek för .NET för att bevara uppgiftens utdata till beständig lagring .

[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: http://storageexplorer.com/
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs 
