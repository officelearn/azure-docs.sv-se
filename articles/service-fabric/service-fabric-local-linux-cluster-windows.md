---
title: Konfigurera Azure Service Fabric Linux-kluster i Windows | Microsoft Docs
description: Den här artikeln beskriver hur du ställer in Service Fabric Linux-kluster som körs på Windows development-datorer. Detta är särskilt användbart för mellan plattformsutveckling.
services: service-fabric
documentationcenter: .net
author: suhuruli
manager: mfussell
editor: ''
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/20/2017
ms.author: suhuruli
ms.openlocfilehash: ad0d383888c173ece5a7fbd3b0de690ed13074f7
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="set-up-a-linux-service-fabric-cluster-on-your-windows-developer-machine"></a>Konfigurera ett Linux Service Fabric-kluster på Windows developer-dator

Det här dokumentet beskriver hur du ställer in en lokal Linux Service Fabric i Windows development-datorer. Konfigurera en lokal Linux-kluster är användbar för att snabbt testa program riktade för Linux-kluster, men har utvecklats på en Windows-dator.

## <a name="prerequisites"></a>Förutsättningar
Linux-baserade Service Fabric-kluster kör inte internt i Windows. Om du vill köra en lokal Service Fabric-kluster tillhandahålls en förkonfigurerad Docker behållare avbildning. Innan du börjar behöver du:

* Minst 4 GB RAM-minne
* Senaste versionen av [Docker](https://store.docker.com/editions/community/docker-ce-desktop-windows)

>[!TIP]
> * Du kan följa anvisningarna i den officiella Docker [dokumentationen](https://store.docker.com/editions/community/docker-ce-desktop-windows/plans/docker-ce-desktop-windows-tier?tab=instructions) installera Docker på Windows. 
> * När installationen är färdig verifierar du om den har installerats korrekt genom att följa anvisningarna [här](https://docs.docker.com/docker-for-windows/#check-versions-of-docker-engine-compose-and-machine)


## <a name="create-a-local-container-and-setup-service-fabric"></a>Skapa en lokal behållare och konfigurera Service Fabric
Utför följande steg för att konfigurera en lokal dockerbehållare och har ett service fabric-kluster som körs på den i PowerShell:

1. Hämta avbildningen från Docker-hubblagringsplatsen:

    ```powershell
    docker pull microsoft/service-fabric-onebox
    ```

2. Uppdatera konfigurationen av Docker-daemon på värden enligt följande och starta om Docker-daemon: 

    ```json
    {
      "ipv6": true,
      "fixed-cidr-v6": "2001:db8:1::/64"
    }
    ```
    Uppmanade sättet att uppdatera är - gå till Docker-ikonen > Inställningar > Daemon > avancerade och uppdatera den det. Starta sedan om Docker-daemon för att ändringarna ska börja gälla. 

3. Starta en behållarinstans för Service Fabric One-box med avbildningen:

    ```powershell
    docker run -itd -p 19080:19080 --name sfonebox microsoft/service-fabric-onebox
    ```
    >[!TIP]
    > * Genom att ange ett namn för din behållarinstans kan du hantera den enklare. 
    > * Om programmet lyssnar på vissa portar måste det anges med hjälp av ytterligare -p-taggar. Till exempel om programmet som lyssnar på port 8080, köra kör docker - itd -p 19080:19080 -p 8080:8080--namnet sfonebox microsoft/service-fabric-onebox

4. Logga in på Docker-behållaren i interaktivt SSH-läge:

    ```powershell
    docker exec -it sfonebox bash
    ```

5. Kör konfigurationsskriptet som ska hämta de nödvändiga beroendena och starta därefter klustret på behållaren.

    ```bash
    ./setup.sh     # Fetches and installs the dependencies required for Service Fabric to run
    ./run.sh       # Starts the local cluster
    ```

6. När steg 5 är klar, går du till ``http://localhost:19080`` från din Windows och du kommer att kunna se Service Fabric explorer. Nu kan du ansluta till det här klustret med hjälp av alla verktyg från din Windows-dator för utvecklare och distribuerar program för Linux Service Fabric-kluster. 

    > [!NOTE]
    > Eclipse-pluginprogrammet stöds för närvarande inte av Windows. 

## <a name="next-steps"></a>Nästa steg
* Kom igång med [Eclipse](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-eclipse)
* Kolla in andra [Java-exempel](https://github.com/Azure-Samples/service-fabric-java-getting-started)


<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png