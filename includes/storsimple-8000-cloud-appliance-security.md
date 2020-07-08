---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 87e6c096c0ff0e58e8acebc13a62e44ea7d65c61
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "67187402"
---
Ha följande säkerhetsöverväganden i åtanke när du använder StorSimple Cloud Appliance:

* Molninstallationen skyddas via din Microsoft Azure-prenumeration. Det innebär att om du använder molninstallationen och din Azure-prenumeration komprometteras, så blir även de data som lagras i molninstallationen sårbara.
* Den offentliga nyckeln för certifikatet som används för att kryptera data som lagras i StorSimple görs tillgänglig för Azure Portal på ett säkert sätt, och den privata nyckeln sparas i StorSimple Cloud Appliance. Med StorSimple Cloud Appliance lagras både den offentliga och privata nyckeln i Azure.
* Molninstallationen finns på Microsoft Azure-datacentret.

