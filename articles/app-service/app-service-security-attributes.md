---
title: Säkerhetsattribut för Azure App Service
description: En check lista över säkerhetsattribut för utvärdering av Azure App Service
services: app-service
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: mbaldwin
ms.openlocfilehash: 299262610c027529749840720f46d6dc97a07b21
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442257"
---
# <a name="security-attributes-for-azure-app-service"></a>Säkerhetsattribut för Azure App Service

I den här artikeln dokumenteras säkerhetsattributen som är inbyggda i Azure App Service.

[!INCLUDE [Security attributes header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Förebyggande

| Säkerhetsattribut | Ja/nej | Anteckningar |
|---|---|--|
| Kryptering i vila (t. ex. kryptering på Server sidan, kryptering på Server sidan med Kundhanterade nycklar och andra krypterings funktioner) | Ja | Innehållet på webbplats filen lagras i Azure Storage, vilket automatiskt krypterar innehållet i vila. Se [Azure Storage kryptering för vilande data](../storage/common/storage-service-encryption.md).<br><br>Kunder som har tillhandahållit hemligheter är krypterade i vila. Hemligheterna är krypterade i rest medan de lagras i App Service konfigurations databaser.<br><br>Lokalt anslutna diskar kan alternativt användas som temporär lagring av webbplatser (D:\Local och% TMP%). Lokalt anslutna diskar är inte krypterade i vila. |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i VNet-kryptering och VNet-VNet-kryptering)| Ja | Kunder kan konfigurera webbplatser för att kräva och använda HTTPS för inkommande trafik. Se blogg inlägget [så här gör du endast Azure App Service https](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/). |
| Hantering av krypterings nyckel (CMK, BYOK osv.)| Ja | Kunder kan välja att lagra program hemligheter i Key Vault och hämta dem vid körning. Se [använda Key Vault referenser för app service och Azure Functions (för hands version)](app-service-key-vault-references.md).|
| Kryptering på kolumn nivå (Azure Data Services)| Gäller inte | |
| Krypterade API-anrop| Ja | Hanterings anrop för att konfigurera App Service sker via [Azure Resource Manager](../azure-resource-manager/index.yml) -anrop via https. |

## <a name="network-segmentation"></a>Nätverks segmentering

| Säkerhetsattribut | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för tjänst slut punkt| Ja | För närvarande tillgängligt som för hands version för App Service. Se [begränsningar för Azure App Service åtkomst](app-service-ip-restrictions.md). |
| Stöd för VNet-injektering| Ja | App Service miljöer är privata implementeringar av App Service dedikerade till en enskild kund som injiceras i ett kunds virtuella nätverk. Se [Introduktion till App Service miljöer](environment/intro.md). |
| Stöd för nätverks isolering och brand vägg| Ja | För den offentliga variationen av flera innehavare av App Service kan kunder konfigurera nätverks-ACL: er (IP-begränsningar) för att låsa tillåten inkommande trafik.  Se [begränsningar för Azure App Service åtkomst](app-service-ip-restrictions.md).  App Service miljöer distribueras direkt till virtuella nätverk och kan därför skyddas med NSG: er. |
| Stöd för Tvingad tunnel trafik| Ja | App Service miljöer kan distribueras till en kunds virtuella nätverk där Tvingad tunnel trafik har kon figurer ATS. Kunderna måste följa anvisningarna i [Konfigurera din app service-miljön med Tvingad tunnel trafik](environment/forced-tunnel-support.md). |

## <a name="detection"></a>Identifiering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Azure Monitoring support (Log Analytics, App Insights osv.)| Ja | App Service integreras med Application Insights för språk som stöder Application Insights (fullständig .NET Framework, .NET Core, Java och Node. JS).  Se [övervaka Azure App Service prestanda](../azure-monitor/app/azure-web-apps.md). App Service skickar även program statistik till Azure Monitor. Se [övervaka appar i Azure App Service](web-sites-monitor.md). |

## <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Authentication| Ja | Kunder kan bygga program på App Service som automatiskt integreras med [Azure Active Directory (Azure AD)](../active-directory/index.yml) samt andra OAuth-kompatibla identitets leverantörer. Se [autentisering och auktorisering i Azure App Service](overview-authentication-authorization.md). För hanterings åtkomst till App Service till gångar styrs all åtkomst av en kombination av Azure AD-autentiserade huvud konton och Azure Resource Manager RBAC-roller. |
| Authorization| Ja | För hanterings åtkomst till App Service till gångar styrs all åtkomst av en kombination av Azure AD-autentiserade huvud konton och Azure Resource Manager RBAC-roller.  |


## <a name="audit-trail"></a>Gransknings logg

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Loggning och granskning av kontroll-och hanterings plan| Ja | Alla hanterings åtgärder som utförs på App Service objekt sker via [Azure Resource Manager](../azure-resource-manager/index.yml). Historiska loggar för dessa åtgärder är tillgängliga både i portalen och via CLI; Se [Azure Resource Manager Resource Provider Operations](../role-based-access-control/resource-provider-operations.md#microsoftweb) och [AZ Monitor Activity-Log](/cli/azure/monitor/activity-log). |
| Loggning och granskning av data planet | Nej | Data planet för App Service är en fjärran sluten fil resurs som innehåller en kunds distribuerad webbplats innehåll.  Det finns ingen granskning av fjärran sluten fil resurs. |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Konfigurations hanterings stöd (konfigurations version osv.)| Ja | För hanterings åtgärder kan tillstånd för en App Service-konfiguration exporteras som en Azure Resource Manager mall och versions hantering över tid. För körnings åtgärder kan kunder underhålla flera olika Live-versioner av ett program med hjälp av funktionen för App Service distributions platser. | 

