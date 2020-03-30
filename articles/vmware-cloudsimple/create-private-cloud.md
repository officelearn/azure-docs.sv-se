---
title: Azure VMware-lösning från CloudSimple - Skapa CloudSimple Private Cloud
description: Beskriver hur du skapar ett CloudSimple Private Cloud för att utöka VMware-arbetsbelastningar till molnet med operativ flexibilitet och kontinuitet
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4f700ac34b6c6e2a651366bee7dd1785c608064f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024797"
---
# <a name="create-a-cloudsimple-private-cloud"></a>Skapa ett cloudsimple privat moln

Ett privat moln är en isolerad VMware-stack som stöder ESXi-värdar, vCenter, vSAN och NSX. Privata moln hanteras via CloudSimple-portalen. De har sin egen vCenter-server i sin egen hanteringsdomän. Stacken körs på dedikerade noder och isolerade bare metal-maskinvarunoder.

Genom att skapa ett privat moln kan du tillgodose en mängd vanliga behov för nätverksinfrastruktur:

* **Tillväxt**. Om du har nått en uppdateringspunkt för maskinvara för din befintliga infrastruktur kan du med ett privat moln expandera utan att någon ny maskinvaruinvestering krävs.

* **Snabb expansion**. Om någon tillfällig eller oplanerad kapacitet behöver uppstå, kan du med ett privat moln skapa ytterligare kapacitet utan fördröjning.

* **Ökat skydd**. Med ett privat moln med tre eller fler noder får du automatisk redundans och skydd mot hög tillgänglighet.

* **Långsiktiga infrastrukturbehov**. Om dina datacenter är på kapacitet eller om du vill omstrukturera för att sänka dina kostnader, kan du med ett privat moln dra tillbaka datacenter och migrera till en molnbaserad lösning samtidigt som du förblir kompatibel med företagets verksamhet.

När du skapar ett privat moln får du ett enda vSphere-kluster och alla virtuella hanterings-datorer som skapas i klustret.

## <a name="before-you-begin"></a>Innan du börjar

Noder måste etableras innan du kan skapa ditt privata moln. Mer information om etablering av noder finns i [Etableringsnoder för Azure VMware-lösning av CloudSimple](create-nodes.md).

Allokera ett CIDR-intervall för vSphere/vSAN-undernät för det privata molnet. Ett privat moln skapas som en isolerad VMware-stackmiljö (med ESXi-värdar, vCenter, vSAN och NSX) som hanteras av en vCenter-server. Hanteringskomponenter distribueras i nätverket som har valts för vSphere/vSAN-undernät CIDR. Nätverkets CIDR-område är indelat i olika undernät under distributionen. Adressutrymmet vSphere/vSAN måste vara unikt. Den får inte överlappa med något nätverk som kommunicerar med CloudSimple-miljön. De nätverk som kommunicerar med CloudSimple inkluderar lokala nätverk och virtuella Azure-nätverk. Mer information om vSphere/vSAN-undernät finns i VLAN och undernätsöversikt.

* Lägsta vSphere/vSAN-undernät CIDR-intervallprefix: /24
* Högsta vSphere/vSAN-undernät CIDR-intervallprefix: /21


## <a name="access-the-cloudsimple-portal"></a>Få åtkomst till CloudSimple-portalen

Öppna [CloudSimple-portalen](access-cloudsimple-portal.md).

## <a name="create-a-new-private-cloud"></a>Skapa ett nytt privat moln

1. Välj **Alla tjänster**.
2. Sök efter **CloudSimple Services**.
3. Välj den CloudSimple-tjänst som du vill skapa ditt privata moln på.
4. Klicka på **Skapa privat moln i** **översikten**om du vill öppna en ny webbläsarflik för CloudSimple-portalen. Om du uppmanas till det loggar du in med dina Azure-inloggningsuppgifter.

    ![Skapa privat moln från Azure](media/create-private-cloud-from-azure.png)

5. Ange ett namn för ditt privata moln i CloudSimple-portalen.
6. Välj **Plats** för ditt privata moln.
7. Välj **Nodtyp**, i enlighet med vad du etablerat på Azure.
8. Ange **antal nod**.  Minst tre noder krävs för att skapa ett privat moln.

    ![Skapa privat moln – grundläggande information](media/create-private-cloud-basic-info.png)

9. Klicka på **Nästa: Avancerade alternativ**.
10. Ange CIDR-intervallet för vSphere/vSAN-undernät. Kontrollera att CIDR-intervallet inte överlappar något av dina lokala eller andra Azure-undernät (virtuella nätverk) eller med gateway-undernätet.

    **CIDR-intervallalternativ:** /24, /23, /22 eller /21. Ett /24 CIDR-intervall stöder upp till nio noder, ett /23 CIDR-intervall stöder upp till 41 noder och ett /22 och /21 CIDR-intervall stöder upp till 64 noder (det maximala antalet noder i ett privat moln).

    > [!IMPORTANT]
    > IP-adresser i vSphere/vSAN CIDR-intervallet är reserverade för användning av infrastrukturen Private Cloud.  Använd inte IP-adressen i det här intervallet på en virtuell dator.

11. Klicka på **Nästa: Granska och skapa**.
12. Granska inställningarna. Om du behöver ändra några inställningar klickar du på **Föregående**.
13. Klicka på **Skapa**.

Etableringsprocessen för privata moln startar. Det kan ta upp till två timmar innan det privata molnet etableras.

Instruktioner om hur du expanderar ett befintligt privat moln finns i [Expandera ett privat moln](expand-private-cloud.md).
