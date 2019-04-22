---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: a3b6616edbe1678cb18f78ec9025fc0286bf124f
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59684505"
---
Tänk på följande metodtips:

- Management-tjänsten kan inte hämta befintliga lösenord: den kan bara återställa dem via Azure portal. Vi rekommenderar att du lagrar alla lösenord på en säker plats så att du inte har att återställa ett lösenord om det glöms bort. Om du återställer ett lösenord, måste du meddela alla användare innan du återställa den.
- Du kan komma åt Windows PowerShell-gränssnittet på enheten via en fjärranslutning via HTTP. Som en säkerhetsåtgärd bör du använda HTTP betrodda nätverk.
- Kontrollera att enhetens lösenord är stark och väl skyddade. Följ den [Metodtips för lösenord](https://docs.microsoft.com/azure/security/azure-security-identity-management-best-practices#enable-password-management).