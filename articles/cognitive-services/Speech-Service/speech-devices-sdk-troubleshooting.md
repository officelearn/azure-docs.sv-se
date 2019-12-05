---
title: Fel sökning av tal enheter SDK-Speech service
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller information som hjälper dig att lösa problem som kan uppstå när du använder tal enheter SDK.
services: cognitive-services
author: mswellsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: wellsi
ms.openlocfilehash: c590e0972de289a36890a75b220eddbded701ea8
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2019
ms.locfileid: "74815574"
---
# <a name="troubleshoot-the-speech-devices-sdk"></a>Felsöka Speech Devices SDK

Den här artikeln innehåller information som hjälper dig att lösa problem som kan uppstå när du använder tal enheter SDK.

## <a name="certificate-failures"></a>Certifikat-fel

Om du får certifikat fel när du använder tal tjänsten kontrollerar du att enheten har rätt datum och tid:

1. Gå till **Inställningar**. Under **System**väljer **datum och tid**.

    ![Under inställningar, väljer du datum och tid](media/speech-devices-sdk/qsg-12.png)

1. Behåll den **automatisk datum och tid** alternativ som valts. Under **väljer tidszon**, Välj den aktuella tidszonen.

    ![Välj alternativ för datum och tidszon](media/speech-devices-sdk/qsg-13.png)

    När du ser att dev-paket tiden matchar tiden på datorn, är dev-paket ansluten till internet.

## <a name="next-steps"></a>Nästa steg

* [Granska viktig information](devices-sdk-release-notes.md)
