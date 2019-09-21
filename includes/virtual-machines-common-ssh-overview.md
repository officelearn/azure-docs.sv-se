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
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/20/2019
ms.locfileid: "71168624"
---
## <a name="overview-of-ssh-and-keys"></a>Översikt över SSH och nycklar

[SSH](https://www.ssh.com/ssh/) är ett krypterat anslutnings protokoll som tillåter säkra inloggningar via oskyddade anslutningar. SSH är standard anslutnings protokollet för virtuella Linux-datorer som finns i Azure. Även om SSH tillhandahåller en krypterad anslutning lämnar lösen ord med SSH-anslutningar fortfarande den virtuella datorn som är sårbar för angrepp eller gissning av lösen ord. En säkrare och önskad metod för att ansluta till en virtuell dator med SSH är att använda ett offentligt privat nyckel par, även kallat *SSH-nycklar*. 

* Den *offentliga nyckeln* placeras på din virtuella Linux-dator eller någon annan tjänst som du vill använda med kryptering med offentliga nycklar.

* Den *privata nyckeln* finns kvar i det lokala systemet. Skydda den privata nyckeln. Dela den inte med andra.

När du använder en SSH-klient för att ansluta till din virtuella Linux-dator (som har den offentliga nyckeln) testar den virtuella fjärrdatorn klienten för att kontrol lera att den har den privata nyckeln. Om klienten har den privata nyckeln beviljas den åtkomst till den virtuella datorn. 

Beroende på din organisations säkerhets principer kan du återanvända ett enda offentligt-privat nyckel par för åtkomst till flera virtuella Azure-datorer och-tjänster. Du behöver inte ett separat nyckel par för varje virtuell dator eller tjänst som du vill komma åt. 

Din offentliga nyckel kan delas med vem som helst, men bara du (eller din lokala säkerhets infrastruktur) måste ha en privat nyckel.