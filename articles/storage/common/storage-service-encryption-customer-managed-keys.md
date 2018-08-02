---
title: Azure Storage Service Encryption använder Kundhanterade nycklar i Azure Key Vault | Microsoft Docs
description: Funktionen Azure Storage Service Encryption för att kryptera Azure Blob storage, Azure Files, Azure Queue storage och Azure Table storage på serversidan vid lagring av data och dekryptera det vid hämtning av data med Kundhanterade nycklar.
services: storage
author: lakasa
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 08/01/2018
ms.author: lakasa
ms.openlocfilehash: b92a486ea8dfc148cd10b905f90a0e871602cc61
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39414940"
---
# <a name="storage-service-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Kryptering av lagringstjänst med Kundhanterade nycklar i Azure Key Vault
Microsoft Azure är förbundet till hjälper dig att skydda och skydda dina data för att uppfylla organisationens säkerhets- och efterlevnadsbestämmelser. Ett sätt att Azure storage-plattformen skyddar dina data är via Storage Service Encryption (SSE), som krypterar dina data vid skrivning till lagring och dekrypterar data vid hämtning av den. Kryptering och dekryptering är automatisk, transparent och använder 256-bitars [AES-kryptering](https://wikipedia.org/wiki/Advanced_Encryption_Standard), en av de starkaste blockchiffer som finns.

Du kan använda Microsoft-hanterade krypteringsnycklarna med SSE eller du kan använda dina egna krypteringsnycklar. Den här artikeln beskriver hur du använder egna krypteringsnycklar. Mer information om hur du använder Microsoft-hanterade nycklar eller om SSE i allmänhet finns i [Storage Service Encryption för vilande data](storage-service-encryption.md).

SSE för Azure Blob storage och Azure Files är integrerat med Azure Key Vault, så att du kan använda ett nyckelvalv för att hantera dina krypteringsnycklar. Du kan skapa egna krypteringsnycklar och lagra dem i ett nyckelvalv, eller du kan använda Azure Key Vault-API: er för att generera krypteringsnycklar. Med Azure Key Vault kan du hantera och kontrollera dina nycklar och även granska din nyckelanvändning.

> [!Note]  
> Kryptering av lagringstjänst är inte tillgänglig för [Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md). Vi rekommenderar att du använder kryptering på OS-nivå, till exempel [Azure Disk Encryption](../../security/azure-security-disk-encryption-overview.md), som använder vanliga [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) på Windows och [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) på Linux för att tillhandahålla kryptering är integrerad med KeyVault.

Varför skapa dina egna nycklar? Anpassade nycklar ger mer flexibilitet, så att du kan skapa, rotera, inaktivera och definiera åtkomstkontroller. Anpassade nycklar kan du granska de krypteringsnycklar som används för att skydda dina data.

## <a name="get-started-with-customer-managed-keys"></a>Kom igång med Kundhanterade nycklar
Om du vill använda Kundhanterade nycklar med SSE, du kan antingen skapa ett nytt nyckelvalv och nyckel eller du kan använda ett befintligt nyckelvalv och nyckel. Storage-konto och nyckelvalvet måste vara i samma region, men de kan vara i olika prenumerationer. 

### <a name="step-1-create-a-storage-account"></a>Steg 1: Skapa ett lagringskonto
Skapa först ett lagringskonto om du inte redan har en. Mer information finns i [skapa ett nytt lagringskonto](storage-quickstart-create-account.md).

### <a name="step-2-enable-sse-for-blob-and-file-storage"></a>Steg 2: Aktivera SSE för Blob- och lagring
Om du vill aktivera SSE med Kundhanterade nycklar, måste två nyckelskydd funktioner, mjuk borttagning och rensa inte, vara aktiverat. De här inställningarna Kontrollera nycklarna inte får vara råkar eller avsiktligt borttagna. Den högsta bevarandeperioden av nycklarna har angetts till 90 dagar, skydda användarna mot skadliga aktörer eller utpressningstrojan-attacker.

Om du vill aktivera programmässigt Kundhanterade nycklar för SSE, kan du använda den [Azure Storage Resource Provider REST API](https://docs.microsoft.com/rest/api/storagerp), [klientbibliotek för Storage Resource Provider för .NET](https://docs.microsoft.com/dotnet/api), [ Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview), eller [Azure CLI](https://docs.microsoft.com/azure/storage/storage-azure-cli).

Om du vill använda Kundhanterade nycklar med SSE, måste du tilldela en identitet för storage-konto till lagringskontot. Du kan ange identiteten genom att köra följande PowerShell-kommando:

```powershell
Set-AzureRmStorageAccount -ResourceGroupName \$resourceGroup -Name \$accountName -AssignIdentity
```

Du kan aktivera mjuk borttagning och gör inte rensa genom att köra följande PowerShell-kommandon:

```powershell
($resource = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -VaultName
$vaultName).ResourceId).Properties | Add-Member -MemberType NoteProperty -Name
enableSoftDelete -Value 'True'

Set-AzureRmResource -resourceid $resource.ResourceId -Properties
$resource.Properties

($resource = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -VaultName
$vaultName).ResourceId).Properties | Add-Member -MemberType NoteProperty -Name
enablePurgeProtection -Value 'True'

Set-AzureRmResource -resourceid $resource.ResourceId -Properties
$resource.Properties
```

### <a name="step-3-enable-encryption-with-customer-managed-keys"></a>Steg 3: Aktivera kryptering med Kundhanterade nycklar
Som standard använder SSE Microsoft-hanterade nycklar. Du kan aktivera SSE med Kundhanterade nycklar för storage-konto med den [Azure-portalen](https://portal.azure.com/). På den **inställningar** bladet för storage-konto klickar du på **kryptering**. Välj den **använda din egen nyckel** alternativ, enligt följande bild.

![Portalen skärmbild med krypteringsalternativet](./media/storage-service-encryption-customer-managed-keys/ssecmk1.png)

### <a name="step-4-select-your-key"></a>Steg 4: Välj din nyckel
Du kan ange en nyckel som en URI eller genom att välja nyckeln från key vault.

#### <a name="specify-a-key-as-a-uri"></a>Ange en nyckel som en URI
Följ dessa steg om du vill ange en nyckel från en URI:

1. Välj den **RETUR-tangenten URI** alternativet.  
2. I den **Key URI: N** fältet Ange URI: N.

    ![Portalen skärmbild kryptering med Ange nyckel-uri-alternativet](./media/storage-service-encryption-customer-managed-keys/ssecmk2.png)


#### <a name="specify-a-key-from-a-key-vault"></a>Ange en nyckel från key vault 
Följ dessa steg om du vill ange en nyckel från key vault:

1. Välj den **Välj från Key Vault** alternativet.  
2. Välj det nyckelvalv som innehåller den nyckel som du vill använda.
3. Välj nyckeln från nyckelvalvet.

    ![Portalen skärmbild som visar krypteringar använda din egen nyckel alternativet](./media/storage-service-encryption-customer-managed-keys/ssecmk3.png)

Om lagringskontot inte har åtkomst till nyckelvalvet, kan du köra Azure PowerShell-kommando som visas i följande bild för att bevilja åtkomst.

![Portalen skärmbild som visar åtkomst nekades för key vault](./media/storage-service-encryption-customer-managed-keys/ssecmk4.png)

Du kan också ge åtkomst via Azure portal genom att gå till Azure Key Vault i Azure-portalen och bevilja åtkomst till lagringskontot.


Du kan associera nyckeln ovan med ett befintligt lagringskonto med hjälp av följande PowerShell-kommandon:
```powershell
$storageAccount = Get-AzureRmStorageAccount -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount"
$keyVault = Get-AzureRmKeyVault -VaultName "mykeyvault"
$key = Get-AzureKeyVaultKey -VaultName $keyVault.VaultName -Name "keytoencrypt"
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVault.VaultName -ObjectId $storageAccount.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get
Set-AzureRmStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName -AccountName $storageAccount.StorageAccountName -EnableEncryptionService "Blob" -KeyvaultEncryption -KeyName $key.Name -KeyVersion $key.Version -KeyVaultUri $keyVault.VaultUri
```

### <a name="step-5-copy-data-to-storage-account"></a>Steg 5: Kopieringsdata till storage-konto
Att överföra data till det nya kontot så att de är krypterade. Mer information finns i [vanliga frågor och svar för kryptering av lagringstjänst](storage-service-encryption.md#faq-for-storage-service-encryption).

### <a name="step-6-query-the-status-of-the-encrypted-data"></a>Steg 6: Fråga status för krypterade data
Fråga status för krypterade data.

## <a name="faq-for-sse-with-customer-managed-keys"></a>Vanliga frågor och svar för SSE med kunden hanterade nycklar
**Jag använder Premium-lagring. kan jag använda Kundhanterade nycklar med SSE?**  
Ja, SSE med Microsoft-hanterad och Kundhanterade nycklar stöds på både Standard-lagring och premiumlagring.

**Kan jag skapa nya storage-konton med SSE med Kundhanterade nycklar aktiverat med Azure PowerShell och Azure CLI?**  
Ja.

**Hur mycket kostar Azure Storage om jag använder Kundhanterade nycklar med SSE?**  
Det finns en kostnad för att använda Azure Key Vault. Mer information på [priser för Key Vault](https://azure.microsoft.com/pricing/details/key-vault/). Det finns ingen extra kostnad för SSE, som är aktiverad för alla lagringskonton.

**Är kryptering av lagringstjänst tillgängligt på Azure Managed Disks?**  
Nej, kryptering av lagringstjänst är inte tillgänglig för [Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md). Vi rekommenderar att du använder kryptering på OS-nivå, till exempel [Azure Disk Encryption](../../security/azure-security-disk-encryption-overview.md), som använder vanliga [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) på Windows och [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) på Linux för att tillhandahålla kryptering är integrerad med KeyVault.

**Hur skiljer sig kryptering av lagringstjänst från Azure Disk Encryption?**  
Azure Disk Encryption innehåller integrering mellan OS-baserade lösningar, till exempel BitLocker och DM-Crypt och Azure KeyVault. Kryptering av lagringstjänst tillhandahåller kryptering internt i Azure storage-plattformen lager, under den virtuella datorn.

**Kan jag återkalla åtkomst till krypteringsnycklarna?**
Ja, du kan återkalla behörigheten när som helst. Det finns flera sätt att återkalla åtkomst till dina nycklar. Referera till [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) och [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault) för mer information. Återkalla åtkomst blockerar effektivt åtkomst till alla blobar i lagringskontot som konto krypteringsnyckeln är otillgänglig av Azure Storage.

**Kan jag skapa ett lagringskonto och nyckel i annan region?**  
Nej, storage-konto och Azure Key Vault och nyckel måste finnas i samma region.

**Kan jag aktivera Kundhanterade nycklar för SSE när du skapar storage-konto?**  
Nej. När du först skapa lagringskontot finns Microsoft-hanterade nycklar för SSE. Om du vill använda Kundhanterade nycklar måste du uppdatera egenskaper för lagringskontot. Du kan använda REST eller någon av storage-klientbibliotek för att programmässigt uppdatera ditt lagringskonto eller uppdatera de egenskaper för lagringskontot i Azure-portalen när du har skapat kontot.

**Kan jag inaktivera kryptering när du använder Kundhanterade nycklar med SSE?**  
Nej, du kan inte inaktivera kryptering. Kryptering är aktiverat som standard för Azure Blob storage, Azure Files, Azure-kö och Azure Table storage. Du kan också växla från att använda Microsoft-hanterade nycklar använder Kundhanterade nycklar och vice versa.

**SSE är aktiverat när jag skapar ett nytt lagringskonto?**  
SSE är aktiverat för alla lagringskonton och Azure Blob storage, Azure Files, Azure Queue storage och Azure Table storage.

**Jag kan inte aktivera SSE med Kundhanterade nycklar på mitt lagringskonto.**  
Är det en Azure Resource Manager-lagringskonto? Klassiska lagringskonton stöds inte med Kundhanterade nycklar. SSE med Kundhanterade nycklar kan bara aktiveras om Resource Manager-lagringskonton.

**Vad är Mjuk borttagning och gör inte rensa? Måste jag aktivera den här inställningen för att använda SSE med Kundhanterade nycklar?**  
Borttagning mjuk och gör inte rensa måste aktiveras att använda SSE med Kundhanterade nycklar. De här inställningarna se till att din nyckel inte är råkar eller avsiktligt borttagna. Den högsta bevarandeperioden av nycklarna har angetts till 90 dagar, skydda användarna mot skadliga aktörer och utpressningstrojan-attacker. Den här inställningen kan inte inaktiveras.

**Tillåts SSE med Kundhanterade nycklar endast i vissa områden?**  
SSE med Kundhanterade nycklar finns i alla regioner för Azure Blob storage och Azure Files.

**Hur kontaktar jag någon om jag har problem eller vill ge feedback?**  
Kontakta [ ssediscussions@microsoft.com ](mailto:ssediscussions@microsoft.com) om eventuella problem som rör kryptering av lagringstjänst.

## <a name="next-steps"></a>Nästa steg
- Mer information om den omfattande uppsättningen security funktioner som hjälper utvecklare att skapa säkra program, finns i den [Lagringssäkerhetsguide](storage-security-guide.md).
- Mer information om Azure Key Vault finns i [vad är Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)?
- Komma igång med Azure Key Vault, finns i [komma igång med Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-get-started).