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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61446173"
---
## <a name="download-the-source-code"></a>Ladda ned källkoden

Fjärrövervakning källkodslager omfattar källkoden och Docker-konfigurationsfiler som du behöver köra mikrotjänster Docker-avbildningar.

Använd din kommandoradsmiljö klona och skapa en lokal version av databasen genom för att navigera till en lämplig mapp på den lokala datorn. Sedan kör något av följande uppsättningar kommandon för att klona antingen .NET-lagringsplatsen:

Om du vill hämta den senaste versionen av .NET mikrotjänst implementeringar, kör du:

```cmd/sh
git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet.git

# To retrieve the latest submodules, run the following command:

cd azure-iot-pcs-remote-monitoring-dotnet
git submodule foreach git pull origin master
```

> [!NOTE]
> Dessa kommandon ladda ned källkoden för alla mikrotjänster utöver de skript som används för att köra mikrotjänster lokalt. Även om du inte behöver källkoden för att köra mikrotjänster i Docker, är källkoden användbart om du senare planerar att ändra solution accelerator och testa ändringarna lokalt.

## <a name="deploy-the-azure-services"></a>Distribuera Azure-tjänster

Även om den här artikeln visar hur du kör mikrotjänster lokalt, de förlitar sig på Azure-tjänster som körs i molnet. Använd följande skript för att distribuera Azure-tjänster. I följande skriptexempel förutsätter att du använder .NET-lagringsplatsen på en Windows-dator. Om du arbetar i en annan miljö, justera de sökvägar, filnamnstillägg, och avgränsarna på rätt sätt.

### <a name="create-new-azure-resources"></a>Skapa nya Azure-resurser

Om du ännu inte har skapat de nödvändiga Azure-resurserna, gör du följande:

1. I din kommandoradmiljö, navigerar du till den **\services\scripts\local\launch** mapp i din klonade kopia av databasen.

1. Kör följande kommandon för att installera den **datorer** CLI-verktyget och logga in på ditt Azure-konto:

    ```cmd
    npm install -g iot-solutions
    pcs login
    ```

1. Kör den **start.cmd** skript. Skriptet uppmanas du att följande information:
   * Ett lösningsnamn på.
   * Den Azure-prenumeration som ska användas.
   * Platsen för Azure-datacenter att använda.

     Skriptet skapar en resursgrupp i Azure med Lösningsnamnet på din. Den här resursgruppen innehåller Azure-resurserna solution accelerator använder. Du kan ta bort den här resursgruppen när du behöver inte längre på motsvarande resurser.

     Skriptet lägger också till en uppsättning miljövariabler med prefixet **datorer** till den lokala datorn. Dessa miljövariabler ange uppgifter för fjärrövervakning för att kunna läsa från en Azure Key Vault-resurs. Den här Key Vault-resursen är där fjärrövervakning läsa dess konfigurationsvärden från.

     > [!TIP]
     > När skriptet har körts också sparas miljövariablerna i en fil med namnet  **\<arbetsmappen\>\\.pcs\\\<lösningsnamn\>.env** . Du kan använda dem för framtida solution accelerator distributioner. Observera att alla miljövariabler som anges på den lokala datorn åsidosätta värden i den **services\\skript\\lokala\\.env** filen när du kör **docker-compose**.

1. Avsluta från kommandoraden-miljön.

### <a name="use-existing-azure-resources"></a>Använd befintliga Azure-resurser

Om du redan har skapat resurserna som krävs Azure skapar du motsvarande miljövariabler på den lokala datorn.
Ange miljövariabler för följande:
* **PCS_KEYVAULT_NAME** -namnet på den Azure Key Vault-resursen
* **PCS_AAD_APPID** -ID för det AAD-program
* **PCS_AAD_APPSECRET** -programhemlighet i AAD

Konfigurationsvärden ska läsas från den här Azure Key Vault-resursen. Dessa miljövariabler kan sparas i den  **\<arbetsmappen\>\\.pcs\\\<lösningsnamn\>.env** filen från distributionen. Observera att miljövariabler som anges på den lokala datorn åsidosätta värden i den **services\\skript\\lokala\\.env** filen när du kör **docker-compose**.

Del av konfigurationen som krävs av mikrotjänst lagras i en instans av **Key Vault** som skapades på den första distributionen. De motsvarande variablerna i keyvault ska ändras efter behov.