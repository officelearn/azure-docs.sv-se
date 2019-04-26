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
ms.date: 04/15/2019
ms.author: damaerte
ms.openlocfilehash: 2511f2c8fb706e232cde9ee4c02c7f8114bd3a2b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60200708"
---
# <a name="using-the-azure-cloud-shell-window"></a>Med hjälp av Azure Cloud Shell-fönstret

Det här dokumentet beskriver hur du använder Cloud Shell-fönstret.

## <a name="swap-between-bash-and-powershell-environments"></a>Växla mellan miljöer Bash och PowerShell

Använd miljöväljaren i Cloud Shells verktygsfält för att växla mellan Bash- och PowerShell-miljöerna.  
![Välj miljö](media/using-the-shell-window/env-selector.png)

## <a name="restart-cloud-shell"></a>Starta om Cloud Shell
Klicka på ikonen omstart i Cloud Shell-verktygsfältet för att återställa datorn.  
![Starta om Cloud Shell](media/using-the-shell-window/restart.png)
> [!WARNING]
> Startar om Cloud Shell återställer tillståndet för datorn och alla filer som inte sparats av din Azure-filresurs går förlorade.

## <a name="change-the-text-size"></a>Ändra textstorleken
Klicka på ikonen för inställningar längst upp till vänster i fönstret hovra över alternativet ”Text-storleken” och välj textstorleken på din önskade. Ditt val ska vara beständig mellan sessioner.
![Textstorlek](media/using-the-shell-window/text-size.png)

## <a name="change-the-font"></a>Ändra teckensnitt
Klicka på ikonen för inställningar längst upp till vänster i fönstret hovra över alternativet ”teckensnitt” och välj din önskade teckensnitt.  Ditt val ska vara beständig mellan sessioner.
![Teckensnitt](media/using-the-shell-window/text-font.png)

## <a name="upload-and-download-files"></a>Ladda upp och ladda ned filer
Klicka på ikonen uppladdning/nedladdning filer på upp till vänster i fönstret, välj sedan överföring eller hämtning.  
![Uppladdning/nedladdning filer](media/using-the-shell-window/uploaddownload.png)
* Använd popup-fönstret för att överföra filer att bläddra till filen på din lokala dator, välja önskad fil och klicka på knappen ”öppet”.  Filen ska överföras till den `/home/user` directory.
* Ange den fullständiga filsökvägen i popup-fönstret för att ladda ned filer, och välj knappen ”Hämta”.  
> [!NOTE] 
> Filer och sökvägar är skiftlägeskänsliga i Cloud Shell. Kontrollera din gemener och versaler i din sökväg.

## <a name="open-another-cloud-shell-window"></a>Öppna ett annat fönster i Cloud Shell
Cloudshell kan flera samtidiga sessioner mellan flikar i webbläsaren genom att låta varje session finns som en separat process.
Om du avslutar en session, måste du avsluta från varje sessionsfönstret som varje process körs enskilt även om de körs på samma dator.  
Klicka på ikonen Öppna ny session på upp till vänster i fönstret. En ny flik öppnas med en annan session som är anslutna till den befintliga behållaren.
![Öppna ny session](media/using-the-shell-window/newsession.png)

## <a name="cloud-shell-editor"></a>Cloud Shell-redigeraren
* Referera till den [med hjälp av Azure Cloud Shell-redigeraren](using-cloud-shell-editor.md) sidan.

## <a name="web-preview"></a>Webbförhandsgranskning
Klicka på ikonen web förhandsversion längst upp till vänster i fönstret, Välj ”Konfigurera”, ange att öppna önskad port.  Välj antingen ”öppna port” att endast öppna porten, eller ”öppna och bläddra” öppna porten och förhandsgranska port på en ny flik.  
![Webbförhandsgranskning](media/using-the-shell-window/preview.png)  
<br>
![Konfigurera port](media/using-the-shell-window/preview-configure.png)  
Klicka på web appförhandsgranskning-ikonen längst upp till vänster i fönstret Välj ”Förhandsgranska port...” att förhandsgranska en öppen port i en ny flik. Klicka på web appförhandsgranskning-ikonen längst upp till vänster i fönstret Välj ”äng port...” att Stäng öppna porten.  
![Förhandsversion/Stäng porten](media/using-the-shell-window/preview-options.png)

## <a name="minimize--maximize-cloud-shell-window"></a>Minimera och maximera fönster för Cloud Shell
Klicka på minimeringsikonen längst upp till höger i fönstret för att dölja den. Klicka på ikonen Cloud Shell igen för att visa.
Klicka på maximeringsikonen för att ange fönster för maxhöjd. Återställ fönstret till tidigare storlek genom att klicka på Återställ.  
![Minimera eller maximera fönstret](media/using-the-shell-window/minmax.png)

## <a name="copy-and-paste"></a>Kopiera och klistra in
[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

## <a name="resize-cloud-shell-window"></a>Ändra storlek på Cloud Shell-fönstret
Klicka och dra den övre kanten verktygsfältet eller mindre i Cloud Shell-fönstret.

## <a name="scrolling-text-display"></a>Rullande textvisning
Rulla med musen eller pekplatta att flytta terminal text.

## <a name="exit-command"></a>Avsluta kommandot
Kör `exit` avslutar den aktiva sessionen. Detta inträffar som standard efter 20 minuter utan interaktion.

## <a name="next-steps"></a>Nästa steg

[Bash i Cloud Shell-Snabbstart](quickstart.md) <br>
[PowerShell i Cloud Shell-Snabbstart](quickstart-powershell.md)