---
title: Azure Storage kryptering för vilande data | Microsoft Docs
description: Azure Storage skyddar dina data genom att automatiskt kryptera dem innan du sparar dem i molnet. Du kan förlita dig på Microsoft-hanterade nycklar för kryptering av ditt lagrings konto, eller så kan du hantera kryptering med dina egna nycklar.
services: storage
author: tamram
ms.service: storage
ms.date: 10/02/2019
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: a15d450d033c04c59f6981a887689f1fc08919f1
ms.sourcegitcommit: 7868d1c40f6feb1abcafbffcddca952438a3472d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/04/2019
ms.locfileid: "71958861"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>Azure Storage kryptering för vilande data

Azure Storage krypterar dina data automatiskt när de behålls i molnet. Kryptering skyddar dina data och hjälper dig att uppfylla organisationens säkerhets-och efterlevnads åtaganden. Data i Azure Storage krypteras och dekrypteras transparent med 256-bitars AES- [kryptering](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), en av de starkaste block chiffer som är tillgängliga och är FIPS 140-2-kompatibel. Azure Storage kryptering liknar BitLocker-kryptering på Windows.

Azure Storage kryptering har Aktiver ATS för alla nya och befintliga lagrings konton och kan inte inaktive ras. Eftersom dina data är säkra som standard behöver du inte ändra koden eller programmen för att dra nytta av Azure Storage kryptering.

Lagrings konton är krypterade oavsett prestanda nivå (standard eller Premium) eller distributions modell (Azure Resource Manager eller klassisk). Alla Azure Storage alternativ för redundans stöder kryptering och alla kopior av ett lagrings konto krypteras. Alla Azure Storage-resurser krypteras, inklusive blobbar, diskar, filer, köer och tabeller. Alla metadata för objekt krypteras också.

Kryptering påverkar inte Azure Storage prestanda. Det kostar inget extra att Azure Storage kryptering.

Mer information om de kryptografiska modulerna underliggande Azure Storage kryptering finns i [Cryptography API: Nästa generation @ no__t-0.

## <a name="about-encryption-key-management"></a>Om hantering av krypterings nyckel

Du kan förlita dig på Microsoft-hanterade nycklar för kryptering av ditt lagrings konto, eller så kan du hantera kryptering med dina egna nycklar. Om du väljer att hantera kryptering med dina egna nycklar har du två alternativ:

- Du kan ange en *kundhanterad nyckel* som ska användas för kryptering och dekryptering av alla data i lagrings kontot. En kundhanterad nyckel används för att kryptera alla data i alla tjänster i ditt lagrings konto.
- Du kan ange en *anpassad nyckel* för Blob Storage-åtgärder. En klient som gör en Läs-eller skrivbegäran mot Blob Storage kan inkludera en krypterings nyckel på begäran om detaljerad kontroll över hur BLOB-data krypteras och dekrypteras.

I följande tabell jämförs nyckel hanterings alternativ för Azure Storage kryptering.

|                                        |    Microsoft-hanterade nycklar                             |    Kundhanterade nycklar                                                                                                                        |    Kund-tillhandahållna nycklar                                                          |
|----------------------------------------|-------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------|
|    Kryptering/dekryptering    |    Azure                                              |    Azure                                                                                                                                        |    Azure                                                                         |
|    Azure Storage tjänster som stöds    |    Alla                                                |    Blob Storage, Azure Files                                                                                                               |    Blob Storage                                                                  |
|    Nyckel lagring                         |    Microsoft nyckel lager    |    Azure Key Vault                                                                                                                              |    Azure Key Vault eller något annat nyckel Arkiv                                                                 |
|    Största ansvar för nyckel rotation         |    Microsoft                                          |    Kunden                                                                                                                                     |    Kunden                                                                      |
|    Nyckelanvändning                           |    Microsoft                                          |    Azure Portal, Provider för lagrings resurs REST API, Azure Storage hanterings bibliotek, PowerShell, CLI        |    Azure Storage REST API (Blob Storage), Azure Storage klient bibliotek    |
|    Nyckel åtkomst                          |    Endast Microsoft                                     |    Microsoft, kund                                                                                                                    |    Endast kund                                                                 |

I följande avsnitt beskrivs de olika alternativen för nyckel hantering i större detalj.

## <a name="microsoft-managed-keys"></a>Microsoft-hanterade nycklar

Som standard använder ditt lagrings konto Microsoft-hanterade krypterings nycklar. Du kan se krypterings inställningarna för ditt lagrings konto i avsnittet **kryptering** i [Azure Portal](https://portal.azure.com), som du ser i följande bild.

![Visa kontot krypterat med Microsoft-hanterade nycklar](media/storage-service-encryption/encryption-microsoft-managed-keys.png)

## <a name="customer-managed-keys"></a>Kundhanterade nycklar

Du kan välja att hantera Azure Storage kryptering på lagrings kontots nivå med dina egna nycklar. När du anger en kundhanterad nyckel på lagrings kontots nivå används nyckeln för att kryptera och dekryptera alla data i lagrings kontot, inklusive BLOB-, Queue-, fil-och tabell data.  Kundhanterade nycklar ger större flexibilitet för att skapa, rotera, inaktivera och återkalla åtkomst kontroller. Du kan också granska de krypterings nycklar som används för att skydda dina data.

Du måste använda Azure Key Vault för att lagra dina Kundhanterade nycklar. Du kan antingen skapa egna nycklar och lagra dem i ett nyckel valv, eller så kan du använda Azure Key Vault API: er för att generera nycklar. Lagrings kontot och nyckel valvet måste finnas i samma region, men de kan finnas i olika prenumerationer. Mer information om Azure Key Vault finns i [Azure Key Vault?](../../key-vault/key-vault-overview.md).

Det här diagrammet visar hur Azure Storage använder Azure Active Directory och Azure Key Vault för att göra förfrågningar med hjälp av kundhanterad nyckel:

![Diagram över hur Kundhanterade nycklar fungerar i Azure Storage](media/storage-service-encryption/encryption-customer-managed-keys-diagram.png)

I följande lista beskrivs de numrerade stegen i diagrammet:

1. En Azure Key Vault administratör beviljar behörigheter till krypterings nycklar till den hanterade identitet som är kopplad till lagrings kontot.
2. En Azure Storage administratör konfigurerar kryptering med en kundhanterad nyckel för lagrings kontot.
3. Azure Storage använder den hanterade identitet som är kopplad till lagrings kontot för att autentisera åtkomst till Azure Key Vault via Azure Active Directory.
4. Azure Storage omsluter konto krypterings nyckeln med kund nyckeln i Azure Key Vault.
5. Vid Läs-och skriv åtgärder skickar Azure Storage begär anden till Azure Key Vault för att omsluta och packa upp konto krypterings nyckeln för att utföra kryptering och dekryptering.

Information om hur du återkallar åtkomst till Kundhanterade nycklar på lagrings kontot finns i [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) och [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault). Att återkalla åtkomsten på ett effektivt sätt blockerar åtkomsten till alla data i lagrings kontot, eftersom krypterings nyckeln inte är tillgänglig via Azure Storage.

Kundhanterade nycklar stöds inte för [Azure Managed disks](../../virtual-machines/windows/managed-disks-overview.md).

Information om hur du använder Kundhanterade nycklar med Azure Storage finns i någon av följande artiklar:

- [Konfigurera kundhanterade nycklar för Azure Storage-kryptering från Azure-portalen](storage-encryption-keys-portal.md)
- [Konfigurera kundhanterade nycklar för Azure Storage-kryptering från PowerShell](storage-encryption-keys-powershell.md)
- [Använda Kundhanterade nycklar med Azure Storage kryptering från Azure CLI](storage-encryption-keys-cli.md)

> [!IMPORTANT]
> Kundhanterade nycklar är beroende av hanterade identiteter för Azure-resurser, en funktion i Azure Active Directory (Azure AD). När du konfigurerar Kundhanterade nycklar i Azure Portal tilldelas en hanterad identitet automatiskt till ditt lagrings konto under försättsblad. Om du senare flyttar prenumerationen, resurs gruppen eller lagrings kontot från en Azure AD-katalog till en annan överförs inte den hanterade identitet som är kopplad till lagrings kontot till den nya klienten, så Kundhanterade nycklar kanske inte längre fungerar. Mer information finns i **överföra en prenumeration mellan Azure AD-kataloger** i [vanliga frågor och svar och kända problem med hanterade identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).  

## <a name="customer-provided-keys-preview"></a>Kundspecifika nycklar (förhands granskning)

Klienter som begär förfrågningar mot Azure Blob Storage har möjlighet att tillhandahålla en krypterings nyckel på en enskild begäran. Inklusive krypterings nyckeln på begäran ger detaljerad kontroll över krypterings inställningarna för Blob Storage-åtgärder. Kundspecifika nycklar (för hands version) kan lagras i Azure Key Vault eller i en annan nyckel lagrings plats.

### <a name="encrypting-read-and-write-operations"></a>Kryptera Läs-och skriv åtgärder

När ett klient program tillhandahåller en krypterings nyckel på begäran utför Azure Storage kryptering och dekryptering transparent vid läsning och skrivning av BLOB-data. En SHA-256-hash av krypterings nyckeln skrivs tillsammans med en blobs innehåll och används för att kontrol lera att alla efterföljande åtgärder mot blobben använder samma krypterings nyckel. Azure Storage lagrar eller hanterar inte den krypterings nyckel som klienten skickar med begäran. Nyckeln ignoreras säkert så snart krypterings-eller dekrypterings processen har slutförts.

När en klient skapar eller uppdaterar en blob med hjälp av en kundtillhandahållen nyckel, måste efterföljande Läs-och skriv förfrågningar för denna BLOB också tillhandahålla nyckeln. Om nyckeln inte anges på en begäran för en blob som redan har krypterats med en kundanged nyckel, Miss lyckas begäran med felkod 409 (konflikt).

Om klient programmet skickar en krypterings nyckel på begäran och lagrings kontot också krypteras med hjälp av en Microsoft-hanterad nyckel eller en kundhanterad nyckel, använder Azure Storage nyckeln som anges i begäran om kryptering och dekryptering.

För att skicka krypterings nyckeln som en del av begäran måste en klient upprätta en säker anslutning till Azure Storage med hjälp av HTTPS.

Varje Blob-ögonblicksbild kan ha en egen krypterings nyckel.

### <a name="request-headers-for-specifying-customer-provided-keys"></a>Begärandehuvuden för att ange kundtillhandahållna nycklar

För REST-anrop kan klienterna använda följande rubriker för att på ett säkert sätt skicka krypterings nyckel information till en begäran till Blob Storage:

|Begärandehuvud | Beskrivning |
|---------------|-------------|
|`x-ms-encryption-key` |Krävs för både Skriv-och Läs begär Anden. Ett base64-kodat AES-256-krypterings nyckel värde. |
|`x-ms-encryption-key-sha256`| Krävs för både Skriv-och Läs begär Anden. Den base64-kodade SHA256 för krypterings nyckeln. |
|`x-ms-encryption-algorithm` | Krävs för Skriv begär Anden, valfritt för Läs begär Anden. Anger algoritmen som ska användas vid kryptering av data med den aktuella nyckeln. Måste vara AES256. |

Det är valfritt att ange krypterings nycklar för begäran. Men om du anger ett av de sidhuvuden som anges ovan för en Skriv åtgärd måste du ange alla.

### <a name="blob-storage-operations-supporting-customer-provided-keys"></a>Blob Storage-åtgärder som stöder kundspecifika nycklar

Följande Blob Storage-åtgärder stöder sändning av kundtillhandahållna krypterings nycklar på en begäran:

- [Lägg till BLOB](/rest/api/storageservices/put-blob)
- [Lista över blockerade](/rest/api/storageservices/put-block-list)
- [Spärra block](/rest/api/storageservices/put-block)
- [Skicka block från URL](/rest/api/storageservices/put-block-from-url)
- [Placerings sida](/rest/api/storageservices/put-page)
- [Lägg till sida från URL](/rest/api/storageservices/put-page-from-url)
- [Lägg till block](/rest/api/storageservices/append-block)
- [Ange BLOB-egenskaper](/rest/api/storageservices/set-blob-properties)
- [Ange BLOB-metadata](/rest/api/storageservices/set-blob-metadata)
- [Hämta BLOB](/rest/api/storageservices/get-blob)
- [Hämta BLOB-egenskaper](/rest/api/storageservices/get-blob-properties)
- [Hämta BLOB-metadata](/rest/api/storageservices/get-blob-metadata)
- [Ögonblicks bilds-BLOB](/rest/api/storageservices/snapshot-blob)

### <a name="rotate-customer-provided-keys"></a>Rotera kundtillhandahållna nycklar

Om du vill rotera en krypterings nyckel som skickats på begäran laddar du ned blobben och laddar upp den igen med den nya krypterings nyckeln.

> [!IMPORTANT]
> Azure Portal kan inte användas för att läsa från eller skriva till en behållare eller BLOB som är krypterad med en nyckel som anges i begäran.
>
> Se till att skydda krypterings nyckeln som du anger på en begäran till Blob Storage i en säker nyckel lagring som Azure Key Vault. Om du försöker utföra en Skriv åtgärd på en behållare eller BLOB utan krypterings nyckeln, Miss lyckas åtgärden och du kommer att förlora åtkomsten till objektet.

### <a name="example-use-a-customer-provided-key-to-upload-a-blob-in-net"></a>Exempel: Använd en kundanged nyckel för att ladda upp en BLOB i .NET

I följande exempel skapas en kundspecifik nyckel som använder den nyckeln för att ladda upp en blob. Koden laddar upp ett block och genomför sedan blockeringslistan för att skriva blobben till Azure Storage. Nyckeln finns på [BlobRequestOptions](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions) -objektet genom att ange egenskapen [CustomerProvidedKey](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.customerprovidedkey) .

Nyckeln skapas med klassen [AesCryptoServiceProvider](/dotnet/api/system.security.cryptography.aescryptoserviceprovider) . Om du vill skapa en instans av den här klassen i koden lägger du till en `using`-instruktion som refererar till namn området `System.Security.Cryptography`:

```csharp
public static void UploadBlobWithClientKey(CloudBlobContainer container)
{
    // Create a new key using the Advanced Encryption Standard (AES) algorithm.
    AesCryptoServiceProvider keyAes = new AesCryptoServiceProvider();

    // Specify the key as an option on the request.
    BlobCustomerProvidedKey customerProvidedKey = new BlobCustomerProvidedKey(keyAes.Key);
    var options = new BlobRequestOptions
    {
        CustomerProvidedKey = customerProvidedKey
    };

    string blobName = "sample-blob-" + Guid.NewGuid();
    CloudBlockBlob blockBlob = container.GetBlockBlobReference(blobName);

    try
    {
        // Create an array of random bytes.
        byte[] buffer = new byte[1024];
        Random rnd = new Random();
        rnd.NextBytes(buffer);

        using (MemoryStream sourceStream = new MemoryStream(buffer))
        {
            // Write the array of random bytes to a block.
            int blockNumber = 1;
            string blockId = Convert.ToBase64String(Encoding.ASCII.GetBytes(string.Format("BlockId{0}",
                blockNumber.ToString("0000000"))));

            // Write the block to Azure Storage.
            blockBlob.PutBlock(blockId, sourceStream, null, null, options, null);

            // Commit the block list to write the blob.
            blockBlob.PutBlockList(new List<string>() { blockId }, null, options, null);
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

## <a name="azure-storage-encryption-versus-disk-encryption"></a>Azure Storage kryptering jämfört med disk kryptering

Med Azure Storage kryptering krypteras alla Azure Storage-konton och de resurser som de innehåller, inklusive de sid-blobbar som backar upp Azure Virtual Machine-diskar. Dessutom kan diskar för virtuella Azure-datorer krypteras med [Azure Disk Encryption](../../security/azure-security-disk-encryption-overview.md). Azure Disk Encryption använder branschstandardiserade [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) på Windows och [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) i Linux för att tillhandahålla operativ systembaserade krypterings lösningar som är integrerade med Azure Key Vault.

## <a name="next-steps"></a>Nästa steg

- [Vad är Azure Key Vault?](../../key-vault/key-vault-overview.md)
- [Konfigurera kundhanterade nycklar för Azure Storage-kryptering från Azure-portalen](storage-encryption-keys-portal.md)
- [Konfigurera kundhanterade nycklar för Azure Storage-kryptering från PowerShell](storage-encryption-keys-powershell.md)
- [Konfigurera kundhanterade nycklar för Azure Storage-kryptering från Azure CLI](storage-encryption-keys-cli.md)
