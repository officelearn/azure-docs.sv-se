---
title: Azure VMware-lösningar (AVS) – allokera offentliga IP-adresser
description: Beskriver hur du allokerar offentliga IP-adresser för virtuella datorer i molnets privata moln miljö
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 87133f5efb9f096d3fdb0956aab1caac58b4bd94
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024304"
---
# <a name="allocate-public-ip-addresses-for-avs-private-cloud-environment"></a>Allokera offentliga IP-adresser för molnets privata moln miljö

Öppna fliken offentliga IP-adresser på sidan nätverk för att allokera offentliga IP-adresser för virtuella datorer i din AVS-miljö för privata moln.

1. [Gå till AVS-portalen](access-cloudsimple-portal.md) och välj **nätverk** på sido menyn.
2. Välj **offentliga IP-adresser**.
3. Klicka på **ny offentlig IP-adress**.

    ![Sidan offentliga IP-adresser](media/public-ips-page.png)

4. Ange ett namn för att identifiera IP-adress posten.
5. Behåll standard platsen.
6. Använd skjutreglaget för att ändra tids gränsen för inaktivitet, om det behövs.
7. Ange den lokala IP-adress som du vill tilldela en offentlig IP-adress för.
8. Ange ett associerat DNS-namn.
9. Klicka på **Skicka**.

![Allokera offentliga IP-adresser](media/network-public-ip-allocate.png)

Uppgiften att allokera den offentliga IP-adressen börjar. Du kan kontrol lera status för aktiviteten på sidan **aktiviteter > uppgifter** . När allokeringen är klar visas den nya posten på sidan offentliga IP-adresser.
