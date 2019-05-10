---
title: Skapa delade Azure VM-avbildningar för Windows med hjälp av portalen | Microsoft Docs
description: Lär dig hur du använder Azure-portalen för att skapa och dela avbildningar av virtuella datorer.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/06/2019
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: d46c545db9e1950988b49cdb577d074b6d04380c
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2019
ms.locfileid: "65236592"
---
# <a name="create-a-shared-image-gallery-using-the-azure-portal"></a>Skapa en delad bildgalleriet med Azure portal

En [delad bildgalleriet](shared-image-galleries.md) förenklar anpassad avbildning delning i hela organisationen. Anpassade avbildningar liknar Marketplace-avbildningar, men du skapar dem själv. Anpassade avbildningar kan användas för att kunna starta distributionsåtgärder som förinläsning av program, programkonfigurationer och andra OS-konfigurationer. 

Delad galleriet kan du dela din anpassade VM-avbildningar med andra i din organisation, inom eller mellan regioner inom en AAD-klient. Välj vilka bilder som du vill dela, vilka regioner som du vill göra dem tillgängliga i och som du vill dela dem med. Du kan skapa flera gallerier så att du kan gruppera delade avbildningar. 

Galleriet är en resurs på toppnivå som ger fullständig rollbaserad åtkomstkontroll (RBAC). Bilder kan versionshanteras och du kan välja att replikera varje versionsnumret för avbildningen till en annan uppsättning Azure-regioner. Galleriet fungerar bara med hanterade avbildningar.

Delad bildgalleriet-funktionen har flera resurstyper. Vi ska använda eller att skapa dessa i den här artikeln:

| Resource | Beskrivning|
|----------|------------|
| **Hanterad avbildning** | Det här är en grundläggande avbildning som kan användas fristående eller används för att skapa en **Avbildningsversion** i ett galleri med avbildningar. Hanterade avbildningarna skapas från generaliserad virtuella datorer. En hanterad avbildning är en särskild typ av virtuell Hårddisk som kan användas för att göra flera virtuella datorer och kan nu användas för att skapa delade bild versioner. |
| **Bildgalleri** | Som Azure Marketplace, en **bildgalleriet** är en lagringsplats för att hantera och dela bilder, men du bestämmer vem som har åtkomst. |
| **Avbildningsdefinitionen** | Bilder har definierats i ett galleri och utföra information om avbildningen och krav för att använda det internt. Detta inkluderar om avbildningen är Windows eller Linux, viktig information och lägsta och högsta minneskrav. Det är en definition av en typ av bild. |
| **Avbildningsversion** | En **Avbildningsversion** är det du använder för att skapa en virtuell dator när du använder ett galleri. Du kan ha flera versioner av en avbildning som behövs för din miljö. Som en hanterad avbildning när du använder en **Avbildningsversion** för att skapa en virtuell dator, versionsnumret för avbildningen som används för att skapa nya diskar för den virtuella datorn. Bild-versioner kan användas flera gånger. |


## <a name="before-you-begin"></a>Innan du börjar

Du måste ha en befintlig hanterad avbildning för att utföra exemplet i den här artikeln. Du kan följa [självstudien: Skapa en anpassad avbildning av en Azure-dator med Azure PowerShell](tutorial-custom-images.md) att skapa en om det behövs. Om hanterad avbildning innehåller en datadisk, får inte datadiskstorleken vara mer än 1 TB.

När du börjar med den här artikeln, Ersätt namnges resursgrupp och virtuell dator där det behövs.


[!INCLUDE [virtual-machines-common-shared-images-portal](../../../includes/virtual-machines-common-shared-images-portal.md)]
 
## <a name="create-vms-from-an-image"></a>Skapa virtuella datorer från en avbildning

När versionsnumret för avbildningen är klar kan skapa du en eller flera nya virtuella datorer. 

Det här exemplet skapar en virtuell dator med namnet *myVMfromImage*i den *myResourceGroup* i den *USA, östra* datacenter.

1. På sidan för din Avbildningsversion väljer **Create VM** på menyn längst upp på sidan.
1. För **resursgrupp**väljer **Skapa nytt** och skriv *myResourceGroup* för namnet.
1. I **virtuellt datornamn**, typ *myVM*.
1. För **Region**väljer *USA, östra*.
1. För **tillgänglighetsalternativ**, låt standardvärdet *ingen redundans för infrastruktur som krävs för*.
1. Värdet för **bild** bör fyllas i automatiskt om du utgick från sidan för versionsnumret för avbildningen.
1. För **storlek**, välja en VM-storlek i listan över tillgängliga storlekar och klickar på ”Välj”.
1. Under **Administratörskonto**, anger du ett användarnamn, som *azureuser*, och ett lösenord. Lösenordet måste vara minst 12 tecken långt och uppfylla [de definierade kraven på komplexitet](faq.md#what-are-the-password-requirements-when-creating-a-vm).
1. Om du vill tillåta fjärråtkomst till den virtuella datorn under **offentliga inkommande portar**, Välj **valda portar** och välj sedan **RDP (port 3389)** från listrutan. Om du inte vill tillåta fjärråtkomst till den virtuella datorn, lämnar **ingen** markerats för **offentliga inkommande portar**.
1. När du är klar väljer du den **granska + skapa** längst ned på sidan.
1. När den virtuella datorn har klarat verifieringen väljer **skapa** längst ned på sidan för att starta distributionen.


## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort resursgruppen, den virtuella datorn och alla relaterade resurser när de inte längre behövs. Om du vill göra detta väljer du resursgruppen för den virtuella datorn. Välj sedan **Ta bort** och kontrollera namnet på resursgruppen som du vill ta bort.

Om du vill ta bort enskilda resurser, måste du ta bort dem i omvänd ordning. Exempelvis om du vill ta bort definitionen för en bild, måste du ta bort alla bild-versioner som skapats från avbildningen.

## <a name="next-steps"></a>Nästa steg

Du kan också skapa delade bildgalleriet resursen med hjälp av mallar. Det finns flera Azure-Snabbstartsmallar: 

- [Skapa en delad bildgalleri](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Skapa en Definition för bilden i en delad Avbildningsgalleri](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Skapa en Bildversion i en delad bildgalleri](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Skapa en virtuell dator från Avbildningsversion](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Mer information om delade Image Galleries finns i den [översikt](shared-image-galleries.md). Om du stöter på problem, se [felsökning delade bildgallerier](troubleshooting-shared-images.md).

