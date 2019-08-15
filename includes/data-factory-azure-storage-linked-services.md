---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: ee368b58195d61a1c6792a3a3655122af7104d58
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "69012290"
---
### <a name="azure-storage-linked-service"></a>Länkad Azure Storage-tjänst
Med den **länkade tjänsten Azure Storage** kan du länka ett Azure Storage-konto till en Azure-datafabrik med hjälp av **konto nyckeln**, som tillhandahåller data fabriken med global åtkomst till Azure Storage. Följande tabell innehåller en beskrivning av JSON-element som är speciella för Azure Storage länkade tjänsten.

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type |Egenskapen Type måste anges till: **AzureStorage** |Ja |
| connectionString |Ange information som krävs för att ansluta till Azure Storage för egenskapen connectionString. |Ja |

I följande avsnitt beskrivs hur du visar/kopierar konto nyckeln för en Azure Storage: [Åtkomst nycklar](../articles/storage/common/storage-account-manage.md#access-keys).

**Exempel:**  

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

### <a name="azure-storage-sas-linked-service"></a>Azure Storage SAS-länkad tjänst
En signatur för delad åtkomst (SAS) ger delegerad åtkomst till resurser i ditt lagrings konto. Det gör att du kan ge en klient begränsad behörighet till objekt i ditt lagrings konto under en angiven tids period och med en angiven uppsättning behörigheter, utan att behöva dela dina konto åtkomst nycklar. SAS är en URI som omfattar den information som krävs för autentiserad åtkomst till en lagrings resurs i dess frågeparametrar. För att få åtkomst till lagrings resurser med SAS måste klienten bara skicka in SAS till lämplig konstruktor eller metod. Mer information om SAS finns i [bevilja begränsad åtkomst till Azure Storage resurser med hjälp av signaturer för delad åtkomst (SAS)](../articles/storage/common/storage-sas-overview.md).

> [!IMPORTANT]
> Azure Data Factory har nu bara stöd för **tjänst-SAS** , men inte konto säkerhets associationer. Observera att SAS URL-generable från Azure Portal eller Storage Explorer är en konto säkerhets Association, vilket inte stöds.

> [!TIP]
> Du kan köra följande PowerShell-kommandon för att skapa en tjänst-SAS för ditt lagrings konto (Ersätt plats hållare och bevilja nödvändig behörighet):`$context = New-AzStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Med den länkade tjänsten Azure Storage SAS kan du länka ett Azure Storage konto till en Azure-datafabrik med hjälp av en signatur för delad åtkomst (SAS). Den tillhandahåller data fabriken med begränsad/tidsbegränsad åtkomst till alla/angivna resurser (BLOB/container) i lagrings utrymmet. Följande tabell innehåller en beskrivning av JSON-element som är speciella för Azure Storage SAS-länkad tjänst. 

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type |Egenskapen Type måste anges till: **AzureStorageSas** |Ja |
| sasUri |Ange signatur-URI för delad åtkomst till Azure Storage resurser som BLOB, container eller Table.  |Ja |

**Exempel:**

```json
{
    "name": "StorageSasLinkedService",
    "properties": {
        "type": "AzureStorageSas",
        "typeProperties": {
            "sasUri": "<Specify SAS URI of the Azure Storage resource>"
        }
    }
}
```

När du skapar en **SAS-URI**, som beaktar följande:  

* Ange lämpliga Läs-/skriv **behörigheter** för objekt baserat på hur den länkade tjänsten (läsa, skriva, läsa/skriva) används i din data fabrik.
* Ange **förfallotiden** på rätt sätt. Se till att åtkomsten till Azure Storage objekt upphör att gälla inom den aktiva perioden för pipelinen.
* URI: n måste skapas på rätt behållare/BLOB eller tabell nivå baserat på behovet. En SAS-URI till en Azure-Blob gör att Data Factorys tjänsten kan komma åt den specifika blobben. Med en SAS-URI till en Azure Blob-behållare kan Data Factorys tjänsten iterera genom blobbar i den behållaren. Om du behöver ge åtkomst mer/färre objekt senare, eller uppdatera SAS-URI: n, måste du komma ihåg att uppdatera den länkade tjänsten med den nya URI: n.   

