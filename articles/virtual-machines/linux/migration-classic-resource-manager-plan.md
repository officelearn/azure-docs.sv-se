---
title: Planera för migrering från klassisk till Azure Resource Manager
description: Planera för migrering av IaaS-resurser från klassisk till Azure Resource Manager
services: virtual-machines-linux
author: tanmaygore
manager: vashan
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: ff829e9ffbd6d6ae0766998e62634ac873afc748
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066665"
---
# <a name="planning-for-migration-of-iaas-resources-from-classic-to-azure-resource-manager"></a>Planera för migrering av IaaS-resurser från klassisk till Azure Resource Manager

> [!IMPORTANT]
> Idag använder cirka 90 % av virtuella IaaS virtuella datorer [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/). Från och med den 28 februari 2020 har klassiska virtuella datorer föråldrats och kommer att vara helt pensionerade den 1 mars 2023. [Läs mer]( https://aka.ms/classicvmretirement) om den här utfasningen och [hur det påverkar dig](https://docs.microsoft.com/azure/virtual-machines/classic-vm-deprecation#how-does-this-affect-me).

Azure Resource Manager erbjuder många fantastiska funktioner, men det är viktigt att planera din migreringsresa för att se till att det går smidigt. Spendera tid på planering kommer att se till att du inte stöter på problem när du utför migreringsaktiviteter.

> [!NOTE]
> Följande vägledning har starkt bidragit till av Azure Customer Advisory-teamet och Cloud Solution-arkitekter som arbetar med kunder på att migrera stora miljöer. Som sådan detta dokument kommer att fortsätta att uppdateras som nya mönster för framgång uppstår, så kom tillbaka då och då för att se om det finns några nya rekommendationer.

Det finns fyra allmänna faser av migrationsresan:

![Migrationsfaser](../media/virtual-machines-windows-migration-classic-resource-manager/plan-labtest-migrate-beyond.png)

## <a name="plan"></a>Planera

### <a name="technical-considerations-and-tradeoffs"></a>Tekniska överväganden och kompromisser

Beroende på dina tekniska krav storlek, geografiska områden och operativa metoder, kanske du vill överväga:

1. Varför önskas Azure Resource Manager för din organisation?  Vilka är affärsskälen till en migrering?
2. Vilka är de tekniska orsakerna till Azure Resource Manager?  Vilka (om några) ytterligare Azure-tjänster vill du utnyttja?
3. Vilket program (eller uppsättningar av virtuella datorer) ingår i migreringen?
4. Vilka scenarier stöds med migrerings-API:et?  Granska [funktioner och konfigurationer som inte stöds](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#unsupported-features-and-configurations).
5. Kommer dina operativa team nu att stödja program/virtuella datorer i både Classic och Azure Resource Manager?
6. Hur (om alls) ändrar Azure Resource Manager dina vm-distributions-, hanterings-, övervaknings- och rapporteringsprocesser?  Behöver dina distributionsskript uppdateras?
7. Vad är kommunikationsplanen för att varna intressenter (slutanvändare, programägare och infrastrukturägare)?
8. Beroende på miljöns komplexitet, bör det finnas en underhållsperiod där programmet inte är tillgängligt för slutanvändare och programägare?  Om så är fallet, hur länge?
9. Vad är utbildningsplanen för att säkerställa att intressenterna är kunniga och kunniga i Azure Resource Manager?
10. Vad är programhanterings- eller projekthanteringsplanen för migreringen?
11. Vilka tidslinjer har Azure Resource Manager-migrering och andra relaterade vägkartor för teknik?  Är de optimalt anpassade?

### <a name="patterns-of-success"></a>Framgångsmönster

Framgångsrika kunder har detaljerade planer där föregående frågor diskuteras, dokumenteras och styrs.  Se till att migrationsplanerna i stor utsträckning meddelas sponsorer och intressenter.  Utrusta dig med kunskap om dina migreringsalternativ; läsa igenom den här migreringsdokumentuppsättningen nedan rekommenderas starkt.

* [Översikt över plattformsstödd migrering av IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [En teknisk djupdykning i plattformsstödd migrering från klassisk distribution till Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Planera för migrering av IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Använda PowerShell för att migrera IaaS-resurser från klassiska till Azure Resource Manager](../windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Använd CLI för att migrera IaaS-resurser från klassiska till Azure Resource Manager](migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Community-verktyg för att hjälpa till med migrering av IaaS-resurser från klassisk till Azure Resource Manager](../windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Granska de vanligaste migreringsfelen](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Granska de vanligaste frågorna om att migrera IaaS-resurser från klassiska till Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="pitfalls-to-avoid"></a>Fallgropar för att undvika

- Underlåtenhet att planera.  De tekniska stegen i denna migration är beprövade och resultatet är förutsägbart.
- Antagandet att plattformen stöds migrering API kommer att ta hänsyn till alla scenarier. Läs [funktionerna och konfigurationerna som inte stöds](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#unsupported-features-and-configurations) för att förstå vilka scenarier som stöds.
- Inte planera potentiella program avbrott för slutanvändare.  Planera tillräckligt med buffert för att på ett adekvat sätt varna slutanvändare för programtid som kan vara otillgänglig.


## <a name="lab-test"></a>Labbtest

**Replikera din miljö och gör en testmigrering**
  > [!NOTE]
  > Exakt replikering av din befintliga miljö utförs med hjälp av ett verktyg som bidrar med community-bidrag som inte stöds officiellt av Microsoft Support. Därför är det ett **valfritt** steg, men det är det bästa sättet att ta reda på problem utan att röra dina produktionsmiljöer. Om det inte är ett alternativ att använda ett community-bidragande verktyg läser du om rekommendationen Validera/Förbered/avbryt torrkörning nedan.
  >

  Att genomföra ett labbtest av ditt exakta scenario (beräkning, nätverk och lagring) är det bästa sättet att säkerställa en smidig migrering. Detta kommer att bidra till att säkerställa:

- Ett helt separat labb eller en befintlig icke-produktionsmiljö att testa. Vi rekommenderar ett helt separat labb som kan migreras upprepade gånger och kan modifieras destruktivt.  Skript för att samla in/återfukta metadata från de verkliga prenumerationerna visas nedan.
- Det är en bra idé att skapa labbet i en separat prenumeration. Anledningen är att labbet kommer att rivas upprepade gånger, och att ha en separat, isolerad prenumeration kommer att minska risken för att något verkligt kommer att få oavsiktligt bort.

  Detta kan åstadkommas med hjälp av asmMetadataParser-verktyget. [Läs mer om det här verktyget här](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)

### <a name="patterns-of-success"></a>Framgångsmönster

Följande var problem som upptäcktes i många av de större migreringarna. Detta är inte en uttömmande lista och du bör hänvisa till [de funktioner och konfigurationer som inte stöds](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#unsupported-features-and-configurations) för mer information. Du kanske eller kanske inte stöter på dessa tekniska problem, men om du löser dessa innan du försöker migrering kommer att säkerställa en smidigare upplevelse.

- **Gör en validera/förbered/avbryta torrkörning** – Det här är kanske det viktigaste steget för att säkerställa att Klassisk till Azure Resource Manager-migrering lyckas. Migrerings-API:et har tre huvudsteg: Validera, förbereda och genomföra. Validate kommer att läsa tillståndet för din klassiska miljö och returnera ett resultat av alla problem. Men eftersom vissa problem kan finnas i Azure Resource Manager-stacken, kommer Validate inte att fånga upp allt. Nästa steg i migreringsprocessen, Förbereda kommer att hjälpa till att avslöja dessa problem. Prepare flyttar metadata från Classic till Azure Resource Manager, men kommer inte att genomföra flytten och kommer inte att ta bort eller ändra något på den klassiska sidan. Den torra körningen innebär att förbereda migreringen och sedan avbryta **(inte begå)** migreringen förbereda. Målet med att validera/förbereda/avbryta torrkörning är att se alla metadata i Azure Resource Manager-stacken, undersöka den *(programmässigt eller i Portal*) och kontrollera att allt migrerar korrekt och arbetar igenom tekniska problem.  Det kommer också att ge dig en känsla av migration varaktighet så att du kan planera för driftstopp därefter.  En validera/förbereda/avbryta orsakar inga avbrott för användaren. Därför är det inte störande för programanvändning.
  - Objekten nedan måste lösas innan torrkörningen, men en torrkörning test kommer också säkert spola ut dessa förberedelser steg om de missas. Under företagsmigreringen har vi funnit att den torra körningen är ett säkert och ovärderligt sätt att säkerställa migrationsberedskap.
  - När förberederen körs låses kontrollplanet (Azure-hanteringsåtgärder) för hela det virtuella nätverket, så inga ändringar kan göras i VM-metadata under validera/förbereda/avbryta.  Men annars påverkas inte någon programfunktion (RD, VM-användning osv.).  Användare av de virtuella datorerna vet inte att den torra körningen körs.

- **Express Route Circuits och VPN**. För närvarande kan Express Route Gateways med auktoriseringslänkar inte migreras utan driftstopp. För lösningen finns i [Migrera ExpressRoute-kretsar och associerade virtuella nätverk från klassikern till Resurshanterarens distributionsmodell](../../expressroute/expressroute-migration-classic-resource-manager.md).

- **VM-tillägg** - Tillägg för virtuella datorer är potentiellt en av de största vägspärrarna till att migrera löpande virtuella datorer. Reparation av VM-tillägg kan ta uppemot 1-2 dagar, så planera därefter.  En fungerande Azure-agent behövs för att rapportera tillbaka VM Extension-status för att köra virtuella datorer. Om statusen kommer tillbaka som dålig för en virtuell dator som körs stoppar detta migreringen. Agenten själv behöver inte vara i funktionsdugligt skick för att möjliggöra migrering, men om tillägg finns på den virtuella datorn, då både en fungerande agent och utgående internetanslutning (med DNS) kommer att behövas för migrering att gå vidare.
  - Om anslutningen till en DNS-server går förlorad under migreringen, alla VM-tillägg utom BGInfo v1. \* måste först tas bort från varje virtuell dator innan migrering förbereds och därefter läggas tillbaka till den virtuella datorn efter Azure Resource Manager-migrering.  **Detta är endast för virtuella datorer som körs.**  Om de virtuella datorerna stoppas behöver VM-tillägg inte tas bort. **Anm.:** Många tillägg som Azure-diagnostik och övervakning av säkerhetscenter kommer att installera om sig själva efter migreringen, så att ta bort dem är inte ett problem.
  - Kontrollera dessutom att nätverkssäkerhetsgrupper inte begränsar utgående internetåtkomst. Detta kan inträffa med vissa nätverkssäkerhetsgrupper konfigurationer. Utgående internetåtkomst (och DNS) behövs för att VM-tillägg ska migreras till Azure Resource Manager.
  - Det finns två versioner av BGInfo-tillägget: v1 och v2.  Om den virtuella datorn skapades med Azure-portalen eller PowerShell, kommer den virtuella datorn sannolikt att ha v1-tillägget på den. Det här tillägget behöver inte tas bort och hoppas över (inte migreras) av migrerings-API:et. Men om den klassiska virtuella datorn skapades med den nya Azure-portalen, kommer det sannolikt att ha JSON-baserade v2-versionen av BGInfo, som kan migreras till Azure Resource Manager förutsatt att agenten fungerar och har utgående internetåtkomst (och DNS).
  - **Reparationsalternativ 1**. Om du vet att dina virtuella datorer inte kommer att ha utgående internetåtkomst, en fungerande DNS-tjänst och fungerande Azure-agenter på de virtuella datorerna, avinstallerar du alla VM-tillägg som en del av migreringen före preparering och installerar sedan om vm-tilläggen efter migreringen.
  - **Reparationsalternativ 2**. Om vm-tillägg är för stora av ett hinder, är ett annat alternativ att stänga/ deallocate alla virtuella datorer före migreringen. Migrera de frigörande virtuella datorerna och starta sedan om dem på Azure Resource Manager-sidan. Fördelen här är att VM-tillägg migreras. Nackdelen är att alla offentliga inför virtuella IP-adresser kommer att gå förlorade (detta kan vara en icke-starter), och naturligtvis de virtuella datorerna kommer att stängas av orsakar en mycket större inverkan på arbetsprogram.

    > [!NOTE]
    > Om en Azure Security Center-princip har konfigurerats mot de virtuella datorer som körs som migreras, måste säkerhetsprincipen stoppas innan tillägg tas bort, annars installeras säkerhetsövervakningstillägget om automatiskt på den virtuella datorn efter att den har tagit bort den.

- **Tillgänglighetsuppsättningar** – För att ett virtuellt nätverk (vNet) ska migreras till Azure Resource Manager måste den klassiska distributionen (dvs. molntjänsten) vara virtuella datorer alla vara i en tillgänglighetsuppsättning eller de virtuella datorerna får alla inte vara i någon tillgänglighetsuppsättning. Att ha mer än en tillgänglighet inställd i molntjänsten är inte kompatibelt med Azure Resource Manager och stoppar migreringen.  Dessutom kan det inte finnas vissa virtuella datorer i en tillgänglighetsuppsättning och vissa virtuella datorer som inte har angetts för tillgänglighet. För att lösa detta måste du åtgärda eller blanda om molntjänsten.  Planera därefter eftersom detta kan vara tidskrävande.

- **Distributioner av webb-/arbetarroller** – molntjänster som innehåller webb- och arbetsroller kan inte migrera till Azure Resource Manager. Webb-/arbetsrollerna måste först tas bort från det virtuella nätverket innan migreringen kan starta.  En typisk lösning är att bara flytta webb-/arbetsrollinstanser till ett separat klassiskt virtuellt nätverk som också är länkat till en ExpressRoute-krets, eller att migrera koden till nyare PaaS App Services (den här diskussionen ligger utanför det här dokumentets omfattning). I det tidigare omfördelningsfallet skapar du ett nytt virtuellt nätverk med Klassiskt nätverk, flyttar/distribuerar webb-/arbetarrollerna till det nya virtuella nätverket och tar sedan bort distributionerna från det virtuella nätverket som flyttas. Inga kodändringar krävs. Den nya [virtual network peering-funktionen](../../virtual-network/virtual-network-peering-overview.md) kan användas för att peer-tillsammans det klassiska virtuella nätverket som innehåller webb-/arbetsroller och andra virtuella nätverk i samma Azure-region, till exempel det virtuella nätverket som migreras (**när den virtuella nätverksmigreringen har slutförts eftersom peer-peer-virtuella nätverk inte kan migreras),** vilket ger samma funktioner utan prestandaförlust och inga fördröjningar/bandbreddsförseningar. Med tanke på tillägget av [Virtual Network Peering](../../virtual-network/virtual-network-peering-overview.md)kan webb-/arbetsrolldistributioner nu enkelt minskas och inte blockera migreringen till Azure Resource Manager.

- **Azure Resource Manager-kvoter** – Azure-regioner har separata kvoter/gränser för både Classic och Azure Resource Manager. Även om ny maskinvara inte förbrukas i ett migreringsscenario *(vi byter befintliga virtuella datorer från Klassisk till Azure Resource Manager)* måste Azure Resource Manager-kvoter fortfarande finnas på plats med tillräcklig kapacitet innan migreringen kan starta. Nedan listas de stora gränserna vi har sett orsaka problem.  Öppna en kvotsupportbiljett för att höja gränserna.

    > [!NOTE]
    > Dessa gränser måste höjas i samma region som din nuvarande miljö som ska migreras.
    >

  - Nätverksgränssnitt
  - Lastbalanserare
  - Offentliga IP-adresser
  - Statiska offentliga IP-adresser
  - Kärnor
  - Nätverkssäkerhetsgrupper
  - Routningstabeller

    Du kan kontrollera dina aktuella Azure Resource Manager-kvoter med följande kommandon med den senaste versionen av Azure CLI.

    **Beräkning** *(kärnor, tillgänglighetsuppsättningar)*

    ```azurecli
    az vm list-usage -l <azure-region> -o jsonc
    ```

    **Nätverk** *(virtuella nätverk, statiska offentliga IP-adresser, offentliga IP-adresser, nätverkssäkerhetsgrupper, nätverksgränssnitt, belastningsutjämnare, vägtabeller)*

    ```azurecli
    az network list-usages -l <azure-region> -o jsonc
    ```

    **Lagring** *(lagringskonto)*

    ```azurecli
    az storage account show-usage
    ```

- **Api-begränsningsbegränsningar för Azure Resource Manager** - Om du har en tillräckligt stor miljö (t.ex. > 400 virtuella datorer i ett VNET) kan du komma till standardgränser för API-begränsning för skrivningar (för närvarande **1 200 skrivningar/timme)** i Azure Resource Manager. Innan du påbörjar migreringen bör du höja en supportbiljett för att öka den här gränsen för din prenumeration.

- **Etablering timed out VM-status** - Om en virtuell dator har status för **etablering timed out**, detta måste lösas före migreringen. Det enda sättet att göra detta är med driftstopp genom att avetablera/återetablera den virtuella datorn (ta bort den, behålla disken och återskapa den virtuella datorn).

- **RoleStateUnknown VM-status** - Om migreringen stoppas på grund av ett okänt felmeddelande om **rolltillstånd,** inspektera den virtuella datorn med hjälp av portalen och se till att den körs. Det här felet försvinner vanligtvis på egen hand (ingen reparation krävs) efter några minuter och är ofta en tillfällig typ som ofta ses under en virtuell dator **start**, **stoppa**, **starta om** åtgärder. **Rekommenderad övning:** försök igen migreringen igen efter några minuter.

- **Fabric Cluster finns inte** - I vissa fall kan vissa virtuella datorer inte migreras av olika udda skäl. Ett av dessa kända fall är om den virtuella datorn nyligen skapades (inom den senaste veckan eller så) och råkade landa ett Azure-kluster som ännu inte är utrustat för Azure Resource Manager-arbetsbelastningar.  Du får ett felmeddelande om att **det inte finns något kluster för fabric-kluster** och att den virtuella datorn inte kan migreras. Väntar ett par dagar kommer vanligtvis att lösa detta problem som klustret kommer snart att få Azure Resource Manager aktiverat. En omedelbar lösning är dock `stop-deallocate` till den virtuella datorn, fortsätt sedan framåt med migrering och starta den virtuella datorn i Azure Resource Manager efter migreringen.

### <a name="pitfalls-to-avoid"></a>Fallgropar för att undvika

- Ta inte genvägar och utelämna inte de validera/förbereda/avbryta migreringarna av torrkörning.
- De flesta, om inte alla, av dina potentiella problem kommer att dyka upp under stegen validera/förbereda/avbryta.

## <a name="migration"></a>Migrering

### <a name="technical-considerations-and-tradeoffs"></a>Tekniska överväganden och kompromisser

Nu är du redo eftersom du har arbetat igenom kända problem med din miljö.

För de verkliga migrningar, kanske du vill överväga:

1. Planera och schemalägg det virtuella nätverket (minsta migreringsenhet) med ökad prioritet.  Gör enkla virtuella nätverk först, och framsteg med de mer komplicerade virtuella nätverk.
2. De flesta kunder kommer att ha icke-produktions- och produktionsmiljöer.  Schemalägg produktionen senast.
3. **(VALFRITT)** Schemalägg ett underhållsavstämning med gott om buffert om oväntade problem uppstår.
4. Kommunicera med och anpassa dig till dina supportteam om problem uppstår.

### <a name="patterns-of-success"></a>Framgångsmönster

Den tekniska vägledningen från avsnittet Labbtest ovan bör övervägas och mildras före en verklig migrering.  Med adekvat testning är migreringen faktiskt en icke-händelse.  För produktionsmiljöer kan det vara bra att ha ytterligare support, till exempel en betrodd Microsoft-partner eller Microsoft Premier-tjänster.

### <a name="pitfalls-to-avoid"></a>Fallgropar för att undvika

Inte fullständigt testa kan orsaka problem och fördröjning i migreringen.  

## <a name="beyond-migration"></a>Bortom migration

### <a name="technical-considerations-and-tradeoffs"></a>Tekniska överväganden och kompromisser

Nu när du är i Azure Resource Manager, maximera plattformen.  Läs [översikten över Azure Resource Manager](../../azure-resource-manager/management/overview.md) för att ta reda på mer om ytterligare fördelar.

Saker att tänka på:

- Kombination av migrationen med andra aktiviteter.  De flesta kunder väljer ett programunderhållsfönster.  Om så är fallet kanske du vill använda den här driftstoppen för att aktivera andra Azure Resource Manager-funktioner som kryptering och migrering till hanterade diskar.
- Gå tillbaka till de tekniska och affärsmässiga orsakerna till Azure Resource Manager. aktivera ytterligare tjänster som endast är tillgängliga på Azure Resource Manager som gäller för din miljö.
- Modernisera din miljö med PaaS-tjänster.

### <a name="patterns-of-success"></a>Framgångsmönster

Var målmedveten om vilka tjänster du nu vill aktivera i Azure Resource Manager.  Många kunder tycker att nedanstående är övertygande för sina Azure-miljöer:

- [Rollbaserad åtkomstkontroll](../../role-based-access-control/overview.md).
- [Azure Resource Manager-mallar för enklare och mer kontrollerad distribution](../../azure-resource-manager/templates/overview.md).
- [Taggar](../../azure-resource-manager/management/tag-resources.md).
- [Aktivitetskontroll](../../azure-resource-manager/management/view-activity-logs.md)
- [Azure-principer](../../governance/policy/overview.md)

### <a name="pitfalls-to-avoid"></a>Fallgropar för att undvika

Kom ihåg varför du startade den här klassiska till Azure Resource Manager-migreringsresan.  Vilka var de ursprungliga affärsskälen? Har du uppnått affärsskäl?


## <a name="next-steps"></a>Nästa steg

* [Översikt över plattformsstödd migrering av IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [En teknisk djupdykning i plattformsstödd migrering från klassisk distribution till Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Planera för migrering av IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Använda PowerShell för att migrera IaaS-resurser från klassiska till Azure Resource Manager](../windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Community-verktyg för att hjälpa till med migrering av IaaS-resurser från klassisk till Azure Resource Manager](../windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Granska de vanligaste migreringsfelen](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Granska de vanligaste frågorna om att migrera IaaS-resurser från klassiska till Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
