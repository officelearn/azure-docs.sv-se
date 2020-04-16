---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: 64eab53d24a14bb51633be3f979db20963677d93
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422057"
---
Hantering av komprimerat ljud implementeras med [GStreamer](https://gstreamer.freedesktop.org). Av licensskäl kompileras inte GStreamer-binärfiler och länkas till Tal-SDK. Utvecklare måste installera flera beroenden och plugins, se [Installera på Windows](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c). Gstreamer-binärfiler måste finnas i systemsökvägen, så att tal-SDK kan läsa in gstreamer-binärfiler under körning. Om tal SDK kan hitta libgstreamer-1.0-0.dll under körning betyder det att gstreamer-binärfilerna finns i systemsökvägen.

