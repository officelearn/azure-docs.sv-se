---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: d8570c0be6b64d4e289575ce3f3f1721c4a65074
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59684500"
---
För de data vid vila:

- För de data vid vila används BitLocker XTS AES-256-kryptering för att skydda lokala data.
- För de data som finns i resurser, begränsas åtkomsten till resurser.

    - För SMB-klienter får åtkomst till filresurs-data, måste de autentiseringsuppgifter som är kopplade till resursen. Dessa autentiseringsuppgifter definieras på resursen skapas.
    - För NFS-klienter som har åtkomst till resurser, måste IP-adresserna för klienterna som ska läggas till i resursen skapas.