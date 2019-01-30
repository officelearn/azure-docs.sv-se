---
title: Registrera ditt program – Custom Decision Service
titlesuffix: Azure Cognitive Services
description: En stegvisa anvisningar för hur du registrerar en ny app med Azure Custom Decision Service.
services: cognitive-services
author: slivkins
manager: cgronlun
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: slivkins
ms.reviewer: marcozo
ms.openlocfilehash: f642e0d4e21c180a92b4b76d05fb2c9bac9f2976
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55219744"
---
# <a name="register-your-application"></a>Registrera ditt program

För att använda Custom Decision Service för ditt program måste du registrera den på portalen. Den här artikeln förklarar hur.

1. Gå till den [förstasida](https://ds.microsoft.com/) av Custom Decision Service. I menyfliksområdet klickar du på **Mina Portal**, som är markerade i bild:

    ![Min Portal](./media/portal.png)

    Om du inte redan är inloggad på portalen uppmanas du att logga in med ditt [microsoftkonto](https://account.microsoft.com/account). När du har loggat in visas på portalen ditt Microsoft-konto i det övre högra hörnet på sidan.

2. Registrera ditt program genom att klicka på den **ny App** knappen.

3. I dialogrutan väljer du ett App-ID för ditt program. Custom Decision Service kräver ett unikt ID för varje program. Om någon annan har redan detta ID, tillfrågas du kan välja en annan enhet.

4. Ange en uppsättning API: et för åtgärden. Den här inställningen är en RSS- eller Atom-feed som kommunicerar material för ditt program till Custom Decision Service. Ange ett namn för flödet och ange URL: en som de har hämtats. Om du vill göra det här steget senare klickar du på den **Feeds** knappen och klicka sedan på den **nytt flöde** knappen. Ett exempel som skapar en RSS-feed beskrivs senare.

5. Om du vill registrera ditt program, Välj den **anpassad App** kryssrutan i det nedre vänstra hörnet. Ange en [anslutningssträngen](../../storage/common/storage-configure-connection-string.md) för Azure storage-konto där dina programdata loggas. Mer information om hur du skapar ett lagringskonto finns i [skapa, hantera eller ta bort ett lagringskonto](../../storage/common/storage-create-storage-account.md).

### <a name="next-steps"></a>Nästa steg

* Kom igång med att optimera [en webbsida](custom-decision-service-get-started-browser.md) eller [en smartphone-app](custom-decision-service-get-started-app.md).
* Gå igenom en [självstudien](custom-decision-service-tutorial-news.md) för en mer ingående exempel.
* Mer information om de tillhandahållna funktionerna finns i [API-referensen](custom-decision-service-api-reference.md).
