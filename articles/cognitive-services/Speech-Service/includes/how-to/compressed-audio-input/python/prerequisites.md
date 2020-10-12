---
author: amitkumarshukla
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: amishu
ms.openlocfilehash: a715d0ece88cf2caf1cb3d20d703f550353094c5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87282759"
---
Hantering av komprimerat ljud implementeras med [`GStreamer`](https://gstreamer.freedesktop.org) . Binärfiler för licens skäl `GStreamer` kompileras inte och länkas inte till tal-SDK: n. Utvecklare måste installera flera beroenden och plugin-program, se [installera på Windows](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c). `GStreamer` binärfiler måste finnas i System Sök vägen, så att tal-SDK kan läsa in binärfilerna under körning. Om tal-SDK: n kan hittas `libgstreamer-1.0-0.dll` under körningen, innebär det att binärfilerna finns i System Sök vägen.

