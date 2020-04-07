---
title: 'Referens: Avbildningsavfasning av datavetenskap för virtuell dator'
titleSuffix: Azure Data Science Virtual Machine
description: Information om utfasningar som påverkar virtual machine för Azure Data Science
author: lobrien
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: laobri
ms.date: 04/03/2020
ms.topic: reference
ms.openlocfilehash: 6004ae0c1fd2fa25a07ab84776ab74789f6e7da9
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754767"
---
# <a name="reference-deprecation-of-dsvm-images"></a>Referens: Utfasning av DSVM-bilder

Nedan diskuterar vi förslag för att hantera kommande utfasningar på Virtual Machine för Azure Data Science.

## <a name="windows-2012-migrating-data-disks"></a>Windows 2012: Migrera datadiskar

Vi kommer att sluta stödja Windows 2012 DSVM-avbildningen den 31 december 2019. Om du vill migrera en datadisk från din befintliga Windows 2012 DSVM till en Windows 2016 DSVM gör du följande:

1. Skapa en ny Windows 2016 DSVM, enligt instruktionerna som visas [här](./provision-vm.md#create-your-dsvm).
1. Koppla bort befintliga datadiskar från Windows 2012-avbildningen med hjälp av [dessa instruktioner](../../virtual-machines/windows/detach-disk.md).
1. Bifoga disken från föregående steg till windows 2016-avbildningen med hjälp av [dessa instruktioner](../../virtual-machines/windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm).

## <a name="centos"></a>CentOS

Nya användare bör använda de senaste Ubuntu- eller Windows-avbildningarna. CentOS kommer att vara tillgängligt för användning med befintliga lösningsmallar.