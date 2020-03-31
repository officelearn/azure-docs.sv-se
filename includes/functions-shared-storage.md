---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/20/2018
ms.author: glenga
ms.openlocfilehash: 82d122ed236dc72ced7ebafe2301ef5f1143897f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "76963671"
---
För att maximera prestanda, använd ett separat lagringskonto för varje funktionsapp. Detta är särskilt viktigt när du har funktioner med varaktiga funktioner eller händelsenav som utlöses, som båda genererar en stor mängd lagringstransaktioner. När din programlogik interagerar med Azure Storage, antingen direkt (med lagringSDK) eller via någon av lagringsbindningarna, bör du använda ett dedikerat lagringskonto. Om du till exempel har en eventnavet-utlöst funktion som skriver vissa&mdash;data till blob-lagring använder du två lagringskonton ett för funktionsappen och ett annat för de blobbar som lagras av funktionen.