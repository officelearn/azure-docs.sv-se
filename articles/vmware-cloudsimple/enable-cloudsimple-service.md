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
ms.openlocfilehash: cacd5510147ce997efec922f4b4656956a098d88
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66676945"
---
# <a name="register-the-microsoftvmwarecloudsimple-resource-provider-on-your-azure-subscription"></a>Registrera resursprovidern Microsoft.VMwareCloudSimple på din Azure-prenumeration

Tjänsten CloudSimple kan du använda Azure VMware-lösning genom CloudSimple. Om du vill använda tjänsten CloudSimple, måste du först aktiverat den på din Azure-prenumeration. Du kan sedan registrera tjänsten Microsoft.VMwareCloudSimple som din resursprovider.

## <a name="enable-the-cloudsimple-service"></a>Aktivera tjänsten CloudSimple

Om du vill aktivera tjänsten CloudSimple på din Azure-prenumeration, öppna en supportbegäran med [Microsoft-supporten](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Välj följande alternativ när du skickar din begäran.

* Typ av problem: **Teknisk**
* Prenumeration: **Ditt prenumerations-ID**
* Tjänstetyp: **VMware-lösning genom CloudSimple**
* Problemtyp: **Dedikerade noder kvot**
* Undertyp av problemet: **Öka kvoten för dedikerade noder**
* Ämne: **Aktivera CloudSimple tjänsten**

Du kan även kontakta din Microsoft-kontorepresentant på [ azurevmwaresales@microsoft.com ](mailto:azurevmwaresales@microsoft.com). Ange ditt Azure-prenumerations-ID för e-postmeddelandet.  

När tjänsten CloudSimple har aktiverats för din prenumeration kan aktivera du resursprovidern för prenumerationen.

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