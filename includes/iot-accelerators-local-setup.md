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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67187776"
---
## <a name="download-the-source-code"></a>Ladda ner källkoden

Källkodsdatabaserna för fjärrövervakning innehåller källkoden och de Docker-konfigurationsfiler som du behöver för att köra dockeravbildningarna för mikrotjänster.

Om du vill klona och skapa en lokal version av databasen använder du kommandoradsmiljön för att navigera till en lämplig mapp på den lokala datorn. Kör sedan en av följande uppsättningar kommandon för att klona antingen .NET-databasen:

Om du vill hämta den senaste versionen av .NET-mikrotjänstimplementeringarna kör du:

```cmd/sh
git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet.git

# To retrieve the latest submodules, run the following command:

cd azure-iot-pcs-remote-monitoring-dotnet
git submodule foreach git pull origin master
```

> [!NOTE]
> Dessa kommandon hämtar källkoden för alla mikrotjänster utöver de skript som du använder för att köra mikrotjänsterna lokalt. Även om du inte behöver källkoden för att köra mikrotjänsterna i Docker, är källkoden användbar om du senare planerar att ändra lösningsacceleratorn och testa ändringarna lokalt.

## <a name="deploy-the-azure-services"></a>Distribuera Azure-tjänsterna

Även om den här artikeln visar hur du kör mikrotjänsterna lokalt, beror de på Azure-tjänster som körs i molnet. Använd följande skript för att distribuera Azure-tjänsterna. Följande skriptexempel förutsätter att du använder .NET-databasen på en Windows-dator. Om du arbetar i en annan miljö justerar du sökvägarna, filnamnstilläggen och sökvägsavgränsarna på rätt sätt.

### <a name="create-new-azure-resources"></a>Skapa nya Azure-resurser

Om du ännu inte har skapat de nödvändiga Azure-resurserna gör du så här:

1. I kommandoradsmiljön navigerar du till mappen **\services\scripts\local\launch** i den klonade kopian av databasen.

1. Kör följande kommandon för att installera **dators CLI-verktyget** och logga in på ditt Azure-konto:

    ```cmd
    npm install -g iot-solutions
    pcs login
    ```

1. Kör **start.cmd-skriptet.** Skriptet uppmanar dig att ange följande information:
   * Ett lösningsnamn.
   * Den Azure-prenumeration som ska användas.
   * Platsen för Azure-datacentret som ska användas.

     Skriptet skapar resursgrupp i Azure med ditt lösningsnamn. Den här resursgruppen innehåller De Azure-resurser som lösningsacceleratorn använder. Du kan ta bort den här resursgruppen när du inte längre behöver motsvarande resurser.

     Skriptet lägger också till en uppsättning miljövariabler med ett prefix **PCS** till din lokala dator. Dessa miljövariabler innehåller information om fjärrövervakning för att kunna läsa från en Azure Key Vault-resurs. Den här Key Vault-resursen är där fjärrövervakning kommer att läsa dess konfigurationsvärden från.

     > [!TIP]
     > När skriptet är klart sparas också miljövariablerna i en fil som kallas ** \<\>\\lösningsnamnet\\\<\>.pcs .env**. Du kan använda dem för framtida distributioner av lösningsacceleratorer. Observera att alla miljövariabler som anges på din lokala dator åsidosätter värden i **\\tjänstskriptens\\lokala\\.env-fil** när du kör **docker-compose**.

1. Lämna kommandoradsmiljön.

### <a name="use-existing-azure-resources"></a>Använda befintliga Azure-resurser

Om du redan har skapat de nödvändiga Azure-resurserna skapar du motsvarande miljövariabler på den lokala datorn.
Ange miljövariabler för följande:
* **PCS_KEYVAULT_NAME** - Namn på Azure Key Vault-resursen
* **PCS_AAD_APPID** - AAD-applikations-ID
* **PCS_AAD_APPSECRET** - AAD-applikationshemligheten

Konfigurationsvärden läss från den här Azure Key Vault-resursen. Dessa miljövariabler kan sparas i ** \<startmappen\>\\.pcs-lösningsnamnet\\\<\>.env-filen** från distributionen. Observera att miljövariabler som anges på din lokala dator åsidosätter värden i **\\tjänstskriptens\\lokala\\.env-fil** när du kör **docker-compose**.

En del av konfigurationen som behövs av mikrotjänsten lagras i en instans av **Key Vault** som skapades vid den första distributionen. Motsvarande variabler i keyvault bör ändras vid behov.