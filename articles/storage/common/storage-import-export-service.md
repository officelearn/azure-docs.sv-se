---
title: Använda Azure Import/Export för att överföra data till och från Azure Storage | Microsoft-dokument
description: Lär dig hur du skapar import- och exportjobb i Azure-portalen för överföring av data till och från Azure Storage.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 03/15/2020
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: eee0fc2797fbe0666a6b848fde574c7807f47cc9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282451"
---
# <a name="what-is-azure-importexport-service"></a>Vad är Azure Import/Export-tjänst?

Azure Import/Export-tjänsten används för att på ett säkert sätt importera stora mängder data till Azure Blob-lagring och Azure-filer genom att skicka diskenheter till ett Azure-datacenter. Den här tjänsten kan också användas för att överföra data från Azure Blob-lagring till hårddiskar och leverera till dina lokala platser. Data från en eller flera diskenheter kan importeras antingen till Azure Blob storage eller Azure Files.

Ange egna hårddiskar och överför data med Azure Import/Export-tjänsten. Du kan också använda hårddiskar som tillhandahålls av Microsoft.

Om du vill överföra data med hjälp av diskenheter som tillhandahålls av Microsoft kan du använda [Azure Data Box Disk](../../databox/data-box-disk-overview.md) för att importera data till Azure. Microsoft levererar upp till 5 krypterade SSD-enheter (Solid State-diskenheter) med en total kapacitet på 40 TB per beställning till ditt datacenter via en regional operatör. Du kan snabbt konfigurera hårddiskar, kopiera data till hårddiskar via en USB 3.0-anslutning och skicka tillbaka diskenheterna till Azure. Mer information finns i [översikt över Azure Data Box Disk](../../databox/data-box-disk-overview.md).

## <a name="azure-importexport-use-cases"></a>Användningsfall för Azure Import/Export

Överväg att använda Azure Import/Export-tjänsten när det går för långsamt att ladda upp eller hämta data över nätverket, eller att få ytterligare nätverksbandbredd är kostnadsöverkomligt. Använd den här tjänsten i följande scenarier:

* **Datamigrering till molnet**: Flytta stora mängder data till Azure snabbt och kostnadseffektivt.
* **Innehållsdistribution:** Skicka snabbt data till dina kundwebbplatser.
* **Säkerhetskopiering**: Ta säkerhetskopior av dina lokala data för att lagra i Azure Storage.
* **Dataåterställning**: Återställ en stor mängd data som lagras i lagring och få dem levererade till din lokala plats.

## <a name="importexport-components"></a>Importera/exportera komponenter

Tjänsten Import/Export använder följande komponenter:

* **Import/exporttjänst:** Den här tjänsten som är tillgänglig i Azure-portalen hjälper användaren att skapa och spåra dataimport (ladda upp) och exportera (hämta) jobb.  

* **WAImportExport-verktyg:** Det här är ett kommandoradsverktyg som gör följande:
  * Förbereder de diskenheter som levereras för import.
  * Gör det lättare att kopiera dina data till enheten.
  * Krypterar data på enheten med AES 128-bitars BitLocker. Du kan använda ett externt nyckelskydd för att skydda BitLocker-tangenten.
  * Genererar de enhetsjournalfiler som används när importen skapades.
  * Hjälper till att identifiera antalet enheter som behövs för exportjobb.

> [!NOTE]
> Verktyget WAImportExport finns i två versioner, version 1 och 2. Vi rekommenderar att du använder:
>
> * Version 1 för import/export till Azure Blob-lagring.
> * Version 2 för import av data till Azure-filer.
>
> WAImportExport-verktyget är endast kompatibelt med 64-bitars Windows-operativsystem. För specifika OS-versioner som stöds går du till [Azure Import/Export-kraven](storage-import-export-requirements.md#supported-operating-systems).

* **Diskenheter:** Du kan leverera SSD-enheter (Solid State Drives) eller hårddiskar (HDDs) till Azure-datacentret. När du skapar ett importjobb skickar du diskenheter som innehåller dina data. När du skapar ett exportjobb skickar du tomma enheter till Azure-datacentret. För specifika disktyper går du till [Disktyper som stöds](storage-import-export-requirements.md#supported-hardware).

## <a name="how-does-importexport-work"></a>Hur fungerar Import/Export?

Azure Import/Export-tjänsten tillåter dataöverföring till Azure Blobbar och Azure-filer genom att skapa jobb. Använd AZURE-portalen eller AZURE Resource Manager REST API för att skapa jobb. Varje jobb är associerat med ett enda lagringskonto.

Jobben kan vara import- eller exportjobb. Med ett importjobb kan du importera data till Azure Blobbar eller Azure-filer, medan exportjobbet gör att data kan exporteras från Azure Blobbar. För ett importjobb skickar du enheter som innehåller dina data. När du skapar ett exportjobb skickar du tomma enheter till ett Azure-datacenter. I varje fall kan du skicka upp till 10 hårddiskar per jobb.

### <a name="inside-an-import-job"></a>Inuti ett importjobb

På en hög nivå innebär ett importjobb följande steg:

1. Bestäm data som ska importeras, antal enheter du behöver, målblobplats för dina data i Azure-lagring.
2. Använd verktyget WAImportExport för att kopiera data till diskenheter. Kryptera diskenheterna med BitLocker.
3. Skapa ett importjobb i ditt mållagringskonto i Azure-portalen. Ladda upp enhetsjournalfilerna.
4. Ange avsändaradress och operatörskontonummer för att skicka tillbaka enheterna till dig.
5. Skicka diskenheterna till den leveransadress som anges när du skapar arbetstillfällen.
6. Uppdatera leveransspårningsnumret i importjobbinformationen och skicka importjobbet.
7. Enheterna tas emot och bearbetas i Azure-datacentret.
8. Enheterna levereras med ditt operatörskonto till den avsändaradress som anges i importjobbet.

> [!NOTE]
> För lokala (inom datacenterland/region) sändningar, vänligen dela ett inhemskt operatörskonto.
>
> För sändningar för länder/regioner utanför datacenter (utanför datacenterland/region) ska du dela ett internationellt transportföretagskonto.

 ![Bild 1:Importera jobbflöde](./media/storage-import-export-service/importjob.png)

Stegvisa instruktioner om dataimport finns i:

* [Importera data till Azure Blobbar](storage-import-export-data-to-blobs.md)
* [Importera data till Azure-filer](storage-import-export-data-to-files.md)

### <a name="inside-an-export-job"></a>Inuti ett exportjobb

> [!IMPORTANT]
> Tjänsten stöder endast export av Azure Blobbar. Export av Azure-filer stöds inte.

På en hög nivå innebär ett exportjobb följande steg:

1. Bestäm vilka data som ska exporteras, antalet enheter du behöver, källblobar eller behållarsökvägar för dina data i Blob-lagring.
2. Skapa ett exportjobb i ditt källlagringskonto i Azure-portalen.
3. Ange källblobar eller behållarsökvägar för de data som ska exporteras.
4. Ange avsändaradress och operatörskontonummer för att skicka tillbaka enheterna till dig.
5. Skicka diskenheterna till den leveransadress som anges när du skapar arbetstillfällen.
6. Uppdatera leveransspårningsnumret i exportjobbinformationen och skicka exportjobbet.
7. Enheterna tas emot och bearbetas i Azure-datacentret.
8. Enheterna krypteras med BitLocker och nycklarna är tillgängliga via Azure-portalen.  
9. Enheterna levereras med ditt operatörskonto till den avsändaradress som anges i importjobbet.

> [!NOTE]
> För lokala (inom datacenterland/region) sändningar, vänligen dela ett inhemskt operatörskonto.
>
> För sändningar för länder/regioner utanför datacenter (utanför datacenterland/region) ska du dela ett internationellt transportföretagskonto.
  
 ![Bild 2:Exportera jobbflöde](./media/storage-import-export-service/exportjob.png)

Stegvisa instruktioner om dataexport finns i [Exportera data från Azure Blobbar](storage-import-export-data-from-blobs.md).

## <a name="region-availability"></a>Regional tillgänglighet

Azure Import/Export-tjänsten stöder kopiering av data till och från alla Azure-lagringskonton. Du kan skicka hårddiskar till en av de angivna platserna. Om ditt lagringskonto finns på en Azure-plats som inte anges här, tillhandahålls en alternativ leveransplats när du skapar jobbet.

### <a name="supported-shipping-locations"></a>Sändningsplatser som stöds

|Land/region  |Land/region  |Land/region  |Land/region  |
|---------|---------|---------|---------|
|USA, östra    | Europa, norra        | Indien, centrala        |USA Gov, Iowa         |
|USA, västra     |Europa, västra         | Indien, södra        | USA DoD, östra        |
|USA, östra 2    | Asien, östra        |  Indien, västra        | USA DoD, centrala        |
|USA, västra 2     | Sydostasien        | Kanada, centrala        | Kina, östra         |
|USA, centrala     | Australien, östra        | Kanada, östra        | Kina, norra        |
|USA, norra centrala     |  Australien, sydöstra       | Brasilien, södra        | Storbritannien, södra        |
|USA, södra centrala     | Japan, västra        |Sydkorea, centrala         | Tyskland, centrala        |
|USA, västra centrala     |  Japan, östra       | US Gov, Virginia        | Tyskland, nordöstra        |

## <a name="security-considerations"></a>Säkerhetsöverväganden

Data på enheten krypteras med AES 128-bitars BitLocker-diskkryptering. Den här krypteringen skyddar dina data när den är under överföring.

För importjobb krypteras enheter på två sätt.  

* Ange alternativet när du använder *filen dataset.csv* när du kör verktyget WAImportExport under utarbetandet av enheten.

* Aktivera BitLocker-kryptering manuellt på enheten. Ange krypteringsnyckeln i *driveset.csv* när du kör VERKTYGSKOMMANDORADEN WAImportExport under förberedelse av enheten. BitLocker-krypteringsnyckeln kan skyddas ytterligare med hjälp av ett externt nyckelskydd (kallas även Microsoft-hanterad nyckel) eller en kundhanterad nyckel. Mer information finns i [hur du använder en kundhanterad nyckel för att skydda bitlockernyckeln](storage-import-export-encryption-key-portal.md).

För exportjobb krypterar tjänsten enheten när du har kopierat dem till enheterna innan den skickas tillbaka till dig. Krypteringsnyckeln tillhandahålls till dig via Azure-portalen. Enheten måste låsas upp med hjälp av verktyget WAImporExport med hjälp av nyckeln.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]

### <a name="pricing"></a>Prissättning

**Avgift för drivhantering**

Det finns en enhetshanteringsavgift för varje enhet som bearbetas som en del av ditt import- eller exportjobb. Se information om [Azure Import/Export Pricing](https://azure.microsoft.com/pricing/details/storage-import-export/).

**Fraktkostnader**

När du skickar enheter till Azure betalar du fraktkostnaden till transportföretaget. När Microsoft returnerar enheterna till dig debiteras fraktkostnaden till det operatörskonto som du angav när du skapades för att skapa arbetstillfällen.

**Transaktionskostnader**

[Standardavgift för lagringstransaktioner](https://azure.microsoft.com/pricing/details/storage/) gäller vid import samt export av data. Standardutgående avgifter gäller också tillsammans med lagringstransaktionsavgifter när data exporteras från Azure Storage. Mer information om utgående kostnader finns i [Priser för dataöverföring.](https://azure.microsoft.com/pricing/details/data-transfers/)

## <a name="next-steps"></a>Nästa steg

Lär dig hur du använder tjänsten Importera/exportera till:

* [Importera data till Azure Blobbar](storage-import-export-data-to-blobs.md)
* [Exportera data från Azure Blobbar](storage-import-export-data-from-blobs.md)
* [Importera data till Azure-filer](storage-import-export-data-to-files.md)
