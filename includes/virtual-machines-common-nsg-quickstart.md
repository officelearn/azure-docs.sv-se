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
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67187313"
---
Du öppnar en port eller skapa en slutpunkt för att en virtuell dator (VM) i Azure genom att skapa ett filter för nätverk på ett undernät eller ett VM-nätverksgränssnitt. Du kan placera dessa filter som styr både inkommande och utgående trafik på en nätverkssäkerhetsgrupp som är kopplade till den resurs som tar emot trafiken.

Exemplet i den här artikeln visar hur du skapar ett filter för nätverk som använder standard TCP-port 80 (förutsätts du har redan igång rätt tjänster och öppnas OS brandväggsregler på den virtuella datorn).

När du har skapat en virtuell dator som är konfigurerad för att hantera webbegäranden på standard TCP-port 80, kan du:

1. Skapa en nätverkssäkerhetsgrupp.

2. Skapa en inkommande säkerhetsregel som tillåter trafik och tilldela värden med följande inställningar:

   - **Målportintervall**: 80

   - **Käll-portintervall**: * (tillåter alla källportar)

   - **Prioritetsvärdet**: Ange ett värde som är mindre än 65,500 och högre upp i prioritet än standardvärdet allomfattande neka inkommande regel.

3. Associera nätverkssäkerhetsgruppen med VM-nätverksgränssnitt eller undernät.

Även om det här exemplet använder en enkel regel för att tillåta HTTP-trafik, kan du också använda nätverkssäkerhetsgrupper och regler för att skapa mer komplexa konfigurationer. 




