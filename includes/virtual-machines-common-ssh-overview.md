---
title: ta med fil
description: ta med fil
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 12/21/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 3f9b86dd3716a25ab95265cf46b616144f57163b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "71168624"
---
## <a name="overview-of-ssh-and-keys"></a>Översikt över SSH och nycklar

[SSH](https://www.ssh.com/ssh/) är ett krypterat anslutningsprotokoll som möjliggör säkra inloggningar via osäkra anslutningar. SSH är standardanslutningsprotokollet för virtuella Linux-datorer som finns i Azure. Även om SSH själv ger en krypterad anslutning, med hjälp av lösenord med SSH-anslutningar fortfarande lämnar den virtuella datorn sårbar för brute-force attacker eller gissa av lösenord. En säkrare och mer föredragen metod för att ansluta till en virtuell dator med SSH är att använda ett offentligt-privat nyckelpar, även kallat *SSH-nycklar*. 

* Den *offentliga nyckeln* placeras på din Virtuella Linux-dator, eller någon annan tjänst som du vill använda med kryptering med offentliga nycklar.

* Den *privata nyckeln* finns kvar på ditt lokala system. Skydda den privata nyckeln. Dela den inte med andra.

När du använder en SSH-klient för att ansluta till din Virtuella Linux-dator (som har den offentliga nyckeln) testar fjärrdatorn klienten för att se till att den har den privata nyckeln. Om klienten har den privata nyckeln beviljas den åtkomst till den virtuella datorn. 

Beroende på organisationens säkerhetsprinciper kan du återanvända ett enda offentligt-privat nyckelpar för att komma åt flera virtuella Azure-datorer och -tjänster. Du behöver inte ett separat par nycklar för varje virtuell dator eller tjänst som du vill komma åt. 

Din offentliga nyckel kan delas med vem som helst, men bara du (eller din lokala säkerhetsinfrastruktur) bör ha din privata nyckel.