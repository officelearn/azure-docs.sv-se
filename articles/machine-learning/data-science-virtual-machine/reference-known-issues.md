---
title: 'Referens: Kända problem & felsökning'
titleSuffix: Azure Data Science Virtual  Machine
description: Få en lista över kända problem, lösningar och felsökning för Virtual Machine för Azure Data Science
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: reference
ms.date: 10/10/2019
ms.openlocfilehash: b83effa95b17d712d4019f8ab5bf13c4f02a7d2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206528"
---
# <a name="known-issues-and-troubleshooting-the-azure-data-science-virtual-machine"></a>Kända problem och felsökning av virtual machine för Azure Data Science

Den här artikeln hjälper dig att hitta och korrigera fel eller fel som du kan stöta på när du använder Virtual Machine för Azure Data Science.

## <a name="python-package-installation-issues"></a>Problem med installation av Python-paket

### <a name="installing-packages-with-pip-breaks-dependencies-on-linux"></a>Installera paket med pip raster beroenden på Linux

Använd `sudo pip install` i `pip install` stället för när du installerar paket.

## <a name="disk-encryption-issues"></a>Problem med diskkryptering

### <a name="disk-encryption-fails-on-the-ubuntu-dsvm"></a>Diskkryptering misslyckas på Ubuntu DSVM

Azure Disk Encryption (ADE) stöds för närvarande inte på Ubuntu DSVM. Som en lösning bör du överväga att konfigurera [servertans kryptering av Azure-hanterade diskar](../../virtual-machines/windows/disk-encryption.md).

## <a name="tool-appears-disabled"></a>Verktyget visas inaktiverat

### <a name="hyper-v-does-not-work-on-the-windows-dsvm"></a>Hyper-V fungerar inte på Windows DSVM

Att Hyper-V från början inte fungerar på Windows är förväntat beteende. För startprestanda har vi inaktiverat vissa tjänster. Så här aktiverar du Hyper-V:

1. Öppna sökfältet i Windows DSVM
1. Skriv in "Tjänster",
1. Ställ in alla Hyper-V-tjänster på "Manuell"
1. Ange "Hyper-V Virtual Machine Management" till "Automatisk"

Den sista skärmen ska se ut så här:

   ![Aktivera Hyper-V](./media/workaround/hyperv-enable-dsvm.png)

