---
title: Knappen Distribuera till Azure
description: Använd knappen för att distribuera Azure Resource Manager-mallar från en GitHub-databas.
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 88436eac970b252d7b0bc7bccee4131e06e9e0cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77109044"
---
# <a name="use-a-deployment-button-to-deploy-templates-from-github-repository"></a>Använda en distributionsknapp för att distribuera mallar från GitHub-databasen

I den här artikeln beskrivs hur du använder knappen **Distribuera till Azure** för att distribuera mallar från en GitHub-databas. Du kan lägga till knappen direkt i README.md-filen i GitHub-databasen eller på en webbsida som refererar till databasen.

## <a name="use-common-image"></a>Använda gemensam bild

Om du vill lägga till knappen på webbsidan eller databasen använder du följande bild:

```html
<img src="https://aka.ms/deploytoazurebutton"/>
```

Bilden visas som:

![Knappen Distribuera till Azure](https://aka.ms/deploytoazurebutton)

## <a name="create-url-for-deploying-template"></a>Skapa URL för distribution av mall

Om du vill skapa URL:en för mallen börjar du med den råa WEBBADRESSEN till mallen i repo:To create the URL for your template, start with the raw URL to the template in your repo:

```html
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Sedan, URL koda den. Du kan använda en onlinekodare eller köra ett kommando. I följande PowerShell-exempel visas hur du kodar ett värde för URL.

```powershell
[uri]::EscapeDataString($url)
```

Exempel-URL:en har följande värde när URL-kodas.

```html
https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json
```

Varje länk börjar med samma grundläggande URL:

```html
https://portal.azure.com/#create/Microsoft.Template/uri/
```

Lägg till url-kodad malllänk i slutet av bas-URL:en.

```html
https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json
```

Du har din fullständiga webbadress för länken.

## <a name="create-deploy-to-azure-button"></a>Knappen Skapa distribuera till Azure

Slutligen, sätta länken och bilden tillsammans.

Om du vill lägga till knappen med Markdown i README.md filen i GitHub-databasen eller på en webbsida använder du:

```markdown
[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json)
```

För HTML använder du:

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json" target="_blank">
  <img src="https://aka.ms/deploytoazurebutton"/>
</a>
```

## <a name="deploy-the-template"></a>Distribuera mallen

Om du vill testa hela lösningen väljer du följande knapp:

[![Distribuera till Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json)

Portalen visar en ruta där du enkelt kan ange parametervärden. Parametrarna är förfyllda med standardvärdena från mallen.

![Använda portalen för att distribuera](./media/deploy-to-azure-button/portal.png)

## <a name="next-steps"></a>Nästa steg

- Mer information om mallar finns [i Förstå strukturen och syntaxen för Azure Resource Manager-mallar](template-syntax.md).
