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
ms.openlocfilehash: 69fd27785e5fc16a79fc23728b6d1e50a0a7b834
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/19/2018
ms.locfileid: "53632865"
---
# <a name="powershell-samples-for-azure-app-service"></a>PowerShell-exempel för Azure App Service

Följande tabell innehåller länkar till PowerShell-skript som skapats med Azure PowerShell.

| | |
|-|-|
|**Skapa app**||
| [Skapa en app med distribution från GitHub](./scripts/powershell-deploy-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapar en App Service-app som hämtar kod från GitHub. |
| [Skapa en app med kontinuerlig distribution från GitHub](./scripts/powershell-continuous-deployment-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapar en App Service-app som kontinuerligt distribuerar kod från GitHub. |
| [Skapa en app och distribuera kod med FTP](./scripts/powershell-deploy-ftp.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapar en App Service-app och laddar upp filer från en lokal katalog med hjälp av FTP. |
| [Skapa en app och distribuera kod från en lokal Git-lagringsplats](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapar en App Service-app och konfigurerar kodöverföring från en lokal Git-lagringsplats. |
| [Skapa en app och distribuera kod till en mellanlagringsmiljö](./scripts/powershell-deploy-staging-environment.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapar en App Service-app med ett distributionsfack för att mellanlagra kodändringar. |
|**Konfigurera app**||
| [Mappa en anpassad domän till en app](./scripts/powershell-configure-custom-domain.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapar en App Service-app och mappar ett anpassat domännamn till den. |
| [Binda ett anpassat SSL-certifikat till en app](./scripts/powershell-configure-ssl-certificate.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapar en App Service-app och binder SSL-certifikatet för ett anpassat domännamn till den. |
|**Skala app**||
| [Skala en app manuellt](./scripts/powershell-scale-manual.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapar en App Service-app och skalar den över 2 instanser. |
| [Skala en app globalt med en arkitektur för hög tillgänglighet](./scripts/powershell-scale-high-availability.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapar två App Service-appar i två olika geografiska regioner och gör dem tillgängliga via en enskild slutpunkt med Azure Traffic Manager. |
|**Anslut app till resurser**||
| [Anslut en webbapp till en SQL-databas](./scripts/powershell-connect-to-sql.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapar en App Service-app och en SQL-databas och lägger sedan till dataanslutningssträngen till appinställningarna. |
| [Anslut en app till ett lagringskonto](./scripts/powershell-connect-to-storage.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapar en App Service-app och ett lagringskonto och lägger sedan till lagringsanslutningssträngen till appinställningarna. |
|**Säkerhetskopiera och återställ app**||
| [Säkerhetskopiera en app](./scripts/powershell-backup-onetime.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapar en App Service-app och en engångssäkerhetskopia för den. |
| [Skapa en schemalagd säkerhetskopiering för en app](./scripts/powershell-backup-scheduled.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapar en App Service-app och skapar en schemalagd säkerhetskopia för den. |
| [Ta bort en säkerhetskopia för en app](./scripts/powershell-backup-delete.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tar bort en befintlig säkerhetskopia för en app. |
| [Återställ en app från säkerhetskopia](./scripts/powershell-backup-restore.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Återställer en app från en tidigare slutförd säkerhetskopia. |
| [Återställ en säkerhetskopia över prenumerationer](./scripts/powershell-backup-restore-diff-sub.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Återställer en webbapp från en säkerhetskopia i en annan prenumeration. |
|**Övervaka app**||
| [Övervaka en app med webbserverloggar](./scripts/powershell-monitor.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapar en App Service-app, aktiverar loggning för den och hämtar loggarna till den lokala datorn. |
| | |
