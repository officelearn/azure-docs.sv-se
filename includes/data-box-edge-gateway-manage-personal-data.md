---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: af4c3829c8b12bfcaae0602dde8f459de7e50f3a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "67187984"
---
- **Beställnings information**. När en order skapas lagras leverans adress, e-postadress och kontakt information för användaren i Azure Portal. Informationen som sparas omfattar:
  - Kontaktnamn
  - Telefonnummer
  - E-postadress
  - Gatuadress
  - Ort
  - POST nummer
  - Status
  - Land/provins/region
  - Spårningsnummer för leveransen

    Order Detaljer krypteras och lagras i tjänsten. Tjänsten behåller informationen tills du uttryckligen tar bort resursen eller ordningen. Borttagningen av resursen och motsvarande ordning blockeras från den tidpunkt då enheten levereras tills enheten återgår till Microsoft.

- **Leverans adress**. När en beställning har placerats tillhandahåller Data Box-enhet tjänsten leverans adressen till tredjeparts-leverantörer som UPS.

- **Dela användare**. Användare på enheten kan även komma åt data som finns på resurserna. En lista över användare som kan komma åt resurs data kan visas. När resurserna tas bort raderas även den här listan.