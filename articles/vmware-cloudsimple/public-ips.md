---
title: Azure VMware-lösning av CloudSimple-allokerade offentliga IP-adresser
description: Beskriver hur du allokerar offentliga IP-adresser för virtuella datorer i den privata moln miljön
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 565c9ad0fbd37b026a1ba555d83b9032d2efcba4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "77024304"
---
# <a name="allocate-public-ip-addresses-for-private-cloud-environment"></a>Allokera offentliga IP-adresser för privat moln miljö

Öppna fliken offentliga IP-adresser på sidan nätverk för att allokera offentliga IP-adresser för virtuella datorer i din privata moln miljö.

1. [Öppna CloudSimple-portalen](access-cloudsimple-portal.md) och välj **nätverk** på sido menyn.
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
