---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 653c175a559f5c0b7dc551b396e91276332df20a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60754319"
---
Enheten är associerad med ett storage-konto som används som mål för dina data i Azure. Åtkomst till lagringskontot styrs av prenumerationen och lagring för två 512-bitars åtkomstnycklar som är associerade med det lagringskontot.

En av nycklarna används för autentisering när Data Box Edge-enhet har åtkomst till lagringskontot. Den andra nyckeln lagras i reserv, så att du kan rotera nycklarna regelbundet.

Av säkerhetsskäl kräver många Datacenter rotation av. Vi rekommenderar att du följer dessa Metodtips för rotation av:

- Din nyckel för lagringskontot liknar rotlösenordet för lagringskontot. Noggrant skydda din kontonyckel. Inte distribuera lösenordet till andra användare, hårt code den eller spara den var som helst i oformaterad text som andra har åtkomst till.
- [Återskapa din kontonyckel](../articles/storage/common/storage-account-manage.md#regenerate-access-keys) via Azure portal om du tror att det äventyras.
- Din Azure-administratör bör regelbundet ändra eller återskapa den primära eller sekundära nyckeln med hjälp av avsnittet lagring i Azure-portalen får direktåtkomst till lagringskontot.