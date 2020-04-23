---
title: 'Snabbstart: Skapa VMware CloudSimple-tjänst'
titleSuffix: Azure VMware Solution by CloudSimple
description: Lär dig hur du skapar CloudSimple-tjänsten, köper noder och reserverar noder
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d7c137a75c0a021aa8bca3aec23da6c4d1ada300
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81868022"
---
# <a name="quickstart---create-azure-vmware-solution-by-cloudsimple-service"></a>Snabbstart - Skapa Azure VMware-lösning via CloudSimple-tjänst

För att komma igång, skapa Azure VMware-lösningen av CloudSimple i Azure-portalen.

## <a name="vmware-solution-by-cloudsimple---service-overview"></a>VMware-lösning från CloudSimple - Service översikt

Med CloudSimple-tjänsten kan du använda Azure VMware-lösning från CloudSimple.  Genom att skapa tjänsten kan du etablera noder, reservera noder och skapa privata moln.  Du lägger till CloudSimple-tjänsten i varje Azure-region där CloudSimple-tjänsten är tillgänglig.  Tjänsten definierar edge-nätverket för Azure VMware-lösning av CloudSimple.  Det här edge-nätverket används för tjänster som inkluderar VPN, ExpressRoute och Internet-anslutning till dina privata moln.

Om du vill lägga till CloudSimple-tjänsten måste du skapa ett gateway-undernät. Gateway-undernätet används när edge-nätverket skapas och kräver ett /28 CIDR-block. Gateway-undernätsadressutrymmet måste vara unikt. Den kan inte överlappa med något av dina lokala nätverksadressutrymmen eller Azure-adressutrymme för virtuella nätverk.

## <a name="before-you-begin"></a>Innan du börjar

Allokera ett /28 CIDR-block för gateway-undernät.  Ett gateway-undernät krävs per CloudSimple-tjänst och är unikt för den region där den skapas. Gateway-undernätet används för Azure VMware-lösning av CloudSimple edge-nätverkstjänster och kräver ett /28 CIDR-block. Gateway-undernätsadressutrymmet måste vara unikt. Den får inte överlappa med något nätverk som kommunicerar med CloudSimple-miljön.  De nätverk som kommunicerar med CloudSimple inkluderar lokala nätverk och virtuella Azure-nätverk.

Granska [nätverksförutsättning .](cloudsimple-network-checklist.md) 

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="create-the-service"></a>Skapa tjänsten

1. Välj **Alla tjänster**.
2. Sök efter **CloudSimple Service**.

    ![Sök CloudSimple-tjänst](media/create-cloudsimple-service-search.png)

3. Välj **CloudSimple Services**.
4. Klicka på **Lägg till** om du vill skapa en ny tjänst.

    ![Lägg till CloudSimple-tjänst](media/create-cloudsimple-service-add.png)

5. Välj den prenumeration där du vill skapa CloudSimple-tjänsten.
6. Välj resursgruppen för tjänsten. Om du vill lägga till en ny resursgrupp klickar du på **Skapa ny**.
7. Ange namn för att identifiera tjänsten.
8. Ange CIDR för tjänstgatewayen. Ange ett /28-undernät som inte överlappar något av dina lokala undernät, Azure-undernät eller planerade CloudSimple-undernät. Du kan inte ändra CIDR när tjänsten har skapats.

    ![Skapa CloudSimple-tjänsten](media/create-cloudsimple-service.png)

9. Klicka på **OK**.

Tjänsten skapas och läggs till i listan över tjänster.

## <a name="provision-nodes"></a>Etablera noder

Om du vill konfigurera betalningskapacitet för en CloudSimple Private Cloud-miljö måste du först etablera noder i Azure-portalen.

1. Välj **Alla tjänster**.
2. Sök efter **CloudSimple Noder**.

    ![Sök CloudSimple-noder](media/create-cloudsimple-node-search.png)

3. Välj **CloudSimple-noder**.
4. Klicka på **Lägg till** om du vill skapa noder.

    ![Lägg till CloudSimple-noder](media/create-cloudsimple-node-add.png)

5. Välj den prenumeration där du vill etablera CloudSimple-noder.
6. Välj resursgruppen för noderna. Om du vill lägga till en ny resursgrupp klickar du på **Skapa ny**.
7. Ange prefixet för att identifiera noderna.
8. Välj plats för nodresurserna.
9. Välj den dedikerade platsen som ska vara värd för nodresurserna.
10. Välj [nodtypen](cloudsimple-node.md).
11. Välj antalet noder som ska etableras.
12. Välj **Granska + Skapa**.
13. Granska inställningarna. Om du vill ändra inställningar klickar du på **Föregående**.
14. Välj **Skapa**.

## <a name="next-steps"></a>Nästa steg

* [Skapa privat moln och konfigurera miljö](quickstart-create-private-cloud.md)
* Läs mer om [CloudSimple-tjänsten](https://docs.microsoft.com/azure/vmware-cloudsimple/cloudsimple-service)
