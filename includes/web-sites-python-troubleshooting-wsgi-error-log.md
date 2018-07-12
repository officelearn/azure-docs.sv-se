---
title: ta med fil
description: ta med fil
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/11/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 5169432af65a046465c9d1ced349687d6fdc8bb7
ms.sourcegitcommit: caebf2bb2fc6574aeee1b46d694a61f8b9243198
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/12/2018
ms.locfileid: "35394034"
---
Om Python påträffar ett fel när ditt program, returneras en enkel felsida (t.ex.) ”Sidan kan inte visas eftersom ett internt serverfel har inträffat”.).

Avbilda Python programfel:

1. Azure-portalen i din webbapp, Välj **inställningar**.
2. På den **inställningar** fliken **programinställningar**.
3. Under **appinställningar**, ange följande nyckel/värde-par:
    * Nyckel: WSGI_LOG
    * Värde: D:\home\site\wwwroot\logs.txt (anger du ditt val av filnamn)

Du bör nu se fel i logs.txt-filen i Wwwroot-mappen.
