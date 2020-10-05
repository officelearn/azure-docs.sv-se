---
title: Hantera och hitta data i Azure Blob Storage med BLOB-index (för hands version)
description: Lär dig hur du använder BLOB-taggar för att kategorisera, hantera och fråga efter BLOB-objekt.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/17/2020
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: hux
ms.custom: references_regions
ms.openlocfilehash: 3b6162552e43c9f475bef2ca3097da22ae198011
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "91713692"
---
# <a name="manage-and-find-azure-blob-data-with-blob-index-preview"></a>Hantera och hitta Azure blob-data med BLOB-index (för hands version)

När data uppsättningar blir större och större, kan det vara svårt och frustrerande att söka efter ett särskilt objekt i en fart på data. BLOB-index ger data hanterings-och identifierings funktioner som använder nyckel-värdens index tag-attribut, vilket gör att du kan kategorisera och hitta objekt inom en enda behållare eller över alla behållare i ditt lagrings konto. Senare, som krav på data ändringar, kan objekt kategoriseras dynamiskt genom att uppdatera sina index taggar samtidigt som de är kvar på plats med sin nuvarande behållar organisation. Användningen av BLOB-index kan förenkla utvecklingen genom att konsolidera dina BLOB-data och associerade indexattribut i samma tjänst. gör det möjligt att bygga effektiva och skalbara program med hjälp av inbyggda funktioner.

Med BLOB-index kan du:

- Kategorisera dina blobar dynamiskt med hjälp av nyckel värde index taggar för data hantering
- Hitta snabbt vissa taggade blobbar i en enda behållare eller ett helt lagrings konto
- Ange villkorliga beteenden för BLOB-API: er baserat på utvärderingen av index Taggar
- Använd index taggar för avancerade kontroller av BLOB Platform-funktioner som [livs cykel hantering](storage-lifecycle-management-concepts.md)

Tänk dig ett scenario där du har miljon tals blobbar i ditt lagrings konto som skrivits och används av många olika program. Du vill söka efter alla relaterade data från ett enda projekt, men du är inte säker på vad som är inom omfånget som data kan spridas över flera behållare med olika namngivnings konventioner för BLOB. Men du vet att dina program laddar upp alla data med Taggar baserat på deras respektive projekt och identifierings beskrivning. I stället för att söka igenom miljon tals blobbar och jämföra namn och egenskaper kan du bara använda `Project = Contoso` som identifierings villkor. BLOB-index filtrerar alla behållare över hela lagrings kontot för att snabbt hitta och returnera bara uppsättningen av 50 blobbar från `Project = Contoso` .

Information om hur du kommer igång med exempel på hur du använder BLOB-index finns i [använda BLOB-index för att hantera och söka efter data](storage-blob-index-how-to.md).

## <a name="blob-index-tags-and-data-management"></a>BLOB-index Taggar och data hantering

Prefix för behållare och blob-namn är en dimensionell kategorisering för dina lagrade data. Med BLOB index kan du nu använda flera dimensions kategoriseringar för alla [BLOB-datatyper (block, tillägg eller sida)](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) med använda Attribute-taggar. Den här flerdimensionella kategoriseringen indexeras och görs tillgänglig så att du snabbt kan fråga efter och hitta dina data.

Överväg följande fem blobbar i ditt lagrings konto:

- *container1/transaction.csv*
- *container2/campaign.docx*
- *foton/bannerphoto.png*
- *Arkiv/slutfört/2019review.pdf*
- *loggar/2020/01/01/logfile.txt*


Dessa blobbar är för närvarande separerade med ett prefix för *behållare/virtuell mapp/BLOB-namn*. Med BLOB-index kan du ange ett index tag-attribut för `Project = Contoso` på dessa fem blobbar för att kategorisera dem tillsammans samtidigt som du behåller sin aktuella prefix organisation. Detta eliminerar behovet av att flytta data genom att exponera möjligheten att filtrera och söka efter data med hjälp av lagrings plattformens multi-dimensionella index.

## <a name="setting-blob-index-tags"></a>Anger BLOB-index-Taggar

Blobb index taggar är nyckel värdes attribut som kan tillämpas på nya eller befintliga objekt i ditt lagrings konto. Du kan ange index Taggar under uppladdnings processen med hjälp av åtgärderna PutBlob, PutBlockList eller CopyBlob och de valfria x-MS-Tags-rubrikerna. Om du redan har blobbar i ditt lagrings konto kan du anropa SetBlobTags med ett formaterat XML-dokument som anger BLOB index tag-attributen i bröd texten i begäran.

Överväg följande exempel taggar som kan ställas in

Du kan använda en enda tagg i blobben för att beskriva när dina data har slutförts.

> "processedDate" = "2020-01-01"

Du kan använda flera taggar i blobben för att vara mer beskrivande data.

> "Projekt" = "contoso"  
> "Klassificerad" = "sant"  
> "Status" = "Unprocessed"  
> "Prioritet" = ' 01 '

Om du vill ändra de befintliga attributen för index tag gen måste du först hämta befintliga taggattribut, ändra taggattribut och ersätta med SetBlobTags-åtgärden. Om du vill ta bort alla index taggar från blobben anropar du åtgärden SetBlobTags utan att ange några taggattribut. Eftersom BLOB index-taggar är en under resurs till BLOB-datainnehållet, ändrar SetBlobTags inte något underliggande innehåll och ändrar inte blobens senaste ändrings tid eller eTag (Entity tag). Du kan skapa eller ändra index taggar för alla aktuella bas-blobar och tidigare versioner. Det går dock inte att ändra taggar för ögonblicks bilder eller mjuk borttagna blobbar.

Följande begränsningar gäller för BLOB-index Taggar:
- Varje Blob kan ha upp till 10 BLOB-Taggar
- Etikett nycklar måste vara mellan 1 och 128 tecken
- Tagg värden måste vara mellan 0 och 256 tecken
- Etikett nycklar och värden är Skift läges känsliga
- Etikett nycklar och värden stöder bara sträng data typer. siffror, datum, tider eller specialtecken kommer att sparas som strängar
- Etikett nycklar och värden måste följa följande namngivnings regler:
  - Alfanumeriska tecken: a-z, A-Z, 0-9
  - Specialtecken: utrymme, plus, minus, punkt, kolon, likhets tecken, under streck, snedstreck

## <a name="getting-and-listing-blob-index-tags"></a>Hämta och Visa BLOB-index Taggar

BLOB index-Taggar lagras som en under resurs tillsammans med BLOB-data och kan hämtas oberoende av det underliggande BLOB-datainnehållet. När du har angett kan BLOB-taggarna för en enskild BLOB hämtas och granskas direkt med GetBlobTags-åtgärden. ListBlobs-åtgärden med `include:tags` parameter returnerar även alla blobbar i en behållare tillsammans med deras använda BLOB-taggar.

För alla blobbar med minst 1 BLOB-tagg returneras x-MS-tag-Count i ListBlobs-, GetBlob-och GetBlobProperties-åtgärderna som indikerar antalet BLOB-taggar som finns i blobben.

## <a name="finding-data-using-blob-index-tags"></a>Hitta data med hjälp av BLOB-Taggar

Med BLOB-Taggar inställda på dina blobar visar indexerings motorn dessa nyckel/värde-attribut i ett flerdimensionellt index. Även om index taggarna finns på blobben och kan hämtas omedelbart, kan det ta lite tid innan BLOB-indexet uppdateras med attributen för uppdaterade index tag. När BLOB-indexet har uppdaterats kan du nu dra nytta av de interna fråge-och identifierings funktionerna som finns i Blob Storage.

Med FindBlobsByTags-åtgärden kan du hämta en filtrerad Return-uppsättning av blobbar vars index taggar matchar ett angivet BLOB index-frågeuttryck. BLOB-index stöder filtrering över alla behållare i ditt lagrings konto eller så kan du begränsa filtreringen till bara en enda behållare. Eftersom alla BLOB-Taggar och-värden är strängar, använder de Relations operatorer som stöds en lexikografisk sortering på index tag gen värden.

Följande villkor gäller för BLOB-index filtrering:
- Etikett nycklar ska omges av dubbla citat tecken (")
- Tagg värden och behållar namn ska omges av enkla citat tecken (')
- @-Tecknen tillåts bara för filtrering på ett angivet container namn (t. ex. @container "ContainerName")
- Filter tillämpas med lexikografisk sortering på strängar
- Samma sido intervall åtgärder på samma nyckel är ogiltiga (t. ex. "rang" > "10" och "rang" >= "15")
- När du använder REST för att skapa ett filter uttryck ska tecknen vara URI-kodade

I tabellen nedan visas alla giltiga operatorer för FindBlobsByTags:

|  Operator  |  Beskrivning  | Exempel |
|------------|---------------|---------|
|     =      |     Lika med     | "Status" = pågår " |
|     >      |  Större än | "Datum" > "2018-06-18" |
|     >=     |  Större än eller lika med | "Prioritet" >= ' 5 ' |
|     <      |  Mindre än   | "Ålder" < "32" |
|     <=     |  Mindre än eller lika med  | "Företag" <= ' contoso ' |
|    AND     |  Logiska och  | "Rang" >= "010" och "rang" < "100" |
| @container | Omfång till en angiven behållare | @container = ' videofiles ' och ' status ' = ' slutförd ' |

> [!NOTE]
> Var bekant med lexicographical-beställning när du ställer in och frågar efter taggar.
> - Siffror sorteras före bokstäver. Siffror sorteras baserat på den första siffran.
> - Versaler sorteras före gemener.
> - Symbolerna är inte standard. Vissa symboler sorteras före numeriska värden. Andra symboler sorteras före eller efter bokstäver.

## <a name="conditional-blob-operations-with-blob-index-tags"></a>Villkorliga BLOB-åtgärder med BLOB-index Taggar
I REST-versioner 2019-10-10 och högre stöder de flesta [BLOB service API: er](https://docs.microsoft.com/rest/api/storageservices/operations-on-blobs) nu en villkors rubrik, x-MS-IF-taggar, så att åtgärden endast lyckas om det angivna BLOB-indexets villkor är uppfyllt. Om villkoret inte uppfylls kommer du att få `error 412: The condition specified using HTTP conditional header(s) is not met` .

Rubriken x-MS-IF-taggar kan kombineras med andra befintliga HTTP villkorliga huvuden (IF-match, If-None-Match osv.).  Om flera villkorliga huvuden anges i en begäran måste alla utvärdera sant för att åtgärden ska lyckas.  Alla villkorliga huvuden kombineras effektivt med logiska och.

I tabellen nedan visas alla giltiga operatorer för villkorliga åtgärder:

|  Operator  |  Beskrivning  | Exempel |
|------------|---------------|---------|
|     =      |     Lika med     | "Status" = pågår " |
|     <>     |   Inte lika med   | "Status"  <>  "slutförd"  |
|     >      |  Större än | "Datum" > "2018-06-18" |
|     >=     |  Större än eller lika med | "Prioritet" >= ' 5 ' |
|     <      |  Mindre än   | "Ålder" < "32" |
|     <=     |  Mindre än eller lika med  | "Företag" <= ' contoso ' |
|    AND     |  Logiska och  | "Rang" >= "010" och "rang" < "100" |
|     ELLER     | Logiska eller   | "Status" = "färdig" eller "prioritet" >= "05" |

> [!NOTE]
> Det finns två ytterligare operatorer, inte lika och logiska eller, som tillåts i rubriken för villkorlig x-MS-Tags-taggar för BLOB operation, men finns inte i FindBlobsByTags-åtgärden.

## <a name="platform-integrations-with-blob-index-tags"></a>Plattforms integrering med BLOB-Taggar

BLOB-indexets taggar kan inte bara hjälpa dig att kategorisera, hantera och söka efter dina BLOB-data, utan även tillhandahålla integration med andra Blob Service funktioner, till exempel [livs cykel hantering](storage-lifecycle-management-concepts.md).

### <a name="lifecycle-management"></a>Livs cykel hantering

Med hjälp av den nya blobIndexMatch som regel filter i livs cykel hantering kan du flytta data till låg frekventa nivåer eller ta bort data baserat på de index taggar som används för dina blobbar. På så sätt kan du vara mer detaljerad i reglerna och bara flytta eller ta bort data om de matchar de angivna taggarna.

Du kan ange en BLOB-index matchning som en fristående filter uppsättning i en livs cykel regel för att tillämpa åtgärder på taggade data. Eller så kan du kombinera både en prefix matchning och en BLOB-index matchning för att matcha mer detaljerade data uppsättningar. Att använda flera filter i en regel för en livs cykel regel är ett logiskt och en åtgärd så att åtgärden endast kommer att gälla om alla filter villkor matchar.

Följande exempel på en hanterings regel för livs cykel gäller för att blockera blobar i behållaren "videofiles" och nivås-blobbar till Arkiv lag ring endast om data matchar BLOB index tag-kriteriet för ```"Status" = 'Processed' AND "Source" == 'RAW'``` .

# <a name="portal"></a>[Portal](#tab/azure-portal)
![Exempel på en matchnings regel för BLOB-index för livs cykel hantering i Azure Portal](media/storage-blob-index-concepts/blob-index-lifecycle-management-example.png)

# <a name="json"></a>[JSON](#tab/json)
```json
{
    "rules": [
        {
            "enabled": true,
            "name": "ArchiveProcessedSourceVideos",
            "type": "Lifecycle",
            "definition": {
                "actions": {
                    "baseBlob": {
                        "tierToArchive": {
                            "daysAfterModificationGreaterThan": 0
                        }
                    }
                },
                "filters": {
                    "blobIndexMatch": [
                        {
                            "name": "Status",
                            "op": "==",
                            "value": "Processed"
                        },
                        {
                            "name": "Source",
                            "op": "==",
                            "value": "RAW"
                        }
                    ],
                    "blobTypes": [
                        "blockBlob"
                    ],
                    "prefixMatch": [
                        "videofiles/"
                    ]
                }
            }
        }
    ]
}
```
---

## <a name="permissions-and-authorization"></a>Behörigheter och auktorisering

Du kan ge åtkomst till BLOB-index med någon av följande metoder:

- Genom att använda rollbaserad åtkomst kontroll i Azure (Azure RBAC) för att bevilja behörighet till ett Azure Active Directory (Azure AD) säkerhets objekt. Microsoft rekommenderar att du använder Azure AD för överlägsen säkerhet och enkel användning. Mer information om hur du använder Azure AD med BLOB-åtgärder finns i [bevilja åtkomst till blobbar och köer med hjälp av Azure Active Directory](../common/storage-auth-aad.md).
- Genom att använda en signatur för delad åtkomst (SAS) för att delegera åtkomst till BLOB-index. Mer information om signaturer för delad åtkomst finns i [bevilja begränsad åtkomst till Azure Storage-resurser med hjälp av signaturer för delad åtkomst (SAS)](../common/storage-sas-overview.md).
- Genom att använda kontots åtkomst nycklar för att godkänna åtgärder med delad nyckel. Mer information finns i [auktorisera med delad nyckel](/rest/api/storageservices/authorize-with-shared-key).

BLOB index-taggar är en under resurs till BLOB-data. En användare med behörigheter eller en SAS-token för att läsa eller skriva blobbar kanske inte har åtkomst till BLOB-taggarna.

### <a name="role-based-access-control"></a>Rollbaserad åtkomstkontroll
Anropare som använder en [Azure AD-identitet](../common/storage-auth-aad.md) kan beviljas följande behörigheter för att arbeta med BLOB-taggar.

|   BLOB-åtgärder  |  Azure RBAC-åtgärd   |
|--------------------|----------------|
| Hitta blobbar efter Taggar | Microsoft. Storage/storageAccounts/blobServices/containers/blobbar/filter/åtgärd |
| Ange BLOB-Taggar      | Microsoft. Storage/storageAccounts/blobServices/containers/blobbar/Taggar/skrivning |
| Hämta BLOB-Taggar      | Microsoft. Storage/storageAccounts/blobServices/containers/blobbar/Taggar/läsa |

Ytterligare behörigheter som skiljer sig från underliggande BLOB-data krävs för att kunna köras över taggarna. Ägaren till Storage BLOB data-rollen kommer att beviljas alla tre dessa behörigheter. Lagrings-BLOB-dataläsaren kommer att beviljas den enda find-blobben efter taggar och hämta BLOB-taggar.

### <a name="sas-permissions"></a>SAS-behörigheter
Anropare som använder en [signatur för delad åtkomst (SAS)](../common/storage-sas-overview.md) kan beviljas begränsade behörigheter för att köras på BLOB-taggar.

#### <a name="blob-sas"></a>BLOB SAS
Följande behörigheter kan beviljas i en Blob Service SAS för att tillåta åtkomst till BLOB index-taggar. Enbart BLOB Läs-och Skriv behörighet är inte tillräckligt för att tillåta läsning eller skrivning av index taggar.

|  Behörighet  |  URI-symbol  | Tillåtna åtgärder |
|--------------|--------------|--------------------|
|  Index Taggar  |      t      | Hämta och ange BLOB index-taggar för en BLOB |

#### <a name="container-sas"></a>Behållar-SAS
Följande behörigheter kan beviljas i behållar tjänstens SAS för att tillåta filtrering på BLOB-taggar.  Behörigheten BLOB List är inte tillräckligt för att tillåta filtrering av blobbar med index taggar.

|  Behörighet  |  URI-symbol  | Tillåtna åtgärder |
|--------------|--------------|--------------------|
| Index Taggar   |      f      | Hitta blobbar med BLOB-Taggar |

## <a name="choosing-between-metadata-and-blob-index-tags"></a>Välja mellan metadata och blob-Taggar
Både BLOB-Taggar och metadata ger möjlighet att lagra godtyckliga användardefinierade nyckel-och värde egenskaper tillsammans med en BLOB-resurs. Båda kan hämtas och anges direkt, utan att returnera eller ändra innehållet i blobben. Det går att använda både metadata och index taggar.

Det är dock bara BLOB-taggar för BLOB som indexeras automatiskt och som kan göras efter frågas av den interna Blob-tjänsten. Det går inte att indexera och fråga metadata internt om du inte använder en separat tjänst, till exempel [Azure Search](../../search/search-blob-ai-integration.md). BLOB index-Taggar har också ytterligare behörigheter för läsning/filtrering och skrivning som är åtskilda från underliggande BLOB-data. Metadata använder samma behörigheter som blobben och returneras som HTTP-huvuden i GetBlob-eller GetBlobProperties-åtgärderna. BLOB-taggarna är krypterade i vila med hjälp av en [Microsoft-hanterad nyckel](../common/storage-service-encryption.md) medan metadata krypteras i vila med samma krypterings nyckel som angetts för BLOB-data.

I följande tabell sammanfattas skillnaderna mellan metadata och blob-index Taggar:

|              |   Metadata   |   BLOB index-Taggar  |
|--------------|--------------|--------------------|
| **Begränsningar**      | Ingen numerisk gräns; totalt 8 KB; Skift läges okänslig | 10 Taggar per BLOB Max; 768 byte per tagg; Skift läges känslig |
| **Uppdateringar**    | Tillåts inte på Arkiv nivå. SetBlobMetadata ersätter alla befintliga metadata. SetBlobMetadata ändrar blobens senaste ändrings tid | Tillåts för alla åtkomst nivåer. SetBlobTags ersätter alla befintliga taggar; SetBlobTags ändrar inte blobens senaste ändrings tid |
| **Storage**     | Lagrad med BLOB-data | Under resurs till BLOB-data |
| **Indexerar & frågor** | Ej tillämpligt, ursprungligt; måste använda en separat tjänst, till exempel Azure Search | Ja, inbyggd indexering och frågor om funktioner som är inbyggda i Blob Storage |
| **Kryptering** | Krypterat i vila med samma krypterings nyckel som används för BLOB-data | Krypterat i vila med en Microsoft-hanterad krypterings nyckel |
| **Prissättning** | Storleken på metadata ingår i lagrings kostnaderna för en BLOB | Fast kostnad per index-tagg |
| **Rubrik svar** | Metadata returnerade som huvuden i GetBlob och GetBlobProperties | TagCount returneras i GetBlob eller GetBlobProperties; Taggar som returneras endast i GetBlobTags och ListBlobs |
| **Behörigheter**  | Läs-eller Skriv behörighet till BLOB-data utökas till metadata | Ytterligare behörigheter krävs för att läsa/filtrera eller skriva Taggar |
| **Namngivning** | Metadata-namn måste följa namngivnings reglerna för C#-identifierare | BLOB index-Taggar har stöd för ett bredare antal alfanumeriska tecken |

## <a name="pricing"></a>Prissättning
Priser för BLOB index är för närvarande en offentlig för hands version och kan ändras för allmän tillgänglighet. Kunderna debiteras för det totala antalet BLOB-index i ett lagrings konto, i genomsnitt under månaden. Det kostar inget att indexera motorn. Begär anden till SetBlobTags, GetBlobTags och FindBlobsByTags debiteras enligt deras respektive åtgärds typer. Mer information finns i avsnittet [om Block-Blob-prissättning](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="regional-availability-and-storage-account-support"></a>Stöd för regional tillgänglighet och lagrings konto

BLOB index är för närvarande bara tillgängligt på Generell användning v2-konton (GPv2) med hierarkiskt namn område (HNS) inaktiverat. Generell användning-konton (GPV1) stöds inte, men du kan uppgradera valfritt GPv1-konto till ett GPv2-konto. Mer information om lagrings konton finns i [Översikt över Azure Storage-konto](../common/storage-account-overview.md).

I den offentliga för hands versionen är BLOB index för närvarande endast tillgängligt i följande valda regioner:
- Kanada, centrala
- Kanada, östra
- Frankrike, centrala
- Frankrike, södra

Information om hur du kommer igång finns i [använda BLOB-index för att hantera och söka efter data](storage-blob-index-how-to.md).

> [!IMPORTANT]
> Se avsnittet villkor i den här artikeln. Registrera din prenumeration innan du kan använda BLOB-index på dina lagrings konton för att registrera dig i för hands versionen.

### <a name="register-your-subscription-preview"></a>Registrera din prenumeration (för hands version)
Eftersom BLOB-indexet endast finns i en offentlig för hands version måste du registrera din prenumeration innan du kan använda funktionen. Kör följande PowerShell-eller CLI-kommandon om du vill skicka en begäran.

#### <a name="register-by-using-powershell"></a>Registrera med PowerShell
```powershell
Register-AzProviderFeature -FeatureName BlobIndex -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

#### <a name="register-by-using-azure-cli"></a>Registrera med Azure CLI
```azurecli
az feature register --namespace Microsoft.Storage --name BlobIndex
az provider register --namespace 'Microsoft.Storage'
```

## <a name="conditions-and-known-issues-preview"></a>Villkor och kända problem (förhands granskning)
I det här avsnittet beskrivs kända problem och villkor i den aktuella offentliga för hands versionen av BLOB-index. Som i de flesta för hands versionerna bör den här funktionen inte användas för produktions arbets belastningar förrän den når GA när beteenden kan ändras.

- För för hands versionen måste du först registrera din prenumeration innan du kan använda BLOB-index för ditt lagrings konto i för hands versions regionerna.
- Endast GPv2-konton stöds för närvarande i för hands versionen. BLOB-, BlockBlobStorage-och HNS-aktiverade DataLake Gen2-konton stöds för närvarande inte av BLOB-index. GPv1-konton stöds inte.
- Överföring av sid-blobar med index taggar för närvarande behåller inte taggarna. Du måste ange taggarna när du har överfört en sid-blob.
- När filtreringen är begränsad till en enda behållare kan det @container bara skickas om alla index Taggar i filter uttrycket är likhets kontroller (nyckel = värde).
- När du använder intervall operatorn med villkoret och kan du bara ange samma index etiketts nyckel namn (ålder > ' 013 ' och ålder < ' 100 ').
- Versions-och blob-index stöds inte för närvarande. BLOB index-taggar bevaras för versioner, men skickas för närvarande inte till BLOB-databasmotorn.
- Redundansväxling av kontot stöds inte för närvarande. BLOB-indexet kanske inte uppdateras korrekt efter redundansväxlingen.
- Livs cykel hantering stöder för närvarande bara likhets kontroller med BLOB-index matchning.
- CopyBlob kopierar inte BLOB-index-taggar från käll-bloben till den nya mål-bloben. Du kan ange de taggar som du vill använda för mål-bloben under kopierings åtgärden.
- CopyBlob (asynkron kopia) från ett annat lagrings konto med tillämpade taggar på mål-bloben gör att BLOB-index motorn inte returnerar blobben och dess Taggar i filter uppsättningen. Vi rekommenderar att du använder CopyBlob från URL (synkronisera kopia) i Interim.
- Taggarna sparas när en ögonblicks bild skapas. Det finns för närvarande inte stöd för att befordra en ögonblicks bild och kan resultera i en tom tag-uppsättning.

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

### <a name="can-blob-index-help-me-filter-and-query-content-inside-my-blobs"></a>Kan du använda BLOB-index för att filtrera och fråga efter innehåll i mina blobbar?
Nej, BLOB index-taggar kan hjälpa dig att hitta de blobbar som du letar efter. Om du behöver söka i dina blobbar använder du Query acceleration eller Azure Search.

### <a name="are-there-any-special-considerations-regarding-blob-index-tag-values"></a>Finns det några särskilda saker som rör BLOB index tag-värden?
BLOB index-Taggar stöder bara sträng data typer och frågor returnerar resultat med lexicographical-ordning. För siffror rekommenderar vi att du inte fyller i numret. För datum och tider rekommenderar vi att du lagrar som ett ISO 8601-kompatibelt format.

### <a name="are-blob-index-tags-and-azure-resource-manager-tags-related"></a>Är BLOB-index Taggar och Azure Resource Manager Taggar relaterade?
Nej, Resource Manager-Taggar hjälper till att organisera kontroll Plans resurser som prenumerationer, resurs grupper och lagrings konton. BLOB index-Taggar tillhandahåller objekt hantering och identifiering av data Plans resurser, till exempel blobbar i ett lagrings konto.

## <a name="next-steps"></a>Nästa steg

Ett exempel på hur du använder BLOB-index finns i [använda BLOB-index för att hantera och söka efter data](storage-blob-index-how-to.md).

Lär dig mer om [livs cykel hantering](storage-lifecycle-management-concepts.md) och ange en regel med matchning av BLOB-index.

