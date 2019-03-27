---
title: Skapa och använda resursfiler – Azure Batch | Microsoft Docs
description: Lär dig mer om att skapa Azure Batch resursfiler från olika inmatningskällor.
services: batch
author: laurenhughes
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 03/14/2019
ms.author: lahugh
ms.openlocfilehash: f6693de861658c7174614e80906ee83633fcb313
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2019
ms.locfileid: "58501098"
---
# <a name="creating-and-using-resource-files"></a>Skapa och använda resursfiler

Ett Azure Batch-aktiviteter kräver ofta någon form av data att bearbeta. Resursfiler är kan du ange dessa data till ditt Batch-dator (VM) via en uppgift. Resursfiler stöd för alla typer av aktiviteter: aktiviteter, starta aktiviteter, jobbförberedelse, jobbpubliceringsaktiviteter osv. Den här artikeln beskriver några vanliga metoder för hur du skapar resursfiler och placerar dem på en virtuell dator.  

Resursfiler är en mekanism för att installera data på en virtuell dator i Batch, men typen av data och hur de används är flexibel. Det finns dock några vanliga användningsområden:

1. Etablera gemensamma filer på varje virtuell dator med hjälp av resursfiler på en startaktivitet
1. Etablera indata som ska bearbetas av aktiviteter

Delade filer kan till exempel vara filer på en startaktivitet som används för att installera program som dina aktiviteter kör. Indata kan vara raw-bild eller video data eller all information som ska bearbetas av Batch.

## <a name="types-of-resource-files"></a>Typer av resursfiler

Det finns några olika alternativ att generera resursfiler. Skapandeprocessen för resursfiler varierar beroende på var den ursprungliga informationen lagras.

Alternativ för att skapa en resursfil:

- [Storage-behållarens Webbadress](#storage-container-url): Genererar en resursfil från alla storage-behållare i Azure
- [Namnet på lagringsbehållaren](#storage-container-name): Genererar en resursfil från namnet på en behållare i ett Azure storage-konto som är länkad till Batch
- [Webbslutpunkt](#web-endpoint): Genererar en resursfil från valfri giltig HTTP-URL

### <a name="storage-container-url"></a>Storage-behållarens Webbadress

Med hjälp av en storage-behållarens Webbadress innebär att du kan komma åt filer i alla storage-behållare i Azure. Med rätt behörighet

I det här C# exempelvis filerna har redan överförts till en Azure storage-behållare som blobblagring. Om du vill komma åt data som behövs för att skapa en resursfil, måste vi först få åtkomst till behållaren.

Skapa en signatur för delad åtkomst (SAS) URI med rätt behörighet till behållaren. Ange förfallotid och behörigheter för SAS. I det här fallet har inga starttid angetts, så att signaturen för delad åtkomst börjar gälla omedelbart och upphör att gälla två timmar efter att den skapats.

```csharp
SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
{
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
    Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List
};
```

> [!NOTE]
> För åtkomst till behållare, måste du ha både `Read` och `List` behörigheter, medan med blobåtkomst, behöver du bara `Read` behörighet.

När behörigheterna som har konfigurerats, skapa SAS-token och formatera SAS-URL för åtkomst till behållaren. Med hjälp av formaterade SAS-Webbadressen för storage-behållare Generera en resursfil med [ `FromStorageContainerUrl` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.resourcefile.fromstoragecontainerurl?view=azure-dotnet).

```csharp
CloudBlobContainer container = blobClient.GetContainerReference(containerName);

string sasToken = container.GetSharedAccessSignature(sasConstraints);
string containerSasUrl = String.Format("{0}{1}", container.Uri, sasToken);

ResourceFile inputFile = ResourceFile.FromStorageContainerUrl(containerSasUrl);
```

Ett alternativ till att generera en SAS-URL är att aktivera anonym, offentlig läsbehörighet till en behållare och dess blobbar i Azure Blob storage. Då kan ge du skrivskyddad åtkomst till dessa resurser utan att dela din kontonyckel och utan en SAS. Offentlig läsbehörighet används vanligtvis för scenarier där du vill att vissa BLOB-och alltid vara tillgänglig för anonym läsbehörighet. Om det här scenariot passar din lösning, visas den [anonym åtkomst till blobbar](../storage/blobs/storage-manage-access-to-resources.md) du lär dig mer om hur du hanterar åtkomst till blob-data.

### <a name="storage-container-name"></a>Namnet på lagringsbehållaren

Du kan använda namnet på ditt Azure storage-behållare att få åtkomst till blob-data i stället för att konfigurera och skapa en SAS-URL. Lagringsbehållaren används måste i Azure storage-kontot som är kopplad till ditt Batch-konto, kallas autostorage-konto. Med hjälp av namnet på lagringsbehållaren för ett autostorage-konto kan du kringgå konfigurerar och skapar en SAS-URL för att komma åt en lagringsbehållare.

I det här exemplet förutsätter vi att data som ska användas för att skapa en resurs filen redan är i ett Azure Storage-konto som är kopplat till ditt Batch-konto. Om du inte har ett autostorage-konto, hittar du i [skapa ett Batch-konto](/create-a-batch-account.md) mer information om hur du skapar och länkar ett konto.

Genom att använda ett länkat storage-konto kan behöver du inte skapa och konfigurera en SAS-URL till en lagringsbehållare. I stället ange namnet på storage-behållare i länkade storage-konto.

```csharp
ResourceFile inputFile = ResourceFile.FromAutoStorageContainer(containerName);
```

### <a name="web-endpoint"></a>Web-slutpunkt

Data som inte har överförts till Azure Storage kan användas för att skapa resursfiler. Du kan ange en giltig HTTP-URL som innehåller dina indata. URL: en har angetts för Batch-API: et och sedan data används för att skapa en resursfil.

I följande C# exempelvis indata finns på fiktiva GitHub-slutpunkten. API: et hämtar filen från giltig webbslutpunkt och genererar en resursfil för att användas av din uppgift. Inga autentiseringsuppgifter krävs för det här scenariot.

```csharp
ResourceFile inputFile = ResourceFile.FromUrl("https://github.com/foo/file.txt", filePath);
```

## <a name="tips-and-suggestions"></a>Användbara tips och råd

Varje aktivitet i Azure Batch använder filer på olika sätt, vilket är anledningen till Batch har alternativ för att hantera filer på uppgifter. Följande scenarier är inte avsedda att vara omfattande, men i stället beskriver några vanliga situationer och ge rekommendationer.

### <a name="many-resource-files"></a>Många resursfiler

Batch-jobb kan innehålla flera uppgifter som alla använder samma, vanliga filer. Om vanliga uppgift filer som delas mellan många aktiviteter vara med hjälp av ett programpaket som innehåller filer istället för att använda resursfiler ett bättre alternativ. Programpaket underlättar optimering för nedladdningshastighet. Dessutom data i programpaket cachelagras mellan aktiviteter, så om din aktivitetsfiler inte ändras ofta, programpaket kan vara ett bra alternativ för din lösning. Med programpaket behöver du inte manuellt hantera flera resursfiler eller generera SAS URL: er för att komma åt filer i Azure Storage. Batch fungerar i bakgrunden med Azure Storage för att lagra och distribuera programpaket till beräkningsnoder.

Om varje aktivitet har många filer som är unika för aktiviteten, är resursfiler mest sannolikt det bästa alternativet. Uppgifter som använder unika filer ofta behöver uppdateras eller ersätts, som inte är lika enkelt att göra med paket programinnehåll. Resursfiler ger ytterligare flexibilitet för att uppdatera, att lägga till eller redigera enskilda filer.

### <a name="number-of-resource-files-per-task"></a>Antal resursfiler per aktivitet

Om det finns flera hundra resursfiler som anges på en aktivitet, kan Batch avvisa aktiviteten för att den var för stor. Det är bäst att hålla dina aktiviteter små genom att minimera antalet resursfiler på själva aktiviteten.

Om det finns inget sätt att minimera antalet filer som uppgiften behov, du kan optimera uppgiften genom att skapa en enskild resurs-fil som refererar till en lagringsbehållare av resursfiler. Om du vill göra detta måste placera din resursfiler i en Azure Storage-behållare och Använd ”behållare” olika lägen för resursfiler. Använd blob-prefix alternativ för att ange samlingar av filer som ska hämtas för dina aktiviteter.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [programpaket](batch-application-packages.md) som ett alternativ till resursfiler.
- Läs mer om hur du använder behållare för resursfiler [behållararbetsbelastningar](batch-docker-container-workloads.md).
- Om du vill lära dig mer om att samla in och spara utdata från dina aktiviteter, se [bevara jobb- och uppgiftsutdata](batch-task-output.md).
- Läs om tillgängliga [Batch-API:er och verktyg](batch-apis-tools.md) för att skapa Batch-lösningar.