---
title: Felsöka tjänsten Talenheter SDK – Tal
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller information som hjälper dig att lösa problem som kan uppstå när du använder SDK för talenheter.
services: cognitive-services
author: mswellsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: wellsi
ms.openlocfilehash: c590e0972de289a36890a75b220eddbded701ea8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "74815574"
---
# <a name="troubleshoot-the-speech-devices-sdk"></a>Felsöka Speech Devices SDK

Den här artikeln innehåller information som hjälper dig att lösa problem som kan uppstå när du använder SDK för talenheter.

## <a name="certificate-failures"></a>Certifikatfel

Om du får certifikatfel när du använder taltjänsten kontrollerar du att enheten har rätt datum och tid:

1. Gå till **Inställningar**. Under **System**väljer du **Datum & tid**.

    ![Under Inställningar väljer du Datum & tid](media/speech-devices-sdk/qsg-12.png)

1. Behåll alternativet **Automatiskt datum & tid** markerat. Under **Välj tidszon**väljer du din aktuella tidszon.

    ![Välj alternativ för datum och tidszon](media/speech-devices-sdk/qsg-13.png)

    När du ser att dev kit tid matchar tiden på datorn, dev kit är ansluten till Internet.

## <a name="next-steps"></a>Nästa steg

* [Granska viktig information](devices-sdk-release-notes.md)
