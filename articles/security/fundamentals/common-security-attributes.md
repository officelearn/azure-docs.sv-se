---
title: Säkerhetsattribut för Azure-tjänster
description: En check lista över säkerhetsattribut för utvärdering av Azure-tjänster
services: security
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: security
ms.subservice: security-fundamentals
ms.topic: conceptual
ms.date: 07/11/2019
ms.author: mbaldwin
ms.openlocfilehash: be39a623dc5dac09dcfe47de67e025191b5c7de3
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727487"
---
# <a name="security-attributes-for-azure-services"></a>Säkerhetsattribut för Azure-tjänster

I den här artikeln samlas säkerhetsattributen in för de valda Azure-tjänsterna. Ett säkerhetsattribut är en kvalitet eller funktion i en Azure-tjänst. Tjänsten bidrar till tjänstens möjlighet att förhindra, identifiera och reagera på säkerhets risker.

Säkerhetsattribut kategoriseras som:
* Förebyggande
* Nätverks segmentering
* Identifiering
* Stöd för identitets-och åtkomst hantering
* Gransknings logg
* Åtkomst kontroller (om de används)
* Konfigurations hantering (om det används)

I varje kategori visar vi "Ja" eller "nej" för att ange om ett attribut används. För vissa tjänster visar vi "ej tillämpligt" för ett attribut som inte är tillämpligt. Vi kan också ange en anteckning eller en länk till mer information om ett attribut.

## <a name="api-managementazureapi-managementapi-management-security-attributes"></a>[API Management](/azure/api-management/api-management-security-attributes)

### <a name="preventative"></a>Förebyggande

| Säkerhetsattribut | Ja/nej | Anteckningar |
|---|---|--|
| Kryptering i vila (t. ex. kryptering på Server sidan, kryptering på Server sidan med Kundhanterade nycklar och andra krypterings funktioner) | Känsliga data, till exempel certifikat, nycklar och hemliga värden, krypteras med hanterade tjänst instanser, per tjänst instans nycklar. |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i VNet-kryptering och VNet-VNet-kryptering) | Ja | [Express Route](/azure/expressroute/index) och VNET-kryptering tillhandahålls av [Azure-nätverk](/azure/virtual-network/index). |
| Hantering av krypterings nyckel (CMK, BYOK osv.)| Nej | Alla krypterings nycklar är per tjänst instans och hanteras av tjänsten. |
| Kryptering på kolumn nivå (Azure Data Services)| Gäller inte | |
| Krypterade API-anrop| Ja | Hanterings Plans anrop görs via [Azure Resource Manager](/azure/azure-resource-manager/index) via TLS. En giltig JSON Web token (JWT) krävs.  Data Plans anrop kan skyddas med TLS och en av de autentiseringsmekanismer som stöds (till exempel klient certifikat eller JWT).
 |

### <a name="network-segmentation"></a>Nätverks segmentering

| Säkerhetsattribut | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för tjänst slut punkt| Nej | |
| Stöd för VNet-injektering| Ja | |
| Stöd för nätverks isolering och brand vägg| Ja | Använda nätverks säkerhets grupper (NSG) och Azure Application Gateway (eller annan program varu installation). |
| Stöd för Tvingad tunnel trafik| Ja | Azure-nätverk tillhandahåller Tvingad tunnel trafik. |

### <a name="detection"></a>Identifiering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Azure Monitoring support (Log Analytics, App Insights osv.)| Ja | |

### <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Authentication| Ja | |
| Authorization| Ja | |


### <a name="audit-trail"></a>Gransknings logg

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Loggning och granskning av kontroll-och hanterings plan| Ja | [Azure Monitor aktivitets loggar](/azure/azure-monitor/platform/activity-logs-overview) |
| Loggning och granskning av data planet| Ja | [Azure Monitor diagnostikloggar](/azure/azure-monitor/platform/diagnostic-logs-overview) och (valfritt) [Azure Application](/azure/azure-monitor/app/app-insights-overview)insikter.|

### <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Konfigurations hanterings stöd (konfigurations version osv.)| Ja | Använda [Azure API Management DevOps Resource Kit](https://aka.ms/apimdevops) |

### <a name="vulnerability-scans-false-positives"></a>Sårbarhet söker efter falska positiva identifieringar

I det här avsnittet dokumenteras vanliga sårbarheter, som inte påverkar Azure-API Management.

| Sårbarhet               | Beskrivning                                                                                                                                                                                                                                                                                                               |
|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ticketbleed (CVE-2016-9244) | Ticketbleed är ett säkerhets problem i implementeringen av TLS SessionTicket-tillägget som finns i vissa F5-produkter. Det tillåter läckage ("avblödning") på upp till 31 byte data från ej initierat minne. Detta orsakas av att utfyllnaden av TLS-stacken är ett sessions-ID som skickas från klienten, med data som gör den 32 bitar lång. |


## <a name="app-serviceazureapp-serviceapp-service-security-attributes"></a>[App Service](/azure/app-service/app-service-security-attributes)

### <a name="preventative"></a>Förebyggande

| Säkerhetsattribut | Ja/nej | Anteckningar |
|---|---|--|
| Kryptering i vila (t. ex. kryptering på Server sidan, kryptering på Server sidan med Kundhanterade nycklar och andra krypterings funktioner) | Ja | Innehållet på webbplats filen lagras i Azure Storage, vilket automatiskt krypterar innehållet i vila. Se [Azure Storage kryptering för vilande data](/azure/storage/common/storage-service-encryption).<br><br>Kunder som har tillhandahållit hemligheter är krypterade i vila. Hemligheterna är krypterade i rest medan de lagras i App Service konfigurations databaser.<br><br>Lokalt anslutna diskar kan alternativt användas som temporär lagring av webbplatser (D:\Local och% TMP%). Lokalt anslutna diskar är inte krypterade i vila. |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i VNet-kryptering och VNet-VNet-kryptering) | Ja | Kunder kan konfigurera webbplatser för att kräva och använda HTTPS för inkommande trafik. Se blogg inlägget [så här gör du endast Azure App Service https](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/). |
| Hantering av krypterings nyckel (CMK, BYOK osv.)| Ja | Kunder kan välja att lagra program hemligheter i Key Vault och hämta dem vid körning. Se [använda Key Vault referenser för app service och Azure Functions (för hands version)](/azure/app-service/app-service-key-vault-references).|
| Kryptering på kolumn nivå (Azure Data Services)| Gäller inte | |
| Krypterade API-anrop| Ja | Hanterings anrop för att konfigurera App Service sker via [Azure Resource Manager](/azure/azure-resource-manager/index) -anrop via https. |

### <a name="network-segmentation"></a>Nätverks segmentering

| Säkerhetsattribut | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för tjänst slut punkt| Ja | För närvarande tillgängligt som för hands version för App Service. Se [begränsningar för Azure App Service åtkomst](/azure/app-service/app-service-ip-restrictions). |
| Stöd för VNet-injektering| Ja | App Service miljöer är privata implementeringar av App Service dedikerade till en enskild kund som injiceras i ett kunds virtuella nätverk. Se [Introduktion till App Service miljöer](/azure/app-service/environment/intro). |
| Stöd för nätverks isolering och brand vägg| Ja | För den offentliga variationen av flera innehavare av App Service kan kunder konfigurera nätverks-ACL: er (IP-begränsningar) för att låsa tillåten inkommande trafik.  Se [begränsningar för Azure App Service åtkomst](/azure/app-service/app-service-ip-restrictions).  App Service miljöer distribueras direkt till virtuella nätverk och kan därför skyddas med NSG: er. |
| Stöd för Tvingad tunnel trafik| Ja | App Service miljöer kan distribueras till en kunds virtuella nätverk där Tvingad tunnel trafik har kon figurer ATS. Kunderna måste följa anvisningarna i [Konfigurera din app service-miljön med Tvingad tunnel trafik](/azure/app-service/environment/forced-tunnel-support). |

### <a name="detection"></a>Identifiering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Azure Monitoring support (Log Analytics, App Insights osv.)| Ja | App Service integreras med Application Insights för språk som stöder Application Insights (fullständig .NET Framework, .NET Core, Java och Node. JS).  Se [övervaka Azure App Service prestanda](/azure/azure-monitor/app/azure-web-apps). App Service skickar även program statistik till Azure Monitor. Se [övervaka appar i Azure App Service](/azure/app-service/web-sites-monitor). |

### <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Authentication| Ja | Kunder kan bygga program på App Service som automatiskt integreras med [Azure Active Directory (Azure AD)](/azure/active-directory/index) samt andra OAuth-kompatibla identitets leverantörer. Se [autentisering och auktorisering i Azure App Service](/azure/app-service/overview-authentication-authorization). För hanterings åtkomst till App Service till gångar styrs all åtkomst av en kombination av Azure AD-autentiserade huvud konton och Azure Resource Manager RBAC-roller. |
| Authorization| Ja | För hanterings åtkomst till App Service till gångar styrs all åtkomst av en kombination av Azure AD-autentiserade huvud konton och Azure Resource Manager RBAC-roller.  |


### <a name="audit-trail"></a>Gransknings logg

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Loggning och granskning av kontroll-och hanterings plan| Ja | Alla hanterings åtgärder som utförs på App Service objekt sker via [Azure Resource Manager](/azure/azure-resource-manager/index). Historiska loggar för dessa åtgärder är tillgängliga både i portalen och via CLI; Se [Azure Resource Manager Resource Provider Operations](/azure/role-based-access-control/resource-provider-operations#microsoftweb) och [AZ Monitor Activity-Log](/cli/azure/monitor/activity-log). |
| Loggning och granskning av data planet | Nej | Data planet för App Service är en fjärran sluten fil resurs som innehåller en kunds distribuerad webbplats innehåll.  Det finns ingen granskning av fjärran sluten fil resurs. |

### <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Konfigurations hanterings stöd (konfigurations version osv.)| Ja | För hanterings åtgärder kan tillstånd för en App Service-konfiguration exporteras som en Azure Resource Manager mall och versions hantering över tid. För körnings åtgärder kan kunder underhålla flera olika Live-versioner av ett program med hjälp av funktionen för App Service distributions platser. | 



## <a name="azure-resource-managerazureazure-resource-managerazure-resource-manager-security-attributes"></a>[Azure Resource Manager](/azure/azure-resource-manager/azure-resource-manager-security-attributes)

### <a name="preventative"></a>Förebyggande

| Säkerhetsattribut | Ja/nej | Anteckningar |
|---|---|--|
| Kryptering i vila (t. ex. kryptering på Server sidan, kryptering på Server sidan med Kundhanterade nycklar och andra krypterings funktioner) | Ja |  |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i VNet-kryptering och VNet-VNet-kryptering) | Ja | HTTPS/TLS. |
| Hantering av krypterings nyckel (CMK, BYOK osv.)| Gäller inte | Azure Resource Manager lagrar inget kund innehåll, endast kontroll data. |
| Kryptering på kolumn nivå (Azure Data Services)| Ja | |
| Krypterade API-anrop| Ja | |

### <a name="network-segmentation"></a>Nätverks segmentering

| Säkerhetsattribut | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för tjänst slut punkt| Nej | |
| Stöd för VNet-injektering| Ja | |
| Stöd för nätverks isolering och brand vägg| Nej |  |
| Stöd för Tvingad tunnel trafik| Nej |  |

### <a name="detection"></a>Identifiering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Azure Monitoring support (Log Analytics, App Insights osv.)| Nej | |

### <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Authentication| Ja | [Azure Active Directory](/azure/active-directory) baserad.|
| Authorization| Ja | |


### <a name="audit-trail"></a>Gransknings logg

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Loggning och granskning av kontroll-och hanterings plan| Ja | Aktivitets loggar visar alla Skriv åtgärder (placering, POST, DELETE) som utförs på resurserna. Se [Visa aktivitets loggar för att granska åtgärder på resurser](/azure/azure-resource-manager/resource-group-audit). |
| Loggning och granskning av data planet| Gäller inte | |

### <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Konfigurations hanterings stöd (konfigurations version osv.)| Ja |  |


## <a name="azure-backupazurebackupbackup-security-attributes"></a>[Azure Backup](/azure/backup/backup-security-attributes)

### <a name="preventative"></a>Förebyggande

| Säkerhetsattribut | Ja/nej | Anteckningar |
|---|---|--|
| Kryptering i vila (t. ex. kryptering på Server sidan, kryptering på Server sidan med Kundhanterade nycklar och andra krypterings funktioner)| Ja | Använda kryptering av lagrings tjänst för lagrings konton. |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i VNet-kryptering och VNet-VNet-kryptering) | Nej | Använda HTTPS. |
| Hantering av krypterings nyckel (CMK, BYOK osv.)| Nej |  |
| Kryptering på kolumn nivå (Azure Data Services)| Nej |  |
| Krypterade API-anrop| Ja |  |

### <a name="network-segmentation"></a>Nätverks segmentering

| Säkerhetsattribut | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för tjänst slut punkt| Nej |  |
| Stöd för VNet-injektering| Nej |  |
| Stöd för nätverks isolering och brand vägg| Ja | Tvingad tunnel trafik stöds för VM-säkerhetskopiering. Tvingad tunnel trafik stöds inte för arbets belastningar som körs i virtuella datorer. |
| Stöd för Tvingad tunnel trafik| Nej |  |

### <a name="detection"></a>Identifiering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Azure Monitoring support (Log Analytics, App Insights osv.)| Ja | Log Analytics stöds via diagnostikloggar. Mer information finns i [övervaka Azure Backup skyddade arbets belastningar med hjälp av Log Analytics](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/) . |

### <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Authentication| Ja | Autentisering är via Azure Active Directory. |
| Authorization| Ja | Kunden skapade och inbyggda RBAC-roller används. Mer information finns i [använda rollbaserad Access Control för att hantera Azure Backup återställnings punkter](/azure/backup/backup-rbac-rs-vault) . |


### <a name="audit-trail"></a>Gransknings logg

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Loggning och granskning av kontroll-och hanterings plan| Ja | Alla kunder som har utlöst åtgärder från Azure Portal loggas till aktivitets loggar. |
| Loggning och granskning av data planet| Nej | Det går inte att nå Azure Backup data planet direkt.  |

### <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Konfigurations hanterings stöd (konfigurations version osv.)| Ja|  |

## <a name="cosmos-dbazurecosmos-dbcosmos-db-security-attributes"></a>[Cosmos DB](/azure/cosmos-db/cosmos-db-security-attributes)

### <a name="preventative"></a>Förebyggande

| Säkerhetsattribut | Ja/Nej | Anteckningar |
|---|---|--|
| Kryptering i vila (t. ex. kryptering på Server sidan, kryptering på Server sidan med Kundhanterade nycklar och andra krypterings funktioner) | Ja | Alla Cosmos DB databaser och säkerhets kopior krypteras som standard. Se [data kryptering i Azure Cosmos DB](/azure/cosmos-db/database-encryption-at-rest). Kryptering på Server sidan med Kundhanterade nycklar stöds inte. |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i VNet-kryptering och VNet-VNet-kryptering)| Ja | Alla Azure Cosmos DB data krypteras vid överföring. |
| Hantering av krypterings nyckel (CMK, BYOK osv.)| Nej |  |
| Kryptering på kolumn nivå (Azure Data Services)| Ja | Endast i Tables API Premium. Alla API: er stöder inte den här funktionen. Se [introduktion till Azure Cosmos DB: Tabell-API](/azure/cosmos-db/table-introduction). |
| Krypterade API-anrop| Ja | Alla anslutningar till Azure Cosmos DB stöd för HTTPS. Azure Cosmos DB stöder även TLS 1,2-anslutningar, men detta är inte tvingande än. Om kunderna stänger av lågnivå-TLS på deras sida, kan de se till att de ansluter till Cosmos DB.  |

### <a name="network-segmentation"></a>Nätverks segmentering

| Säkerhetsattribut | Ja/Nej | Anteckningar |
|---|---|--|
| Stöd för tjänst slut punkt| Ja |  |
| Stöd för VNet-injektering| Ja | Med VNet-tjänstens slut punkt kan du konfigurera ett Azure Cosmos DB konto för att tillåta åtkomst endast från ett speciellt undernät i ett virtuellt nätverk (VNet). Du kan också kombinera VNet-åtkomst med brand Väggs regler.  Se [åtkomst Azure Cosmos dB från virtuella nätverk](/azure/cosmos-db/vnet-service-endpoint). |
| Stöd för nätverks isolering och brand vägg| Ja | Med brand Väggs stöd kan du konfigurera ditt Azure Cosmos-konto så att det bara tillåter åtkomst från en godkänd uppsättning IP-adresser, ett intervall med IP-adresser och/eller moln tjänster. Se [Konfigurera IP-brandvägg i Azure Cosmos DB](/azure/cosmos-db/how-to-configure-firewall).|
| Stöd för Tvingad tunnel trafik| Ja | Kan konfigureras på klient sidan på det VNet där de virtuella datorerna finns.   |

### <a name="detection"></a>Identifiering

| Säkerhetsattribut | Ja/Nej | Anteckningar|
|---|---|--|
| Azure Monitoring support (Log Analytics, App Insights osv.)| Ja | Alla begär Anden som skickas till Azure Cosmos DB loggas. [Azure-övervakning](/azure/azure-monitor/overview), Azure-mått, Azure gransknings loggning stöds.  Du kan logga information som motsvarar data Plans begär Anden, fråga efter körnings statistik, frågetext, MongoDB-begäranden. Du kan också konfigurera aviseringar. |

### <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Säkerhetsattribut | Ja/Nej | Anteckningar|
|---|---|--|
| Authentication| Ja | Ja på databas konto nivå; på data planet nivå använder Cosmos DB resurspooler och nyckel åtkomst. |
| Authorization| Ja | Stöds på Azure Cosmos-kontot med huvud nycklar (primära och sekundära) och resurs-token. Du kan hämta Läs-och skriv-eller Läs behörighet till data med huvud nycklar. Med resurs-token får du begränsad åtkomst till resurser som dokument och behållare. |

### <a name="audit-trail"></a>Gransknings logg

| Säkerhetsattribut | Ja/Nej | Anteckningar|
|---|---|--|
| Loggning och granskning av kontroll-och hanterings plan| Ja | Azure aktivitets logg för åtgärder på konto nivå, till exempel brand väggar, virtuella nätverk, nycklar åtkomst och IAM. |
| Loggning och granskning av data planet | Ja | Loggning av diagnostik för behållar åtgärder, till exempel skapa behållare, etablera data flöde, indexerings principer och CRUD åtgärder för dokument. |

### <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Konfigurations hanterings stöd (konfigurations version osv.)| Nej  | | 

### <a name="additional-security-attributes-for-cosmos-db"></a>Ytterligare säkerhetsattribut för Cosmos DB

| Säkerhetsattribut | Ja/Nej | Anteckningar|
|---|---|--|
| Resurs delning mellan ursprung (CORS) | Ja | Se [Konfigurera CORS (Cross-Origin resurs delning)](/azure/cosmos-db/how-to-configure-cross-origin-resource-sharing). |


## <a name="event-hubsazureevent-hubsevent-hubs-security-attributes"></a>[Event Hubs](/azure/event-hubs/event-hubs-security-attributes)

### <a name="preventative"></a>Förebyggande

| Säkerhetsattribut | Ja/nej | Anteckningar |
|---|---|--|
| Kryptering i vila (t. ex. kryptering på Server sidan, kryptering på Server sidan med Kundhanterade nycklar och andra krypterings funktioner) |  Ja | |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i VNet-kryptering och VNet-VNet-kryptering) | Ja | |
| Hantering av krypterings nyckel (CMK, BYOK osv.)| Nej |  |
| Kryptering på kolumn nivå (Azure Data Services)| Gäller inte | |
| Krypterade API-anrop| Ja |  |

### <a name="network-segmentation"></a>Nätverks segmentering

| Säkerhetsattribut | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för tjänst slut punkt| Ja |  |
| Stöd för VNet-injektering| Nej | |
| Stöd för nätverks isolering och brand vägg| Ja |  |
| Stöd för Tvingad tunnel trafik| Nej |  |

### <a name="detection"></a>Identifiering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Azure Monitoring support (Log Analytics, App Insights osv.)| Ja | |

### <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Authentication| Ja | |
| Authorization|  Ja | |


### <a name="audit-trail"></a>Gransknings logg

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Loggning och granskning av kontroll-och hanterings plan| Ja |  |
| Loggning och granskning av data planet| Ja |   |

### <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Konfigurations hanterings stöd (konfigurations version osv.)| Ja | |


## <a name="expressrouteazureexpressrouteexpressroute-security-attributes"></a>[ExpressRoute](/azure/expressroute/expressroute-security-attributes)

### <a name="preventative"></a>Förebyggande

| Säkerhetsattribut | Ja/nej | Anteckningar |
|---|---|--|
| Kryptering i vila (t. ex. kryptering på Server sidan, kryptering på Server sidan med Kundhanterade nycklar och andra krypterings funktioner) |  Gäller inte | ExpressRoute lagrar inte kund information. |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i VNet-kryptering och VNet-VNet-kryptering) | Nej | |
| Hantering av krypterings nyckel (CMK, BYOK osv.)| Gäller inte |  |
| Kryptering på kolumn nivå (Azure Data Services)| Gäller inte | |
| Krypterade API-anrop| Ja | Via [Azure Resource Manager](/azure/azure-resource-manager/index) och https. |

### <a name="network-segmentation"></a>Nätverks segmentering

| Säkerhetsattribut | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för tjänst slut punkt| Gäller inte |  |
| Stöd för VNet-injektering| Gäller inte | |
| Stöd för nätverks isolering och brand vägg| Ja | Varje kund ingår i en egen routningsdomän och dirigeras sedan till sitt eget VNet |
| Stöd för Tvingad tunnel trafik| Gäller inte | Via Border Gateway Protocol (BGP). |

### <a name="detection"></a>Identifiering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Azure Monitoring support (Log Analytics, App Insights osv.)| Ja | Se [ExpressRoute-övervakning, mått och aviseringar](/azure/expressroute/expressroute-monitoring-metrics-alerts).|

### <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Authentication| Ja | Tjänst konto för gateway för Microsoft (GWM) (kontrollant); JIT-åtkomst (just in Time) för utveckling och OP. |
| Authorization|  Ja |Tjänst konto för gateway för Microsoft (GWM) (kontrollant); JIT-åtkomst (just in Time) för utveckling och OP. |


### <a name="audit-trail"></a>Gransknings logg

| Säkerhetsattribut | Ja/nej | Anteckningar| 
|---|---|--|
| Loggning och granskning av kontroll-och hanterings plan| Ja |  |
| Loggning och granskning av data planet| Nej |   |

### <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Konfigurations hanterings stöd (konfigurations version osv.)| Ja | Via nätverks resurs leverantören (NRP). |


## <a name="key-vaultkey-vaultkey-vault-security-attributesmd"></a>[Key Vault](../../key-vault/key-vault-security-attributes.md)

### <a name="preventative"></a>Förebyggande

| Säkerhetsattribut | Ja/nej | Anteckningar |
|---|---|--|
| Kryptering i vila (t. ex. kryptering på Server sidan, kryptering på Server sidan med Kundhanterade nycklar och andra krypterings funktioner) | Ja | Alla objekt är krypterade. |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i VNet-kryptering och VNet-VNet-kryptering) | Ja | All kommunikation sker via krypterade API-anrop |
| Hantering av krypterings nyckel (CMK, BYOK osv.)| Ja | Kunden styr alla nycklar i sina Key Vault. När HSM-säkerhetskopierade nycklar har angetts skyddar en FIPS-nivå 2 HSM nyckeln, certifikatet eller hemligheten. |
| Kryptering på kolumn nivå (Azure Data Services)| Gäller inte |  |
| Krypterade API-anrop| Ja | Använda HTTPS. |

### <a name="network-segmentation"></a>Nätverks segmentering

| Säkerhetsattribut | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för tjänst slut punkt| Ja | Använda tjänst slut punkter för Virtual Network (VNet). |
| Stöd för VNet-injektering| Nej |  |
| Stöd för nätverks isolering och brand vägg| Ja | Använd brand Väggs regler för VNet. |
| Stöd för Tvingad tunnel trafik| Nej |  |

### <a name="detection"></a>Identifiering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Azure Monitoring support (Log Analytics, App Insights osv.)| Ja | Använda Log Analytics. |

### <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Authentication| Ja | Autentisering är via Azure Active Directory. |
| Authorization| Ja | Använda Key Vault åtkomst princip. |

### <a name="audit-trail"></a>Gransknings logg

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Kontroll/hantering plan-loggning och granskning| Ja | Använda Log Analytics. |
| Loggning och granskning av data planet| Ja | Använda Log Analytics. |

### <a name="access-controls"></a>Åtkomstkontroller

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Åtkomst kontroller för kontroll/hanterings plan | Ja | Azure Resource Manager rollbaserad åtkomstkontroll (RBAC) |
| Åtkomst kontroller för data plan (på varje service nivå) | Ja | Key Vault åtkomst princip |

## <a name="load-balancerload-balancerload-balancer-security-attributesmd"></a>[Lastbalanserare](../../load-balancer/load-balancer-security-attributes.md)

### <a name="preventative"></a>Förebyggande

| Säkerhetsattribut | Ja/nej | Anteckningar |
|---|---|--|
| Kryptering i vila (t. ex. kryptering på Server sidan, kryptering på Server sidan med Kundhanterade nycklar och andra krypterings funktioner) | Gäller inte | |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i VNet-kryptering och VNet-VNet-kryptering)| Gäller inte | |
| Hantering av krypterings nyckel (CMK, BYOK osv.)| Gäller inte | |
| Kryptering på kolumn nivå (Azure Data Services)| Gäller inte | |
| Krypterade API-anrop| Ja | Via [Azure Resource Manager](/azure/azure-resource-manager/index). |

### <a name="network-segmentation"></a>Nätverks segmentering

| Säkerhetsattribut | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för tjänst slut punkt| Gäller inte | |
| Stöd för VNet-injektering| Gäller inte | . |
| Stöd för nätverks isolering och brand vägg| Gäller inte |  |
| Stöd för Tvingad tunnel trafik| Gäller inte | |

### <a name="detection"></a>Identifiering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Azure Monitoring support (Log Analytics, App Insights osv.)| Ja | Se [Azure Monitor-loggar för offentliga grundläggande Load Balancer](/azure/load-balancer/load-balancer-monitor-log). |

### <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Authentication| Gäller inte |  |
| Authorization| Gäller inte |  |

### <a name="audit-trail"></a>Gransknings logg

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Loggning och granskning av kontroll-och hanterings plan| Ja | Se [Azure Monitor-loggar för offentliga grundläggande Load Balancer](/azure/load-balancer/load-balancer-monitor-log). |
| Loggning och granskning av data planet | Gäller inte |  |

### <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Konfigurations hanterings stöd (konfigurations version osv.)| Gäller inte |  | 

## <a name="service-bus-messagingazureservice-bus-messagingservice-bus-messaging-security-attributes"></a>[Service Bus meddelanden](/azure/service-bus-messaging/service-bus-messaging-security-attributes)

### <a name="preventative"></a>Förebyggande

| Säkerhetsattribut | Ja/nej | Anteckningar |
|---|---|--|
| Kryptering i vila (t. ex. kryptering på Server sidan, kryptering på Server sidan med Kundhanterade nycklar och andra krypterings funktioner) |  Ja för kryptering på Server sidan – rest som standard. | Kundhanterade nycklar och BYOK stöds inte ännu. Kryptering på klient sidan är klientens ansvar |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i VNet-kryptering och VNet-VNet-kryptering) | Ja | Stöder standard-HTTPS/TLS-mekanismen. |
| Hantering av krypterings nyckel (CMK, BYOK osv.)| Nej |   |
| Kryptering på kolumn nivå (Azure Data Services)| Gäller inte | |
| Krypterade API-anrop| Ja | API-anrop görs via [Azure Resource Manager](/azure/azure-resource-manager/index) och https. |

### <a name="network-segmentation"></a>Nätverks segmentering

| Säkerhetsattribut | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för tjänst slut punkt| Ja (endast Premium-nivå) | VNet-tjänstens slut punkter stöds endast för [Service Bus Premium-nivån](/azure/service-bus-messaging/service-bus-premium-messaging) . |
| Stöd för VNet-injektering| Nej | |
| Stöd för nätverks isolering och brand vägg| Ja (endast Premium-nivå) |  |
| Stöd för Tvingad tunnel trafik| Nej |  |

### <a name="detection"></a>Identifiering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Azure Monitoring support (Log Analytics, App Insights osv.)| Ja | Stöds via [Azure Monitor och aviseringar](/azure/service-bus-messaging/service-bus-metrics-azure-monitor). |

### <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Authentication| Ja | Hanteras via [Azure Active Directory hanterad tjänstidentitet](/azure/service-bus-messaging/service-bus-managed-service-identity); Se [Service Bus autentisering och auktorisering](/azure/service-bus-messaging/service-bus-authentication-and-authorization).|
| Authorization| Ja | Stöder auktorisering via [RBAC](/azure/service-bus-messaging/service-bus-role-based-access-control) (för hands version) och SAS-token; Se [Service Bus autentisering och auktorisering](/azure/service-bus-messaging/service-bus-authentication-and-authorization). |


### <a name="audit-trail"></a>Gransknings logg

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Loggning och granskning av kontroll-och hanterings plan| Ja | Det finns tillgängliga åtgärds loggar. Se [Service Bus diagnostikloggar](/azure/service-bus-messaging/service-bus-diagnostic-logs).  |
| Loggning och granskning av data planet| Nej |  |

### <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Konfigurations hanterings stöd (konfigurations version osv.)| Ja | Stöder versions hantering av resurs leverantörer via [Azure Resource Manager API](/rest/api/resources/).|


## <a name="service-bus-relayazureservice-bus-relayservice-bus-relay-security-attributes"></a>[Service Bus Relay](/azure/service-bus-relay/service-bus-relay-security-attributes)

### <a name="preventative"></a>Förebyggande

| Säkerhetsattribut | Ja/nej | Anteckningar |
|---|---|--|
| Kryptering i vila (t. ex. kryptering på Server sidan, kryptering på Server sidan med Kundhanterade nycklar och andra krypterings funktioner) |  Gäller inte | Relay är en webbsocket och bevarar inte data. |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i VNet-kryptering och VNet-VNet-kryptering) | Ja | Tjänsten kräver TLS. |
| Hantering av krypterings nyckel (CMK, BYOK osv.)| Nej | Använder endast Microsoft TLS-certifikat.  |
| Kryptering på kolumn nivå (Azure Data Services)| Gäller inte | |
| Krypterade API-anrop| Ja | HTTPS. |

### <a name="network-segmentation"></a>Nätverks segmentering

| Säkerhetsattribut | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för tjänst slut punkt| Nej |  |
| Stöd för nätverks isolering och brand vägg| Nej |  |
| Stöd för Tvingad tunnel trafik| Gäller inte | Relä är TLS-tunneln  |

### <a name="detection"></a>Identifiering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Azure Monitoring support (Log Analytics, App Insights osv.)| Ja | |

### <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Authentication| Ja | Via SAS. |
| Authorization|  Ja | Via SAS. |

### <a name="audit-trail"></a>Gransknings logg

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Loggning och granskning av kontroll-och hanterings plan| Ja | Via [Azure Resource Manager](/azure/azure-resource-manager/index). |
| Loggning och granskning av data planet| Ja | Lyckad/misslyckad anslutning och fel och loggad.  |

### <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Konfigurations hanterings stöd (konfigurations version osv.)| Ja | Via [Azure Resource Manager](/azure/azure-resource-manager/index).|

## <a name="service-fabricservice-fabricservice-fabric-security-attributesmd"></a>[Service Fabric](../../service-fabric/service-fabric-security-attributes.md)

### <a name="preventative"></a>Förebyggande

| Säkerhetsattribut | Ja/nej | Anteckningar |
|---|---|--|
| Kryptering i vila (t. ex. kryptering på Server sidan, kryptering på Server sidan med Kundhanterade nycklar och andra krypterings funktioner) | Ja | Kunden äger klustret och den skalnings uppsättning för virtuella datorer som klustret har skapats på. Azure Disk Encryption kan aktive ras på den virtuella datorns skal uppsättning. |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i VNet-kryptering och VNet-VNet-kryptering) | Ja | Kunden äger klustret och den skalnings uppsättning för virtuella datorer som klustret har skapats på. Azure Disk Encryption kan aktive ras på den virtuella datorns skal uppsättning. |
| Kryptering på kolumn nivå (Azure Data Services)| Gäller inte |  |
| Krypterade API-anrop| Ja | Service Fabric API-anrop görs via Azure Resource Manager. En giltig JSON Web token (JWT) krävs. |

### <a name="network-segmentation"></a>Nätverks segmentering

| Säkerhetsattribut | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för tjänst slut punkt| Ja |  |
| Stöd för VNet-injektering| Ja |  |
| Stöd för nätverks isolering och brand vägg| Ja | Använda nätverks säkerhets grupper (NSG). |
| Stöd för Tvingad tunnel trafik| Ja | Azure-nätverk tillhandahåller Tvingad tunnel trafik. |

### <a name="detection"></a>Identifiering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Azure Monitoring support (Log Analytics, App Insights osv.)| Ja | Använda Azure Monitoring support och stöd från tredje part. |

### <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Authentication| Ja | Autentisering är via Azure Active Directory. |
| Authorization| Ja | Identitets-och åtkomst hantering (IAM) för anrop via SFRP. Anrop direkt till kluster slut punkten har stöd för två roller: Användare och administratör. Kunden kan mappa API: erna till vilken roll som möjligt. |

### <a name="audit-trail"></a>Gransknings logg

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Loggning och granskning av kontroll-och hanterings plan| Ja | Alla kontroll Plans åtgärder körs genom processer för granskning och godkännanden. |
| Loggning och granskning av data planet| Gäller inte | Kunden äger klustret.  |

### <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Konfigurations hanterings stöd (konfigurations version osv.)| Ja | |

## <a name="sql-databasesql-databasesql-database-security-attributesmd"></a>[SQL Database](../../sql-database/sql-database-security-attributes.md)

SQL Database innehåller både [en enkel databas och en](/azure/sql-database/sql-database-single-index) [hanterad instans](/azure/sql-database/sql-database-managed-instance). Följande poster gäller för båda erbjudandena utom i de fall där annat anges.

### <a name="preventative"></a>Förebyggande

| Säkerhetsattribut | Ja/nej | Anteckningar |
|---|---|--|
| Kryptering i vila (t. ex. kryptering på Server sidan, kryptering på Server sidan med Kundhanterade nycklar och andra krypterings funktioner) | Ja | Kallas "kryptering i användning", enligt beskrivningen i artikeln [Always Encrypted](/azure/sql-database/sql-database-always-encrypted). Kryptering på Server sidan använder [transparent data kryptering](/azure/sql-database/transparent-data-encryption-azure-sql).|
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i VNet-kryptering och VNet-VNet-kryptering) | Ja | Använda HTTPS. |
| Kryptering – nyckel hantering, till exempel CMK eller BYOK| Ja | Både hanterad och kundhanterad nyckel hantering erbjuds. Den senare erbjuds via [Azure Key Vault](/azure/key-vault/index). |
| Kryptering på kolumn nivå som tillhandahålls av Azure Data Services| Ja | Via [Always Encrypted](/azure/sql-database/sql-database-always-encrypted). |
| Krypterade API-anrop| Ja | Använda HTTPS/SSL. |

### <a name="network-segmentation"></a>Nätverks segmentering

| Säkerhetsattribut | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för tjänst slut punkt| Ja | Gäller endast för [en enskild databas](/azure/sql-database/sql-database-single-index) . |
| Stöd för Azure Virtual Network-injektering| Ja | Gäller endast för [hanterade instanser](/azure/sql-database/sql-database-managed-instance) . |
| Nätverks isolering och brand Väggs stöd| Ja | Brand väggen på både databas nivå och server nivå. Nätverks isolering är endast för [hanterad instans](/azure/sql-database/sql-database-managed-instance) . |
| Stöd för Tvingad tunnel trafik| Ja | [Hanterad instans](/azure/sql-database/sql-database-managed-instance) via en [ExpressRoute](/azure/expressroute/index.yml) VPN. |

### <a name="detection"></a>Identifiering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Stöd för Azure-övervakning, till exempel Log Analytics eller Application Insights| Ja | SecureSphere, SIEM-lösningen från Imperva, stöds också via [Azure Event Hubs](/azure/event-hubs/index) integration via [SQL-granskning](/azure/sql-database/sql-database-auditing). |

### <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Authentication| Ja | Azure Active Directory (Azure AD) |
| Authorization| Ja | Inga |

### <a name="audit-trail"></a>Gransknings logg

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Kontroll – plan och hantering – plan loggning och granskning| Ja | Ja endast för vissa händelser |
| Data Plans loggning och granskning | Ja | Via [SQL audit](/azure/sql-database/sql-database-auditing) |

### <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Konfiguration – hanterings stöd, till exempel konfiguration av versioner| Nej  | Ingen |

### <a name="additional-security-attributes-for-sql-database"></a>Ytterligare säkerhetsattribut för SQL Database

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Förebyggande: sårbarhets bedömning | Ja | Se [SQL sårbarhet Assessment service hjälper dig att identifiera databas sårbarheter](/azure/sql-database/sql-vulnerability-assessment). |
| Förebyggande: data identifiering och klassificering  | Ja | Se [Azure SQL Database och SQL Data Warehouse data identifiering & klassificering](/azure/sql-database/sql-database-data-discovery-and-classification). |
| Identifiering: Hot identifiering | Ja | Se [Avancerat skydd för Azure SQL Database](/azure/sql-database/sql-database-threat-detection-overview). |


## <a name="storageazurestoragecommonstorage-security-attributes"></a>[Storage](/azure/storage/common/storage-security-attributes)

### <a name="preventative"></a>Förebyggande

| Säkerhetsattribut | Ja/nej | Anteckningar |
|---|---|--|
| Kryptering i vila (t. ex. kryptering på Server sidan, kryptering på Server sidan med Kundhanterade nycklar och andra krypterings funktioner) | Ja |  |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i VNet-kryptering och VNet-VNet-kryptering) | Ja | Stöd för standard-HTTPS/TLS-mekanismer.  Användare kan också kryptera data innan de skickas till tjänsten. |
| Hantering av krypterings nyckel (CMK, BYOK osv.)| Ja | Se [kryptering för lagringstjänst att använda Kundhanterade nycklar i Azure Key Vault](/azure/storage/common/storage-service-encryption-customer-managed-keys?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).|
| Kryptering på kolumn nivå (Azure Data Services)| Gäller inte |  |
| Krypterade API-anrop| Ja |  |

### <a name="network-segmentation"></a>Nätverks segmentering

| Säkerhetsattribut | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för tjänst slut punkt| Ja |  |
| Stöd för VNet-injektering| Gäller inte |  |
| Stöd för nätverks isolering och brand vägg| Ja | |
| Stöd för Tvingad tunnel trafik| Gäller inte |  |

### <a name="detection"></a>Identifiering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Azure Monitoring support (Log Analytics, App Insights osv.)| Ja | Azure Monitor tillgängliga mått nu, loggar startar för hands version |

### <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Authentication| Ja | Azure Active Directory, delad nyckel, delad åtkomsttoken. |
| Authorization| Ja | Stöd för auktorisering via RBAC, POSIX ACL: er och SAS-token |

### <a name="audit-trail"></a>Gransknings logg

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Loggning och granskning av kontroll-och hanterings plan | Ja | Azure Resource Manager aktivitets logg |
| Loggning och granskning av data planet| Ja | Loggar för tjänsten Diagnostic och Azure Monitor loggning starta för hands version  |

### <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Konfigurations hanterings stöd (konfigurations version osv.)| Ja | Stöd för Resource Provider-versioner via Azure Resource Manager API: er |

## <a name="virtual-machines-and-virtual-machine-scale-sets"></a>Virtual Machines och Virtual Machine Scale Sets

[Virtuella Linux-datorer](/azure/virtual-machines/windows/virtual-machines-windows-security-attributes) | [Windows VM](/azure/virtual-machines/windows/virtual-machines-windows-security-attributes) | [Virtual Machine Scale Sets](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-security-attributes)


### <a name="preventative"></a>Förebyggande

| Säkerhetsattribut | Ja/nej | Anteckningar |
|---|---|--|
| Kryptering i vila (t. ex. kryptering på Server sidan, kryptering på Server sidan med Kundhanterade nycklar och andra krypterings funktioner) | Ja | Se [så här krypterar du en virtuell Linux-dator i Azure](/azure/virtual-machines/linux/encrypt-disks) och [krypterar virtuella diskar på en virtuell Windows-dator](/azure/virtual-machines/windows/encrypt-disks). |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i VNet-kryptering och VNet-VNet-kryptering)| Ja | Azure Virtual Machines stöder [ExpressRoute](/azure/expressroute) och VNET-kryptering. Se [kryptering under överföring i virtuella datorer](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms). |
| Hantering av krypterings nyckel (CMK, BYOK osv.)| Ja | Kundhanterade nycklar är ett Azure-krypterings scenario som stöds. Se [Översikt över Azure-kryptering](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms).|
| Kryptering på kolumn nivå (Azure Data Services)| Gäller inte | |
| Krypterade API-anrop| Ja | Via HTTPS och SSL. |

### <a name="network-segmentation"></a>Nätverks segmentering

| Säkerhetsattribut | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för tjänst slut punkt| Ja | |
| Stöd för VNet-injektering| Ja | . |
| Stöd för nätverks isolering och brand vägg| Ja |  |
| Stöd för Tvingad tunnel trafik| Ja | Se [Konfigurera Tvingad tunnel trafik med Azure Resource Manager distributions modell](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm). |

### <a name="detection"></a>Identifiering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Azure Monitoring support (Log Analytics, App Insights osv.)| Ja | Se [övervaka och uppdatera en virtuell Linux-dator i Azure](/azure/virtual-machines/linux/tutorial-monitoring) och [övervaka och uppdatera en virtuell Windows-dator i Azure](/azure/virtual-machines/windows/tutorial-monitoring). |

### <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Authentication| Ja |  |
| Authorization| Ja |  |


### <a name="audit-trail"></a>Gransknings logg

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Loggning och granskning av kontroll-och hanterings plan| Ja |  |
| Loggning och granskning av data planet | Nej |  |

### <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Konfigurations hanterings stöd (konfigurations version osv.)| Ja |  | 


## <a name="vpn-gatewayazurevpn-gatewayvpn-gateway-security-attributes"></a>[VPN Gateway](/azure/vpn-gateway/vpn-gateway-security-attributes)

### <a name="preventative"></a>Förebyggande

| Säkerhetsattribut | Ja/nej | Anteckningar |
|---|---|--|
| Kryptering i vila (t. ex. kryptering på Server sidan, kryptering på Server sidan med Kundhanterade nycklar och andra krypterings funktioner) | Gäller inte | Kunddata för VPN gateway-överföring lagrar inte kund information |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i VNet-kryptering och VNet-VNet-kryptering)| Ja | VPN gateway krypterar kund paket mellan Azure VPN-gatewayer och lokala VPN-enheter (S2S) eller VPN-klienter (P2S). VPN-gatewayer stöder även VNet-till-VNet-kryptering. |
| Hantering av krypterings nyckel (CMK, BYOK osv.)| Nej | Kundspecificerade i förväg delade nycklar är krypterade i vila. men inte integrerat med CMK än. |
| Kryptering på kolumn nivå (Azure Data Services)| Gäller inte | |
| Krypterade API-anrop| Ja | Via [Azure Resource Manager](/azure/azure-resource-manager/index) och https  |

### <a name="network-segmentation"></a>Nätverks segmentering

| Säkerhetsattribut | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för tjänst slut punkt| Gäller inte | |
| Stöd för VNet-injektering| Gäller inte | . |
| Stöd för nätverks isolering och brand vägg| Ja | VPN-gatewayer är dedikerade VM-instanser för varje kund Virtual Network  |
| Stöd för Tvingad tunnel trafik| Ja |  |

### <a name="detection"></a>Identifiering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Azure Monitoring support (Log Analytics, App Insights osv.)| Ja | Se [Azure Monitor diagnostikloggar/varning](/azure/vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log) & [Azure Monitor mått/avisering](/azure/vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric).  |

### <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Authentication| Ja | [Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis) för att hantera tjänsten och konfigurera Azure VPN-gatewayen. |
| Authorization| Ja | Stöd för auktorisering via [RBAC](/azure/role-based-access-control/overview). |

### <a name="audit-trail"></a>Gransknings logg

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Loggning och granskning av kontroll-och hanterings plan| Ja | Azure Resource Manager aktivitets logg. |
| Loggning och granskning av data planet | Ja | [Azure Monitor diagnostikloggar](/azure/azure-resource-manager/resource-group-audit) för loggning och granskning av VPN-anslutningar. |

### <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Konfigurations hanterings stöd (konfigurations version osv.)| Ja | För hanterings åtgärder kan statusen för en Azure VPN gateway-konfiguration exporteras som en Azure Resource Manager mall och versions hantering över tid. | 

