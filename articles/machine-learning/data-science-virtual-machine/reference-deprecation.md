---
title: 'Referens: Avbildningsavfasning av datavetenskap för virtuell dator'
titleSuffix: Azure Data Science Virtual Machine
description: Information om utfasningar som påverkar virtual machine för Azure Data Science
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 10/14/2019
ms.topic: reference
ms.openlocfilehash: 71ce8519dca6fa71340b1c34fe3a6891ccf5753f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77525795"
---
# <a name="reference-deprecation-of-dsvm-images"></a>Referens: Utfasning av DSVM-bilder

Nedan diskuterar vi förslag för att hantera kommande utfasningar på Virtual Machine för Azure Data Science.

## <a name="windows-2012-migrating-data-disks"></a>Windows 2012: Migrera datadiskar

Vi kommer att sluta stödja Windows 2012 DSVM-avbildningen den 31 december 2019. Om du vill migrera en datadisk från din befintliga Windows 2012 DSVM till en Windows 2016 DSVM gör du följande:

1. Skapa en ny Windows 2016 DSVM, enligt instruktionerna som visas [här](./provision-vm.md#create-your-dsvm).
1. Koppla bort befintliga datadiskar från Windows 2012-avbildningen med hjälp av [dessa instruktioner](../../virtual-machines/windows/detach-disk.md).
1. Bifoga disken från föregående steg till windows 2016-avbildningen med hjälp av [dessa instruktioner](../../virtual-machines/windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm).
