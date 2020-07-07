---
title: 'Självstudie: starta den fördjupade läsaren med exemplet Swift iOS Code'
titleSuffix: Azure Cognitive Services
description: I den här självstudien konfigurerar och kör du ett exempel på Swift-program som startar den fördjupade läsaren.
services: cognitive-services
author: dylankil
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 06/10/2020
ms.author: dylankil
ms.openlocfilehash: a9259026747102dd65be3bb8da853735098667db
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2020
ms.locfileid: "86050178"
---
# <a name="tutorial-launch-the-immersive-reader-using-the-swift-ios-code-sample"></a>Självstudie: starta den fördjupade läsaren med exemplet Swift iOS Code

I [översikten](./overview.md)har du lärt dig om vad den fördjupade läsaren är och hur den implementerar beprövade tekniker för att förbättra läsningen av förståelse för språkstuderande, nya läsare och studenter med inlärnings skillnader. Den här självstudien beskriver hur du skapar ett iOS-program som startar den fördjupade läsaren. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Konfigurera och kör en SWIFT-app för iOS med ett exempel projekt
> * Hämta en åtkomsttoken
> * Starta den fördjupade läsaren med exempel innehåll

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Krav

* En fördjupad läsar resurs som kon figurer ATS för Azure Active Directory autentisering. Följ [dessa instruktioner](./how-to-create-immersive-reader.md) för att konfigurera. Du behöver några av de värden som skapas här när du konfigurerar miljö egenskaperna. Spara utdata från sessionen i en textfil för framtida bruk.
* [macOS](https://www.apple.com/macos)
* [Git](https://git-scm.com/)
* [SDK för avancerad läsare](https://github.com/microsoft/immersive-reader-sdk)
* [Xcode](https://apps.apple.com/us/app/xcode/id497799835?mt=12)

## <a name="configure-authentication-credentials"></a>Konfigurera autentiseringsuppgifter för autentisering

1. Öppna Xcode och öppna **Immersive-Reader-SDK/JS/samples/iOS/QuickStart-Swift/QuickStart-Swift. XCODEPROJ**.
2. I den översta menyn klickar du på **produkt > schema > Redigera schema...**
3. I **körnings** vyn klickar du på fliken **argument** .
4. I avsnittet **miljövariabler** lägger du till följande namn och värden, och anger de värden som angavs när du skapade din fördjupade läsare-resurs.

```text
TENANT_ID=<YOUR_TENANT_ID>
CLIENT_ID=<YOUR_CLIENT_ID>
CLIENT_SECRET<YOUR_CLIENT_SECRET>
SUBDOMAIN=<YOUR_SUBDOMAIN>
```

Se till att du inte genomför ovanstående ändring i käll kontrollen eftersom den innehåller hemligheter som inte bör göras offentliga.

## <a name="launch-the-immersive-reader-with-sample-content"></a>Starta den fördjupade läsaren med exempel innehåll

1. I Xcode trycker du på **CTRL-R** för att köra projektet.

## <a name="next-steps"></a>Nästa steg

* Utforska SDK: [n för avancerad läsare](https://github.com/microsoft/immersive-reader-sdk) och [Avancerad läsare SDK-referens](./reference.md)
* Visa kod exempel på [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/)
