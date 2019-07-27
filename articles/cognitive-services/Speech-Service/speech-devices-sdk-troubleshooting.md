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
ms.openlocfilehash: 9a757402360b4b69e760315a809482be9b4e04d4
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68558899"
---
# <a name="troubleshoot-the-speech-devices-sdk"></a>Felsöka Speech Devices SDK

Den här artikeln innehåller information som hjälper dig att lösa problem som kan uppstå när du använder tal enheter SDK.

## <a name="certificate-failures"></a>Certifikat-fel

Om du får certifikat fel när du använder tal tjänsterna kontrollerar du att enheten har rätt datum och tid:

1. Gå till **inställningar**. Under **System**väljer **datum och tid**.

    ![Under inställningar, väljer du datum och tid](media/speech-devices-sdk/qsg-12.png)

1. Behåll den **automatisk datum och tid** alternativ som valts. Under **väljer tidszon**, Välj den aktuella tidszonen.

    ![Välj alternativ för datum och tidszon](media/speech-devices-sdk/qsg-13.png)

    När du ser att dev-paket tiden matchar tiden på datorn, är dev-paket ansluten till internet.

## <a name="next-steps"></a>Nästa steg

* [Granska viktig information](devices-sdk-release-notes.md)
