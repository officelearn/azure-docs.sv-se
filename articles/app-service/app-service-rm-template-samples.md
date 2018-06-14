---
title: Azure Resource Manager-mallexempel – App Service | Microsoft Docs
description: Azure Resource Manager-mallexempel för funktionen Web Apps i App Service
services: app-service
documentationcenter: app-service
author: tfitzmac
manager: timlt
editor: ggailey777
tags: azure-service-management
ms.service: app-service
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 02/26/2018
ms.author: tomfitz
ms.custom: mvc
ms.openlocfilehash: 155b47fc4c664701ec0f21bdc5ae34f3d7f666ff
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
ms.locfileid: "29933076"
---
# <a name="azure-resource-manager-templates-for-web-apps"></a>Azure Resource Manager-mallar för Web Apps

Följande tabell innehåller länkar till Azure Resource Manager-mallar för Web Apps-funktionen i Azure App Service. Rekommendationer om hur du undviker vanliga fel när du skapar webbappmallar finns i [Vägledning för att distribuera webbappar med Azure Resource Manager-mallar](web-sites-rm-template-guidance.md).

| | |
|-|-|
|**Distribuera en webbapp**||
| [Webbapp som är länkad till en GitHub-lagringsplats](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-github-deploy)| Distribuerar ett Azure-webbprogram som tar emot kod från GitHub. |
| [Webbapp med anpassade distributionsfack](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-custom-deployment-slots)| Distribuerar ett Azure-webbprogram med anpassade distributionsfack/miljöer. |
|**Konfigurera en webbapp**||
| [Webbappcertifikat från Key Vault](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault)| Distribuerar ett Azure-webbappcertifikat från en Azure Key Vault-hemlighet och använder den för SSL-bindning. |
| [Webbapp med en anpassad domän](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain)| Distribuerar ett Azure-webbprogram med ett anpassat värdnamn. |
| [Webbapp med en anpassad domän och SSL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain-and-ssl)| Distribuerar en Azure-webbapp med ett anpassat värdnamn och hämtar ett certifikat för webbappar från Key Vault för SSL-bindning. |
| [Webbapp med ett GoLang-tillägg](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-with-golang)| Distribuerar en Azure-webbapp med GoLang-platstillägget. Du kan sedan köra webbprogram som har utvecklats för GoLang på Azure. |
| [Webbapp med Java 8 och Tomcat 8](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-java-tomcat)| Distribuerar en Azure-webbapp med Java 8 och Tomcat 8 aktiverat. Du kan sedan köra Java-program i Azure. |
|**Linux-webbapp**||
| [Webbapp i Linux med MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-mysql) | Distribuerar en Azure-webbapp i Linux med Azure Database for MySQL. |
| [Webbapp i Linux med PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-postgresql) | Distribuerar en Azure-webbapp i Linux med Azure Database for PostgreSQL. |
|**Webbapp med anslutna resurser**||
| [Webbapp med MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-mysql)| Distribuerar en Azure-webbapp i Windows med Azure Database for MySQL. |
| [Webbapp med PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-postgresql)| Distribuerar en Azure-webbapp i Windows med Azure Database for PostgreSQL. |
| [Webbapp med en SQL-databas](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database)| Distribuerar en Azure-webbapp och en SQL-databas på grundtjänstnivå. |
| [Webbapp med Blob Storage-anslutning](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-blob-connection)| Distribuerar en Azure-webbapp med en Azure Blob Storage-anslutningssträng. Du kan sedan använda Blob Storage från webbappen. |
| [Webbapp med Redis Cache](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-redis-cache)| Distribuera en Azure-webbapp med Redis Cache. |
|**App Service Environment för PowerApps**||
| [Skapa en App Service-miljö v2](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-create) | Skapar en App Service-miljö v2 i det virtuella nätverket. |
| [Skapa en App Service-miljö v2 med en ILB-adress](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-ilb-create/) | Skapar en App Service-miljö v2 i det virtuella nätverket med en privat intern adress för belastningsutjämnaren. |
| [Konfigurera SSL-standardcertifikatet för en ILB App Service-miljö eller ILB App Service-miljö v2](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-ase-ilb-configure-default-ssl) | Konfigurerar SSL-standardcertifikatet för en ILB App Service-miljö eller en ILB App Service-miljö v2. |
| | |
