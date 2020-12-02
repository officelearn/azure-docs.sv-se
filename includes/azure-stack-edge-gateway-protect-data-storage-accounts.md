---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 653c175a559f5c0b7dc551b396e91276332df20a
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96467808"
---
Enheten associeras med ett lagringskonto som används som mål för dina data i Azure. Åtkomsten till lagringskontot styrs via prenumerationen och två lagringsåtkomstnycklar på 512 bitar som är kopplade till lagringskontot.

En av nycklarna används för autentisering när den Data Box Edge enheten har åtkomst till lagrings kontot. Den andra nyckeln sparas i reserv så du kan rotera nycklarna med jämna mellanrum.

Många datacenter kräver nyckelrotation av säkerhetsskäl. Vi rekommenderar att du följer dessa metod tips för nyckel rotation:

- Din nyckel för lagringskontot liknar rotlösenordet för lagringskontot. Skydda din kontonyckel noga. Sprid inte lösenordet till andra användare, hårdkoda det eller spara det i klartext där andra kan se.
- [Återskapa din konto nyckel](../articles/storage/common/storage-account-manage.md#regenerate-access-keys) via Azure Portal om du tror att den kan komprometteras.
- Azure-administratören bör regelbundet ändra eller återskapa den primära eller sekundära nyckeln med hjälp av lagrings avsnittet i Azure Portal för att komma åt lagrings kontot direkt.