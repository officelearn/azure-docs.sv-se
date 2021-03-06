---
title: Azures säkerhets benchmark v2 – säkerhets kopiering och återställning
description: Säkerhets kopiering och återställning av Azure-säkerhetsprestandatest v2
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 1ed477c6cf069dbb402e5bda9c1f48f7bc208f18
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94698809"
---
# <a name="security-control-v2-backup-and-recovery"></a>Säkerhets kontroll v2: säkerhets kopiering och återställning

Säkerhets kopiering och återställning täcker kontroller för att säkerställa att säkerhets kopiering av data och konfigurationer på olika tjänst nivåer utförs, verifieras och skyddas.

## <a name="br-1-ensure-regular-automated-backups"></a>BR-1: Säkerställ vanliga automatiserade säkerhets kopieringar

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP800 – 53 R4-ID: n |
|--|--|--|--|
| BR-1 | 10.1 | CP-2, CP4, CP-6, CP-9 |

Se till att du säkerhetskopierar system och data för att bibehålla affärs kontinuiteten efter en oväntad händelse. Detta bör definieras av alla mål för återställnings punkt mål (återställnings punkt mål) och återställnings tids mål (RTO).

Aktivera Azure Backup och konfigurera säkerhets kopierings källan (t. ex. virtuella Azure-datorer, SQL Server, HANA-databaser eller fil resurser) samt önskad frekvens och kvarhållningsperiod.  

För en högre skydds nivå kan du aktivera alternativ för Geo-redundant lagring för att replikera säkerhetskopierade data till en sekundär region och återställa med hjälp av återställning mellan regioner.

- [Affärskontinuitet och haveriberedskap i företagsskala](/azure/cloud-adoption-framework/ready/enterprise-scale/business-continuity-and-disaster-recovery)

- [Så här aktiverar du Azure Backup](../../backup/index.yml)

- [Aktivera återställning mellan regioner](../../backup/backup-azure-arm-restore-vms.md#cross-region-restore)

**Ansvar**: kund

**Kund säkerhets intressenter** ([Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Principer och standarder](/azure/cloud-adoption-framework/organize/cloud-security-policy-standards)

- [Säkerhetsarkitektur](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Infrastruktur- och slutpunktssäkerhet](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Incidentberedskap](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

## <a name="br-2-encrypt-backup-data"></a>BR-2: kryptera säkerhets kopierings data

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP800 – 53 R4-ID: n |
|--|--|--|--|
| BR-2 | 10,2 | CP-9 |

Se till att säkerhets kopiorna skyddas mot attacker. Detta bör innefatta kryptering av säkerhets kopiorna för att skydda mot förlust av konfidentialitet.   

För lokala säkerhets kopieringar med Azure Backup anges kryptering vid vila med hjälp av den lösen fras som du anger. För vanliga säkerhets kopieringar av Azure-tjänster krypteras säkerhetskopierade data automatiskt med hjälp av Azure Platform-hanterade nycklar. Du kan välja att kryptera säkerhets kopiorna med hjälp av kund Managed Key. I det här fallet ser du till att den här Kundhanterade nyckeln i nyckel valvet också finns i säkerhets kopierings omfånget. 

Använd rollbaserad åtkomst kontroll i Azure i Azure Backup, Azure Key Vault eller andra resurser för att skydda säkerhets kopior och Kundhanterade nycklar. Dessutom kan du aktivera avancerade säkerhetsfunktioner så att du kan kräva MFA innan du kan ändra eller ta bort säkerhets kopior.

- [Översikt över säkerhetsfunktioner i Azure Backup](../../backup/security-overview.md)

- [Kryptering av säkerhets kopierings data med Kundhanterade nycklar](../../backup/encryption-at-rest-with-cmk.md) 

- [Säkerhetskopiera Key Vault nycklar i Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

- [Säkerhetsfunktioner som hjälper till att skydda hybrid säkerhets kopieringar från attacker](../../backup/backup-azure-security-feature.md#prevent-attacks)

**Ansvar**: kund

**Kund säkerhets intressenter** ([Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Säkerhetsarkitektur](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Infrastruktur- och slutpunktssäkerhet](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Incidentberedskap](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

## <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3: validera alla säkerhets kopior inklusive Kundhanterade nycklar

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP800 – 53 R4-ID: n |
|--|--|--|--|
| BR-3 | 10,3 | CP-4, CP-9 |

Regelbundet utföra Data återställning av säkerhets kopian. Se till att du kan återställa säkerhetskopierade nycklar som hanteras av kunden.

- [Återställa filer från säkerhets kopiering av virtuella Azure-datorer](../../backup/backup-azure-restore-files-from-vm.md)

- [Så här återställer du Key Vault nycklar i Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Ansvar**: kund

**Kund säkerhets intressenter** ([Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Incidentberedskap](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Hantering av säkerhetskompatibilitet](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4: minska risken för förlorade nycklar

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP800 – 53 R4-ID: n |
|--|--|--|--|
| BR-4 | 10,4 | CP-9 |

Se till att du har mått på plats för att förhindra och återställa från förlust av nycklar. Aktivera mjuk borttagning och tömning av skydd i Azure Key Vault för att skydda nycklar mot oavsiktlig eller skadlig borttagning.  

- [Så här aktiverar du mjuk borttagning och tömning av skydd i Key Vault](../../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Ansvar**: kund

**Kund säkerhets intressenter** ([Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Säkerhetsarkitektur](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Incidentberedskap](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Data säkerhet](/azure/cloud-adoption-framework/organize/cloud-security-data-security)