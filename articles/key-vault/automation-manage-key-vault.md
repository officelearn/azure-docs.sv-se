---
title: Hantera Azure Key Vault med hjälp av Azure Automation | Microsoft Docs
description: Läs mer om hur Azure Automation-tjänsten kan användas för att hantera Azure Key Vault.
services: Key-Vault, automation
documentationcenter: ''
author: mgoedtel
manager: jwhit
editor: ''
ms.assetid: 4e780762-19b6-4ca6-b894-ebb44c538f35
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2016
ms.author: magoedte
ms.openlocfilehash: b5f8a1b826717d51729b0bb621bf26e35d4bdd36
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="managing-azure-key-vault-using-azure-automation"></a>Hantera Azure Key Vault med hjälp av Azure Automation
Den här guiden innehåller en introduktion till Azure Automation-tjänsten och hur det kan användas för att förenkla hanteringen av dina nycklar och hemligheter i Azure Key Vault.

## <a name="what-is-azure-automation"></a>Vad är Azure Automation?
[Azure Automation](../automation/automation-intro.md) är en Azure-tjänst som förenklar molnhantering via automatisering och önskad tillståndskonfiguration. Med hjälp av Azure Automation, manuell, kan upprepas tidskrävande och felbenägna aktiviteter automatiseras för att öka tillförlitligheten, effektivitet och tid för värde för din organisation.

Azure Automation ger en Körningsmotor för arbetsflöden för mycket tillförlitliga, hög tillgänglighet som kan skalas för att uppfylla dina behov. I Azure Automation kan processer vara inletts manuellt av 3 part eller med schemalagda intervall så att aktiviteter inträffa exakt vid behov.

Minska operativ tillsyn och frigör IT och DevOps-personal att fokusera på arbete som lägger till affärsvärde genom att flytta dina uppgifter för hantering av molnet att köras automatiskt av Azure Automation.

## <a name="how-can-azure-automation-help-manage-azure-key-vault"></a>Hur Azure Automation kan hantera Azure Key Vault?
Key Vault kan hanteras i Azure Automation med hjälp av den [AzureRM Key Vault-cmdlets](https://www.powershellgallery.com/packages/AzureRM.KeyVault/1.1.4) och [Azure klassiska Key Vault-cmdlets](https://msdn.microsoft.com/library/azure/dn868052.aspx). Azure-modulen för hantering av den klassiska Key Vault är tillgängligt i Azure Automation automatiskt och du kan importera den [AzureRM KeyVault modulen](https://www.powershellgallery.com/packages/AzureRM.KeyVault/1.1.4) i Azure Automation så att du kan utföra många av din Key Vault hanteringsuppgifter i tjänsten. Du kan också koppla dessa cmdlets i Azure Automation med cmdlets för andra Azure-tjänster, att automatisera avancerade uppgifter över Azure-tjänster och 3 part.

Du kan utföra dessa uppgifter, bland annat med Azure Key Vault-cmdlets: 

* Skapa och konfigurera ett nyckelvalv
* Skapa eller importera en nyckel
* Skapa eller uppdatera en hemlighet
* Uppdatera attribut för en nyckel
* Hämta en nyckel eller hemlighet.
* Ta bort en nyckel eller hemlighet.

Här följer några exempel på att använda PowerShell för att hantera Key Vault:  

* [Azure Key Vault - steg-för-steg](https://blogs.technet.microsoft.com/kv/2015/06/02/azure-key-vault-step-by-step)
* [Installera och konfigurera en Azure Key Vault](https://www.simple-talk.com/cloud/platform-as-a-service/setting-up-and-configuring-an-azure-key-vault)

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna i Azure Automation och hur det kan användas för att hantera Azure Key Vault kan du följa dessa länkar om du vill veta mer om Azure Automation.

* Se Azure Automation [komma igång-självstudiekurs](../automation/automation-first-runbook-graphical.md).
* Finns det [Azure Key Vault PowerShell-skript](https://gallery.technet.microsoft.com/scriptcenter/site/search?query=azure%20key%20vault&f%5B0%5D.Value=azure%20key%20vault&f%5B0%5D.Type=SearchText&ac=5).

