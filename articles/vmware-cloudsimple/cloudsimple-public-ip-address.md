---
title: VMware-lösning av CloudSimple – offentlig IP-adress för Azure
description: Lär dig mer om offentliga IP-adresser och deras fördelar med VMware-lösningen av CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 29d2bdb75a7f4409e6b3ffee2608cfe7adde6b1f
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812534"
---
# <a name="cloudsimple-public-ip-address-overview"></a>Översikt över CloudSimple offentlig IP-adress

En offentlig IP-adress gör det möjligt för Internet resurser att kommunicera inkommande, till privata moln resurser på en privat IP-adress. Den privata IP-adressen är antingen en virtuell dator eller en belastningsutjämnare för program vara. Den privata IP-adressen finns i ditt privata moln vCenter. Med den offentliga IP-adressen kan du exponera tjänster som körs i ditt privata moln till Internet.

Den offentliga IP-adressen är dedikerad till den privata IP-adressen tills du tar bort tilldelningen. En offentlig IP-adress kan bara tilldelas en privat IP-adress.

En resurs som är associerad med en offentlig IP-adress använder alltid den offentliga IP-adressen för Internet åtkomst. Som standard tillåts endast utgående Internet åtkomst på en offentlig IP-adress.  Inkommande trafik på den offentliga IP-adressen nekas.  Om du vill tillåta inkommande trafik skapar du en brand Väggs regel för den offentliga IP-adressen till den aktuella porten.

## <a name="benefits"></a>Fördelar

Genom att använda en offentlig IP-adress för att kommunicera inkommande tillhandahåller:

* DDoS-skydd (distributed denial of Service). Det här skyddet aktive ras automatiskt för den offentliga IP-adressen.
* Always on trafikövervakning och real tids minskning av vanliga attacker på nätverks nivå. De här försvaren är samma försvar som används av Microsoft onlinetjänster.
* Hela skalan för det globala Azure-nätverket. Nätverket kan användas för att distribuera och minimera angrepps trafik mellan regioner.  

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [allokerar en offentlig IP-adress](https://docs.azure.cloudsimple.com/public-ips/)
