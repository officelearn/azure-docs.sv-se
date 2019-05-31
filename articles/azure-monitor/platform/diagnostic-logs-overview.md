---
title: Översikt över Azure diagnostikloggar
description: Läs mer om Azure diagnostikloggar i Azure Monitor och hur du kan använda dem för att förstå händelser som inträffar inom en Azure-resurs.
author: nkiest
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: nikiest
ms.subservice: logs
ms.openlocfilehash: 8902e29baa5802e3416bcda97ca59a5576d41829
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244864"
---
# <a name="overview-of-azure-diagnostic-logs"></a>Översikt över Azure diagnostikloggar

**Diagnostikloggar** tillhandahåller omfattande, frekventa data om användningen av en Azure-resurs. Azure Monitor tillhandahåller finns två typer av diagnostikloggar:

* **Klient loggar** -loggarna som kommer från på klientnivå tjänster som finns utanför en Azure-prenumeration som Azure Active Directory-loggar.
* **Resursen loggar** -loggarna som kommer från Azure-tjänster som distribuera resurser inom en Azure-prenumeration, till exempel Nätverkssäkerhetsgrupper eller Storage-konton.

    ![Resurs-diagnostics-loggar jämfört med andra typer av loggar](media/diagnostic-logs-overview/Diagnostics_Logs_vs_other_logs_v5.png)

Innehållet i de här loggarna varierar beroende på vilken Azure-tjänsten och resursen. Till exempel finns Nätverkssäkerhetsgrupp kopplad till regeln räknare och Key Vault granskningar två typer av diagnostikloggar.

Dessa loggar skiljer sig från den [aktivitetsloggen](activity-logs-overview.md). Aktivitetsloggen ger insikt i de åtgärder som utförts på resurser i din prenumeration med hjälp av Resource Manager, till exempel skapar en virtuell dator eller ta bort en logikapp. Aktivitetsloggen är en prenumerationsnivå logg. Resursnivå diagnostikloggar ger information om åtgärder som utförts i den här resursen, till exempel hämta en hemlighet från Key Vault.

Dessa loggar är också skiljer sig från diagnostikloggar för gäst-OS-nivå. Gäst-OS-diagnostikloggar är dessa som samlats in av en agent som körs i en virtuell dator eller andra stöds resurstyp. Resursnivå diagnostikloggar kräver inga agenten och avbilda resurs-specifika data från Azure-plattformen, diagnostikloggar för gäst-OS-nivå samla in data från det operativsystem och program som körs på en virtuell dator.

Inte alla tjänster som stöder diagnostikloggar som beskrivs här. [Den här artikeln innehåller en lista för avsnittet vilka tjänster stöder diagnostikloggar](./../../azure-monitor/platform/diagnostic-logs-schema.md).

## <a name="what-you-can-do-with-diagnostic-logs"></a>Vad du kan göra med diagnostikloggar
Här följer några av de saker som du kan göra med diagnostikloggar:

![Logiska placeringen av diagnostikloggar](./media/diagnostic-logs-overview/Diagnostics_Logs_Actions.png)

* Sparar dem till en [ **Lagringskonto** ](../../azure-monitor/platform/archive-diagnostic-logs.md) för granskning eller manuell granskning. Du kan ange kvarhållning tid (i dagar) med **resursdiagnostikinställningar**.
* [Stream att **Händelsehubbar** ](diagnostic-logs-stream-event-hubs.md) för inmatning av en tjänst från tredje part eller anpassade analyslösning till exempel Power BI.
* Analysera dem med [Azure Monitor](../../azure-monitor/platform/collect-azure-metrics-logs.md), där data skrivs direkt till Azure Monitor och behöver inte först skriva data till lagring.  

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Du kan använda ett lagringskonto eller Event Hubs-namnområde som inte är i samma prenumeration som det genererar loggar. Den användare som konfigurerar inställningen måste ha lämplig RBAC-åtkomst till båda prenumerationerna.

> [!NOTE]
>  Du kan för närvarande inte arkivera flödesloggar för nätverk till ett lagringskonto som ligger bakom ett skyddat virtuellt nätverk.

## <a name="diagnostic-settings"></a>Diagnostikinställningar

Resursdiagnostikloggar är konfigurerade med resursdiagnostikinställningar. Klient-diagnostikloggar är konfigurerade med en diagnostikinställning för klienten. **Diagnostikinställningar** för en kontroll av tjänsten:

* Diagnostikloggar och mått mottagna (Storage-konto, Händelsehubbar och/eller Azure Monitor).
* Vilka loggkategorier skickas och om måttdata skickas också.
* Hur länge varje loggkategori ska behållas i ett lagringskonto.
    - En kvarhållning av noll dagar innebär loggar hålls alltid. I annat fall kan värdet vara valfritt antal dagar mellan 1 och 365.
    - Om principerna för kvarhållning har angetts men lagra loggar i ett Lagringskonto är inaktiverad (till exempel om det bara Event Hubs eller Log Analytics-alternativen är markerade), påverkar principerna för kvarhållning inte.
    - Principer för kvarhållning är tillämpad per dag, så i slutet av en dag (UTC) loggar från den dag som är nu utöver kvarhållning principen tas bort. Till exempel om du har en bevarandeprincip för en dag skulle i början av dagen idag loggar från dag innan igår tas bort. Ta bort börjar vid midnatt UTC-tid, men Observera att det kan ta upp till 24 timmar innan loggarna som ska tas bort från ditt lagringskonto.

De här inställningarna är konfigurerade från diagnostikinställningar i portalen med Azure PowerShell och CLI-kommandon eller med den [Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/).

> [!NOTE]
> Det går för närvarande inte att skicka flerdimensionella mätvärden via diagnostikinställningar. Mått med dimensioner exporteras som tillplattade endimensionella mått som aggregeras över dimensionsvärden.
>
> *Till exempel*: Måttet för inkommande meddelanden i en händelsehubb kan utforskas och visas för varje enskild kö. När måttet exporteras via diagnostikinställningar visas det dock som alla inkommande meddelanden i alla köer i händelsehubben.
>
>

## <a name="supported-services-categories-and-schemas-for-diagnostic-logs"></a>Tjänster som stöds, kategorier och scheman för diagnostikloggar

[Se den här artikeln](../../azure-monitor/platform/diagnostic-logs-schema.md) för en fullständig lista över tjänster som stöds och loggkategorier och scheman som används av dessa tjänster.

## <a name="next-steps"></a>Nästa steg

* [Stream resursdiagnostikloggar till **Event Hubs**](diagnostic-logs-stream-event-hubs.md)
* [Ändra resursdiagnostikinställningar med hjälp av Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/)
* [Analysera loggar från Azure storage med Azure Monitor](collect-azure-metrics-logs.md)
