---
title: Snabb start – konfigurera konfigurations servern för Azure våren Cloud
description: Beskriver konfiguration av Azure våren Cloud config server för app-distribution.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 09/08/2020
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 639672bdeff2f833c280a041e497197286c9ff24
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90885708"
---
# <a name="quickstart-set-up-azure-spring-cloud-configuration-server"></a>Snabb start: Konfigurera Azure våren Cloud Configuration Server

Azure våren Cloud config server är en centraliserad konfigurations tjänst för distribuerade system. Det använder ett lagrings Bart lagrings lager som för närvarande stöder lokal lagring, git och under version. I den här snabb starten ställer du in konfigurations servern för att hämta data från en git-lagringsplats.

::: zone pivot="programming-language-csharp"

## <a name="prerequisites"></a>Förutsättningar

* Slutför den tidigare snabb starten i den här serien: [etablera Azure våren Cloud Service](spring-cloud-quickstart-provision-service-instance.md).

## <a name="azure-spring-cloud-config-server-procedures"></a>Azure våren Cloud config server-procedurer

Konfigurera din konfigurations server med platsen för git-lagringsplatsen för projektet genom att köra följande kommando. Ersätt `<service instance name>` med namnet på den tjänst som du skapade tidigare. Standardvärdet för namn på tjänst instans som du angav i föregående snabb start fungerar inte med det här kommandot.

```azurecli
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples --search-paths steeltoe-sample/config
```

Det här kommandot anger konfigurations servern för att hitta konfigurations data i mappen [steeltoe-Sample/config](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/steeltoe-sample/config) i exempel App-lagringsplatsen. Eftersom namnet på den app som ska hämta konfigurations data är är `planet-weather-provider` filen som ska användas [planet-Weather-Provider. yml](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/blob/master/steeltoe-sample/config/planet-weather-provider.yml).

::: zone-end

::: zone pivot="programming-language-java"
Azure våren Cloud config server är en centraliserad konfigurations tjänst för distribuerade system. Det använder ett lagrings Bart lagrings lager som för närvarande stöder lokal lagring, git och under version.  Konfigurera konfigurations servern för att distribuera mikroservice-appar till Azure våren Cloud.

## <a name="prerequisites"></a>Förutsättningar

* [Installera JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable&preserve-view=true)
* [Registrera dig för en Azure-prenumeration](https://azure.microsoft.com/free/)
* Valfritt [Installera Azure CLI-versionen 2.0.67 eller högre](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) och installera Azure våren Cloud-tillägget med kommandot: `az extension add --name spring-cloud`
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
::: zone-end

## <a name="clean-up-resources"></a>Rensa resurser

Hoppa över det här steget om du tänker fortsätta till nästa snabb start i den här serien.

I de här snabb starterna skapade du Azure-resurser som kommer fortsätta att debiteras om de finns kvar i din prenumeration. Om du inte tänker fortsätta till nästa snabb start och du inte förväntar dig att behöva dessa resurser i framtiden, tar du bort resurs gruppen med hjälp av portalen eller genom att köra följande kommando i Cloud Shell:

```azurecli
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

I en tidigare snabb start anger du också standard resurs gruppens namn. Om du inte tänker fortsätta med nästa snabb start tar du bort standardvärdet genom att köra följande CLI-kommando:

```azurecli
az configure --defaults group=
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa och distribuera appar](spring-cloud-quickstart-deploy-apps.md)
