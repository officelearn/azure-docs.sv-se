---
title: Säkerhets kontroller för Azure App Service
description: En check lista över säkerhets kontroller för utvärdering av Azure App Service
services: app-service
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: mbaldwin
ms.openlocfilehash: 8fd4d19fa2c95107990fc7942e673ca5ad719df3
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/06/2019
ms.locfileid: "70743788"
---
# <a name="security-controls-for-azure-app-service"></a>Säkerhets kontroller för Azure App Service

I den här artikeln dokumenteras de säkerhets kontroller som är inbyggda i Azure App Service.

[!INCLUDE [Security attributes header](../../includes/security-attributes-header.md)]

## <a name="network"></a>Nätverk

| Säkerhets kontroll | Ja/nej | Anteckningar | Dokumentation
|---|---|--|
| Stöd för tjänst slut punkt| Ja | För närvarande tillgängligt som för hands version för App Service.| [Begränsningar för Azure App Service åtkomst](app-service-ip-restrictions.md)
| Stöd för VNet-injektering| Ja | App Service miljöer är privata implementeringar av App Service dedikerade till en enskild kund som injiceras i ett kunds virtuella nätverk. | [Introduktion till App Service miljöer](environment/intro.md)
| Stöd för nätverks isolering och brand vägg| Ja | För den offentliga variationen av flera innehavare av App Service kan kunder konfigurera nätverks-ACL: er (IP-begränsningar) för att låsa tillåten inkommande trafik.  App Service miljöer distribueras direkt till virtuella nätverk och kan därför skyddas med NSG: er. | [Begränsningar för Azure App Service åtkomst](app-service-ip-restrictions.md)
| Stöd för Tvingad tunnel trafik| Ja | App Service miljöer kan distribueras till en kunds virtuella nätverk där Tvingad tunnel trafik har kon figurer ATS. | [Konfigurera App Service-miljö med tvingande dirigering](environment/forced-tunnel-support.md)

## <a name="monitoring--logging"></a>Övervaka & loggning

| Säkerhets kontroll | Ja/nej | Anteckningar | Dokumentation
|---|---|--|
| Azure Monitoring support (Log Analytics, App Insights osv.)| Ja | App Service integreras med Application Insights för språk som stöder Application Insights (fullständig .NET Framework, .NET Core, Java och Node. JS).  Se [övervaka Azure App Service prestanda](../azure-monitor/app/azure-web-apps.md). App Service skickar även program statistik till Azure Monitor. | [Övervaka appar i Azure App Service](web-sites-monitor.md)
| Loggning och granskning av kontroll-och hanterings plan| Ja | Alla hanterings åtgärder som utförs på App Service objekt sker via [Azure Resource Manager](../azure-resource-manager/index.yml). Historiska loggar för dessa åtgärder är tillgängliga både i portalen och via CLI. | [Azure Resource Manager Resource Provider-åtgärder](../role-based-access-control/resource-provider-operations.md#microsoftweb), [AZ övervaka aktivitet – logg](/cli/azure/monitor/activity-log)
| Loggning och granskning av data planet | Nej | Data planet för App Service är en fjärran sluten fil resurs som innehåller en kunds distribuerad webbplats innehåll.  Det finns ingen granskning av fjärran sluten fil resurs. |

## <a name="identity"></a>Identitet

| Säkerhets kontroll | Ja/nej | Anteckningar |  Dokumentation
|---|---|--|
| Authentication| Ja | Kunder kan bygga program på App Service som automatiskt integreras med [Azure Active Directory (Azure AD)](../active-directory/index.yml) samt andra OAuth-kompatibla identitets leverantörer för hanterings åtkomst till App Service till gångar, och all åtkomst kontrol leras av en kombination av Azure AD-autentiserade huvud konton och Azure Resource Manager RBAC-roller. | [Autentisering och auktorisering i Azure App Service](overview-authentication-authorization.md)
| Authorization| Ja | För hanterings åtkomst till App Service till gångar styrs all åtkomst av en kombination av Azure AD-autentiserade huvud konton och Azure Resource Manager RBAC-roller.  | [Autentisering och auktorisering i Azure App Service](overview-authentication-authorization.md)

## <a name="data-protection"></a>Dataskydd

| Säkerhets kontroll | Ja/nej | Anteckningar | Dokumentation
|---|---|--|
| Kryptering på Server sidan på rest: Microsoft-hanterade nycklar | Ja | Innehållet på webbplats filen lagras i Azure Storage, vilket automatiskt krypterar innehållet i vila. <br><br>Kunder som har tillhandahållit hemligheter är krypterade i vila. Hemligheterna är krypterade i rest medan de lagras i App Service konfigurations databaser.<br><br>Lokalt anslutna diskar kan alternativt användas som temporär lagring av webbplatser (D:\Local och% TMP%). Lokalt anslutna diskar är inte krypterade i vila. | [Azure Storage kryptering för vilande data](../storage/common/storage-service-encryption.md)
| Kryptering på Server sidan på rest: Kundhanterade nycklar (BYOK) | Ja | Kunder kan välja att lagra program hemligheter i Key Vault och hämta dem vid körning. | [Använda Key Vault referenser för App Service och Azure Functions (förhands granskning)](app-service-key-vault-references.md)
| Kryptering på kolumn nivå (Azure Data Services)| Gäller inte | |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i VNet-kryptering och VNet-VNet-kryptering)| Ja | Kunder kan konfigurera webbplatser för att kräva och använda HTTPS för inkommande trafik.  | [Så här gör du endast Azure App Service https](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/) (blogg inlägg)
| Krypterade API-anrop| Ja | Hanterings anrop för att konfigurera App Service sker via [Azure Resource Manager](../azure-resource-manager/index.yml) -anrop via https. |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhets kontroll | Ja/nej | Anteckningar | Dokumentation
|---|---|--|
| Konfigurations hanterings stöd (konfigurations version osv.)| Ja | För hanterings åtgärder kan tillstånd för en App Service-konfiguration exporteras som en Azure Resource Manager mall och versions hantering över tid. För körnings åtgärder kan kunder underhålla flera olika Live-versioner av ett program med hjälp av funktionen för App Service distributions platser. | 

