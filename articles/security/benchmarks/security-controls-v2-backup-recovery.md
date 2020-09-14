---
title: Azures säkerhets benchmark v2 – säkerhets kopiering och återställning
description: Säkerhets kopiering och återställning av Azure-säkerhetsprestandatest v2
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/13/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: fe6861a3319b9d9c0e6535ee3303c90f0a0f26c8
ms.sourcegitcommit: 94c750edd4d755d6ecee50ac977328098a277479
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/13/2020
ms.locfileid: "90059387"
---
# <a name="security-control-backup-and-recovery"></a>Säkerhets kontroll: säkerhets kopiering och återställning

Säkerhets kopiering och återställning täcker kontroller för att säkerställa att säkerhets kopiering av data och konfigurationer på olika tjänst nivåer utförs, verifieras och skyddas.

## <a name="br-1-ensure-regular-automated-backups"></a>BR-1: Säkerställ vanliga automatiserade säkerhets kopieringar

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP800 – 53 R4-ID: n |
|--|--|--|--|
| BR-1 | 10.1 | CP-2, CP4, CP-6, CP-9 |

Se till att du säkerhetskopierar system och data för att bibehålla affärs kontinuiteten efter en oväntad händelse. Detta bör vara vägledning för alla mål för återställnings punkt mål (återställnings punkt mål) och återställnings tids mål (RTO).

Aktivera Azure Backup och konfigurera säkerhets kopierings källan (t. ex. virtuella Azure-datorer, SQL Server, HANA-databaser eller fil resurser) samt önskad frekvens och kvarhållningsperiod.  

För en högre nivå av redundans kan du aktivera Geo-redundant lagring för att replikera säkerhetskopierade data till en sekundär region och återställa med återställning mellan regioner.

- [Så här aktiverar du Azure Backup](/azure/backup/)

- [Aktivera återställning mellan regioner](/azure/backup/backup-azure-arm-restore-vms#cross-region-restore)

**Ansvar**: kund

**Kund säkerhets intressenter**:

- [Principer och standarder](/azure/cloud-adoption-framework/organize/cloud-security-policy-standards)

- [Säkerhetsarkitektur](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Infrastruktur- och slutpunktssäkerhet](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Incidentberedskap](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

## <a name="br-2-encrypt-backup-data"></a>BR-2: kryptera säkerhets kopierings data

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP800 – 53 R4-ID: n |
|--|--|--|--|
| BR-2 | 10,2 | CP-9 |

Se till att säkerhets kopiorna skyddas mot attacker. Detta bör innefatta kryptering av säkerhets kopiorna för att skydda mot förlust av konfidentialitet.   

För lokal säkerhets kopiering med Azure Backup tillhandahålls kryptering vid vila med hjälp av den lösen fras som du anger. För vanlig säkerhets kopiering av Azure-tjänsten krypteras säkerhetskopierade data automatiskt med hjälp av Azure Platform-hanterade nycklar. Du kan välja att kryptera säkerhets kopian med hjälp av kund Managed Key. I det här fallet ser du till att den här Kundhanterade nyckeln i nyckel valvet också finns i säkerhets kopierings omfånget. 

Använd rollbaserad åtkomst kontroll i Azure Backup, Azure Key Vault eller andra resurser för att skydda säkerhets kopior och kund hanterade nycklar. Dessutom kan du aktivera avancerade säkerhetsfunktioner så att du kan kräva MFA innan du kan ändra eller ta bort säkerhets kopior.

- [Översikt över säkerhetsfunktioner i Azure Backup](/azure/backup/security-overview)

- [Kryptering av säkerhets kopierings data med Kundhanterade nycklar](/azure/backup/encryption-at-rest-with-cmk) 

- [Säkerhetskopiera Key Vault nycklar i Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Ansvar**: kund

**Kund säkerhets intressenter**:

- [Säkerhetsarkitektur](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Infrastruktur- och slutpunktssäkerhet](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Förberedelse av incidenten](/) Azure/Cloud-adoption-Framework/organisera/Cloud-Security-incident-preparation

## <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3: validera alla säkerhets kopior inklusive Kundhanterade nycklar

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP800 – 53 R4-ID: n |
|--|--|--|--|
| BR-3 | 10,3 | CP-4, CP-9 |

Regelbundet utföra Data återställning av säkerhets kopian. Se till att du kan återställa säkerhetskopierade nycklar som hanteras av kunden.

- [Återställa filer från säkerhets kopiering av virtuella Azure-datorer](/azure/backup/backup-azure-restore-files-from-vm)

- [Så här återställer du Key Vault nycklar i Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Ansvar**: kund

**Kund säkerhets intressenter**:

- [Incidentberedskap](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Hantering av säkerhetskompatibilitet](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4: minska risken för förlorade nycklar

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP800 – 53 R4-ID: n |
|--|--|--|--|
| BR-4 | 10,4 | CP-9 |

Se till att du har mått på plats för att förhindra och återställa från förlust av nycklar. Aktivera mjuk borttagning och tömning av skydd i Azure Key Vault för att skydda nycklar mot oavsiktlig eller skadlig borttagning.  

- [Så här aktiverar du mjuk borttagning och tömning av skydd i Key Vault](/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Ansvar**: kund

**Kund säkerhets intressenter**:

- [Säkerhetsarkitektur](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Incidentberedskap](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Data säkerhet](/azure/cloud-adoption-framework/organize/cloud-security-data-security)

