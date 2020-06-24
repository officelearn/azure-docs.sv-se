---
title: Azure Storage-kryptering av vilande data
description: Azure Storage skyddar dina data genom att automatiskt kryptera dem innan du sparar dem i molnet. Du kan förlita dig på Microsoft-hanterade nycklar för kryptering av data i ditt lagrings konto, eller så kan du hantera kryptering med dina egna nycklar.
services: storage
author: tamram
ms.service: storage
ms.date: 06/17/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 8b4236e40e8dfbe6ce67bca007be0b6737a6e0c8
ms.sourcegitcommit: 51977b63624dfd3b4f22fb9fe68761d26eed6824
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/17/2020
ms.locfileid: "84945587"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>Azure Storage-kryptering av vilande data

Azure Storage krypterar dina data automatiskt när de sparas i molnet. Azure Storage kryptering skyddar dina data och hjälper dig att uppfylla organisationens säkerhets-och efterlevnads åtaganden.

## <a name="about-azure-storage-encryption"></a>Om Azure Storage kryptering

Data i Azure Storage krypteras och dekrypteras transparent med 256-bitars AES- [kryptering](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), en av de starkaste block chiffer som är tillgängliga och är FIPS 140-2-kompatibel. Azure Storage kryptering liknar BitLocker-kryptering på Windows.

Azure Storage kryptering har Aktiver ATS för alla lagrings konton, inklusive både Resource Manager och klassiska lagrings konton. Azure Storage kryptering kan inte inaktive ras. Eftersom dina data är säkra som standard behöver du inte ändra koden eller programmen för att dra nytta av Azure Storage kryptering.

Data i ett lagrings konto är krypterade oavsett prestanda nivå (standard eller Premium), åtkomst nivå (frekvent eller låg frekvent) eller distributions modell (Azure Resource Manager eller klassisk). Alla blobbar på Arkiv nivån krypteras också. Alla Azure Storage alternativ för redundans stöder kryptering och alla data i både den primära och den sekundära regionen krypteras när geo-replikering är aktiverat. Alla Azure Storage-resurser krypteras, inklusive blobbar, diskar, filer, köer och tabeller. Alla metadata för objekt krypteras också. Det kostar inget extra att Azure Storage kryptering.

Varje Block-Blob, Lägg till BLOB eller sid-blob som skrevs till Azure Storage efter den 20 oktober 2017 är krypterad. Blobbar som skapats före detta datum fortsätter att krypteras med en bakgrunds process. Om du vill framtvinga kryptering av en blob som skapades före den 20 oktober 2017 kan du skriva om blobben. Information om hur du kontrollerar krypterings statusen för en BLOB finns i [kontrol lera krypterings statusen för en BLOB](../blobs/storage-blob-encryption-status.md).

Mer information om de kryptografiska modulerna underliggande Azure Storage kryptering finns i [Cryptography-API: nästa generation](https://docs.microsoft.com/windows/desktop/seccng/cng-portal).

## <a name="about-encryption-key-management"></a>Om hantering av krypterings nyckel

Data i ett nytt lagrings konto krypteras med Microsoft-hanterade nycklar. Du kan förlita dig på Microsoft-hanterade nycklar för kryptering av dina data, eller så kan du hantera kryptering med dina egna nycklar. Om du väljer att hantera kryptering med dina egna nycklar har du två alternativ:

- Du kan ange en *kundhanterad nyckel* med Azure Key Vault som ska användas för att kryptera och dekryptera data i Blob Storage och i Azure Files. <sup>1, 2</sup> mer information om Kundhanterade nycklar finns i [använda Kundhanterade nycklar med Azure Key Vault för att hantera Azure Storage kryptering](encryption-customer-managed-keys.md).
- Du kan ange en *anpassad nyckel* för Blob Storage-åtgärder. En klient som gör en Läs-eller skrivbegäran mot Blob Storage kan inkludera en krypterings nyckel på begäran om detaljerad kontroll över hur BLOB-data krypteras och dekrypteras. Mer information om kundspecifika nycklar finns i [Ange en krypterings nyckel på en begäran till Blob Storage (för hands version)](encryption-customer-provided-keys.md).

I följande tabell jämförs nyckel hanterings alternativ för Azure Storage kryptering.

|                                        |    Microsoft-hanterade nycklar                             |    Kundhanterade nycklar                                                                                                                        |    Kund-tillhandahållna nycklar                                                          |
|----------------------------------------|-------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------|
|    Kryptering/dekryptering    |    Azure                                              |    Azure                                                                                                                                        |    Azure                                                                         |
|    Azure Storage tjänster som stöds    |    Alla                                                |    Blob Storage, Azure Files<sup>1, 2</sup>                                                                                                               |    Blob Storage                                                                  |
|    Nyckel lagring                         |    Microsoft nyckel lager    |    Azure Key Vault                                                                                                                              |    Kundens egna nyckel lager                                                                 |
|    Största ansvar för nyckel rotation         |    Microsoft                                          |    Kund                                                                                                                                     |    Kund                                                                      |
|    Nyckel kontroll                          |    Microsoft                                     |    Kund                                                                                                                    |    Kund                                                                 |

<sup>1</sup> information om hur du skapar ett konto som stöder användning av Kundhanterade nycklar med Queue Storage finns i [skapa ett konto som stöder Kundhanterade nycklar för köer](account-encryption-key-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).<br />
<sup>2</sup> mer information om hur du skapar ett konto som stöder användning av Kundhanterade nycklar med Table Storage finns i [skapa ett konto som stöder Kundhanterade nycklar för tabeller](account-encryption-key-create.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json).

Information om kryptering och nyckel hantering för Azure Managed disks finns i [kryptering på Server sidan av Azure Managed disks](../../virtual-machines/windows/disk-encryption.md) för virtuella Windows-datorer eller [kryptering på Server sidan av Azure Managed disks](../../virtual-machines/linux/disk-encryption.md) för virtuella Linux-datorer.

## <a name="next-steps"></a>Nästa steg

- [Vad är Azure Key Vault?](../../key-vault/general/overview.md)
- [Konfigurera kundhanterade nycklar för Azure Storage-kryptering från Azure-portalen](storage-encryption-keys-portal.md)
- [Konfigurera kundhanterade nycklar för Azure Storage-kryptering från PowerShell](storage-encryption-keys-powershell.md)
- [Konfigurera kundhanterade nycklar för Azure Storage-kryptering från Azure CLI](storage-encryption-keys-cli.md)
