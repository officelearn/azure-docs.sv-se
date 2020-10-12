---
title: inkludera fil
description: inkludera fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/10/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7700b1118c4f04643607f44a474338ffdd5c09e0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "86231021"
---
- Har inte stöd för Ultra disks.
- Det går inte att aktivera om Azure Disk Encryption (gäst-VM-kryptering med BitLocker/VM-dekryptering) har Aktiver ATS på dina virtuella datorer/skal uppsättningar för virtuella datorer.
- Azure Disk Encryption kan inte aktive ras på diskar som har kryptering på värddatorn aktive rad.
- Krypteringen kan aktive ras på den befintliga skalnings uppsättningen för virtuella datorer. Men endast nya virtuella datorer som skapats efter aktivering av kryptering krypteras automatiskt.
- Befintliga virtuella datorer måste frigöras och omallokeras för att kunna krypteras.