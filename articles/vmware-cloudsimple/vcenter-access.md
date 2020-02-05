---
title: Azure VMware-lösningar (AVS) – Access vSphere-klient
description: Beskriver hur du kommer åt vCenter från ditt moln privata moln.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/30/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ad018ea89b194d42ab1867a0569725c4c3680f7d
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2020
ms.locfileid: "77022672"
---
# <a name="access-your-avs-private-cloud-vcenter-portal"></a>Få åtkomst till din Cloud privat Cloud vCenter-Portal

Du kan starta din Clouds vCenter-Portal för privata moln från Azure Portal eller AVS-portalen. med vCenter-portalen kan du hantera VMware-infrastrukturen i ditt AVS-moln.

## <a name="before-you-begin"></a>Innan du börjar

Nätverks anslutning måste upprättas och DNS-namnmatchning måste aktive ras för åtkomst till vCenter-portalen. Du kan upprätta en nätverks anslutning till ditt moln privata moln med något av alternativen nedan.

* [Ansluta från lokalt till AVS med ExpressRoute](on-premises-connection.md)
* [Konfigurera en VPN-anslutning till ditt moln privata moln](set-up-vpn.md)

Information om hur du konfigurerar DNS-namnmatchning för ditt molns lokala VMware-infrastruktur komponenter finns i [Konfigurera DNS för namn matchning för molnets privata åtkomst till lokala arbets stationer](on-premises-dns-setup.md)

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="access-vcenter-from-azure-portal"></a>Anslut till vCenter från Azure Portal

Du kan starta vCenter-portalen för ditt AVS-privata moln från Azure Portal.

1. Välj **Alla tjänster**.

2. Sök efter **AVS-tjänster**.

3. Välj den AVS-tjänst för ditt moln-privata moln som du vill ansluta till.

4. På sidan **Översikt** klickar du på **Visa privata moln i VMware AVS**

    ![Översikt över AVS-tjänsten](media/cloudsimple-service-overview.png)

5. Välj det privata AVS-molnet i listan över AVS-privata moln och klicka på **Starta vSphere-klient**.

    ![Starta vSphere-klienten](media/cloudsimple-service-launch-vsphere-client.png)

## <a name="access-vcenter-from-avs-portal"></a>Åtkomst till vCenter från AVS-portalen

Du kan starta vCenter-portalen för ditt AVS-moln i molnet från AVS-portalen.

1. Få åtkomst till din [AVS-Portal](access-cloudsimple-portal.md).

2. Från **resurserna** väljer du det molnbaserade privata molnet som du vill komma åt och klickar på **Starta vSphere-klient**.

    ![Starta vSphere-klient – resurser](media/cloudsimple-portal-resources-launch-vcenter.png)

3. Du kan också starta vCenter-portalen från sammanfattnings skärmen i ditt AVS-moln.

    ![Starta vSphere-klient – Sammanfattning](media/cloudsimple-resources-summary-launch-vcenter.png)

## <a name="next-steps"></a>Nästa steg

* [Skapa och hantera VLAN/undernät för dina moln privata moln](create-vlan-subnet.md)
* [Behörighets modell för molnets privata moln i VMware vCenter](learn-private-cloud-permissions.md)