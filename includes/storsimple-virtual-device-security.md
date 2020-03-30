---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: cb160a140b5c0cb184a5172da10ade0de37c4fed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67187387"
---
<!--v-sharos 10/13/2105 virtual device security-->

Tänk på följande säkerhetsaspekter när du använder den virtuella StorSimple-enheten:

* Den virtuella enheten skyddas via din Microsoft Azure-prenumeration. Det innebär att om du använder den virtuella enheten och din Azure-prenumeration äventyras är de data som lagras på din virtuella enhet också känsliga.
* Den offentliga nyckeln för certifikatet som används för att kryptera data som lagras i Azure StorSimple är säkert tillgänglig för den klassiska Azure-portalen och den privata nyckeln behålls med StorSimple-enheten. På den virtuella storsimple-enheten lagras både offentliga och privata nycklar i Azure.
* Den virtuella enheten finns i Microsoft Azure-datacentret.

