---
title: Kända problem & fel sökning
titleSuffix: Azure Data Science Virtual  Machine
description: Hämta en lista över kända problem, lösningar och fel sökning för Azure Data Science Virtual Machine
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: reference
ms.date: 10/10/2019
ms.openlocfilehash: a0b22326a429edfa2f2b8741453215b42910891c
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2019
ms.locfileid: "72301924"
---
# <a name="known-issues-and-troubleshooting-the-azure-data-science-virtual-machine"></a>Kända problem och fel sökning av Azure-Data Science Virtual Machine

Den här artikeln hjälper dig att hitta och korrigera fel eller fel som uppstår när du använder Azure-Data Science Virtual Machine.

## <a name="python-package-installation-issues"></a>Installations problem för python-paket

### <a name="installing-packages-with-pip-breaks-dependencies-on-linux"></a>Om du installerar paket med pip bryts beroenden på Linux

Använd `sudo pip install` i stället för `pip install` när du installerar paket.

## <a name="disk-encryption-issues"></a>Disk krypterings problem

### <a name="disk-encryption-fails-on-the-ubuntu-dsvm"></a>Disk kryptering Miss lyckas på Ubuntu-DSVM

Azure Disk Encryption (ADE) stöds inte för närvarande på Ubuntu-DSVM. Som en lösning bör du överväga att konfigurera [Azure Storage kryptering med Kundhanterade nycklar](../../storage/common/storage-encryption-keys-portal.md).

## <a name="tool-appears-disabled"></a>Verktyget visas inaktiverat

### <a name="hyper-v-does-not-work-on-the-windows-dsvm"></a>Hyper-V fungerar inte på Windows-DSVM

Detta är ett förväntat beteende, för start prestanda har vi inaktiverat vissa tjänster. Om du vill aktivera igen öppnar du Sök fältet på Windows-DSVM, skriver in "tjänster", anger sedan alla Hyper-V-tjänster till "Manuell" och anger "automatisk" hantering av virtuella Hyper-V-datorer.

Den slutliga skärmen bör se ut så här:

   ![Aktivera Hyper-V](./media/workaround/hyperv-enable-dsvm.png)

