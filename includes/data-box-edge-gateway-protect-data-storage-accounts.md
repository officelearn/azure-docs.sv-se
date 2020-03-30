---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 8840e6649058837207c75dbdf7baa5c0b11ef387
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75469782"
---
Din enhet är associerad med ett lagringskonto som används som mål för dina data i Azure. Åtkomsten till lagringskontot styrs av prenumerationen och två 512-bitars lagringsåtkomstnycklar som är associerade med det lagringskontot.

En av nycklarna används för autentisering när Data Box Edge-enheten kommer åt lagringskontot. Den andra nyckeln hålls i reserv, så att du kan rotera tangenterna med jämna mellanrum.

Av säkerhetsskäl kräver många datacenter nyckelrotation. Vi rekommenderar att du följer dessa metodtips för nyckelrotation:

- Din nyckel för lagringskontot liknar rotlösenordet för lagringskontot. Skydda din kontonyckel noggrant. Distribuera inte lösenordet till andra användare, koda det eller spara det var som helst i oformaterad text som är tillgänglig för andra.
- Återskapa din kontonyckel via Azure-portalen om du tror att den kan äventyras. Mer information finns i [Hantera åtkomstnycklar för lagringskonto](../articles/storage/common/storage-account-keys-manage.md).
- Din Azure-administratör bör regelbundet ändra eller återskapa den primära eller sekundära nyckeln med hjälp av lagringsavsnittet i Azure-portalen för att komma åt lagringskontot direkt.