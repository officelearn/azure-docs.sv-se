---
title: ta med fil
description: ta med fil
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 05/17/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: d9c8a0e6a3bd6d79a11ee0d0dab0500a209e5571
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34684990"
---
Du öppnar en port eller skapa en slutpunkt för en virtuell dator (VM) i Azure genom att skapa ett filter för nätverk på ett undernät eller Virtuella datorns nätverksgränssnitt. Du kan placera dessa filter som styr både inkommande och utgående trafik på en Nätverkssäkerhetsgrupp kopplad till den resurs som tar emot trafiken.

Nu ska vi använda ett vanligt exempel på Internet-trafik på port 80. När du har en virtuell dator som är konfigurerad för att hantera webbförfrågningar på standard-TCP port 80 (Glöm inte att starta rätt tjänster och öppna alla OS brandväggsregler på den virtuella datorn samt), du:

1. Skapa en säkerhetsgrupp för nätverk.
2. Skapa en inkommande regel som tillåter trafik med:
   * Målportintervall ”80”
   * Källportintervall av ”*” (vilket innebär att alla källportar)
   * ett prioritetsvärde för mindre 65 500 (för att vara högre i prioritet än standard allomfattande regel för att neka inkommande)
3. Koppla Nätverkssäkerhetsgruppen till den Virtuella datorns nätverksgränssnitt eller undernät.

Du kan skapa komplexa nätverkskonfigurationer för att skydda din miljö med Nätverkssäkerhetsgrupper och regler. Vårt exempel används bara en eller två regler som tillåter HTTP-trafik eller för fjärrhantering. Mer information finns i följande [”mer Information”](#more-information-on-network-security-groups) avsnittet eller [vad är en Nätverkssäkerhetsgrupp?](../articles/virtual-network/security-overview.md)

