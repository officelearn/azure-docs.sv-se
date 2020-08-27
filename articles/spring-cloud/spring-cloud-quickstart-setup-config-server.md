---
title: Snabb start – konfigurera konfigurations servern för Azure våren Cloud
description: Beskriver konfiguration av Azure våren Cloud config server för app-distribution.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/03/2020
ms.custom: devx-track-java
ms.openlocfilehash: 47f74b551919177b13f5a72d6eedeae3c77b9f14
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2020
ms.locfileid: "88951914"
---
# <a name="quickstart-set-up-azure-spring-cloud-configuration-server"></a>Snabb start: Konfigurera Azure våren Cloud Configuration Server

Azure våren Cloud config server är en centraliserad konfigurations tjänst för distribuerade system. Det använder ett lagrings Bart lagrings lager som för närvarande stöder lokal lagring, git och under version.  Konfigurera konfigurations servern för att distribuera mikroservice-appar till Azure våren Cloud.

## <a name="prerequisites"></a>Förutsättningar

* [Installera JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
* [Registrera dig för en Azure-prenumeration](https://azure.microsoft.com/free/)
* Valfritt [Installera Azure CLI-versionen 2.0.67 eller högre](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) och installera Azure våren Cloud-tillägget med kommandot: `az extension add --name spring-cloud`
* Valfritt [Installera Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) och [inloggning](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)

## <a name="azure-spring-cloud-config-server-procedures"></a>Azure våren Cloud config server-procedurer

#### <a name="portal"></a>[Portal](#tab/Azure-portal)

Följande procedur konfigurerar konfigurations servern med hjälp av Azure Portal för att distribuera [Piggymetrics-exemplet](spring-cloud-quickstart-sample-app-introduction.md).

1. Gå till sidan tjänst **Översikt** och välj **konfigurations Server**.

2. I avsnittet **standard plats** ställer du in **URI** till " https://github.com/Azure-Samples/piggymetrics-config ".

3. Tryck på **Apply** (Verkställ) för att spara ändringarna.

    ![Skärm bild av ASC-portalen](media/spring-cloud-quickstart-launch-app-portal/portal-config.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

I följande procedur används Azure CLI för att konfigurera konfigurations servern för att distribuera [Piggymetrics-exemplet](spring-cloud-quickstart-sample-app-introduction.md).

Konfigurera din konfigurations server med platsen för git-lagringsplatsen för projektet:

```azurecli
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/Azure-Samples/piggymetrics-config
```

---

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Bygg och distribuera appar](spring-cloud-quickstart-deploy-apps.md)
