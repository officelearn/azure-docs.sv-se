---
title: Så här uppdaterar du en tjänst i molnet | Microsoft Docs
description: Lär dig hur du uppdaterar molntjänster i Azure. Lär dig hur en uppdatering på en tjänst i molnet fortsätter att säkerställa tillgänglighet.
services: cloud-services
documentationcenter: ''
author: Thraka
manager: timlt
editor: ''
ms.assetid: c6a8b5e6-5c99-454c-9911-5c7ae8d1af63
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: adegeo
ms.openlocfilehash: 36d4ee9dabd39f4103d17455e47521b378af6ebb
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/16/2017
ms.locfileid: "24860679"
---
# <a name="how-to-update-a-cloud-service"></a>Så här uppdaterar du en tjänst i molnet

Uppdatera en tjänst i molnet, inklusive dess roller och ett operativsystem, är tre steg. Först måste binärfilerna och konfigurationsfiler för ny molntjänst eller OS-version laddas upp. Därefter reserverar Azure beräknings- och nätverksresurser för Molntjänsten baserat på kraven för den nya cloud service-versionen. Slutligen utför Azure uppgraderingen för att uppdatera inkrementellt klienten till den nya versionen eller operativsystem, samtidigt som din tillgänglighet. Den här artikeln beskriver information om det sista steget – uppgraderingen.

## <a name="update-an-azure-service"></a>Uppdatera en Azure-tjänst
Azure ordnar dina rollinstanser i logiska grupper kallas uppgraderingsdomäner (UD). Uppgraderingsdomäner (UD) är en logisk uppsättning rollinstanser som uppdateras som en grupp.  Azure-uppdateringar en cloud service en UD samtidigt, vilket ger instanser i andra UDs fortsätta trafik.

Standardantalet uppgraderingsdomäner är 5. Du kan ange ett annat antal uppgraderingsdomäner genom att inkludera attributet upgradeDomainCount i definitionsfilen för tjänstens (.csdef). Läs mer om attributet upgradeDomainCount [WebRole schemat](https://msdn.microsoft.com/library/azure/gg557553.aspx) eller [WorkerRole schemat](https://msdn.microsoft.com/library/azure/gg557552.aspx).

När du utför en uppdatering på plats av en eller flera roller i din service uppdaterar Azure uppsättningar av rollinstanser enligt uppgraderingsdomän de tillhör. Azure-uppdateringar för instanser i en viss uppgradera domän – stoppar dem, uppdatera dem, gör dem tillbaka online – flyttar till nästa domän. Genom att stoppa endast de objektinstanser som körs i den aktuella uppgraderingsdomänen säkerställer Azure att en uppdatering görs med minsta möjliga påverkan på tjänsten som körs. Mer information finns i [hur uppdateringen fortsätter](#howanupgradeproceeds) senare i den här artikeln.

> [!NOTE]
> När villkoren **uppdatera** och **uppgradera** ha något annan betydelse i kontexten Azure, de kan användas synonymt för processer och beskrivningar av funktionerna i det här dokumentet.
>
>

Din tjänst måste definiera minst två instanser av en roll för den rollen som ska uppdateras på plats utan driftavbrott. Om tjänsten består av endast en instans av en roll, blir tjänsten otillgänglig tills uppdateringen på plats har slutförts.

Det här avsnittet innehåller följande information om Azure-uppdateringar:

* [Tillåtna-tjänsten ändras under en uppdatering](#AllowedChanges)
* [Hur en uppgradering pågår](#howanupgradeproceeds)
* [Återställning av en uppdatering](#RollbackofanUpdate)
* [Initierar flera mutating åtgärder på ett pågående distribution](#multiplemutatingoperations)
* [Distributionen av roller uppgradera domäner](#distributiondfroles)

<a name="AllowedChanges"></a>

## <a name="allowed-service-changes-during-an-update"></a>Tillåtna-tjänsten ändras under en uppdatering
I följande tabell visas tillåtna ändringar i en tjänst under en uppdatering:

| Ändringar tillåts till värd, tjänster och roller | Uppdatering på plats | Stegvis (VIP swap) | Ta bort och omdistribuera |
| --- | --- | --- | --- |
| Operativsystemversion |Ja |Ja |Ja |
| .NET-förtroendenivåer |Ja |Ja |Ja |
| Storlek på virtuell dator<sup>1</sup> |Ja<sup>2</sup> |Ja |Ja |
| Inställningar för lokal lagring |Öka endast<sup>2</sup> |Ja |Ja |
| Lägg till eller ta bort roller i en tjänst |Ja |Ja |Ja |
| Antal instanser av en viss roll |Ja |Ja |Ja |
| Antalet eller typen av slutpunkter för en tjänst |Ja<sup>2</sup> |Nej |Ja |
| Namn och värden för konfigurationsinställningar |Ja |Ja |Ja |
| Värden (men inte namn) för konfigurationsinställningar |Ja |Ja |Ja |
| Lägga till nya certifikat |Ja |Ja |Ja |
| Ändra befintliga certifikat |Ja |Ja |Ja |
| Distribuera nya kod |Ja |Ja |Ja |

<sup>1</sup> storlek ändras begränsas till delmängden av storlekar som finns tillgängliga för Molntjänsten.

<sup>2</sup> kräver Azure SDK 1.5 eller senare versioner.

> [!WARNING]
> Lokala data går förlorade om du ändrar storlek på virtuell dator.
>
>

Följande objekt stöds inte under en uppdatering:

* Ändra namnet på en roll. Ta bort och Lägg sedan till rollen med det nya namnet.
* Ändra antalet uppgraderingen domän.
* Minska storleken på de lokala resurserna.

Om du gör andra uppdateringar i definition av din tjänst, till exempel minska storleken på lokal resurs, måste du utföra en VIP-växling uppdatering i stället. Mer information finns i [växla distribution](https://msdn.microsoft.com/library/azure/ee460814.aspx).

<a name="howanupgradeproceeds"></a>

## <a name="how-an-upgrade-proceeds"></a>Hur en uppgradering pågår
Du kan välja om du vill uppdatera alla roller i din tjänst eller en roll i tjänsten. I båda fallen är alla instanser av varje roll som uppgraderas och som hör till den första uppgraderingsdomänen Stoppad, uppgraderas och online igen. När de är tillbaka online är instanserna i den andra uppgraderingsdomänen stoppad uppgraderas och online igen. En tjänst i molnet kan ha högst en uppgradering aktiva samtidigt. Uppgraderingen utförs alltid mot den senaste versionen av Molntjänsten.

Följande diagram illustrerar hur uppgraderingen fortsätter om du uppgraderar alla roller i tjänsten:

![Uppgradera tjänsten](media/cloud-services-update-azure-service/IC345879.png "uppgradera tjänsten")

Den här nästa diagram illustrerar hur uppdateringen fortsätter om du uppgraderar bara en enda roll:

![Uppgradera rollen](media/cloud-services-update-azure-service/IC345880.png "uppgradera roll")  

Under en automatisk uppdatering utvärderar med jämna mellanrum hälsotillståndet för Molntjänsten att bestämma när det är säkert att stega igenom nästa UD i Azure-Infrastrukturkontrollanten. Hälsotillstånd utvärderingen utförs på grundval av per roll och anser endast instanser i den senaste versionen (d.v.s. instanser från UDs har redan gått). Den verifierar att ett minsta antal rollinstanser för varje roll har uppnått ett tillfredsställande avslutat tillstånd.

### <a name="role-instance-start-timeout"></a>Rollen instans Start Timeout
I Infrastrukturkontrollanten väntar 30 minuter för varje rollinstans skulle nå ett startat tillstånd. Om det ska gå att timeout-varaktighet, fortsätter i Infrastrukturkontrollanten går till nästa rollinstans.

### <a name="impact-to-drive-data-during-cloud-service-upgrades"></a>Påverkan på data från enheter under Molntjänsten uppgraderas

När du uppgraderar en tjänst från en enda instans till flera instanser kommer din tjänst medan uppgraderingen utförs på grund av sättet uppgraderingar av Azure-tjänster. Servicenivåavtal garanterande tjänsttillgänglighet för tjänsten gäller bara för tjänster som har distribuerats med mer än en instans. I följande lista beskrivs hur data på varje enhet påverkas av uppgraderingsscenario varje Azure-tjänsten:

|Scenario|C-enheten|Enhet D|E-enheten|
|--------|-------|-------|-------|
|VM-omstart|Bevaras|Bevaras|Bevaras|
|Portalen omstart|Bevaras|Bevaras|Förstörs|
|Portalen avbildningsåterställning|Bevaras|Förstörs|Förstörs|
|Uppgradering på plats|Bevaras|Bevaras|Förstörs|
|Noden migrering|Förstörs|Förstörs|Förstörs|

Observera att, i listan ovan E:-enheten representerar rollens rotenhet och får inte vara hårdkodade. Använd i stället de **RoleRoot %** miljövariabeln för att representera enheten.

Distribuera en ny tjänst för flera instanser till testservern för att minimera avbrottstiden när du uppgraderar en single instance-tjänst, och utföra en VIP-växling.

<a name="RollbackofanUpdate"></a>

## <a name="rollback-of-an-update"></a>Återställning av en uppdatering
Azure tillhandahåller flexibilitet för att hantera tjänster under en uppdatering genom att du kan initiera ytterligare åtgärder för en tjänst när inledande uppdateringsbegäran har godkänts av Azure-Infrastrukturkontrollanten. En återställning kan endast utföras när en uppdatering (konfigurationsändring) eller uppgraderingen finns i den **pågår** tillstånd för distributionen. En uppdatering eller uppgradering av anses vara pågående så länge det finns minst en instans av tjänsten som ännu inte har uppdaterats till den nya versionen. Om du vill testa om en återställning är tillåtet att kontrollera värdet för flaggan RollbackAllowed som returneras av [hämta distribution](https://msdn.microsoft.com/library/azure/ee460804.aspx) och [hämta egenskaper för moln](https://msdn.microsoft.com/library/azure/ee460806.aspx) åtgärder, har angetts till true.

> [!NOTE]
> Det finns ingen anledning att anropa Rollback i en **lokalt** uppdatera eller uppgradera eftersom VIP-växling uppgraderingar inkluderar ersätta en hel körs instans av din tjänst med en annan.
>
>

Återställning av en pågående uppdatering har följande effekter för distributionen:

* Alla rollinstanser som ännu inte har uppdaterats eller uppgraderas till den nya versionen inte uppdateras eller uppgraderas eftersom dessa instanser körs redan Målversionen av tjänsten.
* Alla rollinstanser som redan har uppdaterats eller uppgraderas till den nya versionen av service-paket (\*.cspkg) fil eller konfigurationen för tjänsten (\*.cscfg)-fil (eller båda) återställs till den förberedande versionen av filerna.

Detta är funktionellt tillhandahålls av följande funktioner:

* Den [återställning uppdatera eller uppgradera](https://msdn.microsoft.com/library/azure/hh403977.aspx) åtgärd som kan anropas på en uppdatering för configuration (utlöses genom att anropa [ändra distributionskonfiguration](https://msdn.microsoft.com/library/azure/ee460809.aspx)) eller en uppgradering (utlöses genom att anropa [ Uppgradera distributionen](https://msdn.microsoft.com/library/azure/ee460793.aspx)) så länge det finns minst en instans i tjänsten som ännu inte har uppdaterats till den nya versionen.
* Elementet låst och elementet RollbackAllowed returneras som en del av texten för svar på de [hämta distribution](https://msdn.microsoft.com/library/azure/ee460804.aspx) och [hämta egenskaper för moln](https://msdn.microsoft.com/library/azure/ee460806.aspx) åtgärder:

  1. Elementet låst kan du identifiera när en mutating åtgärden kan anropas på en viss distribution.
  2. RollbackAllowed-elementet kan du identifiera när den [återställning uppdatering eller uppgradera](https://msdn.microsoft.com/library/azure/hh403977.aspx) åtgärden kan anropas på en viss distribution.

  Du behöver inte kontrollera både låst och RollbackAllowed-element för att kunna utföra en återställning. Det tillräckligt för att bekräfta att RollbackAllowed har angetts till true. Dessa element returneras bara om de här metoderna anropas med begärandehuvudet inställd på ”x-ms-version: 2011-10-01” eller en senare version. Mer information om versionshantering huvuden finns [Management Versionhantering](https://msdn.microsoft.com/library/azure/gg592580.aspx).

Det finns vissa situationer där en återställning av en uppdatering eller uppgradering stöds inte, de är följande:

* Minskad lokala resurser - om uppdateringen ökar lokala resurser för en roll Azure-plattformen tillåter inte att återställa.
* Kvotgränsen - har om uppdateringen har en skala ned åtgärden som du kanske inte längre tillräcklig beräkning kvot för att slutföra åtgärden för återställning. Varje Azure-prenumeration har en kvot som är associerade med den som anger maximalt antal kärnor som kan användas av alla värdbaserade tjänster som hör till prenumerationen. Om en återställning av en viss uppdatering får din prenumeration via kvoten sedan som aktiveras en återställning inte.
* Konkurrenstillstånd – om den första uppdateringen har slutförts, en återställning är inte möjlig.

Är ett exempel på när återställningen av en uppdatering kan vara användbar om du använder den [Uppgraderingsdistribution](https://msdn.microsoft.com/library/azure/ee460793.aspx) åtgärden i manuellt läge för att styra den hastighet med vilken en större uppgradering på plats till din Azure-värdtjänsten är distribuerat.

Under den här fasen av uppgraderingen av du anropa [Uppgraderingsdistribution](https://msdn.microsoft.com/library/azure/ee460793.aspx) i manuellt läge och börjar gå uppgraderingsdomäner. Om någon gång när du övervakar uppgraderingen måste du observera vissa rollinstanser i de första uppgraderingsdomäner som du undersöker inte har svarar, kan du anropa den [återställning uppdatering eller uppgradera](https://msdn.microsoft.com/library/azure/hh403977.aspx) åtgärd avseende distributionen, vilket leder till orörda instanser som inte hade har uppgraderats och rollback-instanser som har uppgraderats till tidigare servicepaket och konfiguration.

<a name="multiplemutatingoperations"></a>

## <a name="initiating-multiple-mutating-operations-on-an-ongoing-deployment"></a>Initierar flera mutating åtgärder på ett pågående distribution
I vissa fall kanske du vill initiera flera samtidiga mutating åtgärder på ett pågående distribution. Exempelvis kan du utföra en uppdatering av tjänsten, och även uppdateringen samlas över din tjänst du vill ändra vissa, t.ex. använda en annan uppdatering för att kunna återställa uppdateringen igen, eller ta bort distributionen. Är ett ärende där detta kan vara nödvändigt om en Serviceuppgradering av innehåller buggy koden som orsakar en uppgraderade rollinstansen kraschar upprepade gånger. I det här fallet är Azure-Infrastrukturkontrollanten inte fortsätta vid tillämpning av uppgradera eftersom ett otillräckligt antal instanser i den uppgraderade domänen är felfria. Det här tillståndet kallas en *fastnat distribution*. Du kan få loss distributionen genom att återställa uppdateringen eller använda en ny uppdatering över övre misslyckas en.

När den ursprungliga begäranden att uppdatera eller uppgradera tjänsten har tagits emot av Azure-Infrastrukturkontrollanten, kan du starta efterföljande mutera åtgärder. Det vill säga att du inte behöver vänta tills den första åtgärden har slutförts innan du kan starta en annan mutating åtgärd.

Initierar en andra uppdateringsåtgärd medan den första uppdateringen pågår utför liknar ångring. Om den andra uppdateringen är i automatiskt läge, uppgraderas den första uppgraderingsdomänen omedelbart, kan leda till instanser från flera uppgraderingsdomäner är offline vid samma tidpunkt.

Åtgärderna mutating är följande: [ändra distributionskonfiguration](https://msdn.microsoft.com/library/azure/ee460809.aspx), [Uppgraderingsdistribution](https://msdn.microsoft.com/library/azure/ee460793.aspx), [Distributionsstatus](https://msdn.microsoft.com/library/azure/ee460808.aspx), [ta bort distributionen ](https://msdn.microsoft.com/library/azure/ee460815.aspx), och [återställning uppdatering eller uppgradering av](https://msdn.microsoft.com/library/azure/hh403977.aspx).

Två åtgärder [hämta distribution](https://msdn.microsoft.com/library/azure/ee460804.aspx) och [hämta egenskaper för moln](https://msdn.microsoft.com/library/azure/ee460806.aspx), returnera flaggan låst som kan granskas för att avgöra om en mutating åtgärden kan anropas på en viss distribution.

För att kunna anropa den version av dessa metoder som returnerar flaggan låst, måste du ange huvudet i begäran till ”x-ms-version: 2011-10-01” eller en senare. Mer information om versionshantering huvuden finns [Management Versionhantering](https://msdn.microsoft.com/library/azure/gg592580.aspx).

<a name="distributiondfroles"></a>

## <a name="distribution-of-roles-across-upgrade-domains"></a>Distributionen av roller uppgradera domäner
Azure fördelar instanser av en roll jämnt över ett antal uppgraderingsdomäner som kan konfigureras som en del av tjänstdefinitionsfilen (.csdef). Max antal uppgraderingsdomäner är 20 och standardvärdet är 5. Mer information om hur du ändrar tjänstdefinitionsfilen finns [Azure Service Definition schemat (.csdef-fil)](cloud-services-model-and-package.md#csdef).

Till exempel om din roll har tio instanser kan innehåller som standard varje uppgraderingsdomänen två instanser. Om din roll har 14 instanser, fyra uppgradera domänerna innehåller tre instanser och en femte domän innehåller två.

Uppgraderingsdomäner identifieras med ett Nollbaserat index: den första uppgraderingsdomänen har ett ID 0 och andra uppgraderingsdomänen har ID 1 och så vidare.

Följande diagram illustrerar hur en tjänst än innehåller två roller ska distribueras när tjänsten definierar två uppgraderingsdomäner. Tjänsten körs åtta instanser av webbrollen och nio instanser av worker-rollen.

![Distribution av Uppgraderingsdomäner](media/cloud-services-update-azure-service/IC345533.png "Distribution av Uppgraderingsdomäner")

> [!NOTE]
> Observera att Azure styr hur instanser fördelade över uppgraderingsdomäner. Det går inte att ange vilka instanser som är allokerade till vilken domän.
>
>

## <a name="next-steps"></a>Nästa steg
[Så här hanterar du molntjänster](cloud-services-how-to-manage-portal.md)  
[Så här övervakar du molntjänster](cloud-services-how-to-monitor.md)  
[Så här konfigurerar du molntjänster](cloud-services-how-to-configure-portal.md)  
