---
title: Snabb start – skapa en Azure Notification Hub med hjälp av Azure CLI | Microsoft Docs
description: I den här självstudien får du lära dig hur du skapar en Azure Notification Hub med hjälp av Azure CLI.
services: notification-hubs
author: dbradish-microsoft
manager: barbkess
editor: sethmanheim
ms.service: notification-hubs
ms.devlang: azurecli
ms.workload: mobile
ms.topic: quickstart
ms.date: 03/17/2020
ms.author: dbradish
ms.reviewer: sethm
ms.lastreviewed: 03/18/2020
ms.openlocfilehash: 830fd33e19a10ec6472650e3d26fec677b82c3d7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "80082453"
---
# <a name="quickstart-create-an-azure-notification-hub-using-the-azure-cli"></a>Snabb start: skapa en Azure Notification Hub med Azure CLI

Azure Notification Hubs innehåller en lättanvänd och uppskalad push-motor som gör det möjligt för dig att skicka meddelanden till valfri plattform (iOS, Android, Windows, Kindle, Baidu osv) från valfri serverdel (molnet eller lokalt). Mer information om tjänsten finns i [Vad är Azure Notification Hubs?](notification-hubs-push-notification-overview.md).

I den här snabb starten skapar du en Notification Hub med hjälp av Azure CLI. Det första avsnittet innehåller steg för steg hur du skapar ett namn område för Notification Hub och frågar om åtkomst princip information för det namn området. Det andra avsnittet innehåller steg för steg hur du skapar en Notification Hub i ett befintligt namn område.  Du lär dig också hur du skapar en anpassad åtkomst princip.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Notification Hubs kräver version 2.0.67 eller senare av Azure CLI. Kör `az --version` för att hitta den version och beroende bibliotek som är installerade. Information om hur du installerar eller uppgraderar finns i [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="prepare-your-environment"></a>Förbered din miljö

1. Logga in.

   Logga in med kommandot [AZ login](/cli/azure/reference-index#az-login) om du använder en lokal installation av cli.

    ```azurecli-interactive
    az login
    ```

    Slutför autentiseringsprocessen genom att följa stegen som visas i terminalen.

2. Installera Azure CLI-tillägget.

   Om du vill köra Azure CLI-kommandona för Notification Hub installerar du Azure CLI- [tillägget för Notification Hubs](/cli/azure/ext/notification-hub/notification-hub).  

    ```azurecli-interactive
    az extension add --name notification-hub
   ```

3. Skapa en resursgrupp.

   Azure Notification Hub, som alla Azure-resurser, måste distribueras till en resurs grupp. Resursgrupper gör det enkelt att organisera och hantera relaterade Azure-resurser.

   I den här snabb starten skapar du en resurs grupp med namnet *spnhubrg* på den *östra* platsen med följande [AZ Group Create](/cli/azure/group#az-group-create) -kommando:

   ```azurecli-interactive
   az group create --name spnhubrg --location eastus
   ```

## <a name="create-a-notification-hub-namespace"></a>Skapa ett namn område för Notification Hub

1. Skapa ett namn område för dina Notification Hub

   Ett namn område innehåller ett eller flera hubbar, och namnet måste vara unikt för alla Azure-prenumerationer.  Om du vill kontrol lera tillgängligheten för den aktuella tjänstens namnrymd använder du kommandot [AZ Notification-Hub namn område check-Availability](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-check-availability) .  Kör kommandot [AZ Notification – Hub namespace Create](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-create) för att skapa ett namn område.  

   ```azurecli-interactive
   #check availability
   az notification-hub namespace check-availability --name spnhubns

   #create the namespace
   az notification-hub namespace create --resource-group spnhubrg --name spnhubns  --location eastus --sku Free
   ```

2. Lista nycklar och anslutnings strängar för din namn områdes åtkomst princip.

   En åtkomst princip med namnet **RootManageSharedAccessKey** skapas automatiskt för ett nytt namn område.  Varje åtkomst princip har två uppsättningar nycklar och anslutnings strängar.  Om du vill visa en lista över nycklar och anslutnings strängar för namn området kör du kommandot [AZ Notification-Hub namespace-Rule List-Keys](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) .

   ```azurecli-interactive
   az notification-hub namespace authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --name RootManageSharedAccessKey
   ```

## <a name="create-notification-hubs"></a>Skapa Notification Hub

1. Skapa din första Notification Hub.

   Nu kan du skapa en Notification Hub i det nya namn området.  Kör kommandot [AZ Notification-Hub Create](/cli/azure/ext/notification-hub/notification-hub#ext-notification-hub-az-notification-hub-create) för att skapa en Notification Hub.

   ```azurecli-interactive
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name spfcmtutorial1nhub --location eastus --sku Free
   ```

2. Skapa en andra Notification Hub.

   Flera Notification Hub kan skapas i ett enda namn område.  Om du vill skapa en andra Notification Hub i samma namnrymd kör du `az notification-hub create` kommandot igen med ett annat nav namn.

   ```azurecli-interactive
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name mysecondnhub --location eastus --sku Free
   ```

## <a name="work-with-access-policies"></a>Arbeta med åtkomst principer

1. Skapa en ny Authorization-regel för en Notification Hub.

   En åtkomst princip skapas automatiskt för varje ny Notification Hub.  Om du vill skapa och anpassa din egen åtkomst princip använder du kommandot [AZ Notification-Hub Authorization-Rule Create](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-create) .

   ```azurecli-interactive
   az notification-hub authorization-rule create --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --rights Listen Send
   ```

2. Lista åtkomst principer för en Notification Hub.

   Om du vill fråga vilka åtkomst principer som finns för en Notification Hub använder du kommandot [AZ Notification-Hub Authorization-Rule List](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list) .

   ```azurecli-interactive
   az notification-hub authorization-rule list --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --output table
   ```

   > [!IMPORTANT]
   > Använd inte **DefaultFullSharedAccessSignature** -principen i ditt program. Detta är endast avsett att användas i din serverdel.  Använd bara principer för **lyssnings** åtkomst i klient programmet.

3. Lista nycklar och anslutnings strängar för en åtkomst princip för Notification Hub

   Det finns två uppsättningar nycklar och anslutnings strängar för varje åtkomst princip.  Du behöver dem senare för att hantera push-meddelanden.  Om du vill visa en lista över nycklar och anslutnings strängar för en åtkomst princip för Notification Hub använder du kommandot [AZ Notification-Hub Authorization-Rule List-Keys](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) .

   ```azurecli-interactive
   #query the keys and connection strings for DefaultListenSharedAccessSignature
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name DefaultListenSharedAccessSignature --output json

   #query the keys and connection strings for the custom policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --output table
   ```

   > [!NOTE]
   > Ett [namn område för Notification Hub](/cli/azure/ext/notification-hub/notification-hub/namespace/authorization-rule#ext-notification-hub-az-notification-hub-namespace-authorization-rule-list-keys) och en [Notification Hub](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) har separata åtkomst principer.  Kontrol lera att du använder rätt Azure CLI-referens när du frågar efter nycklar och anslutnings strängar.

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs kan du använda kommandot [AZ Group Delete](/cli/azure/group) för att ta bort resurs gruppen och alla relaterade resurser.

```azurecli-interactive
az group delete --name spnhubrg
```

## <a name="see-also"></a>Se även

Identifiera fullständiga funktioner för att hantera Notification Hub med Azure CLI.

* [Notification Hubs fullständig Azure CLI-referens lista](/cli/azure/ext/notification-hub/notification-hub)
* [Notification Hubs namn område Azure CLI-referens lista](/cli/azure/ext/notification-hub/notification-hub/namespace)
* [Notification Hubs auktoriseringsregel för Azure CLI-referens](/cli/azure/ext/notification-hub/notification-hub/authorization-rule)
* [Notification Hubs referens lista för Azure CLI-autentiseringsuppgifter](/cli/azure/ext/notification-hub/notification-hub/credential)
