---
title: Hantera Azure Key Vault med Azure Automation | Microsoft Docs
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
ms.topic: conceptual
ms.date: 07/29/2016
ms.author: magoedte
ms.openlocfilehash: be7829c35f299381a5e82621412feb13cc2e8bb2
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2018
ms.locfileid: "44299244"
---
# <a name="managing-azure-key-vault-using-azure-automation"></a>Hantera Azure Key Vault med Azure Automation
Den här guiden innehåller en introduktion till Azure Automation-tjänsten och hur den kan användas för att förenkla hanteringen av dina nycklar och hemligheter i Azure Key Vault.

## <a name="what-is-azure-automation"></a>Vad är Azure Automation?
[Azure Automation](../automation/automation-intro.md) är en Azure-tjänst för att förenkla molnhantering med Processautomatisering och önskad tillståndskonfiguration. Med hjälp av Azure Automation, manuell, kan upprepas, tidskrävande och felbenägna aktiviteter automatiseras för att öka tillförlitligheten, effektivitet och tid till värde för din organisation.

Azure Automation tillhandahåller en mycket pålitlig, högtillgängliga motor för arbetsflödeskörning som kan skalas efter dina behov. I Azure Automation, kan processer vara startats manuellt, med 3 part eller med schemalagda intervall så att uppgifter inträffa exakt när det behövs.

Minska driftsomkostnader och frigör IT och DevOps-personal att fokusera på arbete som ger ett mervärde för verksamheten genom att flytta din molnhanteringsuppgifter att köras automatiskt av Azure Automation.

## <a name="how-can-azure-automation-help-manage-azure-key-vault"></a>Hur kan Azure Automation till att hantera Azure Key Vault?
Key Vault kan hanteras i Azure Automation med hjälp av den [AzureRM Key Vault-cmdlets](https://www.powershellgallery.com/packages/AzureRM.KeyVault/1.1.4) och [Azure klassiska Key Vault-cmdlets](https://docs.microsoft.com/powershell/module/servicemanagement/azure). Azure-modulen för att hantera klassiska Key Vault är tillgängligt i Azure Automation automatiskt och du kan importera den [AzureRM-KeyVault-modulen](https://www.powershellgallery.com/packages/AzureRM.KeyVault/1.1.4) i Azure Automation, så att du kan utföra många av dina uppgifter för hantering av Key Vault i tjänsten. Du kan också koppla dessa cmdletar i Azure Automation med cmdlets för andra Azure-tjänster, att automatisera avancerade uppgifter över Azure-tjänster och system med 3 part.

Du kan utföra dessa uppgifter, bland annat med Azure Key Vault-cmdlets: 

* Skapa och konfigurera ett nyckelvalv
* Skapa eller importera en nyckel
* Skapa eller uppdatera en hemlighet
* Uppdatera attribut för en nyckel
* Hämta en nyckel eller hemlighet
* Ta bort en nyckel eller hemlighet

Här följer några exempel på använder PowerShell för att hantera Nyckelvalv:  

* [Azure Key Vault - steg-för-steg](https://blogs.technet.microsoft.com/kv/2015/06/02/azure-key-vault-step-by-step)
* [Att installera och konfigurera ett Azure Key Vault](https://www.simple-talk.com/cloud/platform-as-a-service/setting-up-and-configuring-an-azure-key-vault)

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna i Azure Automation och hur den kan användas för att hantera Azure Key Vault kan du följa dessa länkar om du vill veta mer om Azure Automation.

* Finns i Azure Automation [komma igång-självstudiekurs](../automation/automation-first-runbook-graphical.md).
* Se den [Azure Key Vault PowerShell-skript](https://gallery.technet.microsoft.com/scriptcenter/site/search?query=azure%20key%20vault&f%5B0%5D.Value=azure%20key%20vault&f%5B0%5D.Type=SearchText&ac=5).

