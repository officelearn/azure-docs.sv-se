---
title: Felsöka Azure Metric-aviseringar
description: Vanliga problem med Azure Monitor metriska aviseringar och möjliga lösningar.
author: harelbr
ms.author: harelbr
ms.topic: reference
ms.date: 06/21/2020
ms.subservice: alerts
ms.openlocfilehash: 36ff80bc0858d6d08cc120d126628de02ba6e703
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85130746"
---
# <a name="troubleshooting-problems-in-azure-monitor-metric-alerts"></a>Fel sökning av problem i Azure Monitor mått varningar 

I den här artikeln beskrivs vanliga problem i Azure Monitor [Metric-aviseringar](alerts-metric-overview.md) och fel sökning.

Azure Monitor aviseringar proaktivt meddela dig när viktiga villkor finns i dina övervaknings data. De gör att du kan identifiera och åtgärda problem innan användarna av systemet ser dem. Mer information om aviseringar finns i [Översikt över aviseringar i Microsoft Azure](alerts-overview.md).

## <a name="metric-alert-should-have-fired-but-didnt"></a>Mått aviseringen bör ha utlösts men inte 

Om du tror att en måtta avisering ska ha utlösts men den inte har startats och inte finns i Azure Portal kan du prova följande steg:

1. **Konfiguration** – granska varnings regel konfigurationen för att kontrol lera att den är korrekt konfigurerad:
    - Kontrol lera att **sammansättnings typen**, **agg regerings precisionen (period)** och **tröskelvärdet** eller **känslighets** värdet är konfigurerade som förväntat
    - För en varnings regel som använder dynamiska tröskelvärden kontrollerar du om avancerade inställningar har kon figurer ATS. **antalet överträdelser** kan filtrera aviseringar och **Ignorera data innan** de kan påverka hur tröskelvärdena beräknas

       > [!NOTE] 
       > Dynamiska tröskelvärden kräver minst 3 dagar och 30 mått exempel innan de blir aktiva.

2. **Utlöses men ingen avisering** – granska [listan över utlösta aviseringar](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/alertsV2) för att se om du kan hitta den utlöst aviseringen. Om du kan se aviseringen i listan, men har problem med några av dess åtgärder eller aviseringar, se mer information [här](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-troubleshoot#action-or-notification-on-my-alert-did-not-work-as-expected).

3. **Redan aktiv** -kontrol lera om det redan finns en utlöst avisering på den Metric Time-serie som du förväntar dig att få en avisering för. Mått aviseringar är tillstånds känsliga, vilket innebär att när en avisering har Aktiver ATS för en speciell mått tids serie, utlöses inte ytterligare aviseringar för den tids serien förrän problemet inte längre har iakttagits. Det här design alternativet minskar bruset. Aviseringen löses automatiskt när varnings villkoret inte uppfylls för tre efterföljande utvärderingar.

4. **Använda dimensioner** – om du har valt några [Dimensions värden för ett mått](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#using-dimensions)övervakar varnings regeln varje individuell mått tids serie (som definieras av kombinationen av dimensions värden) för en tröskel överträdelse. För att övervaka den sammanställda mått tids serien (utan valda dimensioner) konfigurerar du ytterligare en varnings regel för måttet utan att välja dimensioner.

5. **Agg regerings-och tids kornig het** – om du visualiserar måttet med hjälp av [mått diagram](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/metrics)bör du se till att:
    * Den valda **agg regeringen** i mått diagrammet är samma som **agg regerings typ** i aviserings regeln
    * Den valda **tids kornig het** är samma som **agg regerings precisionen (period)** i aviserings regeln (och är inte inställd på automatiskt)

## <a name="metric-alert-fired-when-it-shouldnt-have"></a>Mått aviseringen utlöses när den inte borde ha

Om du tror att din måtta avisering inte borde ha utlösts utan den gjorde kan följande steg hjälpa dig att lösa problemet.

1. Granska [listan över aktiverade varningar](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/alertsV2) för att hitta den utlöst aviseringen och klicka på för att visa information om den. Läs informationen under **Varför hjälpte den här aviseringen?** om du vill se mått diagrammet, **Metric-värdet**och **tröskelvärdet** vid den tidpunkt då aviseringen utlöstes.

    > [!NOTE] 
    > Om du använder villkorstypen Dynamiska tröskelvärden och anser att tröskelvärdena som används var felaktiga kan du ge feedback med ikonen för bister min. Den här feedbacken påverkar forskningen om Machine Learning-algoritmer och hjälper till att förbättra framtida identifieringar.

2. Om du har valt flera dimensions värden för ett mått utlöses aviseringen när **någon** av mått tids serien (som definieras av kombinationen av dimensions värden) bryter mot tröskelvärdet. Mer information om hur du använder dimensioner i måttaviseringar finns [här](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#using-dimensions).

3. Granska varnings regel konfigurationen för att kontrol lera att den är korrekt konfigurerad:
    - Kontrol lera att **sammansättnings typen**, **agg regerings precisionen (period)** och **tröskelvärdet** eller **känslighets** värdet är konfigurerade som förväntat
    - För en varnings regel som använder dynamiska tröskelvärden kontrollerar du om avancerade inställningar har kon figurer ATS. **antalet överträdelser** kan filtrera aviseringar och **Ignorera data innan** de kan påverka hur tröskelvärdena beräknas

   > [!NOTE]
   > Dynamiska tröskelvärden kräver minst 3 dagar och 30 mått exempel innan de blir aktiva.

4. Om du visualiserar måttet med [mått diagram](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/metrics)bör du se till att:
    - Den valda **agg regeringen** i mått diagrammet är samma som **agg regerings typ** i aviserings regeln
    - Den valda **tids kornig het** är samma som **agg regerings precisionen (period)** i aviserings regeln (och är inte inställd på automatiskt)

5. Om aviseringen utlöses när det redan finns aviseringar som övervakar samma villkor (som inte är lösta) *kontrollerar du om* aviserings regeln har kon figurer ATS med egenskapen Autoformat till **false** (den här egenskapen kan bara konfigureras via REST/PowerShell/CLI, så kontrol lera skriptet som används för att distribuera varnings regeln). I detta fall löser varnings regeln inte autonotifieringar som skickats och kräver inte att en utlöst avisering stängs innan den utlöses igen.


## <a name="cant-find-metric-to-alert-on---virtual-machines"></a>Det går inte att hitta mått för avisering på virtuella datorer 

Se till att du har konfigurerat diagnostikinställningar för att skicka data till en Azure Monitor mottagare för att varna på gäst mått på virtuella datorer (för minne, disk utrymme).
    * [För virtuella Windows-datorer](https://docs.microsoft.com/azure/azure-monitor/platform/collect-custom-metrics-guestos-resource-manager-vm)
    * [För virtuella Linux-datorer](https://docs.microsoft.com/azure/azure-monitor/platform/collect-custom-metrics-linux-telegraf)
    
> [!NOTE] 
> Om du har konfigurerat gäst mått som ska skickas till en Log Analytics arbets yta visas måtten under arbets ytan Log Analytics arbets yta och kommer att börja visa **data när** du har skapat en varnings regel som övervakar dem. Det gör du genom att följa stegen för att [konfigurera en måttavisering för loggar](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-logs#configuring-metric-alert-for-logs).

## <a name="cant-find-the-metric-to-alert-on"></a>Det går inte att hitta det mått som ska aviseras

Om du vill Avisera om ett speciellt mått men inte kan se mått för resursen, [kontrollerar du om resurs typen stöds för mått varningar](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-near-real-time).
Om du kan se vissa mått för resursen men inte kan hitta ett visst mått, [kontrollerar du om måttet är tillgängligt](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported), och i så fall, se mått beskrivningen för att se om den endast är tillgänglig i vissa versioner eller utgåvor av resursen.

## <a name="cant-find-the-metric-dimension-to-alert-on"></a>Det går inte att hitta mått dimensionen som ska aviseras

Observera följande om du vill få en avisering om [ett måtts dimensions värden](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#using-dimensions), men inte kan hitta dessa värden:

1. Det kan ta några minuter innan dimensionsvärden visas under listan **Dimensionsvärden**
1. Dimensionsvärden som visas baseras på måttdata som samlats in under de senaste tre dagarna
1. Om dimension svärdet inte har spridits än, klickar du på tecknet "+" för att lägga till ett anpassat värde
1. Om du vill få aviseringar om alla möjliga värden för en dimension (inklusive framtida värden) markerar du kryss rutan Select * (Välj *)

## <a name="metric-alert-rules-still-defined-on-a-deleted-resource"></a>Mått varnings regler som fortfarande har definierats för en borttagen resurs 

Vid borttagning av en Azure-resurs tas inte associerade måttaviseringsregler bort automatiskt. Ta bort aviserings regler som är kopplade till en resurs som har tagits bort:

1. Öppna den resursgrupp där den borttagna resursen definierades
1. I den lista som visar resurserna markerar du kryssrutan **Visa dolda typer**
1. Filtrera listan efter Typ == **microsoft.insights/metricalerts**
1. Välj relevanta aviserings regler och välj **ta bort**

## <a name="make-metric-alerts-occur-every-time-my-condition-is-met"></a>Gör mått varningar varje gång mitt villkor är uppfyllt

Mått varningar är tillstånds känsliga som standard och därför utlöses inte ytterligare aviseringar om det redan finns en utlöst avisering på en specifik tids serie. Om du vill göra en speciell varnings regel för tillstånds begränsning, och få aviseringar om varje utvärdering som varnings villkoret är uppfyllt, skapar du aviserings regeln program mässigt (till exempel via [Resource Manager](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates), [PowerShell](https://docs.microsoft.com/powershell/module/az.monitor/?view=azps-3.6.1), [rest](https://docs.microsoft.com/rest/api/monitor/metricalerts/createorupdate), [CLI](https://docs.microsoft.com/cli/azure/monitor/metrics/alert?view=azure-cli-latest)) och ställer in egenskapen *automildra* till "false".

> [!NOTE] 
> Att göra en regel för att varna regler förhindrar att skickade aviseringar löses, så även när villkoret inte är uppfyllt, kommer de aktiverade aviseringarna att behållas i ett utlöst tillstånd fram till den 30 dagarnas kvarhållningsperiod.


## <a name="metric-alert-rules-quota-too-small"></a>Måttet för mått för varnings regler är för litet

Antalet mått för aviserings regler per prenumeration omfattas av [kvot gränser](https://docs.microsoft.com/azure/azure-monitor/service-limits).

Om du har nått kvotgränsen kan följande steg hjälpa dig att lösa problemet:
1. Försök att ta bort eller inaktivera mått regler som inte används längre.

2. Växla till att använda måttaviseringsregler som övervakar flera resurser. Med den här funktionen kan en enda varnings regel övervaka flera resurser med bara en varnings regel som räknas mot kvoten. Mer information om den här funktionen och vilka resurstyper som stöds finns [här](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#monitoring-at-scale-using-metric-alerts-in-azure-monitor).

3. Om du behöver öka kvot gränsen kan du öppna en supportbegäran och ange följande information:

    - Prenumerations-ID: n för vilka kvot gränsen måste ökas
    - Resurs typ för kvot ökningen: **mått varningar** eller **mått aviseringar (klassisk)**
    - Begärd kvotgräns

## <a name="check-total-number-of-metric-alert-rules"></a>Kontrol lera det totala antalet varnings regler för mått

Följ stegen nedan om du vill kontrol lera den aktuella användningen av mått varnings regler.

### <a name="from-the-azure-portal"></a>Från Azure-portalen

1. Öppna skärmen **Aviseringar** och klicka på **Hantera aviseringsregler**
2. Filtrera efter den relevanta prenumerationen med hjälp av list Rute kontrollen för **prenumerationen**
3. Se till att du inte filtrerar till en speciell resurs grupp, resurs typ eller resurs
4. Välj **mått** i list Rute kontrollen **signal typ**
5. Kontrol lera att List rutan **status** är inställd på **aktive rad**
6. Det totala antalet varnings regler för mått visas ovanför regel listan

### <a name="from-api"></a>Från API

- PowerShell – [Get-AzMetricAlertRuleV2](https://docs.microsoft.com/powershell/module/az.monitor/get-azmetricalertrulev2?view=azps-3.7.0)
- REST API – [Lista efter prenumeration](https://docs.microsoft.com/rest/api/monitor/metricalerts/listbysubscription)
- Azure CLI – [az monitor metrics alert list](https://docs.microsoft.com/cli/azure/monitor/metrics/alert?view=azure-cli-latest#az-monitor-metrics-alert-list)

## <a name="managing-alert-rules-using-resource-manager-templates-rest-api-powershell-or-azure-cli"></a>Hantera aviserings regler med Resource Manager-mallar, REST API, PowerShell eller Azure CLI

Om du får problem med att skapa, uppdatera, hämta eller ta bort mått aviseringar med hjälp av Resource Manager-mallar, REST API, PowerShell eller kommando rads gränssnittet för Azure (CLI), kan följande steg hjälpa dig att lösa problemet.

### <a name="resource-manager-templates"></a>Mallar för Resurshanteraren

- Titta på listan över [vanliga Azure-distributionsfel](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-common-deployment-errors) och felsök
- Se [mått varningar Azure Resource Manager mal exemplen](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates) för att se till att du skickar alla parametrar korrekt

### <a name="rest-api"></a>REST-API

Läs [REST API-guiden](https://docs.microsoft.com/rest/api/monitor/metricalerts/) och kontrollera att du skickar alla parametrar korrekt

### <a name="powershell"></a>PowerShell

Kontrol lera att du använder rätt PowerShell-cmdlets för mått varningar:

- PowerShell-cmdletar för måttaviseringar finns i modulen [AZ. Monitor](https://docs.microsoft.com/powershell/module/az.monitor/?view=azps-3.6.1)
- Se till att använda cmdletarna som slutar med "v2" för nya (icke-klassiska) mått varningar (till exempel [Add-AzMetricAlertRuleV2](https://docs.microsoft.com/powershell/module/az.monitor/Add-AzMetricAlertRuleV2?view=azps-3.6.1))

### <a name="azure-cli"></a>Azure CLI

Kontrol lera att du använder rätt CLI-kommandon för mått varningar:

- CLI-kommandon för måttaviseringar börjar med `az monitor metrics alert`. Granska [Azure CLI-referensen](https://docs.microsoft.com/cli/azure/monitor/metrics/alert?view=azure-cli-latest) om du vill veta mer om syntaxen.
- Du kan se [det exempel som visar hur du använder CLI för måttaviseringar](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric#with-azure-cli)
- Om du vill varna för ett anpassat mått måste du ange prefixet till måttets namn med relevant mått för namnområde: NAMESPACE.METRIC

### <a name="general"></a>Allmänt

- Om du får ett fel av typen `Metric not found`:

   - För ett plattforms mått: kontrol lera att du använder **mått** namnet på [sidan Azure Monitor mått som stöds](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported)och inte **måttets visnings namn**

   - För ett anpassat mått: kontrol lera att måttet redan har skickats (du kan inte skapa en varnings regel för ett anpassat mått som ännu inte finns) och att du tillhandahåller det anpassade måttets namnrymd (se en ARM-mall exempel [här](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates#template-for-a-static-threshold-metric-alert-that-monitors-a-custom-metric))

- Om du skapar [måttaviseringar för loggar](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-logs) bör du se till att lämpliga beroenden ingår. Se [exempelmallen](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-logs#resource-template-for-metric-alerts-for-logs).

- Observera följande begränsningar om du skapar en aviserings regel som innehåller flera villkor:

   - Du kan bara välja ett värde per dimension i varje kriterium
   - Du kan inte använda ”\*” som dimensionsvärde
   - När mått som kon figurer ATS i olika villkor stöder samma dimension måste ett konfigurerat dimensions värde uttryckligen anges på samma sätt för alla dessa mått (se ett exempel på en Resource Manager-mall [här](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates#template-for-a-static-threshold-metric-alert-that-monitors-multiple-criteria))


## <a name="no-permissions-to-create-metric-alert-rules"></a>Inga behörigheter för att skapa mått varnings regler

Om du vill skapa en regel för mått varningar måste du ha följande behörigheter:

- Läs behörighet för aviserings regelns mål resurs
- Skriv behörighet för resurs gruppen där varnings regeln skapas (om du skapar varnings regeln från Azure Portal skapas varnings regeln i samma resurs grupp där mål resursen finns)
- Läs behörighet för alla åtgärds grupper som är kopplade till aviserings regeln (om tillämpligt)


## <a name="naming-restrictions-for-metric-alert-rules"></a>Namngivnings begränsningar för mått varnings regler

Observera följande begränsningar för namn på mått för varnings regler:

- Det går inte att ändra regel namn för mått varningar (omdöpt) när den har skapats
- Mått för varnings regel namn måste vara unika inom en resurs grupp
- Mått varnings regel namn får inte innehålla följande tecken: * # & +:  < > ? @ % { } \ / 
- Regel namn för mått varningar får inte sluta med följande:.


## <a name="restrictions-when-using-dimensions-in-a-metric-alert-rule-with-multiple-conditions"></a>Begränsningar när du använder dimensioner i en mått varnings regel med flera villkor

Mått aviseringar stöder aviseringar om flerdimensionella mått samt stöd för att definiera flera villkor (upp till 5 villkor per varnings regel).

Observera följande begränsningar när du använder dimensioner i en varnings regel som innehåller flera villkor:
1. Du kan bara välja ett värde per dimension i varje villkor.
2. Du kan inte använda alternativet "Välj alla aktuella och framtida värden" (Välj \* ).
3. När mått som har kon figurer ATS på olika villkor stöder samma dimension måste ett konfigurerat dimensions värde uttryckligen anges på samma sätt för alla dessa mått (i de relevanta villkoren).
Ett exempel:
    - Överväg en regel för mått varningar som definieras på ett lagrings konto och övervakar två villkor:
        * Totalt antal **transaktioner** > 5
        * Genomsnittlig **SuccessE2ELatency** > 250 MS
    - Jag skulle vilja uppdatera det första villkoret och endast övervaka transaktioner där **ApiName** -dimensionen är lika med *"GetBlob"*
    - Eftersom både **transaktionerna** och **SuccessE2ELatency** -måtten har stöd för en **ApiName** -dimension måste jag uppdatera båda villkoren och låta båda ange dimensionen **ApiName** med värdet *"GetBlob"* .


## <a name="next-steps"></a>Nästa steg

- Allmän felsöknings information om aviseringar och meddelanden finns [i fel söknings problem i Azure Monitor aviseringar](alerts-troubleshoot.md).
