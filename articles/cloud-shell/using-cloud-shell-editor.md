---
title: Med hjälp av Azure Cloud Shell-redigeraren | Microsoft Docs
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60199232"
---
# <a name="using-the-azure-cloud-shell-editor"></a>Med hjälp av Azure Cloud Shell-redigeraren

Azure Cloud Shell innehåller en integrerad textfilsredigeraren som bygger på öppen källkod [Monaco redigeraren](https://github.com/Microsoft/monaco-editor). Cloud Shell-redigeraren stöder funktioner som språk markeringar, kommandopaletten och en Utforskaren.

![Cloud Shell-redigeraren](media/using-cloud-shell-editor/open-editor.png)

## <a name="opening-the-editor"></a>När du öppnar Redigeraren

Starta redigeraren genom att köra för att skapa en enkel fil och redigera `code .` i Cloud Shell-terminalen. Den här åtgärden öppnar Redigeraren med active arbetskatalogen i terminalen.

Om du vill öppna en fil för att snabbt redigera direkt, kör `code <filename>` så öppnas ett redigeringsfönster utan Utforskaren.

Om du vill öppna Redigeraren för via Användargränssnittet knappen klickar du på den `{}` redigeraren ikonen i verktygsfältet. Det öppnar Redigeraren och standard Utforskaren för att den `/home/<user>` directory.

## <a name="closing-the-editor"></a>Stänga redigeraren

Stäng redigeraren genom att öppna den `...` åtgärdspanel uppe till höger i redigeraren och välj `Close editor`.

![Stäng redigeringsprogrammet](media/using-cloud-shell-editor/close-editor.png)

## <a name="command-palette"></a>Kommandopalett

Starta kommandopaletten genom att använda den `F1` nyckeln när fokus har angetts för redigeraren. Att öppna kommandopaletten kan även utföras från panelen åtgärden.

![Cmd palette](media/using-cloud-shell-editor/cmd-palette.png)

## <a name="contributing-to-the-monaco-editor"></a>Bidra till Monaco redigeraren

Markera språkstöd i Cloud Shell-redigeraren stöds via överordnade funktioner i den [Monaco redigeraren](https://github.com/Microsoft/monaco-editor)s användning av Monarch syntax definitioner. Om du vill lära dig mer om att göra bidrag, läsa den [Monaco deltagarguiden](https://github.com/Microsoft/monaco-editor/blob/master/CONTRIBUTING.md).

## <a name="next-steps"></a>Nästa steg
[Prova Snabbstart för Bash i Cloud Shell](quickstart.md)
[visa en fullständig lista över integrerade verktyg för Cloud Shell](features.md)