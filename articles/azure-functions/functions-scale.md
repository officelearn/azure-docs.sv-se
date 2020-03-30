---
title: Skala och var värd i Azure Functions
description: Lär dig hur du väljer mellan Azure Functions Consumption plan och Premium-abonnemang.
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.topic: conceptual
ms.date: 03/27/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0a54d7490fb306bfbc8e1b111e7b7d64c09d2292
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276613"
---
# <a name="azure-functions-scale-and-hosting"></a>Skala och var värd i Azure Functions

När du skapar en funktionsapp i Azure måste du välja en värdplan för din app. Det finns tre värdplaner för Azure Functions: [Förbrukningsplan,](#consumption-plan) [Premium-plan](#premium-plan)och [Dedikerad (App Service) plan](#app-service-plan).

Den värdplan du väljer styr följande beteenden:

* Hur din funktionsapp skalas.
* De resurser som är tillgängliga för varje funktionsappinstans.
* Stöd för avancerade funktioner, till exempel Azure Virtual Network-anslutning.

Både Förbruknings- och Premium-abonnemang lägger automatiskt till beräkningskraft när koden körs. Appen skalas ut när det behövs för att hantera belastningen och skalas in när koden slutar köras. För förbrukningsplanen behöver du inte heller betala för inaktiva virtuella datorer eller reservera kapacitet i förväg.  

Premium-planen innehåller ytterligare funktioner, till exempel premiumberäkningsinstanser, möjligheten att hålla instanserna varma på obestämd tid och VNet-anslutning.

Med App Service-planen kan du dra nytta av dedikerad infrastruktur som du hanterar. Din funktionsapp skalas inte baserat på händelser, vilket innebär att den aldrig skalas till noll. (Kräver att [Alltid på](#always-on) är aktiverat.)

## <a name="hosting-plan-support"></a>Support för hostingplan

Funktionsstöd hör till följande två kategorier:

* _Allmänt tillgänglig (GA):_ fullt stöd och godkänd för produktion.
* _Preview_: ännu inte fullt stöd eller godkänt för produktionsbruk.

Följande tabell anger den aktuella stödnivån för de tre värdplanerna när de körs på antingen Windows eller Linux:

| | Förbrukningsplan | Premiumplan | Särskild plan |
|-|:----------------:|:------------:|:----------------:|
| Windows | Allmän tillgänglighet (GA) | Allmän tillgänglighet (GA) | Allmän tillgänglighet (GA) |
| Linux | Allmän tillgänglighet (GA) | Allmän tillgänglighet (GA) | Allmän tillgänglighet (GA) |

## <a name="consumption-plan"></a>Förbrukningsplan

När du använder förbrukningsplanen läggs instanser av Azure Functions-värden dynamiskt till och tas bort baserat på antalet inkommande händelser. Den här serverlösa planen skalas automatiskt och du debiteras bara för beräkningsresurser när dina funktioner körs. I en förbrukningsplan får en funktions körningstid timeout efter en konfigurerbar tidsperiod.

Fakturering baseras på antalet körningar, körningstid och använt minne. Fakturering slås samman över alla funktioner inom en funktionsapp. Mer information finns på [prissidan för Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

Förbrukningsplanen är standardhostingplanen och erbjuder följande fördelar:

* Betala endast när dina funktioner körs
* Skala ut automatiskt, även under perioder med hög belastning

Funktionsappar i samma region kan tilldelas samma förbrukningsplan. Det finns ingen nackdel eller inverkan på att ha flera appar som körs i samma förbrukningsplan. Att tilldela flera appar till samma förbrukningsplan påverkar inte återhämtningsförmåga, skalbarhet eller tillförlitlighet för varje app.

Mer information om hur du beräknar kostnader när du kör i en förbrukningsplan finns i [Förstå kostnader för förbrukningsplan](functions-consumption-costs.md).

## <a name="premium-plan"></a><a name="premium-plan"></a>Premiumplan

När du använder Premium-planen läggs instanser av Azure Functions-värden till och tas bort baserat på antalet inkommande händelser precis som förbrukningsplanen.  Premium-abonnemanget stöder följande funktioner:

* Ständigt varma förekomster för att undvika kallstart
* Anslutning till VNet
* Obegränsad körningstid (60 minuter garanterad)
* Premium-instansstorlekar (en kärna, två kärn- och fyra kärninstanser)
* Mer förutsägbar prissättning
* Appallokering med hög densitet för planer med flera funktionsappar

Information om hur du kan konfigurera dessa alternativ finns i [Azure Functions Premium-plandokumentet](functions-premium-plan.md).

I stället för fakturering per körning och förbrukat minne baseras faktureringen för Premium-abonnemanget på antalet kärnsekunder och minne som används i nödvändiga och förvärmda instanser. Minst en instans måste vara varm hela tiden per plan. Det innebär att det finns en minsta månadskostnad per aktiv plan, oavsett antalet körningar. Tänk på att alla funktionsappar i en Premium-abonnemang delar förvärmda och aktiva instanser.

Tänk på Azure Functions Premium-abonnemanget i följande situationer:

* Dina funktionsappar körs kontinuerligt eller nästan kontinuerligt.
* Du har ett stort antal små körningar och har en hög körningsfaktura men låg GB-andra faktura i förbrukningsplanen.
* Du behöver fler CPU- eller minnesalternativ än vad som tillhandahålls av förbrukningsplanen.
* Koden måste köras längre än den [maximala körningstiden som tillåts](#timeout) i förbrukningsplanen.
* Du behöver funktioner som bara är tillgängliga i en Premium-plan, till exempel virtuell nätverksanslutning.

När du kör JavaScript-funktioner på en Premium-abonnemang bör du välja en instans som har färre virtuella processorer. Mer information finns i [Välj Premium-abonnemang med en kärna](functions-reference-node.md#considerations-for-javascript-functions).  

## <a name="dedicated-app-service-plan"></a><a name="app-service-plan"></a>Dedikerad plan (App Service)

Dina funktionsappar kan också köras på samma dedikerade virtuella datorer som andra App Service-appar (grundläggande, standard, premium och isolerade SKU:er).

Tänk på en apptjänstplan i följande situationer:

* Du har befintliga, underutnyttjade virtuella datorer som redan kör andra App Service-instanser.
* Du vill ange en anpassad avbildning som du kan köra dina funktioner på.

Du betalar samma för funktionsappar i en apptjänstplan som för andra App Service-resurser, till exempel webbappar. Mer information om hur App Service-planen fungerar finns [i översikten över Azure App Service-planer på djupet](../app-service/overview-hosting-plans.md).

Med en App Service-plan kan du skala ut manuellt genom att lägga till fler VM-instanser. Du kan också aktivera automatisk skalning. Mer information finns i [Skala instansantal manuellt eller automatiskt](../azure-monitor/platform/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json). Du kan också skala upp genom att välja en annan App Service-plan. Mer information finns [i Skala upp en app i Azure](../app-service/manage-scale-up.md). 

När du kör JavaScript-funktioner på en App Service-plan bör du välja ett abonnemang som har färre virtuella processorer. Mer information finns i [Välj apptjänstplaner med en kärna](functions-reference-node.md#choose-single-vcpu-app-service-plans). 
<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 

### <a name="always-on"></a><a name="always-on"></a>Alltid på

Om du kör på en App Service-plan bör du aktivera **alltid på** inställningen så att funktionsappen körs korrekt. På en App Service-plan går funktionernas körning inaktiv efter några minuters inaktivitet, så endast HTTP-utlösare kommer att "aktivera" dina funktioner. Alltid på är endast tillgänglig på en App Service-plan. På en förbrukningsplan aktiverar plattformen funktionsappar automatiskt.

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]


Även med Always On aktiverat styrs körningstiden för `functionTimeout` enskilda funktioner av inställningen i [värdjsonprojektfilen.](functions-host-json.md#functiontimeout)

## <a name="determine-the-hosting-plan-of-an-existing-application"></a>Bestäm värdplanen för ett befintligt program

Information om vilken värdplan som används av funktionsappen finns i **App Service-planen/prisnivån** på fliken **Översikt** för funktionsappen i [Azure-portalen](https://portal.azure.com). För App Service-planer anges även prisnivån.

![Visa skalningsplan i portalen](./media/functions-scale/function-app-overview-portal.png)

Du kan också använda Azure CLI för att bestämma planen enligt följande:

```azurecli-interactive
appServicePlanId=$(az functionapp show --name <my_function_app_name> --resource-group <my_resource_group> --query appServicePlanId --output tsv)
az appservice plan list --query "[?id=='$appServicePlanId'].sku.tier" --output tsv
```  

När utdata från `dynamic`det här kommandot är finns din funktionsapp i förbrukningsplanen. När utdata från `ElasticPremium`det här kommandot är finns din funktionsapp i Premium-planen. Alla andra värden anger olika nivåer i en App Service-plan.

## <a name="storage-account-requirements"></a>Krav för lagringskonto

På alla abonnemang kräver en funktionsapp ett allmänt Azure Storage-konto som stöder Azure Blob, Kö, Filer och Tabelllagring. Detta beror på att Funktioner är beroende av Azure Storage för åtgärder som att hantera utlösare och logga funktionskörningar, men vissa lagringskonton stöder inte köer och tabeller. Dessa konton, som innehåller lagringskonton med endast blob-fördr (inklusive premiumlagring) och lagringskonton för allmänt ändamål med zonupptredande lagringsreplikering, filtreras bort från dina befintliga **lagringskontoval** när du skapar en funktionsapp.

Samma lagringskonto som används av din funktionsapp kan också användas av dina utlösare och bindningar för att lagra dina programdata. För lagringsintensiva åtgärder bör du dock använda ett separat lagringskonto.  

Det är säkert möjligt för flera funktionsappar att dela samma lagringskonto utan problem. (Ett bra exempel på detta är när du utvecklar flera appar i din lokala miljö med hjälp av Azure Storage Emulator, som fungerar som ett lagringskonto.) 

<!-- JH: Does using a Premium Storage account improve perf? -->

Mer information om lagringskontotyper finns i [Introduktion till Azure Storage-tjänsterna](../storage/common/storage-introduction.md#azure-storage-services).

## <a name="how-the-consumption-and-premium-plans-work"></a>Så här fungerar konsumtions- och premiumplanerna

I förbruknings- och Premium-abonnemangen skalar Azure Functions-infrastrukturen CPU- och minnesresurser genom att lägga till ytterligare instanser av functions-värden, baserat på antalet händelser som dess funktioner utlöses på. Varje instans av functions-värden i förbrukningsplanen är begränsad till 1,5 GB minne och en processor.  En instans av värden är hela funktionsappen, vilket innebär att alla funktioner i en funktionsapp delar resurs inom en instans och skalar samtidigt. Funktionsappar som delar samma förbrukningsplan skalas oberoende av sig.  I Premium-planen avgör din planstorlek det tillgängliga minnet och processorn för alla appar i den planen på den instansen.  

Funktionskodfiler lagras på Azure Files-resurser på funktionens huvudlagringskonto. När du tar bort funktionsappens huvudlagringskonto tas funktionskodfilerna bort och kan inte återställas.

### <a name="runtime-scaling"></a>Skalning av körtid

Azure Functions använder en komponent som kallas *skalningsstyrenheten* för att övervaka händelsehastigheten och avgöra om du ska skala ut eller skala in. Skalstyrenheten använder heuristik för varje utlösartyp. När du till exempel använder en Azure Queue storage-utlösare skalas den baserat på kölängden och åldern på det äldsta kömeddelandet.

Skalenheten för Azure Functions är funktionsappen. När funktionsappen skalas ut allokeras ytterligare resurser för att köra flera instanser av Azure Functions-värden. Omvänt, när beräkningsbehovet minskas, tar skalningsstyrenheten bort funktionsvärdinstanser. Antalet instanser *skalas* så småningom till noll när inga funktioner körs i en funktionsapp.

![Skala kontrollanten övervaka händelser och skapa instanser](./media/functions-scale/central-listener.png)

### <a name="understanding-scaling-behaviors"></a>Förstå skalningsbeteenden

Skalning kan variera beroende på ett antal faktorer och skalas olika beroende på vilken utlösare och språk som valts. Det finns några krångligheter av skalning beteenden att vara medveten om:

* En enda funktionsapp skalas bara ut till högst 200 instanser. En enskild instans kan bearbeta mer än ett meddelande eller en begäran åt gången, så det finns inte en fastställd gräns för antalet samtidiga körningar.
* För HTTP-utlösare allokeras nya instanser högst en gång per sekund.
* För icke-HTTP-utlösare allokeras nya instanser högst en gång var 30:e sekund. Skalning är snabbare när du kör i en [Premium-plan](#premium-plan).
* För Service Bus-utlösare använder du _Hantera_ rättigheter på resurser för den mest effektiva skalningen. Med _lyssningsrättigheter_ är skalning inte lika exakt eftersom kölängden inte kan användas för att informera skalningsbeslut. Mer information om hur du anger rättigheter i åtkomstprinciper för Service Bus finns i [Behörighetsprincip för delad åtkomst](../service-bus-messaging/service-bus-sas.md#shared-access-authorization-policies).
* För Event Hub-utlösare finns i [skalningsvägledningen](functions-bindings-event-hubs-trigger.md#scaling) i referensartikeln. 

### <a name="best-practices-and-patterns-for-scalable-apps"></a>Metodtips och mönster för skalbara appar

Det finns många aspekter av en funktionsapp som påverkar hur väl den skalas, inklusive värdkonfiguration, körningsavtryck och resurseffektivitet.  Mer information finns i [avsnittet skalbarhet i artikeln prestandaöverväganden](functions-best-practices.md#scalability-best-practices). Du bör också vara medveten om hur anslutningar fungerar när din funktionsapp skalas. Mer information finns [i Så här hanterar du anslutningar i Azure Functions](manage-connections.md).

Mer information om skalning i Python och Node.js finns i [utvecklarhandboken för Azure Functions Python - Skalning och samtidighet](functions-reference-python.md#scaling-and-concurrency) och [Azure Functions Node.js-utvecklarguide - Skalning och samtidighet](functions-reference-node.md#scaling-and-concurrency).

### <a name="billing-model"></a>Faktureringsmodell

Fakturering för de olika abonnemangen beskrivs i detalj på [prissidan för Azure Functions](https://azure.microsoft.com/pricing/details/functions/). Användningen aggregeras på funktionsappnivå och räknar bara den tid som funktionskoden körs. Följande är enheter för fakturering:

* **Resursförbrukning i gigabyte-sekunder (GB-s)**. Beräknad som en kombination av minnesstorlek och körningstid för alla funktioner i en funktionsapp. 
* **Avrättningar**. Räknas varje gång en funktion körs som svar på en händelseutlösare.

Användbara frågor och information om hur du förstår din förbrukningsfaktura finns [på vanliga frågor och svar om fakturering](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ).

[Azure Functions pricing page]: https://azure.microsoft.com/pricing/details/functions

## <a name="service-limits"></a>Tjänstbegränsningar

I följande tabell visas de gränser som gäller för funktionsappar när de körs i de olika värdplanerna:

[!INCLUDE [functions-limits](../../includes/functions-limits.md)]
