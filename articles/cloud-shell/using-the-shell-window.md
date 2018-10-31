---
title: Med hjälp av fönstret Azure Cloud Shell | Microsoft Docs
description: Översikt över hur du använder Azure Cloud Shell-fönstret.
services: azure
documentationcenter: ''
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 01/30/2018
ms.author: juluk
ms.openlocfilehash: 43da2bf5b66ff7db03a6fb5c2e1ceaebe322bcbb
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
ms.locfileid: "28920002"
---
# <a name="using-the-azure-cloud-shell-window"></a>Med hjälp av fönstret Azure Cloud Shell

Det här dokumentet förklarar hur du använder molntjänster Shell-fönstret.

## <a name="swap-between-bash-and-powershell-environments"></a>Växla mellan Bash och PowerShell-miljöer
![](media/using-the-shell-window/env-selector.png)

Använd miljö väljaren I molnet shell för att växla mellan Bash och PowerShell-miljöer.

## <a name="restart-cloud-shell"></a>Starta om Cloud Shell
![](media/using-the-shell-window/restart.png)
> [!WARNING]
> Startar om molnet Shell återställer tillståndet för datorn och alla filer inte bestående av din Azure-filresursen går förlorade.

* Klicka på ikonen omstart i molnet Shell verktygsfältet för att återställa datorns tillstånd.

## <a name="minimize--maximize-cloud-shell-window"></a>Minimera & maximera fönster för molnet Shell
![](media/using-the-shell-window/minmax.png)
* Klicka på minimeringsikonen längst upp höger i fönstret för att dölja den. Klicka på ikonen molnet Shell igen för att ta fram.
* Klicka på maximeringsikonen för att ange fönster för maximala höjd. Återställ fönstret till tidigare storlek genom att klicka på Återställ.

## <a name="concurrent-sessions"></a>Samtidiga sessioner
Molnet Shell kan flera samtidiga sessioner mellan flikar i webbläsaren genom att låta varje session finns som en separat Bash-process.
Om du avslutar en session måste du avsluta från varje session-fönster som varje process körs enskilt även om de körs på samma dator.

## <a name="copy-and-paste"></a>Kopiera och klistra in
[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

## <a name="resize-cloud-shell-window"></a>Ändra storlek på molnet Shell-fönster
* Klicka och dra den övre kanten på verktygsfältet eller mindre molnet Shell-fönstret.

## <a name="scrolling-text-display"></a>Visning av rullande text
* Rulla med musen eller touchpad flytta terminal text.

## <a name="changing-the-text-size"></a>Ändra textstorleken
![](media/using-the-shell-window/text-size.png)
* Klicka på ikonen för inställningar på upp till vänster i fönstret markören över alternativet ”textstorlek” och välj önskad text-storlek. Ditt val ska vara beständig mellan sessioner.

## <a name="exit-command"></a>Avslutningskommandot
Kör `exit` avslutar den aktiva sessionen. Detta inträffar som standard efter 20 minuter utan interaktion.

## <a name="next-steps"></a>Nästa steg

[Bash i molnet Shell Quickstart](quickstart.md)
[PowerShell i molnet Shell Snabbstart](quickstart-powershell.md)
