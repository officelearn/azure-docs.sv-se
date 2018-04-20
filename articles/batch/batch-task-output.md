---
title: Spara resultaten eller loggar från slutförda jobb och aktiviteter i ett datalager - Azure Batch | Microsoft Docs
description: Lär dig mer om olika alternativ för bestående av utdata från batchuppgifter och jobb. Du kan spara data till Azure Storage, eller ett annat datalager.
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
ms.openlocfilehash: cb8b1ca3514e27221e95cb2def823c8f89d151e5
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2018
---
# <a name="persist-job-and-task-output"></a>Bevara jobb- och uppgiftsutdata

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Vissa vanliga exempel på utdata för aktiviteten:

- Filer som skapas när indata för aktivitet processer.
- Loggfiler som är associerade med körning av aktiviteten. 

Den här artikeln beskrivs olika alternativ för bestående uppgiftens utdata och scenarier där varje alternativ passar bäst.   

## <a name="about-the-batch-file-conventions-standard"></a>Om filen konventioner för Batch-standarden

Batch definierar en valfri uppsättning namnkonventionerna för aktiviteten utdatafilerna i Azure Storage. Den [Batch filen konventioner standard](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions) beskriver dessa regler. Filen konventioner standard anger namnen på behållaren och blob målsökvägen i Azure Storage för en given utdatafilen baserat på namnen på jobbet och aktivitet.

Det är upp till dig om du väljer att använda filen konventioner standard för att namnge datafilerna utdata. Du kan också namnge målbehållare och blob men du vill. Om du använder filen konventioner standard för namngivning av filer och utgående filer är tillgängliga för visning i den [Azure-portalen][portal].

Det finns några olika sätt som du kan använda filen konventioner standard:

- Om du använder API för Batch-tjänsten för att bevara utdatafilerna du namn på målet behållare och blobbar enligt filen konventioner standard. API för Batch-tjänsten kan du bevara utdatafilerna från klientkoden, utan att ändra tillämpningsprogrammet aktivitet.
- Om du utvecklar med .NET, kan du använda den [filen konventioner för Azure Batch-biblioteket för .NET][nuget_package]. En fördel med det här biblioteket är att den stöder fråga din utdatafilerna enligt deras ID eller syfte. Inbyggda funktioner för frågor gör det enkelt att komma åt filer från ett klientprogram eller andra uppgifter. Men måste tillämpningsprogrammet uppgiften ändras för att anropa filen konventioner bibliotek. Mer information finns i referensen för den [filen konventioner biblioteket för .NET](https://msdn.microsoft.com/library/microsoft.azure.batch.conventions.files.aspx).
- Om du utvecklar med ett annat språk än .NET måste implementera du filen konventioner standard i ditt program.

## <a name="design-considerations-for-persisting-output"></a>Designöverväganden för att spara utdata 

När du skapar Batch-lösning bör du beakta följande faktorer relaterade till projekt- och utdata.

* **Compute-nod livstid**: Compute-noder är ofta övergående, särskilt i Autoskala-aktiverade pooler. Utdata från en aktivitet som körs på en nod är bara tillgänglig när noden finns och endast inom kvarhållningsperioden för filen som har angetts för uppgiften. Om en aktivitet producerar utdata som kan behövas när uppgiften har slutförts, uppgiften måste ladda upp dess utdatafilerna till en beständig store, till exempel Azure Storage.

* **Utdata lagring**: Azure Storage rekommenderas som ett datalager för uppgiftens utdata, men du kan använda alla beständig lagring. Skrivning uppgiftens utdata till Azure Storage är integrerad i API för Batch-tjänsten. Om du använder en annan form av beständiga lagringsutrymme behöver du skriva programlogiken för att bevara uppgiften utdata själv.   

* **Utdata hämtning**: du kan hämta uppgiftens utdata direkt från beräkningsnoder i din pool eller från Azure Storage eller ett annat datalager om du har sparat uppgiftens utdata. Om du vill hämta en uppgiftens utdata direkt från en beräkningsnod behöver du filnamnet och dess platsen på noden. Om du bevara uppgiftens utdata till Azure Storage, måste den fullständiga sökvägen till filen i Azure Storage för att hämta utdatafiler med Azure Storage SDK: N.

* **Visa utdata**: när du navigerar till en Batch-aktivitet i Azure portal och välj **filer på noden**, visas alla filer som hör till aktiviteten, inte bara utgående filer du är intresserad av. Filer på datornoderna igen, är tillgängliga när noden finns och endast inom kvarhållningstiden fil som har angetts för uppgiften. Om du vill visa uppgiftens utdata som du har sparat till Azure Storage, du kan använda Azure-portalen eller ett Azure Storage client-program som de [Azure Lagringsutforskaren][storage_explorer]. Om du vill visa utdata i Azure Storage med portalen eller något annat verktyg, måste du känner till filens plats och navigera till den direkt.

## <a name="options-for-persisting-output"></a>Alternativ för att spara utdata

Det finns flera olika metoder som du kan vidta för att bevara uppgiftsutdata beroende på ditt scenario:

- Använd API för Batch-tjänsten.  
- Använd filen konventioner för Batch-biblioteket för .NET.  
- Implementera Batch filen konventioner standard i ditt program.
- Implementera en lösning för flytt av anpassade filen.

I följande avsnitt beskrivs varje metod i detalj.

### <a name="use-the-batch-service-api"></a>Använd API för Batch-tjänsten

Med versionen 2017-05-01, Batch-tjänsten lägger till stöd för att ange utdatafilerna i Azure Storage för aktivitetsdata när du [lägger till ett jobb](https://docs.microsoft.com/rest/api/batchservice/add-a-task-to-a-job) eller [lägga till en samling aktiviteter i ett jobb](https://docs.microsoft.com/rest/api/batchservice/add-a-collection-of-tasks-to-a-job).

API för Batch-tjänsten stöder bestående aktivitetsdata till ett Azure Storage-konto från poolen som skapats med konfigurationen av virtuella datorn. Med API: et för Batch-tjänsten bevara du aktivitetsdata utan att ändra de program som uppgiften körs. Du kan alternativt följer den [Batch filen konventioner standard](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions) för namngivning av filer som du bevara till Azure Storage. 

Använd API för Batch-tjänsten för att bevara utdata när uppgiften:

- Du vill bevara data från Batch-aktiviteter och jobb manager aktiviteter i pooler som har skapats med den virtuella datorkonfigurationen.
- Vill du spara data till en Azure Storage-behållare med ett godtyckligt namn.
- Vill du spara data till en Azure Storage-behållare med namnet enligt den [Batch filen konventioner standard](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions). 

> [!NOTE]
> API för Batch-tjänsten stöder inte bestående data från aktiviteter som körs i pooler som har skapats med tjänsten molnkonfigurationen. Information om bestående uppgiften utdata från pooler med cloud services-konfigurering finns [spara projekt- och data till Azure Storage med Batch filen konventioner biblioteket för .NET att bevara ](batch-task-output-file-conventions.md)
> 
> 

Mer information om bestående uppgiftens utdata med API för Batch-tjänsten finns [spara aktivitetsdata till Azure Storage med Batch-tjänsten API](batch-task-output-files.md). Se även [PersistOutputs] [github_persistoutputs] exempelprojektet på GitHub, som visar hur du använder Batch-klientbibliotek för .NET för att bevara uppgiftens utdata till beständig lagring.

### <a name="use-the-batch-file-conventions-library-for-net"></a>Använda filen konventioner för Batch-biblioteket för .NET

Skapa Batch lösningar med C# och .NET-utvecklare kan använda den [filen konventioner biblioteket för .NET] [ nuget_package] för att spara uppgiftsdata till ett Azure Storage-konto bl.a till den [Batch filen konventioner standard](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions). Filen konventioner biblioteket hanterar glidande utdatafilerna till Azure Storage och namnge mål behållare och blobbar på kända sätt.

Filen konventioner biblioteket stöder frågar utdatafilerna efter ID eller ändamål, vilket gör det enkelt att hitta dem utan att behöva filen fullständiga URI: er. 

Använd filen konventioner för Batch-biblioteket för .NET för att bevara utdata när uppgiften:

- Vill du dataströmmen data till Azure Storage medan uppgiften körs.
- Du vill bevara data från poolen som skapats med molnkonfigurationen för tjänsten eller konfigurationen av virtuella datorn.
- Klientprogrammet eller andra uppgifter i jobbet måste leta upp och hämta utdata aktivitetsfiler genom ID eller syfte. 
- Du vill utföra kontroll pekar eller tidig överföring av första resultaten.
- Du vill visa uppgiftens utdata i Azure-portalen.

Mer information om bestående uppgiftens utdata med filen konventioner-biblioteket för .NET finns [spara projekt- och data till Azure Storage med Batch filen konventioner biblioteket för .NET att bevara ](batch-task-output-file-conventions.md). Se även [PersistOutputs] [github_persistoutputs] exempelprojektet på GitHub, som visar hur du använder filen konventioner-biblioteket för .NET för att bevara uppgiftens utdata till beständig lagring.

[PersistOutputs] [github_persistoutputs] exempelprojektet på GitHub visar hur du använder Batch-klientbibliotek för .NET för att bevara uppgiftens utdata till beständig lagring.

### <a name="implement-the-batch-file-conventions-standard"></a>Implementera Batch filen konventioner standard

Om du använder ett annat språk än .NET, kan du implementera den [Batch filen konventioner standard](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions) i ditt eget program. 

Du kanske vill implementera filen konventioner namngivningsstandarden själv när du vill använda en beprövad namngivningsschemat, eller när du vill visa uppgiftens utdata i Azure-portalen.

### <a name="implement-a-custom-file-movement-solution"></a>Implementera en lösning för flytt av anpassad fil

Du kan också implementera din egen lösning för flytt av hela filen. Använd detta närmar sig när:

- Vill du spara aktivitetsdata till ett annat dataarkiv än Azure Storage. Du kan skapa ett anpassat skript eller en körbar fil att ladda upp till platsen för att ladda upp filer till ett dataarkiv som Azure SQL- eller Azure DataLake. Du kan sedan anropa den på kommandoraden när du har kört din primära körbar fil. Till exempel på en Windows-nod, kan du anropa dessa två kommandon: `doMyWork.exe && uploadMyFilesToSql.exe`
- Du vill utföra kontroll pekar eller tidig överföring av första resultaten.
- Du vill behålla granulär kontroll över felhantering. Du kanske vill implementera din egen lösning om du vill använda-beroende åtgärder för att vidta vissa överför åtgärder baserat på en viss uppgift slutkoder. Mer information om aktiviteten beroende åtgärder finns i [Skapa uppgift beroenden för att köra uppgifter som är beroende av andra aktiviteter](batch-task-dependencies.md). 

## <a name="next-steps"></a>Nästa steg

- Börja använda nya funktioner i API för Batch-tjänsten för att spara uppgiftsdata i [spara aktivitetsdata till Azure Storage med Batch-tjänsten API](batch-task-output-files.md).
- Lär dig mer om hur du använder filen konventioner för Batch-biblioteket för .NET i [spara projekt- och data till Azure Storage med Batch filen konventioner biblioteket för .NET att bevara ](batch-task-output-file-conventions.md).
- Se [PersistOutputs] [github_persistoutputs] exempelprojektet på GitHub, som visar hur du använder både Batch klientbiblioteket för .NET och filen konventioner-biblioteket för .NET för att bevara uppgiftens utdata till beständig lagring.

[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: http://storageexplorer.com/
