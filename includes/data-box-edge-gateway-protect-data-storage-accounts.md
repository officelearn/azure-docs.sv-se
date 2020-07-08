---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 9ac9865afe37916f1777d92eab8637884eba0c08
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "82562140"
---
Enheten är associerad med ett lagrings konto som används som mål för dina data i Azure. Åtkomst till lagrings kontot styrs av prenumerations-och 2 512-bitars lagrings åtkomst nycklar som är kopplade till det lagrings kontot.

En av nycklarna används för autentisering när Azure Stack Edge-enheten har åtkomst till lagrings kontot. Den andra nyckeln lagras i reserv, så du kan rotera nycklarna med jämna mellanrum.

Av säkerhets skäl kräver många data Center nyckel rotation. Vi rekommenderar att du följer dessa metod tips för nyckel rotation:

- Din nyckel för lagringskontot liknar rotlösenordet för lagringskontot. Skydda din konto nyckel noggrant. Distribuera inte lösen ordet till andra användare, hårdkoda det eller spara det var som helst med vanlig text som är tillgänglig för andra.
- Återskapa din konto nyckel via Azure Portal om du tror att den kan komprometteras. Mer information finns i [Hantera åtkomst nycklar för lagrings konton](../articles/storage/common/storage-account-keys-manage.md).
- Azure-administratören bör regelbundet ändra eller återskapa den primära eller sekundära nyckeln med hjälp av lagrings avsnittet i Azure Portal för att komma åt lagrings kontot direkt.