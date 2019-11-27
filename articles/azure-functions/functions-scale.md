---
title: Azure Functions skala och vara värd
description: Lär dig hur du väljer mellan Azure Functions förbruknings plan och Premium-plan.
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.topic: conceptual
ms.date: 03/27/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ebb2fcf0f626a82bcb5e6439183ba98c39c58588
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74322891"
---
# <a name="azure-functions-scale-and-hosting"></a>Azure Functions skala och vara värd

När du skapar en Function-app i Azure måste du välja en värd plan för din app. Det finns tre värd planer för Azure Functions: [förbruknings plan](#consumption-plan), [Premium plan](#premium-plan)och [App Service plan](#app-service-plan).

Värd planen du väljer styr följande beteenden:

* Hur din Function-app skalas.
* De resurser som är tillgängliga för varje funktions program instans.
* Stöd för avancerade funktioner, t. ex. VNET-anslutning.

Både förbruknings-och Premium planer lägger automatiskt till beräknings kraft när koden körs. Din app skalas ut när det behövs för att hantera belastningen och skalas ned när kod slutar köras. För förbruknings planen behöver du inte heller betala för inaktiva virtuella datorer eller reserv kapacitet i förväg.  

Premium-prenumerationen innehåller ytterligare funktioner, till exempel Premium Compute-instanser, möjligheten att hålla instanserna varmt under obestämd tid och VNet-anslutning.

Med App Service plan kan du dra nytta av dedikerad infrastruktur som du hanterar. Din Function-app skalar inte baserat på händelser, vilket innebär att aldrig skalas ned till noll. (Kräver att [Always on](#always-on) är aktiverat.)

## <a name="hosting-plan-support"></a>Support Plans support

Funktions stödet ingår i följande två kategorier:

* _Allmänt tillgänglig (ga)_ : fullständigt stöd för och godkänd användning av produktion.
* För _hands version_: stöds ännu inte fullt ut och godkänts för produktions användning.

Följande tabell visar den aktuella support nivån för de tre värd planerna, när de körs på antingen Windows eller Linux:

| | Förbrukningsplan | Premiumplan | Dedikerad plan |
|-|:----------------:|:------------:|:----------------:|
| Windows | Allmän tillgänglighet (GA) | Allmän tillgänglighet (GA) | Allmän tillgänglighet (GA) |
| Linux | Allmän tillgänglighet (GA) | Allmän tillgänglighet (GA) | Allmän tillgänglighet (GA) |

## <a name="consumption-plan"></a>Förbrukningsplan

När du använder förbruknings planen läggs instanser av Azure Functions-värden dynamiskt till och tas bort baserat på antalet inkommande händelser. Den här server lös planen skalas automatiskt och du debiteras för beräknings resurser endast när funktionerna körs. I en förbruknings plan är en funktions körning tids gräns efter en konfigurerbar tids period.

Faktureringen baseras på antalet körningar, körnings tid och använt minne. Faktureringen sammanställs för alla funktioner i en Function-app. Mer information finns på sidan med [Azure Functions priser](https://azure.microsoft.com/pricing/details/functions/).

Förbruknings planen är standard värd planen och ger följande fördelar:

* Betala endast när funktionerna körs
* Skala ut automatiskt, även under perioder med hög belastning

Function-appar i samma region kan tilldelas samma förbruknings plan. Det finns ingen nack delar eller påverkan på att flera appar körs i samma förbruknings plan. Tilldelning av flera appar till samma förbruknings plan påverkar inte återhämtning, skalbarhet eller tillförlitlighet för varje app.

Mer information om hur du beräknar kostnader när du kör i en förbruknings plan finns i [förstå förbruknings plan kostnader](functions-consumption-costs.md).

## <a name="premium-plan"></a>Premium-plan

När du använder Premium-planen läggs instanser av Azure Functions-värden till och tas bort baserat på antalet inkommande händelser precis som förbruknings planen.  Premium-planen stöder följande funktioner:

* Ständigt varma instanser för att undvika kall start
* VNet-anslutning
* Obegränsad körnings tid
* Premium-instans storlekar (en kärna, två kärnor och fyra kärn instanser)
* Mer förutsägbar prissättning
* App-allokering med hög densitet för planer med flera Function-appar

Information om hur du kan konfigurera de här alternativen finns i [Azure Functions Premium plan-dokumentet](functions-premium-plan.md).

I stället för fakturering per körning och använt minne baseras faktureringen för Premium-prenumerationen på det antal kärn sekunder och minne som används för alla nödvändiga och förvärmade instanser. Minst en instans måste vara varm vid alla tidpunkter per plan. Det innebär att det finns en lägsta månatlig kostnad per aktiv plan, oavsett antalet körningar. Tänk på att alla funktions program i en Premium plan delar förvärmade och aktiva instanser.

Överväg Azure Functions Premium-planen i följande situationer:

* Dina Function-appar körs kontinuerligt eller nästan kontinuerligt.
* Du har ett stort antal små körningar och har en hög körnings faktura men låg GB andra fakturor i förbruknings planen.
* Du behöver fler processor-eller minnes alternativ än vad som tillhandahålls av förbruknings planen.
* Din kod måste köras längre än den [maximala körnings tiden som tillåts](#timeout) i förbruknings planen.
* Du behöver funktioner som bara är tillgängliga i en Premium-plan, till exempel VNET/VPN-anslutning.

När du kör JavaScript-funktioner i en Premium-plan bör du välja en instans som har färre virtuella processorer. Mer information finns i [Premium-planerna för att välja en enda kärna](functions-reference-node.md#considerations-for-javascript-functions).  

## <a name="app-service-plan"></a>Dedikerad (App Service) plan

Dina Function-appar kan också köras på samma dedikerade virtuella datorer som andra App Service appar (Basic, standard, Premium och isolerade SKU: er).

Överväg ett App Service plan i följande situationer:

* Du har befintliga, underutnyttjade virtuella datorer som redan kör andra App Service instanser.
* Du vill ange en anpassad avbildning som dina funktioner ska köras på.

Du betalar samma för functions-appar i en App Service planera precis som för andra App Service resurser, t. ex. Web Apps. Mer information om hur App Service plan fungerar finns [i Översikt över Azure App Service planer](../app-service/overview-hosting-plans.md).

Med en App Service plan kan du manuellt skala ut genom att lägga till fler VM-instanser. Du kan också aktivera autoskalning. Mer information finns i [skala antalet instanser manuellt eller automatiskt](../azure-monitor/platform/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json). Du kan också skala upp genom att välja en annan App Service plan. Mer information finns i [skala upp en app i Azure](../app-service/manage-scale-up.md). 

När du kör JavaScript-funktioner på en App Service plan bör du välja en plan som har färre virtuella processorer. Mer information finns i [välj App Service planer med en kärna](functions-reference-node.md#choose-single-vcpu-app-service-plans). 
<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 

### <a name="always-on"></a>Always on

Om du kör på en App Service plan bör du aktivera inställningen **Always on** så att din funktions app fungerar som den ska. På en App Service plan aktive ras funktions körningen efter några minuter av inaktivitet, så endast HTTP-utlösare kommer att aktivera dina funktioner. Always On är bara tillgängligt på en App Service plan. I en förbruknings plan aktiverar plattformen automatiskt funktions appar.

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]


Även om Always On är aktiverat, kontrol leras tids gränsen för körningar för enskilda funktioner av `functionTimeout` inställningen i [Host. JSON](functions-host-json.md#functiontimeout) -projektfilen.

## <a name="determine-the-hosting-plan-of-an-existing-application"></a>Fastställa värd planen för ett befintligt program

För att avgöra vilken värd plan som används av din Function-app, se **App Service plan/pris nivå** på fliken **Översikt** för function-appen i [Azure Portal](https://portal.azure.com). För App Service planer anges även pris nivån.

![Visa skalnings plan i portalen](./media/functions-scale/function-app-overview-portal.png)

Du kan också använda Azure CLI för att fastställa planen enligt följande:

```azurecli-interactive
appServicePlanId=$(az functionapp show --name <my_function_app_name> --resource-group <my_resource_group> --query appServicePlanId --output tsv)
az appservice plan list --query "[?id=='$appServicePlanId'].sku.tier" --output tsv
```  

När utdata från det här kommandot är `dynamic`är din Function-app i förbruknings planen. När utdata från det här kommandot är `ElasticPremium`, är din Function-app i Premium-planen. Alla andra värden indikerar olika nivåer för en App Service plan.

## <a name="storage-account-requirements"></a>Krav för lagringskonto

I alla planer kräver en Function-app ett allmänt Azure Storage konto, som stöder Azure Blob, Queue, Files och table Storage. Detta beror på att funktioner förlitar sig på Azure Storage för åtgärder som att hantera utlösare och loggning av funktions körningar, men vissa lagrings konton stöder inte köer och tabeller. Dessa konton, som inkluderar BLOB-endast lagrings konton (inklusive Premium Storage) och allmänna lagrings konton med zon-redundant lagrings replikering, filtreras bort från dina befintliga **lagrings konto** val när du skapar en function-app.

Samma lagrings konto som används av din Function-app kan också användas av utlösare och bindningar för att lagra program data. För lagrings intensiva åtgärder bör du dock använda ett separat lagrings konto.   

<!-- JH: Does using a Premium Storage account improve perf? -->

Mer information om lagrings konto typer finns i [Introduktion till Azure Storage-tjänsterna](../storage/common/storage-introduction.md#azure-storage-services).

## <a name="how-the-consumption-and-premium-plans-work"></a>Så här fungerar förbruknings-och Premium planerna

I förbruknings-och Premium-planerna skalar Azure Functions-infrastrukturen processor-och minnes resurser genom att lägga till ytterligare instanser av Functions-värden, baserat på antalet händelser som dess funktioner aktive ras på. Varje instans av Functions-värden i förbruknings planen är begränsad till 1,5 GB minne och en processor.  En instans av värden är hela Function-appen, vilket innebär att alla funktioner i en Function-resurs resurs i en instans och skalas på samma gång. Function-appar som delar samma förbruknings plan skalas oberoende av varandra.  I Premium-planen avgör din Plans storlek det tillgängliga minnet och CPU: n för alla appar i planen på den instansen.  

Funktions kod filen lagras på Azure Files resurser på funktionens huvud lagrings konto. När du tar bort huvud lagrings kontot för Function-appen tas funktions kod filerna bort och kan inte återställas.

### <a name="runtime-scaling"></a>Körnings skalning

Azure Functions använder en komponent som kallas för *skalnings styrenheten* för att övervaka händelse frekvensen och bestämma om du vill skala ut eller skala in. Skalnings styrenheten använder heuristik för varje utlösnings typ. Om du till exempel använder en Azure Queue Storage-utlösare, skalas den baserat på köns längd och ålder för det äldsta Queue meddelandet.

Skalnings enheten för Azure Functions är Function-appen. När funktions programmet skalas ut allokeras ytterligare resurser för att köra flera instanser av den Azure Functions värden. I takt med att Compute demand minskas, tar skalnings kontrollen bort funktions värd instanser. Antalet instanser skalas slutligen ned till noll när inga funktioner körs i en Function-app.

![Skala övervakning av kontroll händelser och skapa instanser](./media/functions-scale/central-listener.png)

### <a name="understanding-scaling-behaviors"></a>Förstå skalnings beteenden

Skalning kan variera beroende på ett antal faktorer och skala på olika sätt beroende på vilken utlösare och vilket språk som valts. Det finns några erna för skalnings beteenden som kan vara medvetna om:

* En enskild funktionsapp skalar bara upp till högst 200 instanser. En enskild instans kan bearbeta mer än ett meddelande eller en begäran i taget, så det finns ingen angiven gräns för antalet samtidiga körningar.
* För HTTP-utlösare allokeras nya instanser bara högst en gång var 1: a sekund.
* För icke-HTTP-utlösare allokeras nya instanser bara högst en gång var 30: e sekund.

Olika utlösare kan också ha olika skalnings gränser samt dokumenterade nedan:

* [Händelsehubb](functions-bindings-event-hubs.md#trigger---scaling)

### <a name="best-practices-and-patterns-for-scalable-apps"></a>Metod tips och mönster för skalbara appar

Det finns många aspekter av en Function-app som påverkar hur väl den kommer att skalas, inklusive värd konfiguration, körnings miljö och resurs effektivitet.  Mer information finns i [avsnittet om skalbarhet i artikeln om prestanda överväganden](functions-best-practices.md#scalability-best-practices). Du bör också vara medveten om hur anslutningar fungerar när din Function-app skalar. Mer information finns i [hantera anslutningar i Azure Functions](manage-connections.md).

### <a name="billing-model"></a>Faktureringsmodell

Faktureringen för olika planer beskrivs i detalj på [sidan Azure Functions prissättning](https://azure.microsoft.com/pricing/details/functions/). Användningen sammanställs på Function-app-nivå och räknar bara den tid som funktions koden körs. Följande är enheter för fakturering:

* **Resursförbrukning i GB-sekunder (GB-s)** . Beräknad som en kombination av minnes storlek och körnings tid för alla funktioner i en Function-app. 
* **Körningar**. Räknas varje gången en funktion körs som svar på en händelse utlösare.

Användbara frågor och information om hur du förstår din förbruknings faktura finns [i vanliga frågor och svar om fakturering](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ).

[Azure Functions pricing page]: https://azure.microsoft.com/pricing/details/functions

## <a name="service-limits"></a>Tjänstbegränsningar

I följande tabell visas de begränsningar som gäller för Function-appar när de körs i de olika värd planerna:

[!INCLUDE [functions-limits](../../includes/functions-limits.md)]
