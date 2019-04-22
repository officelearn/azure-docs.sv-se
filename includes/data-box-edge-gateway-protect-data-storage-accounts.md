---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 23587e617c62cfe4aa24256330c5f7673dd1c674
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59684489"
---
Enheten är associerad med ett storage-konto som används som mål för dina data i Azure. Åtkomst till lagringskontot styrs av prenumerationen och lagring för två 512-bitars åtkomstnycklar som är associerade med det lagringskontot.

En av nycklarna används för autentisering när Data Box Edge-enhet har åtkomst till lagringskontot. Den andra nyckeln lagras i reserv, så att du kan rotera nycklarna regelbundet.

Av säkerhetsskäl kräver många Datacenter rotation av. Vi rekommenderar att du följer dessa Metodtips för rotation av:

- Din nyckel för lagringskontot liknar rotlösenordet för lagringskontot. Noggrant skydda din kontonyckel. Inte distribuera lösenordet till andra användare, hårt code den eller spara den var som helst i oformaterad textfil som andra har åtkomst till.
- [Återskapa din kontonyckel](../articles/storage/common/storage-account-manage.md#regenerate-access-keys) med Azure portal om du tror att den komprometterats.
- Din Azure-administratör bör med jämna mellanrum, ändra eller återskapa den primära eller sekundära nyckeln med hjälp av avsnittet lagring i Azure portal för direkt åtkomst till lagringskontot.