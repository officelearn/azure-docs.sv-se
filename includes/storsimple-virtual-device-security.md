---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: cb160a140b5c0cb184a5172da10ade0de37c4fed
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2018
ms.locfileid: "50164935"
---
<!--v-sharos 10/13/2105 virtual device security-->

Ha följande säkerhetsöverväganden i åtanke när du använder den virtuella StorSimple-enheten:

* Den virtuella enheten skyddas via Microsoft Azure-prenumerationen. Det innebär att om du använder den virtuella enheten och din Azure-prenumeration komprometteras, de data som lagras på din virtuella enhet är också sårbara.
* Den offentliga nyckeln för certifikatet som används för att kryptera data som lagras i Azure StorSimple på ett säkert sätt få tillgång till den klassiska Azure-portalen och den privata nyckeln sparas med StorSimple-enheten. På den virtuella StorSimple-enheten lagras både offentliga och privata nycklar i Azure.
* Den virtuella enheten finns i Microsoft Azure-datacentret.

