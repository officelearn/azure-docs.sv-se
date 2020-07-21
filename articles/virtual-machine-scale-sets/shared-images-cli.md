---
title: Använd delade VM-avbildningar för att skapa en skalnings uppsättning i Azure CLI
description: Lär dig hur du använder Azure CLI för att skapa delade VM-avbildningar som ska användas för att distribuera skalnings uppsättningar för virtuella datorer i Azure.
author: axayjo
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: akjosh
ms.reviewer: cynthn
ms.custom: ''
ms.openlocfilehash: cdcd11b6bd9c773e8018ee303564fff6abd098a6
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86494912"
---
# <a name="create-and-use-shared-images-for-virtual-machine-scale-sets-with-the-azure-cli-20"></a>Skapa och Använd delade avbildningar för skalnings uppsättningar för virtuella datorer med Azure CLI 2,0

När du skapar en skalningsuppsättning, kan du ange en avbildning som ska användas när de virtuella datorinstanserna distribueras. Ett [delat bild galleri](shared-image-galleries.md) fören klar en anpassad bild delning i hela organisationen. Anpassade avbildningar liknar Marketplace-avbildningar, men du skapar dem själv. Anpassade avbildningar kan användas för startkonfigurationer, till exempel förinläsning av program, programkonfigurationer och andra OS-konfigurationer. 

Med galleriet för delade avbildningar kan du dela dina avbildningar med andra. Välj vilka bilder du vill dela, vilka regioner du vill göra tillgängliga i och vilka du vill dela dem med. 


[!INCLUDE [virtual-machines-common-shared-images-cli](../../includes/virtual-machines-common-shared-images-cli.md)]


## <a name="next-steps"></a>Nästa steg

Skapa en avbildnings version från en [virtuell dator](../virtual-machines/image-version-vm-cli.md)eller en [hanterad avbildning](../virtual-machines/image-version-managed-image-cli.md).

Mer information om delade avbildnings gallerier finns i [översikten](shared-image-galleries.md). Om du stöter på problem, se [Felsöka delade avbildnings gallerier](troubleshooting-shared-images.md).
