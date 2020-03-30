---
title: Övervaka Azure-funktioner med Azure Monitor-loggar
description: Lär dig hur du använder Azure Monitor-loggar med Azure-funktioner för att övervaka funktionskörningar.
author: craigshoemaker
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: cshoe
ms.openlocfilehash: 13c72a1cf8a0dd4a1124e51b9ceee04ae04bf261
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77649882"
---
# <a name="monitoring-azure-functions-with-azure-monitor-logs"></a>Övervaka Azure-funktioner med Azure Monitor-loggar

Azure Functions erbjuder en integrering med [Azure Monitor-loggar](../azure-monitor/platform/data-platform-logs.md) för att övervaka funktioner. Den här artikeln visar hur du konfigurerar Azure Functions för att skicka systemgenererade och användargenererade loggar till Azure Monitor Logs.

Azure Monitor Logs ger dig möjlighet att konsolidera loggar från olika resurser på samma arbetsyta, där den kan [analyseras](../azure-monitor/log-query/log-query-overview.md) med frågor för att snabbt hämta, konsolidera och analysera insamlade data.  Du kan skapa och testa frågor med [Log Analytics](../azure-monitor/log-query/portals.md) i Azure-portalen och sedan antingen direkt analysera data med hjälp av dessa verktyg eller spara frågor för användning med [visualiseringar](../azure-monitor/visualizations.md) eller [varningsregler](../azure-monitor/platform/alerts-overview.md).

Azure Monitor använder en version av [Kusto-frågespråket](/azure/kusto/query/) som används av Azure Data Explorer och som är lämplig för enkla loggfrågor men även innehåller avancerade funktioner som aggregeringar, kopplingar och smart analys. Du kan snabbt lära dig frågespråket med [flera lektioner](../azure-monitor/log-query/get-started-queries.md).

> [!NOTE]
> Integrering med Azure Monitor Logs är för närvarande i offentlig förhandsversion för funktionsappar som körs på Windows-förbrukning, Premium och Dedikerade värdplaner.

## <a name="setting-up"></a>Inrätta

Välj **Diagnostikinställningar i** avsnittet **Övervakning** och klicka sedan på **Lägg till diagnostikinställning**.

![Lägga till en diagnostikinställning](media/functions-monitor-log-analytics/diagnostic-settings-add.png)

På sidan **Inställningar för diagnostik** väljer du Skicka till **Logganalys**och väljer sedan arbetsytan Log Analytics. Under **log** välj **FunctionAppLogs**innehåller den här tabellen önskade loggar.

![Lägga till en diagnostikinställning](media/functions-monitor-log-analytics/choose-table.png)

## <a name="user-generated-logs"></a>Användargenererade loggar

Om du vill generera anpassade loggar kan du använda den specifika loggningssatsen beroende på vilket språk du har, här är exempelkodavsnitt:


# <a name="c"></a>[C#](#tab/csharp)

```csharp
log.LogInformation("My app logs here.");
```

# <a name="java"></a>[Java](#tab/java)

```java
context.getLogger().info("My app logs here.");
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
context.log('My app logs here.');
```

# <a name="powershell"></a>[Powershell](#tab/powershell)

```powershell
Write-Host "My app logs here."
```

# <a name="python"></a>[Python](#tab/python)

```python
logging.info('My app logs here.')
```

---

## <a name="querying-the-logs"></a>Fråga loggarna

Om du vill fråga efter de genererade loggarna går du till arbetsytan Log Analytics som du har konfigurerat för att skicka funktionsloggarna till och klickar på **Loggar**.

![Frågefönster i LA-arbetsytan](media/functions-monitor-log-analytics/querying.png)

Azure Functions skriver alla loggar till **FunctionAppLogs** tabell, här är några exempelfrågor.

### <a name="all-logs"></a>Alla loggar

```

FunctionAppLogs
| order by TimeGenerated desc

```

### <a name="a-specific-function-logs"></a>En specifik funktion loggar

```

FunctionAppLogs
| where FunctionName == "<Function name>" 

```

### <a name="exceptions"></a>Undantag

```

FunctionAppLogs
| where ExceptionDetails != ""  
| order by TimeGenerated asc

```

## <a name="next-steps"></a>Nästa steg

- Granska [översikten Över Azure-funktioner](functions-overview.md)
- Läs mer om [Azure Monitor-loggar](../azure-monitor/platform/data-platform-logs.md)
- Läs mer om [frågespråket](../azure-monitor/log-query/get-started-queries.md).
