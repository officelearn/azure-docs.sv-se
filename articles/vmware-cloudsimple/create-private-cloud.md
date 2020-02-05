---
title: Azure VMware-lösningar (AVS) – skapa ett privat AVS-moln
description: Beskriver hur du skapar ett privat AVS-moln för att utöka VMware-arbetsbelastningar till molnet med drifts flexibilitet och kontinuitet
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 128a0a1eec03878d0deba93048c54324aab7d888
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024797"
---
# <a name="create-an-avs-private-cloud"></a>Skapa ett privat AVS-moln

Ett privat AVS-moln är en isolerad VMware-stack som stöder ESXi-värdar, vCenter, virtuellt San och NSX. Molnets privata moln hanteras via AVS-portalen. De har sina egna vCenter-servrar i sin egen hanterings domän. Stacken körs på dedikerade noder och isolerade Bare Metal-maskinvarukonfigurationer.

Genom att skapa ett privat moln moln kan du hantera olika vanliga behov för nätverks infrastruktur:

* **Tillväxt**. Om du har nått en maskin varu uppdaterings plats för din befintliga infrastruktur kan du med ett privat moln moln expandera utan att behöva ny maskin varu investering.

* **Snabb expansion**. Om det uppstår temporära eller oplanerade kapacitets behov kan du skapa ytterligare kapacitet utan fördröjning i ett privat moln moln.

* **Ökat skydd**. Med ett moln privat moln med tre eller fler noder får du automatisk redundans och skydd med hög tillgänglighet.

* **Långsiktiga infrastruktur behov**. Om dina data Center har kapacitet eller om du vill strukturera om för att sänka dina kostnader kan du dra tillbaka data Center och migrera till en molnbaserad lösning samtidigt som du inte är kompatibel med dina företags åtgärder.

När du skapar ett moln privat moln får du ett enda vSphere-kluster och alla virtuella hanterings datorer som skapas i det klustret.

## <a name="before-you-begin"></a>Innan du börjar

Noder måste vara etablerade innan du kan skapa ditt privata moln i molnet. Mer information om hur du etablerar noder finns i [etablera noder för Azure VMware-lösningar (AVS)](create-nodes.md).

Allokera ett CIDR-intervall för vSphere/virtuellt San-undernät för det privata AVS-molnet. Ett privat AVS-moln skapas som en isolerad VMware stack-miljö (med ESXi-värdar, vCenter, virtuellt San och NSX) som hanteras av en vCenter-Server. Hanterings komponenter distribueras i det nätverk som väljs för vSphere/virtuellt San-undernät CIDR. Nätverks-CIDR-intervallet är indelat i olika undernät under distributionen. Adress utrymmet för vSphere/virtuellt San-under nätet måste vara unikt. Det får inte överlappa något nätverk som kommunicerar med AVS-miljön. De nätverk som kommunicerar med AVS inkluderar lokala nätverk och virtuella Azure-nätverk. Mer information om vSphere/virtuellt San-undernät finns i Översikt över VLAN och undernät.

* Minsta vSphere/virtuellt San-undernät CIDR-intervall prefix:/24
* Maximalt vSphere/virtuellt San-undernät CIDR-intervall prefix:/21


## <a name="access-the-avs-portal"></a>Åtkomst till AVS-portalen

Få åtkomst till [AVS-portalen](access-cloudsimple-portal.md).

## <a name="create-a-new-avs-private-cloud"></a>Skapa ett nytt AVS-privat moln

1. Välj **Alla tjänster**.
2. Sök efter **AVS-tjänster**.
3. Välj den AVS-tjänst som du vill skapa ditt AVS-privata moln på.
4. Från **Översikt**klickar du på **skapa AVS-privat moln** för att öppna en ny flik i webbläsaren för AVS-portalen. Logga in med dina inloggnings uppgifter för Azure om du uppmanas till detta.

    ![Skapa ett privat AVS-moln från Azure](media/create-private-cloud-from-azure.png)

5. I AVS-portalen anger du ett namn för ditt AVS-privata moln.
6. Välj **plats** för ditt AVS-privata moln.
7. Välj **nodtyp**, konsekvent med det du köpte i Azure.
8. Ange **antal noder**. Minst tre noder krävs för att skapa ett privat AVS-moln.
5. I CloudSimple-portalen anger du ett namn för ditt privata moln.
6. Välj **plats** för ditt privata moln.
7. Välj **nodtyp**, konsekvent med det du etablerade i Azure.
8. Ange **antal noder**.  Minst tre noder krävs för att skapa ett privat moln.

    ![Skapa ett privat AVS-moln – grundläggande information](media/create-private-cloud-basic-info.png)

9. Klicka på **Nästa: avancerade alternativ**.
10. Ange CIDR-intervallet för vSphere/virtuellt San-undernät. Se till att CIDR-intervallet inte överlappar något av dina lokala eller andra Azure-undernät (virtuella nätverk) eller med Gateway-undernätet.

    **Alternativ för CIDR-intervall:** /24,/23,/22 eller/21. Ett/24 CIDR-intervall stöder upp till nio noder, ett/23 CIDR-intervall stöder upp till 41 noder och ett/22-och/21 CIDR-intervall stöder upp till 64-noder (det maximala antalet noder i ett moln privat moln).

    > [!IMPORTANT]
    > IP-adresser i CIDR-intervallet vSphere/virtuellt San är reserverade för användning av molnets privata moln infrastruktur. Använd inte IP-adressen i det här intervallet på någon virtuell dator.

11. Klicka på **Nästa: granska och skapa**.
12. Granska inställningarna. Om du behöver ändra några inställningar klickar du på **föregående**.
13. Klicka på **Skapa**.

Etablerings processen för molnets privata moln startar. Det kan ta upp till två timmar för det privata moln molnet att tillhandahållas.

Anvisningar om hur du expanderar ett befintligt moln privat moln finns i avsnittet [expandera ett privat moln i molnet](expand-private-cloud.md).
