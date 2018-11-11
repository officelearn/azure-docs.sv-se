---
title: ta med fil
description: ta med fil
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 11/08/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: cff3d7bfb89d5b03f986da32edc148efcfb7e7bd
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2018
ms.locfileid: "51506414"
---
## <a name="overview-of-ssh-and-keys"></a>Översikt över SSH och nycklar

SSH är en krypterad anslutningsprotokoll som tillåter säker inloggningar över oskyddade anslutningar. SSH är anslutningsprotokoll standard för virtuella Linux-datorer i Azure. Även om SSH själva tillhandahåller en krypterad anslutning, lämnar med hjälp av lösenord med SSH-anslutningar fortfarande den virtuella datorn sårbar för brute force-attacker eller gissa lösenord. En säkrare och önskad metod för att ansluta till en virtuell dator med SSH är med hjälp av ett offentligt / privat nyckelpar, även kallat *SSH-nycklar*. 

* Den *offentlig nyckel* placeras på din Linux-VM eller någon annan tjänst som du vill använda med offentliga nycklar.

* Den *privata nyckeln* på du lokalt system används av en SSH-klient för att verifiera din identitet när du ansluter till din Linux-VM. Skydda den privata nyckeln. Dela den inte med andra.

Beroende på organisationens säkerhetsprinciper, kan du återanvända ett enda offentligt / privat nyckelpar för att komma åt flera virtuella Azure-datorer och tjänster. Du behöver inte en separat par nycklar för varje virtuell dator eller tjänst som du vill använda. 

Din offentliga nyckel kan delas med vem som helst, men bara du (eller din lokala säkerhetsinfrastruktur) ha din privata nyckel.