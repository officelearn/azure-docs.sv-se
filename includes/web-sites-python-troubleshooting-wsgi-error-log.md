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
ms.openlocfilehash: 020e59f029b09f3c7656f67039731e4141e68d31
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61270209"
---
Om Python påträffar ett fel när ditt program, returneras en enkel felsida (t.ex.) ”Sidan kan inte visas eftersom ett internt serverfel har inträffat”.).

Avbilda Python programfel:

1. Azure-portalen i din webbapp, Välj **inställningar**.
2. På den **inställningar** fliken **programinställningar**.
3. Under **appinställningar**, ange följande nyckel/värde-par:
    * Nyckel: WSGI_LOG
    * Värde: D:\home\site\wwwroot\logs.txt (anger du ditt val av filnamn)

Du bör nu se fel i logs.txt-filen i Wwwroot-mappen.
