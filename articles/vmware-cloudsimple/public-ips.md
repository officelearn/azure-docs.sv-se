---
title: Azure VMware-lösning från CloudSimple - Tilldela offentliga IP-adresser
description: Beskriver hur du allokerar offentliga IP-adresser för virtuella datorer i private cloud-miljön
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 565c9ad0fbd37b026a1ba555d83b9032d2efcba4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024304"
---
# <a name="allocate-public-ip-addresses-for-private-cloud-environment"></a>Allokera offentliga IP-adresser för private cloud-miljö

Öppna fliken Offentliga IP-adresser på sidan Nätverk om du vill allokera offentliga IP-adresser för virtuella datorer i din privata molnmiljö.

1. [Öppna CloudSimple-portalen](access-cloudsimple-portal.md) och välj **Nätverk** på sidomenyn.
2. Välj **Offentliga IPs**.
3. Klicka på **Ny offentlig IP**.

    ![Offentlig sida av IPs](media/public-ips-page.png)

4. Ange ett namn för att identifiera IP-adressposten.
5. Behåll standardplatsen.
6. Använd skjutreglaget för att ändra tidsgränsen för inaktiv tid om det behövs.
7. Ange den lokala IP-adress som du vill tilldela en offentlig IP-adress för.
8. Ange ett associerat DNS-namn.
9. Klicka på **Skicka**.

![Tilldela offentliga IPs](media/network-public-ip-allocate.png)

Uppgiften att allokera den offentliga IP-adressen börjar. Du kan kontrollera status för aktiviteten på sidan **Aktivitet > Aktiviteter.** När allokeringen är klar visas den nya transaktionen på sidan Offentliga IPs.
