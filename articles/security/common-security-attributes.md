---
title: Security attribut för Azure-tjänster
description: En lista med vanliga security attribut för att utvärdera Azure Service Fabric
services: security
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: security
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: mbaldwin
ms.openlocfilehash: 460d8756c437a1212aef054cf069be2bccac8c8a
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/22/2019
ms.locfileid: "66001365"
---
# <a name="security-attributes-for-azure-services"></a>Security attribut för Azure-tjänster

Den här artikeln samlar in vanliga security-attribut för valda Azure-tjänster. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="azure-api-managementapi-managementapi-management-security-attributesmd"></a>[Azure API Management](../api-management/api-management-security-attributes.md)

### <a name="preventative"></a>Förebyggande

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Vilande kryptering:<ul><li>Kryptering på serversidan</li><li>Kryptering på serversidan med Kundhanterade nycklar</li><li>Andra krypteringsfunktioner (t.ex på klientsidan, alltid krypterad, osv.)</ul>| Ja (endast tjänstsidan kryptering) | Känsliga data, till exempel certifikat, nycklar och värden för krypterad hemlighet är krypterade med tjänsthanterad per instans för för tjänsten. |
| Kryptering under överföring:<ul><li>Express route-kryptering</li><li>Virtuellt nätverk med kryptering</li><li>VNet-VNet-kryptering</ul>| Ja | [Express Route](../expressroute/index.yml) och tillhandahålls krypteringen av virtuellt nätverk av [Azure-nätverk](../virtual-network/index.yml). |
| Kryptering viktiga hantering (CMK, BYOK osv.)| Nej | Alla krypteringsnycklar är per instans och tjänsthanterade. |
| Kolumnen filnivåkryptering (Azure-datatjänster)| Gäller inte | |
| API-anrop som är krypterad| Ja | Hantering av plan anrop görs via [Azure Resource Manager](../azure-resource-manager/index.yml) via TLS. Det krävs en giltig JSON webbtoken (JWT).  Plan dataöverföringar kan skyddas med TLS och en av autentiseringsmekanismer som stöds (t.ex. klientcertifikatet eller JWT).
 |

### <a name="network-segmentation"></a>Nätverkssegmentering

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för Service-slutpunkt| Nej | |
| VNet-stöd för inmatning| Ja | |
| Isolering av nätverk och brandväggsfunktioner support| Ja | Med hjälp av nätverk nätverkssäkerhetsgrupper (NSG) och Azure Application Gateway (eller andra programinstallation) respektive. |
| Tvingad tunneltrafik support| Ja | Azure-nätverket innehåller Tvingad tunneltrafik. |

### <a name="detection"></a>Identifiering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Azure övervakningsstöd (Log analytics, appinsikter osv.)| Ja | |

### <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Autentisering| Ja | |
| Auktorisering| Ja | |


### <a name="audit-trail"></a>Granskningslogg

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Kontroll och hantering av plan loggning och granskning| Ja | [Azure Monitor-aktivitetsloggar](../azure-monitor/platform/activity-logs-overview.md) |
| Data plan loggning och granskning| Ja | [Azure Monitor-diagnostikloggar](../azure-monitor/platform/diagnostic-logs-overview.md) och (frivilligt) [Azure Application Insights](../azure-monitor/app/app-insights-overview.md).  |

### <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Support för Configuration (versionshanteringen för konfiguration, osv.)| Ja | Med hjälp av den [Azure API Management DevOps Resource Kit](https://aka.ms/apimdevops) |


## <a name="azure-app-serviceapp-serviceapp-service-security-attributesmd"></a>[Azure App Service](../app-service/app-service-security-attributes.md)

### <a name="preventative"></a>Förebyggande

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Kryptering i vila (t.ex kryptering på serversidan, kryptering på serversidan med Kundhanterade nycklar och andra krypteringsfunktioner) | Ja | Webbplats filinnehållet lagras i Azure Storage, som krypterar automatiskt innehållet i vila. Se [Azure Storage kryptering för vilande data](../storage/common/storage-service-encryption.md).<br><br>Kunden tillhandahållna hemligheter krypteras i vila. Hemligheterna är krypterade i vila medan lagras i App Service-konfigurationsdatabaser.<br><br>Lokalt anslutna diskar kan du kan också användas som tillfällig lagring av webbplatser (D:\local och % TMP %). Lokalt anslutna diskar som är inte krypterade i vila. |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i virtuella nätverk, och kryptering för VNet-VNet)| Ja | Kunder kan konfigurera webbplatser för att kräva och använda HTTPS för inkommande trafik. Finns i bloggposten [hur du gör en Azure App Service endast HTTPS](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/). |
| Kryptering viktiga hantering (CMK, BYOK osv.)| Ja | Kunder kan välja att lagra hemligheter i Key Vault och hämta dem vid körning. Se [använda Key Vault refererar till för App Service och Azure Functions (förhandsversion)](../app-service/app-service-key-vault-references.md).|
| Kolumnen filnivåkryptering (Azure-datatjänster)| Gäller inte | |
| API-anrop som är krypterad| Ja | Hanteringsanrop att konfigurera App Service sker [Azure Resource Manager](../azure-resource-manager/index.yml) anrop via HTTPS. |

### <a name="network-segmentation"></a>Nätverkssegmentering

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för Service-slutpunkt| Ja | För närvarande tillgängligt i förhandsversionen av App Service. Se [åtkomstbegränsningar för Azure App Service](../app-service/app-service-ip-restrictions.md). |
| VNet-stöd för inmatning| Ja | App Service-miljöer är privat implementeringar av Apptjänst som är dedikerad till en enda kund som införs i kundens virtuella nätverk. Se [introduktion till App Service-miljöer](../app-service/environment/intro.md). |
| Stöd för isolering av nätverk och Firewalling| Ja | Kunder kan konfigurera åtkomstkontrollistor (IP-begränsningar) för att låsa tillåten inkommande trafik i nätverket för den offentliga flera innehavare-variationen av App Service.  Se [åtkomstbegränsningar för Azure App Service](../app-service/app-service-ip-restrictions.md).  App Service-miljöer har distribuerats direkt i virtuella nätverk och därför kan skyddas med NSG: er. |
| Tvingad tunneltrafik support| Ja | App Service-miljöer kan distribueras till en kunds virtuella nätverk där Tvingad tunneltrafik har konfigurerats. Kunder måste följa anvisningarna i [konfigurera App Service Environment med tvingande dirigering](../app-service/environment/forced-tunnel-support.md). |

### <a name="detection"></a>Identifiering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Azure övervakningsstöd (Log analytics, appinsikter osv.)| Ja | App Service kan integreras med Application Insights för språk som har stöd för Application Insights (fullständiga .NET Framework, .NET Core, Java och Node.JS).  Se [övervaka Azure App Service-prestanda](../azure-monitor/app/azure-web-apps.md). App Service skickar också programmått i Azure Monitor. Se [övervaka appar i Azure App Service](../app-service/web-sites-monitor.md). |

### <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Autentisering| Ja | Kunder kan utveckla program i App Service som automatiskt integreras med [Azure Active Directory (Azure AD)](../active-directory/index.md) samt andra OAuth kompatibla identitetsleverantörer; Se [autentisering och auktorisering Azure App Service](../app-service/overview-authentication-authorization.md). För av hanteringsåtkomst till App Service-tillgångar måste styrs all åtkomst av en kombination av huvudnamn för Azure AD som autentiseras och Azure Resource Manager RBAC-roller. |
| Auktorisering| Ja | För av hanteringsåtkomst till App Service-tillgångar måste styrs all åtkomst av en kombination av huvudnamn för Azure AD som autentiseras och Azure Resource Manager RBAC-roller.  |


### <a name="audit-trail"></a>Granskningslogg

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Kontroll och hantering av plan loggning och granskning| Ja | Alla hanteringsåtgärder som utförts på App Service-objekt sker [Azure Resource Manager](../azure-resource-manager/index.yml). Historiska loggarna för dessa åtgärder är tillgängliga både i portalen och via CLI; Se [Azure Resource Manager åtgärder för resursprovider](../role-based-access-control/resource-provider-operations.md#microsoftweb) och [az monitor-aktivitetsloggen](/cli/azure/monitor/activity-log). |
| Data plan loggning och granskning | Nej | Dataplanet för App Service är en fjärransluten filresurs som innehåller en kund s distribuerade webbplats innehåll.  Det finns ingen granskning fjärransluten filresurs. |

### <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Support för Configuration (versionshanteringen för konfiguration, osv.)| Ja | För hanteringsåtgärder, kan tillståndet för en App Service-konfiguration exporteras som en Azure Resource Manager-mall och version över tid. Kunder kan underhålla flera olika live versioner av ett program med App Service-fack distributionsfunktion för runtime åtgärder. | 


## <a name="azure-resource-managerazure-resource-managerazure-resource-manager-security-attributesmd"></a>[Azure Resource Manager](../azure-resource-manager/azure-resource-manager-security-attributes.md)

### <a name="preventative"></a>Förebyggande

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Vilande kryptering:<ul><li>Kryptering på serversidan</li><li>Kryptering på serversidan med Kundhanterade nycklar</li><li>Andra krypteringsfunktioner (t.ex på klientsidan, alltid krypterad, osv.)</ul>| Ja |  |
| Kryptering under överföring:<ul><li>Express route-kryptering</li><li>Virtuellt nätverk med kryptering</li><li>VNet-VNet-kryptering</ul>| Ja | HTTPS/TLS. |
| Kryptering viktiga hantering (CMK, BYOK osv.)| Gäller inte | Azure Resource Manager lagrar endast kontrolldata inget kund-innehåll. |
| Kolumnen filnivåkryptering (Azure-datatjänster)| Ja | |
| API-anrop som är krypterad| Ja | |

### <a name="network-segmentation"></a>Nätverkssegmentering

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för Service-slutpunkt| Nej | |
| VNet-stöd för inmatning| Ja | |
| Isolering av nätverk och brandväggsfunktioner support| Nej |  |
| Tvingad tunneltrafik support| Nej |  |

### <a name="detection"></a>Identifiering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Azure övervakningsstöd (Log analytics, appinsikter osv.)| Nej | |

### <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Autentisering| Ja | [Azure Active Directory](/azure/active-directory) baserat.|
| Auktorisering| Ja | |


### <a name="audit-trail"></a>Granskningslogg

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Kontroll och hantering av plan loggning och granskning| Ja | Aktivitetsloggar exponerar alla skrivåtgärder (PUT, POST, ta bort) utförs på dina resurser. Se [visa aktivitetsloggar till granska åtgärder på resurser](../azure-resource-manager/resource-group-audit.md). |
| Data plan loggning och granskning| Gäller inte | |

### <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Support för Configuration (versionshanteringen för konfiguration, osv.)| Ja |  |


## <a name="azure-backupbackupbackup-security-attributesmd"></a>[Azure Backup](../backup/backup-security-attributes.md)

### <a name="preventative"></a>Förebyggande

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Vilande kryptering:<ul><li>Kryptering på serversidan</li><li>Kryptering på serversidan med Kundhanterade nycklar</li><li>Andra krypteringsfunktioner (t.ex på klientsidan, alltid krypterad, osv.)</ul>| Ja | Med hjälp av kryptering av lagringstjänst för storage-konton. |
| Kryptering under överföring:<ul><li>Express route-kryptering</li><li>Virtuellt nätverk med kryptering</li><li>VNet-VNet-kryptering</ul>| Nej | Med hjälp av HTTPS. |
| Kryptering viktiga hantering (CMK, BYOK osv.)| Nej |  |
| Kolumnen filnivåkryptering (Azure-datatjänster)| Nej |  |
| API-anrop som är krypterad| Ja |  |

### <a name="network-segmentation"></a>Nätverkssegmentering

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för Service-slutpunkt| Nej |  |
| VNet-stöd för inmatning| Nej |  |
| Isolering av nätverk och brandväggsfunktioner support| Ja | Tvingad tunneltrafik har stöd för säkerhetskopiering av virtuella datorer. Tvingad tunneltrafik finns inte stöd för arbetsbelastningar som körs på virtuella datorer. |
| Tvingad tunneltrafik support| Nej |  |

### <a name="detection"></a>Identifiering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Azure övervakningsstöd (Log analytics, appinsikter osv.)| Ja | Log Analytics stöds via diagnostikloggar. Se övervakaren Azure Backup skyddade arbetsbelastningar med hjälp av Log Analytics (https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/) för mer information. |

### <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Autentisering| Ja | Autentisering är via Azure Active Directory. |
| Auktorisering| Ja | Kunden har skapat och inbyggda RBAC-roller används. Se Use Role-Based Access Control för att hantera Azure Backup återställningspunkter (/ azure/backup/backup-rbac-rs-valv) för mer information. |


### <a name="audit-trail"></a>Granskningslogg

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Kontroll och hantering av plan loggning och granskning| Ja | Alla åtgärder som kund som utlöses från Azure portal loggas aktivitetsloggar. |
| Data plan loggning och granskning| Nej | Azure Backup-dataplanet kan inte nås direkt.  |

### <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Support för Configuration (versionshanteringen för konfiguration, osv.)| Ja|  |


## <a name="azure-key-vaultkey-vaultkey-vault-security-attributesmd"></a>[Azure Key Vault](../key-vault/key-vault-security-attributes.md)

### <a name="preventative"></a>Förebyggande

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Vilande kryptering:<ul><li>Kryptering på serversidan</li><li>Kryptering på serversidan med Kundhanterade nycklar</li><li>Andra krypteringsfunktioner (t.ex på klientsidan, alltid krypterad, osv.)</ul>| Ja | Alla objekt som är krypterade. |
| Kryptering under överföring:<ul><li>Express route-kryptering</li><li>Virtuellt nätverk med kryptering</li><li>VNet-VNet-kryptering</ul>| Ja | All kommunikation är via krypterade API-anrop |
| Kryptering viktiga hantering (CMK, BYOK osv.)| Ja | Kunden styr alla nycklar i sina Key Vault. När modul (HSM) som backas upp av maskinvara säkerhetsnycklar anges, skyddar en FIPS nivå 2 HSM nyckeln, certifikat eller hemligheten. |
| Kolumnen filnivåkryptering (Azure-datatjänster)| Gäller inte |  |
| API-anrop som är krypterad| Ja | Med hjälp av HTTPS. |

### <a name="network-segmentation"></a>Nätverkssegmentering

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för Service-slutpunkt| Ja | Med tjänstslutpunkter i virtuella nätverk (VNet). |
| VNet-stöd för inmatning| Nej |  |
| Isolering av nätverk och brandväggsfunktioner support| Ja | Med hjälp av brandväggsregler för virtuellt nätverk. |
| Tvingad tunneltrafik support| Nej |  |

### <a name="detection"></a>Identifiering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Azure övervakningsstöd (Log analytics, appinsikter osv.)| Ja | Använda Log Analytics. |

### <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Autentisering| Ja | Autentisering är via Azure Active Directory. |
| Auktorisering| Ja | Med hjälp av Nyckelvalvets åtkomstprincip. |


### <a name="audit-trail"></a>Granskningslogg

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Kontroll och hantering dataplaner loggning och granskning| Ja | Använda Log Analytics. |
| Data plan loggning och granskning| Ja | Använda Log Analytics. |

### <a name="access-controls"></a>Åtkomstkontroller

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Kontroll och hantering dataplanets åtkomstkontroller | Ja | Azure Resource Manager rollbaserad åtkomstkontroll (RBAC) |
| Data dataplanets åtkomstkontroller (på varje servicenivå) | Ja | Nyckelvalvets åtkomstprincip |

## <a name="azure-service-bus-messagingservice-bus-messagingservice-bus-messaging-security-attributesmd"></a>[Azure Service Bus-meddelanden](../service-bus-messaging/service-bus-messaging-security-attributes.md)

### <a name="preventative"></a>Förebyggande

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Vilande kryptering:<ul><li>Kryptering på serversidan</li><li>Kryptering på serversidan med Kundhanterade nycklar</li><li>Andra krypteringsfunktioner (t.ex på klientsidan, alltid krypterad, osv.)</ul>|  Ja för serversidan kryptering vid vila som standard. | Kundhanterade nycklar och BYOK stöds inte ännu. Kryptering är klientens ansvar |
| Kryptering under överföring:<ul><li>Express route-kryptering</li><li>Virtuellt nätverk med kryptering</li><li>VNet-VNet-kryptering</ul>| Ja | Stöder standard HTTPS/TLS-mekanism. |
| Kryptering viktiga hantering (CMK, BYOK osv.)| Nej |   |
| Kolumnen filnivåkryptering (Azure-datatjänster)| Gäller inte | |
| API-anrop som är krypterad| Ja | API-anrop som görs via [Azure Resource Manager](../azure-resource-manager/index.yml) och HTTPS. |

### <a name="network-segmentation"></a>Nätverkssegmentering

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för Service-slutpunkt| Ja (endast Premium-nivån) | Tjänstslutpunkter i virtuella nätverk stöds för [Service Bus Premium-nivån](../service-bus-messaging/service-bus-premium-messaging.md) endast. |
| VNet-stöd för inmatning| Nej | |
| Isolering av nätverk och brandväggsfunktioner support| Ja (endast Premium-nivån) |  |
| Tvingad tunneltrafik support| Nej |  |

### <a name="detection"></a>Identifiering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Azure övervakningsstöd (Log analytics, appinsikter osv.)| Ja | Stöds via [Azure Monitor och aviseringar](../service-bus-messaging/service-bus-metrics-azure-monitor.md). |

### <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Autentisering| Ja | Hanteras via [Azure Active Directory hanterad tjänstidentitet](../service-bus-messaging/service-bus-managed-service-identity.md); Se [Service Bus, autentisering och auktorisering](../service-bus-messaging/service-bus-authentication-and-authorization.md).|
| Auktorisering| Ja | Har stöd för auktorisering via [RBAC](../service-bus-messaging/service-bus-role-based-access-control.md) (förhandsversion) och SAS-token, se [Service Bus, autentisering och auktorisering](../service-bus-messaging/service-bus-authentication-and-authorization.md). |

### <a name="audit-trail"></a>Granskningslogg

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Kontroll och hantering av plan loggning och granskning| Ja | Åtgärdsloggar är tillgängliga. Se [Service Bus diagnostikloggar](../service-bus-messaging/service-bus-diagnostic-logs.md).  |
| Data plan loggning och granskning| Nej |  |

### <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Support för Configuration (versionshanteringen för konfiguration, osv.)| Ja | Har stöd för resource provider versionshantering via den [Azure Resource Manager API](/rest/api/resources/).|


## <a name="azure-service-bus-relayservice-bus-relayservice-bus-relay-security-attributesmd"></a>[Azure Service Bus Relay](../service-bus-relay/service-bus-relay-security-attributes.md)

### <a name="preventative"></a>Förebyggande

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Vilande kryptering:<ul><li>Kryptering på serversidan</li><li>Kryptering på serversidan med Kundhanterade nycklar</li><li>Andra krypteringsfunktioner (t.ex på klientsidan, alltid krypterad, osv.)</ul>|  Gäller inte | Relay är en web-socket och data sparas inte. |
| Kryptering under överföring:<ul><li>Express route-kryptering</li><li>Virtuellt nätverk med kryptering</li><li>VNet-VNet-kryptering</ul>| Ja | Tjänsten kräver TLS. |
| Kryptering viktiga hantering (CMK, BYOK osv.)| Nej | Använder Microsoft TLS-certifikat.  |
| Kolumnen filnivåkryptering (Azure-datatjänster)| Gäller inte | |
| API-anrop som är krypterad| Ja | HTTPS. |

### <a name="network-segmentation"></a>Nätverkssegmentering

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för Service-slutpunkt| Nej |  |
| Isolering av nätverk och brandväggsfunktioner support| Nej |  |
| Tvingad tunneltrafik support| Gäller inte | Relay är TLS-tunnel  |

### <a name="detection"></a>Identifiering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Azure övervakningsstöd (Log analytics, appinsikter osv.)| Ja | |

### <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Autentisering| Ja | Via SAS. |
| Auktorisering|  Ja | Via SAS. |


### <a name="audit-trail"></a>Granskningslogg

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Kontroll och hantering av plan loggning och granskning| Ja | Via [med Azure Resource Manager](../azure-resource-manager/index.yml). |
| Data plan loggning och granskning| Ja | Anslutningen lyckas / fel och fel och loggas.  |

### <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Support för Configuration (versionshanteringen för konfiguration, osv.)| Ja | Via [med Azure Resource Manager](../azure-resource-manager/index.yml).|


## <a name="azure-service-fabricservice-fabricservice-fabric-security-attributesmd"></a>[Azure Service Fabric](../service-fabric/service-fabric-security-attributes.md)

### <a name="preventative"></a>Förebyggande

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Vilande kryptering:<ul><li>Kryptering på serversidan</li><li>Kryptering på serversidan med Kundhanterade nycklar</li><li>Andra krypteringsfunktioner (t.ex på klientsidan, alltid krypterad, osv.)</ul>| Ja | Kunden äger klustret och skalningsuppsättningen för virtuell dator (VM) som klustret bygger på. Azure-diskkryptering kan aktiveras på virtuella datorns skalningsuppsättning. |
| Kryptering under överföring:<ul><li>Express route-kryptering</li><li>Virtuellt nätverk med kryptering</li><li>VNet-VNet-kryptering</ul>| Ja |  |
| Kryptering viktiga hantering (CMK, BYOK osv.)| Ja | Kunden äger klustret och skalningsuppsättningen för virtuell dator (VM) som klustret bygger på. Azure-diskkryptering kan aktiveras på virtuella datorns skalningsuppsättning. |
| Kolumnen filnivåkryptering (Azure-datatjänster)| Gäller inte |  |
| API-anrop som är krypterad| Ja | Service Fabric-API-anrop görs via Azure Resource Manager. Det krävs en giltig JSON webbtoken (JWT). |

### <a name="network-segmentation"></a>Nätverkssegmentering

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för Service-slutpunkt| Ja |  |
| VNet-stöd för inmatning| Ja |  |
| Isolering av nätverk och brandväggsfunktioner support| Ja | Med hjälp av nätverk nätverkssäkerhetsgrupper (NSG). |
| Tvingad tunneltrafik support| Ja | Azure-nätverket innehåller Tvingad tunneltrafik. |

### <a name="detection"></a>Identifiering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Azure övervakningsstöd (Log analytics, appinsikter osv.)| Ja | Med hjälp av Azure-övervakning stöd och stöd från tredje part. |

### <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Autentisering| Ja | Autentisering är via Azure Active Directory. |
| Auktorisering| Ja | Identitets- och åtkomsthantering (IAM) för anrop via SFRP. Anrop direkt till slutpunkten för klustret har stöd för två roller: Användare och administratör. Kunden kan mappa API: erna till någon av rollerna. |

### <a name="audit-trail"></a>Granskningslogg

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Kontroll och hantering av plan loggning och granskning| Ja | Alla kontrollplanåtgärder kör via processer för att granska och godkännanden. |
| Data plan loggning och granskning| Gäller inte | Kunden äger klustret.  |

### <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Support för Configuration (versionshanteringen för konfiguration, osv.)| Ja | Tjänstkonfigurationen är versionsnummer och distribuerade använda Azure distribuerar. Koden (program och runtime) skapas med Azure-versionen.
 |

## <a name="azure-sql-databasesql-databasesql-database-security-attributesmd"></a>[Azure SQL Database](../sql-database/sql-database-security-attributes.md)

### <a name="preventative"></a>Förebyggande

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Vilande kryptering:<ul><li>Kryptering på serversidan</li><li>Kryptering på serversidan med Kundhanterade nycklar</li><li>Andra krypteringsfunktioner (t.ex på klientsidan, alltid krypterad, osv.)</ul>| Ja | Kallas ”kryptering vid användning”, enligt beskrivningen i artikeln [Always Encrypted](../sql-database/sql-database-always-encrypted.md). Tjänstsidan kryptering använder [transparent datakryptering](../sql-database/transparent-data-encryption-azure-sql.md) (TDE).|
| Kryptering under överföring:<ul><li>ExpressRoute-kryptering</li><li>Virtuellt nätverk med kryptering</li><li>VNet-VNet-kryptering</ul>| Ja | Med hjälp av HTTPS. |
| Kryptering viktiga hantering (CMK, BYOK osv.)| Ja | Både tjänsthanterad och kundhanterad nyckel hantering erbjuds (det senare via [Azure Key Vault](../key-vault/index.yml). |
| Kolumnen filnivåkryptering (Azure-datatjänster)| Ja | Via [Always Encrypted](../sql-database/sql-database-always-encrypted.md). |
| API-anrop som är krypterad| Ja | Med hjälp av HTTPS/SSL. |

### <a name="network-segmentation"></a>Nätverkssegmentering

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för Service-slutpunkt| Ja | Gäller för [enkel databas](../sql-database/sql-database-single-index.yml) endast. |
| VNet-stöd för inmatning| Ja | Gäller för [hanterad instans](../sql-database/sql-database-managed-instance.md) endast. |
| Isolering av nätverk och brandväggsfunktioner support| Ja | Brandväggen på båda databas - och server-nivå. nätverksisolering för [hanterad instans](../sql-database/sql-database-managed-instance.md) endast |
| Tvingad tunneltrafik support| Ja | [hanterad instans](../sql-database/sql-database-managed-instance.md) via [Azure ExpressRoute](../expressroute/index.yml) VPN |

### <a name="detection"></a>Identifiering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Azure övervakningsstöd (Log analytics, appinsikter osv.)| Ja | SIEM-lösning från tredje part från Imperva (SecureSphere) är också stöds via [Azure Event Hubs](../event-hubs/index.yml) integrering via [SQL audit](../sql-database/sql-database-auditing.md). |

### <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Autentisering| Ja | Azure Active Directory. |
| Auktorisering| Ja |  |


### <a name="audit-trail"></a>Granskningslogg

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Kontroll och hantering av plan loggning och granskning| Ja | Ja för endast vissa händelser. |
| Data plan loggning och granskning | Ja | Via [SQL audit](../sql-database/sql-database-auditing.md). |

### <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Support för Configuration (versionshanteringen för konfiguration, osv.)| Nej  | | 

### <a name="additional-security-attributes-for-sql-database"></a>Attribut för ytterligare säkerhet för SQL-databas

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Förebyggande: utvärdering av säkerhetsrisker | Ja | Se [Sårbarhetsbedömning för SQL service hjälper dig att identifiera databasen sårbarheter](../sql-database/sql-vulnerability-assessment.md). |
| Förebyggande: dataidentifiering och klassificering  | Ja | Se [Azure SQL Database och SQL Data Warehouse dataidentifiering och klassificering](../sql-database/sql-database-data-discovery-and-classification.md). |
| Identifiering: hotidentifiering | Ja | Se [Avancerat skydd för Azure SQL Database](../sql-database/sql-database-threat-detection-overview.md). |

## <a name="azure-storagestoragecommonstorage-security-attributesmd"></a>[Azure Storage](../storage/common/storage-security-attributes.md)

### <a name="preventative"></a>Förebyggande

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Vilande kryptering:<ul><li>Kryptering på serversidan</li><li>Kryptering på serversidan med Kundhanterade nycklar</li><li>Andra krypteringsfunktioner (t.ex på klientsidan, alltid krypterad, osv.)</ul>| Ja |  |
| Kryptering under överföring:<ul><li>Express route-kryptering</li><li>Virtuellt nätverk med kryptering</li><li>VNet-VNet-kryptering</ul>| Ja | Stöd för standardmetoder HTTPS/TLS.  Användare kan också kryptera data innan de skickas till tjänsten. |
| Kryptering viktiga hantering (CMK, BYOK osv.)| Ja | Se [kryptering av lagringstjänst med Kundhanterade nycklar i Azure Key Vault](../storage/common/storage-service-encryption-customer-managed-keys.md).|
| Kolumnen filnivåkryptering (Azure-datatjänster)| Gäller inte |  |
| API-anrop som är krypterad| Ja |  |

### <a name="network-segmentation"></a>Nätverkssegmentering

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för Service-slutpunkt| Ja |  |
| VNet-stöd för inmatning| Gäller inte |  |
| Isolering av nätverk och brandväggsfunktioner support| Ja | |
| Tvingad tunneltrafik support| Gäller inte |  |

### <a name="detection"></a>Identifiering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Azure övervakningsstöd (Log analytics, appinsikter osv.)| Ja | Azure Monitor Metrics som är tillgängliga nu loggar från förhandsversion |

### <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Autentisering| Ja | Azure Active Directory delad nyckel, delad åtkomst-token. |
| Auktorisering| Ja | Stöd för auktorisering via RBAC, POSIX-ACL: er och SAS-token |


### <a name="audit-trail"></a>Granskningslogg

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Kontroll och hantering av plan loggning och granskning | Ja | Azure Resource Manager-aktivitetslogg |
| Data plan loggning och granskning| Ja | Diagnostikloggar för tjänsten och Azure Monitor-loggning från förhandsversion  |

### <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Support för Configuration (versionshanteringen för konfiguration, osv.)| Ja | Stöd för versionshantering Resource Provider via Azure Resource Manager API: er |