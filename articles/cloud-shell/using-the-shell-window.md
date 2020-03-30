---
title: Använda fönstret Azure Cloud Shell | Microsoft-dokument
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
ms.openlocfilehash: 3435d958049012750e775e2a17f43f463a0cc358
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70860318"
---
# <a name="using-the-azure-cloud-shell-window"></a>Använda Azure Cloud Shell-fönstret

I det här dokumentet beskrivs hur du använder cloud shell-fönstret.

## <a name="swap-between-bash-and-powershell-environments"></a>Växla mellan Bash- och PowerShell-miljöer

Använd miljöväljaren i verktygsfältet Cloud Shell för att växla mellan Bash- och PowerShell-miljöer.  
![Välj miljö](media/using-the-shell-window/env-selector.png)

## <a name="restart-cloud-shell"></a>Starta om cloud shell
Klicka på omstartsikonen i verktygsfältet Cloud Shell för att återställa maskintillståndet.  
![Starta om cloud shell](media/using-the-shell-window/restart.png)
> [!WARNING]
> Om du startar om Cloud Shell återställs maskintillståndet och alla filer som inte sparats av din Azure-filresurs går förlorade.

## <a name="change-the-text-size"></a>Ändra textstorlek
Klicka på inställningsikonen längst upp till vänster i fönstret och hovra sedan över alternativet "Textstorlek" och välj önskad textstorlek. Ditt val sparas mellan sessioner.
![Textstorlek](media/using-the-shell-window/text-size.png)

## <a name="change-the-font"></a>Ändra teckensnitt
Klicka på inställningsikonen längst upp till vänster i fönstret och hovra sedan över alternativet "Teckensnitt" och välj önskat teckensnitt.  Ditt val sparas mellan sessioner.
![Teckensnitt](media/using-the-shell-window/text-font.png)

## <a name="upload-and-download-files"></a>Ladda upp och ladda ned filer
Klicka på ikonen för uppladdnings-/nedladdningsfiler längst upp till vänster i fönstret och välj sedan ladda upp eller ladda ned.  
![Ladda upp/ladda ned filer](media/using-the-shell-window/uploaddownload.png)
* För att ladda upp filer, använd popup-fönstret för att bläddra till filen på din lokala dator, välj önskad fil och klicka på "Öppna"-knappen.  Filen laddas upp till `/home/user` katalogen.
* För att ladda ner filen, ange den fullt kvalificerade filsökvägen i popup-fönstret `/home/user` (dvs. i princip en sökväg under katalogen som dyker upp som standard), och välj "Download"-knappen.  
> [!NOTE] 
> Filer och filsökvägar är skiftlägeskänsliga i Cloud Shell. Dubbelkolla höljet i filsökvägen.

## <a name="open-another-cloud-shell-window"></a>Öppna ett annat Cloud Shell-fönster
Cloud Shell möjliggör flera samtidiga sessioner över webbläsarflikar genom att låta varje session existera som en separat process.
Om du avslutar en session måste du avsluta varje sessionsfönster när varje process körs oberoende av varandra även om de körs på samma dator.  
Klicka på ikonen öppna ny session längst upp till vänster i fönstret. En ny flik öppnas med en annan session ansluten till den befintliga behållaren.
![Öppna ny session](media/using-the-shell-window/newsession.png)

## <a name="cloud-shell-editor"></a>Redigerare för Cloud Shell
* På [sidan Använda Azure Cloud Shell-redigeraren.](using-cloud-shell-editor.md)

## <a name="web-preview"></a>Förhandsgranskning av webb
Klicka på ikonen för förhandsgranskning av webben längst upp till vänster i fönstret, välj "Konfigurera", ange önskad port för att öppna.  Välj antingen "Öppna port" om du bara vill öppna porten eller "Öppna och bläddra" för att öppna porten och förhandsgranska porten på en ny flik.  
![Förhandsgranskning av webb](media/using-the-shell-window/preview.png)  
<br>
![Konfigurera port](media/using-the-shell-window/preview-configure.png)  
Klicka på ikonen för förhandsgranskning av webben längst upp till vänster i fönstret, välj "Förhandsgranska port ..." om du vill förhandsgranska en öppen port på en ny flik. för att stänga den öppna porten.  
![Port för förhandsgranskning/stängning](media/using-the-shell-window/preview-options.png)

## <a name="minimize--maximize-cloud-shell-window"></a>Minimera & maximera Cloud Shell-fönstret
Klicka på minimera ikonen längst upp till höger i fönstret för att dölja den. Klicka på ikonen Cloud Shell igen för att ta fram.
Klicka på maximera ikonen för att ställa in fönstret till max höjd. Om du vill återställa fönstret till föregående storlek klickar du på återställ.  
![Minimera eller maximera fönstret](media/using-the-shell-window/minmax.png)

## <a name="copy-and-paste"></a>Kopiera och klistra in
[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

## <a name="resize-cloud-shell-window"></a>Ändra storlek på cloud shell-fönstret
Klicka och dra verktygsfältets övre kant uppåt eller nedåt om du vill ändra storlek på cloud shell-fönstret.

## <a name="scrolling-text-display"></a>Rullande textvisning
Rulla med musen eller pekplattan för att flytta terminaltext.

## <a name="exit-command"></a>Avsluta kommando
Att `exit` köra avslutar den aktiva sessionen. Detta inträffar som standard efter 20 minuter utan interaktion.

## <a name="next-steps"></a>Nästa steg

[Bash i Cloud Shell Snabbstart](quickstart.md) <br>
[PowerShell i Snabbstart i Cloud Shell](quickstart-powershell.md)
