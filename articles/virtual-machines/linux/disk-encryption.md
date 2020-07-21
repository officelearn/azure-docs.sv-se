---
title: Kryptering på Server sidan av Azure Managed Disks – Azure CLI
description: Azure Storage skyddar dina data genom att kryptera dem i vila innan du sparar dem i lagrings kluster. Du kan förlita dig på Microsoft-hanterade nycklar för kryptering av dina hanterade diskar, eller så kan du använda Kundhanterade nycklar för att hantera kryptering med dina egna nycklar.
author: roygara
ms.date: 07/10/2020
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines-linux
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 0847f2360a3562f96454992470e3cc2000f66b11
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86502732"
---
# <a name="server-side-encryption-of-azure-disk-storage"></a>Kryptering på Server sidan av Azure-disklagring

Server Side Encryption (SSE) skyddar dina data och hjälper dig att uppfylla organisationens säkerhets-och efterlevnads åtaganden. SSE krypterar automatiskt dina data som lagras på Azure Managed disks (OS-och data diskar) i vila som standard när de sparas i molnet. 

Data i Azure Managed disks krypteras transparent med 256-bitars [AES-kryptering](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), en av de starkaste block chiffer som är tillgängliga och är FIPS 140-2-kompatibel. Mer information om de kryptografiska modulerna underliggande Azure Managed disks finns i [Cryptography-API: nästa generation](/windows/desktop/seccng/cng-portal)

Kryptering på Server sidan påverkar inte prestanda för hanterade diskar och det finns ingen ytterligare kostnad. 

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
    Om du behöver kringgå detta måste du [Kopiera alla data](disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk) till en helt annan hanterad disk som inte använder Kundhanterade nycklar.
[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

> [!IMPORTANT]
> Kundhanterade nycklar är beroende av hanterade identiteter för Azure-resurser, en funktion i Azure Active Directory (Azure AD). När du konfigurerar Kundhanterade nycklar, tilldelas en hanterad identitet automatiskt till dina resurser under försättsblad. Om du senare flyttar prenumerationen, resurs gruppen eller den hanterade disken från en Azure AD-katalog till en annan överförs inte den hanterade identitet som är kopplad till hanterade diskar till den nya klienten, så Kundhanterade nycklar kanske inte längre fungerar. Mer information finns i [överföra en prenumeration mellan Azure AD-kataloger](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

## <a name="encryption-at-host---end-to-end-encryption-for-your-vm-data"></a>Kryptering vid värd-slutpunkt-till-slutpunkt-kryptering för dina VM-data

När du aktiverar kryptering på värden startar krypteringen på själva VM-värden, den Azure-server som den virtuella datorn är tilldelad till. Data för dina temporära diskar och OS/datadisk-cachen lagras på den virtuella dator värden. När du har aktiverat kryptering på värden krypteras alla dessa data i vila och flöden krypteras till lagrings tjänsten, där de är bestående. Kryptering på värden krypterar i princip dina data från slut punkt till slut punkt. Kryptering på värden använder inte den virtuella datorns CPU och påverkar inte den virtuella datorns prestanda. 

Temporära diskar krypteras i vila med plattforms hanterade nycklar när du aktiverar kryptering från slut punkt till slut punkt. Cacheminnen för operativ system och datadisk krypteras i vila med antingen Kundhanterade eller plattforms hanterade nycklar, beroende på vilken disk krypterings typ som valts. Om en disk till exempel krypteras med Kundhanterade nycklar, krypteras cachen för disken med Kundhanterade nycklar, och om en disk krypteras med plattforms hanterade nycklar krypteras cachen för disken med plattforms hanterade nycklar.

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

[Azure Disk Encryption](../../security/fundamentals/azure-disk-encryption-vms-vmss.md) utnyttjar funktionen [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) i Linux för att kryptera hanterade diskar med Kundhanterade nycklar i den virtuella gäst datorn.  Kryptering på Server sidan med Kundhanterade nycklar förbättrar på ADE genom att du kan använda alla OS-typer och avbildningar för dina virtuella datorer genom att kryptera data i lagrings tjänsten.

## <a name="next-steps"></a>Nästa steg

- [Aktivera kryptering från slut punkt till slut punkt med kryptering på värd-CLI](disks-enable-host-based-encryption-cli.md)
- [Aktivera dubbel kryptering i vila för hanterade diskar – CLI](disks-enable-double-encryption-at-rest-cli.md)
- [Aktivera Kundhanterade nycklar för hanterade diskar – CLI](disks-enable-customer-managed-keys-cli.md)
- [Aktivera Kundhanterade nycklar för hanterade diskar – Portal](disks-enable-customer-managed-keys-portal.md)
- [Vad är Azure Key Vault?](../../key-vault/general/overview.md)
