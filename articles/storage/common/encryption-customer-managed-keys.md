---
title: Använda Kundhanterade nycklar med Azure Key Vault för att hantera konto kryptering
titleSuffix: Azure Storage
description: Du kan använda din egen krypterings nyckel för att skydda data i ditt lagrings konto. När du anger en kundhanterad nyckel används nyckeln för att skydda och kontrol lera åtkomsten till nyckeln som krypterar dina data. Kundhanterade nycklar ger större flexibilitet för att hantera åtkomst kontroller.
services: storage
author: tamram
ms.service: storage
ms.date: 03/12/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: b2755d5aa5dbaa669fa2fdd8b84596e040b5dd6b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81456829"
---
# <a name="use-customer-managed-keys-with-azure-key-vault-to-manage-azure-storage-encryption"></a>Använda Kundhanterade nycklar med Azure Key Vault för att hantera Azure Storage kryptering

Du kan använda din egen krypterings nyckel för att skydda data i ditt lagrings konto. När du anger en kundhanterad nyckel används nyckeln för att skydda och kontrol lera åtkomsten till nyckeln som krypterar dina data. Kundhanterade nycklar ger större flexibilitet för att hantera åtkomst kontroller.

Du måste använda Azure Key Vault för att lagra dina Kundhanterade nycklar. Du kan antingen skapa egna nycklar och lagra dem i ett nyckel valv, eller så kan du använda Azure Key Vault API: er för att generera nycklar. Lagrings kontot och nyckel valvet måste finnas i samma region och i samma Azure Active Directory-klient (Azure AD), men de kan finnas i olika prenumerationer. Mer information om Azure Key Vault finns i [Azure Key Vault?](../../key-vault/general/overview.md).

## <a name="about-customer-managed-keys"></a>Om Kundhanterade nycklar

Följande diagram visar hur Azure Storage använder Azure Active Directory och Azure Key Vault för att göra förfrågningar med hjälp av kundhanterad nyckel:

![Diagram över hur Kundhanterade nycklar fungerar i Azure Storage](media/encryption-customer-managed-keys/encryption-customer-managed-keys-diagram.png)

I följande lista beskrivs de numrerade stegen i diagrammet:

1. En Azure Key Vault administratör beviljar behörigheter till krypterings nycklar till den hanterade identitet som är kopplad till lagrings kontot.
2. En Azure Storage administratör konfigurerar kryptering med en kundhanterad nyckel för lagrings kontot.
3. Azure Storage använder den hanterade identitet som är kopplad till lagrings kontot för att autentisera åtkomst till Azure Key Vault via Azure Active Directory.
4. Azure Storage omsluter konto krypterings nyckeln med kund nyckeln i Azure Key Vault.
5. Vid Läs-och skriv åtgärder skickar Azure Storage begär anden till Azure Key Vault för att packa upp konto krypterings nyckeln för att utföra kryptering och dekryptering.

## <a name="create-an-account-that-supports-customer-managed-keys-for-queues-and-tables"></a>Skapa ett konto som stöder Kundhanterade nycklar för köer och tabeller

Data som lagras i kö-och tabell tjänsterna skyddas inte automatiskt av en kundhanterad nyckel när Kundhanterade nycklar har Aktiver ATS för lagrings kontot. Du kan också konfigurera dessa tjänster när du skapar lagrings kontot som ska ingå i skyddet.

Mer information om hur du skapar ett lagrings konto som stöder Kundhanterade nycklar för köer och tabeller finns i [skapa ett konto som stöder Kundhanterade nycklar för tabeller och köer](account-encryption-key-create.md).

Data i BLOB-och fil tjänsterna skyddas alltid av Kundhanterade nycklar när Kundhanterade nycklar har kon figurer ATS för lagrings kontot.

## <a name="enable-customer-managed-keys-for-a-storage-account"></a>Aktivera Kundhanterade nycklar för ett lagrings konto

Kundhanterade nycklar kan bara aktive ras på befintliga lagrings konton. Nyckel valvet måste vara etablerad med åtkomst principer som beviljar nyckel behörigheter till den hanterade identitet som är kopplad till lagrings kontot. Den hanterade identiteten är bara tillgänglig när lagrings kontot har skapats.

När du konfigurerar en kundhanterad nyckel omsluts Azure Storage data krypterings nyckeln för kontot med den Kundhanterade nyckeln i det associerade nyckel valvet. Att aktivera Kundhanterade nycklar påverkar inte prestanda och börjar gälla omedelbart.

När du ändrar den nyckel som används för Azure Storage kryptering genom att aktivera eller inaktivera Kundhanterade nycklar, uppdatera nyckel versionen eller ange en annan nyckel ändras krypteringen av rot nyckeln, men data i ditt Azure Storage-konto behöver inte krypteras igen.

När du aktiverar eller inaktiverar Kundhanterade nycklar, eller när du ändrar nyckeln eller nyckel versionen, ändras skyddet av rot krypterings nyckeln, men data i Azure Storage-kontot behöver inte krypteras igen.

Information om hur du använder Kundhanterade nycklar med Azure Key Vault för Azure Storage kryptering finns i någon av följande artiklar:

- [Konfigurera Kundhanterade nycklar med Key Vault för Azure Storage kryptering från Azure Portal](storage-encryption-keys-portal.md)
- [Konfigurera Kundhanterade nycklar med Key Vault för Azure Storage kryptering från PowerShell](storage-encryption-keys-powershell.md)
- [Konfigurera Kundhanterade nycklar med Key Vault för Azure Storage kryptering från Azure CLI](storage-encryption-keys-cli.md)

> [!IMPORTANT]
> Kundhanterade nycklar är beroende av hanterade identiteter för Azure-resurser, en funktion i Azure AD. Hanterade identiteter stöder för närvarande inte scenarier mellan kataloger. När du konfigurerar Kundhanterade nycklar i Azure Portal tilldelas en hanterad identitet automatiskt till ditt lagrings konto under försättsblad. Om du senare flyttar prenumerationen, resurs gruppen eller lagrings kontot från en Azure AD-katalog till en annan överförs inte den hanterade identitet som är kopplad till lagrings kontot till den nya klienten, så Kundhanterade nycklar kanske inte längre fungerar. Mer information finns i **överföra en prenumeration mellan Azure AD-kataloger** i [vanliga frågor och svar och kända problem med hanterade identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).  

## <a name="store-customer-managed-keys-in-azure-key-vault"></a>Lagra Kundhanterade nycklar i Azure Key Vault

Om du vill aktivera Kundhanterade nycklar på ett lagrings konto måste du använda en Azure Key Vault för att lagra dina nycklar. Du måste aktivera både den **mjuka borttagningen** och **Rensa inte** egenskaperna i nyckel valvet.

Endast 2048-bitars RSA-och RSA-HSM-nycklar stöds med Azure Storage kryptering. Mer information om nycklar finns **Key Vault nycklar** i [om Azure Key Vault nycklar, hemligheter och certifikat](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys).

## <a name="rotate-customer-managed-keys"></a>Rotera Kundhanterade nycklar

Du kan rotera en kundhanterad nyckel i Azure Key Vault enligt efterlevnadsprinciper. När nyckeln roteras måste du uppdatera lagrings kontot för att använda den nya nyckel versions-URI: n. Information om hur du uppdaterar lagrings kontot för att använda en ny version av nyckeln i Azure Portal finns i avsnittet **Uppdatera nyckel versionen** i [Konfigurera kund hanterade nycklar för Azure Storage med hjälp av Azure Portal](storage-encryption-keys-portal.md).

Rotation av nyckeln utlöser inte Omkryptering av data i lagrings kontot. Det krävs ingen ytterligare åtgärd från användaren.

## <a name="revoke-access-to-customer-managed-keys"></a>Återkalla åtkomst till Kundhanterade nycklar

Du kan när som helst återkalla lagrings kontots åtkomst till den Kundhanterade nyckeln. När åtkomst till Kundhanterade nycklar har återkallats, eller när nyckeln har inaktiverats eller tagits bort, kan klienterna inte anropa åtgärder som läser från eller skriver till en BLOB eller dess metadata. Försök att anropa någon av följande åtgärder Miss lyckas med felkoden 403 (tillåts inte) för alla användare:

- [Visa en lista med blobar](/rest/api/storageservices/list-blobs), vid `include=metadata` anrop med parametern på begärande-URI
- [Hämta BLOB](/rest/api/storageservices/get-blob)
- [Hämta blobegenskaper](/rest/api/storageservices/get-blob-properties)
- [Hämta BLOB-metadata](/rest/api/storageservices/get-blob-metadata)
- [Ange BLOB-metadata](/rest/api/storageservices/set-blob-metadata)
- [Ögonblicks bilds-BLOB](/rest/api/storageservices/snapshot-blob)vid `x-ms-meta-name` anrop med begär ande huvudet
- [Kopiera blob](/rest/api/storageservices/copy-blob)
- [Kopiera BLOB från URL](/rest/api/storageservices/copy-blob-from-url)
- [Ange blobnivå](/rest/api/storageservices/set-blob-tier)
- [Spärra block](/rest/api/storageservices/put-block)
- [Skicka block från URL](/rest/api/storageservices/put-block-from-url)
- [Lägg till block](/rest/api/storageservices/append-block)
- [Lägg till block från URL](/rest/api/storageservices/append-block-from-url)
- [Placera blob](/rest/api/storageservices/put-blob)
- [Placerings sida](/rest/api/storageservices/put-page)
- [Lägg till sida från URL](/rest/api/storageservices/put-page-from-url)
- [BLOB för stegvis kopiering](/rest/api/storageservices/incremental-copy-blob)

Om du vill anropa dessa åtgärder igen återställer du åtkomsten till den Kundhanterade nyckeln.

Alla data åtgärder som inte listas i det här avsnittet kan fortsätta efter att Kundhanterade nycklar har återkallats eller att en nyckel har inaktiverats eller tagits bort.

Om du vill återkalla åtkomsten till Kundhanterade nycklar använder du [PowerShell](storage-encryption-keys-powershell.md#revoke-customer-managed-keys) eller [Azure CLI](storage-encryption-keys-cli.md#revoke-customer-managed-keys).

## <a name="customer-managed-keys-for-azure-managed-disks"></a>Kundhanterade nycklar för Azure Managed disks

Kundhanterade nycklar är också tillgängliga för hantering av kryptering av Azure Managed disks. Kundhanterade nycklar fungerar annorlunda för hanterade diskar än för Azure Storage resurser. Mer information finns i [kryptering på Server sidan av Azure Managed disks](../../virtual-machines/windows/disk-encryption.md) for Windows eller [Server Side Encryption på Azure Managed disks](../../virtual-machines/linux/disk-encryption.md) for Linux.

## <a name="next-steps"></a>Nästa steg

- [Konfigurera Kundhanterade nycklar med Key Vault för Azure Storage kryptering från Azure Portal](storage-encryption-keys-portal.md)
- [Konfigurera Kundhanterade nycklar med Key Vault för Azure Storage kryptering från PowerShell](storage-encryption-keys-powershell.md)
- [Konfigurera Kundhanterade nycklar med Key Vault för Azure Storage kryptering från Azure CLI](storage-encryption-keys-cli.md)
- [Azure Storage kryptering för vilande data](storage-service-encryption.md)
