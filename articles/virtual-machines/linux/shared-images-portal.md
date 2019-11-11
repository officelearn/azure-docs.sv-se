---
title: Skapa delade virtuella Azure-dator avbildningar för Linux med hjälp av portalen | Microsoft Docs
description: Lär dig hur du använder Azure Portal för att skapa och dela avbildningar av virtuella datorer.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/06/2019
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: a56b34318725667a9eef143bbf2be90f411b74a1
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904970"
---
# <a name="create-a-shared-image-gallery-using-the-azure-portal"></a>Skapa ett galleri för delad avbildning med hjälp av Azure Portal

Ett [delat bild galleri](shared-image-galleries.md) fören klar en anpassad bild delning i hela organisationen. Anpassade avbildningar liknar Marketplace-avbildningar, men du skapar dem själv. Anpassade avbildningar kan användas för att starta distributions åtgärder som till exempel för att läsa in program, programkonfigurationer och andra OS-konfigurationer. 

Med galleriet för delade avbildningar kan du dela dina anpassade VM-avbildningar med andra i din organisation, inom eller mellan regioner, inom en AAD-klient. Välj vilka bilder du vill dela, vilka regioner du vill göra tillgängliga i och vilka du vill dela dem med. Du kan skapa flera gallerier så att du kan gruppera delade avbildningar logiskt. 

Galleriet är en resurs på den översta nivån som ger fullständig rollbaserad åtkomst kontroll (RBAC). Avbildningar kan vara versioner och du kan välja att replikera varje avbildnings version till en annan uppsättning Azure-regioner. Galleriet fungerar bara med hanterade bilder.

Funktionen för delad bild galleri har flera resurs typer. Vi kommer att använda eller skapa dessa i den här artikeln:

| Resurs | Beskrivning|
|----------|------------|
| **Hanterad avbildning** | En grundläggande bild som kan användas separat eller som används för att skapa en **avbildnings version** i ett bild galleri. Hanterade avbildningar skapas från [generaliserade](shared-image-galleries.md#generalized-and-specialized-images) virtuella datorer. En hanterad avbildning är en särskild typ av virtuell hård disk som kan användas för att skapa flera virtuella datorer och kan nu användas för att skapa delade avbildnings versioner. |
| **Ögonblicks bild** | En kopia av en virtuell hård disk som kan användas för att skapa en **avbildnings version**. Ögonblicks bilder kan hämtas från en [specialiserad](shared-image-galleries.md#generalized-and-specialized-images) virtuell dator (en som inte har generaliserats), sedan används separat eller med ögonblicks bilder av data diskar för att skapa en specialiserad avbildnings version.
| **Bild galleri** | Precis som Azure Marketplace är ett **avbildnings Galleri** en lagrings plats för att hantera och dela bilder, men du styr vem som har åtkomst. |
| **Bild definition** | Avbildningar definieras i ett galleri och bär information om avbildningen och kraven för att använda den i din organisation. Du kan inkludera information, till exempel om avbildningen är generaliserad eller specialiserad, kraven på operativ system, minsta och högsta mängd minne och viktig information. Det är en definition av en typ av bild. |
| **Avbildnings version** | En **avbildnings version** är vad du använder för att skapa en virtuell dator när du använder ett galleri. Du kan ha flera versioner av en avbildning efter behov för din miljö. Som en hanterad avbildning används avbildnings versionen för att skapa nya diskar för den virtuella datorn när du använder en **avbildnings version** för att skapa en virtuell dator. Avbildnings versioner kan användas flera gånger. |

<br>

> [!IMPORTANT]
> Specialiserade avbildningar är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> **Kända för hands versions begränsningar** Virtuella datorer kan bara skapas från specialiserade avbildningar med hjälp av portalen eller API: et. Är inget CLI-eller PowerShell-stöd för förhands granskningen.


## <a name="before-you-begin"></a>Innan du börjar

För att kunna slutföra exemplet i den här artikeln måste du ha en befintlig hanterad avbildning av en generaliserad virtuell dator eller en ögonblicks bild av en specialiserad virtuell dator. Du kan följa [Självstudier: skapa en anpassad avbildning av en virtuell Azure-dator med Azure PowerShell](tutorial-custom-images.md) för att skapa en hanterad avbildning eller [skapa en ögonblicks bild](../windows/snapshot-copy-managed-disk.md) för en specialiserad virtuell dator. Data disk storleken får inte vara större än 1 TB för både hanterade avbildningar och ögonblicks bilder.

När du arbetar med den här artikeln ersätter du resurs gruppen och VM-namnen där det behövs.

 
[!INCLUDE [virtual-machines-common-shared-images-portal](../../../includes/virtual-machines-common-shared-images-portal.md)]

## <a name="create-vms"></a>Skapa VM:ar 

Nu kan du skapa en eller flera nya virtuella datorer. I det här exemplet skapas en virtuell dator med namnet *myVMfromImage*i *myResourceGroup* i data centret *USA, östra* .

1. Gå till din avbildnings definition. Du kan använda resurs filtret för att visa alla tillgängliga avbildnings definitioner.
1. På sidan för din avbildnings definition väljer du **Skapa virtuell dator** på menyn längst upp på sidan.
1. För **resurs grupp**väljer du **Skapa ny** och skriver *myResourceGroup* som namn.
1. Skriv *myVM*i **namn på virtuell dator**.
1. För **region**väljer du *östra USA*.
1. För **tillgänglighets alternativ**lämnar du standardvärdet *ingen redundans krävs*.
1. Värdet för **bild** fylls automatiskt med `latest` avbildnings versionen om du startade från sidan för avbildnings definitionen.
1. I **storlek**väljer du en storlek på virtuell dator i listan över tillgängliga storlekar och väljer sedan **Välj**.
1. Under **administratörs konto**, om den virtuella käll datorn generaliseras, anger du ditt **användar namn** och den **offentliga SSH-nyckeln**. Om den virtuella käll datorn var specialiserad, kommer dessa alternativ att tonas ut eftersom informationen från den virtuella käll datorn används.
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

