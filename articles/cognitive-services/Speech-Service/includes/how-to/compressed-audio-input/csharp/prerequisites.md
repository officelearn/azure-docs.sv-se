---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: 64eab53d24a14bb51633be3f979db20963677d93
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "81422057"
---
Hantering av komprimerat ljud implementeras med [gstreamer](https://gstreamer.freedesktop.org). Av licens skäl GStreamer binärfiler inte kompileras och länkas till tal-SDK: n. Utvecklare måste installera flera beroenden och plugin-program, se [installera på Windows](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c). Gstreamer-binärfiler måste finnas i System Sök vägen, så att tal-SDK kan läsa in gstreamer-binärfiler under körning. Om tal-SDK kan hitta libgstreamer-1.0 -0. dll under körningen innebär det att binärfilerna för gstreamer finns i System Sök vägen.

