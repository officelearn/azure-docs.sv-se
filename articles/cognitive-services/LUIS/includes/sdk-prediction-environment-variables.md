---
title: ta med fil
description: ta med fil
ms.topic: include
ms.date: 01/23/2020
ms.openlocfilehash: a7f4e0b47b3aaf9924f8c82eb6a9fa37decc0b2a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76772446"
---
### <a name="create-an-environment-variable"></a>Skapa en miljövariabel

Med hjälp av körningsnyckeln och slutpunkten för körning skapar du miljövariabler för autentisering och åtkomst:

* `LUIS_RUNTIME_KEY`- Resursnyckeln för körning för att autentisera dina begäranden.
* `LUIS_RUNTIME_ENDPOINT`- Slutpunkten för körning som är associerad med nyckeln.
* `LUIS_APP_ID`- Det offentliga LUIS IoT-app-ID:et är `df67dcdb-c37d-46af-88e1-8b97951ca1c2`.
* `LUIS_APP_SLOT_NAME` - `production`Eller`staging`

Om du tänker använda den här snabbstarten för att komma åt din egen app måste du vidta ytterligare åtgärder:
* Skapa appen och hämta app-ID
* Tilldela körningsnyckeln till appen i LUIS-portalen
* Testa webbadressen med webbläsaren, som du kan komma åt appen

Följ instruktionerna för operativsystemet.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx LUIS_RUNTIME_KEY <replace-with-your-resource-key>
setx LUIS_RUNTIME_ENDPOINT <replace-with-your-resource-endpoint>
setx LUIS_APP_ID <replace-with-your-app-id>
setx LUIS_APP_SLOT_NAME <replace-with-production-or-staging>
```

När du har lagt till miljövariablerna startar du om konsolfönstret.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export LUIS_RUNTIME_KEY= <replace-with-your-resource-key>
export LUIS_RUNTIME_ENDPOINT= <replace-with-your-resource-endpoint>
export LUIS_APP_ID= <replace-with-your-app-id>
export LUIS_APP_SLOT_NAME= <replace-with-production-or-staging>
```

När du har lagt till `source ~/.bashrc` miljövariablerna körs du från konsolfönstret för att göra ändringarna gällande.

#### <a name="macos"></a>[Macos](#tab/unix)

Redigera `.bash_profile`din och lägg till miljövariabeln:

```bash
export LUIS_RUNTIME_KEY= <replace-with-your-resource-key>
export LUIS_RUNTIME_ENDPOINT= <replace-with-your-resource-endpoint>
export LUIS_APP_ID= <replace-with-your-app-id>
export LUIS_APP_SLOT_NAME= <replace-with-production-or-staging>
```

När du har lagt till `source .bash_profile` miljövariablerna körs du från konsolfönstret för att göra ändringarna gällande.

***
