---
title: Skapa en Azure VMware-lösning via CloudSimple privat moln
description: Beskriver hur du skapar ett CloudSimple privat moln för att utöka VMware-arbetsbelastningar till molnet med drift flexibilitet och kontinuitet
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 02a2bd311ea1e89a49eb12ef57a167a08eea5f98
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812246"
---
# <a name="create-a-cloudsimple-private-cloud"></a>Skapa ett privat CloudSimple-moln

Genom att skapa ett privat moln kan du hantera en mängd olika vanliga behov för nätverks infrastruktur:

* **Tillväxt**. Om du har nått en plats för maskin varu uppdatering för den befintliga infrastrukturen kan du med ett privat moln utöka utan att behöva ny maskin varu investering.

* **Snabb expansion**. Om det uppstår temporära eller oplanerade kapacitets behov kan du skapa ytterligare kapacitet utan fördröjning i ett privat moln.

* **Ökat skydd**. Med ett privat moln med tre eller fler noder får du automatisk redundans och skydd med hög tillgänglighet.

* **Långsiktiga infrastruktur behov**. Om dina data Center är i kapacitet eller om du vill strukturera om för att sänka kostnaderna, kan du använda ett privat moln för att dra tillbaka data Center och migrera till en molnbaserad lösning, samtidigt som de är kompatibla med dina företags åtgärder.

När du skapar ett privat moln får du ett enda vSphere-kluster och alla virtuella hanterings datorer som skapas i det klustret.

## <a name="before-you-begin"></a>Innan du börjar

Noder måste vara etablerade innan du kan skapa ditt privata moln.  Mer information om hur du etablerar noder finns i [etablera noder för VMware-lösning av CloudSimple-Azure-](create-nodes.md) artikeln.

Allokera ett CIDR-intervall för vSphere/virtuellt San-undernät för det privata molnet. Ett privat moln skapas som en isolerad VMware-stack (ESXi-värdar, vCenter-, virtuellt San-och NSX)-miljö som hanteras av en vCenter-Server. Hanterings komponenter distribueras i det nätverk som valts för vSphere/virtuellt San-undernät CIDR. Nätverks-CIDR-intervallet är indelat i olika undernät under distributionen.  Adress utrymmet för vSphere/virtuellt San-under nätet måste vara unikt. Det får inte överlappa något nätverk som kommunicerar med CloudSimple-miljön.  De nätverk som kommunicerar med CloudSimple omfattar lokala nätverk och virtuella Azure-nätverk.  Mer information om vSphere/virtuellt San-undernät finns i [Översikt över VLAN och undernät](cloudsimple-vlans-subnets.md).

* Minsta vSphere/virtuellt San-undernät CIDR-intervall prefix:/24 
* Maximalt vSphere/virtuellt San-undernät CIDR-intervall prefix:/21

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Få åtkomst till CloudSimple-portalen

Få åtkomst till [CloudSimple-portalen](access-cloudsimple-portal.md).

## <a name="create-a-new-private-cloud"></a>Skapa ett nytt privat moln

1. På sidan **resurser** klickar du på **Nytt privat moln**.

    ![Skapa ett privat moln – så här startar du](media/create-pc-button.png)

2. Välj den plats som ska vara värd för de privata moln resurserna.

3. Välj CS28-eller CS36-nodtyp you'ev som har skapats för det privata molnet. Det senare alternativet inkluderar den maximala beräknings-och minnes kapaciteten.

4. Välj antalet noder för det privata molnet. Du kan välja högst det tillgängliga antalet noder som you'ev har [allokerats](create-nodes.md).

    ![Skapa ett privat moln – grundläggande inställningar](media/create-private-cloud-basic-info.png)

5. Klicka på **Nästa: Avancerade alternativ**.

6. Ange CIDR-intervallet för vSphere/virtuellt San-undernät. Se till att CIDR-intervallet inte överlappar något av dina lokala eller andra Azure-undernät (virtuella nätverk) eller med Gateway-undernätet.  Använd inte något CIDR-intervall som definierats i virtuella Azure-nätverk.
    
    **Alternativ för CIDR-intervall:** /24,/23,/22 eller/21. Ett/24 CIDR-intervall stöder upp till nio noder, ett/23 CIDR-intervall stöder upp till 41 noder och ett/22-och/21 CIDR-intervall stöder upp till 64-noder (det maximala antalet noder i ett privat moln).

    > [!CAUTION]
    > IP-adresser i vSphere/virtuellt San CIDR-intervall är reserverat för användning av privat moln infrastruktur.  Använd inte IP-adressen i det här intervallet på någon virtuell dator.

7. Klicka på **Nästa: Granska och skapa**.

8. Granska inställningarna. Om du behöver ändra några inställningar klickar du på **föregående**.

9. Klicka på **Skapa**.

Etablering av privat moln startar när du klickar på Skapa.  Du kan övervaka förloppet från [aktiviteter](https://docs.azure.cloudsimple.com/activity/#tasks) -sidan på CloudSimple-portalen.  Etableringen kan ta 30 minuter till två timmar.  Du får ett e-postmeddelande när etableringen har slutförts.

## <a name="next-steps"></a>Nästa steg

* [Expandera privat moln](expand-private-cloud.md)