---
title: Skapa delade virtuella Azure-dator avbildningar för Linux med hjälp av portalen | Microsoft Docs
description: Lär dig hur du använder Azure Portal för att skapa och dela avbildningar av virtuella datorer.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/27/2019
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: 8be4890f01ae2c0d893bb7c45f29c6f8178844f9
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70082118"
---
# <a name="create-a-shared-image-gallery-using-the-azure-portal"></a>Skapa ett galleri för delad avbildning med hjälp av Azure Portal

Ett [delat bild galleri](shared-image-galleries.md) fören klar en anpassad bild delning i hela organisationen. Anpassade avbildningar liknar Marketplace-avbildningar, men du skapar dem själv. Anpassade avbildningar kan användas för att starta distributions åtgärder som till exempel för att läsa in program, programkonfigurationer och andra OS-konfigurationer. 

Med galleriet för delade avbildningar kan du dela dina anpassade VM-avbildningar med andra i din organisation, inom eller mellan regioner, inom en AAD-klient. Välj vilka bilder du vill dela, vilka regioner du vill göra tillgängliga i och vilka du vill dela dem med. Du kan skapa flera gallerier så att du kan gruppera delade avbildningar logiskt. 

Galleriet är en resurs på den översta nivån som ger fullständig rollbaserad åtkomst kontroll (RBAC). Avbildningar kan vara versioner och du kan välja att replikera varje avbildnings version till en annan uppsättning Azure-regioner. Galleriet fungerar bara med hanterade bilder.

Funktionen för delad bild galleri har flera resurs typer. Vi kommer att använda eller skapa dessa i den här artikeln:

| Resource | Beskrivning|
|----------|------------|
| **Hanterad avbildning** | Detta är en grundläggande bild som kan användas separat eller användas för att skapa en **avbildnings version** i ett bild galleri. Hanterade avbildningar skapas från generaliserade virtuella datorer. En hanterad avbildning är en särskild typ av virtuell hård disk som kan användas för att skapa flera virtuella datorer och kan nu användas för att skapa delade avbildnings versioner. |
| **Bild galleri** | Precis som Azure Marketplace är ett **avbildnings Galleri** en lagrings plats för att hantera och dela bilder, men du styr vem som har åtkomst. |
| **Bild definition** | Avbildningar definieras i ett galleri och bär information om avbildningen och kraven för att använda den internt. Detta inkluderar om avbildningen är Windows eller Linux, viktig information och minimi-och högsta minnes krav. Det är en definition av en typ av bild. |
| **Avbildnings version** | En **avbildnings version** är vad du använder för att skapa en virtuell dator när du använder ett galleri. Du kan ha flera versioner av en avbildning efter behov för din miljö. Som en hanterad avbildning används avbildnings versionen för att skapa nya diskar för den virtuella datorn när du använder en **avbildnings version** för att skapa en virtuell dator. Avbildnings versioner kan användas flera gånger. |


## <a name="before-you-begin"></a>Innan du börjar

Du måste ha en befintlig hanterad avbildning för att kunna slutföra exemplet i den här artikeln. Du kan följa [själv studie kursen: Skapa en anpassad avbildning av en virtuell Azure-dator](tutorial-custom-images.md) med Azure PowerShell för att skapa en om det behövs. Om den hanterade avbildningen innehåller en datadisk får data disk storleken inte vara större än 1 TB.

När du arbetar med den här artikeln ersätter du resurs gruppen och VM-namnen där det behövs.

 
[!INCLUDE [virtual-machines-common-shared-images-portal](../../../includes/virtual-machines-common-shared-images-portal.md)]

## <a name="create-vms-from-an-image"></a>Skapa virtuella datorer från en avbildning

När avbildnings versionen är klar kan du skapa en eller flera nya virtuella datorer. 

> [!IMPORTANT]
> Du kan inte använda portalen för att distribuera en virtuell dator från en avbildning i en annan Azure-klient. Om du vill skapa en virtuell dator från en avbildning som delas mellan klienter, måste du använda [Azure CLI](shared-images.md#create-a-vm) eller [PowerShell](../windows/shared-images.md#create-vms-from-an-image).


I det här exemplet skapas en virtuell dator med namnet *myVMfromImage*i *myResourceGroup* i data centret *USA, östra* .

1. På sidan för din avbildnings version väljer du **Skapa virtuell dator** på menyn längst upp på sidan.
1. För **resurs grupp**väljer du **Skapa ny** och skriver *myResourceGroup* som namn.
1. Skriv *myVM*i **namn på virtuell dator**.
1. För **region**väljer du *östra USA*.
1. För **tillgänglighets alternativ**lämnar du standardvärdet *ingen redundans krävs*.
1. Värdet för **avbildningen** bör fyllas i automatiskt om du startade från sidan för avbildnings versionen.
1. I **storlek**väljer du en storlek på virtuell dator i listan över tillgängliga storlekar och klickar sedan på Välj.
1. Under **administratörs konto**väljer du **lösen ord** eller **Offentlig SSH-nyckel**och anger sedan din information.
1. Om du vill tillåta fjärråtkomst till den virtuella datorn under **offentliga inkommande portar**väljer du **Tillåt valda portar** och väljer **SSH (22)** i list rutan. Om du inte vill tillåta fjärråtkomst till den virtuella datorn lämnar du **inget** markerat för **offentliga inkommande portar**.
1. När du är färdig väljer du knappen **Granska + skapa** längst ned på sidan.
1. När den virtuella datorn har godkänts för validering väljer du **skapa** längst ned på sidan för att starta distributionen.


## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort resursgruppen, den virtuella datorn och alla relaterade resurser när de inte längre behövs. Om du vill göra detta väljer du resursgruppen för den virtuella datorn. Välj sedan **Ta bort** och kontrollera namnet på resursgruppen som du vill ta bort.

Om du vill ta bort enskilda resurser måste du ta bort dem i omvänd ordning. Om du till exempel vill ta bort en avbildnings definition måste du ta bort alla avbildnings versioner som skapats från avbildningen.

## <a name="next-steps"></a>Nästa steg

Du kan också skapa en delad resurs för avbildnings galleriet med hjälp av mallar. Det finns flera tillgängliga Azure snabb starts mallar: 

- [Skapa ett galleri för delad avbildning](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Skapa en avbildnings definition i ett galleri för delade avbildningar](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Skapa en avbildnings version i ett galleri för delad avbildning](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Skapa en virtuell dator från avbildnings version](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Mer information om delade avbildnings gallerier finns i [översikten](shared-image-galleries.md). Om du stöter på problem, se [Felsöka delade avbildnings gallerier](troubleshooting-shared-images.md).

