---
title: ta med fil
description: ta med fil
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 01/09/2018
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: f8122f35ac6d604908fc31dcece7dfb53dd50286
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2019
ms.locfileid: "55985515"
---
Delade bildgalleriet är en tjänst som hjälper dig att skapa struktur och organisation runt dina anpassade hanterade VM-avbildningar. Med hjälp av en delad bildgalleriet kan du dela dina avbildningar till olika användare, tjänstens huvudnamn eller AD-grupper i din organisation. Delade bilder kan replikeras till flera regioner, för snabbare skalning av dina distributioner.

En hanterad avbildning är en kopia av antingen en fullständig virtuell dator (inklusive eventuella anslutna datadiskar) eller bara OS-disk, beroende på hur du skapar avbildningen. När du skapar en virtuell dator från avbildningen används en kopia av de virtuella hårddiskarna i bild för att skapa diskar för den nya virtuella datorn. Hanterad avbildning finns kvar i lagringsutrymmet och upprepade gånger kan användas för att skapa nya virtuella datorer.

Om du har ett stort antal hanterade avbildningar som du behöver underhålla och vill göra dem tillgängliga i hela företaget kan använda du en delad bildgalleriet som en lagringsplats som gör det enkelt att uppdatera och dela dina avbildningar. Avgifter för att använda en delad bildgalleriet är bara kostnaderna för den lagring som används av avbildningarna, plus eventuella kostnader för nätverksegress för att replikera avbildningar från källregionen till de publicerade regionerna.

Funktionen delad bildgalleriet har flera resurstyper:

| Resurs | Beskrivning|
|----------|------------|
| **Hanterad avbildning** | Det här är en grundläggande avbildning som kan användas fristående eller används för att skapa en **Avbildningsversion** i ett galleri med avbildningar. Hanterade avbildningarna skapas från generaliserad virtuella datorer. En hanterad avbildning är en särskild typ av virtuell Hårddisk som kan användas för att göra flera virtuella datorer och kan nu användas för att skapa delade bild versioner. |
| **Bildgalleri** | Som Azure Marketplace, en **bildgalleriet** är en lagringsplats för att hantera och dela bilder, men du bestämmer vem som har åtkomst. |
| **Avbildningsdefinitionen** | Bilder har definierats i ett galleri och utföra information om avbildningen och krav för att använda det internt. Detta inkluderar om avbildningen är Windows eller Linux, viktig information och lägsta och högsta minneskrav. Det är en definition av en typ av bild. |
| **Avbildningsversion** | En **Avbildningsversion** är det du använder för att skapa en virtuell dator när du använder ett galleri. Du kan ha flera versioner av en avbildning som behövs för din miljö. Som en hanterad avbildning när du använder en **Avbildningsversion** för att skapa en virtuell dator, versionsnumret för avbildningen som används för att skapa nya diskar för den virtuella datorn. Bild-versioner kan användas flera gånger. |

<br>


![Bild som visar hur du kan ha flera versioner av en avbildning i galleriet](./media/shared-image-galleries/shared-image-gallery.png)

### <a name="regional-support"></a>Regional Support

Regional support för delade bildgallerier är i en begränsad förhandsversion, men kommer att expandera över tid. För den begränsade förhandsversionen är här listan över regioner där du kan skapa gallerier och listan över regioner där du kan replikera alla galleriet bilden: 

| Skapa galleri i  | Replikera Version till |
|--------------------|----------------------|
| Västra centrala USA    |Alla offentliga regioner&#42;|
| USA, östra 2          ||
| Södra centrala USA   ||
| Sydostasien     ||
| Västra Europa        ||
| Västra USA            ||
| Östra USA            ||
| Centrala Kanada     ||
|                    ||



&#42;Om du vill replikera till Australien, centrala och Australien centrala 2 som du behöver ha din prenumeration i listan över godkända. Om du vill begära en lista över tillåtna, går du till: https://www.microsoft.com/en-au/central-regions-eligibility/

## <a name="scaling"></a>Skalning
Delade galleri med avbildningar kan du ange antalet repliker som du vill att Azure ska hålla av avbildningarna. Detta hjälper i scenarier för flera virtuella datorer som VM-distributioner kan spridas till olika repliker, vilket minskar risken för att skapa en instans bearbetningen begränsas på grund av överbelastning av en enskild replik.

![Bild som visar hur du kan skala bilder](./media/shared-image-galleries/scaling.png)


## <a name="replication"></a>Replikering
Delade galleri med avbildningar kan du automatiskt replikera dina avbildningar till andra Azure-regioner. Varje delad Avbildningsversion kan replikeras till andra regioner beroende på vad passar för din organisation. Ett exempel är att alltid replikera den senaste avbildningen i flera områden, medan alla äldre versioner är endast tillgängliga i 1 region. Detta kan hjälpa att spara lagringskostnader för delade bild versioner. 

De regioner som en delad Avbildningsversion replikeras till kan uppdateras efter skapandet. Den tid det tar för att replikera till olika regioner beror på mängden data som kopieras och antalet regioner versionen replikeras till. Det kan ta några timmar i vissa fall. När replikeringen sker, kan du visa replikeringsstatusen per region. När avbildningen replikeringen är klar i en region, kan du sedan distribuera en virtuell dator eller VMSS hjälp bild i regionen.

![Bild som visar hur du kan replikera avbildningar](./media/shared-image-galleries/replication.png)


## <a name="access"></a>Access
Eftersom bildgalleriet för delade, delade avbildningen och delade Avbildningsversion alla resurser, kan de delas med hjälp av den inbyggda interna Azure RBAC styr. Du kan använda RBAC för att dela dessa resurser till andra användare, tjänstens huvudnamn och grupper i din organisation. Omfånget för delning av dessa resurser som ligger inom samma Azure AD-klienten. När en användare har åtkomst till delade avbildningsversionen, kan de distribuera en virtuell dator eller en Virtual Machine Scale Sets i någon av de prenumerationer som de har åtkomst till inom samma Azure AD-klient som delas Avbildningsversion.  Här är delningsapplikationen matrisen som hjälper dig att förstå vad användaren får åtkomst till:

| Delade med användaren     | Delat bildgalleri | Delade bild | Delade Avbildningsversion |
|----------------------|----------------------|--------------|----------------------|
| Delat bildgalleri | Ja                  | Ja          | Ja                  |
| Delade bild         | Nej                   | Ja          | Ja                  |
| Delade Avbildningsversion | Nej                   | Nej           | Ja                  |



## <a name="billing"></a>Fakturering
Det finns utan extra kostnad för att använda delade bildgalleriet-tjänsten. Du kommer att debiteras för följande resurser:
- Kostnader för lagring för att lagra delade bild-versioner. Detta beror på antalet repliker av versionen och antalet regioner versionen replikeras till.
- Nätverks-kostnader för utgående trafik för replikering från regionen som källa för versionen till replikerade regioner.

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

**F.** Hur registrerar jag mig för delade bild galleriet förhandsversion?
 
 A. För att registrera mig för den offentliga förhandsversionen av delade galleri med avbildningar som du behöver registrera för funktionen genom att köra följande kommandon från var och en av de prenumerationer som du vill skapa en delad bildgalleriet, avbildningsdefinitionen eller avbildning versionsresurser, och även om du avser att distribuera virtuella datorer med bild-versioner.

**CLI**: 

```bash 
az feature register --namespace Microsoft.Compute --name GalleryPreview
az provider register --name Microsoft.Compute
```

**PowerShell**: 

```powershell
Register-AzProviderFeature -FeatureName GalleryPreview -ProviderNamespace Microsoft.Compute
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

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


**F.** Hur gör jag för att dela mina avbildningar mellan prenumerationer?
 
 A. Du kan dela bilder över prenumerationer med hjälp av rollbaserad åtkomstkontroll (RBAC). Alla användare som har läsbehörighet till en Bildversion, även över prenumerationer, kommer att kunna distribuera en virtuell dator med versionsnumret för avbildningen.


**F.** Kan jag flytta min befintliga avbildningen i delade avbildningsgalleri?
 
 A. Ja. Det finns 3 scenarier baserat på typerna av bilder som du kan ha.

 Scenario 1: Om du har en hanterad avbildning kan skapas en avbildningsdefinitionen och versionsnumret för avbildningen från den.

 Scenario 2: Om du har en ohanterad generaliserad avbildning kan du skapa en hanterad avbildning från den och skapar sedan en avbildningsdefinitionen och versionsnumret för avbildningen från den. 

 Scenario 3: Om du har en virtuell Hårddisk i ditt lokala filsystem, måste du överföra den virtuella Hårddisken och skapa en hanterad avbildning kan du skapa och bild definitions- och versionsnumret för avbildningen från den. 
    - Om den virtuella Hårddisken är av en virtuell Windows-dator, se [överföra en generaliserad virtuell Hårddisk](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed).
    - Om den virtuella Hårddisken är för en Linux VM, se [överföra en virtuell Hårddisk](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd#option-1-upload-a-vhd)


**F.** Kan jag skapa en Avbildningsversion från en särskild disk?

 A. Nej, vi stöder för närvarande inte specialiserade diskar som bilder. Om du har en särskild disk kan du behöva [skapa en virtuell dator från den virtuella Hårddisken](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-specialized-portal#create-a-vm-from-a-disk) genom att koppla en särskild disk till en ny virtuell dator. När du har en aktiv virtuell dator, måste du följa anvisningarna för att skapa en hanterad avbildning från den [Windows VM](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-custom-images) eller [Linux VM](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images). När du har en generaliserad avbildning av hanterade kan starta du processen med att skapa en delad Bildbeskrivning och versionsnumret för avbildningen.


**F.** Kan jag skapa en delad bildgalleriet och avbildningsdefinitionen Avbildningsversion via Azure portal?

 A. Nej, för närvarande vi stöder inte skapandet av någon av bildgalleriet för delade resurser via Azure-portalen. Men stöder vi skapandet av bildgalleriet för delade resurser via CLI, mallar och SDK: er. PowerShell släpps även snart.

 
**F.** När du skapat kan jag uppdatera avbildningsdefinitionen eller versionsnumret för avbildningen? Vilken typ av information kan jag ändra?

 A. Den information som kan uppdateras på var och en av resurserna som anges nedan:
 
Delade bildgalleriet:
- Beskrivning

definition av avbildningen:
- Rekommenderade virtuella processorer
- Minne
- Beskrivning
- Slutet av liv datum

Avbildningsversion:
- Regionala replikantal
- Målregioner
- Undantag från den senaste
- Slutet av liv datum


**F.** När du skapat kan jag flytta delade bildgalleriet resursen till en annan prenumeration?

 A. Nej, du kan inte flytta galleriresursen delade avbildning till en annan prenumeration. Dock kommer du att kunna replikera bild-versioner i galleriet till andra regioner efter behov.

**F.** Kan jag replikera versionerna avbildning i molnet – Azure Kina 21Vianet, Azure Tyskland och Azure Government-molnet? 

 A. Nej, du kan inte replikera bild versioner i molnet.

**F.** Kan jag replikera bild versionerna mellan prenumerationer? 

 A. Nej, kan du replikera versionerna som bild över regioner i en prenumeration och använda den i andra prenumerationer via RBAC.

**F.** Kan jag dela bild versioner i Azure AD-klienter? 

 A. Nej, stöder inte för närvarande delade bildgalleriet delning av avbildningsversioner över Azure AD-klienter. Du kan dock använda funktionen privata erbjudanden på Azure Marketplace för att uppnå detta.


**F.** Hur lång tid tar det för att replikera bild versioner i målregioner?

 A. Bild-version replikeringstid är helt beroende på storleken på bilden och antalet regioner som replikeras till. Dock som bästa praxis rekommenderas att du hålla avbildningen små och regionerna som käll- och stänga för bästa resultat. Du kan kontrollera status för replikering med hjälp av flaggan - ReplicationStatus.


**F.** Hur många delade bildgallerier kan jag skapa i en prenumeration?

 A. Standardkvoten är: 
- 10 delade bildgallerier per prenumeration per region
- 200 bild definitioner, per prenumeration per region
- 2000 bild versioner, per prenumeration per region


**F.** Vad är skillnaden mellan källa och målregion?

 A. Källregionen är den region där din Avbildningsversion kommer att skapas och målregioner är de regioner som en kopia av din Avbildningsversion ska lagras. Du kan bara ha en källregionen för varje Avbildningsversion av. Kontrollera också att du skickar region källplats som en av målregionerna som när du skapar en Bildversion.  


**F.** Hur anger jag källregionen när du skapar versionsnumret för avbildningen?

 A. När du skapar en Bildversion måste du använda den **--plats** tagg i CLI och **-plats** tagg i PowerShell för att ange källregionen. Kontrollera att den hantera avbildning som du använder som bas bilden för att skapa versionsnumret för avbildningen är på samma plats som den plats där du tänker skapa versionsnumret för avbildningen. Kontrollera också att du skickar region källplats som en av målregionerna som när du skapar en Bildversion.  


**F.** Hur anger jag antalet repliker för bild-version som ska skapas i varje region?

 A. Det finns två sätt som du kan ange antalet repliker för bild-version som ska skapas i varje region:
 
1. Regionala replikantalet som anger antalet repliker som du vill skapa per region. 
2. Vanliga replikantalet som är standard per region antal om regionala replikantal inte har angetts. 

Om du vill ange regionala replikantalet skicka plats tillsammans med antalet repliker som du vill skapa i den regionen så här: ”Södra centrala USA = 2”. 

Om regionala replikantal inte anges med varje plats, kommer standardvärdet för antal repliker vara vanliga replikantalet som du har angett. 

Om du vill ange vanliga replikantalet i CLI använder den **--replikantal** argumentet i den `az sig image-version create` kommando.


**F.** Kan jag skapa delade bildgalleriet i en annan plats än den där jag vill skapa avbildningsdefinitionen och Avbildningsversion?

 A. Ja, det är möjligt. Men som bästa praxis, rekommenderar vi att du behåller resursgrupp, delade bildgalleriet, avbildningsdefinitionen och Avbildningsversion på samma plats.


**F.** Vad kostar för att använda delade galleriet?

 A. Det kostar inget att använda delade bildgalleriet tjänsten, utom på lagringskostnaderna för lagring av avbildningsversioner och kostnader för utgående trafik i nätverket för att replikera bild-versioner från källregionen till målregioner.

**F.** Vilka API-version ska jag använda för att skapa delade bildgalleriet, Avbildningsdefinitionen, versionsnumret för avbildningen och VM/VMSS utanför versionsnumret för avbildningen?

 A. För virtuell dator och Virtual Machine Scale Sets distributioner som använder en Avbildningsversion av, rekommenderar vi du använder API-versionen 2018-04-01 eller högre. Om du vill arbeta med delade bildgallerier, bild-definitioner och avbildningsversioner, rekommenderar vi att du använder API-versionen 2018-06-01. 
