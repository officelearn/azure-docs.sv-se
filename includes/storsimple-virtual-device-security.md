---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: cb160a140b5c0cb184a5172da10ade0de37c4fed
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/27/2020
ms.locfileid: "67187387"
---
<!--v-sharos 10/13/2105 virtual device security-->

Tänk på följande säkerhets aspekter när du använder den virtuella StorSimple-enheten:

* Den virtuella enheten skyddas via din Microsoft Azure prenumeration. Det innebär att om du använder den virtuella enheten och din Azure-prenumeration komprometteras, är data som lagras på den virtuella enheten också sårbara.
* Den offentliga nyckeln för certifikatet som används för att kryptera data som lagras i Azure StorSimple görs säkert tillgängligt för den klassiska Azure-portalen och den privata nyckeln behålls med StorSimple-enheten. Både offentliga och privata nycklar lagras i Azure på den virtuella StorSimple-enheten.
* Den virtuella enheten finns i Microsoft Azure Data centret.

