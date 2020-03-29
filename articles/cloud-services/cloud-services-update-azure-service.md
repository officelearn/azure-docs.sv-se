---
title: Så här uppdaterar du en molntjänst | Microsoft-dokument
description: Lär dig hur du uppdaterar molntjänster i Azure. Lär dig hur en uppdatering av en molntjänst fortsätter för att säkerställa tillgänglighet.
services: cloud-services
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 04/19/2017
ms.author: tagore
ms.openlocfilehash: 731f4e8cc8a93f33d6887f44fc8d09585e92a75a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75360352"
---
# <a name="how-to-update-a-cloud-service"></a>Så här uppdaterar du en molntjänst

Att uppdatera en molntjänst, inklusive både dess roller och gäst-OS, är en process i tre steg. Först måste binärfilerna och konfigurationsfilerna för den nya molntjänsten eller OS-versionen överföras. Därefter reserverar Azure beräknings- och nätverksresurser för molntjänsten baserat på kraven i den nya molntjänstversionen. Slutligen utför Azure en rullande uppgradering för att stegvis uppdatera klienten till den nya versionen eller gästoperativsystemet, samtidigt som du bevarar din tillgänglighet. I den här artikeln beskrivs detaljerna i det sista steget – den rullande uppgraderingen.

## <a name="update-an-azure-service"></a>Uppdatera en Azure-tjänst
Azure organiserar dina rollinstanser i logiska grupperingar som kallas uppgraderingsdomäner (UD). Uppgraderingsdomäner (UD) är logiska uppsättningar med rollinstanser som uppdateras som en grupp.  Azure uppdaterar en molntjänst en UD i taget, vilket gör att instanser i andra UD:er kan fortsätta betjäna trafik.

Standardantalet uppgraderingsdomäner är 5. Du kan ange ett annat antal uppgraderingsdomäner genom att inkludera attributet upgradeDomainCount i tjänstens definitionsfil (.csdef). Mer information om attributet upgradeDomainCount finns i [Azure Cloud Services Definition Schema (.csdef File)](https://docs.microsoft.com/azure/cloud-services/schema-csdef-file).

När du utför en uppdatering på plats av en eller flera roller i tjänsten uppdaterar Azure uppsättningar med rollinstanser enligt den uppgraderingsdomän som de tillhör. Azure uppdaterar alla instanser i en viss uppgraderingsdomän – stoppar dem, uppdaterar dem, tar dem tillbaka online – och flyttar sedan till nästa domän. Genom att stoppa endast instanser som körs i den aktuella uppgraderingsdomänen ser Azure till att en uppdatering sker med minsta möjliga påverkan på den tjänst som körs. Mer information finns i [Hur uppdateringen fortsätter](#howanupgradeproceeds) senare i den här artikeln.

> [!NOTE]
> Medan termerna **uppdatera** och **uppgradera** har något annorlunda betydelse i sammanhanget Azure, kan de användas omväxlande för processer och beskrivningar av funktionerna i det här dokumentet.
>
>

Tjänsten måste definiera minst två instanser av en roll för att rollen ska kunna uppdateras på plats utan driftstopp. Om tjänsten bara består av en instans av en roll är tjänsten inte tillgänglig förrän uppdateringen på plats är klar.

Det här avsnittet innehåller följande information om Azure-uppdateringar:

* [Tillåtna tjänständringar under en uppdatering](#AllowedChanges)
* [Hur en uppgradering fortsätter](#howanupgradeproceeds)
* [Återställning av en uppdatering](#RollbackofanUpdate)
* [Initiera flera muterande åtgärder på en pågående distribution](#multiplemutatingoperations)
* [Fördelning av roller mellan uppgraderingsdomäner](#distributiondfroles)

<a name="AllowedChanges"></a>

## <a name="allowed-service-changes-during-an-update"></a>Tillåtna tjänständringar under en uppdatering
I följande tabell visas tillåtna ändringar av en tjänst under en uppdatering:

| Ändringar som tillåts till hosting, tjänster och roller | Uppdatering på plats | Iscensatt (VIP-byte) | Ta bort och distribuera om |
| --- | --- | --- | --- |
| Operativsystemversion |Ja |Ja |Ja |
| .NET-förtroendenivå |Ja |Ja |Ja |
| Storlek för virtuell dator<sup>1</sup> |Ja<sup>2</sup> |Ja |Ja |
| Inställningar för lokal lagring |Öka endast<sup>2</sup> |Ja |Ja |
| Lägga till eller ta bort roller i en tjänst |Ja |Ja |Ja |
| Antal instanser av en viss roll |Ja |Ja |Ja |
| Antal eller typ av slutpunkter för en tjänst |Ja<sup>2</sup> |Inga |Ja |
| Namn och värden för konfigurationsinställningar |Ja |Ja |Ja |
| Värden (men inte namn) för konfigurationsinställningar |Ja |Ja |Ja |
| Lägga till nya certifikat |Ja |Ja |Ja |
| Ändra befintliga certifikat |Ja |Ja |Ja |
| Distribuera ny kod |Ja |Ja |Ja |

<sup>1</sup> Storleksändring begränsad till den delmängd av storlekar som är tillgängliga för molntjänsten.

<sup>2</sup> Kräver Azure SDK 1.5 eller senare versioner.

> [!WARNING]
> Om du ändrar storleken på den virtuella datorn förstörs lokala data.
>
>

Följande objekt stöds inte under en uppdatering:

* Ändra namnet på en roll. Ta bort och lägg sedan till rollen med det nya namnet.
* Ändring av antalet uppgraderingsdomäner.
* Minska storleken på de lokala resurserna.

Om du gör andra uppdateringar av tjänstens definition, till exempel minska storleken på den lokala resursen, måste du utföra en VIP-uppdatering i stället. Mer information finns i [Byta distribution](/previous-versions/azure/reference/ee460814(v=azure.100)).

<a name="howanupgradeproceeds"></a>

## <a name="how-an-upgrade-proceeds"></a>Hur en uppgradering fortsätter
Du kan bestämma om du vill uppdatera alla roller i tjänsten eller en enskild roll i tjänsten. I båda fallen stoppas, uppgraderas och förs alla instanser av varje roll som uppgraderas och tillhör den första uppgraderingsdomänen. När de är online igen stoppas, uppgraderas och online förs instanserna i den andra uppgraderingsdomänen. En molntjänst kan ha högst en uppgradering aktiv i taget. Uppgraderingen utförs alltid mot den senaste versionen av molntjänsten.

Följande diagram visar hur uppgraderingen fortsätter om du uppgraderar alla roller i tjänsten:

![Uppgradera tjänsten](media/cloud-services-update-azure-service/IC345879.png "Uppgradera tjänsten")

I nästa diagram visas hur uppdateringen fortsätter om du bara uppgraderar en enskild roll:

![Uppgradera roll](media/cloud-services-update-azure-service/IC345880.png "Uppgradera roll")  

Under en automatisk uppdatering utvärderar Azure Fabric Controller regelbundet molntjänstens hälsa för att avgöra när det är säkert att gå till nästa UD.During an automatic update, the Azure Fabric Controller periodicallys the health of the cloud service to determine when it's safe to walk the next UD. Denna hälsoutvärdering utförs per roll och tar endast hänsyn till instanser i den senaste versionen (dvs. instanser från UD:er som redan har gått). Den kontrollerar att ett minsta antal rollinstanser för varje roll har uppnått ett tillfredsställande terminaltillstånd.

### <a name="role-instance-start-timeout"></a>Timeout för rollinstansstart
Fabric Controller väntar 30 minuter för varje rollinstans att nå ett startat tillstånd. Om tidsgränsen för tidsgränsen har gått fortsätter Fabric Controller att gå vidare till nästa rollförekomst.

### <a name="impact-to-drive-data-during-cloud-service-upgrades"></a>Effekt för att driva data under uppgraderingar av Molntjänsten

När du uppgraderar en tjänst från en enda instans till flera instanser kommer din tjänst att tas bort medan uppgraderingen utförs på grund av hur Azure uppgraderar tjänster. Servicenivåavtalet som garanterar tjänstens tillgänglighet gäller endast för tjänster som distribueras med mer än en instans. I följande lista beskrivs hur data på varje enhet påverkas av varje Azure-tjänstuppgraderingsscenario:

|Scenario|C-enhet|D-enhet|E-enhet|
|--------|-------|-------|-------|
|Omstart av virtuella datorer|Bevarade|Bevarade|Bevarade|
|Omstart av portalen|Bevarade|Bevarade|Förstörde|
|Portalens ombild|Bevarade|Förstörde|Förstörde|
|Uppgradering på plats|Bevarade|Bevarade|Förstörde|
|Nodmigrering|Förstörde|Förstörde|Förstörde|

Observera att i listan ovan representerar E:-enheten rollens rotenhet och bör inte vara hårdkodad. Använd i stället miljövariabeln **%RoleRoot%** för att representera enheten.

Om du vill minimera driftstoppet när du uppgraderar en tjänst med en instans distribuerar du en ny tjänst med flera instanser till mellanlagringsservern och utför en VIP-swap.

<a name="RollbackofanUpdate"></a>

## <a name="rollback-of-an-update"></a>Återställning av en uppdatering
Azure ger flexibilitet i hanteringen av tjänster under en uppdatering genom att låta dig initiera ytterligare åtgärder på en tjänst, efter att den första uppdateringsbegäran har accepterats av Azure Fabric Controller. En återställning kan endast utföras när en uppdatering (konfigurationsändring) eller uppgradering är i **pågående** tillstånd för distributionen. En uppdatering eller uppgradering anses vara pågående så länge det finns minst en instans av tjänsten som ännu inte har uppdaterats till den nya versionen. Om du vill testa om en återställning tillåts kontrollerar du värdet för flaggan RollbackAllowed, som returneras av åtgärder [hämta distribution](/previous-versions/azure/reference/ee460804(v=azure.100)) och [hämta molntjänstegenskaper,](/previous-versions/azure/reference/ee460806(v=azure.100)) är inställt på true.

> [!NOTE]
> Det är bara meningsfullt att ringa rollback på en **på plats** uppdatering eller uppgradering eftersom VIP swap uppgraderingar innebär att ersätta en hel kör instans av din tjänst med en annan.
>
>

Återställning av en pågående uppdatering har följande effekter på distributionen:

* Alla rollinstanser som ännu inte har uppdaterats eller uppgraderats till den nya versionen uppdateras eller uppgraderas inte, eftersom dessa instanser redan kör målversionen av tjänsten.
* Alla rollinstanser som redan har uppdaterats eller uppgraderats till den nya versionen av tjänstpaketets (.cspkg)\*fil eller tjänstkonfigurationen (CSCFG)\*(eller båda filerna) återställs till förhandsuppgraderingsversionen av dessa filer.

Detta tillhandahålls funktionellt av följande funktioner:

* [Åtgärden Återställningsuppdatering eller uppgradering,](/previous-versions/azure/reference/hh403977(v=azure.100)) som kan anropas på en konfigurationsuppdatering (utlöses genom att anropa [ändringsdistributionskonfiguration)](/previous-versions/azure/reference/ee460809(v=azure.100))eller en uppgradering (utlöses genom att anropa [uppgraderingsdistribution)](/previous-versions/azure/reference/ee460793(v=azure.100))så länge det finns minst en instans i tjänsten som ännu inte har uppdaterats till den nya versionen.
* Det låsta elementet och elementet RollbackAllowed, som returneras som en del av svarstexten för åtgärderna [Hämta distribution](/previous-versions/azure/reference/ee460804(v=azure.100)) och [Hämta molntjänstegenskaper:](/previous-versions/azure/reference/ee460806(v=azure.100))

  1. Med det låsta elementet kan du identifiera när en muterande åtgärd kan anropas på en viss distribution.
  2. Med elementet RollbackAllowed kan du identifiera när [återställningsuppdateringen eller uppgraderingsåtgärden](/previous-versions/azure/reference/hh403977(v=azure.100)) kan anropas för en viss distribution.

  För att kunna utföra en återställning behöver du inte kontrollera både de låsta och de RollbackAllowed-elementen. Det räcker att bekräfta att RollbackAllowed är inställd på sant. Dessa element returneras endast om dessa metoder anropas med hjälp av begäran huvudet inställt på "x-ms-version: 2011-10-01" eller en senare version. Mer information om versionshuvuden finns i Versionshantering för [tjänsthantering](/previous-versions/azure/gg592580(v=azure.100)).

Det finns vissa situationer där en återställning av en uppdatering eller uppgradering inte stöds, dessa är följande:

* Minskning av lokala resurser - Om uppdateringen ökar de lokala resurserna för en roll tillåter azure-plattformen inte att återställa.
* Kvotbegränsningar - Om uppdateringen var en nedskalningsåtgärd kanske du inte längre har tillräcklig beräkningskvot för att slutföra återställningsåtgärden. Varje Azure-prenumeration har en kvot som är associerad med den som anger det maximala antalet kärnor som kan förbrukas av alla värdbaserade tjänster som tillhör den prenumerationen. Om du utför en återställning av en viss uppdatering skulle placera din prenumeration över kvoten då att en återställning inte kommer att aktiveras.
* Race villkor - Om den första uppdateringen har slutförts, är en återställning inte möjligt.

Ett exempel på när återställningen av en uppdatering kan vara användbar är om du använder [uppgraderingsdistributionen](/previous-versions/azure/reference/ee460793(v=azure.100)) i manuellt läge för att styra hur hög hastighet en större uppgradering på plats till din Azure-värdtjänst distribueras.

Under distributionen av uppgraderingen anropar du [uppgraderingsdistribution](/previous-versions/azure/reference/ee460793(v=azure.100)) i manuellt läge och börjar gå uppgradera domäner. Om du någon gång, när du övervakar uppgraderingen, noterar vissa rollinstanser i de första uppgraderingsdomänerna som du undersöker har inte svarat, kan du anropa åtgärden [Återställningsuppdatering eller uppgradering](/previous-versions/azure/reference/hh403977(v=azure.100)) på distributionen, vilket lämnar de instanser som ännu inte har uppgraderats och återställningsinstanserna som hade uppgraderats till det tidigare tjänstpaketet och konfigurationen.

<a name="multiplemutatingoperations"></a>

## <a name="initiating-multiple-mutating-operations-on-an-ongoing-deployment"></a>Initiera flera muterande åtgärder på en pågående distribution
I vissa fall kanske du vill initiera flera samtidiga muteringsåtgärder på en pågående distribution. Du kan till exempel utföra en tjänstuppdatering och även om uppdateringen distribueras över tjänsten vill du göra vissa ändringar, till exempel för att återställa uppdateringen, installera en annan uppdatering eller till och med ta bort distributionen. Ett fall där detta kan vara nödvändigt är om en tjänst uppgradering innehåller buggy kod som orsakar en uppgraderad roll instans att upprepade gånger krascha. I det här fallet kan Azure Fabric Controller inte göra framsteg med att tillämpa uppgraderingen eftersom ett otillräckligt antal instanser i den uppgraderade domänen är felfria. Det här tillståndet kallas en *fast distribution*. Du kan ta bort distributionen genom att återställa uppdateringen eller tillämpa en ny uppdatering överst på den felaktiga.

När den första begäran om att uppdatera eller uppgradera tjänsten har tagits emot av Azure Fabric Controller kan du starta efterföljande muterande åtgärder. Det vill säga du behöver inte vänta på att den första åtgärden ska slutföras innan du kan starta en annan muterande åtgärd.

Initiera en andra uppdateringsåtgärd medan den första uppdateringen pågår kommer att utföra liknande återställningsåtgärden. Om den andra uppdateringen är i automatiskt läge uppgraderas den första uppgraderingsdomänen omedelbart, vilket kan leda till att instanser från flera uppgraderingsdomäner är offline vid samma tidpunkt.

De muterande åtgärderna är följande: [Ändra distributionskonfiguration,](/previous-versions/azure/reference/ee460809(v=azure.100)) [uppgraderingsdistribution,](/previous-versions/azure/reference/ee460793(v=azure.100)) [uppdatering av distributionsstatus,](/previous-versions/azure/reference/ee460808(v=azure.100)) [ta bort distribution](/previous-versions/azure/reference/ee460815(v=azure.100))och [återställningsuppdatering eller återställning](/previous-versions/azure/reference/hh403977(v=azure.100)).

Två åtgärder, [Hämta distribution](/previous-versions/azure/reference/ee460804(v=azure.100)) och [Hämta molntjänstegenskaper](/previous-versions/azure/reference/ee460806(v=azure.100)), returnerar den låsta flaggan som kan undersökas för att avgöra om en muterande åtgärd kan anropas på en viss distribution.

För att kunna anropa den version av dessa metoder som returnerar den låsta flaggan måste du ange begäranden till "x-ms-version: 2011-10-01" eller en senare. Mer information om versionshuvuden finns i Versionshantering för [tjänsthantering](/previous-versions/azure/gg592580(v=azure.100)).

<a name="distributiondfroles"></a>

## <a name="distribution-of-roles-across-upgrade-domains"></a>Fördelning av roller mellan uppgraderingsdomäner
Azure distribuerar instanser av en roll jämnt över ett anstämt antal uppgraderingsdomäner, som kan konfigureras som en del av tjänstdefinitionsfilen (.csdef). Det maximala antalet uppgraderingsdomäner är 20 och standardvärdet är 5. Mer information om hur du ändrar tjänstdefinitionsfilen finns i [Azure Service Definition Schema (.csdef File)](cloud-services-model-and-package.md#csdef).

Om din roll till exempel har tio instanser innehåller varje uppgraderingsdomän som standard två instanser. Om din roll har 14 instanser innehåller fyra av uppgraderingsdomänerna tre instanser och en femte domän innehåller två.

Uppgraderingsdomäner identifieras med ett nollbaserat index: den första uppgraderingsdomänen har ett ID på 0 och den andra uppgraderingsdomänen har ett ID på 1 och så vidare.

Följande diagram visar hur en tjänst än innehåller två roller distribueras när tjänsten definierar två uppgraderingsdomäner. Tjänsten kör åtta instanser av webbrollen och nio instanser av arbetarrollen.

![Distribution av uppgraderingsdomäner](media/cloud-services-update-azure-service/IC345533.png "Distribution av uppgraderingsdomäner")

> [!NOTE]
> Observera att Azure styr hur instanser allokeras mellan uppgraderingsdomäner. Det går inte att ange vilka instanser som ska fördelas till vilken domän.
>
>

## <a name="next-steps"></a>Nästa steg
[Hantera molntjänster](cloud-services-how-to-manage-portal.md)  
[Så här övervakar du molntjänster](cloud-services-how-to-monitor.md)  
[Konfigurera molntjänster](cloud-services-how-to-configure-portal.md)  



