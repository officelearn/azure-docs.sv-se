---
title: Skapa resurspooler med Azure-prenumerations mappning
titleSuffix: Azure VMware Solution by CloudSimple
description: Beskriver hur du skapar resurspooler för ditt privata moln via Azure-prenumerations mappning
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 474ef03d482288b6bf7b5a8b1c224349a8e2d3a8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "77014971"
---
# <a name="create-resource-pools-for-your-private-cloud-with-azure-subscription-mapping"></a>Skapa resurspooler för ditt privata moln med Azure-prenumerations mappning
Med mappning av Azure-prenumeration kan du skapa resurspooler för ditt privata moln från tillgängliga vSphere-resurspooler. I CloudSimple-portalen kan du Visa och hantera Azure-prenumerationen för dina privata moln.

> [!NOTE]
> När du mappar en resurspool mappas även eventuella underordnade resurspooler. Det går inte att mappa en överordnad resurspool om några underordnade resurspooler redan har mappats.

1. [Få åtkomst till CloudSimple-portalen](access-cloudsimple-portal.md).
2. Öppna sidan **resurser** och välj **Mappning av Azure-prenumerationer**.  
3. Klicka på **Redigera Azure-prenumerations mappning**.  
4. Om du vill mappa tillgängliga resurspooler väljer du dem till vänster och klickar på pilen till höger. 
5. Om du vill ta bort mappningar markerar du dem till höger och klickar på pilen till vänster. 

    ![Azure-prenumerationer](media/resources-azure-mapping.png)

6. Klicka på **OK**.
