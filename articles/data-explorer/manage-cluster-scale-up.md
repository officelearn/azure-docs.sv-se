---
title: Skala Azure Data Explorer kluster ändrade behov
description: Den här artikeln beskriver steg för att skala ut och skala i ett Azure Data Explorer-kluster utifrån ändrade begäran.
author: radennis
ms.author: radennis
ms.reviewer: v-orspod
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 213a49d87d5e9f801bb17604a322c231a4e3dee2
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2019
ms.locfileid: "55735818"
---
# <a name="manage-cluster-scale-up-to-accommodate-changing-demand"></a>Hantera klustret skala upp till tillgodose ändrade behov

Ändra storlek på ett kluster på rätt sätt är det viktigt att prestandan för Azure Data Explorer. Men det går inte att förutse efterfrågan på ett kluster med 100% noggrannhet. En statisk klusterstorlek kan leda till under användning eller Överskriden användning, inte är idealiskt. En bättre metod är att *skala* ett kluster, att lägga till och ta bort Kapacitets- och CPU med ändringen av begäran. Den här artikeln visar hur du hanterar klustret skala upp.

Navigera till ditt kluster och under **inställningar** Välj **skala upp**.

Du kan sedan få listan över SKU: er som är tillgängliga. Du kan välja i listan över aktiverade kort. Till exempel på den nedan bild visas endast en SKU som kan väljas från D14_vs.

![Skala upp](media/manage-cluster-scale-up/scale-up.png)

D13_v2 har inaktiverats eftersom det här är den aktuella SKU: N i klustret. L8 och L16 har inaktiverats eftersom de finns inte i den region där klustret är.

Om du vill ändra en SKU klickar du på SKU: N, du vill använda och klicka på den **Välj** knappen.

[!NOTE] Skala upp processen kan ta några minuter och under den tiden klustret kommer att inaktiveras. Observera att skala ned kan skada din klusterprestanda.

Om du behöver hjälp med klusterskalning problem, öppna en supportbegäran i den [Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).