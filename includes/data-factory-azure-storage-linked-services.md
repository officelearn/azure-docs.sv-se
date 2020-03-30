---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 37917e0ed663675677f1d0452b5796120ca2694e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "75467711"
---
### <a name="azure-storage-linked-service"></a>Länkad Azure Storage-tjänst
Med azure **storage-länkade tjänsten** kan du länka ett Azure-lagringskonto till en Azure-datafabrik med hjälp av **kontonyckeln**, som ger datafabriken global åtkomst till Azure Storage. Följande tabell innehåller en beskrivning av JSON-element som är specifika för Azure Storage-länkade tjänsten.

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ |Typegenskapen måste anges till: **AzureStorage** |Ja |
| Connectionstring |Ange information som behövs för att ansluta till Azure-lagring för egenskapen connectionString. |Ja |

Information om hur du hämtar åtkomstnycklarna för lagringskonto finns i [Hantera åtkomstnycklar för lagringskonto](../articles/storage/common/storage-account-keys-manage.md).

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

### <a name="azure-storage-sas-linked-service"></a>Azure Storage Sas-länkad tjänst
En signatur för delad åtkomst (Shared Access Signature, SAS) ger delegerad åtkomst till resurser på ditt lagringskonto. Det gör att du kan bevilja en klient begränsad behörighet till objekt i ditt lagringskonto under en viss tidsperiod och med en angiven uppsättning behörigheter, utan att behöva dela dina kontoåtkomstnycklar. SAS är en URI som i sina frågeparametrar omfattar all information som behövs för autentiserat åtkomst till en lagringsresurs. För att komma åt lagringsresurser med SAS behöver klienten bara skicka i SAS till lämplig konstruktor eller metod. Mer information om SAS finns i [Bevilja begränsad åtkomst till Azure Storage-resurser med hjälp av SAS (Shared Access Signatures).](../articles/storage/common/storage-sas-overview.md)

> [!IMPORTANT]
> Azure Data Factory stöder nu bara **Service SAS** men inte Konto SAS. Sas-URL-url:en som kan vara generabel från Azure-portalen eller Storage Explorer är ett konto SAS som inte stöds.

> [!TIP]
> Du kan köra under PowerShell-kommandon för att generera en Service SAS för ditt lagringskonto (ersätta platsinnehavarna och bevilja nödvändig behörighet):`$context = New-AzStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Med den länkade Azure Storage SAS-tjänsten kan du länka ett Azure Storage-konto till en Azure-datafabrik med hjälp av en SAS (Shared Access Signature). Det ger datafabriken begränsad/tidsbunden åtkomst till alla/specifika resurser (blob/container) i lagringen. Följande tabell innehåller en beskrivning av JSON-element som är specifika för Azure Storage SAS-länkade tjänst. 

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ |Typegenskapen måste anges till: **AzureStorageSas** |Ja |
| sasuri (samma sak) |Ange URI för delad åtkomstsignatur till Azure Storage-resurser som blob, behållare eller tabell.  |Ja |

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

När du skapar en **SAS URI**med tanke på följande:  

* Ange lämpliga **läs-/skrivbehörigheter** för objekt baserat på hur den länkade tjänsten (läsa, skriva, läsa/skriva) används i din datafabrik.
* Ställ **in utgångstid** på lämpligt sätt. Kontrollera att åtkomsten till Azure Storage-objekt inte upphör att gälla inom den aktiva perioden för pipelinen.
* Uri ska skapas på rätt behållare/blob eller Tabellnivå baserat på behovet. En SAS Uri till en Azure-blob gör det möjligt för datafabrikstjänsten att komma åt just den bloben. En SAS Uri till en Azure blob-behållare gör att datafabrikstjänsten kan iterera via blobbar i den behållaren. Om du behöver ge åtkomst till fler/färre objekt senare, eller uppdatera SAS URI, kom ihåg att uppdatera den länkade tjänsten med den nya URI.If you need to provide access more/fewer objects later, or update the SAS URI, remember to update the linked service with the new URI.   

