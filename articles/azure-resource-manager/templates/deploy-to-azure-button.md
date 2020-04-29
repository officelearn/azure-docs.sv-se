---
title: Knappen Distribuera till Azure
description: Använd knappen för att distribuera Azure Resource Manager mallar från en GitHub-lagringsplats.
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 88436eac970b252d7b0bc7bccee4131e06e9e0cf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "77109044"
---
# <a name="use-a-deployment-button-to-deploy-templates-from-github-repository"></a>Använd en distributions knapp för att distribuera mallar från GitHub-lagringsplatsen

Den här artikeln beskriver hur du använder knappen **distribuera till Azure** för att distribuera mallar från en GitHub-lagringsplats. Du kan lägga till knappen direkt i README.md-filen i din GitHub-lagringsplats eller på en webb sida som hänvisar till lagrings platsen.

## <a name="use-common-image"></a>Använd gemensam avbildning

Om du vill lägga till knappen på din webb sida eller lagrings plats använder du följande bild:

```html
<img src="https://aka.ms/deploytoazurebutton"/>
```

Bilden visas som:

![Knappen Distribuera till Azure](https://aka.ms/deploytoazurebutton)

## <a name="create-url-for-deploying-template"></a>Skapa URL för distribution av mall

Om du vill skapa en URL för mallen börjar du med den råa URL: en till mallen i din lagrings platsen:

```html
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Sedan kodas URL-adressen. Du kan använda en online-kodare eller köra ett kommando. I följande PowerShell-exempel visas hur du kodar ett värde i URL-adressen.

```powershell
[uri]::EscapeDataString($url)
```

Exempel-URL: en har följande värde när URL: en är kodad.

```html
https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json
```

Varje länk börjar med samma bas-URL:

```html
https://portal.azure.com/#create/Microsoft.Template/uri/
```

Lägg till länken URL-kodad mall i slutet av bas-URL: en.

```html
https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json
```

Du har en fullständig URL för länken.

## <a name="create-deploy-to-azure-button"></a>Knappen Skapa distribuera till Azure

Lägg slutligen till länken och bilden tillsammans.

Om du vill lägga till knappen med markdown i README.md-filen på GitHub-lagringsplatsen eller på en webb sida använder du:

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

Om du vill testa den fullständiga lösningen väljer du följande knapp:

[![Distribuera till Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json)

Portalen visar ett fönster som gör att du enkelt kan ange parameter värden. Parametrarna fylls i automatiskt med standardvärdena från mallen.

![Använda portalen för att distribuera](./media/deploy-to-azure-button/portal.png)

## <a name="next-steps"></a>Nästa steg

- Mer information om mallar finns i [förstå strukturen och syntaxen för Azure Resource Manager mallar](template-syntax.md).
