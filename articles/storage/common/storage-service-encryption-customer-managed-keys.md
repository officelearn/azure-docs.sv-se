---
title: Azure Storage Service-kryptering med kundhanterad nycklar i Azure Key Vault | Microsoft Docs
description: "Funktionen Azure Storage Service-kryptering för att kryptera din Azure-Blobblagring på tjänstsidan när data lagrades och dekryptera den vid hämtning av data med kundhanterad nycklar."
services: storage
author: lakasa
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 03/07/2018
ms.author: lakasa
ms.openlocfilehash: b40858640d10e5661be420976520774bd50837cb
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="storage-service-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Lagringstjänstens kryptering med kundhanterad nycklar i Azure Key Vault

Microsoft Azure är hjälper dig att skydda och skydda dina data för att uppfylla din organisations säkerhet och efterlevnad åtaganden. Ett sätt att Azure Storage skyddar dina data är via Storage Service kryptering (SSE), som krypterar dina data när du skriver till lagring och dekrypterar data vid hämtning av den. Kryptering och dekryptering är automatisk, transparent och använder 256-bitars [AES-kryptering](https://wikipedia.org/wiki/Advanced_Encryption_Standard), en av de starkaste blocket chiffer tillgängliga.

Du kan använda Microsoft-hanterad krypteringsnycklarna med SSE eller du kan använda dina egna krypteringsnycklar. Den här artikeln beskriver hur du använder egna krypteringsnycklar. Mer information om hur du använder Microsoft-hanterad nycklar eller om SSE i allmänhet finns [Lagringstjänstens kryptering av vilande Data](storage-service-encryption.md).

SSE för Blob- och lagring är integrerad med Azure Key Vault så att du kan använda ett nyckelvalv för att hantera krypteringsnycklar. Du kan skapa egna krypteringsnycklar och lagrar dem i ett nyckelvalv eller du kan använda Azure Key Vault-API: er för att generera krypteringsnycklar. Med Azure Key Vault kan hantera och styra dina nycklar och också granska din nyckelanvändning.

Varför ska man skapa dina egna nycklar? Anpassade nycklar ger mer flexibilitet så att du kan skapa, rotera, inaktivera och definiera åtkomstkontroller. Anpassade nycklar kan du granska de krypteringsnycklar som används för att skydda dina data.

## <a name="get-started-with-customer-managed-keys"></a>Kom igång med kundhanterad nycklar

Du kan antingen skapa ett nytt nyckelvalv om du vill använda kundhanterad nycklar med SSE och nyckel eller du kan använda en befintlig nyckelvalvet och nyckel. Storage-konto och nyckelvalvet måste vara i samma region, men de kan ha olika prenumerationer. 

### <a name="step-1-create-a-storage-account"></a>Steg 1: Skapa ett lagringskonto

Först skapar du ett storage-konto om du inte redan har en. Mer information finns i [skapa ett nytt lagringskonto](storage-quickstart-create-account.md).

### <a name="step-2-enable-sse-for-blob-and-file-storage"></a>Steg 2: Aktivera SSE för Blob- och lagring

Om du vill aktivera SSE med kundhanterad nycklar måste två nyckelskydd funktioner, mjuk ta bort och rensa inte, vara aktiverat. Dessa inställningar Se till att nycklarna inte kan vara av misstag eller avsiktligt borttagna. Den högsta bevarandeperioden nycklar har angetts till 90 dagarna, skyddar användare mot skadliga aktörer eller en utpressningstrojan som attacker.

Om du vill aktivera programmässigt kundhanterad nycklar för SSE, kan du använda den [Azure Storage Resource Provider REST API](https://docs.microsoft.com/rest/api/storagerp), [Storage-klientbiblioteket för .NET](https://docs.microsoft.com/dotnet/api), [ Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview), eller [Azure CLI](https://docs.microsoft.com/azure/storage/storage-azure-cli).

Om du vill använda kundhanterad nycklar med SSE, måste du tilldela en identitet för storage-konto till lagringskontot. Du kan ange identiteten genom att köra följande PowerShell-kommando:

```powershell
Set-AzureRmStorageAccount -ResourceGroupName \$resourceGroup -Name \$accountName -AssignIdentity
```

Du kan aktivera ej permanent ta bort och gör inte rensa genom att köra följande PowerShell-kommandon:

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

### <a name="step-3-enable-encryption-with-customer-managed-keys"></a>Steg 3: Aktivera kryptering med kundhanterad nycklar

Som standard använder SSE Microsoft-hanterade nycklar. Du kan aktivera SSE med kundhanterad nycklar för storage-konto med den [Azure-portalen](https://portal.azure.com/). På den **inställningar** bladet för storage-konto klickar du på **kryptering**. Välj den **använda din egen nyckel** alternativ, som visas i följande bild.

![Skärmbild som visar Portal krypteringsalternativet](./media/storage-service-encryption-customer-managed-keys/ssecmk1.png)

### <a name="step-4-select-your-key"></a>Steg 4: Välj din nyckel

Du kan ange en nyckel som en URI, eller genom att välja nyckeln från ett nyckelvalv.

#### <a name="specify-a-key-as-a-uri"></a>Ange en nyckel som en URI

Följ dessa steg om du vill ange en nyckel från en URI:

1. Välj den **RETUR-tangenten URI** alternativet.  
2. I den **nyckel URI** ange URI: N.

    ![Portalen skärmbild som visar kryptering med viktiga uri-alternativ](./media/storage-service-encryption-customer-managed-keys/ssecmk2.png)

#### <a name="specify-a-key-from-a-key-vault"></a>Ange en nyckel från ett nyckelvalv 

Följ dessa steg om du vill ange en nyckel från ett nyckelvalv:

1. Välj den **Välj Key Vault** alternativet.  
2. Välj nyckelvalvet som innehåller den nyckel som du vill använda.
3. Välj nyckeln från nyckelvalvet.

    ![Portalen skärmbild som visar krypteringar använda din egen nyckel alternativet](./media/storage-service-encryption-customer-managed-keys/ssecmk3.png)

Du kan köra Azure PowerShell-kommandot som visas i följande bild för att bevilja åtkomst om lagringskontot inte har åtkomst till nyckelvalvet.

![Portalen skärmbild som visar åtkomst nekades för nyckelvalvet](./media/storage-service-encryption-customer-managed-keys/ssecmk4.png)

Du kan också ge åtkomst via Azure portal genom att gå till Azure Key Vault i Azure-portalen och bevilja åtkomst till lagringskontot.

### <a name="step-5-copy-data-to-storage-account"></a>Steg 5: Kopiera data till storage-konto

Om du vill överföra data till det nya kontot så att den är krypterad, se steg3 i [komma igång i Lagringstjänstens kryptering av vilande Data](storage-service-encryption.md#step-3-copy-data-to-storage-account).

### <a name="step-6-query-the-status-of-the-encrypted-data"></a>Steg 6: Fråga status för krypterade data

Om du vill fråga status för krypterade data, finns i steg 4 i [komma igång i Lagringstjänstens kryptering av vilande Data](storage-service-encryption.md#step-4-query-the-status-of-the-encrypted-data).

## <a name="faq-for-sse-with-customer-managed-keys"></a>Vanliga frågor om SSE med kunden hanterade nycklar

**F: Jag använder Premium-lagring. kan jag använda kundhanterad nycklar med SSE?**

S: Ja stöds SSE med Microsoft-hanterad och kundhanterad nycklar på både Standard lagrings- och Premium-lagring.

**F: kan jag skapa ny storage-konton med SSE med kundhanterad nycklar aktiverad med Azure PowerShell och Azure CLI?**

S: Ja.

**F: hur mycket kostar Azure Storage om jag använder kundhanterad nycklar med SSE?**

S: det kostar för att använda Azure Key Vault. Mer information finns på [Key Vault-priser](https://azure.microsoft.com/pricing/details/key-vault/). Det finns inga extra kostnad för SSE som är aktiverad för alla lagringskonton.

**F: kan jag återkalla åtkomst till krypteringsnycklarna?**

S: Ja, du kan återkalla åtkomst när som helst. Det finns flera sätt att återkalla åtkomst till dina nycklar. Referera till [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) och [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault) för mer information. Återkalla åtkomst blockerar effektivt åtkomst till alla BLOB storage-kontot eftersom kontot krypteringsnyckeln är otillgänglig genom Azure Storage.

**F: kan jag skapa ett lagringskonto och en nyckel i annan region?**

S: Nej, storage-konto och Azure Key Vault och nyckel måste vara i samma region.

**F: kan jag aktivera kundhanterad nycklar för SSE när lagringskontot?**

S: Nej. Microsoft-hanterad nycklar är tillgängliga för SSE när du först skapa lagringskontot. Om du vill använda kundhanterad nycklar, måste du uppdatera lagringskontoegenskaperna. Du kan använda REST- eller en lagringsklientbiblioteken programmässigt uppdatera ditt lagringskonto eller uppdatera lagringskontoegenskaperna med Azure-portalen när du har skapat kontot.

**F: kan jag inaktivera kryptering när du använder kundhanterad nycklar med SSE?**

A: du kan inte Nej, inaktivera kryptering. Kryptering är aktiverat som standard för alla tjänster – Blob, fil-, tabell- och kön lagring. Du kan om du vill växla från med hjälp av Microsoft-hanterad nycklar kundhanterad trycker och vice versa.

**F: är SSE aktiverad som standard när jag skapar ett nytt lagringskonto?**

S: SSE är aktiverad som standard för alla lagringskonton och för alla tjänster – Blob, fil-, tabell- och kön lagringsutrymmen.

**F: Jag kan inte aktivera SSE med kundhanterad nycklar för storage-konto.**

S: är det ett Azure Resource Manager storage-konto? Klassiska lagringskonton stöds inte med kundhanterad nycklar. SSE med kundhanterad nycklar kan endast aktiveras på lagringskonton för hanteraren för filserverresurser.

**F: Vad är ej permanent ta bort och gör inte rensa? Behöver jag aktivera den här inställningen om du vill använda SSE med kundhanterad nycklar?**

S: mjuka ta bort och gör inte rensa måste aktiveras att använda SSE med kundhanterad nycklar. De här inställningarna kan du kontrollera att nyckeln inte är av misstag eller avsiktligt borttagna. Den högsta bevarandeperioden nycklar har angetts till 90 dagarna, skyddar användare mot skadliga aktörer och är en utpressningstrojan som attacker. Den här inställningen kan inte inaktiveras.

**F: är SSE med kundhanterad nycklar får endast i vissa områden?**

S: SSE med kundhanterad nycklar är tillgänglig i alla regioner för Blob- och lagring.

**F: hur kan jag få någon om jag har problem eller vill ge feedback?**

A: Kontakta [ ssediscussions@microsoft.com ](mailto:ssediscussions@microsoft.com) för eventuella problem som rör Storage Service-kryptering.

## <a name="next-steps"></a>Nästa steg

-   Mer information om en omfattande uppsättning säkerhet funktioner som hjälper utvecklare att skapa säkra program, finns det [säkerhetsguiden för lagring](storage-security-guide.md).

-   Mer information om Azure Key Vault finns [vad är Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)?

-   Komma igång på Azure Key Vault finns [komma igång med Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-get-started).
