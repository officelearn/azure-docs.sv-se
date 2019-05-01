---
title: VMware Solution by CloudSimple - Azure Public IP address
description: Läs mer om offentliga IP-adresser och sina förmåner på VMware-lösning genom CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a366ec821e5e9663908f790ab3a88424fa956075
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/26/2019
ms.locfileid: "64576977"
---
# <a name="cloudsimple-public-ip-address-overview"></a>Översikt för CloudSimple offentliga IP-adress

En offentlig IP-adress kan Internetresurser kommunicerar inbördes till resurser för privata moln på en privat IP-adress. Den privata IP-adressen är en virtuell dator eller en belastningsutjämnare för programvara. Den privata IP-adressen finns på ditt privata moln vCenter. Offentliga IP-adress kan du exponera tjänster som körs i ditt privata moln till internet.

Den offentliga IP-adressen är dedikerad till den privata IP-adressen tills du ta bort den. En offentlig IP-adress kan endast tilldelas en privat IP-adress.

En resurs som är associerade med en offentlig IP-adress alltid använder den offentliga IP-adressen för internet-åtkomst. Som standard tillåts endast utgående internet-åtkomst på en offentlig IP-adress.  Inkommande trafik på den offentliga IP-adressen nekas.  Skapa en brandväggsregel för den offentliga IP-adressen till den specifika porten som tillåter inkommande trafik.

## <a name="benefits"></a>Fördelar

Med en offentlig IP-adress kan kommunicera inkommande innehåller:

* Datorn service (DDoS) skydd mot attacker. Det här skyddet aktiveras automatiskt för den offentliga IP-adressen.
* Ständigt aktiverad övervakning och i realtid minskning av vanliga attacker på nätverksnivå. Dessa försvar är samma försvar som används av Microsoft online services.
* Hela skalan av Azure globala nätverk. Nätverket kan användas för att distribuera och minimera attack trafik mellan regioner.  

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [allokera en offentlig IP-adress](https://docs.azure.cloudsimple.com/publicips/)
