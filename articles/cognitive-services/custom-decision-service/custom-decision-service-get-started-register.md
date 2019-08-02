---
title: Registrera ditt program – Custom Decision Service
titlesuffix: Azure Cognitive Services
description: En steg-för-steg-guide om hur du registrerar en ny app med Azure Custom Decision Service.
services: cognitive-services
author: slivkins
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: slivkins
ms.reviewer: marcozo
ROBOTS: NOINDEX
ms.openlocfilehash: 4a8e4e91e768e3ad28ea9ff88c18515db4a40323
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704556"
---
# <a name="register-your-application"></a>Registrera ditt program

Om du vill använda Custom Decision Service för ditt program kan du registrera det på portalen. I den här artikeln förklaras hur.

1. Gå till den [första sidan](https://portal.ds.microsoft.com/) i Custom Decision service. I menyfliksområdet klickar du på **min Portal**som marker ATS i bilden:

    ![Min Portal](./media/portal.png)

    Om du inte redan har loggat in uppmanas du att logga in med din [Microsoft-konto](https://account.microsoft.com/account)i portalen. När du har loggat in visas Microsoft-konto i det övre högra hörnet på sidan.

2. Registrera ditt program genom att klicka på knappen **ny app** .

3. I dialog rutan väljer du ett app-ID för programmet. Custom Decision Service kräver ett unikt ID för varje program. Om någon annan redan har tagit detta ID ber systemet dig att välja ett annat.

4. Ange ett API för åtgärds uppsättning. Den här inställningen är en RSS-eller Atom-feed som förmedlar det tillgängliga innehållet för ditt program att Custom Decision Service. Ange ett namn för feeden och ange den URL som den betjänas från. Om du vill göra det senare klickar du på feeds-knappen och sedan på knappen **ny feed** . Ett exempel som skapar en RSS-feed beskrivs senare.

5. Registrera ditt program genom att markera kryss rutan **anpassad app** i det nedre vänstra hörnet. Ange en [anslutnings sträng](../../storage/common/storage-configure-connection-string.md) för det Azure Storage-konto där dina program data loggas. Mer information om hur du skapar ett lagrings konto finns i [skapa, hantera eller ta bort ett lagrings konto](../../storage/common/storage-create-storage-account.md).

### <a name="next-steps"></a>Nästa steg

* Kom igång med att optimera [en webbsida](custom-decision-service-get-started-browser.md) eller [en smartphone-app](custom-decision-service-get-started-app.md).
* Arbeta i en [självstudie](custom-decision-service-tutorial-news.md) för ett mer djupgående exempel.
* Mer information om de tillhandahållna funktionerna finns i [API-referensen](custom-decision-service-api-reference.md).
