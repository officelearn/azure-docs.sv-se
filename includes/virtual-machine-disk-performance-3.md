---
title: ta med fil
description: ta med fil
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 10/12/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 8eff9da82fdfa5749fd1c2bc04652d5c8ce8dfd2
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92518091"
---
![Skärm bild av f i o-utdata som visar r = 22.8 k markerat.](media/vm-disk-performance/utilization-metrics-example/fio-output.jpg)

Standard_D8s_v3 kan få totalt 28 600 IOPS. Vi använder måtten för att undersöka vad som händer och identifiera vår Flask hals i Storage. I den vänstra rutan väljer du **mått**:

![Skärm bild som visar mått som marker ATS i det vänstra fönstret.](media/vm-disk-performance/utilization-metrics-example/metrics-menu.jpg)

Låt oss ta en titt på vårt mått på **VM cachelagrad IOPS förbrukade procent** :

![Skärm bild som visar den förbrukade procent satsen V M cachelagrad I O s](media/vm-disk-performance/utilization-metrics-example/vm-cached.jpg)

Det här måttet visar att 61% av 16 000 IOPS som tilldelats till cachelagrade IOPS på den virtuella datorn används. Den här procent andelen innebär att lagrings-i/o-Flask halsen inte är med de diskar som cachelagras eftersom den inte är 100%. Nu ska vi titta på vårt mått för **förbrukade IOPS i virtuell dator i procent** :

![Skärm bild som visar V M Uncached I O P S förbrukade procent.](media/vm-disk-performance/utilization-metrics-example/vm-uncached.jpg)

Måttet är på 100%. Det meddelar oss att alla 12 800 IOPS som tilldelats till den cachelagrade IOPS på den virtuella datorn används. Ett sätt som vi kan åtgärda problemet är att ändra storleken på den virtuella datorn till en större storlek som kan hantera ytterligare IO. Men innan vi gör det ska vi titta på den anslutna disken för att ta reda på hur många IOPS de ser. Kontrol lera OS-disken genom att titta på **OS-diskens IOPS förbrukade procent**:

![Skärm bild som visar O-S disk-I/O S förbrukade procent.](media/vm-disk-performance/utilization-metrics-example/os-disk.jpg)

Det här måttet visar oss att cirka 90% av 5 000 IOPS som har allokerats för den här P30 OS-disken används. Den här procent andelen innebär att det inte finns någon Flask hals på OS-disken. Nu ska vi kontrol lera de data diskar som är anslutna till den virtuella datorn genom att titta på **data disken IOPS förbrukade procent**:

![Skärm bild som visar den förbrukade procenten för datadisk I O s.](media/vm-disk-performance/utilization-metrics-example/data-disks-no-splitting.jpg)

Det här måttet anger att den genomsnittliga IOPS-förbrukade procenten på alla diskar som är anslutna är cirka 42%. Den här procent satsen beräknas baserat på IOPS som används av diskarna och som inte hanteras från värd-cachen. Nu ska vi gå djupare i det här måttet genom att använda *delning* för dessa mått och dela upp dem med LUN-värdet:

![Skärm bild som visar datadisk I O P S förbrukade procent med delning.](media/vm-disk-performance/utilization-metrics-example/data-disks-splitting.jpg)

Det här måttet talar om för oss att data diskarna som är anslutna på LUN 3 och 2 använder runt 85% av sina etablerade IOPS. Här är ett diagram över hur IO-händelsen ser ut som i arkitekturen för virtuella datorer och diskar:

![Diagram över enhets mått I O-exempel.](media/vm-disk-performance/utilization-metrics-example/metrics-diagram.jpg)
