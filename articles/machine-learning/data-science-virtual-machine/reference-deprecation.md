---
title: 'Referens: Data Science Virtual Machine föråldrad bild'
titleSuffix: Azure Data Science Virtual Machine
description: Information om föråldrade som påverkar Azure-Data Science Virtual Machine
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 10/14/2019
ms.topic: reference
ms.openlocfilehash: 71ce8519dca6fa71340b1c34fe3a6891ccf5753f
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2020
ms.locfileid: "77525795"
---
# <a name="reference-deprecation-of-dsvm-images"></a>Referens: utfasning av DSVM-avbildningar

Nedan diskuterar vi förslag på hur du hanterar kommande ingångar på Azure Data Science Virtual Machine.

## <a name="windows-2012-migrating-data-disks"></a>Windows 2012: migrera data diskar

Vi kommer att sluta stödja Windows 2012 DSVM-avbildningen den 31 december 2019. Utför följande steg för att migrera en datadisk från din befintliga Windows 2012-DSVM till en Windows 2016-DSVM:

1. Skapa en ny Windows 2016-DSVM genom att följa anvisningarna som visas [här](./provision-vm.md#create-your-dsvm).
1. Koppla från befintliga data diskar från Windows 2012-avbildningen med hjälp av [de här anvisningarna](../../virtual-machines/windows/detach-disk.md).
1. Koppla disken från föregående steg till Windows 2016-avbildningen med hjälp av [de här anvisningarna](../../virtual-machines/windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm).
