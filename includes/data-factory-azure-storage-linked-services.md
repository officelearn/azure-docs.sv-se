---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: b8585b62b0728d1ba6e010e42b44840903c46833
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2019
ms.locfileid: "58500873"
---
### <a name="azure-storage-linked-service"></a>Länkad Azure Storage-tjänst
Den **länkad Azure Storage-tjänst** kan du länka ett Azure storage-konto till en Azure-datafabrik med hjälp av den **kontonyckel**, vilket möjliggör data factory med global åtkomst till Azure Storage. Följande tabell innehåller en beskrivning för JSON-element som är specifika för Azure Storage-länkade tjänst.

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ |Type-egenskapen måste anges till: **AzureStorage** |Ja |
| connectionString |Ange information som behövs för att ansluta till Azure storage för connectionString-egenskapen. |Ja |

Se följande avsnitt innehåller steg för att visa/kopiera kontonyckeln för Azure Storage: [Åtkomstnycklar](../articles/storage/common/storage-account-manage.md#access-keys).

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

### <a name="azure-storage-sas-linked-service"></a>Azure Storage Sas länkad tjänst
En signatur för delad åtkomst (SAS) ger delegerad åtkomst till resurser i ditt storage-konto. Det kan du ge en klient begränsad behörighet till objekt i ditt storage-konto under en viss tidsperiod och med en angiven uppsättning behörigheter, utan att behöva dela åtkomstnycklarna för kontot. Signaturen för delad åtkomst är en URI som omfattar all information som behövs för autentiserad åtkomst till en lagringsresurs i dess Frågeparametrar. Om du vill få åtkomst till lagringsresurser med SAS, behöver klienten bara använda SAS till lämplig konstruktor nebo metodu. Detaljerad information om SAS finns i [signaturer för delad åtkomst: Förstå SAS-modellen](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md)

> [!IMPORTANT]
> Azure Data Factory nu endast stöder **SAS för tjänst** men inte SAS-konto. Se [typer av signaturer för delad åtkomst](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures) mer information om dessa två typer och hur du skapar. Observera SAS-Webbadressen generable från Azure-portalen eller Storage Explorer är en SAS-konto, vilket inte stöds.

> [!TIP]
> Du kan köra nedan PowerShell-kommandon för att generera en SAS för tjänst för ditt lagringskonto (Ersätt platshållarna och bevilja behörigheten som krävs): `$context = New-AzStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Med Azure Storage SAS länkad service kan du länka ett Azure Storage-konto till en Azure-datafabrik med hjälp av en signatur för delad åtkomst (SAS). Data factory skapar med begränsade/Tidsbundna åtkomst till alla utvalda resurser (blobbehållare) i lagringen. Följande tabell innehåller en beskrivning för JSON-element som är specifika för Azure Storage SAS länkad tjänst. 

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ |Type-egenskapen måste anges till: **AzureStorageSas** |Ja |
| sasUri |Ange URI för delad åtkomst-signatur i Azure Storage-resurser, till exempel blob, behållaren eller tabellen.  |Ja |

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

När du skapar en **SAS-URI**, överväger följande:  

* Ange rätt skrivbar **behörigheter** på objekt baserat på hur den länkade tjänsten (läsa, skriva, Läs/Skriv) används i din datafabrik.
* Ange **förfallotiden** på rätt sätt. Se till att åtkomsten till Azure Storage-objekt inte går ut inom den aktiva perioden för pipelinen.
* URI: N ska skapas på rätt behållare/blob eller Table utifrån behov. En SAS-Uri till en Azure-blob kan Data Factory-tjänsten att få åtkomst till den specifika blobben. En SAS-Uri till en Azure blobbehållare kan Data Factory-tjänsten att gå igenom blobar i behållaren. Om du vill ge åtkomst mer/färre objekt senare eller uppdatera SAS-URI, Kom ihåg att uppdatera den länkade tjänsten med den nya URI.   

