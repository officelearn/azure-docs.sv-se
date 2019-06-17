---
title: Security attribut för Azure App Service
description: En checklista med säkerhetsattribut för att utvärdera Azure App Service
services: app-service
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: mbaldwin
ms.openlocfilehash: e38e1479c1e094f691e8f22f0a48f9342cae5cf3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66475076"
---
# <a name="security-attributes-for-azure-app-service"></a>Security attribut för Azure App Service

Den här artikeln beskrivs vanliga security attribut som är inbyggda i Azure App Service.

[!INCLUDE [Security attributes header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Förebyggande

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Kryptering i vila (t.ex kryptering på serversidan, kryptering på serversidan med Kundhanterade nycklar och andra krypteringsfunktioner) | Ja | Webbplats filinnehållet lagras i Azure Storage, som krypterar automatiskt innehållet i vila. Se [Azure Storage kryptering för vilande data](../storage/common/storage-service-encryption.md).<br><br>Kunden tillhandahållna hemligheter krypteras i vila. Hemligheterna är krypterade i vila medan lagras i App Service-konfigurationsdatabaser.<br><br>Lokalt anslutna diskar kan du kan också användas som tillfällig lagring av webbplatser (D:\local och % TMP %). Lokalt anslutna diskar som är inte krypterade i vila. |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i virtuella nätverk, och kryptering för VNet-VNet)| Ja | Kunder kan konfigurera webbplatser för att kräva och använda HTTPS för inkommande trafik. Finns i bloggposten [hur du gör en Azure App Service endast HTTPS](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/). |
| Kryptering viktiga hantering (CMK, BYOK osv.)| Ja | Kunder kan välja att lagra hemligheter i Key Vault och hämta dem vid körning. Se [använda Key Vault refererar till för App Service och Azure Functions (förhandsversion)](app-service-key-vault-references.md).|
| Kolumnen filnivåkryptering (Azure-datatjänster)| Gäller inte | |
| API-anrop som är krypterad| Ja | Hanteringsanrop att konfigurera App Service sker [Azure Resource Manager](../azure-resource-manager/index.yml) anrop via HTTPS. |

## <a name="network-segmentation"></a>Nätverkssegmentering

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för Service-slutpunkt| Ja | För närvarande tillgängligt i förhandsversionen av App Service. Se [åtkomstbegränsningar för Azure App Service](app-service-ip-restrictions.md). |
| VNet-stöd för inmatning| Ja | App Service-miljöer är privat implementeringar av Apptjänst som är dedikerad till en enda kund som införs i kundens virtuella nätverk. Se [introduktion till App Service-miljöer](environment/intro.md). |
| Stöd för isolering av nätverk och Firewalling| Ja | Kunder kan konfigurera åtkomstkontrollistor (IP-begränsningar) för att låsa tillåten inkommande trafik i nätverket för den offentliga flera innehavare-variationen av App Service.  Se [åtkomstbegränsningar för Azure App Service](app-service-ip-restrictions.md).  App Service-miljöer har distribuerats direkt i virtuella nätverk och därför kan skyddas med NSG: er. |
| Tvingad tunneltrafik support| Ja | App Service-miljöer kan distribueras till en kunds virtuella nätverk där Tvingad tunneltrafik har konfigurerats. Kunder måste följa anvisningarna i [konfigurera App Service Environment med tvingande dirigering](environment/forced-tunnel-support.md). |

## <a name="detection"></a>Detection (Identifiering)

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Azure övervakningsstöd (Log analytics, appinsikter osv.)| Ja | App Service kan integreras med Application Insights för språk som har stöd för Application Insights (fullständiga .NET Framework, .NET Core, Java och Node.JS).  Se [övervaka Azure App Service-prestanda](../azure-monitor/app/azure-web-apps.md). App Service skickar också programmått i Azure Monitor. Se [övervaka appar i Azure App Service](web-sites-monitor.md). |

## <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Autentisering| Ja | Kunder kan utveckla program i App Service som automatiskt integreras med [Azure Active Directory (Azure AD)](../active-directory/index.yml) samt andra OAuth kompatibla identitetsleverantörer; Se [autentisering och auktorisering Azure App Service](overview-authentication-authorization.md). För av hanteringsåtkomst till App Service-tillgångar måste styrs all åtkomst av en kombination av huvudnamn för Azure AD som autentiseras och Azure Resource Manager RBAC-roller. |
| Auktorisering| Ja | För av hanteringsåtkomst till App Service-tillgångar måste styrs all åtkomst av en kombination av huvudnamn för Azure AD som autentiseras och Azure Resource Manager RBAC-roller.  |


## <a name="audit-trail"></a>Granskningslogg

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Kontroll och hantering av plan loggning och granskning| Ja | Alla hanteringsåtgärder som utförts på App Service-objekt sker [Azure Resource Manager](../azure-resource-manager/index.yml). Historiska loggarna för dessa åtgärder är tillgängliga både i portalen och via CLI; Se [Azure Resource Manager åtgärder för resursprovider](../role-based-access-control/resource-provider-operations.md#microsoftweb) och [az monitor-aktivitetsloggen](/cli/azure/monitor/activity-log). |
| Data plan loggning och granskning | Nej | Dataplanet för App Service är en fjärransluten filresurs som innehåller en kunds distribuerade webbplatsinnehåll.  Det finns ingen granskning fjärransluten filresurs. |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Support för Configuration (versionshanteringen för konfiguration, osv.)| Ja | För hanteringsåtgärder, kan tillståndet för en App Service-konfiguration exporteras som en Azure Resource Manager-mall och version över tid. Kunder kan underhålla flera olika live versioner av ett program med App Service-fack distributionsfunktion för runtime åtgärder. | 

