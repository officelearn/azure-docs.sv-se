---
title: inkludera fil
description: inkludera fil
services: cognitive-services
author: roy-har
manager: diberry
ms.service: cognitive-services
ms.date: 06/03/2020
ms.subservice: language-understanding
ms.topic: include
ms.custom: include file
ms.author: roy-har
ms.openlocfilehash: 8e67a6d0c98a3839922a79e9b452465087da1b69
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "84418075"
---
1. Välj [pizza-app-for-luis-v6.jspå](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-app-for-luis-v6.json) för att öppna filens GitHub-sida `pizza-app-for-luis.json` .
1. Högerklicka eller länge knacka på knappen **RAW** och välj **Spara länk som** för att spara på `pizza-app-for-luis.json` din dator.
1. Logga in på [Luis-portalen](https://www.luis.ai).
1. Välj [Mina appar](https://www.luis.ai/applications).
1. På sidan **Mina appar** väljer du **+ ny app för konversation**.
1. Välj **Importera som JSON**.
1. I dialog rutan **Importera ny app** väljer du knappen **Välj fil** .
1. Välj den `pizza-app-for-luis.json` fil som du laddade ned och välj sedan **Öppna**.
1. I fältet **Importera ny app** dialog **namn** anger du ett namn för din pizza-app och väljer sedan knappen **OK** .

Appen kommer att importeras.

Om du ser dialog rutan **skapa en effektiv Luis-app**stänger du dialog rutan.

## <a name="train-and-publish-the-pizza-app"></a>Träna och publicera pizza-appen

Sidan **avsikter** bör visas med en lista över syftet i pizza-appen.

[!INCLUDE [How to train](howto-train.md)]

[!INCLUDE [How to publish](howto-publish.md)]

Din pizza-app är nu redo att användas.

## <a name="record-the-app-id-prediction-key-and-prediction-endpoint-of-your-pizza-app"></a>Registrera app-ID, förutsägelse nyckel och slut punkt för förutsägelse för din pizza-app

Om du vill använda den nya pizza-appen behöver du app-ID, förutsägelse nyckel och förutsägelse slut punkt för din pizza-app.

Så här hittar du dessa värden:

1. Välj **Hantera**på sidan **avsikter** .
1. På sidan **program inställningar** registrerar du **app-ID: t**.
1. Välj **Azure-resurser**.
1. På sidan **Azure-resurser** registrerar du den **primära nyckeln**. Det här värdet är din förutsägelse nyckel.
1. Registrera **slut punkts-URL: en**. Det här värdet är din förutsägelse slut punkt.
