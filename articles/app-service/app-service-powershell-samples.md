---
title: Azure PowerShell-exempel – App Service | Microsoft Docs
description: Azure PowerShell-exempel – App Service
services: app-service
documentationcenter: app-service
author: syntaxc4
manager: erikre
editor: ggailey777
tags: azure-service-management
ms.assetid: b48d1137-8c04-46e0-b430-101e07d7e470
ms.service: app-service
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 03/08/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 211284f4eff112b9aebec7fa1a031f292a4a92f4
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2018
ms.locfileid: "52290747"
---
# <a name="azure-powershell-samples"></a>Azure PowerShell-exempel

Följande tabell innehåller länkar till PowerShell-skript som skapats med Azure PowerShell.

| | |
|-|-|
|**Skapa app**||
| [Skapa en app med distribution från GitHub](./scripts/app-service-powershell-deploy-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapar en Azure-webbapp som hämtar kod från GitHub. |
| [Skapa en app med kontinuerlig distribution från GitHub](./scripts/app-service-powershell-continuous-deployment-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapar en Azure-webbapp som kontinuerligt distribuerar kod från GitHub. |
| [Skapa en app och distribuera kod med FTP](./scripts/app-service-powershell-deploy-ftp.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapar en Azure-webapp och laddar upp filer från en lokal katalog med hjälp av FTP. |
| [Skapa en app och distribuera kod från en lokal Git-lagringsplats](./scripts/app-service-powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapar en Azure-webbapp och konfigurerar kodöverföring från en lokal Git-lagringsplats. |
| [Skapa en app och distribuera kod till en mellanlagringsmiljö](./scripts/app-service-powershell-deploy-staging-environment.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapar en Azure-webbapp med ett distributionsfack för att mellanlagra kodändringar. |
|**Konfigurera app**||
| [Mappa en anpassad domän till en app](./scripts/app-service-powershell-configure-custom-domain.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapar en Azure-webbapp och mappar ett anpassat domännamn till den. |
| [Binda ett anpassat SSL-certifikat till en app](./scripts/app-service-powershell-configure-ssl-certificate.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapar en Azure-webbapp och binder SSL-certifikatet för ett anpassat domännamn till den. |
|**Skala app**||
| [Skala en app manuellt](./scripts/app-service-powershell-scale-manual.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapar en Azure-webbapp och skalar den över 2 instanser. |
| [Skala en app globalt med en arkitektur för hög tillgänglighet](./scripts/app-service-powershell-scale-high-availability.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapar två Azure-webbappar i två olika geografiska regioner och gör dem tillgängliga via en enskild slutpunkt med Azure Traffic Manager. |
|**Anslut app till resurser**||
| [Anslut en webbapp till en SQL-databas](./scripts/app-service-powershell-connect-to-sql.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapar en Azure-webbapp och en SQL-databas och lägger sedan till dataanslutningssträngen till appinställningarna. |
| [Anslut en app till ett lagringskonto](./scripts/app-service-powershell-connect-to-storage.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapar en Azure-webbapp och ett lagringskonto och lägger sedan till lagringsanslutningssträngen till appinställningarna. |
|**Säkerhetskopiera och återställ app**||
| [Säkerhetskopiera en app](./scripts/app-service-powershell-backup-onetime.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapar en Azure-webbapp och en engångssäkerhetskopia för den. |
| [Skapa en schemalagd säkerhetskopiering för en app](./scripts/app-service-powershell-backup-scheduled.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapar en Azure-webbapp och en schemalagd säkerhetskopia för den. |
| [Ta bort en säkerhetskopia för en app](./scripts/app-service-powershell-backup-delete.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tar bort en befintlig säkerhetskopia för en app. |
| [Återställ en app från säkerhetskopia](./scripts/app-service-powershell-backup-restore.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Återställer en app från en tidigare slutförd säkerhetskopia. |
| [Återställ en säkerhetskopia över prenumerationer](./scripts/app-service-powershell-backup-restore-diff-sub.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Återställer en webbapp från en säkerhetskopia i en annan prenumeration. |
|**Övervaka app**||
| [Övervaka en app med webbserverloggar](./scripts/app-service-powershell-monitor.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapar en Azure-webbapp, aktiverar loggning för den och laddar ned loggarna till den lokala datorn. |
| | |
