---
title: 'Referens: Data Science Virtual Machine föråldrad bild'
titleSuffix: Azure Data Science Virtual Machine
description: Information om föråldrade som påverkar Azure-Data Science Virtual Machine
author: lobrien
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: laobri
ms.date: 04/03/2020
ms.topic: reference
ms.openlocfilehash: 6004ae0c1fd2fa25a07ab84776ab74789f6e7da9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "80754767"
---
# <a name="reference-deprecation-of-dsvm-images"></a>Referens: utfasning av DSVM-avbildningar

Nedan diskuterar vi förslag på hur du hanterar kommande ingångar på Azure Data Science Virtual Machine.

## <a name="windows-2012-migrating-data-disks"></a>Windows 2012: migrera data diskar

Vi kommer att sluta stödja Windows 2012 DSVM-avbildningen den 31 december 2019. Utför följande steg för att migrera en datadisk från din befintliga Windows 2012-DSVM till en Windows 2016-DSVM:

1. Skapa en ny Windows 2016-DSVM genom att följa anvisningarna som visas [här](./provision-vm.md#create-your-dsvm).
1. Koppla från befintliga data diskar från Windows 2012-avbildningen med hjälp av [de här anvisningarna](../../virtual-machines/windows/detach-disk.md).
1. Koppla disken från föregående steg till Windows 2016-avbildningen med hjälp av [de här anvisningarna](../../virtual-machines/windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm).

## <a name="centos"></a>CentOS

Nya användare bör använda de senaste Ubuntu-eller Windows-avbildningarna. CentOS är fortfarande tillgängliga för användning med befintliga Solution templates.