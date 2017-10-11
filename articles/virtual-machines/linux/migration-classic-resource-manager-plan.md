---
title: "Planera för migrering av IaaS-resurser från klassiska till Azure Resource Manager | Microsoft Docs"
description: "Planera för migrering av IaaS-resurser från klassiska till Azure Resource Manager"
services: virtual-machines-linux
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 78492a2c-2694-4023-a7b8-c97d3708dcb7
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/01/2017
ms.author: kasing
ms.openlocfilehash: 5db4e5b18ad385e7eba125a1296a9c5054213446
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="planning-for-migration-of-iaas-resources-from-classic-to-azure-resource-manager"></a>Planera för migrering av IaaS-resurser från klassiska till Azure Resource Manager
Azure Resource Manager erbjuder en mängd fantastiska funktioner, är det viktigt att du har planerat din migrering resa att göra det för att gå smidigt. Utgifter tid om hur du planerar säkerställer att det inte uppstår problem vid körning av migreringsaktiviteter. 

> [!NOTE] 
> Följande riktlinjer bidragit kraftigt till Azure Customer Advisory team och Molnlösning arkitekter som arbetar med kunder på migrera stora enviornments. Som det här dokumentet kommer att fortsätta få uppdaterade när nya mönster av lyckade framkommer, så kontrollera tillbaka från tid till tid om det finns några nya rekommendationer.

Det finns fyra allmänna faser transporten migrering:

![Migrering faser](../media/virtual-machines-windows-migration-classic-resource-manager/plan-labtest-migrate-beyond.png)

## <a name="plan"></a>Planera

### <a name="technical-considerations-and-tradeoffs"></a>Teknisk information och nackdelar

Beroende på dina tekniska krav storlek geografiska områden och operativa metoder, kanske du vill överväga:

1. Varför önskas Azure Resource Manager för din organisation?  Vad är affärsskäl för migrering?
2. Vilka är de tekniska orsakerna för Azure Resource Manager?  Vad (eventuella) ytterligare Azure-tjänster vill du använda?
3. Vilka program (eller uppsättningar av virtuella datorer) ingår i migreringen?
4. Vilka scenarier stöds med migreringen API?  Granska de [stöds inte funktioner och konfigurationer](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#unsupported-features-and-configurations).
5. Kommer dina operativa team nu stöd för program/virtuella datorer i Azure Resource Manager och klassisk?
6. Hur (om alls) Azure Resource Manager ändras din VM-distribution, hantering, övervakning och rapportering av processer?  Behöver skripten distributionen uppdateras?
7. Vad är meddelanden som planerar att meddela berörda personer (användare, ägare och infrastrukturens ägare)?
8. Beroende på komplexiteten i miljön, ska det vara en underhållsperiod att programmet är inte tillgänglig för slutanvändare och programägarna?  I så fall, hur länge?
9. Vad är utbildningsplan så intressenter är kunniga och proficient i Azure Resource Manager?
10. Vad är programhantering eller hantering av projektplanen för migrering?
11. Vad är tidslinjer för migrering av Azure Resource Manager och andra relaterade teknik väg maps?  De optimalt justeras?

### <a name="patterns-of-success"></a>Mönster för att lyckas

Lyckad kunder ha detaljerade planer där dessa frågor diskuteras, dokumenteras och omfattas.  Kontrollera migreringen planer brett förmedlas till sponsorer och intressenter.  Förse dig med kunskap om migreringsalternativ; Vi rekommenderar starkt att du läsa igenom det här dokumentet som nedan.

* [Översikt över plattformar som stöds migrering av IaaS-resurser från klassiska till Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Tekniska ingående om plattformen stöds från klassiska till Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Planera för migrering av IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Använda PowerShell för att migrera IaaS-resurser från klassiska till Azure Resource Manager](../windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Använda CLI för att migrera IaaS-resurser från klassiska till Azure Resource Manager](migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Community-verktyg för att hjälpa till med migrering av IaaS-resurser från klassiska till Azure Resource Manager](../windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Granska de vanligaste migreringsfelen](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Granska de vanligaste frågorna om migrera IaaS-resurser från klassiska till Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="pitfalls-to-avoid"></a>Fallgropar att undvika

- Det gick inte att planera.  Teknik-stegen för migreringen är beprövade och resultatet är förutsägbara.
- Antagandet att plattformen stöds migrering API tar hänsyn till alla scenarier. Läs den [stöds inte funktioner och konfigurationer](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#unsupported-features-and-configurations) att förstå vilka scenarier som stöds.
- Planera inte potentiella avbrott i programmet för slutanvändare.  Planera otillräckligt med buffert för att varna rätt slutanvändare kan vara otillgänglig programmet tid.


## <a name="lab-test"></a>Laboratorietest 

**Replikera din miljö och gör en testmigrering**
  > [!NOTE]
  > Exakt replikering av din befintliga miljö körs med hjälp av ett community-bidragit verktyg som inte stöds officiellt av Microsoft-supporten. Därför är det en **valfria** steg, men det är det bästa sättet att ta reda på problem utan att röra din produktionsmiljöer. Om verktyget en community-bidragit inte är ett alternativ, sedan läsa om Abort-Validera/Förbered testsändning rekommendationen nedan.
  >
  
  Utföra ett laboratorietest för ditt exakt scenario (beräkning, nätverk och lagring) är det bästa sättet att säkerställa en smidig migrering. Detta bidrar till:

  - Ett helt separata labb eller en befintlig icke-produktionsmiljö vill testa. Vi rekommenderar en helt separata labb som kan migreras flera gånger och förstörande kan ändras.  Skript för att samla in/hydrat metadata från de verkliga prenumerationerna i listan nedan.
  - Det är en bra idé att skapa labbet i en separat prenumeration. Orsaken är att labbet torn flera gånger, och med en separat, isolerade prenumeration minskar risken att något verkliga kommer av misstag tas bort.

  Detta kan åstadkommas med hjälp av verktyget AsmMetadataParser. [Läs mer om det här verktyget här](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)

### <a name="patterns-of-success"></a>Mönster för att lyckas

Följande har problem som identifieras i många av större migreringar. Detta är inte en fullständig förteckning och du bör se den [stöds inte funktioner och konfigurationer](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#unsupported-features-and-configurations) för mer information. Du kan eller inte kan stöta på dessa tekniska problem, men om du vill lösa dessa innan du försöker migrera säkerställer en jämnare upplevelse.

- **Gör en Avbryt-Validera/Förbered testsändning** -detta kanske är det viktigaste steget så klassisk till Azure Resource Manager migreringen lyckades. Migreringen API har tre steg: verifiera, förbereda och genomförande. Verifiera att läsa tillståndet för klassiska miljön och returnera ett resultat av alla frågor. Men eftersom vissa problem det kan finnas i Azure Resource Manager-stacken, kommer verifiera inte fånga allt. Nästa steg i migreringen, Förbered hjälper exponera dessa problem. Förbereda kommer flytta metadata från klassiska till Azure Resource Manager, men kommer inte genomför flyttningen, och ska inte ta bort eller ändra allt på den klassiska sidan. Testsändningen innebär att förbereda migreringen och sedan avbryts (**inte genomför**) förbereda migreringen. Målet med Validera/förbereda/Avbryt testsändning är att se alla metadata i Azure Resource Manager-stacken, undersöka den (*programmässigt eller i portalen*), och kontrollera att allt migreras korrekt och gå igenom tekniska problem.  Den också ger dig en uppfattning om migreringstid så att du kan planera för avbrottstid i enlighet med detta.  En Validera/förbereda/Avbryt medför några driftavbrott; Därför är det icke-störande för programanvändning.
  - Objekten nedan måste lösas innan testsändningen, men ett testsändning test kommer också på ett säkert sätt att tömma ut dessa förberedelsesteg om de saknas. Under migreringen enterprise hittades testsändning ska vara en säker och ovärderlig sätt att kontrollera migreringsberedskap.
  - När förbereder körs kontrollen plan (Azure hanteringsåtgärder) kommer att låsas för hela virtuella nätverk, så att inga ändringar kan göras VM metadata under Validera/förbereda/Avbryt.  Men annars eventuella program-funktionen (RD, VM användning, etc.) påverkas.  Användare av de virtuella datorerna vet inte att testsändningen utförs.

- **Express Route-kretsar och VPN-**. Express Route-Gateways med auktorisering länkar kan för närvarande inte migreras utan driftavbrott. Lösningen, se [migrera ExpressRoute-kretsar och associerade virtuella nätverk från klassiskt till Resource Manager-distributionsmodellen](../../expressroute/expressroute-migration-classic-resource-manager.md).

- **VM-tillägg** -tillägg för virtuell dator är potentiellt en av de största förbi vägspärrar att migrera virtuella datorer som körs. Reparation av VM-tillägg höjningen 1 – 2 dagar kan ta så planera på lämpligt sätt.  Ett aktivt Azure-agenten behövs för att rapportera status för VM-tillägg för virtuella datorer som körs. Om statusen kommer tillbaka som felaktigt för en VM som körs, stoppas detta migrering. Själva agenten behöver inte vara i fungerande skick vill aktivera migrering, men om tillägg finns på den virtuella datorn, sedan både en fungerande agent och utgående internet-anslutning (med DNS) krävs för migrering för att gå vidare.
  - Om anslutningen till en DNS-server går förlorad under migreringen, alla VM-tillägg utom BGInfo v1. \* måste först tas bort från varje VM innan migreringen förbereder och nytt läggs till i efterhand tillbaka till den virtuella datorn efter migreringen för Azure Resource Manager.  **Detta är endast för virtuella datorer som körs.**  Om de virtuella datorerna har stoppats frigjorts, behöver inte VM-tillägg som ska tas bort. **Obs:** många tillägg som Azure-diagnostik och security center övervakning kan installera sig igen efter migreringen så att de är inte ett problem.
  - Kontrollera dessutom Nätverkssäkerhetsgrupper inte är att begränsa utgående Internetåtkomst. Detta kan inträffa för vissa konfigurationer för Nätverkssäkerhetsgrupper. Utgående Internetåtkomst (och DNS) krävs för VM-tillägg som ska migreras till Azure Resource Manager. 
  - Det finns två versioner av BGInfo-tillägget: v1 och v2.  Om den virtuella datorn har skapats med den klassiska portalen eller PowerShell, har den virtuella datorn sannolikt v1-tillägget på den. Det här tillägget behöver inte tas bort och kommer att hoppas över (inte migrerade) av migreringen API. Men om den klassiska virtuella datorn har skapats med den nya Azure-portalen, troligen har JSON-baserade version 2 av BGInfo som kan migreras till Azure Resource Manager som agenten fungerar och har utgående Internetåtkomst (och DNS). 
  - **Reparation alternativ 1**. Om du vet att dina virtuella datorer inte har utgående Internetåtkomst, en fungerande DNS-tjänsten och arbetar Azure-agenter på virtuella datorer, avinstallera alla VM-tillägg som en del av migreringen innan Förbered kan du sedan installera om VM-tillägg efter migreringen. 
  - **Reparation alternativ 2**. Om VM-tillägg är för stort för en överskrida ett annat alternativ är att avstängning/frigöra alla virtuella datorer innan migreringen. Migrera frigjord virtuella datorer och sedan starta om dem på Azure Resource Manager-sida. Här fördelen är att VM-tillägg ska migreras. Nackdelen är att alla externa virtuella IP-adresser kommer att försvinna (det kan vara en icke-starter), och naturligtvis de virtuella datorerna kommer avslutas orsakar en mycket större inverkan på fungerande program.

    > [!NOTE] 
    > Om en princip för Azure Security Center konfigureras mot de virtuella datorer som körs som migreras, säkerhetsprincipen måste stoppas innan du tar bort tillägg, annars säkerhetsövervakning tillägget installeras automatiskt på den virtuella datorn när du tar bort den.

- **Tillgänglighetsuppsättningar** – för ett virtuellt nätverk (vNet) som ska migreras till Azure Resource Manager, klassisk distribution (d.v.s. Molntjänsten) innehåller virtuella datorer måste vara i en tillgänglighetsuppsättning eller de virtuella datorerna inte finnas i någon tillgänglighetsuppsättning. Med mer än en tillgänglighetsuppsättning i Molntjänsten är inte kompatibel med Azure Resource Manager och stoppas migrering.  Dessutom kan kan det finnas vissa virtuella datorer i en tillgänglighetsuppsättning och vissa virtuella datorer inte i en tillgänglighetsuppsättning. För att lösa problemet behöver du reparera eller blandar Molntjänsten.  Planera därför detta kan ta lång tid. 

- **Web/Worker-Rollsdistributioner** -molntjänster som innehåller webb-och arbetsroller kan inte migrera till Azure Resource Manager. Web/worker-roller måste först tas bort från det virtuella nätverket innan migreringen kan starta.  En typisk lösning är att flytta web/worker rollinstanser till ett separat klassiskt virtuellt nätverk som också är kopplad till en ExpressRoute-krets eller migrera koden till nyare PaaS Apptjänster (den här diskussionen är utanför omfattningen för det här dokumentet). I den tidigare omdistribuera fallet, skapa ett nytt klassiskt virtuellt nätverk, flytta/Omdistributionen web/worker-roller till det nya virtuella nätverket och sedan ta bort distributioner från det virtuella nätverket som flyttas. Inga kodändringar. Den nya [virtuella nätverk Peering](../../virtual-network/virtual-network-peering-overview.md) kapaciteten kan användas för peer-klassiskt virtuellt nätverk som innehåller web/worker-roller och andra virtuella nätverk i samma Azure-region som det virtuella nätverket migreras (**när virtuella nätverk migreringen är klar som peerkoppla virtuella nätverk som inte kan migreras**), därför att ge samma funktioner varken prestanda och ingen fördröjning/bandbredd påföljder. Eftersom egenskaperna [virtuella nätverk Peering](../../virtual-network/virtual-network-peering-overview.md), web/worker-rollsdistributioner kan nu enkelt begränsas och inte blockerar migrering till Azure Resource Manager.

- **Azure Resource Manager-kvoter** -Azure-regioner har separata kvoter/gränser för både klassiska och Azure Resource Manager. Även om ny maskinvara inte används i ett Migreringsscenario *(vi växling befintliga virtuella datorer från klassiska till Azure Resource Manager)*, Azure Resource Manager kvoter fortfarande behöver vara på plats med tillräckligt med kapacitet innan migreringen kan starta. Nedan visas de huvudsakliga gränser som vi har sett orsaka problem.  Öppna ett supportärende för kvot för att höja gränserna. 

    > [!NOTE]
    > Dessa gränser måste aktiveras i samma region som din aktuella miljö som ska migreras.
    >

    - Nätverksgränssnitt
    - Belastningsutjämnare
    - Offentliga IP-adresser
    - Statiska offentliga IP-adresser
    - Kärnor
    - Nätverkssäkerhetsgrupper
    - Routningstabeller

    Du kan kontrollera din aktuella Azure Resource Manager-kvoter med följande kommandon med den senaste versionen av Azure CLI 2.0.

    **Beräkna** *(kärnor, Avaiability anger)*

    ```bash
    az vm list-usage -l <azure-region> -o jsonc 
    ```

    **Nätverket** *(virtuella nätverk, statiska offentliga IP-adresser, offentliga IP-adresser, säkerhetsgrupper för nätverk, nätverket gränssnitt, belastningsutjämnare, vägtabeller)*
    
    ```bash
    az network list-usages -l <azure-region> -o jsonc
    ```

    **Lagring** *(Lagringskonto)*
    
    ```bash
    az storage account show-usage
    ```

- **Azure Resource Manager API throttling-begränsning** - om du har en tillräckligt stor miljö (t.ex. > 400 virtuella datorer i ett virtuellt nätverk), du kan träffa standard-API som begränsning begränsningar för skrivning (för närvarande **1200 skrivningar per timme**) i Azure Resource Manager. Innan du påbörjar migreringen bör du höja ett supportärende för att öka denna gräns för din prenumeration.

- **Etableringsstatus tidsgränsen ut VM** – om någon virtuell dator har status **etablering tidsgränsen**, det måste vara löst före migrering. Det enda sättet att göra detta är avbrott som avetablering/reprovisioning VM (ta bort det, hålla disken och återskapa den virtuella datorn). 

- **RoleStateUnknown VM-statusen** - om migrering stoppas på grund av att en **roll okänt tillstånd** felmeddelande, kontrollera den virtuella datorn med hjälp av portalen och se till att den körs. Det här felet försvinner vanligtvis dess äger (ingen reparation krävs) efter några minuter och ofta övergående typen ofta visas under en virtuell dator **starta**, **stoppa**, **starta om** åtgärder. **Rekommenderad praxis:** nytt försök migrera igen efter några minuter. 

- **Fabric-kluster finns inte** – i vissa fall kan vissa virtuella datorer som inte kan migreras för olika udda skäl. En av dessa kända fall om den virtuella datorn har nyligen skapas (inom den senaste veckan eller liknande) och har hänt med mark ett Azure-kluster som inte ännu är utrustade för Azure Resource Manager-arbetsbelastningar.  Du får ett felmeddelande som säger **fabric-kluster finns inte** och går inte att migrera den virtuella datorn. Väntar på ett par dagar kommer vanligtvis Lös det här problemet enligt klustret kommer snart Azure Resource Manager är aktiverad. En omedelbar lösning är dock att `stop-deallocate` den virtuella datorn sedan fortsätta vidarebefordra migreringen och starta den virtuella datorn tillbaka i Azure Resource Manager när du har migrerat.

### <a name="pitfalls-to-avoid"></a>Fallgropar att undvika

- Inte ta genvägar och utelämna Validera/förbereda/Avbryt testsändning migreringar.
- De flesta, om inte alla, kommer av din potentiella problem yta under Validera/förbereda/Avbryt stegen.

## <a name="migration"></a>Migrering

### <a name="technical-considerations-and-tradeoffs"></a>Teknisk information och nackdelar

Nu är du redo eftersom du har arbetat med kända problem med din miljö.

För de verkliga migreringarna kanske du vill överväga:

1. Planera och schemalägga det virtuella nätverket (minsta enheten av migrering) med ökande prioritet.  Göra enkla virtuella nätverk och gå vidare med mer komplicerad virtuella nätverk.
2. De flesta kunder har icke-produktion och produktionsmiljöer.  Schemalägga produktion senast.
3. **(VALFRITT)**  Schemalägga ett Underhåll driftstopp med mycket buffert ifall oväntade problem uppstå.
4. Kommunicerar med och justera med din supportteam om problem uppstår.

### <a name="patterns-of-success"></a>Mönster för att lyckas

Teknisk information från avsnittet Test Lab ska anses vara och skriptkommandot innan en verklig migrering.  Migreringen är faktiskt en händelsebaserad med lämpliga tester.  För produktionsmiljöer, kan det vara praktiskt att ha ytterligare support, till exempel en betrodd Microsoft-partner eller Microsoft Premier-tjänster.

### <a name="pitfalls-to-avoid"></a>Fallgropar att undvika

Testa inte helt kan orsaka problem och fördröjning i migreringen.  

## <a name="beyond-migration"></a>Utöver migreringen

### <a name="technical-considerations-and-tradeoffs"></a>Teknisk information och nackdelar

Nu när du är i Azure Resource Manager kan du maximera plattformen.  Läs den [översikt över Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) att ta reda på om ytterligare fördelar.

Saker att tänka på:

- Paketering migrering med andra aktiviteter.  Merparten av kunderna välja en underhållsperiod för programmet.  I så fall kanske du vill använda den här driftstörningen för att aktivera andra Azure Resource Manager-funktioner som kryptering och migreringen till hanterade diskar.
- Kontrollera igen tekniska och affärsmässiga skäl för Azure Resource Manager; aktivera ytterligare tjänster som är tillgängliga bara på Azure Resource Manager som gäller för din miljö.
- Modernisera din miljö med PaaS-tjänster.

### <a name="patterns-of-success"></a>Mönster för att lyckas

Vara ändamålsenlig på vilka tjänster som du nu vill aktivera i Azure Resource Manager.  Många kunder att hitta den under intressant för sina miljöer i Azure:

- [Rollbaserad åtkomstkontroll](../../azure-resource-manager/resource-group-overview.md#access-control).
- [Azure Resource Manager-mallar för distribution av enklare och mer kontrollerad](../../azure-resource-manager/resource-group-overview.md#template-deployment).
- [Taggar](../../azure-resource-manager/resource-group-using-tags.md).
- [Kontroll av aktiviteten](../../azure-resource-manager/resource-group-audit.md)
- [Resursprinciper](../../azure-resource-manager/resource-manager-policy.md)

### <a name="pitfalls-to-avoid"></a>Fallgropar att undvika

Kom ihåg varför du startade den här Classic till Azure Resource Manager migrering resa.  Vilka är de ursprungliga affärsskäl? Har du uppnått affärsskäl?


## <a name="next-steps"></a>Nästa steg

* [Översikt över plattformar som stöds migrering av IaaS-resurser från klassiska till Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Tekniska ingående om plattformen stöds från klassiska till Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Planera för migrering av IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Använda PowerShell för att migrera IaaS-resurser från klassiska till Azure Resource Manager](../windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Community-verktyg för att hjälpa till med migrering av IaaS-resurser från klassiska till Azure Resource Manager](../windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Granska de vanligaste migreringsfelen](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Granska de vanligaste frågorna om migrera IaaS-resurser från klassiska till Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
