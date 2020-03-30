---
title: Skapa resurspooler med Azure-prenumerationsmappning
titleSuffix: Azure VMware Solution by CloudSimple
description: Beskriver hur du skapar resurspooler för ditt privata moln via Azure-prenumerationsmappning
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 474ef03d482288b6bf7b5a8b1c224349a8e2d3a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77014971"
---
# <a name="create-resource-pools-for-your-private-cloud-with-azure-subscription-mapping"></a>Skapa resurspooler för ditt privata moln med Azure-prenumerationsmappning
Med Azure-prenumerationsmappning kan du skapa resurspooler för ditt privata moln från de tillgängliga vSphere-resurspoolerna. I CloudSimple-portalen kan du visa och hantera Azure-prenumerationen för dina privata moln.

> [!NOTE]
> Mappning av en resurspool mappar också alla underordnade resurspooler. Det går inte att mappa en överordnad resurspool om några underordnade resurspooler redan är mappade.

1. [Öppna CloudSimple-portalen](access-cloudsimple-portal.md).
2. Öppna sidan **Resurser** och välj **Azure-prenumerationer mappning**.  
3. Klicka på **Redigera Azure-prenumerationsmappning**.  
4. Om du vill mappa tillgängliga resurspooler markerar du dem till vänster och klickar på pilen som är vänd mot höger. 
5. Om du vill ta bort mappningar markerar du dem till höger och klickar på den vänstervända pilen. 

    ![Azure-prenumerationer](media/resources-azure-mapping.png)

6. Klicka på **OK**.
