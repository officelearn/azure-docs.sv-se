---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 367ae27a6afec803d2e3f98f54bdcf852330ddc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "70274563"
---
## <a name="configure-an-environment-variable-for-authentication"></a>Konfigurera en miljövariabel för autentisering

Program måste autentisera åtkomsten till de Kognitiva tjänster de använder. För att autentisera rekommenderar vi att du skapar en miljövariabel för att lagra nycklarna till dina Azure-resurser. 

När du har nyckeln skriver du den till en ny miljövariabel på den lokala datorn som kör programmet. Konfigurera miljövariabeln genom att öppna ett konsolfönster och följa anvisningarna för ditt operativsystem. Ersätt `your-key` med en av nycklarna för din resurs.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx COGNITIVE_SERVICE_KEY "your-key"
```

När du har lagt till miljövariabeln kan du behöva starta om alla program som körs och som behöver läsa in miljövariabeln, däribland konsolfönstret. Om du till exempel använder Visual Studio som redigerare startar du om Visual Studio innan du kör exemplet.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export COGNITIVE_SERVICE_KEY=your-key
```

När du har lagt till miljövariabeln så kör `source ~/.bashrc` från konsolfönstret så att ändringarna träder i kraft.

#### <a name="macos"></a>[Macos](#tab/unix)

Redigera din .bash_profile och lägg till miljövariabeln:

```bash
export COGNITIVE_SERVICE_KEY=your-key
```

När du har lagt till miljövariabeln så kör `source .bash_profile` från konsolfönstret så att ändringarna träder i kraft.

***