---
title: Kryptering på Server sidan av Azure Managed Disks – PowerShell
description: Azure Storage skyddar dina data genom att kryptera dem i vila innan du sparar dem i lagrings kluster. Du kan förlita dig på Microsoft-hanterade nycklar för kryptering av dina hanterade diskar, eller så kan du använda Kundhanterade nycklar för att hantera kryptering med dina egna nycklar.
author: roygara
ms.date: 07/10/2020
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 0bb0fb268d18ddc152dae45014e2154686762976
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2020
ms.locfileid: "86259832"
---
# <a name="server-side-encryption-of-azure-disk-storage"></a>Kryptering på Server sidan av Azure-disklagring

Server Side Encryption (SSE) skyddar dina data och hjälper dig att uppfylla organisationens säkerhets-och efterlevnads åtaganden. SSE krypterar automatiskt dina data som lagras på Azure Managed disks (OS-och data diskar) i vila som standard när de sparas i molnet. 

Data i Azure Managed disks krypteras transparent med 256-bitars [AES-kryptering](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), en av de starkaste block chiffer som är tillgängliga och är FIPS 140-2-kompatibel. Mer information om de kryptografiska modulerna underliggande Azure Managed disks finns i [Cryptography-API: nästa generation](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)

Kryptering på Server sidan påverkar inte prestanda för hanterade diskar och det finns ingen ytterligare kostnad. 

> [!NOTE]
> Temporära diskar är inte hanterade diskar och krypteras inte av SSE om du inte aktiverar kryptering på värden.

## <a name="about-encryption-key-management"></a>Om hantering av krypterings nyckel

Du kan förlita dig på plattforms hanterade nycklar för kryptering av din hanterade disk, eller så kan du hantera kryptering med hjälp av dina egna nycklar. Om du väljer att hantera kryptering med dina egna nycklar kan du ange en *kundhanterad nyckel* som ska användas för att kryptera och dekryptera alla data i Managed disks. 

I följande avsnitt beskrivs de olika alternativen för nyckel hantering i större detalj.

### <a name="platform-managed-keys"></a>Plattforms hanterade nycklar

Som standard använder hanterade diskar plattforms hanterade krypterings nycklar. Från och med den 10 juni 2017 krypteras alla nya hanterade diskar, ögonblicks bilder, bilder och nya data som skrivs till befintliga hanterade diskar automatiskt i vila med plattforms hanterade nycklar.

### <a name="customer-managed-keys"></a>Kundhanterade nycklar

[!INCLUDE [virtual-machines-managed-disks-description-customer-managed-keys](../../../includes/virtual-machines-managed-disks-description-customer-managed-keys.md)]

#### <a name="restrictions"></a>Begränsningar

För närvarande har Kundhanterade nycklar följande begränsningar:

- Om den här funktionen är aktive rad för disken kan du inte inaktivera den.
    Om du behöver kringgå detta måste du [Kopiera alla data](disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk) till en helt annan hanterad disk som inte använder Kundhanterade nycklar.
[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

## <a name="encryption-at-host---end-to-end-encryption-for-your-vm-data"></a>Kryptering vid värd-slutpunkt-till-slutpunkt-kryptering för dina VM-data

Kryptering från slut punkt till slut punkt börjar från VM-värden, den Azure-server som den virtuella datorn allokeras till. Data på dina temporära diskar och OS/datadisk-cachen lagras på den virtuella dator värden. När du aktiverar kryptering från slut punkt till slut punkt krypteras alla dessa data i vila och flöden krypteras till lagrings tjänsten, där de sparas. Kryptering från slut punkt till slut punkt använder inte den virtuella datorns processor och påverkar inte den virtuella datorns prestanda. 

Temporära diskar krypteras i vila med plattforms hanterade nycklar när du aktiverar kryptering från slut punkt till slut punkt. Cacheminnen för operativ system och datadisk är krypterade i vila med antingen Kundhanterade eller plattforms hanterade nycklar, beroende på krypterings typ. Om en disk till exempel krypteras med Kundhanterade nycklar, krypteras cachen för disken med Kundhanterade nycklar, och om en disk krypteras med plattforms hanterade nycklar krypteras cachen för disken med plattforms hanterade nycklar.

### <a name="restrictions"></a>Begränsningar

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

#### <a name="supported-regions"></a>Regioner som stöds

[!INCLUDE [virtual-machines-disks-encryption-at-host-regions](../../../includes/virtual-machines-disks-encryption-at-host-regions.md)]

#### <a name="supported-vm-sizes"></a>VM-storlekar som stöds

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

## <a name="double-encryption-at-rest"></a>Dubbel kryptering i vila

Hög säkerhets känsliga kunder som är intresserade av den risk som är kopplad till en viss krypteringsalgoritm, implementering eller nyckel som komprometteras kan nu välja ytterligare lager av kryptering med hjälp av en annan krypteringsalgoritm/läge på infrastruktur lagret med hjälp av plattforms hanterade krypterings nycklar. Det nya lagret kan tillämpas på diskar, ögonblicks bilder och avbildningar, som alla kommer att krypteras i vila med dubbel kryptering.

### <a name="supported-regions"></a>Regioner som stöds

[!INCLUDE [virtual-machines-disks-double-encryption-at-rest-regions](../../../includes/virtual-machines-disks-double-encryption-at-rest-regions.md)]

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>Kryptering på Server sidan jämfört med Azure Disk Encryption

[Azure Disk Encryption](../../security/fundamentals/azure-disk-encryption-vms-vmss.md) utnyttjar [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) -funktionen i Windows för att kryptera hanterade diskar med Kundhanterade nycklar i den virtuella gäst datorn.  Kryptering på Server sidan med Kundhanterade nycklar förbättrar på ADE genom att du kan använda alla OS-typer och avbildningar för dina virtuella datorer genom att kryptera data i lagrings tjänsten.

> [!IMPORTANT]
> Kundhanterade nycklar är beroende av hanterade identiteter för Azure-resurser, en funktion i Azure Active Directory (Azure AD). När du konfigurerar Kundhanterade nycklar, tilldelas en hanterad identitet automatiskt till dina resurser under försättsblad. Om du senare flyttar prenumerationen, resurs gruppen eller den hanterade disken från en Azure AD-katalog till en annan överförs inte den hanterade identitet som är kopplad till hanterade diskar till den nya klienten, så Kundhanterade nycklar kanske inte längre fungerar. Mer information finns i [överföra en prenumeration mellan Azure AD-kataloger](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).


## <a name="next-steps"></a>Nästa steg

- [Aktivera kryptering från slut punkt till slut punkt med kryptering på värd – PowerShell](disks-enable-host-based-encryption-powershell.md)
- [Aktivera dubbel kryptering i vila för dina hanterade diskar – PowerShell](disks-enable-double-encryption-at-rest-powershell.md)
- [Aktivera Kundhanterade nycklar för dina hanterade diskar – PowerShell](disks-enable-customer-managed-keys-powershell.md)
- [Aktivera Kundhanterade nycklar för dina hanterade diskar – portalen](disks-enable-customer-managed-keys-portal.md)
- [Utforska Azure Resource Manager mallar för att skapa krypterade diskar med Kundhanterade nycklar](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Vad är Azure Key Vault?](../../key-vault/general/overview.md)
