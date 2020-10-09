---
title: Konfigurera Azure Service Fabric maskor CLI
description: Service Fabric Mesh-kommandoradsgränssnittet (CLI) krävs för att distribuera och hantera resurser lokalt och i Azure Service Fabric Mesh. Så här konfigurerar du det.
author: georgewallace
ms.author: gwallace
ms.date: 11/28/2018
ms.topic: conceptual
ms.openlocfilehash: fb059fe5dc4e64df104e026983e51f799236f916
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2020
ms.locfileid: "91842809"
---
# <a name="set-up-service-fabric-mesh-cli"></a>Konfigurera Service Fabric Mesh CLI
Service Fabric Mesh-kommandoradsgränssnittet (CLI) krävs för att distribuera och hantera resurser lokalt och i Azure Service Fabric Mesh. Så här konfigurerar du det.

Det finns tre typer av CLI som kan användas och de sammanfattas i tabellen nedan.

| CLI-modul | Målmiljö |  Beskrivning | 
|---|---|---|
| az mesh | Azure Service Fabric Mesh | Primärt CLI som gör att du kan distribuera dina program och hantera resurser mot Azure Service Fabric Mesh-miljön. 
| sfctl | Lokala kluster | Service Fabric-CLI som möjliggör distribution och testning av Service Fabric-resurser mot lokala kluster.  
| Maven CLI | Lokala kluster och Azure Service Fabric Mesh | En omslutning `az mesh` `sfctl` som gör att Java-utvecklare kan använda en välbekant kommando rads upplevelse för lokal och Azure-utvecklings miljö.  

För förhandsversionen är Azure Service Fabric Mesh CLI skrivet som ett tillägg till Azure CLI. Du kan installera det i Azure Cloud Shell eller en lokal installation av Azure CLI. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

## <a name="install-the-azure-service-fabric-mesh-cli"></a>Installera Azure Service Fabric Mesh CLI
1. Du måste installera Azure CLI-versionen 2.0.67 eller senare. Kör `az --version` för att hitta versionen. Om du vill installera eller uppgradera till den senaste versionen av CLI kan du läsa [Installera Azure CLI][azure-cli-install].

2. Installera Azure Service Fabric Mesh CLI-tilläggsmodulen med hjälp av följande kommando. 

    ```azurecli-interactive
    az extension add --name mesh
    ```

3. Uppdatera en befintlig Azure Service Fabric Mesh CLI-modul med hjälp av följande kommando.

    ```azurecli-interactive
    az extension update --name mesh
    ```

## <a name="install-the-service-fabric-cli-sfctl"></a>Installera Service Fabric CLI (sfctl) 

Följ instruktionerna i [Konfigurera Service Fabric CLI](../service-fabric/service-fabric-cli.md). Modulen **sfctl** kan användas för distribution av program baserat på resursmodellen mot Service Fabric-kluster på den lokala datorn. 

## <a name="install-the-maven-cli"></a>Installera Maven CLI 

För att kunna använda Maven CLI måste följande vara installerat på datorn: 

* [Java](https://www.azul.com/downloads/zulu/)
* [Maven](https://maven.apache.org/download.cgi)
* [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
* Azure Mesh CLI (az mesh) – För Azure Service Fabric Mesh som mål 
* SFCTL (sfctl) – För lokala kluster som mål 

Maven CLI för Service Fabric är fortfarande i förhandsversion. 

Om du vill använda Maven-pluginprogrammet i Maven Java-appen lägger du till följande kodavsnitt i filen pom.xml:

```XML
<project>
  ...
  <build>
    ...
    <plugins>
      ...
      <plugin>
        <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-sfmesh-maven-plugin</artifactId>
          <version>0.1.0</version>
          <configuration>
            ...
          </configuration>
      </plugin>
    </plugins>
  </build>
</project>
```

Läs avsnittet med [Maven CLI-referensen](service-fabric-mesh-reference-maven.md) om du vill veta mer om detaljerad användning.

## <a name="next-steps"></a>Nästa steg

Du kan även konfigurera [Windows-utvecklingsmiljön](service-fabric-mesh-howto-setup-developer-environment-sdk.md).

Få svar på [vanliga frågor och problem](service-fabric-mesh-faq.md).

[azure-cli-install]: /cli/azure/install-azure-cli
