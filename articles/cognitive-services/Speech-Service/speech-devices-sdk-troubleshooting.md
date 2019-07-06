---
title: Felsökning av tal enheter SDK – Speech Services
titleSuffix: Azure Cognitive Services
description: 'Den här artikeln innehåller information som hjälper dig att lösa problem som kan uppstå när du använder SDK: N för tal-enheter.'
services: cognitive-services
author: mswellsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: wellsi
ms.openlocfilehash: f55171a177dfcbebb9bc6df5ce125a8f29494946
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606304"
---
# <a name="troubleshoot-the-speech-devices-sdk"></a>Felsöka Speech Devices SDK

Den här artikeln innehåller information som hjälper dig att lösa problem som kan uppstå när du använder SDK: N för tal-enheter.

## <a name="certificate-failures"></a>Certifikat-fel

Om det uppstår fel certifikat när du använder Speech Services kan du kontrollera att du använder rätt datum och tid:

1. Gå till **inställningar**. Under **System**väljer **datum och tid**.

    ![Under inställningar, väljer du datum och tid](media/speech-devices-sdk/qsg-12.png)

1. Behåll den **automatisk datum och tid** alternativ som valts. Under **väljer tidszon**, Välj den aktuella tidszonen.

    ![Välj alternativ för datum och tidszon](media/speech-devices-sdk/qsg-13.png)

    När du ser att dev-paket tiden matchar tiden på datorn, är dev-paket ansluten till internet.

## <a name="next-steps"></a>Nästa steg

* [Granska viktig information](devices-sdk-release-notes.md)
