---
title: Beständiga jobb- och uppgiftsutdata i ett datalager - Azure Batch
description: Lär dig mer om olika alternativ för beständiga utdata från Batch-aktiviteter och jobb. Du kan spara data till Azure Storage eller till ett annat datalager.
services: batch
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 16e12d0e-958c-46c2-a6b8-7843835d830e
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 11/14/2018
ms.author: labrenne
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0809a838f1d34491eb4e276ce356eded9b98756e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022842"
---
# <a name="persist-job-and-task-output"></a>Bevara jobb- och uppgiftsutdata

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Några vanliga exempel på uppgiftsutdata är:

- Filer som skapas när uppgiften bearbetar indata.
- Loggfiler som är associerade med uppgiftskörning.

I den här artikeln beskrivs olika alternativ för beständiga aktivitetsutdata.

## <a name="options-for-persisting-output"></a>Alternativ för beständig utdata

Beroende på ditt scenario finns det några olika metoder som du kan använda för att bevara aktivitetsutdata:

- [Använd Batch-tjänst API](batch-task-output-files.md).  
- [Använd batchfilskonventioner för .NET](batch-task-output-file-conventions.md).  
- Implementera standarden batchfilkonventioner i ditt program.
- Implementera en anpassad filrörelselösning.

I följande avsnitt beskrivs kortfattat varje metod samt allmänna designöverväganden för kvarstående utdata.

### <a name="use-the-batch-service-api"></a>Använda BATCH-tjänst-API:et

Batch-tjänsten stöder att ange utdatafiler i Azure Storage för uppgiftsdata när du [lägger till en aktivitet i ett jobb](https://docs.microsoft.com/rest/api/batchservice/add-a-task-to-a-job) eller [lägger till en samling uppgifter i ett jobb](https://docs.microsoft.com/rest/api/batchservice/add-a-collection-of-tasks-to-a-job).

Mer information om beständiga aktivitetsutdata med batch-tjänst-API:et finns [i Beständiga uppgiftsdata till Azure Storage med batch-tjänst-API:et](batch-task-output-files.md).

### <a name="use-the-batch-file-conventions-library-for-net"></a>Använda batchfilskonventioner för .NET

Batch definierar en valfri uppsättning konventioner för att namnge uppgiftsutdatafiler i Azure Storage. Standarden [Batch File Conventions](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files#conventions) beskriver dessa konventioner. Standarden Filkonventioner bestämmer namnen på målbehållaren och blob-sökvägen i Azure Storage för en viss utdatafil baserat på namnen på jobbet och uppgiften.

Det är upp till dig om du väljer att använda filkonventioner standard för att namnge dina utdatafiler. Du kan också namnge målbehållaren och bloben hur du vill. Om du använder filkonventioner standard för namngivning av utdatafiler, då dina utdatafiler är tillgängliga för visning i [Azure-portalen][portal].

Utvecklare som bygger batch-lösningar med C# och .NET kan använda [filkonventioner för .NET för][nuget_package] att spara uppgiftsdata till ett Azure Storage-konto, enligt [standarden Batch File Conventions](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files#conventions). Filkonventioner biblioteket hanterar flytta utdatafiler till Azure Storage och namnge målbehållare och blobbar på ett välkänt sätt.

Mer information om beständiga uppgiftsutdata med filkonventionsbiblioteket för .NET finns [i Beständiga jobb- och uppgiftsdata till Azure Storage med batchfilskonventioner för .NET](batch-task-output-file-conventions.md).

### <a name="implement-the-batch-file-conventions-standard"></a>Implementera standarden batchfilkonventioner

Om du använder ett annat språk än .NET kan du implementera [standarden batchfilkonventioner](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files#conventions) i ditt eget program.

Du kanske vill implementera filkonventionerna som namnger standard själv när du vill ha ett beprövat namngivningsschema eller när du vill visa aktivitetsutdata i Azure-portalen.

### <a name="implement-a-custom-file-movement-solution"></a>Implementera en anpassad filrörelselösning

Du kan också implementera din egen kompletta filrörelselösning. Använd den här metoden när:

- Du vill spara uppgiftsdata i ett annat datalager än Azure Storage. Om du vill överföra filer till ett datalager som Azure SQL eller Azure DataLake kan du skapa ett anpassat skript eller körbart att överföra till den platsen. Du kan sedan anropa den på kommandoraden efter att ha kört din primära körbara fil. På en Windows-nod kan du till exempel anropa följande två kommandon:`doMyWork.exe && uploadMyFilesToSql.exe`
- Du vill utföra kontrollpunkt eller tidig uppladdning av de första resultaten.
- Du vill behålla detaljerad kontroll över felhantering. Du kanske till exempel vill implementera en egen lösning om du vill använda aktivitetssambandsåtgärder för att vidta vissa överföringsåtgärder baserat på specifika uppgiftsutträdeskoder. Mer information om aktivitetssambandsåtgärder finns i [Skapa aktivitetssamband för att köra aktiviteter som är beroende av andra aktiviteter](batch-task-dependencies.md).

## <a name="design-considerations-for-persisting-output"></a>Designöverväganden för kvarstående utdata

När du utformar batch-lösningen bör du tänka på följande faktorer som är relaterade till jobb- och aktivitetsutdata.

- **Beräkningsnodens livstid**: Beräkningsnoder är ofta tillfälliga, särskilt i pooler som är aktiverade automatiskt. Utdata från en aktivitet som körs på en nod är endast tillgängligt medan noden finns och endast inom den filkvarhållningsperiod som du har angett för aktiviteten. Om en uppgift ger utdata som kan behövas när uppgiften har slutförts måste uppgiften överföra sina utdatafiler till ett varaktigt arkiv som Azure Storage.

- **Utdatalagring:** Azure Storage rekommenderas som ett datalager för aktivitetsutdata, men du kan använda all varaktig lagring. Att skriva uppgiftsutdata till Azure Storage är integrerat i batch-tjänst-API:et. Om du använder en annan form av varaktig lagring måste du skriva programlogiken för att själv spara aktivitetsutdata.

- **Utdatahämtning:** Du kan hämta aktivitetsutdata direkt från beräkningsnoderna i poolen, eller från Azure Storage eller ett annat datalager om du har sparat aktivitetsutdata. Om du vill hämta en aktivitets utdata direkt från en beräkningsnod behöver du filnamnet och dess utdataplats på noden. Om du beständig uppgiftsutdata till Azure Storage behöver du den fullständiga sökvägen till filen i Azure Storage för att hämta utdatafilerna med Azure Storage SDK.

- **Visa utdata:** När du navigerar till en batch-uppgift i Azure-portalen och väljer **Filer på nod**visas alla filer som är associerade med uppgiften, inte bara de utdatafiler som du är intresserad av. Återigen är filer på beräkningsnoder endast tillgängliga medan noden finns och endast inom den filkvarhållningstid som du har angett för aktiviteten. Om du vill visa uppgiftsutdata som du har sparat i Azure Storage kan du använda Azure-portalen eller ett Azure Storage-klientprogram som [Azure Storage Explorer][storage_explorer]. Om du vill visa utdata i Azure Storage med portalen eller ett annat verktyg måste du känna till filens plats och navigera till den direkt.

## <a name="next-steps"></a>Nästa steg

- Utforska med hjälp av de nya funktionerna i batch-tjänst-API:et för att bevara uppgiftsdata i [Beständiga uppgiftsdata till Azure Storage med batch-tjänst-API:et](batch-task-output-files.md).
- Lär dig mer om hur du använder batchfilskonventioner för .NET i [Beständiga jobb- och uppgiftsdata till Azure Storage med batchfilkonventioner för .NET](batch-task-output-file-conventions.md).
- Se exempelprojektet [PersistOutputs][github_persistoutputs] på GitHub, som visar hur du använder både batchklientbiblioteket för .NET och filkonventionsbiblioteket för .NET för att spara aktivitetsutdata till varaktig lagring.

[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: https://storageexplorer.com/
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs 
