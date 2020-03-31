---
title: Använda Azure Cloud Shell-redigeraren | Microsoft-dokument
description: Översikt över hur du använder Azure Cloud Shell-redigeraren.
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
ms.date: 07/24/2018
ms.author: damaerte
ms.openlocfilehash: 7f597bb5cba1a12bdb93325fe2b877ffc644e3e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60199232"
---
# <a name="using-the-azure-cloud-shell-editor"></a>Använda Azure Cloud Shell-redigeraren

Azure Cloud Shell innehåller en integrerad filredigerare byggd från [Monaco](https://github.com/Microsoft/monaco-editor)Editor med öppen källkod . Cloud Shell-redigeringsprogrammet stöder funktioner som språkmarkering, kommandopaletten och en filutforskare.

![Redigerare för Cloud Shell](media/using-cloud-shell-editor/open-editor.png)

## <a name="opening-the-editor"></a>Öppna redaktören

Om du vill kunna skapa och redigera filer enkelt startar du redigeringsprogrammet genom att köra `code .` i Cloud Shell-terminalen. Då öppnas redigeringsprogrammet med den aktiva arbetskatalogen i terminalen.

Om du vill öppna en fil direkt för snabb redigering kör du `code <filename>` för att öppna redigeringsprogrammet utan filutforskaren.

Om du vill öppna redigeringsprogrammet med en knapp i användargränssnittet klickar du på ikonen för `{}`-redigeringsprogrammet i verktygsfältet. Då öppnas redigeringsprogrammet och katalogen `/home/<user>` används som standard i filutforskaren.

## <a name="closing-the-editor"></a>Stänga redigeraren

Om du vill stänga `...` redigeraren öppnar du åtgärdspanelen längst upp till höger i redigeraren och väljer `Close editor`.

![Stäng redigeraren](media/using-cloud-shell-editor/close-editor.png)

## <a name="command-palette"></a>Kommandopalett

Om du vill starta kommandopaletten `F1` använder du nyckeln när fokus är inställt på redigeraren. Om du öppnar kommandopaletten kan du också göra via åtgärdspanelen.

![Komdpalett](media/using-cloud-shell-editor/cmd-palette.png)

## <a name="contributing-to-the-monaco-editor"></a>Bidra till Monaco Editor

Stöd för språkhöjd i Cloud Shell-redigeraren stöds genom uppströmsfunktioner i [Monaco-redigerarens](https://github.com/Microsoft/monaco-editor)användning av Monarch-syntaxdefinitioner. Om du vill veta hur du gör bidrag, läs [Monaco contributor guide](https://github.com/Microsoft/monaco-editor/blob/master/CONTRIBUTING.md).

## <a name="next-steps"></a>Nästa steg
[Prova snabbstarten för Bash i Cloud Shell](quickstart.md)
[Visa den fullständiga listan över integrerade Cloud Shell-verktyg](features.md)