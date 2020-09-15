---
title: Felsöka Windows kunde inte slutföra konfigurationen av systemet
titlesuffix: Azure Virtual Machines
description: Den här artikeln innehåller steg för att lösa problem där Sysprep-processen förhindrar start av en virtuell Azure-dator.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 5fc57a8f-5a0c-4b5f-beef-75eecb4d310d
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/09/2020
ms.author: v-miegge
ms.openlocfilehash: 9c170607d6300c4921285e85ac78db5a8a18ad9b
ms.sourcegitcommit: 1fe5127fb5c3f43761f479078251242ae5688386
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90078826"
---
# <a name="troubleshoot-windows-could-not-finish-configuring-the-system"></a>Felsöka Windows kunde inte slutföra konfigurationen av systemet

Den här artikeln innehåller steg för att lösa problem där Sysprep-processen förhindrar start av en virtuell Azure-dator (VM).

## <a name="symptom"></a>Symptom

När du använder [startdiagnostik](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) för att Visa skärm bilden för den virtuella datorn ser du att skärm bilden visar ett installations Windows-fel medan Windows installations programmet startar tjänster. Felet visar meddelandet:

`Windows could not finish configuring the system. To attempt to resume configuration, restart the computer. Setup is starting services`

  ![Bild 1 visar ett installations Windows-fel med meddelandet: "Windows kunde inte slutföra konfigurationen av systemet. Starta om datorn för att försöka återuppta konfigurationen. Tjänsterna startas](./media/windows-could-not-configure-system/1-windows-error-configure.png)

## <a name="cause"></a>Orsak

Det här felet beror på att operativ systemet (OS) inte kan slutföra Sysprep- [processen](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep-process-overview). Felet uppstår när du försöker utföra en första start av en generaliserad virtuell dator. Om det här problemet uppstår återskapar du den generaliserade avbildningen eftersom avbildningen är i ett tillstånd som inte kan distribueras och inte kan återställas.

## <a name="solution"></a>Lösning

Åtgärda problemet genom att följa Azure- [vägledningen om hur du förbereder/fångar en avbildning](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed) och förbereder en ny generaliserad avbildning.
