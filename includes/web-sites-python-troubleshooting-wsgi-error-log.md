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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67187947"
---
Om Python stöter på ett fel när programmet startas returneras endast en enkel felsida (t.ex.

Så här fångar du python-programfel:

1. Välj **Inställningar**i webbappen i Azure-portalen .
2. Välj **Programinställningar**på fliken **Inställningar** .
3. Under **Appinställningar**anger du följande nyckel-/värdepar:
    * Nyckel : WSGI_LOG
    * Värde : D:\home\site\wwwroot\logs.txt (ange ditt val av filnamn)

Du bör nu se fel i filen logs.txt i wwwroot-mappen.
