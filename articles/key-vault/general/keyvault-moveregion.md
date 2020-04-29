---
title: Azure Key Vault att flytta ett valv till en annan region | Microsoft Docs
description: Vägledning om hur du flyttar ett nyckel valv till en annan region.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to move my vault to another region.
ms.openlocfilehash: e65a723ac9daafdc09896a50e197034104408df2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82254153"
---
# <a name="moving-an-azure-key-vault-across-regions"></a>Flytta en Azure Key Vault över flera regioner

## <a name="overview"></a>Översikt

Key Vault har inte stöd för en resurs flyttnings åtgärd som tillåter att ett nyckel valv flyttas till en annan region. Den här artikeln beskriver lösningar om du har ett företag som behöver flytta ett nyckel valv till en annan region. Varje alternativ har begränsningar och det är viktigt att förstå konsekvenserna av dessa lösningar innan du provar dem i en produktions miljö.

Om du behöver flytta ett nyckel valv till en annan region, är lösningen att skapa ett nytt nyckel valv i önskad region och manuellt kopiera över varje enskild hemlighet från ditt befintliga nyckel valv till det nya nyckel valvet. Den här åtgärden kan utföras på något av följande sätt som anges nedan.

## <a name="design-considerations"></a>Designanmärkningar

* Key Vault namn är globalt unika. Du kommer inte att kunna återanvända samma valv namn.

* Du måste konfigurera om åtkomst principer och nätverks konfigurations inställningar i det nya nyckel valvet.

* Du måste konfigurera om mjuk borttagnings-och rensnings skyddet i det nya nyckel valvet.

* Säkerhets kopierings-och återställnings åtgärden kommer inte att bevara inställningarna för autorotation som du kan behöva konfigurera om inställningarna.

## <a name="option-1---use-the-key-vault-backup-and-restore-commands"></a>Alternativ 1 – Använd säkerhets kopierings-och återställnings kommandona för Key Vault

Du kan säkerhetskopiera varje enskild hemlighet, nyckel och certifikat i valvet med hjälp av säkerhets kopierings kommandot. Dina hemligheter kommer att hämtas som en krypterad blob. Du kan sedan återställa blobben till det nya nyckel valvet. Kommandona är dokumenterade i länken nedan.

[Azure Key Vault kommandon](https://docs.microsoft.com/powershell/module/azurerm.keyvault/?view=azurermps-6.13.0#key_vault)

### <a name="limitations"></a>Begränsningar

* Du kan inte säkerhetskopiera ett nyckel valv i en geografi och återställa det till ett annat geografiskt område. Lär dig mer om Azures geografiska områden. [Länk](https://azure.microsoft.com/global-infrastructure/geographies/)

* Säkerhets kopierings kommandot säkerhetskopierar alla versioner av varje hemlighet. Om du har en hemlighet med ett stort antal tidigare versioner (större än 10) är det en risk att begäran överskrider den maximala tillåtna storlek och att åtgärden kan Miss lyckas.

## <a name="option-2---manually-download-and-upload-secrets"></a>Alternativ 2 – Hämta och ladda ned hemligheter manuellt

Vissa typer av hemliga typer kan hämtas manuellt. Du kan till exempel Ladda ned certifikat som en PFX-fil. Med det här alternativet elimineras de geografiska begränsningarna för vissa typer av hemliga typer, t. ex. certifikat. Du kan ladda upp PFX-filerna till alla nyckel valv i vilken region som helst. Din hemlighet kommer att hämtas i ett format som inte är lösenordsskyddad. Du ansvarar för att skydda dina hemligheter när de lämnar Key Vault medan flyttningen utförs.
