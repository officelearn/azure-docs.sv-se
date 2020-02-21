---
title: 'Referens: kända problem & fel sökning'
titleSuffix: Azure Data Science Virtual  Machine
description: Hämta en lista över kända problem, lösningar och fel sökning för Azure Data Science Virtual Machine
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: reference
ms.date: 10/10/2019
ms.openlocfilehash: d022b1124146a1e506401e6cee257805e3a38fd3
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2020
ms.locfileid: "77526560"
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

