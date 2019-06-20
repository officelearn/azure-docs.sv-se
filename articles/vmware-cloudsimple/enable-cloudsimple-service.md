---
title: Aktivera Azure VMware-lösningen av CloudSimple-tjänsten
description: Beskriver hur du aktiverar tjänsten CloudSimple på en Azure-prenumeration och sedan registrerar resursprovidern CLoudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2553aa95d5028c510b4e1a1b7f51a9f410bcea51
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/17/2019
ms.locfileid: "67154861"
---
# <a name="register-the-microsoftvmwarecloudsimple-resource-provider-on-your-azure-subscription"></a>Registrera resursprovidern Microsoft.VMwareCloudSimple på din Azure-prenumeration

Tjänsten CloudSimple kan du använda Azure VMware-lösning genom CloudSimple. Du kan registrera tjänsten Microsoft.VMwareCloudSimple som din resursprovider.

## <a name="register-the-resource-provider"></a>Registrera resursprovidern

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Välj **Alla tjänster**.

3. Sök efter och välj **prenumerationer**.

    ![Välj prenumerationer](media/cloudsimple-service-select-subscriptions.png)

4. Välj den prenumeration som du vill aktivera CloudSimple-tjänsten.

5. Klicka på **resursprovidrar** för prenumerationen.

6. Använd **Microsoft.VMwareCloudSimple** att filtrera resursprovidern.

7. Välj resursprovidern och klicka på **registrera**.

    ![Registrera resursprovider](media/cloudsimple-service-enable-resource-provider.png)

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [skapar du en CloudSimple-tjänst](create-cloudsimple-service.md)
* Lär dig hur du [konfigurera en privat molnmiljö](quickstart-create-private-cloud.md)
