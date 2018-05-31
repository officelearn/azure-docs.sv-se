---
title: Exempel på principmallar
description: JSON-exempel för Azure Policy
services: azure-policy
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 01/17/2018
ms.author: dacoulte
ms.custom: mvc
ms.openlocfilehash: 4b9096c1fb0d9ee74849e259a6e0af2486c5d29b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34195132"
---
# <a name="templates-for-azure-policy"></a>Mallar för Azure Policy

Följande tabell innehåller länkar till json-mallar för Azure Policy. De här exemplen finns i [lagringsplatsen för Azure Policy-exempel](https://github.com/Azure/azure-policy).

| | |
|---|---|
|**Compute**||
| [Godkända VM-avbildningar](scripts/allowed-custom-images.md) | Kräver att endast godkända anpassade avbildningar distribueras i din miljö. Du anger en matris med godkända avbildnings-ID. |
| [Granska när VM inte använder hanterad disk](scripts/create-vm-managed-disk.md) | Granskar när en virtuell dator skapas som inte använder hanterade diskar.|
| [Granska om tillägg inte finns](scripts/audit-ext-not-exist.md) | Granskar om ett tillägg inte distribueras med en virtuell dator. Du anger tilläggsutgivaren och typ för att kontrollera om den har distribuerats. |
| [Tillåt anpassad VM-avbildning från en resursgrupp](scripts/allow-custom-vm-image.md) |  Kräver att anpassade avbildningar kommer från en godkänd resursgrupp. Du anger namnet på den godkända resursgruppen. |
| [Neka hybridanvändningsförmån](scripts/deny-hybrid-use.md) | Förbjuder användning av Azure Hybrid Use Benefit (AHUB). Använd det här när du inte vill tillåta användning av lokala licenser. |
| [Otillåtna VM-tillägg](scripts/not-allowed-vm-ext.md) | Förbjuder användning av angivna tillägg. Du anger en matris som innehåller de otillåtna tilläggstyperna. |
| [Tillåt endast en viss VM-plattformsavbildning](scripts/allow-certain-vm-image.md) | Kräver att virtuella datorer använder en specifik version av UbuntuServer. |
| [Skapa VM med hanterad disk](scripts/use-managed-disk-vm.md) | Kräver att virtuella datorer använder hanterade diskar.|
|**Övervakning**||
| [Granska diagnostikinställning](scripts/audit-diag-setting.md) | Granskar om diagnostikinställningarna inte är aktiverade för angivna resurstyper. Du anger en matris med resurstyper för att kontrollera om diagnostikinställningarna är aktiverade. |
|**Konventioner för namn och text**||
| [Tillåt flera namnmönster](scripts/allow-multiple-name-patterns.md) | Tillåt ett av många namnmönster som ska användas för resurser. |
| [Kräv like-mönster](scripts/enforce-like-pattern.md) | Se till att resursnamnen uppfyller like-villkoret för ett mönster. |
| [Kräv matchningsmönster](scripts/enforce-match-pattern.md) | Se till att resursnamnen matchar namngivningsmönstret. |
| [Kräv taggmatchningsmönster](scripts/enforce-tag-match-pattern.md) | Se till att ett taggvärde matchar ett textmönster. |
|**Nätverk**||
| [Tillåtna SKU:er för programgateway](scripts/allowed-app-gate-sku.md) | Kräver att programgatewayer använder en godkänd SKU. Du anger en matris med godkända SKU:er. |
| [Granska om Network Watcher inte är aktiverat för regionen](scripts/net-watch-not-enabled.md) | Granskar om Network Watcher inte är aktiverat för en angiven region. Du anger namnet på regionen för att kontrollera om Network Watcher är aktiverat. |
| [NSG X på varje NIC](scripts/nsg-on-nic.md) | Kräver att en viss nätverkssäkerhetsgrupp används med varje gränssnitt för virtuellt nätverk. Du anger ID för nätverkssäkerhetsgruppen som ska användas. |
| [NSG X i varje undernät](scripts/nsg-on-subnet.md) | Kräver att en viss nätverkssäkerhetsgrupp används med varje virtuella undernät. Du anger ID för nätverkssäkerhetsgruppen som ska användas. |
| [Tillåten Express Route-bandbredd](scripts/allowed-er-band.md) | Kräver att ExpressRoutes använder en angiven uppsättning bandbredder. Du anger en matris med SKU:er som kan anges för ExpressRoute. |
| [Tillåten peeringplats för Express Route](scripts/allowed-peering-er.md) | Kräver att Express Routes använder angivna peeringplatser. Du anger en matris med tillåtna peeringplatser. |
| [Tillåtna Express Route-SKU:er](scripts/allowed-er-skus.md) | Kräver att Express Routes använder en godkänd SKU. Du anger en matris med tillåtna SKU: er. |
| [Tillåtna SKU:er för belastningsutjämnare](scripts/allowed-lb-skus.md) | Kräver att belastningsutjämnare använder en godkänd SKU. Du anger en matris med tillåtna SKU: er. |
| [Ingen nätverkspeering till ER-nätverk](scripts/no-peering-er-net.md) | Förbjuder att en nätverkspeering associeras till ett nätverk i en angiven resursgrupp. Använd för att förhindra anslutning med centralt hanterad nätverksinfrastruktur. Du kan ange namnet på resursgruppen att förhindra association. |
| [Ingen användardefinierad routningstabell](scripts/no-user-def-route-table.md)  |Förbjuder att virtuella nätverk distribueras med en användardefinierad routningstabell. |
| [Tillåtna SKU:er för virtuell nätverksgateway](scripts/allowed-vn-gate-sku.md) | Kräver att virtuella nätverksgatewayer använder en godkänd SKU och gatewaytyp. Du anger en matris med godkända SKU:er och en matris med godkända gatewaytyper. |
| [Använda godkända undernät för VM-nätverksgränssnitt](scripts/use-approved-subnet-vm-nics.md) | Kräver att nätverksgränssnitt använder ett godkänt undernät. Du anger ID för det godkända undernätet. |
| [Använda godkända vNet för VM-nätverksgränssnitt](scripts/use-approved-vnet-vm-nics.md) | Kräver att nätverksgränssnitt använder ett godkänt virtuellt nätverk. Du anger ID för det godkända virtuella nätverket. |
|**Taggar**||
| [Principinitiativ för faktureringstaggar](scripts/billing-tags-policy-init.md) | Kräver angivna taggvärden för kostnadsställe och produktnamn. Använder inbyggda principer för att tillämpa och framtvinga taggar som krävs. Du anger de värden som krävs för taggar.  |
| [Lägg till tagg och dess värde i resursgrupper](scripts/enforce-tag-rg.md) | Kräver en tagg och ett värde i en resursgrupp. Du anger taggnamn och värde som ska krävas.  |
|**SQL**||
| [Granska granskningsinställning på SQL DB-nivå](scripts/audit-sql-db-audit-setting.md) | Granskar granskningsinställningar för SQL Database om inställningarna inte matchar en angiven inställning. Du kan ange ett värde som anger om granskningsinställningar ska vara aktiverade eller inaktiverade.  |
| [Granska status för transparent datakryptering](scripts/audit-trans-data-enc-status.md) | Granskar transparent datakryptering för SQL Database om det inte är aktiverat.  |
| [Granska inställningar för hotidentifiering på DB-nivå](scripts/audit-db-threat-det-setting.md) | Granskar principer för SQL Database-säkerhetsaviseringar om principerna inte är inställda på det angivna tillståndet. Du kan ange ett värde som anger om hotidentifiering är aktiverat eller inaktiverat.  |
| [Granska granskningsinställningar på SQL Server-nivå](scripts/audit-sql-ser-leve-audit-setting.md) | Granskar granskningsinställningar för SQL Server om inställningarna inte matchar en angiven inställning. Du kan ange ett värde som anger om granskningsinställningar ska vara aktiverade eller inaktiverade. |
| [Granska inställningar för hotidentifiering på servernivå](scripts/audit-sql-ser-threat-det-setting.md) | Granskar principer för SQL Database-säkerhetsaviseringar om principerna inte är inställda på det angivna tillståndet. Du kan ange ett värde som anger om hotidentifiering är aktiverat eller inaktiverat.  |
| [Granska när det saknas Azure Active Directory-administratör](scripts/audit-no-aad-admin.md) | Granska när det inte finns någon Azure Active Directory-administratör som tilldelats till SQL-servern. |
| [Tillåtna SKU:er för SQL DB](scripts/allowed-sql-db-skus.md) | Kräver att för SQL Database använder en godkänd SKU. Du kan ange en matris med tillåtna ID:n för SKU:er eller en matris med tillåtna SKU-namn. |
|**Storage**||
| [Tillåtna SKU:er för lagringskonton och virtuella datorer](scripts/allowed-skus-storage.md) | Kräver att lagringskonton och virtuella datorer använder godkända SKU:er. Använder inbyggda principer till att säkerställa godkända SKU:er. Du anger en matris med godkända SKU:er för virtuella datorer och en matris med godkända SKU:er för lagringskonton. |
| [Garantera endast https-trafik för lagringskonto](scripts/ensure-https-stor-acct.md) | Kräver att lagringskonton använder HTTPS-trafik.  |
| [Neka lågfrekvent åtkomstnivå för lagringskonton](scripts/deny-cool-access-tiering.md) | Förbjuder användning av lågfrekvent åtkomstnivå för Blob Storage-konton.  |
| [Garantera kryptering för lagringsfil](scripts/ensure-store-file-enc.md) | Kräver att filkryptering är aktiverat för lagringskonton.  |
|**Inbyggd princip**||
| [Tillåtna platser](scripts/allowed-locs.md) | Kräver att alla resurser distribueras till de godkända platserna. Du anger en matris med godkända platser.  |
| [Tillåtna resurstyper](scripts/allowed-res-types.md) | Garanterar att bara godkända resurstyper distribueras. Du anger en matris med resurstyper som tillåts.  |
| [Tillåtna SKU:er för lagringskonto](scripts/allowed-stor-acct-skus.md) | Kräver att lagringskonton använder en godkänd SKU. Du anger en matris med godkända SKU:er. |
| [Använd taggen och dess standardvärde](scripts/apply-tag-def-val.md) | Lägger till ett angivet taggnamn och värde, om den taggen inte tillhandahålls. Du anger taggnamn och värde som ska gälla.  |
| [Granska för SQL Database-kryptering](scripts/sql-database-encryption-audit.md) | Granskar om transparent datakryptering inte är aktiverat för SQL Database. |
| [Granska granskningsinställningar för SQL Server](scripts/sql-server-audit.md) | Granskar SQL Server beroende på om granskningsinställningarna är aktiverade. |
| [Kräv kryptering för Data Lake Store](scripts/enforce-datalakestore-encryption.md) | Nekar alla Data Lake Store-konton som inte har kryptering aktiverat. |
| [Framtvinga tagg och dess värde](scripts/enforce-tag-val.md) | Kräver ett angivet taggnamn och värde. Du anger taggnamn och värde som ska framtvingas.  |
| [Otillåtna resurstyper](scripts/not-allowed-res-type.md) | Förhindrar distributionen av angivna resurstyper. Du anger en matris med resurstyper som ska blockeras.  |
| [Kräv SQL Server version 12.0](scripts/req-sql-12.md) | Kräver att SQL-servrar använder version 12.0.  |
| [Kräv kryptering för lagringskonto](scripts/req-store-acct-enc.md) | Kräver att lagringskontot använder blobkryptering.  |
