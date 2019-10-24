---
title: Säkerhets loggning och granskning i Azure | Microsoft Docs
description: Lär dig mer om de loggar som är tillgängliga i Azure och de säkerhets insikter du kan få.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/21/2019
ms.author: terrylan
ms.openlocfilehash: d1f3f1c00ab8d92ee598a3ef52c1e6f05303b004
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755600"
---
# <a name="azure-security-logging-and-auditing"></a>Säkerhets loggning och granskning i Azure

Azure tillhandahåller en mängd olika konfigurerbara säkerhets gransknings-och loggnings alternativ som hjälper dig att identifiera luckor i dina säkerhets principer och-mekanismer. Den här artikeln beskriver hur du skapar, samlar in och analyserar säkerhets loggar från tjänster som finns i Azure.

> [!Note]
> Vissa rekommendationer i den här artikeln kan leda till ökad data-, nätverks-eller beräknings resursanvändning och öka dina licens-eller prenumerations kostnader.

## <a name="types-of-logs-in-azure"></a>Typer av loggar i Azure

Moln program är komplexa med många rörliga delar. Loggnings data kan ge insikter om dina program och hjälpa dig att:

- Felsök tidigare problem eller förhindra att de är möjliga
- Förbättra programmets prestanda eller hanterbarhet
- Automatisera åtgärder som annars skulle kräva manuell åtgärd

Azure-loggar kategoriseras i följande typer:
* **Kontroll-/hanterings loggar** innehåller information om Azure Resource Manager Skapa, uppdatera och ta bort åtgärder. Mer information finns i [Azure aktivitets loggar](../../azure-monitor/platform/activity-logs-overview.md).

* **Data Plans loggar** innehåller information om händelser som Aktiver ATS som en del av Azure-resursanvändningen. Exempel på den här typen av logg är Windows händelse system-, säkerhets-och program loggar i en virtuell dator (VM) och [de diagnostikloggar](../../azure-monitor/platform/resource-logs-overview.md) som konfigureras via Azure Monitor.

* **Bearbetade händelser** ger information om analyserade händelser/aviseringar som har bearbetats för din räkning. Exempel på den här typen är [Azure Security Center varningar](../../security-center/security-center-managing-and-responding-alerts.md) där [Azure Security Center](../../security-center/security-center-intro.md) har bearbetat och analyserat din prenumeration och ger kortfattade säkerhets aviseringar.

I följande tabell visas de viktigaste typerna av loggar som är tillgängliga i Azure:

| Loggkategori | Loggtyp | Användning | Integrering |
| ------------ | -------- | ------ | ----------- |
|[Aktivitets loggar](../../azure-monitor/platform/activity-logs-overview.md)|Kontroll – plan händelser på Azure Resource Manager resurser|  Ger insikt i de åtgärder som utfördes på resurser i din prenumeration.|    REST-API, [Azure Monitor](../../azure-monitor/platform/activity-logs-overview.md)|
|[Azure resurs loggar](../../azure-monitor/platform/resource-logs-overview.md)|Frekventa data om driften av Azure Resource Manager resurser i prenumerationen|   Ger inblick i åtgärder som din resurs själv utfört.| Azure Monitor|
|[Azure Active Directory rapportering](../../active-directory/reports-monitoring/overview-reports.md)|Loggar och rapporter | Rapporterar användar inloggnings aktiviteter och system aktivitets information om användare och grupp hantering.|[Graph API](../../active-directory/develop/active-directory-graph-api-quickstart.md)|
|[Virtuella datorer och moln tjänster](../../azure-monitor/learn/quick-collect-azurevm.md)|Windows händelse logg tjänst och Linux syslog|  Avbildar system data och loggnings data på de virtuella datorerna och överför dessa data till ett valfritt lagrings konto.|   Windows (med Windows Azure-diagnostik [[wad](../../monitoring-and-diagnostics/azure-diagnostics.md)] Storage) och Linux i Azure Monitor|
|[Azure-lagringsanalys](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)|Lagrings loggning, tillhandahåller mått data för ett lagrings konto|Ger insyn i trace-begäranden, analyserar användnings trender och diagnostiserar problem med ditt lagrings konto.|   REST API eller [klient biblioteket](https://msdn.microsoft.com/library/azure/mt347887.aspx)|
|[Flödes loggar för nätverks säkerhets gruppen (NSG)](../../network-watcher/network-watcher-nsg-flow-logging-overview.md)|JSON-format, visar utgående och inkommande flöden per regel|Visar information om inkommande och utgående IP-trafik via en nätverks säkerhets grupp.|[Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md)|
|[Program insikter](../../azure-monitor/app/app-insights-overview.md)|Loggar, undantag och anpassad diagnostik|  Tillhandahåller en tjänst för program prestanda övervakning (APM) för webbutvecklare på flera plattformar.| REST API [Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)|
|[Process data/säkerhets aviseringar](../../security-center/security-center-intro.md)|  Azure Security Center aviseringar Azure Monitor loggar aviseringar|    Tillhandahåller säkerhets information och aviseringar.|  REST API: er, JSON|

## <a name="log-integration-with-on-premises-siem-systems"></a>Logg integrering med lokala SIEM-system
Att [integrera Security Center varningar](../../security-center/security-center-export-data-to-siem.md) beskriver hur du synkroniserar Security Center aviseringar, säkerhets händelser för virtuella datorer som samlas in av Azure Diagnostics-loggar och Azure audit-loggar med dina Azure Monitor loggar eller SIEM-lösning.

## <a name="next-steps"></a>Nästa steg

- [Granskning och loggning](management-monitoring-overview.md): skydda data genom att underhålla insyn och svara snabbt på säkerhets aviseringar.

- [Säkerhets loggning och Gransknings logg insamling i Azure](https://azure.microsoft.com/resources/videos/security-logging-and-audit-log-collection/): Använd de här inställningarna för att se till att dina Azure-instanser samlar in rätt säkerhets-och gransknings loggar.

- [Konfigurera gransknings inställningar för en webbplats samling](https://support.office.com/article/Configure-audit-settings-for-a-site-collection-A9920C97-38C0-44F2-8BCB-4CF1E2AE22D2?ui=&rs=&ad=US): om du är en webbplats samlings administratör hämtar du historiken för enskilda användares åtgärder och historiken för de åtgärder som vidtagits under ett visst datum intervall.

- [Sök i gransknings loggen i office 365-säkerhets-och Efterlevnadscenter](https://support.office.com/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=&rs=&ad=US): använd Office 365-säkerhets-och Efterlevnadscenter för att söka i enhetlig Gransknings logg och Visa användar-och administratörs aktivitet i din Office 365-organisation.
