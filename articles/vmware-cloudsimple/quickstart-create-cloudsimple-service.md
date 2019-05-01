---
title: Azure VMware-lösning av CloudSimple Quickstart – skapa tjänst
description: Lär dig hur du skapar tjänsten CloudSimple, köpa noder och reservera noder
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9b3b95db24f4b0f9a0cf8f5102dfeea5dc51e29f
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/26/2019
ms.locfileid: "64577577"
---
# <a name="quickstart---create-service"></a>Snabbstart – skapa tjänst

Kom igång genom att skapa Azure VMware-lösning genom CloudSimple i Azure-portalen.

## <a name="vmware-solution-by-cloudsimple---service-overview"></a>VMware-lösning genom CloudSimple - tjänstöversikt

Tjänsten CloudSimple kan du använda Azure VMware-lösning genom CloudSimple.  Skapandet av tjänsten kan du köpa noder, Reservera noder och skapar privata moln.  Du lägger till tjänsten CloudSimple i varje Azure-region där CloudSimple-tjänsten är tillgänglig.  Tjänsten definierar edge-nätverk med Azure VMware-lösning genom CloudSimple.  Det här Microsoft edge-nätverket används för tjänster som omfattar VPN och ExpressRoute Internet-anslutning till dina privata moln.

Om du vill lägga till tjänsten CloudSimple, måste du skapa ett gateway-undernät. Gateway-undernätet används när du skapar edge-nätverket och kräver en/28 CIDR-block. Adressutrymme för gateway-undernätet måste vara unikt. Det får inte överlappa med några av dina lokala nätverksadressutrymmen eller Azure virtuella nätverkets adressutrymme.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="enable-microsoftvmwarecloudsimple-resource-provider"></a>Enable Microsoft.VMwareCloudSimple resource provider

Följ stegen nedan för att aktivera resursprovidern för CloudSimple-tjänsten.

1. Välj **Alla tjänster**.
2. Sök efter och välj **prenumerationer**.

    ![Välj prenumerationer](media/cloudsimple-service-select-subscriptions.png)

3. Välj den prenumeration som du vill aktivera CloudSimple service
4. Klicka på **resursprovidrar** för prenumerationen
5. Använd **Microsoft.VMwareCloudSimple** att filtrera resursprovidern
6. Välj den **Microsoft.VMwareCloudSimple** resursprovidern och klicka på **registrera**

    ![Registrera resursprovider](media/cloudsimple-service-enable-resource-provider.png)

## <a name="create-the-service"></a>Skapa tjänsten

>[!NOTE]
> CloudSimple tjänsten måste aktiveras på din prenumeration. Om din prenumeration har inte aktiverats, får du ett fel vid försök att skapa tjänsten.  Följ stegen i [aktivera CloudSimple service](https://docs.azure.cloudsimple.com/enable-cloudsimple-service) artikeln om du vill aktivera tjänsten.

1. Välj **Alla tjänster**.
2. Sök efter **CloudSimple Service**.

    ![Search CloudSimple Service](media/create-cloudsimple-service-search.png)

3. Välj **CloudSimple Services**.
4. Klicka på **Lägg till** att skapa en ny tjänst.

    ![Add CloudSimple Service](media/create-cloudsimple-service-add.png)

5. Välj den prenumeration där du vill skapa den CloudSimple-tjänsten.
6. Välj resursgrupp för tjänsten. Lägg till en ny resursgrupp genom att klicka på **Skapa ny**.
7. Ange namn för att identifiera tjänsten.
8. Ange CIDR för service-gateway. Ange en/28 undernät som inte överlappar några av dina lokala undernät, Azure-undernät eller planerade CloudSimple undernät. Du kan inte ändra CIDR när tjänsten har skapats.

    ![Skapa CloudSimple-tjänst](media/create-cloudsimple-service.png)

9. Klicka på **OK**.

Tjänsten skapas och läggs till i listan över tjänster.

## <a name="purchase-nodes"></a>Köpa noder

Om du vill konfigurera betala-som-du gå kapacitet för en CloudSimple privat molnmiljö först etablera noder i Azure-portalen.

1. Välj **Alla tjänster**.
2. Sök efter **CloudSimple noder**.

    ![Sök efter CloudSimple noder](media/create-cloudsimple-node-search.png)

3. Välj **CloudSimple noder**.
4. Klicka på **Lägg till** att skapa noderna.

    ![Add CloudSimple Nodes](media/create-cloudsimple-node-add.png)

5. Välj den prenumeration där du vill köpa CloudSimple noder.
6. Välj resursgrupp för noderna. Lägg till en ny resursgrupp genom att klicka på **Skapa ny**.
7. Ange prefix för att identifiera noderna.
8. Välj plats för nod-resurser.
9. Välj den dedikerade platsen som värd för nod-resurser.
10. Välj nodtyp av. Du kan välja den [CS28 eller CS36](cloudsimple-node.md). Det senare alternativet innehåller maximal kapacitet för beräkning och minne.
11. Välj antalet noder för att etablera.
12. Välj **Granska + Skapa**.
13. Granska inställningarna. Om du vill ändra inställningar klickar du på **föregående**.
14. Välj **Skapa**.

## <a name="next-steps"></a>Nästa steg

* [Skapa privata moln och konfigurera miljön](quickstart-create-private-cloud.md)
* Läs mer om [CloudSimple service](https://docs.azure.cloudsimple.com/cloudsimple-service)
