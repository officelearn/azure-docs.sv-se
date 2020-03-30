---
title: ta med fil
description: ta med fil
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 05/06/2019
ms.author: akjosh
ms.custom: include file
ms.openlocfilehash: a477114bda7d138a6860d21f2fad75e27d968833
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80116897"
---
Shared Image Gallery är en tjänst som hjälper dig att skapa struktur och organisation kring dina hanterade avbildningar. Delade bildgallerier innehåller:

- Hanterad global replikering av bilder.
- Versionshantering och gruppering av bilder för enklare hantering.
- ZRS-konton (Zone Redundant Storage) med hög tillgänglighet i regioner som stöder tillgänglighetszoner. ZRS erbjuder bättre motståndskraft mot zonfel.
- Dela mellan prenumerationer, och även mellan Active Directory (AD) klienter, med hjälp av RBAC.
- Skala dina distributioner med avbildningsrepliker i varje region.

Med hjälp av ett delat bildgalleri kan du dela dina bilder med olika användare, tjänsthuvudnamn eller AD-grupper inom organisationen. Delade avbildningar kan replikeras till flera regioner, för snabbare skalning av dina distributioner.

En hanterad avbildning är en kopia av antingen en fullständig virtuell dator (inklusive alla anslutna datadiskar) eller bara OS-disken, beroende på hur du skapar avbildningen. När du skapar en virtuell dator från avbildningen används en kopia av de virtuella hårddiskarna i avbildningen för att skapa diskarna för den nya virtuella datorn. Den hanterade avbildningen finns kvar i lager och kan användas om och om igen för att skapa nya virtuella datorer.

Om du har ett stort antal hanterade bilder som du behöver underhålla och vill göra dem tillgängliga i hela företaget kan du använda ett delat bildgalleri som en databas som gör det enkelt att dela dina bilder. 

Funktionen Delat bildgalleri har flera resurstyper:

| Resurs | Beskrivning|
|----------|------------|
| **Hanterad bild** | En grundläggande bild som kan användas ensam eller användas för att skapa en **bildversion** i ett bildgalleri. Hanterade avbildningar skapas från [generaliserade](#generalized-and-specialized-images) virtuella datorer. En hanterad avbildning är en speciell typ av virtuell hårddisk som kan användas för att skapa flera virtuella datorer och som nu kan användas för att skapa delade avbildningsversioner. |
| **Ögonblicksbild** | En kopia av en virtuell hårddisk som kan användas för att skapa en **bildversion**. Ögonblicksbilder kan tas från en [specialiserad](#generalized-and-specialized-images) virtuell dator (en som inte har generaliserats) och sedan användas ensamt eller med ögonblicksbilder av datadiskar, för att skapa en specialiserad avbildningsversion.
| **Bildgalleri** | Precis som Azure Marketplace är ett **avbildningsgalleri** en databas för hantering och delning av avbildningar, men du styr vem som har åtkomst. |
| **Bilddefinition** | Bilder definieras i ett galleri och innehåller information om bilden och krav för att använda den i organisationen. Du kan inkludera information som om avbildningen är generaliserad eller specialiserad, operativsystemet, lägsta och högsta minneskrav och viktig information. Det är en definition av en typ av bild. |
| **Bildversion** | En **bildversion** är vad du använder för att skapa en virtuell dator när du använder ett galleri. Du kan ha flera versioner av en avbildning efter behov för din miljö. Precis som en hanterad avbildning används avbildningsversionen när du använder en **avbildningsversion** för att skapa en virtuell dator för att skapa nya diskar för den virtuella datorn. Bildversioner kan användas flera gånger. |

<br>

![Bild som visar hur du kan ha flera versioner av en bild i galleriet](./media/shared-image-galleries/shared-image-gallery.png)

## <a name="image-definitions"></a>Bilddefinitioner

Bilddefinitioner är en logisk gruppering för versioner av en bild. Bilddefinitionen innehåller information om varför bilden skapades, vilket operativsystem den är till för och information om hur du använder bilden. En bilddefinition är som en plan för alla detaljer kring att skapa en viss bild. Du distribuerar inte en virtuell dator från en avbildningsdefinition, utan från den avbildningsversion som skapats från definitionen.

Det finns tre parametrar för varje bilddefinition som används i kombination - **Publisher**, **Offer** och **SKU**. Dessa används för att hitta en specifik bilddefinition. Du kan ha bildversioner som delar ett eller två, men inte alla tre värdena.  Här är till exempel tre bilddefinitioner och deras värden:

|Bilddefinition|Utgivare|Erbjudande|Sku|
|---|---|---|---|
|myImage1|Contoso|Ekonomi|Backend|
|myImage2|Contoso|Ekonomi|Klientdel|
|myImage3|Testning|Ekonomi|Klientdel|

Alla tre har unika uppsättningar av värden. Formatet liknar hur du för närvarande kan ange utgivare, erbjudande och SKU för [Azure Marketplace-avbildningar](../articles/virtual-machines/windows/cli-ps-findimage.md) i Azure PowerShell för att få den senaste versionen av en Marketplace-avbildning. Varje bilddefinition måste ha en unik uppsättning av dessa värden.

Följande är andra parametrar som kan ställas in på bilddefinitionen så att du lättare kan spåra dina resurser:

* Operativsystemtillstånd - Du kan ställa in os-tillståndet till [generaliserad eller specialiserad](#generalized-and-specialized-images).
* Operativsystem - kan vara antingen Windows eller Linux.
* Beskrivning - använd beskrivning för att ge mer detaljerad information om varför bilddefinitionen finns. Du kan till exempel ha en bilddefinition för frontend-servern som har programmet förinstallerat.
* Eula - kan användas för att peka på ett licensavtal för slutanvändare som är specifikt för bilddefinitionen.
* Sekretesspolicy och viktig information – lagra viktig information och sekretesspolicyer i Azure-lagring och tillhandahålla en URI för åtkomst till dem som en del av avbildningsdefinitionen.
* Slutdatum - bifoga ett slutdatum till din bilddefinition för att kunna använda automatisering för att ta bort gamla bilddefinitioner.
* Tag - du kan lägga till taggar när du skapar din bilddefinition. Mer information om taggar finns i [Använda taggar för att ordna dina resurser](../articles/azure-resource-manager/management/tag-resources.md)
* Lägsta och högsta rekommendationer för vCPU och minne - om din bild har vCPU- och minnesrekommendationer kan du bifoga den informationen till din bilddefinition.
* Otillåtna disktyper – du kan ange information om lagringsbehoven för den virtuella datorn. Om bilden till exempel inte är lämplig för vanliga hårddiskar lägger du till dem i listan tillåt inte.

## <a name="generalized-and-specialized-images"></a>Generaliserade och specialiserade bilder

Det finns två operativsystemtillstånd som stöds av Det delade bildgalleriet. Vanligtvis kräver avbildningar att den virtuella datorn som används för att skapa avbildningen har generaliserats innan avbildningen togs. Generalisering är en process som tar bort dator- och användarspecifik information från den virtuella datorn. För Windows används även Sysprep. För Linux kan du använda `-deprovision+user` [waagent](https://github.com/Azure/WALinuxAgent) `-deprovision` eller parametrar.

Specialiserade virtuella datorer har inte gått igenom en process för att ta bort datorspecifik information och konton. Dessutom har virtuella datorer som skapats `osProfile` från specialiserade avbildningar inte en associerad med dem. Detta innebär att specialiserade bilder kommer att ha vissa begränsningar.

- Konton som kan användas för att logga in på den virtuella datorn kan också användas på alla virtuella datorer som skapas med den specialiserade avbildningen som skapas från den virtuella datorn.
- Virtuella datorer har **datornamnet** på den virtuella datorn som avbildningen togs från. Du bör ändra datornamnet för att undvika kollisioner.
- Det `osProfile` är hur viss känslig information skickas `secrets`till den virtuella datorn med hjälp av . Detta kan orsaka problem med att använda KeyVault, WinRM och andra funktioner som används `secrets` i `osProfile`. I vissa fall kan du använda hanterade tjänstidentiteter (MSI) för att kringgå dessa begränsningar.

> [!IMPORTANT]
> Specialiserade bilder är för närvarande i offentlig förhandsgranskning.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> **Kända begränsningar för förhandsgranskning** Virtuella datorer kan bara skapas från specialiserade avbildningar med hjälp av portalen eller API:et. Det finns inget CLI- eller PowerShell-stöd för förhandsversionen.


## <a name="regional-support"></a>Regionalt stöd

Källregioner visas i tabellen nedan. Alla offentliga regioner kan vara målregioner, men för att replikera till Australia Central och Australia Central 2 måste du ha din prenumeration vitlistad. Om du vill begära vitlistning går du till:https://azure.microsoft.com/global-infrastructure/australia/contact/


| Källregioner        |                   |                    |                    |
| --------------------- | ----------------- | ------------------ | ------------------ |
| Australien, centrala     | Kina, östra        | Indien, södra        | Europa, västra        |
| Australien, centrala 2   | Kina Öst 2      | Sydostasien     | Storbritannien, södra           |
| Australien, östra        | Kina, norra       | Japan, östra         | Storbritannien, västra            |
| Australien, sydöstra   | Kina Norra 2     | Japan, västra         | USA DoD, centrala     |
| Brasilien, södra          | Asien, östra         | Sydkorea, centrala      | USA DoD, östra        |
| Kanada, centrala        | USA, östra           | Sydkorea, södra        | US Gov, Arizona     |
| Kanada, östra           | USA, östra 2         | USA, norra centrala   | US Gov, Texas       |
| Indien, centrala         | Östra USA 2 EUAP    | Europa, norra       | US Gov, Virginia    |
| USA, centrala            | Frankrike, centrala    | USA, södra centrala   | Indien, västra         |
| Centrala USA EUAP       | Frankrike, södra      | USA, västra centrala    | USA, västra            |
|                       |                   |                    | USA, västra 2          |



## <a name="limits"></a>Begränsningar 

Det finns gränser per prenumeration för att distribuera resurser med delade avbildningsgallerier:
- 100 delade bildgallerier, per prenumeration, per region
- 1 000 bilddefinitioner per prenumeration, per region
- 10 000 bildversioner, per prenumeration, per region
- Alla diskar som är anslutna till avbildningen måste vara mindre än eller lika med 1 TB i storlek

Mer information finns i [Kontrollera resursanvändning mot gränser](https://docs.microsoft.com/azure/networking/check-usage-against-limits) för exempel på hur du kontrollerar din aktuella användning.
 
## <a name="scaling"></a>Skalning
Med det delade bildgalleriet kan du ange hur många repliker du vill att Azure ska behålla avbildningarna. Detta hjälper i flera VM-distributionsscenarier eftersom vm-distributionerna kan spridas till olika repliker vilket minskar risken för att instansskapande bearbetning begränsas på grund av överbelastning av en enda replik.

Med Shared Image Gallery kan du nu distribuera upp till 1 000 VM-instanser i en uppsättning virtuella datorer (upp från 600 med hanterade avbildningar). Avbildningsrepliker ger bättre distributionsprestanda, tillförlitlighet och konsekvens. Du kan ange ett antal olika repliker i varje målregion, baserat på regionens skalbehov. Eftersom varje replik är en djup kopia av avbildningen hjälper detta till att skala distributionerna linjärt med varje extra replik. Även om vi förstår att inga två bilder eller regioner är desamma, här är vår allmänna riktlinje om hur du använder repliker i en region:

- För VMSS-distributioner (Non-Virtual Machine Scale Set) – för varje 20 virtuella datorer som du skapar samtidigt rekommenderar vi att du behåller en replik. Om du till exempel skapar 120 virtuella datorer samtidigt med samma bild i en region föreslår vi att du behåller minst 6 repliker av bilden. 
- För VMSS-distributioner (Virtual Machine Scale Set) – För varje skalningsuppsättningsdistribution med upp till 600 instanser rekommenderar vi att du behåller minst en replik. Om du till exempel skapar 5 skalningsuppsättningar samtidigt, var och en med 600 VM-instanser med samma avbildning i en enda region, föreslår vi att du behåller minst 5 repliker av avbildningen. 

Vi rekommenderar alltid att du överetablerar antalet repliker på grund av faktorer som bildstorlek, innehåll och OS-typ.

![Bild som visar hur du kan skala bilder](./media/shared-image-galleries/scaling.png)

## <a name="make-your-images-highly-available"></a>Gör dina bilder mycket tillgängliga

[Azure Zone Redundant Storage (ZRS)](https://azure.microsoft.com/blog/azure-zone-redundant-storage-in-public-preview/) ger återhämtning mot ett tillgänglighetszonfel i regionen. Med den allmänna tillgängligheten för det delade bildgalleriet kan du välja att lagra dina bilder i ZRS-konton i regioner med tillgänglighetszoner. 

Du kan också välja kontotyp för var och en av målområdena. Standardtypen för lagringskonto är Standard_LRS, men du kan välja Standard_ZRS för regioner med tillgänglighetszoner. Kontrollera den regionala tillgängligheten för ZRS [här](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs).

![Bild som visar ZRS](./media/shared-image-galleries/zrs.png)

## <a name="replication"></a>Replikering
Med det delade avbildningsgalleriet kan du också replikera dina avbildningar till andra Azure-regioner automatiskt. Varje version av delad avbildning kan replikeras till olika regioner beroende på vad som är meningsfullt för din organisation. Ett exempel är att alltid replikera den senaste avbildningen i flera regioner medan alla äldre versioner endast är tillgängliga i 1 region. Detta kan hjälpa till att spara på lagringskostnader för delade avbildningsversioner. 

De regioner som en version av delad avbildning replikeras till kan uppdateras efter skapandetid. Hur lång tid det tar att replikera till olika regioner beror på hur mycket data som kopieras och antalet regioner som versionen replikeras till. Detta kan ta några timmar i vissa fall. Medan replikeringen sker kan du visa status för replikering per region. När avbildningsreplikeringen är klar i en region kan du sedan distribuera en virtuell dator eller skaluppsättning med den avbildningsversionen i regionen.

![Bild som visar hur du kan replikera bilder](./media/shared-image-galleries/replication.png)

## <a name="access"></a>Åtkomst

Eftersom versionen För delad bildgalleri, bilddefinition och avbildning är alla resurser kan de delas med de inbyggda Azure RBAC-kontrollerna. Med RBAC kan du dela dessa resurser till andra användare, tjänsthuvudnamn och grupper. Du kan även dela åtkomst till personer utanför klienten som de skapades inom. När en användare har åtkomst till versionen delad avbildning kan de distribuera en virtuell dator eller en skaluppsättning för virtuella datorer.  Här är delningsmatrisen som hjälper till att förstå vad användaren får tillgång till:

| Delas med användare     | Delat bildgalleri | Bilddefinition | Avbildningsversion |
|----------------------|----------------------|--------------|----------------------|
| Delat bildgalleri | Ja                  | Ja          | Ja                  |
| Bilddefinition     | Inga                   | Ja          | Ja                  |

Vi rekommenderar att du delar på gallerinivå för bästa upplevelse. Vi rekommenderar inte att du delar enskilda bildversioner. Mer information om RBAC finns i [Hantera åtkomst till Azure-resurser med RBAC](../articles/role-based-access-control/role-assignments-portal.md).

Avbildningar kan också delas i stor skala även över klienter med hjälp av en appregistrering för flera innehavare. Mer information om hur du delar av bilder mellan klienter finns i [Dela virtuella avbildningar i galleri för azure-klienter](../articles/virtual-machines/linux/share-images-across-tenants.md).

## <a name="billing"></a>Fakturering
Det tillkommer ingen extra kostnad för att använda tjänsten Delat bildgalleri. Du debiteras för följande resurser:
- Lagringskostnader för lagring av versionerna för delad bild. Kostnaden beror på antalet repliker av avbildningsversionen och antalet regioner som versionen replikeras till. Om du till exempel har två avbildningar och båda replikeras till 3 regioner debiteras du för 6 hanterade diskar baserat på deras storlek. Mer information finns i [Priser för hanterade diskar](https://azure.microsoft.com/pricing/details/managed-disks/).
- Nätverksutgående avgifter för replikering av den första avbildningsversionen från källregionen till de replikerade regionerna. Efterföljande repliker hanteras inom regionen, så det finns inga extra avgifter. 

## <a name="updating-resources"></a>Uppdatera resurser

När du har skapat kan du göra vissa ändringar i bildgalleriets resurser. Dessa är begränsade till:
 
Delat bildgalleri:
- Beskrivning

Bilddefinition:
- Rekommenderade vCPUs
- Rekommenderat minne
- Beskrivning
- Datum för uttjänt

Bildversion:
- Antal regionala repliker
- Målregioner
- Uteslut från senaste
- Datum för uttjänt

## <a name="sdk-support"></a>SDK-support

Följande SDK-filer har stöd för att skapa delade bildgallerier:

- [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/virtualmachines/management?view=azure-dotnet)
- [Java](https://docs.microsoft.com/java/azure/?view=azure-java-stable)
- [Node.js](https://docs.microsoft.com/javascript/api/@azure/arm-compute)
- [Python](https://docs.microsoft.com/python/api/overview/azure/virtualmachines?view=azure-python)
- [Kör](https://docs.microsoft.com/azure/go/)

## <a name="templates"></a>Mallar

Du kan skapa resurs för delat bildgalleri med hjälp av mallar. Det finns flera Azure Quickstart-mallar tillgängliga: 

- [Skapa ett delat bildgalleri](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Skapa en bilddefinition i ett delat bildgalleri](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Skapa en bildversion i ett delat bildgalleri](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Skapa en virtuell dator från avbildningsversion](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar 

* [Hur kan jag lista alla resurser i det delade bildgalleriet mellan prenumerationer?](#how-can-i-list-all-the-shared-image-gallery-resources-across-subscriptions) 
* [Kan jag flytta min befintliga bild till det delade bildgalleriet?](#can-i-move-my-existing-image-to-the-shared-image-gallery)
* [Kan jag skapa en avbildningsversion från en specialiserad disk?](#can-i-create-an-image-version-from-a-specialized-disk)
* [Kan jag flytta resursen Delat bildgalleri till en annan prenumeration när den har skapats?](#can-i-move-the-shared-image-gallery-resource-to-a-different-subscription-after-it-has-been-created)
* [Kan jag replikera mina avbildningsversioner över moln som Azure China 21Vianet eller Azure Germany eller Azure Government Cloud?](#can-i-replicate-my-image-versions-across-clouds-such-as-azure-china-21vianet-or-azure-germany-or-azure-government-cloud)
* [Kan jag replikera mina avbildningsversioner över prenumerationer?](#can-i-replicate-my-image-versions-across-subscriptions)
* [Kan jag dela avbildningsversioner över Azure AD-klienter?](#can-i-share-image-versions-across-azure-ad-tenants)
* [Hur lång tid tar det att replikera avbildningsversioner i målområdena?](#how-long-does-it-take-to-replicate-image-versions-across-the-target-regions)
* [Vad är skillnaden mellan källregion och målregion?](#what-is-the-difference-between-source-region-and-target-region)
* [Hur anger jag källregionen när jag skapar avbildningsversionen?](#how-do-i-specify-the-source-region-while-creating-the-image-version)
* [Hur anger jag antalet bildversionsrepliker som ska skapas i varje region?](#how-do-i-specify-the-number-of-image-version-replicas-to-be-created-in-each-region)
* [Kan jag skapa det delade bildgalleriet på en annan plats än det för bilddefinitionen och bildversionen?](#can-i-create-the-shared-image-gallery-in-a-different-location-than-the-one-for-the-image-definition-and-image-version)
* [Vilka avgifter debiteras för att använda det delade bildgalleriet?](#what-are-the-charges-for-using-the-shared-image-gallery)
* [Vilken API-version ska jag använda för att skapa delat bildgalleri och bilddefinition och bildversion?](#what-api-version-should-i-use-to-create-shared-image-gallery-and-image-definition-and-image-version)
* [Vilken API-version ska jag använda för att skapa delad virtuell dator eller skala för virtuell dator av avbildningsversionen?](#what-api-version-should-i-use-to-create-shared-vm-or-virtual-machine-scale-set-out-of-the-image-version)

### <a name="how-can-i-list-all-the-shared-image-gallery-resources-across-subscriptions"></a>Hur kan jag lista alla resurser i det delade bildgalleriet mellan prenumerationer?

Så här listar du alla resurser i det delade bildgalleriet över prenumerationer som du har åtkomst till på Azure-portalen:

1. Öppna [Azure-portalen](https://portal.azure.com).
1. Gå till **Alla resurser**.
1. Välj alla prenumerationer som du vill visa alla resurser under.
1. Leta efter resurser av typen **Privat galleri**.
 
   Om du vill visa bilddefinitioner och bildversioner bör du också välja **Visa dolda typer**.
 
   Om du vill visa alla resurser i det delade bildgalleriet över prenumerationer som du har behörighet till använder du följande kommando i Azure CLI:

   ```azurecli
   az account list -otsv --query "[].id" | xargs -n 1 az sig list --subscription
   ```

### <a name="can-i-move-my-existing-image-to-the-shared-image-gallery"></a>Kan jag flytta min befintliga bild till det delade bildgalleriet?
 
Ja. Det finns 3 scenarier baserat på vilka typer av bilder du kan ha.

 Scenario 1: Om du har en hanterad avbildning i samma prenumeration som SIG kan du skapa en bilddefinition och bildversion från den.

 Scenario 2: Om du har en ohanterad avbildning i samma prenumeration som SIG kan du skapa en hanterad avbildning från den och sedan skapa en bilddefinition och bildversion från den. 

 Scenario 3: Om du har en virtuell hårddisk i ditt lokala filsystem måste du ladda upp den virtuella hårddisken till en hanterad avbildning, kan du skapa en bilddefinition och bildversion från den.

- Om den virtuella hårddisken är av en Virtuell Windows-dator läser du [Ladda upp en virtuell hårddisk](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed).
- Om den virtuella hårddisken är för en Virtuell Linux-dator läser du [Ladda upp en virtuell hårddisk](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd#option-1-upload-a-vhd)

### <a name="can-i-create-an-image-version-from-a-specialized-disk"></a>Kan jag skapa en avbildningsversion från en specialiserad disk?

Ja, stöd för specialiserade diskar som avbildningar är i förhandsgranskning. Du kan bara skapa en virtuell dator från en specialiserad avbildning med hjälp av portalen[(Windows](../articles/virtual-machines/linux/shared-images-portal.md) eller [Linux)](../articles/virtual-machines/linux/shared-images-portal.md)och API. Det finns inget PowerShell-stöd för förhandsversionen.

### <a name="can-i-move-the-shared-image-gallery-resource-to-a-different-subscription-after-it-has-been-created"></a>Kan jag flytta resursen Delat bildgalleri till en annan prenumeration när den har skapats?

Nej, du kan inte flytta den delade bildgalleriresursen till en annan prenumeration. Du kan dock replikera avbildningsversionerna i galleriet till andra regioner efter behov.

### <a name="can-i-replicate-my-image-versions-across-clouds-such-as-azure-china-21vianet-or-azure-germany-or-azure-government-cloud"></a>Kan jag replikera mina avbildningsversioner över moln som Azure China 21Vianet eller Azure Germany eller Azure Government Cloud?

Nej, du kan inte replikera avbildningsversioner över moln.

### <a name="can-i-replicate-my-image-versions-across-subscriptions"></a>Kan jag replikera mina avbildningsversioner över prenumerationer?

Nej, du kan replikera avbildningsversionerna mellan regioner i en prenumeration och använda den i andra prenumerationer via RBAC.

### <a name="can-i-share-image-versions-across-azure-ad-tenants"></a>Kan jag dela avbildningsversioner över Azure AD-klienter? 

Ja, du kan använda RBAC för att dela med personer mellan klienter. Men om du vill dela i stor skala läser du "Dela galleriavbildningar över Azure-klienter" med [PowerShell](../articles/virtual-machines/windows/share-images-across-tenants.md) eller [CLI](../articles/virtual-machines/linux/share-images-across-tenants.md).

### <a name="how-long-does-it-take-to-replicate-image-versions-across-the-target-regions"></a>Hur lång tid tar det att replikera avbildningsversioner i målområdena?

Replikeringstiden för avbildningsversionen är helt beroende av bildens storlek och antalet regioner som den replikeras till. Som bästa praxis rekommenderar vi dock att du håller bilden liten och att käll- och målområdena stängs för bästa resultat. Du kan kontrollera replikeringens status med flaggan -ReplicationStatus.

### <a name="what-is-the-difference-between-source-region-and-target-region"></a>Vad är skillnaden mellan källregion och målregion?

Källregion är den region där bildversionen skapas och målområden är de regioner där en kopia av bildversionen ska lagras. För varje bildversion kan du bara ha en källregion. Se också till att du skickar källregionens plats som ett av målområdena när du skapar en bildversion.

### <a name="how-do-i-specify-the-source-region-while-creating-the-image-version"></a>Hur anger jag källregionen när jag skapar avbildningsversionen?

När du skapar en bildversion kan du använda taggen **--location** i CLI och **-Location-taggen** i PowerShell för att ange källregionen. Kontrollera att den hanterade avbildningen som du använder som basavbildning för att skapa bildversionen är på samma plats som den plats där du tänker skapa avbildningsversionen. Se också till att du skickar källregionens plats som ett av målområdena när du skapar en bildversion.  

### <a name="how-do-i-specify-the-number-of-image-version-replicas-to-be-created-in-each-region"></a>Hur anger jag antalet bildversionsrepliker som ska skapas i varje region?

Du kan ange hur många bildversionsrepliker som ska skapas i varje region:
 
1. Antalet regionala repliker som anger antalet repliker som du vill skapa per region. 
2. Det vanliga replikantalet som är standardantalet per region om inte antalet regionala repliker anges. 

Om du vill ange antalet regionala repliker skickar du platsen tillsammans med antalet repliker som du vill skapa i den regionen: "South Central US=2". 

Om regionalt replikantal inte anges med varje plats, blir standardantalet repliker det gemensamma replikantalet som du angav. 

Om du vill ange det vanliga replikantalet i `az sig image-version create` CLI använder du argumentet **--replica-count** i kommandot.

### <a name="can-i-create-the-shared-image-gallery-in-a-different-location-than-the-one-for-the-image-definition-and-image-version"></a>Kan jag skapa det delade bildgalleriet på en annan plats än det för bilddefinitionen och bildversionen?

Ja, det kan du. Men som bästa praxis rekommenderar vi att du behåller resursgruppen, det delade bildgalleriet, bilddefinitionen och bildversionen på samma plats.

### <a name="what-are-the-charges-for-using-the-shared-image-gallery"></a>Vilka avgifter debiteras för att använda det delade bildgalleriet?

Det finns inga avgifter för att använda tjänsten Delat bildgalleri, förutom lagringsavgifterna för lagring av avbildningsversioner och nätverksutgående avgifter för att replikera avbildningsversionerna från källregionen till målområden.

### <a name="what-api-version-should-i-use-to-create-shared-image-gallery-and-image-definition-and-image-version"></a>Vilken API-version ska jag använda för att skapa delat bildgalleri och bilddefinition och bildversion?

Om du vill arbeta med delade bildgallerier, bilddefinitioner och bildversioner rekommenderar vi att du använder API-version 2018-06-01. Zon redundant lagring (ZRS) kräver version 2019-03-01 eller senare.

### <a name="what-api-version-should-i-use-to-create-shared-vm-or-virtual-machine-scale-set-out-of-the-image-version"></a>Vilken API-version ska jag använda för att skapa delad virtuell dator eller skala för virtuell dator av avbildningsversionen?

För vm- och virtual machine-skalningsuppsättningsdistributioner med hjälp av en avbildningsversion rekommenderar vi att du använder API-version 2018-04-01 eller senare.
