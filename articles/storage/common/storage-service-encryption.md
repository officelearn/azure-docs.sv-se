---
title: Azure Storage-kryptering för data i vila
description: Azure Storage skyddar dina data genom att automatiskt kryptera dem innan den sparas i molnet. Du kan lita på Microsoft-hanterade nycklar för kryptering av data i ditt lagringskonto, eller så kan du hantera kryptering med dina egna nycklar.
services: storage
author: tamram
ms.service: storage
ms.date: 04/10/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: f112a4523bc5af9ecae57e93dfb90795d3fe9c50
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113277"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>Azure Storage-kryptering för data i vila

Azure Storage krypterar automatiskt dina data när de sparas till molnet. Azure Storage-kryptering skyddar dina data och hjälper dig att uppfylla dina organisatoriska säkerhets- och efterlevnadsåtaganden.

## <a name="about-azure-storage-encryption"></a>Om Azure Storage-kryptering

Data i Azure Storage krypteras och dekrypteras transparent med 256-bitars [AES-kryptering](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), ett av de starkaste blockchiffer som finns och är FIPS 140-2-kompatibel. Azure Storage-kryptering liknar BitLocker-kryptering i Windows.

Azure Storage-kryptering är aktiverad för alla lagringskonton, inklusive både Resource Manager och klassiska lagringskonton. Azure Storage-kryptering kan inte inaktiveras. Eftersom dina data är skyddade som standard behöver du inte ändra din kod eller dina program för att dra nytta av Azure Storage-kryptering.

Data i ett lagringskonto krypteras oavsett prestandanivå (standard eller premium), åtkomstnivå (frekvent eller cool) eller distributionsmodell (Azure Resource Manager eller klassisk). Alla blobbar på arkivnivån är också krypterade. Alla Azure Storage-redundansalternativ stöder kryptering och alla data i både primära och sekundära regioner krypteras när geo-replikering är aktiverat. Alla Azure Storage-resurser krypteras, inklusive blobbar, diskar, filer, köer och tabeller. Alla objektmetadata är också krypterade. Det finns ingen extra kostnad för Azure Storage-kryptering.

Varje blockblob, tilläggsblob eller sidblob som skrevs till Azure Storage efter den 20 oktober 2017 är krypterad. Blobbar som skapats före detta datum fortsätter att krypteras av en bakgrundsprocess. Om du vill tvinga fram kryptering av en blob som skapades före den 20 oktober 2017 kan du skriva om bloben. Mer information om hur du kontrollerar krypteringsstatus för en blob finns [i Kontrollera krypteringsstatus för en blob](../blobs/storage-blob-encryption-status.md).

Mer information om kryptografiska moduler som ligger till grund för Azure Storage-kryptering finns i [Kryptografi-API: Nästa generation](https://docs.microsoft.com/windows/desktop/seccng/cng-portal).

## <a name="about-encryption-key-management"></a>Om hantering av krypteringsnyckel

Data i ett nytt lagringskonto krypteras med Microsoft-hanterade nycklar. Du kan lita på Microsoft-hanterade nycklar för kryptering av dina data, eller så kan du hantera kryptering med dina egna nycklar. Om du väljer att hantera kryptering med dina egna nycklar har du två alternativ:

- Du kan ange en *kundhanterad nyckel* med Azure Key Vault som ska användas för att kryptera och dekryptera data i Blob-lagring och i Azure-filer. <sup>1,2</sup> Mer information om kundhanterade nycklar finns i [Använda kundhanterade nycklar med Azure Key Vault för att hantera Azure Storage-kryptering](encryption-customer-managed-keys.md).
- Du kan ange en *kundad nyckel* för Blob-lagringsåtgärder. En klient som gör en läs- eller skrivbegäran mot Blob-lagring kan innehålla en krypteringsnyckel på begäran om detaljerad kontroll över hur blob-data krypteras och dekrypteras. Mer information om nycklar som tillhandahålls av kunden finns i [Ange en krypteringsnyckel på en begäran till Blob-lagring (förhandsversion).](encryption-customer-provided-keys.md)

I följande tabell jämförs viktiga hanteringsalternativ för Azure Storage-kryptering.

|                                        |    Microsoft-hanterade nycklar                             |    Kundhanterade nycklar                                                                                                                        |    Nycklar som tillhandahålls av kunden                                                          |
|----------------------------------------|-------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------|
|    Krypterings-/dekrypteringsåtgärder    |    Azure                                              |    Azure                                                                                                                                        |    Azure                                                                         |
|    Azure Storage-tjänster stöds    |    Alla                                                |    Blob-lagring, Azure-filer<sup>1,2</sup>                                                                                                               |    Blob Storage                                                                  |
|    Nyckellagring                         |    Microsoft-nyckelbutik    |    Azure Key Vault                                                                                                                              |    Azure Key Vault eller någon annan nyckelbutik                                                                 |
|    Nyckelrotationsansvar         |    Microsoft                                          |    Kund                                                                                                                                     |    Kund                                                                      |
|    Nyckelkontroll                          |    Microsoft                                     |    Kund                                                                                                                    |    Kund                                                                 |

<sup>1</sup> Information om hur du skapar ett konto som stöder användning av kundhanterade nycklar med Kölagring finns i [Skapa ett konto som stöder kundhanterade nycklar för köer](account-encryption-key-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).<br />
<sup>2</sup> Information om hur du skapar ett konto som stöder användning av kundhanterade nycklar med Tabelllagring finns i [Skapa ett konto som stöder kundhanterade nycklar för tabeller](account-encryption-key-create.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json).

## <a name="next-steps"></a>Nästa steg

- [Vad är Azure Key Vault?](../../key-vault/key-vault-overview.md)
- [Konfigurera kundhanterade nycklar för Azure Storage-kryptering från Azure-portalen](storage-encryption-keys-portal.md)
- [Konfigurera kundhanterade nycklar för Azure Storage-kryptering från PowerShell](storage-encryption-keys-powershell.md)
- [Konfigurera kundhanterade nycklar för Azure Storage-kryptering från Azure CLI](storage-encryption-keys-cli.md)
