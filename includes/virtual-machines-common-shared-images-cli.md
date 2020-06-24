---
title: inkludera fil
description: inkludera fil
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/24/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: b49dc6ef2bfee311bc3ca524a5ccb0a4e4b5ca9c
ms.sourcegitcommit: 52d2f06ecec82977a1463d54a9000a68ff26b572
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/15/2020
ms.locfileid: "84793670"
---
## <a name="create-an-image-gallery"></a>Skapa ett bild galleri 

Ett avbildnings galleri är den primära resurs som används för att aktivera avbildnings delning. 

Tillåtna tecken för Galleri namn är versaler eller gemener, siffror, punkter och punkter. Galleri namnet får inte innehålla bindestreck.   Galleri namn måste vara unika inom din prenumeration. 

Skapa ett bild galleri med [AZ sig-Create](/cli/azure/sig#az-sig-create). I följande exempel skapas en resurs grupp med namnet *myGalleryRG* i *USA, östra*och ett galleri som heter *Galleri.*

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="share-the-gallery"></a>Dela galleriet

Du kan dela avbildningar över prenumerationer med hjälp av rollbaserad Access Control (RBAC). Du kan dela bilder i galleriet, bild definitionen eller bild versions nivån. Alla användare som har Läs behörighet till en avbildnings version, även över prenumerationer, kan distribuera en virtuell dator med hjälp av avbildnings versionen.

Vi rekommenderar att du delar med andra användare på Galleri nivån. Om du vill hämta objekt-ID: t för galleriet använder du [AZ sig Visa](/cli/azure/sig#az-sig-show).

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

Använd objekt-ID: t som ett omfång, tillsammans med en e-postadress och [AZ roll tilldelning skapa](/cli/azure/role/assignment#az-role-assignment-create) för att ge en användare åtkomst till det delade avbildnings galleriet. Ersätt `<email-address>` och `<gallery iD>` med din egen information.

```azurecli-interactive
az role assignment create \
   --role "Reader" \
   --assignee <email address> \
   --scope <gallery ID>
```

Mer information om hur du delar resurser med RBAC finns i [Hantera åtkomst med RBAC och Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli).
