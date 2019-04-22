---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: bea14544949f90290bf3878295b49f1e08be9eea
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59684482"
---
För data-i-flygningens längd:

- Standard TLS 1.2 används för data som går mellan enheten och Azure. Det finns ingen reserv till TLS 1.1 och tidigare. Agentkommunikation kommer att blockeras om TLS 1.2 inte stöds. TLS 1.2 krävs också för portalen och SDK-hanteringsåtgärder.
- När klienterna kommer åt enheten via det lokala webbgränssnittet i en webbläsare, används som standard TLS 1.2 som standard säkert protokoll.

    - Det bästa sättet är att konfigurera din webbläsare för att använda TLS 1.2.
    - Om webbläsaren inte stöder TLS 1.2, kan du använda TLS 1.1 eller TLS 1.0.
- För att skydda data när du kopierar den från dina dataservrar, rekommenderar vi att du använder SMB 3.0 med kryptering.
