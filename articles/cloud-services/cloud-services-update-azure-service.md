---
title: Så här uppdaterar du en tjänst i molnet | Microsoft Docs
description: Lär dig hur du uppdaterar molntjänster i Azure. Lär dig hur en uppdatering på en tjänst i molnet fortsätter att säkerställa tillgänglighet.
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: c6a8b5e6-5c99-454c-9911-5c7ae8d1af63
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: jeconnoc
ms.openlocfilehash: 2f5a82fac18ab34bfa9d6b46f553227ed44a994a
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39008101"
---
# <a name="how-to-update-a-cloud-service"></a>Så här uppdaterar du en tjänst i molnet

Uppdatering av en tjänst i molnet, inklusive dess roller och ett gäst-OS, är en process i tre steg. Binärfiler och konfigurationsfiler för ny molntjänst eller version av Operativsystemet måste först överföras. Azure reserverar sedan resurser för beräkning och nätverk för Molntjänsten baserat på kraven för den nya cloud service-versionen. Slutligen utför en löpande uppgradering för att stegvis uppdatera klienten till den nya versionen eller gäst-OS, samtidigt som tillgängligheten till din Azure. Den här artikeln beskriver information om det sista steget – uppgraderingen.

## <a name="update-an-azure-service"></a>Uppdatera en Azure-tjänst
Azure ordnar dina rollinstanser i logiska grupper som kallas uppgraderingsdomäner (UD). Uppgraderingsdomäner (UD) är en logisk uppsättning rollinstanser som har uppdaterats som en grupp.  Azure-uppdateringar a cloud service en UD samtidigt, vilket ger instanser i andra ud fortsätta trafik.

Standardvärdet för antal uppgraderingsdomäner är 5. Du kan ange ett annat antal uppgraderingsdomäner genom att inkludera attributet upgradeDomainCount i tjänstens-definitionsfil (.csdef). Läs mer om attributet upgradeDomainCount [WebRole-Schema](https://msdn.microsoft.com/library/azure/gg557553.aspx) eller [WorkerRole-Schema](https://msdn.microsoft.com/library/azure/gg557552.aspx).

När du utför en uppdatering på plats av en eller flera roller i din tjänst, uppdaterar Azure uppsättningar rollinstanser enligt vilken uppgraderingsdomän de tillhör. Azure-uppdateringar alla instanser i en viss uppgraderingsdomän – stoppar dem, uppdaterar dem, infogar tillbaka online – flyttar till nästa domän. Azure ser till att en uppdatering sker med minsta möjliga påverkan på tjänsten som körs genom att stoppa de instanser som körs i aktuell uppgraderingsdomän. Mer information finns i [hur uppdateringen fortsätter](#howanupgradeproceeds) senare i den här artikeln.

> [!NOTE]
> När villkoren **uppdatera** och **uppgradera** har något annan betydelse i kontexten Azure, de kan användas antingen för processer och beskrivningar av funktionerna i det här dokumentet.
>
>

Din tjänst måste ange minst två instanser av en roll för rollen som ska uppdateras på plats utan avbrott. Om tjänsten består av endast en instans av en roll, blir tjänsten otillgänglig tills uppdatering på plats har slutförts.

Det här avsnittet innehåller följande information om Azure-uppdateringar:

* [Tillåtna tjänsteändringar under en uppdatering](#AllowedChanges)
* [Hur en uppgraderingen fortsätter](#howanupgradeproceeds)
* [Återställning av en uppdatering](#RollbackofanUpdate)
* [Initierar flera mutating åtgärder på en pågående distribution](#multiplemutatingoperations)
* [Distributionen av roller i uppgraderingsdomäner](#distributiondfroles)

<a name="AllowedChanges"></a>

## <a name="allowed-service-changes-during-an-update"></a>Tillåtna tjänsteändringar under en uppdatering
I följande tabell visar de tillåtna ändringarna till en tjänst under en uppdatering:

| Ändringar tillåts som är värd för, tjänster och roller | Uppdatering på plats | Mellanlagrad (VIP-växling) | Ta bort och omdistribuera |
| --- | --- | --- | --- |
| Operativsystemversion |Ja |Ja |Ja |
| .NET-förtroendenivåer |Ja |Ja |Ja |
| Storlek på virtuell dator<sup>1</sup> |Ja<sup>2</sup> |Ja |Ja |
| Inställningar för lokal lagring |Öka endast<sup>2</sup> |Ja |Ja |
| Lägg till eller ta bort roller i en tjänst |Ja |Ja |Ja |
| Antal instanser av en viss roll |Ja |Ja |Ja |
| Typ av slutpunkter för en tjänst eller antal |Ja<sup>2</sup> |Nej |Ja |
| Namn och värden för konfigurationsinställningar |Ja |Ja |Ja |
| Värden (men inte namn) för konfigurationsinställningar |Ja |Ja |Ja |
| Lägga till nya certifikat |Ja |Ja |Ja |
| Ändra befintliga certifikat |Ja |Ja |Ja |
| Distribuera ny kod |Ja |Ja |Ja |

<sup>1</sup> storlek ändras begränsas till delmängden av storlekar som är tillgängliga för Molntjänsten.

<sup>2</sup> kräver Azure SDK 1.5 eller senare versioner.

> [!WARNING]
> Lokala data går förlorade om du ändrar storlek på virtuell dator.
>
>

Följande objekt stöds inte under en uppdatering:

* Ändra namnet på en roll. Ta bort och sedan lägger du till rollen med det nya namnet.
* Ändringar av antalet Uppgraderingsdomänen.
* Minska storleken på de lokala resurserna.

Om du gör andra uppdateringar i din tjänst-definition, som att minska storleken på lokal resurs måste du utföra en VIP-växling uppdatering i stället. Mer information finns i [växla distribution](https://msdn.microsoft.com/library/azure/ee460814.aspx).

<a name="howanupgradeproceeds"></a>

## <a name="how-an-upgrade-proceeds"></a>Hur en uppgraderingen fortsätter
Du kan bestämma om du vill uppdatera alla roller i din tjänst eller en enda roll i tjänsten. I båda fallen är alla instanser av varje roll som uppgraderas och tillhöra den första uppgraderingsdomänen stoppas, uppgraderas och online igen. När de är online igen, instanserna i den andra uppgraderingsdomänen stoppas, uppgraderas, och online igen. En molnbaserad tjänst kan ha högst en uppgradering aktiv i taget. Uppgraderingen utförs alltid mot den senaste versionen av Molntjänsten.

Följande diagram illustrerar hur uppgraderingen fortsätter om du uppgraderar alla roller i tjänsten:

![Uppgradera service](media/cloud-services-update-azure-service/IC345879.png "uppgradera tjänsten")

Den här nästa diagram illustrerar hur uppdateringen fortsätter om du uppgraderar bara en enda roll:

![Uppgradera rollen](media/cloud-services-update-azure-service/IC345880.png "uppgradera roll")  

I Azure-Infrastrukturkontrollanten under en automatisk uppdatering utvärderar med jämna mellanrum hälsotillståndet för Molntjänsten att avgöra när det är säkert att gå igenom nästa UD. Den här hälsotillstånd utvärderingen utförs på basis av per roll och tar hänsyn till endast instanser i den senaste versionen (d.v.s. instanser från ud som redan har gått). Den kontrollerar att ett minsta antal rollinstanser för varje roll har uppnått ett tillfredsställande avslutat tillstånd.

### <a name="role-instance-start-timeout"></a>Tidsgräns för rollen instans Start
Infrastrukturkontrollanten väntar 30 minuter för varje rollinstans att nå tillståndet startad. Om tidsgränsen konfigurationsändringsavisering, fortsätter Infrastrukturkontrollanten gå till nästa rollinstansen.

### <a name="impact-to-drive-data-during-cloud-service-upgrades"></a>Påverkan på data från enheter under molntjänst uppgraderas

När du uppgraderar en tjänst från en enda instans till flera instanser ska din tjänst framställas medan uppgraderingen utförs på grund av sättet uppgraderingar av Azure-tjänster. Servicenivåavtal garanterande tjänsttillgänglighet för tjänsten gäller bara för tjänster som har distribuerats med fler än en instans. I följande lista beskrivs hur data på varje enhet påverkas av uppgraderingsscenariot varje Azure-tjänsten:

|Scenario|C-enheten|D-hårddisken|E-enheten|
|--------|-------|-------|-------|
|Omstart av virtuell dator|Bevaras|Bevaras|Bevaras|
|Portalen omstart|Bevaras|Bevaras|Förstörs|
|Portalen reimage|Bevaras|Förstörs|Förstörs|
|Uppgradering på plats|Bevaras|Bevaras|Förstörs|
|Noden migrering|Förstörs|Förstörs|Förstörs|

Observera att, i listan ovan e-enheten representerar rollens rotenhet och får inte vara hårdkodade. Använd i stället de **% RoleRoot %** miljövariabeln som representerar enheten.

Distribuera en ny tjänst för flera instanser till den tillfälliga servern för att minimera i driftstopp när du uppgraderar en enda instans-tjänst, och utföra en VIP-växling.

<a name="RollbackofanUpdate"></a>

## <a name="rollback-of-an-update"></a>Återställning av en uppdatering
Azure tillhandahåller flexibilitet vid hanteringen av tjänster under en uppdatering genom att du kan starta ytterligare åtgärder för en tjänst, när den inledande uppdateringsbegäran har godkänts av Azure-Infrastrukturkontrollanten. En återställning kan endast utföras när en uppdatering (konfigurationsändring) eller uppgraderingen finns i den **pågår** tillstånd för distributionen. En uppdatering eller uppgradering anses vara pågår så länge det finns minst en instans av tjänsten som ännu inte uppdaterats till den nya versionen. Du kan testa om en återställning är tillåtet, kontrollera värdet för flaggan RollbackAllowed som returneras av [få distribution](https://msdn.microsoft.com/library/azure/ee460804.aspx) och [hämta egenskaper för molntjänst](https://msdn.microsoft.com/library/azure/ee460806.aspx) åtgärder, har angetts till true.

> [!NOTE]
> Det finns ingen anledning att anropa återställning i en **plats** eller -uppgradering eftersom VIP-växling uppgraderingar avser ersätta hela köra en instans av din tjänst med en annan.
>
>

Återställning av en pågående uppdatering ger följande effekter för distributionen:

* Alla rollinstanser som hade ännu inte har uppdaterats eller uppgraderas till den nya versionen är inte uppdateras eller uppgraderas eftersom dessa instanser körs redan för målets version av tjänsten.
* Alla rollinstanser som redan har uppdaterats eller uppgraderas till den nya versionen av service-paketet (\*.cspkg) fil eller tjänstkonfigurationen (\*.cscfg)-fil (eller båda filerna) återställs till den förberedande versionen av filerna.

Detta är funktionellt tillhandahålls av följande funktioner:

* Den [återställning uppdatera eller uppgradera](https://msdn.microsoft.com/library/azure/hh403977.aspx) åtgärd, som kan anropas på en konfigurationsuppdateringen (utlöses genom att anropa [ändra distributionskonfiguration](https://msdn.microsoft.com/library/azure/ee460809.aspx)) eller en uppgradering (utlöses genom att anropa [ Uppgraderingsdistribution](https://msdn.microsoft.com/library/azure/ee460793.aspx)) så länge det finns minst en instans i tjänsten som ännu inte uppdaterats till den nya versionen.
* Låst elementet och RollbackAllowed-element som returneras som en del av svarstexten av den [få distribution](https://msdn.microsoft.com/library/azure/ee460804.aspx) och [hämta egenskaper för molntjänst](https://msdn.microsoft.com/library/azure/ee460806.aspx) åtgärder:

  1. Elementet låst kan du identifiera när en mutating åtgärd kan anropas på en viss distribution.
  2. RollbackAllowed-elementet kan du identifiera när den [återställning uppdatering eller uppgradera](https://msdn.microsoft.com/library/azure/hh403977.aspx) åtgärden kan anropas på en viss distribution.

  För att kunna utföra en återställning, behöver du inte kontrollera både låst och RollbackAllowed-element. Det tillräckligt för att bekräfta att RollbackAllowed har angetts till true. Dessa element returneras bara om de här metoderna anropas med rubriken inställd på ”x-ms-version: 2011-10-01” eller en senare version. Läs mer om versionshantering huvuden [Management versionshantering](https://msdn.microsoft.com/library/azure/gg592580.aspx).

Det finns vissa situationer där en återställning av en uppdatering eller uppgradering stöds inte, här visas på följande sätt:

* Minskning av lokala resurser – om uppdateringen ökar lokala resurser för en roll på Azure-plattformen tillåter inte att återställa.
* Kvotbegränsningar - ha om uppdateringen har en skala ned åtgärden som du kanske inte längre tillräcklig beräkningskvot att slutföra åtgärden för återställning. Varje Azure-prenumeration har en kvot som är associerade med den som anger det maximala antalet kärnor som kan användas av alla värdbaserade tjänster som hör till den prenumerationen. Om en återställning av en viss uppdatering lägger din prenumeration via kvot och som aktiveras inte en återställning.
* Konkurrenstillstånd – om den första uppdateringen har slutförts, en återställning är inte möjlig.

Är ett exempel på när återställningen av en uppdatering kan vara användbar om du använder den [Uppgraderingsdistribution](https://msdn.microsoft.com/library/azure/ee460793.aspx) åtgärden i manuellt läge för att styra den hastighet med vilken en större uppgradering på plats till ditt Azure-värdtjänsten distribueras.

Under distributionen av uppgraderingen du anropar [Uppgraderingsdistribution](https://msdn.microsoft.com/library/azure/ee460793.aspx) i manuellt läge och börjar gå igenom uppgraderingsdomäner. Om någon gång när du övervakar uppgraderingen måste du observera vissa rollinstanser i de första uppgradera domäner som du undersöker har svarar, kan du anropa den [återställning uppdatering eller uppgradera](https://msdn.microsoft.com/library/azure/hh403977.aspx) åtgärd avseende distributionen, vilket leder till orörda instanser som inte hade har uppgraderats och rollback-instanser som har uppgraderats till den föregående service-paketet och konfigurationen.

<a name="multiplemutatingoperations"></a>

## <a name="initiating-multiple-mutating-operations-on-an-ongoing-deployment"></a>Initierar flera mutating åtgärder på en pågående distribution
I vissa fall kanske du vill initiera flera samtidiga mutating åtgärder på en pågående distribution. Exempelvis kan du utföra en tjänstuppdatering, och medan uppdateringen distribueras i din tjänst, du vill göra vissa ändringar, t.ex. använda en annan uppdatering för att kunna återställa uppdateringen igen, eller ta bort distributionen. Om en Serviceuppgradering av innehåller buggy koden som orsakar en uppgraderad rollinstans kraschar upprepade gånger är ett ärende där det är nödvändigt. I det här fallet är Azure-Infrastrukturkontrollanten inte fortsätta vid tillämpning av som uppgradera eftersom tillräckligt många instanser i den uppgraderade domänen är felfria. Det här tillståndet kallas en *fastnat distribution*. Du kan få loss distributionen genom att återställa uppdateringen eller använda en ny uppdatering över övre misslyckas en.

När den första begäran att uppdatera eller uppgradera tjänsten har tagits emot av Azure-Infrastrukturkontrollanten, kan du börja efterföljande mutera åtgärder. Det vill säga att du inte behöver vänta tills den första åtgärden har slutförts innan du kan starta en annan mutating åtgärd.

Initierar en andra uppdateringsåtgärd medan den första uppdateringen pågår utför liknar ångring. Om den andra uppdateringen är i läget för automatisk, uppgraderas den första uppgraderingsdomänen direkt, kan leda till instanser från flera uppgraderingsdomäner är offline vid samma tidpunkt i tid.

Mutating åtgärderna är följande: [ändra distributionskonfiguration](https://msdn.microsoft.com/library/azure/ee460809.aspx), [Uppgraderingsdistribution](https://msdn.microsoft.com/library/azure/ee460793.aspx), [Distributionsstatus](https://msdn.microsoft.com/library/azure/ee460808.aspx), [ta bort distribution ](https://msdn.microsoft.com/library/azure/ee460815.aspx), och [Återställ uppdatering eller uppgradering](https://msdn.microsoft.com/library/azure/hh403977.aspx).

Två åtgärder [få distribution](https://msdn.microsoft.com/library/azure/ee460804.aspx) och [hämta egenskaper för molntjänst](https://msdn.microsoft.com/library/azure/ee460806.aspx), returnera flaggan låst som kan undersökas för att avgöra om en mutating åtgärd kan anropas på en viss distribution.

För att kunna anropa den version av dessa metoder som returnerar flaggan låst, måste du ange huvudet i begäran till ”x-ms-version: 2011-10-01” eller en senare. Läs mer om versionshantering huvuden [Management versionshantering](https://msdn.microsoft.com/library/azure/gg592580.aspx).

<a name="distributiondfroles"></a>

## <a name="distribution-of-roles-across-upgrade-domains"></a>Distributionen av roller i uppgraderingsdomäner
Azure distribuerar instanser av en roll jämnt över ett visst antal uppgraderingsdomäner som kan konfigureras som en del av tjänstdefinitionsfilen (.csdef). Max antal uppgraderingsdomäner är 20 och standardvärdet är 5. Läs mer om hur du ändrar tjänstdefinitionsfilen [Azure Tjänstdefinitionsschemat (.csdef-filen)](cloud-services-model-and-package.md#csdef).

Exempel: om din roll har tio instanser kan som standard varje uppgraderingsdomän innehåller två instanser. Om din roll har 14 instanser kan sedan fyra av uppgraderingsdomänerna innehålla tre instanser och en femte domän innehåller två.

Uppgraderingsdomäner identifieras med ett Nollbaserat index: den första uppgraderingsdomänen har ID 0 och andra uppgraderingsdomän har ID 1 och så vidare.

Följande diagram illustrerar hur en tjänst än innehåller två roller ska distribueras när tjänsten definierar två uppgraderingsdomäner. Tjänsten körs åtta instanser av webbrollen och nio instanser av arbetsrollen.

![Distribution av Uppgraderingsdomäner](media/cloud-services-update-azure-service/IC345533.png "Distribution av Uppgraderingsdomäner")

> [!NOTE]
> Observera att Azure styr hur instanser allokeras över uppgraderingsdomäner. Det går inte att ange vilka instanser som allokerats till vilken domän.
>
>

## <a name="next-steps"></a>Nästa steg
[Så här hanterar du molntjänster](cloud-services-how-to-manage-portal.md)  
[Så här övervakar du Cloud Services](cloud-services-how-to-monitor.md)  
[Så här konfigurerar du Cloud Services](cloud-services-how-to-configure-portal.md)  
