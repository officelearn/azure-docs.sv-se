---
title: 'Självstudie: starta den fördjupade läsaren med Android-kod exempel'
titleSuffix: Azure Cognitive Services
description: I den här självstudien får du konfigurera och köra ett Android-exempelprogram som startar den fördjupade läsaren.
services: cognitive-services
author: dylankil
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 06/10/2020
ms.author: dylankil
ms.openlocfilehash: d847b4ab9f3c06634390e1f67dfed36df938c3ad
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2020
ms.locfileid: "86050174"
---
# <a name="tutorial-launch-the-immersive-reader-using-the-android-java-code-sample"></a>Självstudie: starta den fördjupade läsaren med hjälp av Android Java Code-exemplet

I [översikten](./overview.md)har du lärt dig om vad den fördjupade läsaren är och hur den implementerar beprövade tekniker för att förbättra läsningen av förståelse för språkstuderande, nya läsare och studenter med inlärnings skillnader. Den här självstudien beskriver hur du skapar ett Android-program som startar den fördjupade läsaren. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Konfigurera och köra en app för Android med ett exempel projekt
> * Hämta en åtkomsttoken
> * Starta den fördjupade läsaren med exempel innehåll

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Krav

* En fördjupad läsar resurs som kon figurer ATS för Azure Active Directory autentisering. Följ [dessa instruktioner](./how-to-create-immersive-reader.md) för att konfigurera. Du behöver några av de värden som skapas här när du konfigurerar miljö egenskaperna. Spara utdata från sessionen i en textfil för framtida bruk.
* [Git](https://git-scm.com/)
* [SDK för avancerad läsare](https://github.com/microsoft/immersive-reader-sdk)
* [Android Studio](https://developer.android.com/studio)

## <a name="configure-authentication-credentials"></a>Konfigurera autentiseringsuppgifter för autentisering

1. Starta Android Studio och öppna projektet från mappen **Avancerad-Reader-SDK/JS/samples/starter-Java-Android** -katalog (Java) eller **Avancerad-Reader-SDK/JS/samples/starter-Kotlin** Directory (Kotlin).

2. Skapa en fil med namnet **Kuvert** i mappen **/assets** och Lägg till följande, ange värden efter behov. Se till att du inte utför den här filen i käll kontrollen eftersom den innehåller hemligheter som inte bör göras offentliga.

```text
TENANT_ID=<YOUR_TENANT_ID>
CLIENT_ID=<YOUR_CLIENT_ID>
CLIENT_SECRET=<YOUR_CLIENT_SECRET>
SUBDOMAIN=<YOUR_SUBDOMAIN>
```

## <a name="launch-the-immersive-reader-with-sample-content"></a>Starta den fördjupade läsaren med exempel innehåll

1. Välj en enhets emulator från AVD Manager och kör projektet.

## <a name="next-steps"></a>Nästa steg

* Utforska SDK: [n för avancerad läsare](https://github.com/microsoft/immersive-reader-sdk) och [Avancerad läsare SDK-referens](./reference.md)
* Visa kod exempel på [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/)