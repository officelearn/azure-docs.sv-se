---
title: Skapa delade Azure Linux VM-avbildningar med hjälp av portalen
description: Lär dig hur du använder Azure-portalen för att skapa och dela Linux-avbildningar för virtuella datorer.
author: cynthn
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: article
ms.workload: infrastructure
ms.date: 11/06/2019
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 74c87d589f1c50551ac5685fe0fa126a82bffbde
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758429"
---
# <a name="create-an-azure-shared-image-gallery-using-the-portal"></a>Skapa ett Azure Shared Image Gallery med hjälp av portalen

Ett [delat bildgalleri](shared-image-galleries.md) förenklar anpassad bilddelning i hela organisationen. Anpassade avbildningar liknar Marketplace-avbildningar, men du skapar dem själv. Anpassade avbildningar kan användas för att bootstrap distributionsuppgifter som förinläsning av program, programkonfigurationer och andra OS-konfigurationer. 

Med det delade bildgalleriet kan du dela dina anpassade VM-avbildningar med andra i organisationen, inom eller mellan regioner, inom en AAD-klientorganisation. Välj vilka bilder du vill dela, vilka regioner du vill göra dem tillgängliga i och vilka du vill dela dem med. Du kan skapa flera gallerier så att du logiskt kan gruppera delade bilder. 

Galleriet är en resurs på den högsta nivån som ger fullständig rollbaserad åtkomstkontroll (RBAC). Avbildningar kan versionsas och du kan välja att replikera varje avbildningsversion till en annan uppsättning Azure-regioner. Galleriet fungerar bara med hanterade bilder.

Funktionen Delat bildgalleri har flera resurstyper. Vi kommer att använda eller bygga dessa i den här artikeln:

| Resurs | Beskrivning|
|----------|------------|
| **Hanterad bild** | En grundläggande bild som kan användas ensam eller användas för att skapa en **bildversion** i ett bildgalleri. Hanterade avbildningar skapas från [generaliserade](shared-image-galleries.md#generalized-and-specialized-images) virtuella datorer. En hanterad avbildning är en speciell typ av virtuell hårddisk som kan användas för att skapa flera virtuella datorer och som nu kan användas för att skapa delade avbildningsversioner. |
| **Ögonblicksbild** | En kopia av en virtuell hårddisk som kan användas för att skapa en **bildversion**. Ögonblicksbilder kan tas från en [specialiserad](shared-image-galleries.md#generalized-and-specialized-images) virtuell dator (en som inte har generaliserats) och sedan användas ensamt eller med ögonblicksbilder av datadiskar, för att skapa en specialiserad avbildningsversion.
| **Bildgalleri** | Precis som Azure Marketplace är ett **avbildningsgalleri** en databas för hantering och delning av avbildningar, men du styr vem som har åtkomst. |
| **Bilddefinition** | Bilder definieras i ett galleri och innehåller information om bilden och krav för att använda den i organisationen. Du kan inkludera information som om avbildningen är generaliserad eller specialiserad, operativsystemet, lägsta och högsta minneskrav och viktig information. Det är en definition av en typ av bild. |
| **Bildversion** | En **bildversion** är vad du använder för att skapa en virtuell dator när du använder ett galleri. Du kan ha flera versioner av en avbildning efter behov för din miljö. Precis som en hanterad avbildning används avbildningsversionen när du använder en **avbildningsversion** för att skapa en virtuell dator för att skapa nya diskar för den virtuella datorn. Bildversioner kan användas flera gånger. |

<br>

> [!IMPORTANT]
> Specialiserade bilder är för närvarande i offentlig förhandsgranskning.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> **Kända begränsningar för förhandsgranskning** Virtuella datorer kan bara skapas från specialiserade avbildningar med hjälp av portalen eller API:et. Det finns inget CLI- eller PowerShell-stöd för förhandsversionen.


## <a name="before-you-begin"></a>Innan du börjar

Om du vill slutföra exemplet i den här artikeln måste du ha en befintlig hanterad avbildning av en generaliserad virtuell dator eller en ögonblicksbild av en specialiserad virtuell dator. Du kan följa [självstudiekurs: Skapa en anpassad avbildning av en Virtuell Azure-dator med Azure PowerShell](tutorial-custom-images.md) för att skapa en hanterad avbildning eller [Skapa en ögonblicksbild](../windows/snapshot-copy-managed-disk.md) för en specialiserad virtuell dator. För både hanterade avbildningar och ögonblicksbilder kan datadiskstorleken inte vara mer än 1 TB.

När du arbetar med den här artikeln ersätter du resursgruppen och VM-namnen där det behövs.

 
[!INCLUDE [virtual-machines-common-shared-images-portal](../../../includes/virtual-machines-common-shared-images-portal.md)]

## <a name="create-vms"></a>Skapa VM:ar 

Nu kan du skapa en eller flera nya virtuella datorer. I det här exemplet skapas en virtuell dator med namnet *myVMfromImage*i *myResourceGroup* i det *östra amerikanska* datacentret.

1. Gå till din bilddefinition. Du kan använda resursfiltret för att visa alla tillgängliga bilddefinitioner.
1. På sidan för bilddefinitionen väljer du **Skapa virtuell dator** högst upp på sidan.
1. För **resursgrupp**väljer du **Skapa ny** och skriv *myResourceGroup* för namnet.
1. Skriv *myVM* **i namnet Virtual Machine**.
1. För **Region**väljer du *Östra USA*.
1. För **tillgänglighetsalternativ**lämnar du standardinställningen *Ingen infrastrukturredundans krävs*.
1. Värdet för **Bild** fylls automatiskt `latest` med bildversionen om du startade från sidan för bilddefinitionen.
1. För **Storlek**väljer du en vm-storlek i listan över tillgängliga storlekar och väljer sedan **Välj**.
1. Om käll-VM var generaliserat anger du den offentliga nyckeln Användarnamn och **SSH**under **Administratörskonto** . **Administrator account** Om källdatorn var specialiserad kommer dessa alternativ att vara nedtonade eftersom informationen från källdatorns virtuella dator används.
1. Om du vill tillåta fjärråtkomst till den virtuella datorn väljer du **Tillåt valda portar** under **Offentliga inkommande portar**och väljer sedan **SSH (22)** i listrutan. Om du inte vill tillåta fjärråtkomst till den virtuella datorn lämnar du **Ingen** markerad för **inkommande inkommande portar**.
1. När du är klar väljer du knappen **Granska + skapa** längst ned på sidan.
1. När den virtuella datorn har godkänts väljer du **Skapa** längst ned på sidan för att starta distributionen.


## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort resursgruppen, den virtuella datorn och alla relaterade resurser när de inte längre behövs. Om du vill göra detta väljer du resursgruppen för den virtuella datorn. Välj sedan **Ta bort** och kontrollera namnet på resursgruppen som du vill ta bort.

Om du vill ta bort enskilda resurser måste du ta bort dem i omvänd ordning. Om du till exempel vill ta bort en bilddefinition måste du ta bort alla bildversioner som skapats från den bilden.

## <a name="next-steps"></a>Nästa steg

Du kan också skapa resurs för delat bildgalleri med hjälp av mallar. Det finns flera Azure Quickstart-mallar tillgängliga: 

- [Skapa ett delat bildgalleri](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Skapa en bilddefinition i ett delat bildgalleri](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Skapa en bildversion i ett delat bildgalleri](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Skapa en virtuell dator från avbildningsversion](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Mer information om delade bildgallerier finns i [översikten](shared-image-galleries.md). Om du stöter på problem läser du [Felsöka delade bildgallerier](troubleshooting-shared-images.md).

