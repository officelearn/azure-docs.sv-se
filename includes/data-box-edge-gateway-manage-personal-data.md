---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: af4c3829c8b12bfcaae0602dde8f459de7e50f3a
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67187984"
---
- **Ordna information**. När en order skapas, lagras den leveransadressen, e-postadress och kontaktinformation för användaren i Azure-portalen. Informationen som sparas omfattar:
  - Kontaktnamn
  - Telefonnummer
  - E-postadress
  - Gatuadress
  - Stad
  - Postnummer/Postnummer
  - Status
  - Land/region/region
  - Spårningsnummer för leveransen

    Beställningsinformation krypteras och lagras i tjänsten. Informationen sparas i tjänsten tills du uttryckligen tar bort en resurs eller en order. Borttagningen av resursen och motsvarande order blockeras tills enheten levereras tills återgår enheten till Microsoft.

- **Leveransadress**. När en beställning placeras, ger Data Box-tjänsten leveransadressen till tredje parts-leverantörer som UPS.

- **Dela användare**. Användare på din enhet kan också komma åt data som finns på filresurser. En lista över användare som har åtkomst till resursdata kan visas. När resurserna tas bort raderas även den här listan.