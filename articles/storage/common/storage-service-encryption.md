---
title: Azure Storage kryptering för vilande data
description: Azure Storage skyddar dina data genom att automatiskt kryptera dem innan du sparar dem i molnet. Du kan förlita dig på Microsoft-hanterade nycklar för kryptering av ditt lagrings konto, eller så kan du hantera kryptering med dina egna nycklar.
services: storage
author: tamram
ms.service: storage
ms.date: 01/03/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 35a5bfd582c9717b062d42d86e7581029861fd0c
ms.sourcegitcommit: 2c59a05cb3975bede8134bc23e27db5e1f4eaa45
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/05/2020
ms.locfileid: "75665438"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>Azure Storage kryptering för vilande data

Azure Storage krypterar dina data automatiskt när de sparas i molnet. Azure Storage kryptering skyddar dina data och hjälper dig att uppfylla organisationens säkerhets-och efterlevnads åtaganden.

## <a name="about-azure-storage-encryption"></a>Om Azure Storage kryptering

Data i Azure Storage krypteras och dekrypteras transparent med 256-bitars AES- [kryptering](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), en av de starkaste block chiffer som är tillgängliga och är FIPS 140-2-kompatibel. Azure Storage kryptering liknar BitLocker-kryptering på Windows.

Azure Storage kryptering har Aktiver ATS för alla nya lagrings konton, inklusive både Resource Manager och klassiska lagrings konton. Azure Storage kryptering kan inte inaktive ras. Eftersom dina data är säkra som standard behöver du inte ändra koden eller programmen för att dra nytta av Azure Storage kryptering.

Lagrings konton är krypterade oavsett prestanda nivå (standard eller Premium) eller distributions modell (Azure Resource Manager eller klassisk). Alla Azure Storage alternativ för redundans stöder kryptering och alla kopior av ett lagrings konto krypteras. Alla Azure Storage-resurser krypteras, inklusive blobbar, diskar, filer, köer och tabeller. Alla metadata för objekt krypteras också.

Kryptering påverkar inte Azure Storage prestanda. Det kostar inget extra att Azure Storage kryptering.

Varje Block-Blob, Lägg till BLOB eller sid-blob som skrevs till Azure Storage efter den 20 oktober 2017 är krypterad. Blobbar som skapats före detta datum fortsätter att krypteras med en bakgrunds process. Om du vill framtvinga kryptering av en blob som skapades före den 20 oktober 2017 kan du skriva om blobben. Information om hur du kontrollerar krypterings statusen för en BLOB finns i [kontrol lera krypterings statusen för en BLOB](../blobs/storage-blob-encryption-status.md).

Mer information om de kryptografiska modulerna underliggande Azure Storage kryptering finns i [Cryptography-API: nästa generation](https://docs.microsoft.com/windows/desktop/seccng/cng-portal).

## <a name="about-encryption-key-management"></a>Om hantering av krypterings nyckel

Du kan förlita dig på Microsoft-hanterade nycklar för kryptering av ditt lagrings konto, eller så kan du hantera kryptering med dina egna nycklar. Om du väljer att hantera kryptering med dina egna nycklar har du två alternativ:

- Du kan ange en *kundhanterad nyckel* med Azure Key Vault som ska användas för att kryptera och dekryptera data i Blob Storage och i Azure Files.
- Du kan ange en *anpassad nyckel* för Blob Storage-åtgärder. En klient som gör en Läs-eller skrivbegäran mot Blob Storage kan inkludera en krypterings nyckel på begäran om detaljerad kontroll över hur BLOB-data krypteras och dekrypteras.

I följande tabell jämförs nyckel hanterings alternativ för Azure Storage kryptering.

|                                        |    Microsoft-hanterade nycklar                             |    Kundhanterade nycklar                                                                                                                        |    Kund-tillhandahållna nycklar                                                          |
|----------------------------------------|-------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------|
|    Kryptering/dekryptering    |    Azure                                              |    Azure                                                                                                                                        |    Azure                                                                         |
|    Azure Storage tjänster som stöds    |    Alla                                                |    Blob Storage, Azure Files                                                                                                               |    Blobb-lagring                                                                  |
|    Nyckel lagring                         |    Microsoft nyckel lager    |    Azure Key Vault                                                                                                                              |    Azure Key Vault eller något annat nyckel Arkiv                                                                 |
|    Största ansvar för nyckel rotation         |    Microsoft                                          |    Kund                                                                                                                                     |    Kund                                                                      |
|    Nyckelanvändning                           |    Microsoft                                          |    Azure Portal, Provider för lagrings resurs REST API, Azure Storage hanterings bibliotek, PowerShell, CLI        |    Azure Storage REST API (Blob Storage), Azure Storage klient bibliotek    |
|    Nyckel åtkomst                          |    Endast Microsoft                                     |    Microsoft, kund                                                                                                                    |    Endast kund                                                                 |

I följande avsnitt beskrivs de olika alternativen för nyckel hantering i större detalj.

## <a name="microsoft-managed-keys"></a>Microsoft-hanterade nycklar

Som standard använder ditt lagrings konto Microsoft-hanterade krypterings nycklar. Du kan se krypterings inställningarna för ditt lagrings konto i avsnittet **kryptering** i [Azure Portal](https://portal.azure.com), som du ser i följande bild.

![Visa kontot krypterat med Microsoft-hanterade nycklar](media/storage-service-encryption/encryption-microsoft-managed-keys.png)

## <a name="customer-managed-keys-with-azure-key-vault"></a>Kundhanterade nycklar med Azure Key Vault

Du kan hantera Azure Storage kryptering på lagrings kontots nivå med dina egna nycklar. När du anger en kundhanterad nyckel på lagrings kontots nivå används nyckeln för att skydda och kontrol lera åtkomsten till rot krypterings nyckeln för lagrings kontot som i sin tur används för att kryptera och dekryptera alla blob-och fildata. Kundhanterade nycklar ger större flexibilitet för att skapa, rotera, inaktivera och återkalla åtkomst kontroller. Du kan också granska de krypterings nycklar som används för att skydda dina data.

Du måste använda Azure Key Vault för att lagra dina Kundhanterade nycklar. Du kan antingen skapa egna nycklar och lagra dem i ett nyckel valv, eller så kan du använda Azure Key Vault API: er för att generera nycklar. Lagrings kontot och nyckel valvet måste finnas i samma region, men de kan finnas i olika prenumerationer. Mer information om Azure Key Vault finns i [Azure Key Vault?](../../key-vault/key-vault-overview.md).

Det här diagrammet visar hur Azure Storage använder Azure Active Directory och Azure Key Vault för att göra förfrågningar med hjälp av kundhanterad nyckel:

![Diagram över hur Kundhanterade nycklar fungerar i Azure Storage](media/storage-service-encryption/encryption-customer-managed-keys-diagram.png)

I följande lista beskrivs de numrerade stegen i diagrammet:

1. En Azure Key Vault administratör beviljar behörigheter till krypterings nycklar till den hanterade identitet som är kopplad till lagrings kontot.
2. En Azure Storage administratör konfigurerar kryptering med en kundhanterad nyckel för lagrings kontot.
3. Azure Storage använder den hanterade identitet som är kopplad till lagrings kontot för att autentisera åtkomst till Azure Key Vault via Azure Active Directory.
4. Azure Storage omsluter konto krypterings nyckeln med kund nyckeln i Azure Key Vault.
5. Vid Läs-och skriv åtgärder skickar Azure Storage begär anden till Azure Key Vault för att omsluta och packa upp konto krypterings nyckeln för att utföra kryptering och dekryptering.

### <a name="enable-customer-managed-keys-for-a-storage-account"></a>Aktivera Kundhanterade nycklar för ett lagrings konto

När du aktiverar kryptering med Kundhanterade nycklar för ett lagrings konto kommer Azure Storage att omsluta konto krypterings nyckeln med den Kundhanterade nyckeln i det associerade nyckel valvet. Att aktivera Kundhanterade nycklar påverkar inte prestanda och kontot krypteras med den nya nyckeln omedelbart utan tids fördröjning.

Ett nytt lagrings konto är alltid krypterat med Microsoft-hanterade nycklar. Det går inte att aktivera Kundhanterade nycklar vid den tidpunkt då kontot skapas. Kundhanterade nycklar lagras i Azure Key Vault och nyckel valvet måste tillhandahållas med åtkomst principer som ger nyckel behörigheter till den hanterade identitet som är kopplad till lagrings kontot. Den hanterade identiteten är bara tillgänglig när lagrings kontot har skapats.

När du ändrar den nyckel som används för Azure Storage kryptering genom att aktivera eller inaktivera Kundhanterade nycklar, uppdatera nyckel versionen eller ange en annan nyckel ändras krypteringen av rot nyckeln, men data i ditt Azure Storage-konto måste krypteras på nytt.

Information om hur du använder Kundhanterade nycklar med Azure Key Vault för Azure Storage kryptering finns i någon av följande artiklar:

- [Konfigurera Kundhanterade nycklar med Key Vault för Azure Storage kryptering från Azure Portal](storage-encryption-keys-portal.md)
- [Konfigurera Kundhanterade nycklar med Key Vault för Azure Storage kryptering från PowerShell](storage-encryption-keys-powershell.md)
- [Konfigurera Kundhanterade nycklar med Key Vault för Azure Storage kryptering från Azure CLI](storage-encryption-keys-cli.md)

> [!IMPORTANT]
> Kundhanterade nycklar är beroende av hanterade identiteter för Azure-resurser, en funktion i Azure Active Directory (Azure AD). När du konfigurerar Kundhanterade nycklar i Azure Portal tilldelas en hanterad identitet automatiskt till ditt lagrings konto under försättsblad. Om du senare flyttar prenumerationen, resurs gruppen eller lagrings kontot från en Azure AD-katalog till en annan överförs inte den hanterade identitet som är kopplad till lagrings kontot till den nya klienten, så Kundhanterade nycklar kanske inte längre fungerar. Mer information finns i **överföra en prenumeration mellan Azure AD-kataloger** i [vanliga frågor och svar och kända problem med hanterade identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Lagra Kundhanterade nycklar i Azure Key Vault

Om du vill aktivera Kundhanterade nycklar på ett lagrings konto måste du använda en Azure Key Vault för att lagra dina nycklar. Du måste aktivera både den **mjuka borttagningen** och **Rensa inte** egenskaperna i nyckel valvet.

Endast RSA-nycklar med storleken 2048 stöds med Azure Storage kryptering. Mer information om nycklar finns **Key Vault nycklar** i [om Azure Key Vault nycklar, hemligheter och certifikat](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys).

Nyckel valvet måste finnas i samma prenumeration som lagrings kontot. Azure Storage använder hanterade identiteter för Azure-resurser för att autentisera till nyckel valvet för kryptering och dekryptering. Hanterade identiteter stöder för närvarande inte scenarier mellan kataloger.

### <a name="rotate-customer-managed-keys"></a>Rotera Kundhanterade nycklar

Du kan rotera en kundhanterad nyckel i Azure Key Vault enligt efterlevnadsprinciper. När nyckeln roteras måste du uppdatera lagrings kontot för att använda den nya nyckel-URI: n. Information om hur du uppdaterar lagrings kontot för att använda en ny version av nyckeln i Azure Portal finns i avsnittet **Uppdatera nyckel versionen** i [Konfigurera kund hanterade nycklar för Azure Storage med hjälp av Azure Portal](storage-encryption-keys-portal.md).

Rotation av nyckeln utlöser inte Omkryptering av data i lagrings kontot. Det krävs ingen ytterligare åtgärd från användaren.

### <a name="revoke-access-to-customer-managed-keys"></a>Återkalla åtkomst till Kundhanterade nycklar

Om du vill återkalla åtkomsten till Kundhanterade nycklar använder du PowerShell eller Azure CLI. Mer information finns i [Azure Key Vault PowerShell](/powershell/module/az.keyvault//) eller [Azure Key Vault CLI](/cli/azure/keyvault). Att återkalla åtkomsten på ett effektivt sätt blockerar åtkomsten till alla data i lagrings kontot, eftersom krypterings nyckeln inte är tillgänglig via Azure Storage.

### <a name="customer-managed-keys-for-azure-managed-disks-preview"></a>Kundhanterade nycklar för Azure Managed disks (för hands version)

Kundhanterade nycklar är också tillgängliga för hantering av kryptering av Azure Managed disks (för hands version). Kundhanterade nycklar fungerar annorlunda för hanterade diskar än för Azure Storage resurser. Mer information finns i [kryptering på Server sidan av Azure Managed disks](../../virtual-machines/windows/disk-encryption.md) for Windows eller [Server Side Encryption på Azure Managed disks](../../virtual-machines/linux/disk-encryption.md) for Linux.

## <a name="customer-provided-keys-preview"></a>Kundspecifika nycklar (förhands granskning)

Klienter som begär förfrågningar mot Azure Blob Storage har möjlighet att tillhandahålla en krypterings nyckel på en enskild begäran. Inklusive krypterings nyckeln på begäran ger detaljerad kontroll över krypterings inställningarna för Blob Storage-åtgärder. Kundspecifika nycklar (för hands version) kan lagras i Azure Key Vault eller i en annan nyckel lagrings plats.

Ett exempel som visar hur du anger en kundbaserad nyckel på en begäran till Blob Storage finns i [Ange en kundbaserad nyckel på en begäran till Blob Storage med .net](../blobs/storage-blob-customer-provided-key.md). 

### <a name="encrypting-read-and-write-operations"></a>Kryptera Läs-och skriv åtgärder

När ett klient program tillhandahåller en krypterings nyckel på begäran utför Azure Storage kryptering och dekryptering transparent vid läsning och skrivning av BLOB-data. Azure Storage skriver en SHA-256-hash av krypterings nyckeln bredvid blobens innehåll. Hashen används för att kontrol lera att alla efterföljande åtgärder mot blobben använder samma krypterings nyckel. 

Azure Storage lagrar eller hanterar inte den krypterings nyckel som klienten skickar med begäran. Nyckeln ignoreras säkert så snart krypterings-eller dekrypterings processen har slutförts.

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

- [Placera blob](/rest/api/storageservices/put-blob)
- [Lista över blockerade](/rest/api/storageservices/put-block-list)
- [Spärra block](/rest/api/storageservices/put-block)
- [Skicka block från URL](/rest/api/storageservices/put-block-from-url)
- [Placerings sida](/rest/api/storageservices/put-page)
- [Lägg till sida från URL](/rest/api/storageservices/put-page-from-url)
- [Lägg till block](/rest/api/storageservices/append-block)
- [Ange BLOB-egenskaper](/rest/api/storageservices/set-blob-properties)
- [Ange BLOB-metadata](/rest/api/storageservices/set-blob-metadata)
- [Hämta BLOB](/rest/api/storageservices/get-blob)
- [Hämta blobegenskaper](/rest/api/storageservices/get-blob-properties)
- [Hämta BLOB-metadata](/rest/api/storageservices/get-blob-metadata)
- [Ta ögonblicksbild av blob](/rest/api/storageservices/snapshot-blob)

### <a name="rotate-customer-provided-keys"></a>Rotera kundtillhandahållna nycklar

Om du vill rotera en krypterings nyckel som skickats på begäran laddar du ned blobben och laddar upp den igen med den nya krypterings nyckeln.

> [!IMPORTANT]
> Azure Portal kan inte användas för att läsa från eller skriva till en behållare eller BLOB som är krypterad med en nyckel som anges i begäran.
>
> Se till att skydda krypterings nyckeln som du anger på en begäran till Blob Storage i en säker nyckel lagring som Azure Key Vault. Om du försöker utföra en Skriv åtgärd på en behållare eller BLOB utan krypterings nyckeln, Miss lyckas åtgärden och du kommer att förlora åtkomsten till objektet.

## <a name="azure-storage-encryption-versus-disk-encryption"></a>Azure Storage kryptering jämfört med disk kryptering

Azure Storage kryptering krypterar sidans blobbar som backar upp virtuella Azure-dator diskar. Dessutom kan alla virtuella Azure-datorer, inklusive lokala temporära diskar, eventuellt krypteras med [Azure Disk Encryption](../../security/azure-security-disk-encryption-overview.md). Azure Disk Encryption använder branschstandardiserade [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) på Windows och [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) i Linux för att tillhandahålla operativ systembaserade krypterings lösningar som är integrerade med Azure Key Vault.

## <a name="next-steps"></a>Nästa steg

- [Vad är Azure Key Vault?](../../key-vault/key-vault-overview.md)
- [Konfigurera kundhanterade nycklar för Azure Storage-kryptering från Azure-portalen](storage-encryption-keys-portal.md)
- [Konfigurera kundhanterade nycklar för Azure Storage-kryptering från PowerShell](storage-encryption-keys-powershell.md)
- [Konfigurera kundhanterade nycklar för Azure Storage-kryptering från Azure CLI](storage-encryption-keys-cli.md)
