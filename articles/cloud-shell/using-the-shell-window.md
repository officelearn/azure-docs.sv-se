---
title: Med hjälp av fönstret Azure Cloud Shell | Microsoft Docs
description: Översikt över hur du använder Azure Cloud Shell-fönstret.
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 01/30/2018
ms.author: damaerte
ms.openlocfilehash: a02642540e6eb39f35b9cc0d38d187a7afa36b7a
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2019
ms.locfileid: "57243456"
---
# <a name="using-the-azure-cloud-shell-window"></a>Med hjälp av Azure Cloud Shell-fönstret

Det här dokumentet beskriver hur du använder Cloud Shell-fönstret.

## <a name="swap-between-bash-and-powershell-environments"></a>Växla mellan miljöer Bash och PowerShell
![](media/using-the-shell-window/env-selector.png)

Använd miljöväljaren i Cloud Shells verktygsfält för att växla mellan Bash- och PowerShell-miljöerna.

## <a name="restart-cloud-shell"></a>Starta om Cloud Shell
![](media/using-the-shell-window/restart.png)
> [!WARNING]
> Startar om Cloud Shell återställer tillståndet för datorn och alla filer som inte sparats av din Azure-filresurs går förlorade.

* Klicka på ikonen omstart i Cloud Shell-verktygsfältet för att återställa datorn.

## <a name="minimize--maximize-cloud-shell-window"></a>Minimera och maximera fönster för Cloud Shell
![](media/using-the-shell-window/minmax.png)
* Klicka på minimeringsikonen längst upp till höger i fönstret för att dölja den. Klicka på ikonen Cloud Shell igen för att visa.
* Klicka på maximeringsikonen för att ange fönster för maxhöjd. Återställ fönstret till tidigare storlek genom att klicka på Återställ.

## <a name="concurrent-sessions"></a>Samtidiga sessioner
Cloudshell kan flera samtidiga sessioner mellan flikar i webbläsaren genom att låta varje session finns som en separat Bash-process.
Om du avslutar en session, måste du avsluta från varje sessionsfönstret som varje process körs enskilt även om de körs på samma dator.

## <a name="copy-and-paste"></a>Kopiera och klistra in
[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

## <a name="resize-cloud-shell-window"></a>Ändra storlek på Cloud Shell-fönstret
* Klicka och dra den övre kanten verktygsfältet eller mindre i Cloud Shell-fönstret.

## <a name="scrolling-text-display"></a>Rullande textvisning
* Rulla med musen eller pekplatta att flytta terminal text.

## <a name="changing-the-text-size"></a>Ändra textstorleken
![](media/using-the-shell-window/text-size.png)
* Klicka på ikonen för inställningar längst upp till vänster i fönstret hovra över alternativet ”Text-storleken” och välj textstorleken på din önskade. Ditt val ska vara beständig mellan sessioner.

## <a name="exit-command"></a>Avsluta kommandot
Kör `exit` avslutar den aktiva sessionen. Detta inträffar som standard efter 20 minuter utan interaktion.

## <a name="next-steps"></a>Nästa steg

[Bash i Cloud Shell snabbstarten](quickstart.md)
[PowerShell i Cloud Shell-Snabbstart](quickstart-powershell.md)