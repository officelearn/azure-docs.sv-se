---
title: Snabbstart – Skapa en Azure-meddelandehubb med Azure CLI | Microsoft-dokument
description: I den här självstudien får du lära dig hur du skapar en Azure-meddelandehubb med Hjälp av Azure CLI.
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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "80082453"
---
# <a name="quickstart-create-an-azure-notification-hub-using-the-azure-cli"></a>Snabbstart: Skapa en Azure-meddelandehubb med Azure CLI

Azure Notification Hubs innehåller en lättanvänd och uppskalad push-motor som gör det möjligt för dig att skicka meddelanden till valfri plattform (iOS, Android, Windows, Kindle, Baidu osv) från valfri serverdel (molnet eller lokalt). Mer information om tjänsten finns i [Vad är Azure Notification Hubs?](notification-hubs-push-notification-overview.md).

I den här snabbstarten skapar du en meddelandehubb med Azure CLI. I det första avsnittet visas steg för att skapa ett namnområde för meddelandehubben och frågeåtkomstprincipinformation för det namnområdet. I det andra avsnittet visas steg för att skapa en meddelandehubb i ett befintligt namnområde.  Du lär dig också hur du skapar en anpassad åtkomstprincip.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Meddelandehubbar kräver version 2.0.67 eller senare av Azure CLI. Kör `az --version` för att hitta den version och beroende bibliotek som är installerade. Information om hur du installerar eller uppgraderar finns i [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="prepare-your-environment"></a>Förbered din miljö

1. Logga in.

   Logga in med kommandot [az login](/cli/azure/reference-index#az-login) om du använder en lokal installation av CLI.

    ```azurecli-interactive
    az login
    ```

    Följ stegen som visas i terminalen för att slutföra autentiseringsprocessen.

2. Installera Azure CLI-tillägget.

   Om du vill köra Azure CLI-kommandon för meddelandehubbar installerar du Azure [CLI-tillägget för meddelandehubbar](/cli/azure/ext/notification-hub/notification-hub).  

    ```azurecli-interactive
    az extension add --name notification-hub
   ```

3. Skapa en resursgrupp.

   Azure-meddelandehubbar, liksom alla Azure-resurser, måste distribueras till en resursgrupp. Resursgrupper gör det enkelt att organisera och hantera relaterade Azure-resurser.

   För den här snabbstarten skapar du en resursgrupp med namnet *spnhubrg* på *eastus-platsen* med följande kommando [för az-gruppge:](/cli/azure/group#az-group-create)

   ```azurecli-interactive
   az group create --name spnhubrg --location eastus
   ```

## <a name="create-a-notification-hub-namespace"></a>Skapa ett namnområde för meddelandehubben

1. Skapa ett namnområde för dina meddelandehubbar

   Ett namnområde innehåller ett eller flera nav och namnet måste vara unikt för alla Azure-prenumerationer.  Om du vill kontrollera tillgängligheten för det angivna tjänstnamnområdet använder du kommandot [kontroll-tillgänglighet för az notification-hub-namespace.](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-check-availability)  Kör kommandot [az notification-hub namespace create](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-create) för att skapa ett namnområde.  

   ```azurecli-interactive
   #check availability
   az notification-hub namespace check-availability --name spnhubns

   #create the namespace
   az notification-hub namespace create --resource-group spnhubrg --name spnhubns  --location eastus --sku Free
   ```

2. Lista nycklar och anslutningssträngar för din namnområdesåtkomstprincip.

   En åtkomstprincip med namnet **RootManageSharedAccessKey** skapas automatiskt för ett nytt namnområde.  Varje åtkomstprincip har två uppsättningar nycklar och anslutningssträngar.  Om du vill visa nycklar och anslutningssträngar för namnområdet kör du kommandot [az notification-hub namespace authorization-rule list-keys.](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys)

   ```azurecli-interactive
   az notification-hub namespace authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --name RootManageSharedAccessKey
   ```

## <a name="create-notification-hubs"></a>Skapa meddelandehubbar

1. Skapa din första meddelandehubb.

   En meddelandehubb kan nu skapas i det nya namnområdet.  Kör kommandot [az notification-hub create](/cli/azure/ext/notification-hub/notification-hub#ext-notification-hub-az-notification-hub-create) för att skapa en meddelandehubb.

   ```azurecli-interactive
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name spfcmtutorial1nhub --location eastus --sku Free
   ```

2. Skapa en andra meddelandehubb.

   Flera meddelandehubbar kan skapas i ett enda namnområde.  Om du vill skapa en andra meddelandehubb i samma namnområde kör du `az notification-hub create` kommandot igen med ett annat navnamn.

   ```azurecli-interactive
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name mysecondnhub --location eastus --sku Free
   ```

## <a name="work-with-access-policies"></a>Arbeta med åtkomstprinciper

1. Skapa en ny auktoriseringsregel för en meddelandehubb.

   En åtkomstprincip skapas automatiskt för varje ny meddelandehubb.  Om du vill skapa och anpassa din egen åtkomstprincip använder du kommandot [az notification-hub authorization-rule create.](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-create)

   ```azurecli-interactive
   az notification-hub authorization-rule create --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --rights Listen Send
   ```

2. Lista åtkomstprinciper för en meddelandehubb.

   Om du vill fråga vilka åtkomstprinciper som finns för en meddelandehubb använder du kommandot [az notification-hub authorization-rule list.](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list)

   ```azurecli-interactive
   az notification-hub authorization-rule list --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --output table
   ```

   > [!IMPORTANT]
   > Använd inte standardprincipen **FullSharedAccessSignature** i ditt program. Detta är endast avsett att användas i din serverdel.  Använd endast **lyssna** åtkomstprinciper i klientprogrammet.

3. Lista nycklar och anslutningssträngar för en åtkomstprincip för meddelandehubben

   Det finns två uppsättningar nycklar och anslutningssträngar för varje åtkomstprincip.  Du behöver dem senare för att hantera push-meddelanden.  Om du vill visa nycklar och anslutningar för en åtkomstprincip för meddelandehubben använder du kommandot [az notification-hub authorization-rule list-keys.](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys)

   ```azurecli-interactive
   #query the keys and connection strings for DefaultListenSharedAccessSignature
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name DefaultListenSharedAccessSignature --output json

   #query the keys and connection strings for the custom policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --output table
   ```

   > [!NOTE]
   > Ett [namnområde för meddelandehubben](/cli/azure/ext/notification-hub/notification-hub/namespace/authorization-rule#ext-notification-hub-az-notification-hub-namespace-authorization-rule-list-keys) och en [meddelandehubb](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) har separata åtkomstprinciper.  Kontrollera att du använder rätt Azure CLI-referens när du frågar efter nycklar och anslutningssträngar.

## <a name="clean-up-resources"></a>Rensa resurser

När det inte längre behövs använder du kommandot [az-grupp delete](/cli/azure/group) för att ta bort resursgruppen och alla relaterade resurser.

```azurecli-interactive
az group delete --name spnhubrg
```

## <a name="see-also"></a>Se även

Upptäck fullständiga funktioner för att hantera meddelandehubbar med Azure CLI.

* [Meddelandehubbar fullständig Azure CLI-referenslista](/cli/azure/ext/notification-hub/notification-hub)
* [Namnområde för Meddelandehubbar Azure CLI-referenslista](/cli/azure/ext/notification-hub/notification-hub/namespace)
* [Auktoriseringsregel för Meddelandehubbar Azure CLI-referenslista](/cli/azure/ext/notification-hub/notification-hub/authorization-rule)
* [Referenslista för Meddelandehubbar](/cli/azure/ext/notification-hub/notification-hub/credential)
