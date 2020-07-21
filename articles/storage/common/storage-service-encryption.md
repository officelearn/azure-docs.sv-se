---
title: Azure Storage-kryptering av vilande data
description: Azure Storage skyddar dina data genom att automatiskt kryptera dem innan du sparar dem i molnet. Du kan förlita dig på Microsoft-hanterade nycklar för kryptering av data i ditt lagrings konto, eller så kan du hantera kryptering med dina egna nycklar.
services: storage
author: tamram
ms.service: storage
ms.date: 07/16/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: b6244b3ab72f7fa8ea375ff67a08e8d1d241df4a
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86527905"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>Azure Storage-kryptering av vilande data

Azure Storage krypterar dina data automatiskt när de sparas i molnet. Azure Storage kryptering skyddar dina data och hjälper dig att uppfylla organisationens säkerhets-och efterlevnads åtaganden.

## <a name="about-azure-storage-encryption"></a>Om Azure Storage kryptering

Data i Azure Storage krypteras och dekrypteras transparent med 256-bitars AES- [kryptering](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), en av de starkaste block chiffer som är tillgängliga och är FIPS 140-2-kompatibel. Azure Storage kryptering liknar BitLocker-kryptering på Windows.

Azure Storage kryptering har Aktiver ATS för alla lagrings konton, inklusive både Resource Manager och klassiska lagrings konton. Azure Storage kryptering kan inte inaktive ras. Eftersom dina data är säkra som standard behöver du inte ändra koden eller programmen för att dra nytta av Azure Storage kryptering.

Data i ett lagrings konto är krypterade oavsett prestanda nivå (standard eller Premium), åtkomst nivå (frekvent eller låg frekvent) eller distributions modell (Azure Resource Manager eller klassisk). Alla blobbar på Arkiv nivån krypteras också. Alla Azure Storage alternativ för redundans stöder kryptering och alla data i både den primära och den sekundära regionen krypteras när geo-replikering är aktiverat. Alla Azure Storage-resurser krypteras, inklusive blobbar, diskar, filer, köer och tabeller. Alla metadata för objekt krypteras också. Det kostar inget extra att Azure Storage kryptering.

Varje Block-Blob, Lägg till BLOB eller sid-blob som skrevs till Azure Storage efter den 20 oktober 2017 är krypterad. Blobbar som skapats före detta datum fortsätter att krypteras med en bakgrunds process. Om du vill framtvinga kryptering av en blob som skapades före den 20 oktober 2017 kan du skriva om blobben. Information om hur du kontrollerar krypterings statusen för en BLOB finns i [kontrol lera krypterings statusen för en BLOB](../blobs/storage-blob-encryption-status.md).

Mer information om de kryptografiska modulerna underliggande Azure Storage kryptering finns i [Cryptography-API: nästa generation](https://docs.microsoft.com/windows/desktop/seccng/cng-portal).

Information om kryptering och nyckel hantering för Azure Managed disks finns i [kryptering på Server sidan av Azure Managed disks](../../virtual-machines/windows/disk-encryption.md) för virtuella Windows-datorer eller [kryptering på Server sidan av Azure Managed disks](../../virtual-machines/linux/disk-encryption.md) för virtuella Linux-datorer.

## <a name="about-encryption-key-management"></a>Om hantering av krypterings nyckel

Data i ett nytt lagrings konto krypteras med Microsoft-hanterade nycklar. Du kan förlita dig på Microsoft-hanterade nycklar för kryptering av dina data, eller så kan du hantera kryptering med dina egna nycklar. Om du väljer att hantera kryptering med dina egna nycklar har du två alternativ:

- Du kan ange en *kundhanterad nyckel* med Azure Key Vault som ska användas för att kryptera och dekryptera data i Blob Storage och i Azure Files. <sup>1, 2</sup> mer information om Kundhanterade nycklar finns i [använda Kundhanterade nycklar med Azure Key Vault för att hantera Azure Storage kryptering](encryption-customer-managed-keys.md).
- Du kan ange en *anpassad nyckel* för Blob Storage-åtgärder. En klient som gör en Läs-eller skrivbegäran mot Blob Storage kan inkludera en krypterings nyckel på begäran om detaljerad kontroll över hur BLOB-data krypteras och dekrypteras. Mer information om kundspecifika nycklar finns i [Ange en krypterings nyckel på en begäran till Blob Storage (för hands version)](encryption-customer-provided-keys.md).

I följande tabell jämförs nyckel hanterings alternativ för Azure Storage kryptering.

| Nyckel hanterings parameter | Microsoft-hanterade nycklar | Kundhanterade nycklar | Kund-tillhandahållna nycklar |
|--|--|--|--|
| Kryptering/dekryptering | Azure | Azure | Azure |
| Azure Storage tjänster som stöds | Alla | Blob Storage, Azure Files<sup>1, 2</sup> | Blob Storage |
| Nyckel lagring | Microsoft nyckel lager | Azure Key Vault | Kundens egna nyckel lager |
| Största ansvar för nyckel rotation | Microsoft | Kund | Kund |
| Nyckel kontroll | Microsoft | Kund | Kund |

<sup>1</sup> information om hur du skapar ett konto som stöder användning av Kundhanterade nycklar med Queue Storage finns i [skapa ett konto som stöder Kundhanterade nycklar för köer](account-encryption-key-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).<br />
<sup>2</sup> mer information om hur du skapar ett konto som stöder användning av Kundhanterade nycklar med Table Storage finns i [skapa ett konto som stöder Kundhanterade nycklar för tabeller](account-encryption-key-create.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json).

## <a name="encryption-scopes-for-blob-storage-preview"></a>Krypterings omfång för Blob Storage (för hands version)

Som standard krypteras ett lagrings konto med en nyckel som är begränsad till lagrings kontot. Du kan välja att använda antingen Microsoft-hanterade nycklar eller Kundhanterade nycklar som lagras i Azure Key Vault för att skydda och kontrol lera åtkomsten till nyckeln som krypterar dina data.

Med krypterings omfång kan du välja att hantera kryptering på nivån för behållaren eller en enskild blob. Du kan använda krypterings omfång för att skapa säkra gränser mellan data som finns i samma lagrings konto men som tillhör olika kunder.

Du kan skapa ett eller flera krypterings omfång för ett lagrings konto med hjälp av Azure Storage Resource Provider. När du skapar ett krypterings omfång anger du om omfattningen är skyddad med en Microsoft-hanterad nyckel eller med en kundhanterad nyckel som lagras i Azure Key Vault. Olika krypterings omfång på samma lagrings konto kan använda antingen Microsoft-hanterade eller kund hanterade nycklar.

När du har skapat ett krypterings omfång kan du ange att krypterings omfång på en begäran om att skapa en behållare eller en blob. Mer information om hur du skapar en krypterings omfattning finns i [skapa och hantera krypterings omfattningar (för hands version)](../blobs/encryption-scope-manage.md).

> [!NOTE]
> Krypterings omfång stöds inte med RA-GRS-konton (Read-Access Geo-redundant Storage) under för hands versionen.

> [!IMPORTANT]
> För hands versionen av krypterings området är endast avsedd för användning utan produktion. Service nivå avtal (service avtal) för produktions tjänster är inte tillgängliga för närvarande.
>
> Undvik oväntade kostnader genom att inaktivera eventuella krypterings omfattningar som du inte behöver.

### <a name="create-a-container-or-blob-with-an-encryption-scope"></a>Skapa en behållare eller BLOB med en krypterings omfattning

Blobbar som skapas under en krypterings omfattning krypteras med den nyckel som angetts för det aktuella omfånget. Du kan ange ett krypterings omfång för en enskild BLOB när du skapar blobben, eller så kan du ange en standard krypterings omfattning när du skapar en behållare. När en standard krypterings omfattning anges på nivån för en behållare krypteras alla blobbar i behållaren med den nyckel som är kopplad till standard omfånget.

När du skapar en BLOB i en behållare som har ett standard krypterings omfång kan du ange en krypterings omfattning som åsidosätter standard krypterings omfånget om behållaren har kon figurer ATS för att tillåta åsidosättningar av standard krypterings omfattningen. För att förhindra åsidosättningar av standard krypterings omfånget konfigurerar du behållaren för att neka åsidosättningar för en enskild blob.

Läs åtgärder på en blob som tillhör ett krypterings område sker transparent, så länge som krypterings omfånget inte är inaktiverat.

### <a name="disable-an-encryption-scope"></a>Inaktivera ett krypterings omfång

När du inaktiverar en krypterings omfattning kommer eventuella efterföljande Läs-eller Skriv åtgärder som görs med krypterings omfånget att Miss Missing med HTTP-felkoden 403 (tillåts inte). Om du aktiverar krypterings omfånget igen, kommer Läs-och skriv åtgärder att fortsätta normalt igen.

När en krypterings omfattning är inaktive rad debiteras du inte längre. Inaktivera eventuella krypterings områden som inte behövs för att undvika onödiga kostnader.

Om krypterings omfattningen är skyddad med Kundhanterade nycklar för Azure Key Vault kan du också ta bort den associerade nyckeln i nyckel valvet för att inaktivera krypterings omfånget. Tänk på att Kundhanterade nycklar i Azure Key Vault skyddas av mjuk borttagnings-och tömnings skydd, och en borttagen nyckel omfattas av det beteende som definierats för dessa egenskaper. Mer information finns i något av följande avsnitt i Azure Key Vault-dokumentationen:

- [Använda mjuk borttagning med PowerShell](../../key-vault/general/soft-delete-powershell.md)
- [Använda mjuk borttagning med CLI](../../key-vault/general/soft-delete-cli.md)

> [!NOTE]
> Det går inte att ta bort en krypterings omfattning.

## <a name="next-steps"></a>Nästa steg

- [Vad är Azure Key Vault?](../../key-vault/general/overview.md)
- [Konfigurera kundhanterade nycklar för Azure Storage-kryptering från Azure-portalen](storage-encryption-keys-portal.md)
- [Konfigurera kundhanterade nycklar för Azure Storage-kryptering från PowerShell](storage-encryption-keys-powershell.md)
- [Konfigurera kundhanterade nycklar för Azure Storage-kryptering från Azure CLI](storage-encryption-keys-cli.md)
