---
title: ta med fil
description: ta med fil
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 04/16/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 0363b719e2587226282257e19e58e3c4e55c2219
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2018
ms.locfileid: "47454582"
---
## <a name="overview-of-ssh-and-keys"></a>Översikt över SSH och nycklar

SSH är en krypterad anslutningsprotokoll som tillåter säker inloggningar över oskyddade anslutningar. SSH är anslutningsprotokoll standard för virtuella Linux-datorer i Azure. Även om SSH själva tillhandahåller en krypterad anslutning, lämnar med hjälp av lösenord med SSH-anslutningar fortfarande den virtuella datorn sårbar för brute force-attacker eller gissa lösenord. En säkrare och önskad metod för att ansluta till en virtuell dator med SSH är med hjälp av ett offentligt / privat nyckelpar, även kallat *SSH-nycklar*. 

* Den *offentlig nyckel* placeras på din Linux-VM eller någon annan tjänst som du vill använda med offentliga nycklar.

* Den *privata nyckeln* är vad du uppger till din Linux-VM när du gör en SSH-anslutning, verifiera din identitet. Skydda den privata nyckeln. Dela den inte med andra.

Beroende på organisationens säkerhetsprinciper, kan du återanvända ett enda offentligt / privat nyckelpar för att komma åt flera virtuella Azure-datorer och tjänster. Du behöver inte en separat par nycklar för varje virtuell dator eller tjänst som du vill använda. 

Din offentliga nyckel kan delas med vem som helst, men bara du (eller din lokala säkerhetsinfrastruktur) ha din privata nyckel.