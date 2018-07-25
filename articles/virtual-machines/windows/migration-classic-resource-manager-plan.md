---
title: Planera för migrering av IaaS-resurser från klassisk till Azure Resource Manager | Microsoft Docs
description: Planera för migrering av IaaS-resurser från klassisk till Azure Resource Manager
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 78492a2c-2694-4023-a7b8-c97d3708dcb7
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/01/2017
ms.author: kasing
ms.openlocfilehash: 7d9d0857a56d135d7fbe8e1f4735380aac1691e0
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/25/2018
ms.locfileid: "39238173"
---
# <a name="planning-for-migration-of-iaas-resources-from-classic-to-azure-resource-manager"></a>Planera för migrering av IaaS-resurser från klassisk till Azure Resource Manager
Även om Azure Resource Manager erbjuder många fantastiska funktioner, är det viktigt att planera migreringen att göra det för att gå smidigt. Ägna tid om hur du planerar säkerställer att det inte uppstår problem vid körning av migreringsaktiviteter.

> [!NOTE]
> Följande riktlinjer har kraftigt bidragit till genom Azure Customer Advisory team och Molnlösning arkitekter som arbetar med kunder migrerar stora miljöer. Eftersom det här dokumentet fortsätter att uppdateras när nya mönster för en lyckad dyker upp, så kom tillbaka från tid till tid för att se om det finns inga nya rekommendationer.

Det finns fyra allmänna faser av migreringen:<br>

![Migreringsfaser](../media/virtual-machines-windows-migration-classic-resource-manager/plan-labtest-migrate-beyond.png)

## <a name="plan"></a>Planera

### <a name="technical-considerations-and-tradeoffs"></a>Teknisk information och nackdelar

Beroende på dina tekniska krav storlek, geografiska områden och operativa metoder kan du tänka på:

1. Varför är Azure Resource Manager det önskade för din organisation?  Vad är affärsskäl för migrering?
2. Vilka är de tekniska orsakerna för Azure Resource Manager?  Vad (i förekommande fall) ytterligare Azure-tjänster vill du använda?
3. Vilka program (eller uppsättningar av virtuella datorer) ingår i migreringen?
4. Vilka scenarier stöds med migreringen API?  Granska den [stöds inte funktioner och konfigurationer](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#unsupported-features-and-configurations).
5. Kommer din operativa team nu stöd för program/VM: ar i både klassiska och Azure Resource Manager?
6. Hur (om alls) Azure Resource Manager ändras VM-distribution, hantering, övervakning och rapporteringsprocesser?  Behöver din distributionsskript uppdateras?
7. Vad är meddelanden som planerar att meddela intressenter (slutanvändare, programägare och infrastrukturens ägare)?
8. Beroende på komplexiteten i miljön, bör det finnas en underhållsperiod där programmet inte är tillgängligt för slutanvändare och programägare?  I så fall, hur länge?
9. Vad är utbildningsplan att säkerställa intressenter kunskap och proficient i Azure Resource Manager?
10. Vad är programhantering eller hantering av projektplanen för migrering?
11. Vad är tidslinjer för Azure Resource Manager-migrering och andra relaterade tekniken vägkartorna?  De optimalt justeras?

### <a name="patterns-of-success"></a>Mönster för en lyckad

Lyckad kunderna har detaljerad planer där föregående frågorna beskrivs, dokumenteras och regleras.  Se till att migreringsplaner brett konfigurationsvärdena förmedlas till sponsorer och intressenter.  Utrusta dig med kunskap om migreringsalternativ; Vi rekommenderar starkt att du läser igenom det här dokumentet för migrering som anges nedan.

* [Översikt över plattformsunderstödd migrering av IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [En teknisk djupdykning i plattformsstödd migrering från klassisk distribution till Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Planera för migrering av IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Använd PowerShell för att migrera IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Använd CLI för att migrera IaaS-resurser från klassisk till Azure Resource Manager](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Community-verktyg för att hjälpa till med migrering av IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Granska de vanligaste migreringsfelen](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Granska de vanligaste frågorna om migrera IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="pitfalls-to-avoid"></a>Man vill undvika

- Det gick inte att planera.  Teknik-steg i migreringen är beprövade och resultatet är förutsägbara.
- Antagandet att plattformen stöds migrering API kommer kontot för alla scenarier. Läs den [stöds inte funktioner och konfigurationer](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#unsupported-features-and-configurations) att förstå vilka scenarier som stöds.
- Planera inte eventuella avbrott för slutanvändare.  Planera otillräckligt med buffert för att varna rätt slutanvändare potentiellt otillgänglig programmet tid.


## <a name="lab-test"></a>Laboratorietest

**Replikera din miljö och gör en testmigrering**
  > [!NOTE]
  > Exakta replikeringen av den befintliga miljön körs med hjälp av ett communityn har bidragit med verktyg som inte officiellt stöds av Microsoft Support. Det är därför en **valfritt** steg, men det är det bästa sättet att ta reda på problem utan att röra produktionsmiljöerna. Om med ett communityn har bidragit med verktyg inte är ett alternativ, läsa om Validera/Förbered/Avbryt testsändning rekommendationen nedan.
  >

  Utföra ett test för labb för just ditt scenario (beräkning, nätverk och lagring) är det bästa sättet att säkerställa en smidig migrering. Detta bidrar till:

  - En helt separat labb eller en befintlig icke-produktionsmiljö att testa. Vi rekommenderar en helt separat labb som kan migreras flera gånger och förstörande kan ändras.  Skript för att samla in/hydrat metadata från de verkliga prenumerationerna finns nedan.
  - Det är en bra idé att skapa labbet i en separat prenumeration. Det beror på att labbet ska vara datakanalen upprepade gånger med en separat, isolerade prenumerationen minskar risken att något riktig kommer av misstag tas bort.

  Detta kan åstadkommas med hjälp av verktyget AsmMetadataParser. [Läs mer om det här verktyget](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)

### <a name="patterns-of-success"></a>Mönster för en lyckad

Följande har ett problem upptäcks i många av de större migreringarna. Detta är inte en fullständig förteckning och du bör använda den [stöds inte funktioner och konfigurationer](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#unsupported-features-and-configurations) för mer information.  Du kan eller inte kan stöta på dessa tekniska problem, men om du vill lösa dessa innan du försöker migrera säkerställer en jämnare upplevelse.

- **Gör en Validera/Förbered/Avbryt testsändning** – detta är kanske det viktigaste steget för att se till att klassisk till Azure Resource Manager migreringen lyckades. Migreringen API har tre huvudsakliga steg: verifiera, förbereda och sedan spara. Verifiera att läsa statusen för din klassiska miljö och returnera ett resultat av alla problem. Men eftersom vissa problem kan finnas i Azure Resource Manager-stacken, kommer verifiera inte fånga allt. Nästa steg i migreringsprocessen, Förbered kan exponera dessa problem. Förbereda att flytta metadata från klassiskt läge till Azure Resource Manager, men kommer inte genomför flytten och ska inte ta bort eller ändra något på sidan för klassisk. Testsändningen innebär att förbereda migreringen och sedan avbryts (**inte transaktionen**) Förbered migreringen. Målet med Validera/förbereda/Avbryt testsändning är att se alla metadata i Azure Resource Manager-stacken, undersöka den (*programmatiskt eller i portalen*), och kontrollera att allt migreras korrekt och gå igenom technical problem.  Kommer också att få en uppfattning om varaktighet för migrering så att du kan planera för avbrott på lämpligt sätt.  En Validera/förbereda/Avbryt medför några driftavbrott; Därför är det icke störande för program som körs på.
  - Objekten nedan måste lösas innan testsändningen, men ett testsändning test kommer också bra att tömma ut dessa förberedelsesteg om de missat. Vi har sett testsändning ska vara ett säkert och ovärderliga sätt att se till att migreringsberedskap under enterprise-migrering.
  - När förbereder körs, kontrollen plan (Azure hanteringsåtgärder) kommer att låsas för hela virtuella nätverk, så att inga ändringar kan göras till VM-metadata under Validera/förbereda/Avbryt.  Men annars eventuella program-funktionen (RD, VM användning osv) påverkas.  Användare av de virtuella datorerna vet inte att testsändningen körs.

- **Express Route-kretsar och VPN-**. Express Route-gatewayer med auktoriseringslänkar kan för närvarande inte migreras utan avbrott. Du hittar lösningen i [migrera ExpressRoute circuits och tillhörande virtuella nätverk från klassiskt till Resource Manager-distributionsmodellen](../../expressroute/expressroute-migration-classic-resource-manager.md).

- **VM-tillägg** -tillägg för virtuell dator är potentiellt en av de största förbi vägspärrar att migrera virtuella datorer som körs. Reparation av VM-tillägg kunde ta höjningen 1 – 2 dagar, så planera på lämpligt sätt.  Ett aktivt Azure-agenten behövs för att rapportera tillbaka VM-tillägg-status för virtuella datorer som körs. Om statusen kommer tillbaka som felaktigt för en aktiv virtuell dator, kommer detta stoppa migreringen. Själva agenten behöver inte vara i fungerande skick att aktivera migrering, men om tillägg finns på den virtuella datorn, sedan både en aktiv agent och utgående internet-anslutning (med DNS) krävs för migrering att gå vidare.
  - Om anslutningen till en DNS-server går förlorad under migreringen, alla VM-tillägg utom BGInfo version 1. \* måste först tas bort från varje virtuell dator innan migreringen förbereder och nytt läggs till i efterhand tillbaka till den virtuella datorn efter migreringen för Azure Resource Manager.  **Detta är endast för virtuella datorer som körs.**  Om de virtuella datorerna stoppas och frigörs, behöver inte VM-tillägg som ska tas bort.

  > [!NOTE]
  > Många av tilläggen som Azure-diagnostik och security center övervakning kommer installera sig igen efter migreringen, så ta bort dem är inte ett problem.

  - Kontrollera dessutom Nätverkssäkerhetsgrupper inte är att begränsa utgående Internetåtkomst. Detta kan inträffa med vissa konfigurationer för Nätverkssäkerhetsgrupper. Utgående internet-åtkomst (och DNS) krävs för VM-tillägg som ska migreras till Azure Resource Manager.
  - Två versioner av BGInfo-tillägget finns och kallas version 1 och 2.  

      - Om den virtuella datorn använder BGInfo version 1-tillägget kan lämna du det här tillägget skick. Migreringen API hoppar över det här tillägget. BGInfo-tillägget kan läggas till efter migreringen.
      - Om Virtuellt datorn använder JSON-baserade BGInfo version 2-tillägget, har den virtuella datorn skapats med Azure portal. Migreringen API innehåller det här tillägget i migreringen till Azure Resource Manager, förutsatt att agenten fungerar och har utgående internet-åtkomst (och DNS).

  - **Alternativ 1 för reparation**. Om du vet att dina virtuella datorer ingen har utgående Internetåtkomst, en fungerande DNS-tjänst och sedan arbeta Azure-agenter på de virtuella datorerna, avinstallera alla VM-tillägg som en del av migreringen innan Förbered kan du sedan installera om VM-tillägg efter migreringen.
  - **Reparation alternativ 2**. Om VM-tillägg är för stor för en överskrida, ett annat alternativ är att Stäng av/frigöra alla virtuella datorer före migreringen. Migrera de virtuella datorerna i frigjort och starta sedan om dem på Azure Resource Manager-sida. Här fördelen är att VM-tillägg migreras. Nackdelen är att alla offentliga virtuella IP-adresser som riktas mot går förlorade (det kan vara en icke-starter), och naturligtvis de virtuella datorerna kommer avslutas orsakar en mycket större inverkan på fungerande program.

    > [!NOTE]
    > Om en princip för Azure Security Center har ställts mot de virtuella datorer som körs som migreras säkerhetsprincipen måste stoppas innan du tar bort tillägg, annars säkerhetsövervakning tillägget installeras automatiskt på den virtuella datorn när du tar bort den.

- **Tillgänglighetsuppsättningar** – för ett virtuellt nätverk (vNet) som ska migreras till Azure Resource Manager, den klassiska distributionen (d.v.s. cloud Services) som de virtuella datorerna måste vara i en tillgänglighetsuppsättning eller de virtuella datorerna inte finnas i någon tillgänglighetsuppsättning. Med fler än en tillgänglighetsuppsättning i Molntjänsten är inte kompatibel med Azure Resource Manager och att stoppa migreringen.  Dessutom kan kan inte det finnas några virtuella datorer i en tillgänglighetsuppsättning och vissa virtuella datorer inte i en tillgänglighetsuppsättning. Om du vill lösa det här behöver du reparera eller blandar din molntjänst.  Planera därför som det kan vara tidskrävande.

- **Web/Worker-Rollsdistributioner** -molntjänster som innehåller webb-och arbetsroller kan inte migrera till Azure Resource Manager. Web/worker-roller måste först tas bort från det virtuella nätverket innan migreringen kan starta.  En typisk lösning är att bara flytta web/worker-rollinstanser till ett separat klassiskt virtuellt nätverk som också är länkad till en ExpressRoute-krets eller migrera koden till den nyare PaaS App Services (den här diskussionen är utanför omfattningen för det här dokumentet). I det tidigare omdistribuera fallet, skapa ett nytt virtuellt nätverk för klassisk, flytta/omdistribution web/worker-roller till det nya virtuella nätverket och sedan ta bort de från det virtuella nätverket som flyttas. Inga kodändringar krävs. Den nya [virtuell Nätverkspeering](../../virtual-network/virtual-network-peering-overview.md) kapaciteten som kan användas för att peer-koppla ihop det klassiska virtuella nätverket som innehåller web/worker-roller och andra virtuella nätverk i samma Azure-region, till exempel virtuella nätverk som migreras (**efter migrering av virtuellt nätverk har slutförts som peer-kopplade virtuella nätverk inte kan migreras**), därför att ge samma funktioner förlora prestanda och ingen fördröjning/bandbredden påföljder. Eftersom egenskaperna [virtuell Nätverkspeering](../../virtual-network/virtual-network-peering-overview.md), web/worker-rollsdistributioner kan nu enkelt kan undvikas och inte blockerar migrering till Azure Resource Manager.

- **Azure Resource Manager-kvoter** -Azure-regioner har separata kvoter/begränsningar för både klassiska och Azure Resource Manager. Även om ny maskinvara inte används i ett Migreringsscenario *(vi växlar befintliga virtuella datorer från klassiskt läge till Azure Resource Manager)*, Azure Resource Manager-kvoter fortfarande behöver vara på plats med tillräckligt med kapacitet innan migrering kan starta. Nedan visas de viktigaste gränserna som vi har sett orsaka problem.  Öppna ett supportärende för kvot för att höja gränserna.

    > [!NOTE]
    > Dessa gränser behöver ökas i samma region som din aktuella miljö som ska migreras.
    >

    - Nätverksgränssnitt
    - Lastbalanserare
    - Offentliga IP-adresser
    - Statiska offentliga IP-adresser
    - Kärnor
    - Nätverkssäkerhetsgrupper
    - Routningstabeller

    Du kan kontrollera din aktuella kvoter för Azure Resource Manager med hjälp av följande kommandon med den senaste versionen av Azure PowerShell.

    **Compute** *(kärnor, Tillgänglighetsuppsättningar)*

    ```powershell
    Get-AzureRmVMUsage -Location <azure-region>
    ```

    **Nätverk** *(virtuella nätverk, statiska offentliga IP-adresser, offentliga IP-adresser, Nätverkssäkerhetsgrupperna, nätverks-gränssnitt, belastningsutjämnare, routningstabeller)*

    ```powershell
    Get-AzureRmUsage /subscriptions/<subscription-id>/providers/Microsoft.Network/locations/<azure-region> -ApiVersion 2016-03-30 | Format-Table
    ```

    **Storage** *(Storage-konto)*

    ```powershell
    Get-AzureRmStorageUsage
    ```

- **Azure Resource Manager API nätverksbegränsningar** – om du har en tillräckligt stor miljö (t.ex.) > 400 virtuella datorer i ett virtuellt nätverk), du kommer till standard-API nätverksbegränsningar för skrivning (för närvarande `1200 writes/hour`) i Azure Resource Manager. Innan du påbörjar migreringen bör du öka ett supportärende för att öka gränsen för din prenumeration.


- **Etableringsstatus tidsgränsen ut VM** – om en virtuell dator har statusen för `provisioning timed out`, det här måste vara löst före migreringen. Det enda sättet att göra detta är avbrott av avetablering/reprovisioning VM (ta bort den, hålla disken och återskapa den virtuella datorn).

- **RoleStateUnknown VM-statusen** – om migrering stannar på grund av ett `role state unknown` felmeddelande, granska den virtuella datorn med hjälp av portalen och se till att den körs. Det här felet försvinner vanligtvis dess äger (ingen reparation krävs) efter ett par minuter och är ofta en typ av tillfälliga ofta visas under en virtuell dator `start`, `stop`, `restart` åtgärder. **Rekommenderad praxis:** nytt försök igen efter några minuter.

- **Fabric-kluster finns inte** – i vissa fall kan vissa inte går att migrera virtuella datorer av olika anledningar för udda. En av dessa kända fall om den virtuella datorn har nyligen skapas (inom den senaste veckan eller liknande) och har hänt att få till ett Azure-kluster som ännu inte är utrustade för Azure Resource Manager-arbetsbelastningar.  Du får ett meddelande om att `fabric cluster does not exist` och går inte att migrera den virtuella datorn. Väntar på ett par dagar löser vanligtvis det här problemet när klustret kommer snart att bli Azure Resource Manager-aktiverade. En omedelbar lösning är dock att `stop-deallocate` den virtuella datorn och sedan fortsätta vidarebefordra migreringen och starta den virtuella datorn tillbaka i Azure Resource Manager när du har migrerat.

### <a name="pitfalls-to-avoid"></a>Man vill undvika

- Inte ta genvägar och utelämna Validera/förbereda/Avbryt testsändning migreringar.
- Mest, om inte alla, visar om din potentiella problem under Validera/förbereda/Avbryt stegen.

## <a name="migration"></a>Migrering

### <a name="technical-considerations-and-tradeoffs"></a>Teknisk information och nackdelar

Nu är du redo eftersom du har arbetat med kända problem med din miljö.

För de verkliga migreringarna kanske du vill att tänka på:

1. Planera och schemalägga det virtuella nätverket (minsta enheten av migrering) med öka prioritet.  Gör enkelt virtuella nätverk först och fortsätter med mer komplicerad virtuella nätverk.
2. De flesta kunder har inte är i produktion och produktionsmiljöer.  Schemalägga produktion senast.
3. **(VALFRITT)**  Schemalägga en driftavbrotten med tillräckligt med buffert om oväntat problem uppstår.
4. Kommunicerar med och överensstämmer med dina supportteam om problem uppstår.

### <a name="patterns-of-success"></a>Mönster för en lyckad

Den tekniska vägledningen från den _testlabb testning_ avsnitt ska räknas och minimera innan en verklig migrering.  Migreringen är faktiskt en händelsebaserad med lämpliga tester.  För produktionsmiljöer, kan det vara bra att ha ytterligare support, till exempel en betrodd Microsoft-partner eller Microsoft Premier-tjänster.

### <a name="pitfalls-to-avoid"></a>Man vill undvika

Testa inte fullständigt kan orsaka problem och fördröjning i migreringen.  

## <a name="beyond-migration"></a>Utöver migreringen

### <a name="technical-considerations-and-tradeoffs"></a>Teknisk information och nackdelar

Nu när du är i Azure Resource Manager kan du maximera plattformen.  Läs den [översikt över Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) att ta reda på om ytterligare förmåner.

Saker att tänka på:

- Paketera migrering med andra aktiviteter.  De flesta kunder välja en underhållsperiod för programmet.  I så fall kanske du vill använda det här driftstoppet för att aktivera andra Azure Resource Manager-funktioner som kryptering och migrera till Managed Disks.
- Gå tillbaka till de tekniska och affärsmässiga skäl för Azure Resource Manager; aktivera ytterligare tjänster som är tillgängliga bara på Azure Resource Manager som gäller för din miljö.
- Modernisera din miljö med PaaS-tjänster.

### <a name="patterns-of-success"></a>Mönster för en lyckad

Vara meningsfullt på vilka tjänster som du nu vill aktivera i Azure Resource Manager.  Många kunder tycker det nedan intressanta för sina Azure-miljöer:

- [Rollbaserad åtkomstkontroll](../../azure-resource-manager/resource-group-overview.md#access-control).
- [Azure Resource Manager-mallar för distribution av enklare och mer kontrollerad](../../azure-resource-manager/resource-group-overview.md#template-deployment).
- [Taggar](../../azure-resource-manager/resource-group-using-tags.md).
- [Kontroll av aktivitet](../../azure-resource-manager/resource-group-audit.md)
- [Azure-principer](../../azure-policy/azure-policy-introduction.md)

### <a name="pitfalls-to-avoid"></a>Man vill undvika

Kom ihåg varför du startat den här från klassisk till Azure Resource Manager-migreringen.  Vad var de ursprungliga affärsskäl? Har du uppnått affärsorsak till?


## <a name="next-steps"></a>Nästa steg

* [Översikt över plattformsunderstödd migrering av IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [En teknisk djupdykning i plattformsstödd migrering från klassisk distribution till Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Använd PowerShell för att migrera IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Använd CLI för att migrera IaaS-resurser från klassisk till Azure Resource Manager](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [VPN-Gateway migrering från klassisk till Resource Manager](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-classic-resource-manager-migration)
* [Migrera ExpressRoute-kretsar och tillhörande virtuella nätverk från klassiskt till Resource Manager-distributionsmodellen](https://docs.microsoft.com/azure/expressroute/expressroute-migration-classic-resource-manager)
* [Community-verktyg för att hjälpa till med migrering av IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Granska de vanligaste migreringsfelen](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Granska de vanligaste frågorna om migrera IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
