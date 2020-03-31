---
title: ta med fil
description: ta med fil
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 09/12/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: ec6cbcbc93fe87634c87caeb0041b75ec916a22f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67187313"
---
Du öppnar en port eller skapar en slutpunkt till en virtuell dator (VM) i Azure genom att skapa ett nätverksfilter i ett undernät eller ett vm-nätverksgränssnitt. Du placerar dessa filter, som styr både inkommande och utgående trafik, i en nätverkssäkerhetsgrupp som är kopplad till den resurs som tar emot trafiken.

Exemplet i den här artikeln visar hur du skapar ett nätverksfilter som använder standard-TCP-port 80 (det antas att du redan har startat lämpliga tjänster och öppnat alla os-brandväggsregler på den virtuella datorn).

När du har skapat en virtuell dator som är konfigurerad för att betjäna webbbegäranden på standard-TCP-port 80 kan du:

1. Skapa en nätverkssäkerhetsgrupp.

2. Skapa en säkerhetsregel för inkommande trafik som tillåter trafik och tilldela värden till följande inställningar:

   - **Destinationshamnsområden:** 80

   - **Källportintervall:*** (tillåter alla källporter)

   - **Prioritetsvärde**: Ange ett värde som är mindre än 65 500 och högre prioritet än standardregeln för neka inkommande inbound.Priority value : Ange ett värde som är mindre än 65 500 och högre prioritet än standardregeln för neka alla neka inkommande.

3. Associera nätverkssäkerhetsgruppen med vm-nätverksgränssnittet eller undernätet.

Även om det här exemplet använder en enkel regel för att tillåta HTTP-trafik kan du också använda nätverkssäkerhetsgrupper och regler för att skapa mer komplexa nätverkskonfigurationer. 




