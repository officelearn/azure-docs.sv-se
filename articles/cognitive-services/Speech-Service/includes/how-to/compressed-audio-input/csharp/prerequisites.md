---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: 07405ad0da9c9ba280810402a638395a7feb8554
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637297"
---
Hantering av komprimerat ljud implementeras med [GStreamer](https://gstreamer.freedesktop.org). Av licensskäl kompileras inte GStreamer-binärfiler och länkas till Tal-SDK. Utvecklare måste installera flera beroenden och plugins, se [Installera på Windows](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c). Gstreamer-binärfiler måste finnas i systemsökvägen, så att tal-SDK kan läsa in gstreamer-binärfiler under körning. Om tal SDK kan hitta libgstreamer-1.0-0.dll under körning betyder det att gstreamer-binärfilerna finns i systemsökvägen.

