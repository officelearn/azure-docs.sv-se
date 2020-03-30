---
author: cynthn
ms.author: cynthn
ms.date: 01/23/2020
ms.topic: include
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: 00fc3a01e6f42c2704af9dbc807dce193ff2971c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80117047"
---
Standardiserade vm-avbildningar (Virtual Machine) gör det möjligt för organisationer att migrera till molnet och säkerställa konsekvens i distributionerna. Avbildningar innehåller vanligtvis fördefinierade säkerhets- och konfigurationsinställningar och nödvändig programvara. Ställa in din egen avbildningspipeline kräver tid, infrastruktur och installation, men med Azure VM Image Builder, bara ge en enkel konfiguration som beskriver din avbildning, skicka den till tjänsten och avbildningen är byggd och distribuerad.
 
Med Azure VM Image Builder (Azure Image Builder) kan du börja med en Windows- eller Linux-baserad Azure Marketplace-avbildning, befintliga anpassade avbildningar eller RHEL-ISO (Red Hat Enterprise Linux) och börja lägga till egna anpassningar. Eftersom Image Builder är byggd på [HashiCorp Packer](https://packer.io/)kan du också importera dina befintliga Packer shell provisioner skript. Du kan också ange var du vill att dina avbildningar ska finnas i [Azure Shared Image Gallery](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)som en hanterad avbildning eller en virtuell hårddisk.

> [!IMPORTANT]
> Azure Image Builder är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="preview-features"></a>Förhandsversionsfunktioner

För förhandsgranskningen stöds dessa funktioner:

- Skapa gyllene baslinjeavbildningar, som innehåller dina minsta säkerhets- och företagskonfigurationer, och tillåta avdelningar att anpassa den ytterligare för deras behov.
- Patchning av befintliga bilder, image builder kan du kontinuerligt lapp befintliga anpassade bilder.
- Anslut avbildningsverktyget till dina befintliga virtuella nätverk, så att du kan ansluta till befintliga konfigurationsservrar (DSC, Chef, Puppet etc.), filresurser eller andra dirigerbara servrar/tjänster.
- Integrering med Azure Shared Image Gallery, kan du distribuera, version och skala avbildningar globalt, och ger dig ett avbildningshanteringssystem.
- Integrering med befintliga pipelines för avbildningsversion, anropa bara Image Builder från pipelinen eller använd den enkla förhandsversionen av Azure DevOps-uppgiften.
- Migrera en befintlig pipeline för avbildningsanpassning till Azure. Använd befintliga skript, kommandon och processer för att anpassa bilder.
- Skapande av bilder i VHD-format.
 

## <a name="regions"></a>Regioner
Azure Image Builder Service är tillgänglig för förhandsversion i dessa regioner. Bilder kan distribueras utanför dessa regioner.
- USA, östra
- USA, östra 2
- USA, västra centrala
- USA, västra
- USA, västra 2
- Europa, norra
- Europa, västra

## <a name="os-support"></a>Stöd för operativsystemet
AIB stöder Azure Marketplace base OS-avbildningar:
- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 7,6, 7,7
- CentOS 7,6, 7,7
- SLES 12 SP4
- SLES 15, SLES 15 SP1
- Windows 10 RS5 Enterprise/Professional/Enterprise för virtuellt skrivbord (EVD) 
- Windows 2016
- Windows 2019

RHEL Isos stöd är inaktuell, se malldokumentationen för mer information.

## <a name="how-it-works"></a>Hur det fungerar


![Konceptuell ritning av Azure Image Builder](./media/virtual-machines-image-builder-overview/image-builder.png)

Azure Image Builder är en fullständigt hanterad Azure-tjänst som är tillgänglig för en Azure-resursleverantör. Azure Image Builder-processen har tre huvuddelar: källa, anpassa och distribuera, dessa representeras i en mall. Diagrammet nedan visar komponenterna, med några av deras egenskaper. 
 


**Image Builder-processen** 

![Konceptuell ritning av Azure Image Builder-processen](./media/virtual-machines-image-builder-overview/image-builder-process.png)

1. Skapa bildmallen som en .json-fil. Den här .json-filen innehåller information om bildkällan, anpassningarna och distributionen. Det finns flera exempel i [Azure Image Builder GitHub-databasen](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts).
1. Skicka den till tjänsten, kommer detta att skapa en bildmall artefakt i den resursgrupp du anger. I bakgrunden hämtar Image Builder källbilden eller ISO-filen och skripten efter behov. Dessa lagras i en separat resursgrupp som skapas automatiskt i din\<prenumeration, i formatet:\<IT_ DestinationResourceGroup>_ TemplateName>. 
1. När bildmallen har skapats kan du sedan skapa avbildningen. I bakgrunden image builder använder mallen och källfiler för att skapa en virtuell dator (standardstorlek: Standard_D1_v2), nätverk,\<offentlig IP, NSG\<och lagring i IT_ DestinationResourceGroup>_ TemplateName> resursgrupp.
1. Som en del av bildskapandet distribuerar Image Builder bilden den enligt mallen och\<tar sedan bort ytterligare\<resurser i IT_ DestinationResourceGroup>_ TemplateName> resursgrupp som skapades för processen.


## <a name="permissions"></a>Behörigheter

Om du vill att Azure VM Image Builder ska kunna distribuera avbildningar till antingen hanterade avbildningar eller till ett delat avbildningsgalleri måste du ange bidragsbehörigheter för tjänsten "Azure Virtual Machine Image Builder" (app-ID: cf32a0cc-373c-47c9-9156-0db11f6a6dfc ) på resursgrupperna. 

Om du använder en befintlig anpassad hanterad avbildning eller avbildningsversion behöver Azure Image Builder minst Reader-åtkomst till dessa resursgrupper.

Du kan tilldela åtkomst med Hjälp av Azure CLI:

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/<distributeResoureGroupName>
```

Du kan tilldela åtkomst med PowerShell:

```azurePowerShell-interactive
New-AzRoleAssignment -ObjectId ef511139-6170-438e-a6e1-763dc31bdf74 -Scope /subscriptions/$subscriptionID/resourceGroups/<distributeResoureGroupName> -RoleDefinitionName Contributor
```


Om tjänstkontot inte hittas kan det innebära att prenumerationen där du lägger till rolltilldelningen ännu inte har registrerats för resursleverantören.


## <a name="costs"></a>Kostnader
Du ådrar dig vissa beräknings-, nätverks- och lagringskostnader när du skapar, skapar och lagrar avbildningar med Azure Image Builder. Dessa kostnader liknar kostnaderna för att manuellt skapa anpassade bilder. För resurserna debiteras du enligt dina Azure-priser. 

Under processen för att skapa avbildningen `IT_<DestinationResourceGroup>_<TemplateName>` hämtas och lagras filer i resursgruppen, vilket medför en liten lagringskostnad. Om du inte vill behålla dessa tar du bort **bildmallen** efter bildversionen.
 
Image Builder skapar en virtuell dator med en D1v2 VM-storlek och den lagring och nätverk som behövs för den virtuella datorn. Dessa resurser kommer att pågå under hela byggprocessen och tas bort när Image Builder har skapat bilden. 
 
Azure Image Builder distribuerar avbildningen till dina valda regioner, vilket kan medföra avgifter för utgående nätverk.
 
## <a name="next-steps"></a>Nästa steg 
 
Om du vill prova Azure Image Builder läser du artiklarna för att skapa [Linux-](../articles/virtual-machines/linux/image-builder.md) eller Windows-avbildningar. [Windows](../articles/virtual-machines/windows/image-builder.md)
 
 
