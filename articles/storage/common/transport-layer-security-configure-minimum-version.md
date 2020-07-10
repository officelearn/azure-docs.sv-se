---
title: Konfigurera den tidigaste version av Transport Layer Security (TLS) som krävs för ett lagrings konto
titleSuffix: Azure Storage
description: Konfigurera ett lagrings konto för att kräva en lägsta version av Transport Layer Security (TLS) för klienter som begär förfrågningar mot Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/08/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: ab83f0ee656dfc717284c1e26d10dcb814fe1c9e
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2020
ms.locfileid: "86209862"
---
# <a name="configure-minimum-required-version-of-transport-layer-security-tls-for-a-storage-account"></a>Konfigurera den tidigaste version av Transport Layer Security (TLS) som krävs för ett lagrings konto

Kommunikation mellan ett klient program och ett Azure Storage-konto krypteras med hjälp av Transport Layer Security (TLS). TLS är ett standardiserat kryptografiskt protokoll som garanterar integritet och data integritet mellan klienter och tjänster via Internet. Mer information om TLS finns i [Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security).

Azure Storage stöder för närvarande tre versioner av TLS-protokollet: 1,0, 1,1 och 1,2. TLS 1,2 är den säkraste versionen av TLS. Azure Storage använder TLS 1,2 på offentliga HTTPs-slutpunkter, men TLS 1,0 och TLS 1,1 stöds fortfarande för bakåtkompatibilitet.

Som standard tillåter Azure Storage-konton att klienter skickar och tar emot data med den äldsta versionen av TLS, TLS 1,0 och senare. Om du vill använda strängare säkerhets åtgärder kan du konfigurera ditt lagrings konto för att kräva att klienter skickar och tar emot data med en nyare version av TLS. Om ett lagrings konto kräver en lägsta version av TLS, kommer alla begär Anden som görs med en tidigare version att Miss förvaltas.

Den här artikeln beskriver hur du konfigurerar ett lagrings konto för att kräva att klienter skickar begär Anden med en lägsta version av TLS. Information om hur du anger en viss version av TLS när du skickar en begäran från ett klient program finns i [konfigurera Transport Layer Security (TLS) för ett klient program](transport-layer-security-configure-client-version.md).

## <a name="detect-the-tls-version-used-by-client-applications"></a>Identifiera den TLS-version som används av klient program

När du tillämpar en lägsta TLS-version för ditt lagrings konto riskerar du att avvisa begär Anden från klienter som skickar data med en tidigare version av TLS. För att förstå hur konfigurering av den lägsta TLS-versionen kan påverka klient program, rekommenderar Microsoft att du aktiverar loggning för ditt Azure Storage-konto och analyserar loggarna efter ett tidsintervall för att fastställa vilka versioner av TLS-klientprogrammet som används.

Om du vill logga förfrågningar till ditt Azure Storage-konto och fastställa vilken TLS-version som används av klienten, kan du använda Azure Storage inloggning Azure Monitor (för hands version). Mer information finns i [övervaka Azure Storage](monitor-storage.md).

Azure Storage loggning i Azure Monitor har stöd för att använda logg frågor för att analysera loggdata. Om du vill söka i loggar kan du använda en Azure Log Analytics-arbetsyta. Mer information om logg frågor finns i [Självstudier: komma igång med Log Analytics frågor](../../azure-monitor/log-query/get-started-portal.md).

Om du vill logga Azure Storage data med Azure Monitor och analysera dem med Azure Log Analytics måste du först skapa en diagnostisk inställning som anger vilka typer av begär Anden och för vilka lagrings tjänster du vill logga data. Följ dessa steg om du vill skapa en diagnostisk inställning i Azure Portal:

1. Registrera dig för för [hands versionen av Azure Storage i Azure Monitor](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u).
1. Skapa en ny Log Analytics-arbetsyta i prenumerationen som innehåller ditt Azure Storage-konto. När du har konfigurerat loggning för ditt lagrings konto är loggarna tillgängliga i Log Analytics arbets ytan. Mer information finns i [skapa en Log Analytics arbets yta i Azure Portal](../../azure-monitor/learn/quick-create-workspace.md).
1. Navigera till ditt lagringskonto i Azure-portalen.
1. I avsnittet övervakning väljer du **diagnostikinställningar (för hands version)**.
1. Välj den Azure Storage tjänst som du vill logga förfrågningar för. Välj till exempel **BLOB** för att logga förfrågningar till Blob Storage.
1. Välj **Lägg till diagnostisk inställning**.
1. Ange ett namn för den diagnostiska inställningen.
1. Under **kategori information**i avsnittet **logg** väljer du vilka typer av begär Anden som ska loggas. Du kan logga läsnings-, skriv-och borttagnings begär Anden. Om du till exempel väljer **StorageRead** och **StorageWrite** loggas Läs-och skriv förfrågningar till den valda tjänsten.
1. Under **mål information**väljer **du skicka till Log Analytics**. Välj din prenumeration och Log Analytics arbets ytan som du skapade tidigare, som du ser i följande bild.

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/create-diagnostic-setting-logs.png" alt-text="Skärm bild som visar hur du skapar en diagnostisk inställning för loggnings begär Anden":::

Efter att du har skapat den diagnostiska inställningen loggas begär anden till lagrings kontot sedan i enlighet med den inställningen. Mer information finns i [skapa diagnostisk inställning för insamling av resurs loggar och mått i Azure](../../azure-monitor/platform/diagnostic-settings.md).

En referens för fält som är tillgängliga i Azure Storage loggar i Azure Monitor finns i [resurs loggar (för hands version)](monitor-storage-reference.md#resource-logs-preview).

### <a name="query-logged-requests-by-tls-version"></a>Fråga loggade förfrågningar per TLS-version

Azure Storage loggar i Azure Monitor inkluderar den TLS-version som används för att skicka en begäran till ett lagrings konto. Använd egenskapen **TlsVersion** för att kontrol lera TLS-versionen av en loggad begäran.

Om du vill hämta loggar för de senaste 7 dagarna och bestämma hur många begär Anden som gjorts mot Blob Storage med varje version av TLS öppnar du Log Analytics arbets ytan. Klistra sedan in följande fråga i en ny logg fråga och kör den. Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden:

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AccountName == "<account-name>"
| summarize count() by TlsVersion
```

Resultaten visar antalet begär Anden som gjorts med varje version av TLS:

:::image type="content" source="media/transport-layer-security-configure-minimum-version/log-analytics-query-version.png" alt-text="Skärm bild som visar resultatet av Log Analytics-frågan för att returnera TLS-version":::

### <a name="query-logged-requests-by-caller-ip-address-and-user-agent-header"></a>Fråga loggade begär Anden efter uppringande IP-adress och användar agent huvud

Azure Storage loggar i Azure Monitor inkluderar även anropare IP-adress och användar agent huvud som hjälper dig att utvärdera vilka klient program som har åtkomst till lagrings kontot. Du kan analysera dessa värden för att bestämma om klient program måste uppdateras för att använda en nyare version av TLS eller om det är acceptabelt att klienten inte skickas med den lägsta TLS-versionen.

Om du vill hämta loggar för de senaste 7 dagarna och avgöra vilka klienter som utför begär Anden med en version av TLS före TLS 1,2, klistrar du in följande fråga i en ny logg fråga och kör den. Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden:

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AccountName == "<account-name>" and TlsVersion != "TLS 1.2"
| project TlsVersion, CallerIpAddress, UserAgentHeader
```

## <a name="configure-the-minimum-tls-version-for-an-account"></a>Konfigurera den lägsta TLS-versionen för ett konto

Om du vill konfigurera den lägsta TLS-versionen för ett lagrings konto använder du Azure Portal eller Azure CLI för att ange **minimumTlsVersion** -versionen för kontot. Den här egenskapen är tillgänglig för alla lagrings konton som skapas med Azure Resource Manager distributions modell. Mer information finns i [Översikt över lagrings konto](storage-account-overview.md).

# <a name="portal"></a>[Portal](#tab/portal)

Följ dessa steg om du vill konfigurera den lägsta TLS-versionen för ett lagrings konto med Azure Portal:

1. Navigera till ditt lagringskonto i Azure-portalen.
1. Välj **konfigurations** inställningen.
1. Under **lägsta TLS-version**använder du List rutan för att välja den lägsta version av TLS som krävs för att komma åt data i det här lagrings kontot, som du ser i följande bild.

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/configure-minimum-version-portal.png" alt-text="Skärm bild som visar hur du konfigurerar den lägsta versionen av TLS i Azure Portal":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill konfigurera den lägsta TLS-versionen för ett lagrings konto med Azure CLI måste du först hämta resurs-ID för ditt lagrings konto genom att anropa kommandot [AZ Resource show](/cli/azure/resource#az-resource-show) . Anropa sedan kommandot [AZ Resource Update](/cli/azure/resource#az-resource-update) för att ange **minimumTlsVersion** -egenskapen för lagrings kontot. Giltiga värden för **minimumTlsVersion** är `TLS1_0` `TLS1_1` och `TLS1_2` .

I följande exempel anges den lägsta TLS-versionen till 1,2. Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden:

```azurecli-interactive
storage_account_id=$(az resource show \
  --name <storage-account> \
  --resource-group <resource-group> \
  --resource-type Microsoft.Storage/storageAccounts \
  --query id \
  --output tsv)

az resource update \
  --ids $storage_account_id \
  --set properties.minimumTlsVersion="TLS1_2"
```

---

> [!NOTE]
> När du har uppdaterat den lägsta TLS-versionen för lagrings kontot kan det ta upp till 30 sekunder innan ändringen har spridits helt.

## <a name="check-the-minimum-required-tls-version-for-an-account"></a>Kontrol lera den minsta TLS-version som krävs för ett konto

Om du vill fastställa den minsta TLS-version som är konfigurerad för ett lagrings konto kontrollerar du egenskapen Azure Resource Manager **minimumTlsVersion** . Om du vill kontrol lera den här egenskapen för ett stort antal lagrings konton samtidigt använder du Azure Resource Graph Explorer.

Egenskapen **minimumTlsVersion** har inte angetts som standard och returnerar inte något värde förrän du uttryckligen anger det. Lagrings kontot har som standard tillstånd att tillåta begär Anden som skickas med TLS version 1,0 eller senare om egenskap svärdet är null.

### <a name="check-the-minimum-required-tls-version-for-a-single-storage-account"></a>Kontrol lera den minsta TLS-version som krävs för ett enda lagrings konto

Om du vill kontrol lera den minsta TLS-version som krävs för ett enda lagrings konto med Azure CLI anropar du kommandot [AZ Resource show](/cli/azure/resource#az-resource-show) och fråga efter egenskapen **minimumTlsVersion** :

```azurecli-interactive
az resource show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --resource-type Microsoft.Storage/storageAccounts \
    --query properties.minimumTlsVersion \
    --output tsv
```

### <a name="check-the-minimum-required-tls-version-for-a-set-of-storage-accounts"></a>Kontrol lera den minsta TLS-version som krävs för en uppsättning lagrings konton

Om du vill kontrol lera den minsta TLS-version som krävs för en uppsättning lagrings konton med optimala prestanda kan du använda Azure Resource Graph Explorer i Azure Portal. Mer information om hur du använder resurs diagram Utforskaren finns i [snabb start: kör din första resurs diagram fråga med Azure Resource Graph Explorer](/azure/governance/resource-graph/first-query-portal).

Om du kör följande fråga i resursens diagram Utforskaren returneras en lista över lagrings konton och den lägsta TLS-versionen för varje konto visas:

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend minimumTlsVersion = parse_json(properties).minimumTlsVersion
| project subscriptionId, resourceGroup, name, minimumTlsVersion
```

---

## <a name="test-the-minimum-tls-version-from-a-client"></a>Testa den lägsta TLS-versionen från en klient

Om du vill testa att den minsta obligatoriska TLS-versionen för ett lagrings konto tillåter att anrop görs med en tidigare version kan du konfigurera en klient att använda en tidigare version av TLS. Mer information om hur du konfigurerar en klient för att använda en specifik version av TLS finns i [konfigurera Transport Layer Security (TLS) för ett klient program](transport-layer-security-configure-client-version.md).

När en klient ansluter till ett lagrings konto med hjälp av en TLS-version som inte uppfyller den lägsta TLS-version som kon figurer ATS för kontot, returnerar Azure Storage felkod 400 fel (felaktig begäran) och ett meddelande som anger att den TLS-version som användes inte tillåts för att begära förfrågningar mot det här lagrings kontot.

## <a name="next-steps"></a>Nästa steg

- [Konfigurera Transport Layer Security (TLS) för ett klient program](transport-layer-security-configure-client-version.md)
- [Säkerhets rekommendationer för Blob Storage](../blobs/security-recommendations.md)
