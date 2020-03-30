---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/18/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 8c5c0c8f649e7cbab2c16688717de1aaabfb93c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "75477153"
---
Den här referensinformationen skalbarhet och prestandamål för Azure Storage. De skalbarhets- och prestandamål som anges här är avancerade mål, men är uppnåeliga. I samtliga fall beror begärandens hastighet och bandbredd som uppnås av ditt lagringskonto på storleken på de objekt som lagras, åtkomstmönstren som används och vilken typ av arbetsbelastning ditt program utför.

Se till att testa din tjänst för att avgöra om dess prestanda uppfyller dina krav. Undvik om möjligt plötsliga toppar i trafikhastigheten och se till att trafiken är väl fördelad över partitioner.

När ditt program når gränsen för vad en partition kan hantera för din arbetsbelastning börjar Azure Storage returnera felkod 503 (Server Busy) eller felkod 500 (Operation Timeout) svar. Om 503 fel inträffar kan du överväga att ändra programmet så att det använder en exponentiell backoff-princip för återförsök. Exponentiell backoff gör att belastningen på partitionen kan minska och för att underlätta toppar i trafiken till den partitionen.
