---
author: cynthn
ms.author: cynthn
ms.date: 04/30/2019
ms.topic: include
ms.service: virtual-machines-linux
manager: jeconnoc
ms.openlocfilehash: e1b3b5fe603072069cb3a19c7597fcc1872fefd7
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65416369"
---
Avbildningar av standardiserad virtuella datorer (VM) gör att organisationer kan migrera till molnet och säkerställa konsekvens i distributionen. -Avbildningar omfattar vanligtvis fördefinierade inställningar för säkerhet och konfiguration och programvara som krävs. Konfigurera en egen avbildning pipeline kräver tid, infrastruktur och konfiguration, men med Azure VM Image Builder innehåller bara en enkel konfiguration som beskriver din avbildning, skickar den till tjänsten och avbildningen har skapats och distribuerats.
 
Azure VM Image Builder (Azure Image Builder) kan du börja med en Windows eller Linux-baserade Azure Marketplace-avbildning, befintliga anpassade avbildningar eller Red Hat Enterprise Linux (RHEL) ISO och börja lägga till dina egna anpassningar. Eftersom Image Builder bygger på [HashiCorp Packer](https://packer.io/), du kan också importera din befintliga Packer provisioner kommandoskript. Du kan också ange var du vill att dina avbildningar som finns i Azures delade bildgalleri (virtual-machines-common-shared-image-galleries.md), som en hanterad avbildning eller en virtuell Hårddisk.

> [!IMPORTANT]
> Azure Image Builder är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="preview-features"></a>Förhandsversionsfunktioner

För förhandsversionen av stöds dessa funktioner:

- Skapa gyllene baslinje-avbildningar, som innehåller din minimikraven för säkerhet och företagets konfigurationer och Tillåt-avdelningar möjlighet att anpassa dem ytterligare för deras behov.
- Korrigeringar av befintliga avbildningar, kan Image Builder du kontinuerligt uppdatera befintliga anpassade avbildningar.
- Integrering med Azures delad bildgalleri, kan du vill distribuera, version, och skala bilder globalt och ger dig ett hanteringssystem för avbildningen.
- Integrering med befintlig avbildning skapa pipelines, bara anropa Image Builder från din pipeline eller Använd uppgiften DevOps enkelt förhandsversion bild Builder Azure.
- Migrera en befintlig avbildning anpassning pipeline till Azure. Använd dina befintliga skript, kommandon och processer för att anpassa avbildningar.
- Använd Red Hat ta med din egen prenumeration support. Skapa Red Hat Enterprise bilder som ska användas med dina berättigade, oanvända Red Hat-prenumerationer.
- Skapa bilder i VHD-format.
 

## <a name="regions"></a>Regioner
Azure Image Builder Service blir tillgänglig som förhandsversion i dessa regioner. Bilder kan fördelas utanför dessa regioner.
- Östra USA
- USA, östra 2
- USA, västra centrala 
- USA, västra
- USA, västra 2

## <a name="os-support"></a>OS-support
AIB har stöd för Azure Marketplace grundläggande OS-avbildningar:
- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 7.6
- CentOS 7.6
- Windows 2016
- Windows 2019


## <a name="how-it-works"></a>Hur det fungerar


![Skiss på Azure Image Builder](./media/virtual-machines-image-builder-overview/image-builder.png)

Azure Image Builder är en fullständigt hanterad Azure-tjänst som kan nås av en Azure-resursprovidern. Azure Image Builder-processen består av tre huvudsakliga delar: källa, anpassa och distribuera, dessa visas i en mall. Diagrammet nedan visas komponenterna, med några av sina egenskaper. 
 


**Image Builder-process** 

![Skiss på Azure Image Builder-process](./media/virtual-machines-image-builder-overview/image-builder-process.png)

1. Skapa mall för avbildningen som en JSON-fil. Den här .json-filen innehåller information om källa, anpassningar och distribution. Det finns flera exempel i den [Azure Image Builder GitHub-lagringsplatsen](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts).
1. Skicka den till tjänsten, detta skapar en mall för bild-artefakt i resursgruppen som du anger. I bakgrunden, Image Builder laddar ned Källavbildningen eller ISO och skript efter behov. Dessa lagras i en separat resursgrupp som skapas automatiskt i din prenumeration, i formatet: IT_<DestinationResourceGroup>_<TemplateName>. 
1. När mallen avbildningen har skapats kan skapa du sedan avbildningen. I bakgrunden Image Builder använder filerna som mallen och källa du skapar en virtuell dator, nätverk och lagring i IT_<DestinationResourceGroup>_<TemplateName> resursgrupp.
1. Som en del av skapandet av avbildningen, Image builder distribuerar avbildningen enligt mallen tas bort ytterligare resurser i IT_<DestinationResourceGroup>_<TemplateName> resursgruppen som skapades av processen.


## <a name="permissions"></a>Behörigheter

Om du vill tillåta Azure VM Image Builder att distribuera avbildningar till de hanterade avbildningarna eller en delad avbildningsgalleri, måste du ange ”Bidragsgivar”-behörigheter för tjänsten ”Azure VM Image Builder” (app-ID: cf32a0cc-373c-47c9-9156-0db11f6a6dfc ) på resursgrupper. 

Om du använder en befintlig anpassad hanterad avbildning eller Avbildningsversion måste minst ”läsare” åtkomst till dessa resursgrupper i Azure Image Builder.

Du kan tilldela åtkomst med hjälp av Azure CLI:

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/<distributeResoureGroupName>
```

Om kontot inte finns, som kan innebära att den prenumeration där du lägger till rolltilldelningen ännu inte registrerats för resursprovidern.


## <a name="costs"></a>Kostnader
Vissa beräkning, nätverk och kostnader för lagring när du skapar, att skapa och lagra avbildningar med Azure Image Builder tillkommer. Dessa kostnader liknar kostnaderna för att manuellt skapa anpassade avbildningar. För resurserna debiteras du enligt priserna för Azure. 

Under skapandeprocessen bild filer laddas ned och lagras i den `IT_<DestinationResourceGroup>_<TemplateName>` resursgruppen som medför en liten lagringskostnader. f som du inte vill behålla dessa kan ta bort mallen avbildning efter bild-versionen.
 
Image Builder skapar en virtuell dator med hjälp av en D1v2 VM-storlek och lagring och nätverk behövs för den virtuella datorn. De här resurserna varar under hela skapandeprocessen och kommer att tas bort när Image Builder har slutförts avbildningen skapas. 
 
Azure Image Builder ska distribuera avbildningen till din valda regioner, vilket kan innebära avgifter för utgående trafik via nätverket.
 
## <a name="next-steps"></a>Nästa steg 
 
Om du vill prova Azure Image Builder finns i artiklar för att skapa [Linux](../articles/virtual-machines/linux/image-builder.md) eller [Windows](../articles/virtual-machines/windows/image-builder.md) bilder.
 
 
