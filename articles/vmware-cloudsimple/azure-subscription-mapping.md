---
title: Skapa resurspooler med Azure-prenumerations mappning
description: Beskriver hur du skapar resurspooler för ditt privata moln via Azure-prenumerations mappning
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3ea102ad8377da70b88a0e59834ebe3a09866632
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2019
ms.locfileid: "69563212"
---
# <a name="create-resource-pools-for-your-private-cloud-with-azure-subscription-mapping"></a>Skapa resurspooler för ditt privata moln med Azure-prenumerations mappning
Med mappning av Azure-prenumeration kan du skapa resurspooler för ditt privata moln från tillgängliga vSphere-resurspooler. I CloudSimple-portalen kan du Visa och hantera Azure-prenumerationen för dina privata moln.

> [!NOTE]
> När du mappar en resurspool mappas även eventuella underordnade resurspooler. Det går inte att mappa en överordnad resurspool om några underordnade resurspooler redan har mappats.

1. [Få åtkomst till CloudSimple-portalen](access-cloudsimple-portal.md).
2. Öppna sidan **resurser** och välj **Mappning av Azure**-prenumerationer.  
3. Klicka på **Redigera Azure-prenumerations mappning**.  
4. Om du vill mappa tillgängliga resurspooler väljer du dem till vänster och klickar på pilen till höger. 
5. Om du vill ta bort mappningar markerar du dem till höger och klickar på pilen till vänster. 

    ![Azure-prenumerationer](media/resources-azure-mapping.png)

6. Klicka på **OK**.
