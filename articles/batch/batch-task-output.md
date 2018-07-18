---
title: Spara resultatet eller loggar från slutförda jobb och aktiviteter till ett datalager – Azure Batch | Microsoft Docs
description: Läs mer om olika alternativ för att spara utdata från Batch-aktiviteter och jobb. Du kan spara data till Azure Storage eller till ett annat datalager.
services: batch
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: 16e12d0e-958c-46c2-a6b8-7843835d830e
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 06/16/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b578abfa6fc0a10edc5daab40f8a0eea5e6653d9
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39115070"
---
# <a name="persist-job-and-task-output"></a>Bevara jobb- och uppgiftsutdata

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Några vanliga exempel på uppgiftsutdata är:

- Filer som skapas när indata för aktiviteten-processer.
- Loggfiler som associeras med körning av aktiviteten. 

Den här artikeln beskrivs olika alternativ för att bevara uppgiftsutdata och de scenarier som varje alternativ passar bäst.   

## <a name="about-the-batch-file-conventions-standard"></a>Om Batch File Conventions-standard

Batch definierar en valfri uppsättning namnkonventionerna för uppgiften utdatafilerna i Azure Storage. Den [Batch File Conventions standard](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions) beskriver dessa konventioner. File Conventions-standard anger namnen på behållare och blobnamn målsökvägen i Azure Storage för en viss utdatafilen baserat på namnen på de jobb- och.

Det är upp till dig om du vill använda den File Conventions-standarden för namngivning av datafilerna utdata. Du kan också namnge till Målbehållaren och blob men du vill. Om du använder den File Conventions-standarden för namngivning av utdatafilerna så din utdatafilerna är tillgängliga för visning i den [Azure-portalen][portal].

Det finns några olika sätt som du kan använda File Conventions-standard:

- Om du använder API: et för Batch-tjänsten för att bevara utdatafilerna, du kan välja att namnet mål behållare och blobbar enligt File Conventions-standarden. API för Batch-tjänsten kan du bevara utdatafilerna från klientkod, utan att ändra din aktivitetsprogrammet.
- Om du utvecklar med .NET, kan du använda den [Azure Batch File Conventions-bibliotek för .NET][nuget_package]. En fördel med att använda det här biblioteket är att den stöder fråga din utdatafilerna enligt deras ID eller syfte. Inbyggda förfrågningar till funktionen gör det enkelt att komma åt utdatafilerna från ett klientprogram eller från andra uppgifter. Din aktivitetsprogrammet måste dock ändras för att anropa File Conventions-bibliotek. Mer information finns i referensen för den [File Conventions-bibliotek för .NET](https://msdn.microsoft.com/library/microsoft.azure.batch.conventions.files.aspx).
- Om du utvecklar med ett annat språk än .NET, kan du implementera File Conventions-standard i ditt program.

## <a name="design-considerations-for-persisting-output"></a>Designöverväganden för bestående utdata 

När du skapar Batch-lösning bör du beakta följande faktorer rör jobb- och utdata.

* **Compute livslängd**: Compute-noder är ofta tillfälligt, särskilt i pooler för automatisk skalning aktiverat. Utdata från en aktivitet som körs på en nod är bara tillgänglig när noden finns, och endast inom kvarhållningsperioden för filen som har angetts för aktiviteten. Om en aktivitet producerar utdata som kan behövas när aktiviteten har slutförts, uppgiften måste ladda upp sina utdatafiler till robust lagring som Azure Storage.

* **Utgående lagring**: Azure Storage rekommenderas som ett datalager för uppgiftens utdata, men du kan använda någon beständig lagring. Skriva uppgiftens utdata till Azure Storage är integrerad i API: et för Batch-tjänsten. Om du använder en annan form av beständig lagring, måste du skriva programlogiken för att bevara uppgiften utdata själv.   

* **Utdata hämtning**: du kan hämta aktivitetsutdata direkt från beräkningsnoderna i din pool eller från Azure Storage eller ett annat datalager om du har gjorts beständiga uppgiftsutdata. Om du vill hämta en aktivitets utdata direkt från en beräkningsnod, behöver du filnamnet och dess utdata plats på noden. Om du bevara uppgiftens utdata till Azure Storage, måste den fullständiga sökvägen till filen i Azure Storage för att ladda ned utdatafiler med Azure Storage SDK.

* **Visa utdata**: när du navigerar till en Batch-aktiviteter i Azure-portalen och välj **filer på noden**, visas alla filer som är associerade med aktiviteten, inte bara utdatafilerna som du är intresserad av. Igen, filer på compute-noder är tillgängliga när noden finns och att endast inom kvarhållningstid för filer som har angetts för aktiviteten. Om du vill visa uppgiftsutdata som du har gjorts beständiga i Azure Storage, du kan använda Azure-portalen eller ett Azure Storage-klientprogram som den [Azure Storage Explorer][storage_explorer]. Om du vill visa utdata i Azure Storage med portalen eller något annat verktyg, måste du känner till filens plats och navigera till den direkt.

## <a name="options-for-persisting-output"></a>Alternativ för att spara utdata

Det finns några olika metoder som du kan använda för att bevara uppgiftsutdata beroende på ditt scenario:

- Använd API: et för Batch-tjänsten.  
- Använd Batch File Conventions-bibliotek för .NET.  
- Implementera Batch File Conventions-standard i ditt program.
- Implementera en lösning för flytt av anpassad fil.

I följande avsnitt beskrivs varje metod i detalj.

### <a name="use-the-batch-service-api"></a>Använda API: et för Batch-tjänsten

Med version 2017-05-01, Batch-tjänsten lägger till stöd för att ange utdatafilerna i Azure Storage för uppgiftsdata när du [lägger till ett jobb](https://docs.microsoft.com/rest/api/batchservice/add-a-task-to-a-job) eller [lägga till en samling aktiviteter i ett jobb](https://docs.microsoft.com/rest/api/batchservice/add-a-collection-of-tasks-to-a-job).

API för Batch-tjänsten stöder bestående uppgiftsinformation till ett Azure Storage-konto från pooler som skapats med konfigurationen av virtuella datorn. Med API: et för Batch-tjänsten kan du behålla uppgiftsinformation utan att ändra program som uppgiften körs. Du kan också följa den [Batch File Conventions standard](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions) för namngivning av filer som du kan spara i ett Azure Storage. 

Använd API: et för Batch-tjänsten för att spara utdata när uppgiften:

- Du kan bevara data från Batch-aktiviteterna och job manager-aktiviteter i pooler som skapats med konfigurationen av virtuella datorn.
- Du kan bevara data till en Azure Storage-behållare med ett godtyckligt namn.
- Du vill bevara data till en Azure Storage-behållare med namnet enligt den [Batch File Conventions standard](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions). 

> [!NOTE]
> API för Batch-tjänsten stöder inte bevara data från aktiviteter som körs i pooler som skapats med molntjänstkonfigurationen. Information om bestående aktivitet utdata från pooler med cloud services-konfigurationen finns i [bevara jobb- och data till Azure Storage med Batch File Conventions-bibliotek för .NET att spara ](batch-task-output-file-conventions.md)
> 
> 

Mer information om bestående uppgiftsutdata med API: et för Batch-tjänsten finns i [spara uppgiftsdata till Azure Storage med Batch service API](batch-task-output-files.md). Se även de [PersistOutputs] [ github_persistoutputs] exempelprojektet på GitHub, som visar hur du använder Batch-klientbiblioteket för .NET för att bevara uppgiftens utdata till beständig lagring.

### <a name="use-the-batch-file-conventions-library-for-net"></a>Använd Batch File Conventions-bibliotek för .NET

Att skapa Batch-lösningar med C# och .NET-utvecklare kan använda den [File Conventions-bibliotek för .NET] [ nuget_package] till spara uppgiftsdata till Azure Storage-konto, bl.a till den [batchfil Konventioner som standard](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions). File Conventions-bibliotek hanterar glidande utdatafilerna till Azure Storage och namnge mål behållare och blobbar i ett välkänt sätt.

File Conventions-bibliotek stöder förfrågningar till utdatafilerna efter ID eller syfte, vilket gör det enkelt att hitta dem utan att behöva den fullständiga filen URI: er. 

Använd Batch File Conventions-bibliotek för .NET för att spara utdata när uppgiften:

- Du vill strömdata till Azure Storage medan uppgiften körs.
- Du kan bevara data från poolen som skapats med molntjänstkonfigurationen eller konfigurationen av virtuella datorn.
- Ditt klientprogram eller andra uppgifter i jobbet behöver leta upp och hämta utdata aktivitetsfiler efter ID eller efter funktion. 
- Du vill utföra kontrollpunkter eller tidig överföring av första resultaten.
- Du vill visa uppgiftens utdata i Azure-portalen.

Mer information om bestående uppgiftens utdata med File Conventions-bibliotek för .NET finns i [bevara jobb- och data till Azure Storage med Batch File Conventions-bibliotek för .NET för att bevara ](batch-task-output-file-conventions.md). Se även de [PersistOutputs] [ github_persistoutputs] exempelprojektet på GitHub, som visar hur du använder File Conventions-bibliotek för .NET för att bevara uppgiftens utdata till beständig lagring.

Den [PersistOutputs] [ github_persistoutputs] exempelprojektet på GitHub visar hur du använder Batch-klientbiblioteket för .NET för att bevara uppgiftens utdata till beständig lagring.

### <a name="implement-the-batch-file-conventions-standard"></a>Implementera Batch File Conventions-standard

Om du använder ett annat språk än .NET kan du implementera den [Batch File Conventions standard](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions) i ditt eget program. 

Du kanske vill implementera File Conventions namnstandarden själv när du vill att en beprövad namngivningsschemat, eller när du vill visa uppgiftens utdata i Azure-portalen.

### <a name="implement-a-custom-file-movement-solution"></a>Implementera en lösning för flytt av anpassad fil

Du kan också implementera en egen lösning för flytt av fullständig fil. Använd detta metod, när:

- Du kan bevara uppgiftsdata till ett datalager än Azure Storage. Om du vill ladda upp filer till ett dataarkiv som Azure SQL och Azure-DataLake kan du skapa ett anpassat skript eller en körbar fil att ladda upp till den platsen. Du kan sedan anropa det på kommandoraden när du har kört ditt primära körbar fil. Du kan till exempel anropa följande två kommandon på en Windows-nod: `doMyWork.exe && uploadMyFilesToSql.exe`
- Du vill utföra kontrollpunkter eller tidig överföring av första resultaten.
- Du vill ha kvar detaljerad kontroll över felhantering. Du kanske exempelvis vill implementera en egen lösning om du vill använda beroende Uppgiftsåtgärder vidtar särskilda åtgärder för överföringen baserat på specifika slutkoder för aktiviteter. Läs mer på beroende Uppgiftsåtgärder [skapa aktivitetssamband för att köra uppgifter som är beroende av andra aktiviteter](batch-task-dependencies.md). 

## <a name="next-steps"></a>Nästa steg

- Utforska med de nya funktionerna i API: et för Batch-tjänsten för att spara uppgiftsdata i [spara uppgiftsdata till Azure Storage med Batch service API](batch-task-output-files.md).
- Läs om hur du använder Batch File Conventions-bibliotek för .NET i [bevara jobb- och data till Azure Storage med Batch File Conventions-bibliotek för .NET](batch-task-output-file-conventions.md).
- Se den [PersistOutputs] [ github_persistoutputs] exempelprojektet på GitHub, som visar hur du använder både av Batch-klientbiblioteket för .NET och File Conventions-bibliotek för .NET för att bevara uppgiftens utdata till beständig lagring .

[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: http://storageexplorer.com/
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs 
