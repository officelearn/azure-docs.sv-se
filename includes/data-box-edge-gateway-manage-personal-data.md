---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: af4c3829c8b12bfcaae0602dde8f459de7e50f3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67187984"
---
- **Beställ information**. När en order skapas lagras användarens leveransadress, e-postadress och kontaktinformation i Azure-portalen. Informationen som sparas omfattar:
  - Kontaktnamn
  - Telefonnummer
  - E-postadress
  - Gatuadress
  - Ort
  - Postnummer/postnummer
  - Status
  - Land/provins/region
  - Spårningsnummer för leveransen

    Orderinformation krypteras och lagras i tjänsten. Tjänsten behåller informationen tills du uttryckligen tar bort resursen eller ordern. Borttagningen av resursen och motsvarande order blockeras från den tidpunkt då enheten levereras tills enheten återgår till Microsoft.

- **Leveransadress**. När en beställning har gjorts tillhandahåller Data Box-tjänsten leveransadressen till tredjepartsföretag som UPS.

- **Dela användare**. Användare på enheten kan också komma åt data som finns på resurserna. En lista över användare som kan komma åt delningsdata kan visas. När resurserna tas bort tas även den här listan bort.