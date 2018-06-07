---
title: Använda Azure Import/Export för att överföra data till och från Azure Storage | Microsoft Docs
description: Lär dig hur du skapar importera och exportera jobben i Azure-portalen för att överföra data till och från Azure Storage.
author: alkohli
manager: jeconnoc
services: storage
ms.service: storage
ms.topic: article
ms.date: 05/17/2018
ms.author: alkohli
ms.openlocfilehash: 83ba437e699eb150e86e6c89e478377394966419
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2018
ms.locfileid: "34809550"
---
# <a name="what-is-azure-importexport-service"></a>Vad är Azure Import/Export service?

Azure Import/Export-tjänsten används för att importera stora mängder data på ett säkert sätt till Azure Blob storage och Azure-filer via diskenheter till ett Azure-datacenter. Den här tjänsten kan också användas för att överföra data från Azure Blob storage diskenheter och leverera till lokala platser. Du kan importera data från en eller flera diskar till Azure Blob storage eller Azure-filer. 

## <a name="azure-importexport-usecases"></a>Azure Import/Export usecases

Överväg att använda Azure Import/Export service när överföra eller hämta data via nätverket är långsamt eller att få ytterligare nätverksbandbredden är mycket höga kostnaden. Använd den här tjänsten i följande scenarier:

* **Migrering av data till molnet**: flytta stora mängder data till Azure snabbt och kostnadseffektivt sätt.
* **Innehållsdistribution**: snabbt skicka data till customer-platser.
* **Säkerhetskopiering**: göra säkerhetskopior av dina lokala data ska lagras i Azure Storage.
* **Dataåterställning**: återställa stora mängder data som lagras i lagring och dem till din lokala plats.

## <a name="importexport-components"></a>Importera och exportera komponenter

Import/Export-tjänsten använder följande komponenter:

- **Importera och exportera**tjänsten: tjänsten tillgänglig i Azure-portalen hjälper användaren att skapa och spåra importera och exportera jobben.  

- **WAImportExport verktyget**: Detta är ett kommandoradsverktyg som gör följande: 
    - Förbereder dina enheter som levereras för import.
    - Underlättar kopiera dina data från enheten.
    - Krypterar data på enheten med BitLocker.
    - Genererar enhet journal-filer som används under importen skapas.
    - Hjälper dig att identifiera antal enheter som behövs för att exportera jobben.

    Det här verktyget finns i två versioner, version 1 och 2. Vi rekommenderar att du använder:

    - Version 1 för import och export till Azure Blob storage. 
    - Version 2 för att importera data till Azure files.

    Verktyget WAImportExport är endast kompatibel med 64-bitars Windows-operativsystem. För specifika OS-versioner som stöds, gå till [krav för Azure Import/Export](storage-import-export-requirements.md#supported-operating-systems).

- **Diskar**: du kan leverera SSD-enheter (SSD) eller -hårddiskar (HDD) till Azure-datacenter. När du skapar ett importjobb kan leverera du diskenheter som innehåller dina data. När du skapar ett exportjobb kan leverera du tomma enheter till Azure-datacenter. För specifika disktyper, gå till [stöds disktyper](storage-import-export-requirements.md#supported-hardware).

## <a name="how-does-importexport-work"></a>Hur fungerar Import/Export

Azure Import/Export-tjänsten kan överföra data till Azure-BLOB och Azure-filer genom att skapa jobb. Använd Azure-portalen eller Azure Resource Manager REST API för att skapa jobb. Varje jobb är associerat med ett enda storage-konto. 

Jobb kan importera eller exportera jobben. Importen kan du importera data till Azure-Blobbar eller Azure files medan exportjobbet kan exporteras från Azure BLOB. För ett importjobb leverera du enheter som innehåller dina data. När du skapar ett exportjobb kan leverera du tomma enheter till ett Azure-datacenter. I båda fallen kan leverera du upp till 10 enheter per jobb.

> [!IMPORTANT]
> Exportera data till Azure-filer stöds inte.

Hög nivå steg som ingår i import och exportjobb beskrivs i det här avsnittet. 


### <a name="inside-an-import-job"></a>I ett importjobb

Ett importjobb omfattar följande steg på en hög nivå:

1. De data som ska importeras, antalet enheter som du behöver, blob-målplatsen för dina data i Azure-lagring.
2. Verktyget WAImportExport används för att kopiera data till enheter. Kryptera diskar med BitLocker.
3. Skapa ett importjobb i mål-lagringskontot i Azure-portalen. Överför journalfiler enhet.
2. Ange avsändaradressen och operatör kontonummer för leverans av enheter till dig.
3. Leverera diskenheter till leveransadress tillhandahålls när jobbet skapas.
4. Uppdatera leveransen spårnings-ID i Jobbinformationen import och skicka importjobbet.
5. Enheter mottas och bearbetas på Azure-datacentret.
6. Enheter levereras med ditt konto operatör avsändaradressen i importjobbet.
  
    ![Bild 1:Import jobbet flöde](./media/storage-import-export-service/importjob.png)

Importera, gå till steg för steg-anvisningar för data:

- [Importera data till Azure-BLOB](storage-import-export-data-to-blobs.md)
- [Importera data till Azure-filer](storage-import-export-data-to-files.md)


### <a name="inside-an-export-job"></a>I ett exportjobb

> [!IMPORTANT]
> Tjänsten stöder bara export av Azure-BLOB. Export av Azure-filer stöds inte.

Ett exportjobb omfattar följande steg på en hög nivå:

1. De data som exporteras, antalet enheter du behöver, källa blobbar eller behållaren sökvägar för dina data i Blob storage.
3. Skapa ett exportjobb i ditt källa storage-konto i Azure-portalen.
4. Ange källa blobbar eller behållaren sökvägar för data som ska exporteras.
5. Ange avsändaradressen och operatör kontonummer för leverans av enheter till dig.
6. Leverera diskenheter till leveransadress tillhandahålls när jobbet skapas.
7. Uppdatera leveransen spårnings-ID i Jobbinformationen export och skicka exportjobbet.
8. Enheterna tas emot och bearbetas på Azure-datacentret.
9. Enheterna som är krypterad med BitLocker och nycklarna är tillgängliga via Azure portal.  
10. Enheterna som levereras med ditt konto operatör avsändaradressen i importjobbet.
  
    ![Bild 2:Export jobbet flöde](./media/storage-import-export-service/exportjob.png)

Stegvisa anvisningar för export av data, gå till [exportera data från Azure BLOB](storage-import-export-data-from-blobs.md).

## <a name="region-availability"></a>Regional tillgänglighet 

Tjänsten Azure Import/Export stöder kopiering av data till och från alla Azure storage-konton. Du kan leverera diskenheter till någon av de angivna platserna. Om ditt lagringskonto finns i en Azure-plats som inte anges här, tillhandahålls en alternativ leveransplats när du skapar jobbet.

### <a name="supported-shipping-locations"></a>Leverans platser som stöds


|Land/region  |Land/region  |Land/region  |Land/region  |
|---------|---------|---------|---------|
|Östra USA    | Norra Europa        | Indien, centrala        |Iowa (USA-förvaltad region)         |
|Västra USA     |Västra Europa         | Södra Indien        | US DoD, östra        |
|Östra USA 2    | Östasien        |  Indien, västra        | US DoD, centrala        |
|Västra USA 2     | Sydostasien        | Centrala Kanada        | Östra Kina         |
|Centrala USA     | Östra Australien        | Östra Kanada        | Norra Kina        |
|Norra centrala USA     |  Sydöstra Australien       | Södra Brasilien        | Storbritannien, södra        |
|Södra centrala USA     | Västra Japan        |Centrala Korea         | Centrala Tyskland        |
|Västra centrala USA     |  Östra Japan       | Virginia (USA-förvaltad region)        | Nordöstra Tyskland        |


## <a name="security-considerations"></a>Säkerhetsöverväganden

Data på enheten krypteras med BitLocker-diskkryptering. Den här kryptering skyddar dina data när den är i överföringen.

För importjobb krypteras enheter på två sätt.  


- Ange alternativet när du använder *dataset.csv* fil när du kör verktyget WAImportExport vid förberedelsen av enheten. 

- Aktivera BitLocker-kryptering manuellt på enheten. Ange krypteringsnyckeln i den *driveset.csv* när du kör kommandoraden i WAImportExport verktyget vid förberedelsen av enheten.


För exportjobb krypterar tjänsten enheten med BitLocker innan leverans till dig när dina data kopieras till enheterna. Krypteringsnyckeln tillhandahålls via Azure portal.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]


### <a name="pricing"></a>Prissättning

**Enheten som hanterar avgift**

Det finns en avgift för hantering av enheten för varje enhet som behandlas som en del av din importera eller exportera jobb. Se information på den [priser för Azure Import/Export](https://azure.microsoft.com/pricing/details/storage-import-export/).

**Leverera kostnader**

När du levererar enheter till Azure betalar kostnaden leverans till leverans operatör. När Microsoft returnerar enheterna du debiteras kostnaden leverans till kontot operatör som du angav på jobbet skapades.

**Transaktionskostnader**

Det finns ingen transaktionskostnader utöver standardlagring transaktionskostnader när du importerar data till Azure Storage. Standard utgång avgifterna är tillämpliga när exporteras data från Blob storage. Mer information om transaktionskostnader finns [dataöverföring priser.](https://azure.microsoft.com/pricing/details/data-transfers/)



## <a name="next-steps"></a>Nästa steg

Lär dig hur du använder tjänsten Import/Export för att:
* [Importera data till Azure-BLOB](storage-import-export-data-to-blobs.md)
* [Exportera data från Azure-BLOB](storage-import-export-data-from-blobs.md)
* [Importera data till Azure-filer](storage-import-export-data-to-files.md)

