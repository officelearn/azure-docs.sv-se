---
title: ta med fil
description: ta med fil
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 05/06/2019
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: 0fe1de9bb674c66d1b665de25ee579bc86e42c75
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65192372"
---
Delade bildgalleriet är en tjänst som hjälper dig att skapa struktur och organisation runt dina anpassade hanterade VM-avbildningar. Delade Image Galleries innehåller:

- Hanterad global replikering av avbildningar.
- Versionshantering och gruppering av avbildningar för enklare hantering.
- Gör dina avbildningar med hög tillgänglighet med Zonredundant lagring (ZRS)-konton i regioner som har stöd för Tillgänglighetszoner. ZRS ger bättre återhämtning mot zonindelad fel.
- Delning mellan prenumerationer och även mellan klienter, med hjälp av RBAC.

Med hjälp av en delad bildgalleriet kan du dela dina avbildningar till olika användare, tjänstens huvudnamn eller AD-grupper i din organisation. Delade bilder kan replikeras till flera regioner, för snabbare skalning av dina distributioner.

En hanterad avbildning är en kopia av antingen en fullständig virtuell dator (inklusive eventuella anslutna datadiskar) eller bara OS-disk, beroende på hur du skapar avbildningen. När du skapar en virtuell dator från avbildningen används en kopia av de virtuella hårddiskarna i bild för att skapa diskar för den nya virtuella datorn. Hanterad avbildning finns kvar i lagringsutrymmet och upprepade gånger kan användas för att skapa nya virtuella datorer.

Om du har ett stort antal hanterade avbildningar som du behöver underhålla och vill göra dem tillgängliga i hela företaget kan använda du en delad bildgalleriet som en lagringsplats som gör det enkelt att dela dina avbildningar. 

Funktionen delad bildgalleriet har flera resurstyper:

| Resource | Beskrivning|
|----------|------------|
| **Hanterad avbildning** | En grundläggande avbildning som kan användas fristående eller används för att skapa en **Avbildningsversion** i ett galleri med avbildningar. Hanterade avbildningarna skapas från generaliserad virtuella datorer. En hanterad avbildning är en särskild typ av virtuell Hårddisk som kan användas för att göra flera virtuella datorer och kan nu användas för att skapa delade bild versioner. |
| **Bildgalleri** | Som Azure Marketplace, en **bildgalleriet** är en lagringsplats för att hantera och dela bilder, men du bestämmer vem som har åtkomst. |
| **Avbildningsdefinitionen** | Bilder har definierats i ett galleri och utföra information om avbildningen och krav för att använda i din organisation. Du kan inkludera information, till exempel om avbildningen är Windows eller Linux, lägsta och högsta minneskrav och viktig information. Det är en definition av en typ av bild. |
| **Avbildningsversion** | En **Avbildningsversion** är det du använder för att skapa en virtuell dator när du använder ett galleri. Du kan ha flera versioner av en avbildning som behövs för din miljö. Som en hanterad avbildning när du använder en **Avbildningsversion** för att skapa en virtuell dator, versionsnumret för avbildningen som används för att skapa nya diskar för den virtuella datorn. Bild-versioner kan användas flera gånger. |

<br>


![Bild som visar hur du kan ha flera versioner av en avbildning i galleriet](./media/shared-image-galleries/shared-image-gallery.png)

## <a name="image-definitions"></a>Avbildningsdefinitioner

Definitionerna för avbildning är en logisk gruppering för versioner av en avbildning. Avbildningsdefinitionen innehåller information om varför avbildningen har skapats, vilka operativsystem som den är avsedd för och information om hur du använder avbildningen. En bild-definition är som en plan för alla detaljer gäller att skapa en viss avbildning. Du distribuera inte en virtuell dator från en bild-definition, men från versionsnumret för avbildningen som skapats från definitionen.


Det finns tre parametrar för varje avbildningsdefinitionen som används i kombination - **Publisher**, **erbjuder** och **SKU**. De används för att hitta en viss avbildning definition. Du kan ha bild-versioner som delar en eller två, men inte alla tre värden.  Här är till exempel tre definitioner som avbildning och deras värden:

|Bilddefinition|Utgivare|Erbjudande|Sku|
|---|---|---|---|
|myImage1|Contoso|Ekonomi|Serverdel|
|myImage2|Contoso|Ekonomi|Klientdel|
|myImage3|Testning|Ekonomi|Klientdel|

Alla tre av dessa ha unika värden. Formatet är liknar hur du kan för närvarande ange utgivare, erbjudande och SKU för [Azure Marketplace-avbildningar](../articles/virtual-machines/windows/cli-ps-findimage.md) i Azure PowerShell för att hämta den senaste versionen av en Marketplace-avbildning. Varje avbildningsdefinitionen måste ha en unik uppsättning dessa värden.

Här följer några andra parametrar som kan ställas in på din avbildningsdefinitionen så att du enkelt kan spåra dina resurser:

* Tillstånd för operativsystemet - du kan konfigurera operativsystemet med generaliserad eller specialiserade, men endast generaliserad stöds för närvarande. Avbildningar måste skapas från virtuella datorer som har generaliserats med hjälp av Sysprep för Windows eller `waagent -deprovision` för Linux.
* Operativsystem – kan vara antingen Windows eller Linux.
* Beskrivning – Använd beskrivning att ge mer detaljerad information om varför avbildningsdefinitionen finns. Du kan till exempel ha en bild-definition för för din front-end-server som har programmet redan är installerat.
* EULA - kan användas för att peka mot ett licensavtal som är specifika för avbildningsdefinitionen.
* Sekretesspolicy och viktig information – lagra viktig information och sekretesspolicy i Azure storage och ange en URI för att komma åt dem som en del av avbildningsdefinitionen.
* Slutet på sin livscykel datum – koppla ett slutet på sin livscykel datum till din avbildningsdefinitionen för att kunna använda automation för att ta bort den gamla avbildningen definitioner.
* Tagg - du kan lägga till taggar när du skapar din avbildningsdefinitionen. Mer information om taggar finns i [med taggar för att organisera dina resurser](../articles/azure-resource-manager/resource-group-using-tags.md)
* Lägsta och högsta virtuell processor och Minnesrekommendationer - om bilden har rekommendationer för virtuella processorer och minne, du kan koppla den här informationen till din avbildningsdefinitionen.
* Otillåten disktyper – du kan ge information om lagringsbehov för den virtuella datorn. Till exempel om avbildningen inte är lämpliga för standarddiskar HDD, du lägga till dem i listan Tillåt inte.


## <a name="regional-support"></a>Regional Support

Regioner visas i tabellen nedan. Alla offentliga regioner kan vara målregioner, men om du vill replikera till Australien, centrala och Australien centrala 2 måste du ha din prenumeration i listan över godkända. Om du vill begära en lista över tillåtna, går du till: https://www.microsoft.com/en-au/central-regions-eligibility/


| Regioner |
|---------------------|-----------------|------------------|-----------------|
| Australien, centrala   | USA, centrala – EUAP | Sydkorea, centrala    | Storbritannien, södra 2      |
| Australien, centrala 2 | Östasien       | Sydkorea, södra      | Storbritannien, västra         |
| Östra Australien      | Östra USA         | Norra centrala USA | Västra centrala USA |
| Sydöstra Australien | USA, östra 2       | Norra Europa     | Västra Europa     |
| Södra Brasilien        | USA, östra 2 – EUAP  | Södra centrala USA | Indien, västra      |
| Centrala Kanada      | Frankrike, centrala  | Södra Indien      | Västra USA         |
| Östra Kanada         | Frankrike, södra    | Sydostasien   | Västra USA         |
| Indien, centrala       | Östra Japan      | Storbritannien, norra         | Västra USA 2       |
| Centrala USA          | Västra Japan      | Storbritannien, södra         |                 |



## <a name="limits"></a>Limits 

Det finns gränser för en prenumeration för att distribuera resurser med hjälp av delad Image Galleries:
- 100 delade bildgallerier per prenumeration per region
- 1 000 bild definitioner, per prenumeration per region
- 10 000 bild-versioner, per prenumeration per region

Mer information finns i [Kontrollera resursanvändningen mot gränser](https://docs.microsoft.com/azure/networking/check-usage-against-limits) för exempel på hur du kontrollerar din aktuella användning.
 

## <a name="scaling"></a>Skalning
Delade galleri med avbildningar kan du ange antalet repliker som du vill att Azure ska hålla av avbildningarna. Detta hjälper i scenarier för flera virtuella datorer som VM-distributioner kan spridas till olika repliker, vilket minskar risken för att skapa en instans bearbetningen begränsas på grund av överbelastning av en enskild replik.

![Bild som visar hur du kan skala bilder](./media/shared-image-galleries/scaling.png)


## <a name="replication"></a>Replikering
Delade galleri med avbildningar kan du automatiskt replikera dina avbildningar till andra Azure-regioner. Varje delad Avbildningsversion kan replikeras till andra regioner beroende på vad passar för din organisation. Ett exempel är att alltid replikera den senaste avbildningen i flera områden, medan alla äldre versioner är endast tillgängliga i 1 region. Detta kan hjälpa att spara lagringskostnader för delade bild versioner. 

De regioner som en delad Avbildningsversion replikeras till kan uppdateras efter skapandet. Den tid det tar för att replikera till olika regioner beror på mängden data som kopieras och antalet regioner versionen replikeras till. Det kan ta några timmar i vissa fall. När replikeringen sker, kan du visa replikeringsstatusen per region. När avbildningen replikeringen är klar i en region, kan du sedan distribuera en virtuell dator eller en skalningsuppsättning med hjälp av den Avbildningsversion i regionen.

![Bild som visar hur du kan replikera avbildningar](./media/shared-image-galleries/replication.png)


## <a name="access"></a>Access

Eftersom bildgalleriet för delade, delade avbildningen och delade Avbildningsversion alla resurser, kan de delas med hjälp av den inbyggda interna Azure RBAC styr. Med RBAC kan du dela dessa resurser till andra användare, tjänstens huvudnamn och grupper. Du kan också dela åtkomst till personer utanför den klient som de skapades i. När en användare har åtkomst till delade avbildningsversionen, kan de distribuera en virtuell dator eller en Virtual Machine Scale Sets.  Här är delningsapplikationen matrisen som hjälper dig att förstå vad användaren får åtkomst till:

| Delade med användaren     | Delat bildgalleri | Delade bild | Delade Avbildningsversion |
|----------------------|----------------------|--------------|----------------------|
| Delat bildgalleri | Ja                  | Ja          | Ja                  |
| Delade bild         | Nej                   | Ja          | Ja                  |
| Delade Avbildningsversion | Nej                   | Nej           | Ja                  |

Vi rekommenderar att dela på nivån galleriet för bästa möjliga upplevelse. Läs mer om RBAC [hantera åtkomst till Azure-resurser med RBAC](../articles/role-based-access-control/role-assignments-portal.md).

Bilder kan också delas, i skala, mellan klienter med hjälp av en app för flera klienter registrering. Läs mer om att dela bilder över klienter [dela galleriet VM-avbildningar i Azure-klienter](../articles/virtual-machines/linux/share-images-across-tenants.md).

## <a name="billing"></a>Fakturering
Det finns utan extra kostnad för att använda delade bildgalleriet-tjänsten. Du kommer att debiteras för följande resurser:
- Kostnader för lagring för att lagra delade bild-versioner. Kostnaden beror på antalet repliker av versionsnumret för avbildningen och antalet regioner versionen replikeras till. Till exempel om du har 2 avbildningar och båda replikeras till 3 regioner, kommer sedan du att ändras för 6 hanterade diskar baserat på deras storlek. Mer information finns i [priser för Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/).
- Nätverks-kostnader för utgående trafik för replikering av den första bildversionen från källregionen till de replikerade regionerna. Efterföljande repliker hanteras i regionen, så det finns inga ytterligare avgifter. 

## <a name="updating-resources"></a>Uppdaterar resurser

När du skapat kan du göra några ändringar till galleriet bildresurser. Det här är begränsat till:
 
Delade bildgalleriet:
- Beskrivning

definition av avbildningen:
- Rekommenderade virtuella processorer
- Rekommenderat minne
- Beskrivning
- Slutet av liv datum

Avbildningsversion:
- Regionala replikantal
- Målregioner
- Undantag från den senaste
- Slutet av liv datum


## <a name="sdk-support"></a>SDK-support

Följande SDK: er stöd för att skapa delade Image Galleries:

- [NET](https://docs.microsoft.com/dotnet/api/overview/azure/virtualmachines/management?view=azure-dotnet)
- [Java](https://docs.microsoft.com/java/azure/?view=azure-java-stable)
- [Node.js](https://docs.microsoft.com/javascript/api/azure-arm-compute/?view=azure-node-latest)
- [Python](https://docs.microsoft.com/python/api/overview/azure/virtualmachines?view=azure-python)
- [Go](https://docs.microsoft.com/go/azure/)

## <a name="templates"></a>Mallar

Du kan skapa delade bildgalleriet resursen med hjälp av mallar. Det finns flera Azure-Snabbstartsmallar: 

- [Skapa en delad bildgalleri](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Skapa en Definition för bilden i en delad Avbildningsgalleri](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Skapa en Bildversion i en delad bildgalleri](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Skapa en virtuell dator från Avbildningsversion](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar 

**F.** Hur kan jag för att lista alla bildgalleriet för delade resurser mellan prenumerationer? 
 
 A. Följ stegen nedan för att lista alla delade bildgalleriet resurser över prenumerationer som du har åtkomst till på Azure portal:

1. Öppna [Azure-portalen](https://portal.azure.com).
1. Gå till **alla resurser**.
1. Välj alla prenumerationer som du vill visa en lista över alla resurser.
1. Leta efter resurser av typen **privat galleri**.
 
   Om du vill se bild definitioner och avbildningsversioner, bör du också välja **Visa dolda typer**.
 
   Om du vill visa alla bildgalleriet för delade resurser över prenumerationer som du har behörighet att använda följande kommando i Azure CLI:

   ```bash
   az account list -otsv --query "[].id" | xargs -n 1 az sig list --subscription
   ```


**F.** Kan jag flytta min befintliga avbildningen i delade avbildningsgalleri?
 
 A. Ja. Det finns 3 scenarier baserat på typerna av bilder som du kan ha.

 Scenario 1: Om du har en hanterad avbildning kan skapas en avbildningsdefinitionen och versionsnumret för avbildningen från den.

 Scenario 2: Om du har en ohanterad generaliserad avbildning kan du skapa en hanterad avbildning från den och skapar sedan en avbildningsdefinitionen och versionsnumret för avbildningen från den. 

 Scenario 3: Om du har en virtuell Hårddisk i ditt lokala filsystem, måste du överföra den virtuella Hårddisken och skapa en hanterad avbildning kan du skapa och bild definitions- och versionsnumret för avbildningen från den.
- Om den virtuella Hårddisken är av en virtuell Windows-dator, se [överföra en generaliserad virtuell Hårddisk](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed).
- Om den virtuella Hårddisken är för en Linux VM, se [överföra en virtuell Hårddisk](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd#option-1-upload-a-vhd)


**F.** Kan jag skapa en Avbildningsversion från en särskild disk?

 A. Nej, vi stöder för närvarande inte specialiserade diskar som bilder. Om du har en särskild disk kan du behöva [skapa en virtuell dator från den virtuella Hårddisken](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-specialized-portal#create-a-vm-from-a-disk) genom att koppla en särskild disk till en ny virtuell dator. När du har en aktiv virtuell dator, måste du följa anvisningarna för att skapa en hanterad avbildning från den [Windows VM](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-custom-images) eller [Linux VM](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images). När du har en generaliserad avbildning av hanterade kan starta du processen med att skapa en delad Bildbeskrivning och versionsnumret för avbildningen.

 
**F.** När du skapat kan jag flytta delade bildgalleriet resursen till en annan prenumeration?

 A. Nej, du kan inte flytta galleriresursen delade avbildning till en annan prenumeration. Dock kommer du att kunna replikera bild-versioner i galleriet till andra regioner efter behov.

**F.** Kan jag replikera versionerna avbildning i molnet – Azure Kina 21Vianet, Azure Tyskland och Azure Government-molnet? 

 A. Nej, du kan inte replikera bild versioner i molnet.

**F.** Kan jag replikera bild versionerna mellan prenumerationer? 

 A. Nej, kan du replikera versionerna som bild över regioner i en prenumeration och använda den i andra prenumerationer via RBAC.

**F.** Kan jag dela bild versioner i Azure AD-klienter? 

 A. Ja, du kan använda RBAC för att dela till enskilda användare över klienter. Men att dela i skala, se ”dela galleri-avbildningar i Azure-klienter” använder [PowerShell](../articles/virtual-machines/windows/share-images-across-tenants.md) eller [CLI](../articles/virtual-machines/linux/share-images-across-tenants.md).


**F.** Hur lång tid tar det för att replikera bild versioner i målregioner?

 A. Bild-version replikeringstid är helt beroende på storleken på bilden och antalet regioner som replikeras till. Dock som bästa praxis rekommenderas att du hålla avbildningen små och regionerna som käll- och stänga för bästa resultat. Du kan kontrollera status för replikering med hjälp av flaggan - ReplicationStatus.


**F.** Vad är skillnaden mellan källa och målregion?

 A. Källregionen är den region där din Avbildningsversion kommer att skapas och målregioner är de regioner som en kopia av din Avbildningsversion ska lagras. Du kan bara ha en källregionen för varje Avbildningsversion av. Kontrollera också att du skickar region källplats som en av målregionerna som när du skapar en Bildversion.  


**F.** Hur anger jag källregionen när du skapar versionsnumret för avbildningen?

 A. När du skapar en Bildversion måste du använda den **--plats** tagg i CLI och **-plats** tagg i PowerShell för att ange källregionen. Kontrollera att den hantera avbildning som du använder som bas bilden för att skapa versionsnumret för avbildningen är på samma plats som den plats där du tänker skapa versionsnumret för avbildningen. Kontrollera också att du skickar region källplats som en av målregionerna som när du skapar en Bildversion.  


**F.** Hur anger jag antalet repliker för bild-version som ska skapas i varje region?

 A. Det finns två sätt som du kan ange antalet repliker för bild-version som ska skapas i varje region:
 
1. Regionala replikantalet som anger antalet repliker som du vill skapa per region. 
2. Vanliga replikantalet som är standard per region antal om regionala replikantal inte har angetts. 

Om du vill ange regionala replikantalet skicka plats tillsammans med antalet repliker som du vill skapa i den regionen: ”Södra centrala USA = 2”. 

Om regionala replikantal inte anges med varje plats, kommer standardvärdet för antal repliker vara vanliga replikantalet som du har angett. 

Om du vill ange vanliga replikantalet i CLI använder den **--replikantal** argumentet i den `az sig image-version create` kommando.


**F.** Kan jag skapa delade bildgalleriet i en annan plats än den där jag vill skapa avbildningsdefinitionen och Avbildningsversion?

 A. Ja, det kan du. Men som bästa praxis, rekommenderar vi att du behåller resursgrupp, delade bildgalleriet, avbildningsdefinitionen och Avbildningsversion på samma plats.


**F.** Vad kostar för att använda delade galleriet?

 A. Det kostar inget att använda delade bildgalleriet tjänsten, utom på lagringskostnaderna för lagring av avbildningsversioner och kostnader för utgående trafik i nätverket för att replikera bild-versioner från källregionen till målregioner.

**F.** Vilka API-version ska jag använda för att skapa delade bildgalleriet, Avbildningsdefinitionen, versionsnumret för avbildningen och VM/VMSS utanför versionsnumret för avbildningen?

 A. För virtuell dator och Virtual Machine Scale Sets distributioner som använder en Avbildningsversion av, rekommenderar vi du använder API-versionen 2018-04-01 eller högre. Om du vill arbeta med delade bildgallerier, bild-definitioner och avbildningsversioner, rekommenderar vi att du använder API-versionen 2018-06-01. 
