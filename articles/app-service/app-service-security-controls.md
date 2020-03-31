---
title: Säkerhetskontroller
description: Hitta en checklista med säkerhetskontroller för utvärdering av Azure App Service för din organisation.
author: msmbaldwin
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 2586821c4c48f809efb5408c3cdae5e42e3b3fcf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74671453"
---
# <a name="security-controls-for-azure-app-service"></a>Säkerhetskontroller för Azure App Service

I den här artikeln dokumenteras de säkerhetskontroller som är inbyggda i Azure App Service.

[!INCLUDE [Security controls header](../../includes/security-controls-header.md)]

## <a name="network"></a>Nätverk

| Säkerhetskontroll | Ja/nej | Anteckningar | Dokumentation
|---|---|--|
| Support för tjänstens slutpunkt| Ja | Tillgänglig för App Service.| [Åtkomstbegränsningar för Azure App-tjänsten](app-service-ip-restrictions.md)
| Stöd för VNet-injektion| Ja | App Service-miljöer är privata implementeringar av App Service dedikerade till en enskild kund som injiceras i en kunds virtuella nätverk. | [Introduktion till Azure App Service-miljöer](environment/intro.md)
| Stöd för nätverksisolering och brandväggar| Ja | För den offentliga varianten av flera innehavare av App Service kan kunder konfigurera IP-begränsningar (Network ACLs) för att låsa tillåten inkommande trafik.  App Service-miljöer distribueras direkt till virtuella nätverk och kan därför skyddas med NSG:er. | [Åtkomstbegränsningar för Azure App-tjänsten](app-service-ip-restrictions.md)
| Stöd för påtvingad tunnelning| Ja | App servicemiljöer kan distribueras till en kunds virtuella nätverk där påtvingad tunnelning är konfigurerad. | [Konfigurera App Service Environment med tvingande dirigering](environment/forced-tunnel-support.md)

## <a name="monitoring--logging"></a>Övervakning & loggning

| Säkerhetskontroll | Ja/nej | Anteckningar | Dokumentation
|---|---|--|
| Azure övervakningsstöd (Logganalys, App insikter, etc.)| Ja | App-tjänsten integreras med Application Insights för språk som stöder Application Insights (Full .NET Framework, .NET Core, Java och Node.JS).  Se [Prestanda för Azure App Service](../azure-monitor/app/azure-web-apps.md). App Service skickar också programmått till Azure Monitor. | [Övervaka appar i Azure App Service](web-sites-monitor.md)
| Kontroll- och hanteringsplan loggning och revision| Ja | Alla hanteringsåtgärder som utförs på App Service-objekt sker via [Azure Resource Manager](../azure-resource-manager/index.yml). Historiska loggar över dessa operationer finns både i portalen och via CLI. | [Azure Resource Manager resursprovideråtgärder](../role-based-access-control/resource-provider-operations.md#microsoftweb), [az övervaka aktivitetslogg](/cli/azure/monitor/activity-log)
| Loggning och granskning av dataplan | Inga | Dataplanet för App Service är en fjärrfilresurs som innehåller en kunds distribuerade webbplatsinnehåll.  Det finns ingen granskning av fjärrfilresursen. |

## <a name="identity"></a>Identitet

| Säkerhetskontroll | Ja/nej | Anteckningar |  Dokumentation
|---|---|--|
| Autentisering| Ja | Kunder kan skapa program på App Service som automatiskt integreras med [Azure Active Directory (Azure AD)](../active-directory/index.yml) samt andra OAuth-kompatibla identitetsleverantörer För hanteringsåtkomst till App Service-resurser styrs all åtkomst av en kombination av Azure AD-autentiserade huvudnamn och Azure Resource Manager RBAC-roller. | [Autentisering och auktorisering i Azure App Service](overview-authentication-authorization.md)
| Auktorisering| Ja | För hanteringsåtkomst till App Service-resurser styrs all åtkomst av en kombination av Azure AD-autentiserade huvudnamn och AZURE Resource Manager RBAC-roller.  | [Autentisering och auktorisering i Azure App Service](overview-authentication-authorization.md)

## <a name="data-protection"></a>Dataskydd

| Säkerhetskontroll | Ja/nej | Anteckningar | Dokumentation
|---|---|--|
| Kryptering på serversidan i vila: Microsoft-hanterade nycklar | Ja | Webbplatsfilinnehåll lagras i Azure Storage, som automatiskt krypterar innehållet i vila. <br><br>Kundförsörjda hemligheter krypteras i vila. Hemligheterna krypteras i vila medan de lagras i App Service-konfigurationsdatabaser.<br><br>Lokalt anslutna diskar kan eventuellt användas som tillfällig lagring av webbplatser (D:\local och %TMP%). Lokalt anslutna diskar krypteras inte i vila. | [Azure Storage-kryptering för data i vila](../storage/common/storage-service-encryption.md)
| Kryptering på serversidan i vila: kundhanterade nycklar (BYOK) | Ja | Kunder kan välja att lagra programhemligheter i Key Vault och hämta dem vid körning. | [Använda Key Vault-referenser för App Service och Azure Functions (förhandsversion)](app-service-key-vault-references.md)
| Kryptering på kolumnnivå (Azure Data Services)| Ej tillämpligt | |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, vnet-kryptering och VNet-VNet-kryptering)| Ja | Kunder kan konfigurera webbplatser så att de behöver och använder HTTPS för inkommande trafik.  | [Så här gör du endast en Azure App Service HTTPS](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/) (blogginlägg)
| API-anrop krypterade| Ja | Hanteringsanrop för att konfigurera App Service sker via [Azure Resource Manager-anrop](../azure-resource-manager/index.yml) via HTTPS. |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetskontroll | Ja/nej | Anteckningar | Dokumentation
|---|---|--|
| Stöd för konfigurationshantering (versionshantering av konfiguration osv.)| Ja | För hanteringsåtgärder kan tillståndet för en App Service-konfiguration exporteras som en Azure Resource Manager-mall och versionsas med tiden. För körningsåtgärder kan kunder underhålla flera olika liveversioner av ett program med hjälp av funktionen för distributionsplatser för App Service. | 

## <a name="next-steps"></a>Nästa steg

- Läs mer om de [inbyggda säkerhetskontrollerna för Azure-tjänster](../security/fundamentals/security-controls.md).
