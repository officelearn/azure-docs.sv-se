---
title: Säkerhets kontroller för Azure App Service
description: Hitta en check lista med säkerhets kontroller för att utvärdera Azure App Service för din organisation.
author: msmbaldwin
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 93e5123e5b61c9013177f7f3c908578b68da52d6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "88962935"
---
# <a name="security-controls-for-azure-app-service"></a>Säkerhets kontroller för Azure App Service

I den här artikeln dokumenteras de säkerhets kontroller som är inbyggda i Azure App Service.

[!INCLUDE [Security controls header](../../includes/security-controls-header.md)]

## <a name="network"></a>Nätverk

| Säkerhets kontroll | Ja/nej | Obs! | Dokumentation
|---|---|--|
| Stöd för tjänst slut punkt| Ja | Tillgängligt för App Service.| [Begränsningar för Azure App Service åtkomst](app-service-ip-restrictions.md)
| Stöd för VNet-injektering| Ja | App Service miljöer är privata implementeringar av App Service dedikerade till en enskild kund som injiceras i ett kunds virtuella nätverk. | [Introduktion till Azure App Service-miljöer](environment/intro.md)
| Stöd för nätverks isolering och brand vägg| Ja | För den offentliga variationen av flera innehavare av App Service kan kunder konfigurera nätverks-ACL: er (IP-begränsningar) för att låsa tillåten inkommande trafik.  App Service miljöer distribueras direkt till virtuella nätverk och kan därför skyddas med NSG: er. | [Begränsningar för Azure App Service åtkomst](app-service-ip-restrictions.md)
| Stöd för Tvingad tunnel trafik| Ja | App Service miljöer kan distribueras till en kunds virtuella nätverk där Tvingad tunnel trafik har kon figurer ATS. | [Konfigurera App Service-miljö med tvingande dirigering](environment/forced-tunnel-support.md)

## <a name="monitoring--logging"></a>Övervaka & loggning

| Säkerhets kontroll | Ja/nej | Obs! | Dokumentation
|---|---|--|
| Azure Monitoring support (Log Analytics, App Insights osv.)| Ja | App Service integreras med Application Insights för språk som stöder Application Insights (fullständig .NET Framework, .NET Core, Java och Node.JS).  Se [övervaka Azure App Service prestanda](../azure-monitor/app/azure-web-apps.md). App Service skickar även program statistik till Azure Monitor. | [Övervaka appar i Azure App Service](web-sites-monitor.md)
| Loggning och granskning av kontroll-och hanterings plan| Ja | Alla hanterings åtgärder som utförs på App Service objekt sker via [Azure Resource Manager](../azure-resource-manager/index.yml). Historiska loggar för dessa åtgärder är tillgängliga både i portalen och via CLI. | [Azure Resource Manager Resource Provider-åtgärder](../role-based-access-control/resource-provider-operations.md#microsoftweb), [AZ övervaka aktivitet – logg](/cli/azure/monitor/activity-log)
| Loggning och granskning av data planet | Inga | Data planet för App Service är en fjärran sluten fil resurs som innehåller en kunds distribuerad webbplats innehåll.  Det finns ingen granskning av fjärran sluten fil resurs. |

## <a name="identity"></a>Identitet

| Säkerhets kontroll | Ja/nej | Obs! |  Dokumentation
|---|---|--|
| Autentisering| Ja | Kunder kan bygga program på App Service som automatiskt integreras med [Azure Active Directory (Azure AD)](../active-directory/index.yml) samt andra OAuth-kompatibla identitets leverantörer för hanterings åtkomst till App Service till gångar, och all åtkomst kontrol leras av en kombination av Azure AD-autentiserade huvud konton och Azure RBAC. | [Autentisering och auktorisering i Azure App Service](overview-authentication-authorization.md)
| Auktorisering| Ja | För hanterings åtkomst till App Service till gångar styrs all åtkomst av en kombination av Azure AD-autentiserade huvud konton och Azure RBAC.  | [Autentisering och auktorisering i Azure App Service](overview-authentication-authorization.md)

## <a name="data-protection"></a>Dataskydd

| Säkerhets kontroll | Ja/nej | Obs! | Dokumentation
|---|---|--|
| Kryptering på Server sidan i vila: Microsoft-hanterade nycklar | Ja | Innehållet på webbplats filen lagras i Azure Storage, vilket automatiskt krypterar innehållet i vila. <br><br>Kunder som har tillhandahållit hemligheter är krypterade i vila. Hemligheterna är krypterade i rest medan de lagras i App Service konfigurations databaser.<br><br>Lokalt anslutna diskar kan alternativt användas som temporär lagring av webbplatser (D:\Local och% TMP%). Lokalt anslutna diskar är inte krypterade i vila. | [Azure Storage-kryptering av vilande data](../storage/common/storage-service-encryption.md)
| Kryptering på Server sidan på rest: Kundhanterade nycklar (BYOK) | Ja | Kunder kan välja att lagra program hemligheter i Key Vault och hämta dem vid körning. | [Använda Key Vault referenser för App Service och Azure Functions (förhands granskning)](app-service-key-vault-references.md)
| Kryptering på kolumn nivå (Azure Data Services)| E.t. | |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i VNet-kryptering och VNet-VNet kryptering)| Ja | Kunder kan konfigurera webbplatser för att kräva och använda HTTPS för inkommande trafik.  | Så [här gör du endast Azure App Service https](/archive/blogs/benjaminperkins/how-to-make-an-azure-app-service-https-only) (blogg inlägg)
| Krypterade API-anrop| Ja | Hanterings anrop för att konfigurera App Service sker via [Azure Resource Manager](../azure-resource-manager/index.yml) -anrop via https. |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhets kontroll | Ja/nej | Obs! | Dokumentation
|---|---|--|
| Konfigurations hanterings stöd (konfigurations version osv.)| Ja | För hanterings åtgärder kan tillstånd för en App Service-konfiguration exporteras som en Azure Resource Manager mall och versions hantering över tid. För körnings åtgärder kan kunder underhålla flera olika Live-versioner av ett program med hjälp av funktionen för App Service distributions platser. | 

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om de [inbyggda säkerhets kontrollerna i Azure-tjänster](../security/fundamentals/security-controls.md).