---
title: Förhindra anonym offentlig Läs behörighet till behållare och blobbar
titleSuffix: Azure Storage
description: Lär dig hur du analyserar anonyma begär Anden mot ett lagrings konto och hur du förhindrar anonym åtkomst för hela lagrings kontot eller för en enskild behållare.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/23/2020
ms.author: tamram
ms.reviewer: fryu
ms.openlocfilehash: e30c4142232a2d695204f5c8f612eb44791c847c
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2020
ms.locfileid: "87133171"
---
# <a name="prevent-anonymous-public-read-access-to-containers-and-blobs"></a>Förhindra anonym offentlig Läs behörighet till behållare och blobbar

Anonym offentlig Läs behörighet till behållare och blobbar i Azure Storage är ett bekvämt sätt att dela data, men det kan också innebära en säkerhets risk. Det är viktigt att du hanterar anonym åtkomst sparsamt och förstår hur du utvärderar anonym åtkomst till dina data. Drifts komplexitet, mänskligt fel eller skadlig attack mot data som är allmänt tillgängliga kan leda till kostsamma data intrång. Microsoft rekommenderar att du bara aktiverar anonym åtkomst när det är nödvändigt för ditt program scenario.

Som standard är offentlig åtkomst till dina BLOB-data alltid förbjuden. Standard konfigurationen för ett lagrings konto tillåter dock en användare med rätt behörighet att konfigurera offentlig åtkomst till behållare och blobbar i ett lagrings konto. För ökad säkerhet kan du neka all offentlig åtkomst till lagrings kontot, oavsett inställningen för offentlig åtkomst för en enskild behållare. Att neka offentlig åtkomst till lagrings kontot förhindrar att en användare aktiverar offentlig åtkomst för en behållare i kontot. Microsoft rekommenderar att du inte tillåter offentlig åtkomst till ett lagrings konto om inte ditt scenario kräver det. Om du inte tillåter offentlig åtkomst kan du förhindra data intrång orsakade av oönskad anonym åtkomst.

Om du inte tillåter offentlig BLOB-åtkomst för lagrings kontot, Azure Storage avvisar alla anonyma begär anden till det kontot. När offentlig åtkomst inte tillåts för ett konto kan behållare i det kontot inte konfigureras senare för offentlig åtkomst. Alla behållare som redan har kon figurer ATS för offentlig åtkomst accepterar inte längre anonyma begär Anden. Mer information finns i [Konfigurera anonym offentlig Läs behörighet för behållare och blobbar](anonymous-read-access-configure.md).

Den här artikeln beskriver hur du analyserar anonyma begär Anden mot ett lagrings konto och hur du förhindrar anonym åtkomst för hela lagrings kontot eller för en enskild behållare.

## <a name="detect-anonymous-requests-from-client-applications"></a>Identifiera anonyma begär Anden från klient program

Om du inte tillåter offentlig Läs behörighet för ett lagrings konto riskerar du att avvisa begär anden till behållare och blobbar som för närvarande är konfigurerade för offentlig åtkomst. Om den offentliga åtkomsten för ett lagrings konto nekas åsidosätts de offentliga åtkomst inställningarna för enskilda behållare i lagrings kontot. När offentlig åtkomst inte tillåts för lagrings kontot kommer alla framtida anonyma begär anden till det kontot att Miss förväntas.

För att förstå hur otillåten offentlig åtkomst kan påverka klient program, rekommenderar Microsoft att du aktiverar loggning och mått för kontot och analyserar mönster för anonyma begär Anden under ett tidsintervall. Använd mått för att fastställa antalet anonyma begär anden till lagrings kontot och Använd loggar för att avgöra vilka behållare som ska användas anonymt.

### <a name="monitor-anonymous-requests-with-metrics-explorer"></a>Övervaka anonyma begär Anden med Metrics Explorer

Om du vill spåra anonyma begär anden till ett lagrings konto använder du Azure Metrics Explorer i Azure Portal. Mer information om Metrics Explorer finns i [komma igång med Azure Metrics Explorer](../../azure-monitor/platform/metrics-getting-started.md).

Följ dessa steg om du vill skapa ett mått som spårar anonyma begär Anden:

1. Navigera till ditt lagringskonto i Azure-portalen. Under avsnittet **övervakning** väljer du **mått**.
1. Välj **Lägg till mått**. I dialog rutan **mått** anger du följande värden:
    1. Lämna fältet scope inställt på namnet på lagrings kontot.
    1. Ange **mått namn området** till *BLOB*. Det här måttet rapporterar endast begär Anden mot Blob Storage.
    1. Ange fältet **mått** till *transaktioner*.
    1. Ange **agg regerings** fältet som ska *summeras*.

    Det nya måttet visar summan av antalet transaktioner för Blob Storage under ett angivet tidsintervall. Det resulterande måttet visas som det visas i följande bild:

    :::image type="content" source="media/anonymous-read-access-prevent/configure-metric-blob-transactions.png" alt-text="Skärm bild som visar hur du konfigurerar måttet till att summera BLOB-transaktioner":::

1. Välj sedan knappen **Lägg till filter** för att skapa ett filter på måttet för anonyma begär Anden.
1. I dialog rutan **filter** anger du följande värden:
    1. Ange **egenskap** svärdet till *autentisering*.
    1. Ställ in **operator** -fältet på likhets tecknet (=).
    1. Ange fältet **värden** som *Anonym*.
1. I det övre högra hörnet väljer du det tidsintervall som du vill visa måttet för. Du kan också ange hur detaljerad agg regeringen av förfrågningar ska vara genom att ange intervall var som helst från 1 minut till 1 månad.

När du har konfigurerat måttet kommer anonyma begär Anden att visas i grafen. Följande bild visar de anonyma begär Anden som aggregerats under de senaste 30 minuterna.

:::image type="content" source="media/anonymous-read-access-prevent/metric-anonymous-blob-requests.png" alt-text="Skärm bild som visar sammanställda anonyma begär Anden mot Blob Storage":::

Du kan också konfigurera en varnings regel för att meddela dig när ett visst antal anonyma begär Anden görs mot ditt lagrings konto. Mer information finns i [skapa, Visa och hantera mått aviseringar med hjälp av Azure Monitor](../../azure-monitor/platform/alerts-metric.md).

### <a name="analyze-logs-to-identify-containers-receiving-anonymous-requests"></a>Analysera loggar för att identifiera behållare som tar emot anonyma begär Anden

Azure Storage loggar fångar information om begär Anden som gjorts mot lagrings kontot, inklusive hur en begäran har auktoriserats. Du kan analysera loggarna för att avgöra vilka behållare som tar emot anonyma begär Anden.

Om du vill logga begär anden till ditt Azure Storage konto för att utvärdera anonyma begär Anden, kan du använda Azure Storage inloggning Azure Monitor (för hands version). Mer information finns i [övervaka Azure Storage](../common/monitor-storage.md).

Azure Storage loggning i Azure Monitor har stöd för att använda logg frågor för att analysera loggdata. Om du vill söka i loggar kan du använda en Azure Log Analytics-arbetsyta. Mer information om logg frågor finns i [Självstudier: komma igång med Log Analytics frågor](../../azure-monitor/log-query/get-started-portal.md).

#### <a name="create-a-diagnostic-setting-in-the-azure-portal"></a>Skapa en diagnostisk inställning i Azure Portal

Om du vill logga Azure Storage data med Azure Monitor och analysera dem med Azure Log Analytics måste du först skapa en diagnostisk inställning som anger vilka typer av begär Anden och för vilka lagrings tjänster du vill logga data. Följ dessa steg om du vill skapa en diagnostisk inställning i Azure Portal:

1. Registrera dig för för [hands versionen av Azure Storage i Azure Monitor](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u).
1. Skapa en ny Log Analytics-arbetsyta i prenumerationen som innehåller ditt Azure Storage-konto. När du har konfigurerat loggning för ditt lagrings konto är loggarna tillgängliga i Log Analytics arbets ytan. Mer information finns i [skapa en Log Analytics arbets yta i Azure Portal](../../azure-monitor/learn/quick-create-workspace.md).
1. Navigera till ditt lagringskonto i Azure-portalen.
1. I avsnittet övervakning väljer du **diagnostikinställningar (för hands version)**.
1. Välj **BLOB** för att logga förfrågningar som görs mot Blob Storage.
1. Välj **Lägg till diagnostisk inställning**.
1. Ange ett namn för den diagnostiska inställningen.
1. Under **kategori information**i avsnittet **logg** väljer du vilka typer av begär Anden som ska loggas. Alla anonyma begär Anden kommer att läsas, så välj **StorageRead** för att avbilda anonyma begär Anden.
1. Under **mål information**väljer **du skicka till Log Analytics**. Välj din prenumeration och Log Analytics arbets ytan som du skapade tidigare, som du ser i följande bild.

    :::image type="content" source="media/anonymous-read-access-prevent/create-diagnostic-setting-logs.png" alt-text="Skärm bild som visar hur du skapar en diagnostisk inställning för loggnings begär Anden":::

Efter att du har skapat den diagnostiska inställningen loggas begär anden till lagrings kontot sedan i enlighet med den inställningen. Mer information finns i [skapa diagnostisk inställning för insamling av resurs loggar och mått i Azure](../../azure-monitor/platform/diagnostic-settings.md).

En referens för fält som är tillgängliga i Azure Storage loggar i Azure Monitor finns i [resurs loggar (för hands version)](../common/monitor-storage-reference.md#resource-logs-preview).

#### <a name="query-logs-for-anonymous-requests"></a>Query-loggar för anonyma begär Anden

Azure Storage loggar i Azure Monitor innehåller den typ av auktorisering som användes för att göra en begäran till ett lagrings konto. Filtrera på egenskapen **AuthenticationType** i din logg fråga för att Visa anonyma begär Anden.

Om du vill hämta loggar för de senaste 7 dagarna för anonyma begär Anden mot Blob Storage öppnar du Log Analytics arbets ytan. Klistra sedan in följande fråga i en ny logg fråga och kör den:

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AuthenticationType == "Anonymous"
| project TimeGenerated, AccountName, AuthenticationType, Uri
```

Du kan också konfigurera en varnings regel baserat på den här frågan för att meddela dig om anonyma begär Anden. Mer information finns i [skapa, Visa och hantera logg aviseringar med hjälp av Azure Monitor](../../azure-monitor/platform/alerts-log.md).

## <a name="remediate-anonymous-public-access"></a>Reparera anonym offentlig åtkomst

När du har utvärderat anonyma begär anden till behållare och blobbar i ditt lagrings konto kan du vidta åtgärder för att begränsa eller förhindra offentlig åtkomst. Om några behållare i lagrings kontot kan behöva vara tillgängliga för offentlig åtkomst kan du konfigurera den offentliga åtkomst inställningen för varje behållare i ditt lagrings konto. Det här alternativet ger den mest detaljerade kontrollen över offentlig åtkomst. Mer information finns i [Ange offentlig åtkomst nivå för en behållare](anonymous-read-access-configure.md#set-the-public-access-level-for-a-container).

För ökad säkerhet kan du neka offentlig åtkomst för hela lagrings kontot. Den offentliga åtkomst inställningen för ett lagrings konto åsidosätter de enskilda inställningarna för behållare i det kontot. Om du inte tillåter offentlig åtkomst för ett lagrings konto är alla behållare som är konfigurerade för att tillåta offentlig åtkomst inte längre tillgängliga anonymt. Mer information finns i [Tillåt eller neka offentlig Läs behörighet för ett lagrings konto](anonymous-read-access-configure.md#allow-or-disallow-public-read-access-for-a-storage-account).

Om ditt scenario kräver att vissa behållare måste vara tillgängliga för offentlig åtkomst kan det vara lämpligt att flytta dessa behållare och deras blobbar till lagrings konton som är reserverade för offentlig åtkomst. Du kan sedan neka offentlig åtkomst för andra lagrings konton.

### <a name="verify-that-public-access-to-a-blob-is-not-permitted"></a>Kontrol lera att offentlig åtkomst till en BLOB inte tillåts

För att kontrol lera att offentlig åtkomst till en viss BLOB inte tillåts kan du försöka hämta blobben via dess URL. Om hämtningen lyckas är blobben fortfarande offentligt tillgänglig. Om blobben inte är offentligt tillgänglig eftersom offentlig åtkomst har varit inaktive ras för lagrings kontot visas ett fel meddelande som anger att offentlig åtkomst inte tillåts på det här lagrings kontot.

I följande exempel visas hur du använder PowerShell för att försöka ladda ned en BLOB via URL: en. Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden:

```powershell
$url = "<absolute-url-to-blob>"
$downloadTo = "<file-path-for-download>"
Invoke-WebRequest -Uri $url -OutFile $downloadTo -ErrorAction Stop
```

### <a name="verify-that-modifying-the-containers-public-access-setting-is-not-permitted"></a>Kontrol lera att det inte är tillåtet att ändra behållarens offentliga åtkomst inställning

För att verifiera att en behållares offentliga åtkomst inställning inte kan ändras efter att offentlig åtkomst inte tillåts för lagrings kontot kan du försöka ändra inställningen. Det går inte att ändra behållarens offentliga åtkomst inställning om ingen offentlig åtkomst tillåts för lagrings kontot.

I följande exempel visas hur du använder PowerShell för att försöka ändra en behållares offentliga åtkomst inställning. Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$containerName = "<container-name>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

Set-AzStorageContainerAcl -Context $ctx -Container $containerName -Permission Blob
```

### <a name="verify-that-creating-a-container-with-public-access-enabled-is-not-permitted"></a>Kontrol lera att det inte är tillåtet att skapa en behållare med aktive rad offentlig åtkomst

Om offentlig åtkomst inte tillåts för lagrings kontot kan du inte skapa en ny behållare med offentlig åtkomst aktive rad. För att verifiera kan du försöka skapa en behållare med offentlig åtkomst aktive rad.

I följande exempel visas hur du använder PowerShell för att försöka skapa en behållare med offentlig åtkomst aktive rad. Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$containerName = "<container-name>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

New-AzStorageContainer -Name $containerName -Permission Blob -Context $ctx
```

## <a name="next-steps"></a>Nästa steg

- [Konfigurera anonym offentlig Läs behörighet för behållare och blobbar](anonymous-read-access-configure.md)
- [Få åtkomst till offentliga behållare och blobbar anonymt med .NET](anonymous-read-access-client.md)
