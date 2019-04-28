---
title: Konfigurera inställningar för Azure Media Clipper tangentbord | Microsoft Docs
description: För att konfigurera konfigurerbara kortkommandon för Azure Media Clipper
services: media-services
keywords: Clip, underklipp, kodning, media
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 03/14/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 2eb32b8ec265a4afa2581374c1f07ac06958a576
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61466114"
---
# <a name="configure-azure-media-clipper-keyboard-shortcuts"></a>Konfigurera kortkommandon för Azure Media Clipper 

Azure Media Clipper stöder anpassa standardkortkommandon genom att tillhandahålla en valfri `keymap` JSON-parametern.

I följande exempel JSON visas standardkortkommandon. Du kan anpassa dessa inställningar genom att ändra nyckelfälten och skicka parametern när initieras Clipper.

```json
{
    "AZURE_MEDIA_SERVICE_SUBCLIPPER": {
        "UNDO": {
            "windows": "ctrl+z",
            "osx": "command+z"
        },
        "REDO": {
            "windows": "ctrl+shift+z",
            "osx": "command+shift+z"
        },
        "MARK_IN": {
            "windows": "ctrl+i",
            "osx": "command+i"
        },
        "MARK_OUT": {
            "windows": "ctrl+o",
            "osx": "command+o"
        },
        "MARK_IN_INPUT": {
            "windows": "ctrl+alt+i",
            "osx": "command+alt+i"
        },
        "MARK_OUT_INPUT": {
            "windows": "ctrl+alt+o",
            "osx": "command+alt+o"
        },
        "PREV_FRAME": {
            "windows": "ctrl+left",
            "osx": "command+left"
        },
        "NEXT_FRAME": {
            "windows": "ctrl+right",
            "osx": "command+right"
        },
        "SUBMIT_JOB": {
            "windows": "ctrl+s",
            "osx": "command+s"
        },
        "PLAY": {
            "windows": "ctrl+alt+p",
            "osx": "command+alt+p"
        },
        "PLAY_PREVIEW": {
            "windows": "ctrl+p",
            "osx": "command+p"
        },
        "MUTE": {
            "windows": "ctrl+m",
            "osx": "command+m"
        },
        "OUTPUT_TYPE": {
            "windows": "ctrl+y",
            "osx": "command+y"
        },
        "CLEAN_JOB": {
            "windows": "ctrl+alt+d",
            "osx": "command+alt+backspace"
        },
        "OPEN_ADVANCED_SETTINGS": {
            "windows": "ctrl+.",
            "osx": "command+."
        },
        "CLOSE_MODAL": "escape",
        "REMOVE_ASSET": {
            "windows": "ctrl+d",
            "osx": "command+backspace"
        },
        "ZOOM_LEFT_LESS": {
            "windows": "ctrl+shift+[",
            "osx": "command+shift+["
        },
        "ZOOM_LEFT_MORE": {
            "windows": "ctrl+[",
            "osx": "command+["
        },
        "ZOOM_RIGHT_LESS": {
            "windows": "ctrl+shift+]",
            "osx": "command+shift+]"
        },
        "ZOOM_RIGHT_MORE": {
           "windows": "ctrl+]",
            "osx": "command+]"
        }
    }
}
```
