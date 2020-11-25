---
title: ta med fil
description: ta med fil
services: iot-accelerators
author: avneet723
ms.service: iot-accelerators
ms.topic: include
ms.date: 01/17/2019
ms.author: avneet723
ms.custom: include file
ms.openlocfilehash: 1f567b3d083853f9bb342bfad462e8545caa6480
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96020268"
---
## <a name="download-the-source-code"></a>Ladda ned käll koden

Käll kods databaserna för fjärrövervakning innehåller käll koden och de Docker-konfigurationsfiler som du behöver för att köra Docker-avbildningarna för mikrotjänster.

Om du vill klona och skapa en lokal version av lagrings platsen använder du kommando rads miljön för att navigera till en lämplig mapp på den lokala datorn. Kör sedan någon av följande kommando uppsättningar för att klona .NET-databasen:

För att ladda ned den senaste versionen av implementeringarna av .NET-mikrotjänster, kör:

```cmd/sh
git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet.git

# To retrieve the latest submodules, run the following command:

cd azure-iot-pcs-remote-monitoring-dotnet
git submodule foreach git pull origin master
```

> [!NOTE]
> De här kommandona hämtar käll koden för alla mikrotjänster utöver de skript som du använder för att köra mikrotjänster lokalt. Även om du inte behöver käll koden för att köra mikrotjänster i Docker är käll koden användbar om du senare planerar att ändra lösnings Accelerator och testa ändringarna lokalt.

## <a name="deploy-the-azure-services"></a>Distribuera Azure-tjänsterna

Även om den här artikeln visar hur du kör mikrotjänster lokalt, är de beroende av Azure-tjänster som körs i molnet. Använd följande skript för att distribuera Azure-tjänsterna. I följande skript exempel förutsätter vi att du använder .NET-databasen på en Windows-dator. Om du arbetar i en annan miljö kan du justera Sök vägarna, fil namns tilläggen och Sök vägs avgränsarna på rätt sätt.

### <a name="create-new-azure-resources"></a>Skapa nya Azure-resurser

Om du ännu inte har skapat de nödvändiga Azure-resurserna följer du dessa steg:

1. I din kommando rads miljö navigerar du till mappen **\services\scripts\local\launch** i din klonade kopia av lagrings platsen.

1. Kör följande kommandon för att installera **PC** CLI-verktyget och logga in på ditt Azure-konto:

    ```cmd
    npm install -g iot-solutions
    pcs login
    ```

1. Kör skriptet **Start. cmd** . I skriptet uppmanas du att ange följande information:
   * Ett lösnings namn.
   * Den Azure-prenumeration som ska användas.
   * Platsen för det Azure-datacenter som ska användas.

     Skriptet skapar en resurs grupp i Azure med ditt lösnings namn. Den här resurs gruppen innehåller de Azure-resurser som Solution Accelerator använder. Du kan ta bort den här resurs gruppen när du inte längre behöver motsvarande resurser.

     Skriptet lägger också till en uppsättning miljövariabler med **ett prefix på** den lokala datorn. De här miljövariablerna tillhandahåller information för fjärrövervakning för att kunna läsa från en Azure Key Vault-resurs. Den här Key Vault resursen är den plats där fjärrövervakningen kommer att läsa konfigurations värden från.

     > [!TIP]
     > När skriptet har slutförts sparas även miljövariablerna i en fil med namnet **\<your home folder\> \\ . PCs. \\ \<solution name\> Kuvert**. Du kan använda dem för framtida distributioner av Solution Accelerator. Observera att alla miljövariabler som angetts på den lokala datorn åsidosätter värden i den **\\ \\ lokala \\ . kuvert** filen för Services-skript när du kör **Docker-sammanställning**.

1. Avsluta från kommando rads miljön.

### <a name="use-existing-azure-resources"></a>Använd befintliga Azure-resurser

Om du redan har skapat de nödvändiga Azure-resurserna skapar du motsvarande miljövariabler på den lokala datorn.
Ange miljövariabler för följande:
* **PCS_KEYVAULT_NAME** -namnet på den Azure Key Vault resursen
* **PCS_AAD_APPID** -ID för AAD-program
* **PCS_AAD_APPSECRET** – AAD-programmets hemlighet

Konfigurations värden kommer att läsas från den här Azure Key Vault resursen. Dessa miljövariabler kan sparas i filen **\<your home folder\> \\ . PCs \\ \<solution name\> . miljö** från distributionen. Observera att miljövariabler som har angetts på den lokala datorn åsidosätter värden i den **\\ \\ lokala \\ . kuvert** filen för Services-skript när du kör **Docker-sammanställning**.

En del av konfigurationen som krävs av mikrotjänsten lagras i en instans av **Key Vault** som skapades vid den första distributionen. Motsvarande variabler i nyckel valvet bör ändras efter behov.