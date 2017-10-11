---
title: "Azure PowerShell-exempel - Apptjänst | Microsoft Docs"
description: "Azure PowerShell-exempel - Apptjänst"
services: app-service
documentationcenter: app-service
author: syntaxc4
manager: erikre
editor: ggailey777
tags: azure-service-management
ms.assetid: b48d1137-8c04-46e0-b430-101e07d7e470
ms.service: app-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 03/08/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 3254fdd57cfcd170f22374c1e3b058e6081d8e8e
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="azure-powershell-samples"></a>Azure PowerShell-exempel

Följande tabell innehåller länkar till bash-skript som skapats med hjälp av Azure PowerShell.

| | |
|-|-|
|**Skapa app**||
| [Skapa en webbapp med distribution från GitHub](./scripts/app-service-powershell-deploy-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapar en Azure-app som tar emot kod från GitHub. |
| [Skapa en webbapp med kontinuerlig distribution från GitHub](./scripts/app-service-powershell-continuous-deployment-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapar ett Azure-webbapp som kontinuerligt distribuerar koden från GitHub. |
| [Skapa en webbapp och distribuera kod med FTP](./scripts/app-service-powershell-deploy-ftp.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapar ett Azure web app och överföra filer från en lokal katalog med hjälp av FTP. |
| [Skapa en webbapp och distribuera kod från en lokal Git-databas](./scripts/app-service-powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapar ett Azure-webbapp och konfigurerar push kod från en lokal Git-lagringsplats. |
| [Skapa en webbapp och distribuera kod till en mellanlagringsmiljö](./scripts/app-service-powershell-deploy-staging-environment.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapar en Azure-app med en distributionsplats för mellanlagring kodändringar. |
|**Konfigurera appen**||
| [Mappa en anpassad domän till en webbapp](./scripts/app-service-powershell-configure-custom-domain.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapar en Azure webbapp och mappa ett anpassat domännamn till den. |
| [Koppla en anpassad SSL-certifikatet till en webbapp](./scripts/app-service-powershell-configure-ssl-certificate.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapar en Azure webbapp och binda SSL-certifikatet för ett anpassat domännamn till den. |
|**Skala appen**||
| [Skala en webbapp manuellt](./scripts/app-service-powershell-scale-manual.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapar en Azure webbapp och skalas över 2 instanser. |
| [Skala en webbapp över hela världen med en arkitektur med hög tillgänglighet](./scripts/app-service-powershell-scale-high-availability.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapar två Azure-webbappar i två olika geografiska regioner och gör dem tillgängliga via en enda slutpunkt med hjälp av Azure Traffic Manager. |
|**Ansluta appen till resurser**||
| [Ansluta en webbapp till en SQL-databas](./scripts/app-service-powershell-connect-to-sql.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapar ett Azure webbapp och en SQL-databas och sedan lägger till databasanslutningssträngen app-inställningar. |
| [Ansluta en webbapp till ett lagringskonto](./scripts/app-service-powershell-connect-to-storage.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapar ett Azure webbapp och ett lagringskonto och sedan lägger till lagringsanslutningssträngen i app-inställningar. |
|**Övervaka app**||
| [Övervaka en webbapp med webbserverloggar](./scripts/app-service-powershell-monitor.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapar ett Azure-webbapp, aktiverar loggning för den och hämtar loggarna till din lokala dator. |
| | |
