---
title: Azure Resource Manager-mallexempel
description: Hitta mallexempel för Azure Resource Manager för några av de vanliga App Service-scenarierna. Läs om hur du automatiserar distributions- eller hanteringsuppgifterna för App Service.
author: tfitzmac
tags: azure-service-management
ms.topic: sample
ms.date: 01/04/2019
ms.author: tomfitz
ms.custom: mvc
ms.openlocfilehash: b1d5f20ccd2f2c637d7db668af10ef331947d018
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74971204"
---
# <a name="azure-resource-manager-templates-for-app-service"></a>Azure Resource Manager-mallar för App Service

Följande tabell innehåller länkar till Azure Resource Manager-mallar för Azure App Service. Rekommendationer om hur du undviker vanliga fel när du skapar appmallar finns i avsnittet med [vägledning för att distribuera appar med Azure Resource Manager-mallar](deploy-resource-manager-template.md).

Mer information om JSON-syntaxen och egenskaper för App Services-resurser finns på sidan med [Microsoft.Web-resurstyper](/azure/templates/microsoft.web/allversions).

| | |
|-|-|
|**Distribuera en app**||
| [App Service plan och enkel Linux-app](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-basic-linux) | Distribuerar en App Service-app som är konfigurerad för Linux. |
| [App Service plan och enkel Windows-app](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-basic-windows) | Distribuerar en App Service-app som är konfigurerad för Windows. |
| [App som är länkad till en GitHub-lagringsplats](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-github-deploy)| Distribuerar en App Service-app som hämtar kod från GitHub. |
| [App med anpassade distributionsfack](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-custom-deployment-slots)| Distribuerar en App Service-app med anpassade distributionsfack/miljöer. |
|**Konfigurera en app**||
| [Appcertifikat från Key Vault](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault)| Distribuerar ett App Service-appcertifikat från en Azure Key Vault-hemlighet och använder den för SSL-bindning. |
| [App med en anpassad domän](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain)| Distribuerar en App Service-app med ett anpassat värdnamn. |
| [App med en anpassad domän och SSL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain-and-ssl)| Distribuerar en App Service-app med ett anpassat värdnamn och hämtar ett appcertifikat från Key Vault för SSL-bindning. |
| [App med ett GoLang-tillägg](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-with-golang)| Distribuerar en App Service-app med Golang-platstillägget. Du kan sedan köra webbprogram som har utvecklats för Golang på Azure. |
| [App med Java 8 och Tomcat 8](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-java-tomcat)| Distribuerar en App Service-app med Java 8 och Tomcat 8 aktiverat. Du kan sedan köra Java-program i Azure. |
|**Skydda en app**||
| [App integrerad med Azure Application Gateway](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-app-gateway-v2)| Distribuerar en App Service-app och en Programgateway och isolerar trafiken med hjälp av tjänstslutpunkt och åtkomstbegränsningar. |
|**Linux-app med anslutna resurser**||
| [App i Linux med MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-mysql) | Distribuerar en App Service-app i Linux med Azure Database for MySQL. |
| [App i Linux med PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-postgresql) | Distribuerar en App Service-app i Linux med Azure Database for PostgreSQL. |
|**App med anslutna resurser**||
| [App med MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-mysql)| Distribuerar en App Service-app i Windows med Azure Database for MySQL. |
| [App med PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-postgresql)| Distribuerar en App Service-app i Windows med Azure Database for PostgreSQL. |
| [App med en SQL-databas](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database)| Distribuerar en App Service-app och en SQL-databas på tjänstnivån Basic. |
| [App med Blob-lagringsanslutning](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-blob-connection)| Distribuerar en App Service-app med en Azure Blob Storage-anslutningssträng. Du kan sedan använda Blob-lagring från appen. |
| [App med en Azure Cache for Redis](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-redis-cache)| Distribuerar en App Service-app med en Azure Cache for Redis. |
|**App Service-miljön**||
| [Skapa en App Service-miljö v2](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-create) | Skapar en App Service-miljö v2 i det virtuella nätverket. |
| [Skapa en App Service-miljö v2 med en ILB-adress](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-ilb-create/) | Skapar en App Service-miljö v2 i det virtuella nätverket med en privat intern adress för lastbalanseraren. |
| [Konfigurera SSL-standardcertifikatet för en ILB App Service-miljö eller ILB App Service-miljö v2](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-ase-ilb-configure-default-ssl) | Konfigurerar SSL-standardcertifikatet för en ILB App Service-miljö eller en ILB App Service-miljö v2. |
| | |
