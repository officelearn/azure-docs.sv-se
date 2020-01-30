---
title: ta med fil
description: ta med fil
ms.topic: include
ms.date: 01/23/2020
ms.openlocfilehash: a7f4e0b47b3aaf9924f8c82eb6a9fa37decc0b2a
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2020
ms.locfileid: "76772446"
---
### <a name="create-an-environment-variable"></a>Skapa en miljö variabel

Använd din körnings nyckel och runtime-slutpunkten och skapa miljövariabler för autentisering och åtkomst:

* `LUIS_RUNTIME_KEY`-resurs nyckeln för körning för att autentisera dina begär Anden.
* `LUIS_RUNTIME_ENDPOINT`-körnings slut punkten som är kopplad till din nyckel.
* `LUIS_APP_ID` – det offentliga LUIS IoT app-ID: t är `df67dcdb-c37d-46af-88e1-8b97951ca1c2`.
* `LUIS_APP_SLOT_NAME` - `production` eller `staging`

Om du tänker använda den här snabb starten för att få åtkomst till din egen app måste du vidta ytterligare åtgärder:
* Skapa appen och hämta app-ID
* Tilldela körnings nyckeln till appen i LUIS-portalen
* Testa URL: en med webbläsaren, som du kan använda för att komma åt appen

Följ anvisningarna för ditt operativ system.

#### <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
setx LUIS_RUNTIME_KEY <replace-with-your-resource-key>
setx LUIS_RUNTIME_ENDPOINT <replace-with-your-resource-endpoint>
setx LUIS_APP_ID <replace-with-your-app-id>
setx LUIS_APP_SLOT_NAME <replace-with-production-or-staging>
```

Starta om konsol fönstret när du har lagt till miljövariablerna.

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export LUIS_RUNTIME_KEY= <replace-with-your-resource-key>
export LUIS_RUNTIME_ENDPOINT= <replace-with-your-resource-endpoint>
export LUIS_APP_ID= <replace-with-your-app-id>
export LUIS_APP_SLOT_NAME= <replace-with-production-or-staging>
```

När du har lagt till miljövariablerna kör du `source ~/.bashrc` från konsol fönstret för att ändringarna ska börja gälla.

#### <a name="macostabunix"></a>[macOS](#tab/unix)

Redigera `.bash_profile`och Lägg till miljövariabeln:

```bash
export LUIS_RUNTIME_KEY= <replace-with-your-resource-key>
export LUIS_RUNTIME_ENDPOINT= <replace-with-your-resource-endpoint>
export LUIS_APP_ID= <replace-with-your-app-id>
export LUIS_APP_SLOT_NAME= <replace-with-production-or-staging>
```

När du har lagt till miljövariablerna kör du `source .bash_profile` från konsol fönstret för att ändringarna ska börja gälla.

***
