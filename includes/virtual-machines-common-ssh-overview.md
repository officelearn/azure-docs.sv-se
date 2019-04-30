---
title: ta med fil
description: ta med fil
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 12/21/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: bca78e2963f19b60071b1b27c8dc65c76818e10e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61473688"
---
## <a name="overview-of-ssh-and-keys"></a>Översikt över SSH och nycklar

[SSH](https://www.ssh.com/ssh/) är en krypterad anslutningsprotokoll som tillåter säker inloggningar över oskyddade anslutningar. SSH är anslutningsprotokoll standard för virtuella Linux-datorer i Azure. Även om SSH själva tillhandahåller en krypterad anslutning, lämnar med hjälp av lösenord med SSH-anslutningar fortfarande den virtuella datorn sårbar för brute force-attacker eller gissa lösenord. En säkrare och önskad metod för att ansluta till en virtuell dator med SSH är med hjälp av ett offentligt / privat nyckelpar, även kallat *SSH-nycklar*. 

* Den *offentlig nyckel* placeras på din Linux-VM eller någon annan tjänst som du vill använda med offentliga nycklar.

* Den *privata nyckeln* förblir i det lokala systemet. Skydda den privata nyckeln. Dela den inte med andra.

När du använder en SSH-klient för att ansluta till din Linux-VM (som har den offentliga nyckeln), Virtuellt fjärrdatorn testar klienten för att se till att det har den privata nyckeln. Om klienten har den privata nyckeln, det har beviljats åtkomst till den virtuella datorn. 

Beroende på organisationens säkerhetsprinciper, kan du återanvända ett enda offentligt / privat nyckelpar för att komma åt flera virtuella Azure-datorer och tjänster. Du behöver inte en separat par nycklar för varje virtuell dator eller tjänst som du vill använda. 

Din offentliga nyckel kan delas med vem som helst, men bara du (eller din lokala säkerhetsinfrastruktur) ha din privata nyckel.