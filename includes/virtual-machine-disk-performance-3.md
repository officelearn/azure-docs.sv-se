---
title: inkludera fil
description: inkludera fil
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 10/12/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 14e74bfbcd087ccc1d8c5f2f10a8e44ed37cce84
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92016594"
---
![Mått menyn](media/vm-disk-performance/utilization-metrics-example/fio-output.jpg)

Standard_D8s_v3 kan få totalt 28 600 IOPs, med hjälp av måtten kan du undersöka vad som pågår och identifiera vår Flask hals i IO-minnet. Börja med att leta upp menyn med mått knappen till vänster och välj den:

![Mått menyn](media/vm-disk-performance/utilization-metrics-example/metrics-menu.jpg)

Låt oss ta en titt på vårt mått på **VM cachelagrad IOPS förbrukade procent** :

![VM cachelagrad IOPS förbrukad procent](media/vm-disk-performance/utilization-metrics-example/vm-cached.jpg)

Det här måttet meddelar oss från 16 000 IOPs som tilldelats till cachelagrade IOPs på den virtuella datorn, 61% används. Det innebär att lagrings-IO-Flask halsen inte är med de diskar som cachelagras på grund av att den inte är 100%. Nu ska vi ta en titt på vårt mått för **förbrukade IOPS i virtuell dator i procent** :

![VM, ej cachelagrad IOPS, procent andel](media/vm-disk-performance/utilization-metrics-example/vm-uncached.jpg)

Det här måttet är på 100% och talar om för oss att alla 12 800 IOPs som tilldelats till den cachelagrade IOPs på den virtuella datorn används. Ett sätt att åtgärda detta är att ändra storleken på den virtuella datorn till en större storlek som kan hantera ytterligare i/o. Men innan vi gör det tar vi en titt på disken som är ansluten för att se hur många IOPs de ser. Låt oss ta en titt på OS-disken genom att titta på **OS-diskens IOPS förbrukade procent**:

![Förbrukade procent av OS-diskens IOPS](media/vm-disk-performance/utilization-metrics-example/os-disk.jpg)

Det här måttet talar om för oss att den 5 000 IOPs som har tillhandahållits för den här P30 OS-disken, cirka 90% av den används. Det innebär att det inte finns någon Flask hals här på OS-disken. Nu ska vi ta en titt på de data diskar som är anslutna till den virtuella datorn genom att titta på **data disken med IOPS förbrukade procent**:

![Förbrukad rabatt i data disk IOPS](media/vm-disk-performance/utilization-metrics-example/data-disks-no-splitting.jpg)

Detta mått talar om för oss att den genomsnittliga IOPs-förbrukade procenten över alla diskar som är anslutna är cirka 42%. Den här procent satsen beräknas baserat på IOPs som används av diskarna och hanteras inte från värd-cachen. Nu ska vi titta närmare på det här måttet och se hur man använder **delning** för dessa mått och delar av LUN-värdet:

![Data disk IOPS förbrukade procent andel med delning](media/vm-disk-performance/utilization-metrics-example/data-disks-splitting.jpg)

Det här måttet talar om för oss att de data diskar som är anslutna på LUN 3 och 2 använder runt 85% av deras etablerade IOPs. Här är ett diagram över hur IO-händelsen ser ut från arkitekturen för virtuella datorer och diskar:

![Exempel diagram för lagring av IO-mått](media/vm-disk-performance/utilization-metrics-example/metrics-diagram.jpg)
