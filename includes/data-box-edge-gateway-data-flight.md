---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: acf1195616d45b155445604ef0204528e5f7ca03
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60754334"
---
För data som rör sig:

- Standard TLS 1.2 används för data som överförs mellan enheten och Azure. Det finns ingen reserv till TLS 1.1 och tidigare. Agentkommunikationen kommer att blockeras om TLS 1.2 inte stöds. TLS 1.2 krävs också för portalen och SDK-hantering.
- När klienter har åtkomst till din enhet via det lokala webbgränssnittet på en webbläsare, används standard TLS 1.2 som standard säkert protokoll.

    - Det bästa sättet är att konfigurera din webbläsare för att använda TLS 1.2.
    - Om webbläsaren inte stöder TLS 1.2, kan du använda TLS 1.1 eller TLS 1.0.
- Vi rekommenderar att du använder SMB 3.0 med kryptering för att skydda data när du kopierar den från dina dataservrar.
