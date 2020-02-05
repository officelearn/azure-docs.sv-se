---
title: Skapa resurspooler med Azure-prenumerations mappning
description: Beskriver hur du skapar resurspooler för ditt AVS-privata moln via Azure-prenumerations mappning
titleSuffix: Azure VMware Solution by CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ab7549650e4e20d27d3ad11a96d77ba943797f88
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2020
ms.locfileid: "77014971"
---
# <a name="create-resource-pools-for-your-avs-private-cloud-with-azure-subscription-mapping"></a>Skapa resurspooler för ditt moln privata moln med Azure-prenumerations mappning
Med mappning av Azure-prenumeration kan du skapa resurspooler för ditt AVS-privata moln från tillgängliga vSphere-resurspooler. I AVS-portalen kan du Visa och hantera Azure-prenumerationen för dina moln privata moln.

> [!NOTE]
> När du mappar en resurspool mappas även eventuella underordnade resurspooler. Det går inte att mappa en överordnad resurspool om några underordnade resurspooler redan har mappats.

1. [Få åtkomst till AVS-portalen](access-cloudsimple-portal.md).
2. Öppna sidan **resurser** och välj **Mappning av Azure-prenumerationer**.  
3. Klicka på **Redigera Azure-prenumerations mappning**.  
4. Om du vill mappa tillgängliga resurspooler väljer du dem till vänster och klickar på pilen till höger. 
5. Om du vill ta bort mappningar markerar du dem till höger och klickar på pilen till vänster. 

    ![Azure-prenumerationer](media/resources-azure-mapping.png)

6. Klicka på **OK**.
