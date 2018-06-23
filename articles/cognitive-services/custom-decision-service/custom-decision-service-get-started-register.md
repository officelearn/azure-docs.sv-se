---
title: Registrera ditt program - kognitiva Azure-tjänster | Microsoft Docs
description: En stegvis guide hur du registrerar en ny app med Azure anpassad beslut Service
services: cognitive-services
author: slivkins
manager: slivkins
ms.service: cognitive-services
ms.topic: article
ms.date: 05/09/2018
ms.author: slivkins
ms.reviewer: marcozo;alekh;marossi
ms.openlocfilehash: 2aa8fbe77c11df4434eefa4c92d8529d5ca1d885
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354516"
---
# <a name="register-your-application"></a>Registrera ditt program

För att använda anpassade beslut Service för ditt program måste du registrera den på portalen. Den här artikeln förklarar hur.

1. Gå till den [framsidan](https://ds.microsoft.com/) för anpassad beslut tjänsten. Klicka på menyfliken **Mina Portal**, som är markerade i bild:

    ![Min Portal](./media/portal.png)

    Om du inte redan är inloggad på portalen uppmanas du att logga in med ditt [Microsoft-konto](https://account.microsoft.com/account). När du har registrerat visar portalen ditt Microsoft-konto i det övre högra hörnet på sidan.

2. Registrera ditt program genom att klicka på den **ny App** knappen.

3. I dialogrutan väljer du ett App-ID för ditt program. Anpassade beslut tjänsten kräver ett unikt ID för varje program. Om någon annan har redan vidtagit detta ID, tillfrågas du om du vill välja en annan.

4. Ange en uppsättning API för åtgärden. Den här inställningen är en RSS- eller Atom-feed som kommunicerar tillgängligt innehåll för programmet till anpassad beslut-tjänsten. Ange ett namn för feeden och ange Webbadressen som hanteras. Om du vill göra det här steget senare klickar du på den **Feeds** knappen och klicka sedan på den **ny feed** knappen. Ett exempel som skapar en RSS-feed beskrivs senare.

5. Om du vill registrera ditt program, Välj den **anpassad App** kryssrutan i det nedre vänstra hörnet. Ange en [anslutningssträngen](../../storage/common/storage-configure-connection-string.md) för Azure storage-konto där dina programdata är inloggad. Mer information om hur du skapar ett lagringskonto finns [skapa, hantera eller ta bort ett lagringskonto](../../storage/common/storage-create-storage-account.md).

### <a name="next-steps"></a>Nästa steg

* Komma igång för att optimera [en webbsida](custom-decision-service-get-started-browser.md) eller [en smartphone-app](custom-decision-service-get-started-app.md).
* Gå igenom en [kursen](custom-decision-service-tutorial-news.md) för en mer ingående exempel.
* Läs den [API-referens](custom-decision-service-api-reference.md) lära dig mer om de angivna funktionerna.