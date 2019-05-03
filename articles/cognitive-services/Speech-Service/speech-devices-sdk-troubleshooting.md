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
ms.date: 05/02/2019
ms.author: wellsi
ms.openlocfilehash: 87fb35f06dcb1d1e3fb8c3ae3be64c7448162f14
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026162"
---
# <a name="troubleshoot-the-speech-devices-sdk"></a>Felsökning av tal enheter SDK

Den här artikeln innehåller information som hjälper dig att lösa problem som kan uppstå när du använder SDK: N för tal-enheter.

## <a name="certificate-failures"></a>Certifikat-fel

Om det uppstår fel certifikat när du använder Speech Services kan du kontrollera att du använder rätt datum och tid:

1. Gå till **Inställningar**. Under **System**väljer **datum och tid**.

    ![Under inställningar, väljer du datum och tid](media/speech-devices-sdk/qsg-12.png)

1. Behåll den **automatisk datum och tid** alternativ som valts. Under **väljer tidszon**, Välj den aktuella tidszonen.

    ![Välj alternativ för datum och tidszon](media/speech-devices-sdk/qsg-13.png)

    När du ser att dev-paket tiden matchar tiden på datorn, är dev-paket ansluten till internet.

## <a name="next-steps"></a>Nästa steg

* [Granska viktig information](devices-sdk-release-notes.md)
