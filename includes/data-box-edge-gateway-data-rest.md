---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 8a6ceee0f7db4b7b368dce1a28c8edc0b2b11180
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67187985"
---
För vilande data:

- BitLocker XTS-AES 256-bitars kryptering används för att skydda lokala data.
- Åtkomst till data som lagras i resurser begränsas.

    - SMB-klienter som har åtkomst till att dela data behöver användarens autentiseringsuppgifter som är kopplade till resursen. Dessa autentiseringsuppgifter definieras när resursen har skapats.
    - IP-adresserna för NFS-klienter som har åtkomst till en resurs måste läggas till när resursen har skapats.