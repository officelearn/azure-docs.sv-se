---
title: Använda kundhanterade nycklar med Azure Key Vault för att hantera kontokryptering
titleSuffix: Azure Storage
description: Du kan använda din egen krypteringsnyckel för att skydda data i ditt lagringskonto. När du anger en kundhanterad nyckel används nyckeln för att skydda och styra åtkomsten till nyckeln som krypterar dina data. Kundhanterade nycklar ger större flexibilitet för att hantera åtkomstkontroller.
services: storage
author: tamram
ms.service: storage
ms.date: 03/12/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: b2755d5aa5dbaa669fa2fdd8b84596e040b5dd6b
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81456829"
---
# <a name="use-customer-managed-keys-with-azure-key-vault-to-manage-azure-storage-encryption"></a>Använda kundhanterade nycklar med Azure Key Vault för att hantera Azure Storage-kryptering

Du kan använda din egen krypteringsnyckel för att skydda data i ditt lagringskonto. När du anger en kundhanterad nyckel används nyckeln för att skydda och styra åtkomsten till nyckeln som krypterar dina data. Kundhanterade nycklar ger större flexibilitet för att hantera åtkomstkontroller.

Du måste använda Azure Key Vault för att lagra dina kundhanterade nycklar. Du kan antingen skapa egna nycklar och lagra dem i ett nyckelvalv, eller så kan du använda Azure Key Vault API:er för att generera nycklar. Lagringskontot och nyckelvalvet måste finnas i samma region och i samma Azure Active Directory-klientorganisation (Azure AD), men de kan finnas i olika prenumerationer. Mer information om Azure Key Vault finns i [Vad är Azure Key Vault?](../../key-vault/general/overview.md).

## <a name="about-customer-managed-keys"></a>Om kundhanterade nycklar

Följande diagram visar hur Azure Storage använder Azure Active Directory och Azure Key Vault för att göra förfrågningar med den kundhanterade nyckeln:

![Diagram som visar hur kundhanterade nycklar fungerar i Azure Storage](media/encryption-customer-managed-keys/encryption-customer-managed-keys-diagram.png)

I följande lista beskrivs de numrerade stegen i diagrammet:

1. En Azure Key Vault-administratör ger behörighet till krypteringsnycklar till den hanterade identitet som är associerad med lagringskontot.
2. En Azure Storage-administratör konfigurerar kryptering med en kundhanterad nyckel för lagringskontot.
3. Azure Storage använder den hanterade identitet som är associerad med lagringskontot för att autentisera åtkomst till Azure Key Vault via Azure Active Directory.
4. Azure Storage omsluter kontokrypteringsnyckeln med kundnyckeln i Azure Key Vault.
5. För läs-/skrivåtgärder skickar Azure Storage begäranden till Azure Key Vault för att packa upp kontokrypteringsnyckeln för att utföra krypterings- och dekrypteringsåtgärder.

## <a name="create-an-account-that-supports-customer-managed-keys-for-queues-and-tables"></a>Skapa ett konto som stöder kundhanterade nycklar för köer och tabeller

Data som lagras i kö- och tabelltjänsterna skyddas inte automatiskt av en kundhanterad nyckel när kundhanterade nycklar är aktiverade för lagringskontot. Du kan också konfigurera dessa tjänster när du skapar lagringskontot som ska ingå i det här skyddet.

Mer information om hur du skapar ett lagringskonto som stöder kundhanterade nycklar för köer och tabeller finns i [Skapa ett konto som stöder kundhanterade nycklar för tabeller och köer](account-encryption-key-create.md).

Data i Blob- och Filtjänsterna skyddas alltid av kundhanterade nycklar när kundhanterade nycklar konfigureras för lagringskontot.

## <a name="enable-customer-managed-keys-for-a-storage-account"></a>Aktivera kundhanterade nycklar för ett lagringskonto

Kundhanterade nycklar kan endast aktiveras på befintliga lagringskonton. Nyckelvalvet måste etableras med åtkomstprinciper som beviljar nyckelbehörigheter till den hanterade identitet som är associerad med lagringskontot. Den hanterade identiteten är endast tillgänglig när lagringskontot har skapats.

När du konfigurerar en kundhanterad nyckel radbryts rotdatakrypteringsnyckeln för kontot med den kundhanterade nyckeln i det associerade nyckelvalvet. Aktivera kundhanterade nycklar påverkar inte prestanda och börjar gälla omedelbart.

När du ändrar nyckeln som används för Azure Storage-kryptering genom att aktivera eller inaktivera kundhanterade nycklar, uppdatera nyckelversionen eller ange en annan nyckel, behöver krypteringen av rotnyckeln ändras, men data i ditt Azure Storage-konto behöver inte krypteras på nytt.

När du aktiverar eller inaktiverar kundhanterade nycklar, eller när du ändrar nyckeln eller nyckelversionen, ändras skyddet av rotkrypteringsnyckeln, men data i ditt Azure Storage-konto behöver inte krypteras på nytt.

Mer information om hur du använder kundhanterade nycklar med Azure Key Vault för Azure Storage-kryptering finns i någon av följande artiklar:

- [Konfigurera kundhanterade nycklar med Key Vault för Azure Storage-kryptering från Azure-portalen](storage-encryption-keys-portal.md)
- [Konfigurera kundhanterade nycklar med Key Vault för Azure Storage-kryptering från PowerShell](storage-encryption-keys-powershell.md)
- [Konfigurera kundhanterade nycklar med Key Vault för Azure Storage-kryptering från Azure CLI](storage-encryption-keys-cli.md)

> [!IMPORTANT]
> Kundhanterade nycklar är beroende av hanterade identiteter för Azure-resurser, en funktion i Azure AD. Hanterade identiteter stöder för närvarande inte scenarier för korskataloger. När du konfigurerar kundhanterade nycklar i Azure-portalen tilldelas en hanterad identitet automatiskt till ditt lagringskonto under omslagen. Om du sedan flyttar prenumerationen, resursgruppen eller lagringskontot från en Azure AD-katalog till en annan överförs inte den hanterade identiteten som är associerad med lagringskontot till den nya klienten, så kundhanterade nycklar kanske inte längre fungerar. Mer information finns i **Överföra en prenumeration mellan Azure AD-kataloger** i vanliga frågor och andra frågor och kända problem med hanterade [identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).  

## <a name="store-customer-managed-keys-in-azure-key-vault"></a>Lagra kundhanterade nycklar i Azure Key Vault

Om du vill aktivera kundhanterade nycklar på ett lagringskonto måste du använda ett Azure Key Vault för att lagra dina nycklar. Du måste aktivera egenskaperna **Mjuk borttagning** **och Rensa inte** på nyckelvalvet.

Endast 2048-bitars RSA- och RSA-HSM-nycklar stöds med Azure Storage-kryptering. Mer information om nycklar finns i **Key Vault-nycklar** i [Om Azure Key Vault-nycklar, hemligheter och certifikat](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys).

## <a name="rotate-customer-managed-keys"></a>Rotera kundhanterade nycklar

Du kan rotera en kundhanterad nyckel i Azure Key Vault enligt dina efterlevnadsprinciper. När nyckeln roteras måste du uppdatera lagringskontot för att kunna använda den nya nyckelversionen URI. Mer information om hur du uppdaterar lagringskontot för att använda en ny version av nyckeln i Azure-portalen finns i avsnittet **Uppdatera nyckelversionen** i [Konfigurera kundhanterade nycklar för Azure Storage med hjälp av Azure-portalen](storage-encryption-keys-portal.md).

Om du roterar nyckeln kan du inte kryptera data i lagringskontot igen. Användaren behöver inte vidta några ytterligare åtgärder.

## <a name="revoke-access-to-customer-managed-keys"></a>Återkalla åtkomst till kundhanterade nycklar

Du kan när som helst återkalla lagringskontots åtkomst till den kundhanterade nyckeln. När åtkomsten till kundhanterade nycklar har återkallats, eller när nyckeln har inaktiverats eller tagits bort, kan klienter inte anropa åtgärder som läs från eller skriver till en blob eller dess metadata. Försök att anropa någon av följande åtgärder misslyckas med felkod 403 (Förbjudet) för alla användare:

- [Lista Blobbar](/rest/api/storageservices/list-blobs), när `include=metadata` anropas med parametern på begäran URI
- [Hämta Blob](/rest/api/storageservices/get-blob)
- [Hämta blobegenskaper](/rest/api/storageservices/get-blob-properties)
- [Hämta Blob-metadata](/rest/api/storageservices/get-blob-metadata)
- [Ange Blob-metadata](/rest/api/storageservices/set-blob-metadata)
- [Ögonblicksbildblob ,](/rest/api/storageservices/snapshot-blob)när `x-ms-meta-name` anropas med begäranden
- [Kopiera blob](/rest/api/storageservices/copy-blob)
- [Kopiera Blob från URL](/rest/api/storageservices/copy-blob-from-url)
- [Ange blobnivå](/rest/api/storageservices/set-blob-tier)
- [Sätt block](/rest/api/storageservices/put-block)
- [Placera block från URL](/rest/api/storageservices/put-block-from-url)
- [Lägg till block](/rest/api/storageservices/append-block)
- [Lägg till block från URL](/rest/api/storageservices/append-block-from-url)
- [Placera blob](/rest/api/storageservices/put-blob)
- [Placera sida](/rest/api/storageservices/put-page)
- [Lägga till url för sida från](/rest/api/storageservices/put-page-from-url)
- [Inkrementell kopia Blob](/rest/api/storageservices/incremental-copy-blob)

Om du vill anropa dessa åtgärder igen återställer du åtkomsten till den kundhanterade nyckeln.

Alla dataåtgärder som inte visas i det här avsnittet kan fortsätta när kundhanterade nycklar har återkallats eller en nyckel har inaktiverats eller tagits bort.

Om du vill återkalla åtkomsten till kundhanterade nycklar använder du [PowerShell](storage-encryption-keys-powershell.md#revoke-customer-managed-keys) eller [Azure CLI](storage-encryption-keys-cli.md#revoke-customer-managed-keys).

## <a name="customer-managed-keys-for-azure-managed-disks"></a>Kundhanterade nycklar för Hanterade Azure-diskar

Kundhanterade nycklar är också tillgängliga för hantering av kryptering av Azure-hanterade diskar. Kundhanterade nycklar fungerar annorlunda för hanterade diskar än för Azure Storage-resurser. Mer information finns i [Server-side kryptering av Azure-hanterade diskar](../../virtual-machines/windows/disk-encryption.md) för Windows eller [Server-kryptering av Azure-hanterade diskar](../../virtual-machines/linux/disk-encryption.md) för Linux.

## <a name="next-steps"></a>Nästa steg

- [Konfigurera kundhanterade nycklar med Key Vault för Azure Storage-kryptering från Azure-portalen](storage-encryption-keys-portal.md)
- [Konfigurera kundhanterade nycklar med Key Vault för Azure Storage-kryptering från PowerShell](storage-encryption-keys-powershell.md)
- [Konfigurera kundhanterade nycklar med Key Vault för Azure Storage-kryptering från Azure CLI](storage-encryption-keys-cli.md)
- [Azure Storage-kryptering för data i vila](storage-service-encryption.md)
