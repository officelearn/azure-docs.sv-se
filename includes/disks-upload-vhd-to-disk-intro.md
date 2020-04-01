---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/27/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e96d205ef1a8f94baa3a0cfe6c5127b6cf570e5a
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420961"
---
I den här artikeln beskrivs hur du antingen laddar upp en virtuell hårddisk från den lokala datorn till en Azure-hanterad disk eller kopierar en hanterad disk till en annan region med AzCopy. Denna process, direkt uppladdning, kan du också ladda upp en VIRTUELLD upp till 32 TiB i storlek direkt till en hanterad disk. För närvarande stöds direktuppladdning för vanliga hårddiskar, standard-SSD- och premium-SSD-hanterade diskar. Det stöds inte för ultra diskar, ännu.

Om du tillhandahåller en säkerhetskopieringslösning för virtuella IaaS-datorer i Azure rekommenderar vi att du använder direktuppladdning för att återställa kundsäkerhetskopior till hanterade diskar. När du överför en virtuell hårddisk från en källa extern till Azure beror hastigheterna på din lokala bandbredd. När du laddar upp eller kopierar från en Virtuell Azure-dator, då din bandbredd skulle vara samma som vanliga hårddiskar.