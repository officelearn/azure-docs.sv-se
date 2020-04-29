---
title: ta med fil
description: ta med fil
ms.topic: include
ms.date: 01/23/2020
ms.openlocfilehash: a7f4e0b47b3aaf9924f8c82eb6a9fa37decc0b2a
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "76772446"
---
### <a name="create-an-environment-variable"></a>Skapa en miljö variabel

Använd din körnings nyckel och runtime-slutpunkten och skapa miljövariabler för autentisering och åtkomst:

* `LUIS_RUNTIME_KEY`– Körnings resurs nyckel för autentisering av dina begär Anden.
* `LUIS_RUNTIME_ENDPOINT`– Runtime-slutpunkten som är kopplad till din nyckel.
* `LUIS_APP_ID`– Det offentliga LUIS IoT app-ID `df67dcdb-c37d-46af-88e1-8b97951ca1c2`: t är.
* `LUIS_APP_SLOT_NAME` - `production`eller`staging`

Om du tänker använda den här snabb starten för att få åtkomst till din egen app måste du vidta ytterligare åtgärder:
* Skapa appen och hämta app-ID
* Tilldela körnings nyckeln till appen i LUIS-portalen
* Testa URL: en med webbläsaren, som du kan använda för att komma åt appen

Följ anvisningarna för ditt operativ system.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx LUIS_RUNTIME_KEY <replace-with-your-resource-key>
setx LUIS_RUNTIME_ENDPOINT <replace-with-your-resource-endpoint>
setx LUIS_APP_ID <replace-with-your-app-id>
setx LUIS_APP_SLOT_NAME <replace-with-production-or-staging>
```

Starta om konsol fönstret när du har lagt till miljövariablerna.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export LUIS_RUNTIME_KEY= <replace-with-your-resource-key>
export LUIS_RUNTIME_ENDPOINT= <replace-with-your-resource-endpoint>
export LUIS_APP_ID= <replace-with-your-app-id>
export LUIS_APP_SLOT_NAME= <replace-with-production-or-staging>
```

När du har lagt till miljövariablerna `source ~/.bashrc` kör du från konsol fönstret för att ändringarna ska börja gälla.

#### <a name="macos"></a>[macOS](#tab/unix)

Redigera din `.bash_profile`och Lägg till miljövariabeln:

```bash
export LUIS_RUNTIME_KEY= <replace-with-your-resource-key>
export LUIS_RUNTIME_ENDPOINT= <replace-with-your-resource-endpoint>
export LUIS_APP_ID= <replace-with-your-app-id>
export LUIS_APP_SLOT_NAME= <replace-with-production-or-staging>
```

När du har lagt till miljövariablerna `source .bash_profile` kör du från konsol fönstret för att ändringarna ska börja gälla.

***
