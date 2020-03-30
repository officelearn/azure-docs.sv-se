---
title: Azure VMware-lösning från CloudSimple - Offentlig IP-adress
description: Lär dig mer om offentliga IP-adresser och deras fördelar på Azure VMware Solution by CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 147ab6b5ace4493e1a0b303c320cb2e81d829b29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024984"
---
# <a name="cloudsimple-public-ip-address-overview"></a>Översikt över CloudSimples offentliga IP-adress

En offentlig IP-adress gör att internetresurser kan kommunicera inkommande till privata molnresurser på en privat IP-adress. Den privata IP-adressen är en virtuell dator eller en programvarubelastningsbalanserare på ditt privata moln vCenter. Med den offentliga IP-adressen kan du exponera tjänster som körs på ditt privata moln för internet.

Den offentliga IP-adressen är dedikerad till den privata IP-adressen tills du tar bort tilldelningen. En offentlig IP-adress kan bara tilldelas en privat IP-adress.

En resurs som är associerad med en offentlig IP-adress använder alltid den offentliga IP-adressen för internetåtkomst. Som standard tillåts endast utgående internetåtkomst på en offentlig IP-adress.  Inkommande trafik på den offentliga IP-adressen nekas.  Om du vill tillåta inkommande trafik skapar du en brandväggsregel för den offentliga IP-adressen till den specifika porten.

## <a name="benefits"></a>Fördelar

Med hjälp av en offentlig IP-adress för att kommunicera inkommande ger:

* DDoS-attackskydd (Distributed Denial of Service). Det här skyddet aktiveras automatiskt för den offentliga IP-adressen.
* Trafikövervakning alltid och begränsning i realtid av gemensamma attacker på nätverksnivå. Dessa försvar är samma försvar som används av Microsofts onlinetjänster.
* Hela skalan för Azures globala nätverk. Nätverket kan användas för att distribuera och minska attacktrafik mellan regioner.  

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [fördelar en offentlig IP-adress](public-ips.md)