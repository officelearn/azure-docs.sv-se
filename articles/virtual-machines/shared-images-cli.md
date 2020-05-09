---
title: Skapa delade avbildnings gallerier med Azure CLI
description: I den här artikeln får du lära dig hur du använder Azure CLI för att skapa en delad avbildning av en virtuell dator i Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: d64b5f94dae7ad0213fa231b5603064ad3647da1
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/05/2020
ms.locfileid: "82796699"
---
# <a name="create-a-shared-image-gallery-with-the-azure-cli"></a>Skapa ett galleri för delad avbildning med Azure CLI

Ett [delat bild galleri](./linux/shared-image-galleries.md) fören klar en anpassad bild delning i hela organisationen. Anpassade avbildningar liknar Marketplace-avbildningar, men du skapar dem själv. Anpassade avbildningar kan användas för startkonfigurationer, till exempel förinläsning av program, programkonfigurationer och andra OS-konfigurationer. 

Med galleriet för delade avbildningar kan du dela dina anpassade VM-avbildningar med andra. Välj vilka bilder du vill dela, vilka regioner du vill göra tillgängliga i och vilka du vill dela dem med. 

[!INCLUDE [virtual-machines-common-shared-images-cli](../../includes/virtual-machines-common-shared-images-cli.md)]


## <a name="next-steps"></a>Nästa steg

Skapa en avbildnings version från en [virtuell dator](image-version-vm-cli.md)eller en [hanterad avbildning](image-version-managed-image-cli.md) med hjälp av Azure CLI.

Mer information om delade avbildnings gallerier finns i [översikten](./linux/shared-image-galleries.md). Om du stöter på problem, se [Felsöka delade avbildnings gallerier](troubleshooting-shared-images.md).
