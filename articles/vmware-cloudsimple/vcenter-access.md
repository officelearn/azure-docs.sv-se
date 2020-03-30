---
title: Azure VMware-lösning från CloudSimple - Access vSphere-klient
description: Beskriver hur du kommer åt vCenter i ditt privata moln.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/30/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 18d9463bc512257034860e1188372879524924f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022672"
---
# <a name="access-your-private-cloud-vcenter-portal"></a>Få tillgång till din privata molnportal vCenter

Du kan starta din Private Cloud vCenter-portal från Azure portal eller CloudSimple-portal.  vCenter portal kan du hantera VMware infrastruktur på ditt privata moln.

## <a name="before-you-begin"></a>Innan du börjar

Nätverksanslutningen måste upprättas och DNS-namnmatchning måste vara aktiverad för åtkomst till vCenter-portalen.  Du kan upprätta nätverksanslutning till ditt privata moln med något av alternativen nedan.

* [Ansluta från lokalt till CloudSimple med ExpressRoute](on-premises-connection.md)
* [Konfigurera en VPN-anslutning till ditt CloudSimple Private Cloud](set-up-vpn.md)

Information om hur du konfigurerar DNS-namnmatchning för dina privata VMware-infrastrukturkomponenter i molnet finns i [Konfigurera DNS för namnmatchning för privat moln vCenter-åtkomst från lokala arbetsstationer](on-premises-dns-setup.md)

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure-portalen på [https://portal.azure.com](https://portal.azure.com).

## <a name="access-vcenter-from-azure-portal"></a>Få tillgång till vCenter från Azure-portalen

Du kan starta vCenter-portalen för ditt privata moln från Azure-portalen.

1. Välj **Alla tjänster**.

2. Sök efter **CloudSimple Services**.

3. Välj CloudSimple-tjänsten för ditt privata moln som du vill ansluta till.

4. Klicka på Visa privata moln för **VMware** på sidan **Översikt**

    ![CloudSimple-tjänstöversikt](media/cloudsimple-service-overview.png)

5. Välj det privata molnet i listan över privata moln och klicka på **Starta vSphere Client**.

    ![Starta vSphere-klient](media/cloudsimple-service-launch-vsphere-client.png)

## <a name="access-vcenter-from-cloudsimple-portal"></a>Få tillgång till vCenter från CloudSimple-portalen

Du kan starta vCenter-portalen för ditt privata moln från CloudSimple-portalen.

1. Öppna din [CloudSimple-portal](access-cloudsimple-portal.md).

2. I **resurser** väljer du det privata molnet, som du vill komma åt och klicka på **Starta vSphere Client**.

    ![Starta vSphere Client - Resurser](media/cloudsimple-portal-resources-launch-vcenter.png)

3. Du kan också starta vCenter-portalen från sammanfattningsskärmen för ditt privata moln.

    ![Starta vSphere Client - Sammanfattning](media/cloudsimple-resources-summary-launch-vcenter.png)

## <a name="next-steps"></a>Nästa steg

* [Skapa och hantera VLAN/undernät för dina privata moln](create-vlan-subnet.md)
* [CloudSimple Private Cloud-behörighetsmodell för VMware vCenter](learn-private-cloud-permissions.md)