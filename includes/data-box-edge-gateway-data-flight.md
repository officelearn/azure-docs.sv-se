---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: acf1195616d45b155445604ef0204528e5f7ca03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67187980"
---
För data under flygning:

- Standard TLS 1.2 används för data som färdas mellan enheten och Azure. Det finns ingen återgång till TLS 1.1 och tidigare. Agentkommunikation blockeras om TLS 1.2 inte stöds. TLS 1.2 krävs också för portal- och SDK-hantering.
- När klienter kommer åt enheten via det lokala webbgränssnittet i en webbläsare används standard-TLS 1.2 som standardregel för säker fil.

    - Det bästa är att konfigurera webbläsaren så att den använder TLS 1.2.
    - Om webbläsaren inte stöder TLS 1.2 kan du använda TLS 1.1 eller TLS 1.0.
- Vi rekommenderar att du använder SMB 3.0 med kryptering för att skydda data när du kopierar dem från dina dataservrar.
