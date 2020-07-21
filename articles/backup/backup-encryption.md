---
title: Kryptering i Azure Backup
description: Lär dig mer om hur krypterings funktioner i Azure Backup hjälper dig att skydda dina säkerhets kopierings data och uppfylla affärs behoven.
ms.topic: conceptual
ms.date: 04/30/2020
ms.custom: references_regions
ms.openlocfilehash: 099e736bfb321f0f92bd3a57f9c24e88293b42bb
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86538759"
---
# <a name="encryption-in-azure-backup"></a>Kryptering i Azure Backup

Alla säkerhetskopierade data krypteras automatiskt när de lagras i molnet med hjälp av Azure Storage kryptering, vilket hjälper dig att uppfylla dina åtaganden om säkerhet och efterlevnad. Den här informationen i rest krypteras med 256-bitars AES-kryptering, en av de starkaste block chiffer som är tillgängliga och är FIPS 140-2-kompatibel.

Förutom kryptering i vila överförs alla dina säkerhets kopierings data i överföring via HTTPS. Det finns alltid i Azure stamnät nätverket.

Mer information finns i [Azure Storage kryptering för vilande data](../storage/common/storage-service-encryption.md). Läs [Azure Backup vanliga frågor](./backup-azure-backup-faq.md#encryption) och svar om du vill besvara eventuella frågor om kryptering.

## <a name="encryption-of-backup-data-using-platform-managed-keys"></a>Kryptering av säkerhets kopierings data med hjälp av plattforms hanterade nycklar

Som standard krypteras alla dina data med hjälp av plattforms hanterade nycklar. Du behöver inte vidta några uttryckliga åtgärder från din slut för att aktivera den här krypteringen och den gäller för alla arbets belastningar som säkerhets kopie ras till Recovery Services-valvet.

## <a name="encryption-of-backup-data-using-customer-managed-keys"></a>Kryptering av säkerhets kopierings data med Kundhanterade nycklar

När du säkerhetskopierar Azure-Virtual Machines kan du nu kryptera dina data med hjälp av nycklar som ägs och hanteras av dig. Med Azure Backup kan du använda dina RSA-nycklar som lagras i Azure Key Vault för kryptering av dina säkerhets kopior. Krypterings nyckeln som används för kryptering av säkerhets kopior kan skilja sig från den som används för källan. Data skyddas med en AES 256-baserad data krypterings nyckel (DEK), som i sin tur skyddas med hjälp av dina nycklar. Detta ger dig fullständig kontroll över data och nycklar. För att tillåta kryptering krävs det att Recovery Services valvet beviljas åtkomst till krypterings nyckeln i Azure Key Vault. Du kan inaktivera nyckeln eller återkalla åtkomst när det behövs. Du måste dock aktivera kryptering med dina nycklar innan du försöker skydda några objekt i valvet.

Läs mer om hur du krypterar dina säkerhetskopierade data med Kundhanterade nycklar [här](encryption-at-rest-with-cmk.md).

## <a name="backup-of-managed-disk-vms-encrypted-using-customer-managed-keys"></a>Säkerhets kopiering av hanterade virtuella hård diskar krypterade med Kundhanterade nycklar

Med Azure Backup kan du också säkerhetskopiera virtuella Azure-datorer som använder din nyckel för [kryptering av lagrings tjänst](../storage/common/storage-service-encryption.md). Den nyckel som används för att kryptera diskarna lagras i Azure Key Vault och hanteras av dig. Kryptering för lagringstjänst (SSE) med Kundhanterade nycklar skiljer sig från Azure Disk Encryption, eftersom ADE använder BitLocker (för Windows) och DM-crypt (för Linux) för att utföra kryptering vid kryptering, krypterar SSE data i lagrings tjänsten, så att du kan använda alla operativ system eller avbildningar för dina virtuella datorer. Mer information finns i [kryptering av hanterade diskar med kund hanterade nycklar](../virtual-machines/windows/disk-encryption.md#customer-managed-keys) .

## <a name="infrastructure-level-encryption-for-backup-data"></a>Kryptering på infrastruktur nivå för säkerhets kopierings data

Förutom att kryptera dina data i Recovery Services valvet med Kundhanterade nycklar kan du också välja att ha ett extra krypterings lager konfigurerat på lagrings infrastrukturen. Den här infrastruktur krypteringen hanteras av plattformen och tillsammans med kryptering i vila med Kundhanterade nycklar, vilket möjliggör kryptering med två lager av dina säkerhets kopierings data. Det bör noteras att infrastruktur kryptering bara kan konfigureras om du först väljer att använda dina egna nycklar för kryptering i vila. Infrastruktur kryptering använder plattforms hanterade nycklar för att kryptera data.

>[!NOTE]
>Infrastruktur kryptering är för närvarande i begränsad för hands version och är tillgängligt i östra USA, USA West2, södra centrala USA, US Gov, Arizona och US GOV Virginia-regioner. Om du vill använda funktionen i någon av dessa regioner kan du fylla i [det här formuläret](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUN0VHNEpJS0ZUWklUNVdJSTEzR0hIOVRMVC4u) och skicka e-post till oss [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .

## <a name="backup-of-vms-encrypted-using-ade"></a>Säkerhets kopiering av virtuella datorer som har krypterats med ADE

Med Azure Backup kan du också säkerhetskopiera virtuella Azure-datorer som har sina operativ system eller data diskar krypterade med Azure Disk Encryption. ADE använder BitLocker för virtuella Windows-datorer och DM-crypt för virtuella Linux-datorer för att utföra kryptering i gästen. Mer information finns i [säkerhetskopiera och återställa krypterade virtuella datorer med Azure Backup](./backup-azure-vms-encryption.md).

## <a name="next-steps"></a>Nästa steg

- [Säkerhetskopiera och återställa en krypterad virtuell Azure-dator](backup-azure-vms-encryption.md)
