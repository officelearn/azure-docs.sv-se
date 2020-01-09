---
title: 'Referens: DSVM-utfasning'
description: Information om föråldrade som påverkar Azure-Data Science Virtual Machine (DSVM)
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 10/14/2019
ms.topic: reference
ms.openlocfilehash: 252f9c66034dbadaf7a2e9e6f78665c26d414deb
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75456290"
---
# <a name="reference-deprecation-of-dsvm-images"></a>Referens: utfasning av DSVM-avbildningar

Nedan diskuterar vi förslag på hur du hanterar kommande ingångar på Azure Data Science Virtual Machine.

## <a name="windows-2012-migrating-data-disks"></a>Windows 2012: migrera data diskar

Vi kommer att sluta stödja Windows 2012 DSVM-avbildningen den 31 december 2019. Utför följande steg för att migrera en datadisk från din befintliga Windows 2012-DSVM till en Windows 2016-DSVM:

1. Skapa en ny Windows 2016-DSVM genom att följa anvisningarna som visas [här](./provision-vm.md#create-your-dsvm).
1. Koppla från befintliga data diskar från Windows 2012-avbildningen med hjälp av [de här anvisningarna](../../virtual-machines/windows/detach-disk.md).
1. Koppla disken från föregående steg till Windows 2016-avbildningen med hjälp av [de här anvisningarna](../../virtual-machines/windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm).
