---
title: "Princip för mallen exempel | Microsoft Docs"
description: "JSON-exempel för Azure-princip"
services: azure-policy
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
tags: 
ms.assetid: 
ms.service: azure-policy
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: 
ms.date: 10/30/2017
ms.author: banders
ms.custom: mvc
ms.openlocfilehash: bb0d996b950a31e3eaaaa9d90a6b96617b58192f
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2017
---
# <a name="templates-for-azure-policy"></a>Mallar för Azure-princip

Följande tabell innehåller länkar till json-mallar för Azure-principen.

| | |
|---|---|
|**Compute**||
| [Godkända VM-avbildningar](scripts/allowed-custom-images.md) | Kräver att enbart godkänd anpassade avbildningar distribuerats i din miljö. Du anger en matris med godkända avbildningen ID: N. |
| [Skapa virtuell dator med hanterad Disk](scripts/create-vm-managed-disk.md) | Granskningar när en virtuell dator skapas som inte använder hanterade diskar.|
| [Granska om tillägg inte finns](scripts/audit-ext-not-exist.md) | Granskningar om filnamnstillägget inte har distribuerats till en virtuell dator. Anger du tillägg utgivaren och typ för att kontrollera om den har distribuerats. |
| [Tillåt anpassade VM-avbildning från en resursgrupp](scripts/allow-custom-vm-image.md) |  Kräver att anpassade avbildningar kommer från en godkänd resursgrupp. Du kan ange namnet på resursgruppen godkända. |
| [Neka hybrid Använd förmån](scripts/deny-hybrid-use.md) | Tillåter inte användning av Azure (AHUB Hybrid använda förmånen). Använd när du inte vill tillåta att den lokala licenser. |
| [Inte tillåtet VM-tillägg](scripts/not-allowed-vm-ext.md) | Förhindrar att den angivna tillägg. Du anger en matris som innehåller otillåtna Tilläggstyper. |
| [Tillåt endast en vissa plattformsavbildning för VM](scripts/allow-certain-vm-image.md) | Kräver att virtuella datorer använder en viss version av UbuntuServer. |
| [Skapa virtuell dator med hanterad Disk](scripts/use-managed-disk-vm.md) | Kräver att virtuella datorer använder hanterade diskar.|
|**Övervakning**||
| [Granska diagnostikinställningen](scripts/audit-diag-setting.md) | Granskningar om diagnostikinställningar har inte aktiverats för anges resurstyper. Du anger en matris med resurstyper för att kontrollera om diagnostikinställningar är aktiverade. |
|**Nätverk**||
| [Tillåtna program Gateway-SKU: er](scripts/allowed-app-gate-sku.md) | Kräver att programgatewayer använder en godkänd SKU. Du anger en matris med godkända SKU: er. |
| [Granska om Nätverksbevakaren inte har aktiverats för region](scripts/net-watch-not-enabled.md) | Granskningar om nätverksbevakaren inte har aktiverats för ett visst område. Du kan ange namnet på regionen som ska kontrollera om nätverksbevakaren har aktiverats. |
| [NSG X på varje nätverkskort](scripts/nsg-on-nic.md) | Kräver att en specifik nätverkssäkerhetsgrupp används med alla virtuella nätverksgränssnitt. Du kan ange ID för nätverkssäkerhetsgruppen ska användas. |
| [NSG X i varje undernät](scripts/nsg-on-subnet.md) | Kräver att en specifik nätverkssäkerhetsgrupp används med alla virtuella undernät. Du kan ange ID för nätverkssäkerhetsgruppen ska användas. |
| [Tillåtna Express Route-bandbredd](scripts/allowed-er-band.md) | Kräver att snabb vägar använder en viss uppsättning bandbredder. Du anger en matris med SKU: er som kan anges för Express Route. |
| [Tillåtna Peeringplatsen för Express Route](scripts/allowed-peering-er.md) | Kräver att Express vägar Använd angetts peering platser. Du anger en matris med tillåtna peering platser. |
| [Tillåtna Express Route-SKU: er](scripts/allowed-er-skus.md) | Kräver att Express vägar använder en godkänd SKU. Du anger en matris med tillåtna SKU: er. |
| [Tillåtna Load Balancer SKU: er](scripts/allowed-lb-skus.md) | Kräver att belastningsutjämnare använder en godkänd SKU. Du anger en matris med tillåtna SKU: er. |
| [Inget nätverk peering till ER nätverk](scripts/no-peering-er-net.md) | Förhindrar att ett nätverk som peering från som hör till ett nätverk i en viss resursgrupp. Använd för att förhindra anslutning med centralt hanterade nätverksinfrastruktur. Du kan ange namnet på resursgruppen att förhindra att kopplingen. |
| [Ingen användardefinierad routningstabellen](scripts/no-user-def-route-table.md)  |Förhindrar att virtuella nätverk som distribueras med en användardefinierad routningstabell. |
| [Tillåtna virtuella Gateway-SKU: er](scripts/no-user-def-route-table.md) | Kräver att virtuella nätverksgatewayerna använder en godkända SKU och gateway-typen. Anger en matris med godkända SKU: er och en matris med godkända gateway-typer. |
| [Använd godkända undernät för Virtuellt nätverkskort](scripts/use-approved-subnet-vm-nics.md) | Kräver att nätverksgränssnitt använder ett godkända undernät. Du kan ange ID för godkända undernätet. |
| [Använd godkända virtuella nätverk för Virtuella nätverkskort](scripts/use-approved-vnet-vm-nics.md) | Kräver att nätverksgränssnitt använder ett virtuellt nätverk som är godkända. Du kan ange ID för det virtuella nätverket som är godkända. |
|**Taggar**||
| [Fakturering taggar princip initiativ](scripts/billing-tags-policy-init.md) | Kräver angivna värden för kostnad center och produktens namn. Använder inbyggda principer för att tillämpa och tillämpa taggar som krävs. Du kan ange obligatoriska värden för taggar.  |
| [Framtvinga taggen och dess värde på resursgrupper](scripts/enforce-tag-rg.md) | Kräver en tagg och värde i en resursgrupp. Du kan ange den obligatoriska taggnamn och värde.  |
|**SQL**||
| [Granska SQL DB-nivån granskning](scripts/audit-sql-db-audit-setting.md) | Granskningar SQL granskningsinställningar för databasen om dessa inställningar inte matchar en angiven nivå. Du kan ange ett värde som anger om granskningsinställningarna ska aktiveras eller inaktiveras.  |
| [Granska transparent data krypteringsstatus](scripts/audit-trans-data-enc-status.md) | Granskningar transparent datakryptering för SQL-databasen om den inte är aktiverad.  |
| [Granska DB nivån threat detection inställningen](scripts/audit-db-threat-det-setting.md) | Granskningar avisering principer SQL-databasen om dessa principer inte har angetts till angivet tillstånd. Du kan ange ett värde som anger om hotidentifiering är aktiverat eller inaktiverat.  |
| [Gransknings-och SQL Server-nivån granskning](scripts/audit-sql-ser-leve-audit-setting.md) | Granskningar SQL server audit-inställningar om dessa inställningar inte matchar en angiven nivå. Du kan ange ett värde som anger om granskningsinställningarna ska aktiveras eller inaktiveras. |
| [Granska Serverinställningen nivån hot identifiering](scripts/audit-sql-ser-threat-det-setting.md) | Granskningar avisering principer SQL-databasen om dessa principer inte har angetts till angivet tillstånd. Du kan ange ett värde som anger om hotidentifiering är aktiverat eller inaktiverat.  |
| [Tillåtna SQL DB SKU: er](scripts/allowed-sql-db-skus.md) | Kräver SQL-databaser använder en godkänd SKU. Du anger en matris med tillåtna SKU-ID: N eller en matris med tillåtna SKU namn. |
|**Storage**||
| [Tillåtna SKU: er för Storage-konton och virtuella datorer](scripts/allowed-skus-storage.md) | Kräver att storage-konton och virtuella datorer använder godkända SKU: er. Använder inbyggda principer för att säkerställa godkända SKU: er. Anger en matris med godkända virtuella datorer SKU: er och en matris med godkända lagringskonto SKU: er. |
| [Se till att https-trafik endast för storage-konto](scripts/ensure-https-stor-acct.md) | Kräver storage-konton för att använda HTTPS-trafik.  |
| [Kall neka skiktning för storage-konton](scripts/deny-cool-access-tiering.md) | Tillåter inte användning av kall åtkomst skiktning för blob storage-konton.  |
| [Se till att filen lagringskryptering](scripts/ensure-store-file-enc.md) | Kräver att filen kryptering är aktiverat för lagringskonton.  |
|**Inbyggd princip**||
| [Tillåtna platser](scripts/allowed-locs.md) | Kräver att alla resurser har distribuerats till godkända platserna. Du anger en matris med godkända platser.  |
| [Tillåtna resurstyper](scripts/allowed-res-types.md) | Garanterar att bara godkända resurstyper distribueras. Du anger en matris med resurstyper som tillåts.  |
| [Tillåtna lagringskonto SKU: er](scripts/allowed-stor-acct-skus.md) | Kräver att storage-konton använder en godkänd SKU. Du anger en matris med godkända SKU: er. |
| [Tillämpa taggen och ett standardvärde](scripts/apply-tag-def-val.md) | Lägger till angivna taggnamn och värde, om taggen inte har angetts. Du kan ange taggnamnet och värdet ska gälla.  |
| [Framtvinga taggen och dess värde](scripts/enforce-tag-val.md) | Kräver angivna taggnamn och värde. Du kan ange taggnamn och värde för att genomdriva.  |
| [Inte tillåtet resurstyper](scripts/not-allowed-res-type.md) | Förhindrar att distributionen av angivna resurstyper. Du anger en matris med resurstyper att blockera.  |
| [Kräv SQL Server version 12.0](scripts/req-sql-12.md) | Kräver SQL-servrar att använda versionen 12.0.  |
| [Kräv kryptering för storage-konto](scripts/req-store-acct-enc.md) | Kräver storage-konto använder blob-kryptering.  |
