---
title: Skapa och använda resursfiler - Azure Batch
description: Lär dig hur du skapar batchresursfiler från olika indatakällor. Den här artikeln innehåller några vanliga metoder för hur du skapar och placerar dem på en virtuell dator.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: article
ms.date: 03/18/2020
ms.author: labrenne
ms.openlocfilehash: 0fe859ac30e7b8050d1f4688d7cf106a465e7566
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79531149"
---
# <a name="creating-and-using-resource-files"></a>Skapa och använda resursfiler

En Azure Batch-uppgift kräver ofta någon form av data för att bearbeta. Resursfiler är det sätt att tillhandahålla dessa data till din virtuella batchdator (VM) via en aktivitet. Alla typer av uppgifter stöder resursfiler: uppgifter, startuppgifter, jobbförberedelseuppgifter, jobbfrisläppningsuppgifter etc. Den här artikeln innehåller några vanliga metoder för hur du skapar resursfiler och placerar dem på en virtuell dator.  

Resursfiler placerar data på en virtuell dator i Batch, men typen av data och hur de används är flexibla. Det finns dock några vanliga användningsfall:

1. Etablera vanliga filer på varje virtuell dator med hjälp av resursfiler på en startaktivitet
1. Etablera indata som ska bearbetas av uppgifter

Vanliga filer kan till exempel vara filer på en startuppgift som används för att installera program som dina uppgifter körs. Indata kan vara råa bild- eller videodata, eller någon information som ska bearbetas av Batch.

## <a name="types-of-resource-files"></a>Typer av resursfiler

Det finns några olika alternativ för att generera resursfiler. Skapandeprocessen för resursfiler varierar beroende på var de ursprungliga data lagras.

Alternativ för att skapa en resursfil:

- [URL för lagringsbehållare:](#storage-container-url)Genererar en resursfil från en lagringsbehållare i Azure
- [Lagringsbehållarens namn](#storage-container-name): Genererar en resursfil från namnet på en behållare i ett Azure-lagringskonto som är länkat till batch
- [Webbslutpunkt](#web-endpoint): Genererar en resursfil från en giltig HTTP-URL

### <a name="storage-container-url"></a>URL för lagringsbehållare

Med hjälp av en URL för lagringsbehållare innebär du, med rätt behörighet, att komma åt filer i alla lagringsbehållare i Azure. 

I det här C#-exemplet har filerna redan överförts till en Azure-lagringsbehållare som blob-lagring. För att komma åt de data som behövs för att skapa en resursfil måste vi först få åtkomst till lagringsbehållaren.

Skapa en SAS-uri (Shared Access Signature) med rätt behörighet för åtkomst till lagringsbehållaren. Ange förfallotid och behörigheter för SAS. I det här fallet anges ingen starttid, så SAS blir giltig omedelbart och upphör att gälla två timmar efter att den har genererats.

```csharp
SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
{
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
    Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List
};
```

> [!NOTE]
> För behållaråtkomst måste `Read` du `List` ha både och behörigheter, medan `Read` du med blob-åtkomst bara behöver behörighet.

När behörigheterna har konfigurerats skapar du SAS-token och formaterar SAS-URL:en för åtkomst till lagringsbehållaren. Med den formaterade SAS-URL:en för lagringsbehållaren genererar du en resursfil med [`FromStorageContainerUrl`](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.resourcefile.fromstoragecontainerurl?view=azure-dotnet).

```csharp
CloudBlobContainer container = blobClient.GetContainerReference(containerName);

string sasToken = container.GetSharedAccessSignature(sasConstraints);
string containerSasUrl = String.Format("{0}{1}", container.Uri, sasToken);

ResourceFile inputFile = ResourceFile.FromStorageContainerUrl(containerSasUrl);
```

Ett alternativ till att generera en SAS-URL är att aktivera anonym, offentlig läsåtkomst till en behållare och dess blobbar i Azure Blob-lagring. På så sätt kan du bevilja skrivskyddad åtkomst till dessa resurser utan att dela din kontonyckel och utan att kräva en SAS. Offentlig läsåtkomst används vanligtvis för scenarier där du vill att vissa blobbar alltid ska vara tillgängliga för anonym läsåtkomst. Om det här scenariot passar din lösning läser du artikeln [Anonym åtkomst till blobbar](../storage/blobs/storage-manage-access-to-resources.md) om du vill veta mer om hur du hanterar åtkomsten till blob-data.

### <a name="storage-container-name"></a>Namn på lagringsbehållare

I stället för att konfigurera och skapa en SAS-URL kan du använda namnet på din Azure-lagringsbehållare för att komma åt dina blob-data. Lagringsbehållaren som du använder måste finnas i Azure-lagringskontot som är kopplat till ditt batchkonto. Det lagringskontot kallas autolagringskontot. Med hjälp av behållaren för automatisk lagring kan du kringgå konfigurera och skapa en SAS-URL för att komma åt en lagringsbehållare.

I det här exemplet antar vi att de data som ska användas för att skapa resursfiler redan finns i ett Azure Storage-konto som är kopplat till ditt batchkonto. Om du inte har ett konto för automatisklagring läser du stegen i [Skapa ett batchkonto](batch-account-create-portal.md) för mer information om hur du skapar och länkar ett konto.

Genom att använda ett länkat lagringskonto behöver du inte skapa och konfigurera en SAS-URL till en lagringsbehållare. Ange i stället namnet på lagringsbehållaren i ditt länkade lagringskonto.

```csharp
ResourceFile inputFile = ResourceFile.FromAutoStorageContainer(containerName);
```

### <a name="web-endpoint"></a>Slutpunkt för webben

Data som inte överförs till Azure Storage kan fortfarande användas för att skapa resursfiler. Du kan ange en giltig HTTP-URL som innehåller indata. URL:en tillhandahålls till batch-API:et och sedan används data för att skapa en resursfil.

I följande C#-exempel finns indata på en fiktiv GitHub-slutpunkt. API:et hämtar filen från den giltiga webbslutpunkten och genererar en resursfil som ska förbrukas av aktiviteten. Inga autentiseringsuppgifter behövs för det här scenariot.

```csharp
ResourceFile inputFile = ResourceFile.FromUrl("https://github.com/foo/file.txt", filePath);
```

## <a name="tips-and-suggestions"></a>Tips och förslag

Varje Azure Batch-uppgift använder filer på olika sätt, vilket är anledningen till att Batch har alternativ för att hantera filer på uppgifter. Följande scenarier är inte avsedda att vara omfattande, utan täcker i stället några vanliga situationer och ger rekommendationer.

### <a name="many-resource-files"></a>Många resursfiler

Batch-jobbet kan innehålla flera uppgifter som alla använder samma, vanliga filer. Om vanliga aktivitetsfiler delas mellan många uppgifter kan det vara ett bättre alternativ att använda ett programpaket för att innehålla filerna i stället för att använda resursfiler. Programpaket ger optimering för nedladdningshastighet. Dessutom cachelagras data i programpaket mellan aktiviteter, så om dina uppgiftsfiler inte ändras ofta kan programpaket vara en bra passform för din lösning. Med programpaket behöver du inte manuellt hantera flera resursfiler eller generera SAS-url:er för att komma åt filerna i Azure Storage. Batch fungerar i bakgrunden med Azure Storage för att lagra och distribuera programpaket för att beräkna noder.

Om varje aktivitet har många filer som är unika för den aktiviteten är resursfiler det bästa alternativet eftersom uppgifter som använder unika filer ofta behöver uppdateras eller ersättas, vilket inte är lika lätt att göra med programpaketinnehåll. Resursfiler ger ytterligare flexibilitet för att uppdatera, lägga till eller redigera enskilda filer.

### <a name="number-of-resource-files-per-task"></a>Antal resursfiler per aktivitet

Om det finns flera hundra resursfiler som anges för en aktivitet kan Batch avvisa aktiviteten som för stor. Det är bäst att hålla dina aktiviteter små genom att minimera antalet resursfiler för själva aktiviteten.

Om det inte finns något sätt att minimera antalet filer som aktiviteten behöver kan du optimera aktiviteten genom att skapa en enda resursfil som refererar till en lagringsbehållare med resursfiler. Det gör du genom att placera resursfilerna i en Azure Storage-behållare och använda de olika ["behållarmetoderna"](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.resourcefile?view=azure-dotnet#methods) för resursfiler. Använd alternativen för blob-prefix för att ange samlingar av filer som ska hämtas för dina uppgifter.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [programpaket](batch-application-packages.md) som ett alternativ till resursfiler.
- Mer information om hur du använder behållare för resursfiler finns i [Behållararbetsbelastningar](batch-docker-container-workloads.md).
- Mer information om hur du samlar in och sparar utdata från dina aktiviteter finns i [Beständiga jobb- och aktivitetsutdata](batch-task-output.md).
- Läs om tillgängliga [Batch-API:er och verktyg](batch-apis-tools.md) för att skapa Batch-lösningar.
