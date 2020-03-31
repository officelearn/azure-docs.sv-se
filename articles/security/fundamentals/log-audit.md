---
title: Loggning och granskning av Azure-säkerhet | Microsoft-dokument
description: Läs mer om loggarna som är tillgängliga i Azure och de säkerhetsinsikter du kan få.
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
ms.date: 10/31/2019
ms.author: terrylan
ms.openlocfilehash: bd0f42507e22559690e2682a391c53b9c090aa6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75750798"
---
# <a name="azure-security-logging-and-auditing"></a>Loggning och granskning av Azure-säkerhet

Azure tillhandahåller ett brett utbud av konfigurerbara säkerhetsgransknings- och loggningsalternativ som hjälper dig att identifiera luckor i dina säkerhetsprinciper och mekanismer. I den här artikeln beskrivs hur du skapar, samlar in och analyserar säkerhetsloggar från tjänster som finns på Azure.

> [!Note]
> Vissa rekommendationer i den här artikeln kan resultera i ökad data-, nätverks- eller beräkningsresursanvändning och öka licens- eller prenumerationskostnaderna.

## <a name="types-of-logs-in-azure"></a>Typer av loggar i Azure

Molnprogram är komplexa med många rörliga delar. Loggning av data kan ge insikter om dina program och hjälpa dig:

- Felsöka tidigare problem eller förhindra potentiella problem
- Förbättra programmets prestanda eller underhållsbarhet
- Automatisera åtgärder som annars skulle kräva manuella åtgärder

Azure-loggar kategoriseras i följande typer:
* **Kontroll-/hanteringsloggar** innehåller information om Azure Resource Manager CREATE-, UPDATE- och DELETE-åtgärder. Mer information finns i [Azure-aktivitetsloggar](../../azure-monitor/platform/platform-logs-overview.md).

* **Dataplanloggar** ger information om händelser som utlöses som en del av Azure-resursanvändning. Exempel på den här typen av logg är Windows händelsesystem, säkerhet och programloggar i en virtuell dator (VM) och [diagnostikloggar](../../azure-monitor/platform/platform-logs-overview.md) som konfigureras via Azure Monitor.

* **Bearbetade händelser** ger information om analyserade händelser/aviseringar som har bearbetats för din räkning. Exempel på den här typen är [Azure Security Center-aviseringar](../../security-center/security-center-managing-and-responding-alerts.md) där [Azure Security Center](../../security-center/security-center-intro.md) har bearbetat och analyserat din prenumeration och ger koncisa säkerhetsaviseringar.

I följande tabell visas de viktigaste typerna av loggar som är tillgängliga i Azure:

| Loggkategori | Typ av logg | Användning | Integrering |
| ------------ | -------- | ------ | ----------- |
|[Aktivitetsloggar](../../azure-monitor/platform/platform-logs-overview.md)|Kontrollplanshändelser på Azure Resource Manager-resurser|  Ger insikt i de åtgärder som utfördes på resurser i din prenumeration.|    Rest API, [Azure Monitor](../../azure-monitor/platform/platform-logs-overview.md)|
|[Azure-resursloggar](../../azure-monitor/platform/platform-logs-overview.md)|Frekventa data om driften av Azure Resource Manager-resurser i prenumerationen|   Ger insikt i åtgärder som din resurs själv har utfört.| Azure Monitor|
|[Azure Active Directory-rapportering](../../active-directory/reports-monitoring/overview-reports.md)|Loggar och rapporter | Rapporterar inloggningsaktiviteter för användare och systemaktivitet om användare och grupphantering.|[Api för diagram](../../active-directory/develop/active-directory-graph-api-quickstart.md)|
|[Virtuella datorer och molntjänster](../../azure-monitor/learn/quick-collect-azurevm.md)|Windows Event Log-tjänst och Linux Syslog|  Samlar in systemdata och loggar data på de virtuella datorerna och överför dessa data till ett lagringskonto som du väljer.|   Windows (med Windows Azure Diagnostics [[WAD](../../monitoring-and-diagnostics/azure-diagnostics.md)] storage) och Linux i Azure Monitor|
|[Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)|Lagringsloggning, tillhandahåller måttdata för ett lagringskonto|Ger insikt i spårningsbegäranden, analyserar användningstrender och diagnostiserar problem med ditt lagringskonto.|   REST API eller [klientbiblioteket](https://msdn.microsoft.com/library/azure/mt347887.aspx)|
|[NSG-flödesloggar (Network Security Group)](../../network-watcher/network-watcher-nsg-flow-logging-overview.md)|JSON-format, visar utgående och inkommande flöden per regel|Visar information om inkommande och utgående IP-trafik via en nätverkssäkerhetsgrupp.|[Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md)|
|[Programinsikt](../../azure-monitor/app/app-insights-overview.md)|Loggar, undantag och anpassad diagnostik|  Tillhandahåller en APM-tjänst (Application Performance Monitoring) för webbutvecklare på flera plattformar.| REST API, [Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)|
|[Processdata/säkerhetsvarningar](../../security-center/security-center-intro.md)|  Azure Security Center-aviseringar, Azure Monitor loggar aviseringar|    Tillhandahåller säkerhetsinformation och aviseringar.|  REST API: er, JSON|

## <a name="log-integration-with-on-premises-siem-systems"></a>Loggintegration med lokala SIEM-system
[Genom att integrera Security Center-aviseringar beskrivs](../../security-center/security-center-export-data-to-siem.md) hur du synkroniserar Security Center-aviseringar, säkerhetshändelser för virtuella datorer som samlas in av Azure-diagnostikloggar och Azure-granskningsloggar med dina Azure Monitor-loggar eller SIEM-lösning.

## <a name="next-steps"></a>Nästa steg

- [Granskning och loggning](management-monitoring-overview.md): Skydda data genom att upprätthålla synlighet och svara snabbt på säkerhetsaviseringar i rätt tid.

- [Insamling av säkerhetsloggning och granskningslogg i Azure](https://azure.microsoft.com/resources/videos/security-logging-and-audit-log-collection/): Tvinga fram dessa inställningar för att säkerställa att dina Azure-instanser samlar in rätt säkerhets- och granskningsloggar.

- [Konfigurera granskningsinställningar för en webbplatssamling](https://support.office.com/article/Configure-audit-settings-for-a-site-collection-A9920C97-38C0-44F2-8BCB-4CF1E2AE22D2?ui=&rs=&ad=US): Om du är administratör för webbplatssamlingen hämtar du historiken för enskilda användares åtgärder och historiken över de åtgärder som vidtagits under ett visst datumintervall.

- [Sök i granskningsloggen i Office 365 Security & Compliance Center](https://support.office.com/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=&rs=&ad=US): Använd Office 365 Security & Compliance Center för att söka i den enhetliga granskningsloggen och visa användar- och administratörsaktivitet i office 365-organisationen.
