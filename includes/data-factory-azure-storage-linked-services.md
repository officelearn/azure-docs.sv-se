### <a name="azure-storage-linked-service"></a>Länkad Azure Storage-tjänst
Den **Azure länkade lagringstjänsten** kan du länka ett Azure storage-konto till ett Azure data factory med hjälp av den **kontonyckel**, vilket möjliggör datafabriken med global åtkomst till Azure Storage. Följande tabell innehåller en beskrivning för JSON-element som är specifika för länkad Azure Storage-tjänst.

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ |Egenskapen type måste anges till: **AzureStorage** |Ja |
| connectionString |Ange information som krävs för att ansluta till Azure-lagring för egenskapen connectionString. |Ja |

Se följande artikel steg för att visa/kopiera nyckeln konto för ett Azure Storage: [visa, kopiera och generera lagring åtkomstnycklar](../articles/storage/common/storage-create-storage-account.md#manage-your-storage-account).

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

### <a name="azure-storage-sas-linked-service"></a>Azure Storage Sas länkade tjänsten
En signatur för delad åtkomst (SAS) ger delegerad åtkomst till resurser i ditt lagringskonto. Det gör att du kan ge en klient begränsade behörigheter till objekt i ditt lagringskonto för en angiven tidsperiod och med en angiven uppsättning behörigheter, utan att behöva dela åtkomstnycklarna för ditt konto. SAS är en URI som omfattar all information som krävs för autentiserad åtkomst till en lagringsresurs i dess Frågeparametrar. För att komma åt lagringsresurser med SAS måste klienten endast att skicka in SAS till lämplig konstruktor eller metod. Detaljerad information om SAS finns [signaturer för delad åtkomst: Förstå SAS-modellen](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md)

> [!IMPORTANT]
> Azure Data Factory nu har bara stöd för **Service SAS** men inte kontots SAS. Se [typer av signaturer för delad åtkomst](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures) mer information om dessa två typer och hur du skapar. Observera SAS-URL generable från Azure-portalen eller Lagringsutforskaren är ett konto-SAS, vilket inte stöds.

> [!TIP]
> Du kan köra nedan PowerShell-kommandon för att generera en SAS för tjänst för ditt lagringskonto (Ersätt plats-innehavare och bevilja behörighet som krävs):`$context = New-AzureStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzureStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Azure Storage SAS länkade tjänsten kan du länka ett Azure Storage-konto till ett Azure data factory med hjälp av en delad signatur åtkomst (SAS). Det ger datafabriken begränsad/Tidsbundna åtkomst till alla utvalda resurser (blobbehållare) i lagringen. Följande tabell innehåller en beskrivning för JSON-element som är specifika för Azure Storage SAS länkad tjänst. 

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ |Egenskapen type måste anges till: **AzureStorageSas** |Ja |
| sasUri |Ange URI för delad åtkomst-signaturen i Azure Storage-resurser, till exempel blob, behållare eller tabellen.  |Ja |

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

När du skapar en **SAS URI**, med tanke på följande:  

* Ange lämpliga läsning och skrivning **behörigheter** på objekt baserat på hur den länkade tjänsten (läsa, skriva, läsning och skrivning) används i din data factory.
* Ange **förfallotiden** på lämpligt sätt. Kontrollera åtkomst till Azure Storage objekt inte upphör inom den aktiva perioden för pipelinen.
* URI: N ska skapas på rätt behållare-blob eller tabellen baserat på behovet. En SAS-Uri för en Azure blob kan Data Factory-tjänsten att få åtkomst till viss blobben. En SAS-Uri för en Azure blob-behållare kan Data Factory-tjänsten att gå igenom blobbar i behållaren. Om du behöver ge åtkomst mer/färre objekt senare eller uppdatera SAS-URI, Kom ihåg att uppdatera den länkade tjänsten med ny URI.   

