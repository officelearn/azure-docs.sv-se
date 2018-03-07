---
title: "Azure Resource Manager-mallexempel – App Service | Microsoft Docs"
description: "Azure Resource Manager-mallexempel – App Service"
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
ms.openlocfilehash: e28a27b9a00164fcbba6eb5d3e5435548486ffa4
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2018
---
# <a name="azure-resource-manager-templates-for-azure-web-apps"></a>Azure Resource Manager-mallar för Azure Web Apps

Följande tabell innehåller länkar till Azure Resource Manager-mallar. Rekommendationer om hur du undviker vanliga fel när du skapar webprogrammallar finns i dokumentationen med [vägledning om att distribuera webbprogram med Azure Resource Manager-mallar](web-sites-rm-template-guidance.md).

| | |
|-|-|
|**Distribuera Web App**||
| [Webbprogram som är kopplade till en GitHub-lagringsplats](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-github-deploy)| Distribuerar ett Azure-webbprogram som tar emot kod från GitHub. |
| [Web App med anpassade distributionsplatser](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-custom-deployment-slots)| Distribuerar ett Azure-webbprogram med anpassade distributionsfack/miljöer. |
|**Konfigurera webbprogrammet**||
| [Webbprogramcertifikat från Key Vault](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault)| Distribuerar ett Azure-webprogramcertifikat från Key Vault (hemligt) och använder det för SSL-bindning. |
| [Web App med anpassad domän](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain)| Distribuerar ett Azure-webbprogram med ett anpassat värdnamn. |
| [Webbprogram med anpassad domän och SSL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain-and-ssl)| Distribuerar ett Azure-webbprogram med ett anpassat värdnamn och hämtar certifikat för webbprogram från Nyckelvalvet för SSL-bindning. |
| [Webbprogram med GoLang-tillägg](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-with-golang)| Distribuerar ett Azure-webbprogram med Golang-webbplatstillägg, där du kan köra webbprogram som har utvecklats på Golang på Azure. |
| [Webbprogram med Java 8 och Tomcat 8](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-java-tomcat)| Distribuerar ett Azure-webbprogram med Java 8 och Tomcat 8 aktiverad, vilket gör att du kan köra Java-program i Azure. |
|**Linux-webbprogram**||
| [Web App on Linux med MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-mysql) | Distribuerar ett Azure-webbprogram på Linux med Azure-databas för MySQL. |
| [Web App on Linux med PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-postgresql) | Distribuerar ett Azure-webbprogram på Linux med Azure-databas för PostgreSQL. |
|**Webbprogram med anslutna resurser**||
| [Web App med MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-mysql)| Distribuerar ett Azure-webbprogram i Windows med Azure-databas för MySQL. |
| [Webbprogram med PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-postgresql)| Distribuerar ett Azure-webbprogram i Windows med Azure-databas för PostgreSQL. |
| [Webbprogram med en SQL-databas](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database)| Distribuerar ett Azure-webbprogram och SQL-databas på grundtjänstnivå. |
| [Webbprogram med Blob-lagringsanslutning](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-blob-connection)| Distribuerar ett Azure-webbprogram med en blob anslutningssträngen för lagring, där du kan använda Azure Blob Storage från webbprogrammet. |
| [Web App med Redis Cache](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-redis-cache)| Distribuera ett Azure-webbprogram med Redis Cache. |
|**App Service Environment**||
| [Skapa App Service Environment v2](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-create) | Skapar App Service Environment v2 i det lokala nätverket. |
| [Skapa App Service Environment v2 med ILB-adress](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-ilb-create/) | Skapar en App Service Environment v2 i det virtuella nätverket med en privat intern adress för belastningsutjämnaren. |
| [Konfigurera standard-SSL-certifikatet för ILB ASE eller ILB ASE v2](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-ase-ilb-configure-default-ssl) | Konfigurerar standard-SSL-certifikatet för ILB App Service Environment eller ILB App Service Environment v2 |
| | |
