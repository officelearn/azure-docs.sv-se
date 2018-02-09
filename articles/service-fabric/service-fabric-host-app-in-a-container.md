---
title: "Distribuera ett .NET-program i en behållare till Azure Service Fabric | Microsoft Docs"
description: "Du lär dig att paketera ett .NET-program i Visual Studio i en Docker-behållare. Det nya ”behållarprogrammet” distribueras sedan till ett Service Fabric-kluster."
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/19/2017
ms.author: mikhegn
ms.openlocfilehash: cd1c3b063132ae549bfbf1e059667c5056c91046
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="deploy-a-net-application-in-a-windows-container-to-azure-service-fabric"></a>Distribuera ett .NET-program i en Windows-behållare till Azure Service Fabric

Den här kursen visar hur du distribuerar ett befintligt ASP.NET-program i en Windows-behållare på Azure.

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Skapar ett Docker-projekt i Visual Studio
> * Använder ett befintligt program i en behållare
> * Konfigurera kontinuerlig integrering med Visual Studio och VSTS

## <a name="prerequisites"></a>Nödvändiga komponenter

1. Installera [Docker CE för Windows](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description) så att du kan köra behållare i Windows 10.
2. Bekanta dig med [snabbstart för Windows 10-behållare][link-container-quickstart].
3. Ladda ned exempelprogrammet [Fabrikam Fiber CallCenter][link-fabrikam-github].
4. Installera [Azure PowerShell][link-azure-powershell-install]
5. Installera tillägget [Continuous Delivery Tools för Visual Studio 2017][link-visualstudio-cd-extension]
6. Skapa en [Azure-prenumeration][link-azure-subscription] och ett [Visual Studio Team Services-konto][link-vsts-account]. 
7. [Skapa ett kluster på Azure](service-fabric-tutorial-create-vnet-and-windows-cluster.md)

## <a name="create-a-cluster-on-azure"></a>Skapa ett kluster på Azure
Service Fabric-program körs i ett kluster, en nätverksansluten uppsättning virtuella eller fysiska datorer. [Konfigurera ett Service Fabric-kluster som körs i Azure](service-fabric-tutorial-create-vnet-and-windows-cluster.md) innan du skapar och distribuerar ditt program. När du skapar klustret väljer du en SKU som stöder körning av behållare (till exempel Windows Server 2016 Datacenter med behållare).

## <a name="containerize-the-application"></a>Använd programmet med behållare

Nu när du har ett Service Fabric-kluster som körs i Azure är du redo att skapa och distribuera ett program i behållare. När vi ska börja köra programmet i en behållare måste vi lägga till **Docker-stöd** till projektet i Visual Studio. När du lägger till **Docker-stöd** till programmet händer två saker. Först läggs en _Docker-fil_ till i projektet. Filen beskriver hur behållaravbildningen ska skapas. Sedan läggs ett nytt _docker compose_-projekt till i lösningen. Det nya projektet innehåller några docker compose-filer. Docker compose-filer kan användas för att beskriva hur behållaren ska köras.

Mer information om hur du arbetar med [Visual Studio Container Tools][link-visualstudio-container-tools].

>[!NOTE]
>Om det är första gången du kör Windows-behållaravbildningar på datorn måste Docker CE hämta grundavbildningarna för dina behållare. Avbildningarna som används i den här kursen är 14 GB. Gå vidare och kör följande terminalkommando för att hämta grundavbildningarna:
>```cmd
>docker pull microsoft/mssql-server-windows-developer
>docker pull microsoft/aspnet:4.6.2
>```

### <a name="add-docker-support"></a>Lägga till Docker-stöd

Öppna filen [FabrikamFiber.CallCenter.sln][link-fabrikam-github] i Visual Studio.

Högerklicka på projektet **FabrikamFiber.Web** > **Lägg till** > **Docker-stöd**.

### <a name="add-support-for-sql"></a>Lägga till stöd för SQL

I det här programmet används SQL som dataleverantör, så det krävs en SQLServer för att köra programmet. Referera till SQL Server-behållaravbildning i vår docker-compose.override.yml-fil.

Gå till Visual Studio, öppna **Solution Explorer**, leta rätt på **docker-compose** och öppna filen **docker-compose.override.yml**.

Navigera till noden `services:` och lägg till en nod med namnet `db:` som definierar SQL Server-posten för behållaren.

```yml
  db:
    image: microsoft/mssql-server-windows-developer
    environment:
      sa_password: "Password1"
      ACCEPT_EULA: "Y"
    ports:
      - "1433"
    healthcheck:
      test: [ "CMD", "sqlcmd", "-U", "sa", "-P", "Password1", "-Q", "select 1" ]
      interval: 1s
      retries: 20
```

>[!NOTE]
>Du kan använda vilken SQL-Server som helst för lokal felsökning så länge den kan nås från värden, men **localdb** stöder inte `container -> host`-kommunikation.

>[!WARNING]
>Körning av SQL Server i en behållare kan inte användas med databevarande. När behållaren stoppas, raderas dina data. Använd inte den här konfigurationen för produktion.

Navigera till noden `fabrikamfiber.web:` och lägg till en underordnad nod med namnet `depends_on:`. Detta säkerställer att tjänsten `db` (SQL Server-behållaren) startas före vårt webbprogram (fabrikamfiber.web).

```yml
  fabrikamfiber.web:
    depends_on:
      - db
```

### <a name="update-the-web-config"></a>Uppdatera webbkonfiguration

I projektet **FabrikamFiber.Web** uppdaterar du anslutningssträngen i filen **web.config** så att den pekar på SQL Server i behållaren.

```xml
<add name="FabrikamFiber-Express" connectionString="Data Source=db,1433;Database=FabrikamFiber;User Id=sa;Password=Password1;MultipleActiveResultSets=True" providerName="System.Data.SqlClient" />

<add name="FabrikamFiber-DataWarehouse" connectionString="Data Source=db,1433;Database=FabrikamFiber;User Id=sa;Password=Password1;MultipleActiveResultSets=True" providerName="System.Data.SqlClient" />
```

>[!NOTE]
>Om du vill använda en annan SQL Server när du skapar ett versionsbygge av ditt webbprogram, lägger du till ytterligare en anslutningssträng i filen web.release.config.

### <a name="test-your-container"></a>Testa din behållare

Tryck på **F5** när du vill köra och felsöka programmet i din behållare.

Edge öppnar programmets definierade startsida med hjälp av behållarens IP-adress i det interna NAT-nätverket (vanligtvis 172.x.x.x). Mer information om hur du felsöker program i behållare med Visual Studio 2017 finns [i den här artikeln][link-debug-container].

![exempel på fabrikam i en behållare][image-web-preview]

Behållaren är du redo att byggas och paketeras i ett Service Fabric-program. När du har behållaravbildningen klar på datorn kan du lägga den i valfritt behållarregister och hämta den till valfri värd för körning.

## <a name="get-the-application-ready-for-the-cloud"></a>Förbereda programmet för molnet

För att kunna förbereda programmet för körning i Service Fabric i Azure måste vi utföra två steg:

1. Exponera den port där vi vill kunna nå vårt webbprogram i Service Fabric-klustret.
2. Ange en produktionsklar SQL-databas för vårt program.

### <a name="expose-the-port-for-the-app"></a>Exponera porten för appen
Service Fabric-klustret som vi konfigurerade har port *80* öppen som standard i Azure Load Balancer, som utjämnar inkommande trafik till klustret. Vi kan exponera vår container på den här porten med filen docker-compose.yml.

Gå till Visual Studio, öppna **Solution Explorer**, leta rätt på **docker-compose** och öppna filen **docker-compose.override.yml**.

Ändra noden `fabrikamfiber.web:` och lägg till en underordnad nod med namnet `ports:`.

Lägg till en strängpost `- "80:80"`. Så här ska din docker-compose.yml-fil se ut:

```yml
  version: '3'

  services:
    fabrikamfiber.web:
      image: fabrikamfiber.web
      build:
        context: .\FabrikamFiber.Web
        dockerfile: Dockerfile
      ports:
        - "80:80"
```

### <a name="use-a-production-sql-database"></a>Använda en SQL Database för produktion
Vid körning i produktion behöver våra data bevaras i databasen. Det finns för närvarande inget sätt att garantera beständiga data i en behållare. Därför kan du inte lagra produktionsdata i SQL Server i en behållare.

Vi rekommenderar att du använder en Azure SQL Database. Om du vill konfigurera och köra en hanterad SQL Server i Azure går du till artikeln [Snabbstart för Azure SQL Database][link-azure-sql].

>[!NOTE]
>Kom ihåg att ändra anslutningssträngen till SQL-servern i filen **web.release.config** i projektet **FabrikamFiber.Web**.
>
>Programmet misslyckas om SQL-databasen inte kan nås. Du kan välja att gå vidare och distribuera programmet utan SQL-server.

## <a name="deploy-with-visual-studio-team-services"></a>Distribuera med Visual Studio Team Services

Om du vill konfigurera distribution med Visual Studio Team Services måste du installera tillägget [Continuous Delivery Tools för Visual Studio-2017][link-visualstudio-cd-extension]. Tillägget gör det lätt att distribuera till Azure genom att konfigurera Visual Studio Team Services och få programmet distribuerat till Service Fabric-klustret.

Först måste din kod finnas i källkontrollen. I resten av det här avsnittet förutsätts att du använder **git**.

### <a name="set-up-a-vsts-repo"></a>Konfigurera en VSTS-lagringsplats
Längst ned till höger i Visual Studio klickar du på **Lägga till i källkontroll** > **Git** (eller det alternativ du föredrar).

![tryck på knappen källkontroll][image-source-control]

Gå till rutan _Team Explorer_ och tryck på **Publicera Git-lagringsplats**.

Välj VSTS-databasnamn och tryck på **Lagringsplats**.

![publicera lagringsplatsen till VSTS][image-publish-repo]

Nu när koden är synkroniserad med en VSTS-källagringsplats kan du konfigurera kontinuerlig integrering och kontinuerlig leverans.

### <a name="setup-continuous-delivery"></a>Konfigurera kontinuerlig leverans

I _Solution Explorer_ högerklickar du på **lösningen** > **Konfigurera kontinuerlig leverans**.

Välj Azure-prenumeration.

För **Värdtyp** anger du **Service Fabric-kluster**.

För **Målvärd** anger du det Service Fabric-kluster som du skapade i föregående avsnitt.

Välj ett **Behållarregister** att publicera din behållare till.

>[!TIP]
>Klicka på knappen **Redigera** för att skapa ett behållarregister.

Tryck på **OK**.

![konfigurera kontinuerlig integrering för Service Fabric][image-setup-ci]
   
   När konfigurationen är klar distribueras behållaren till Service Fabric. När du skickar uppdateringar till databasen genomförs en ny version och utgåva.
   
   >[!NOTE]
   >Det tar ca 15 minuter att skapa behållaravbildningarna.
   >Den första distributionen till Service Fabric-klustret gör att de grundläggande behållaravbildningarna för Windows Server Core laddas ned. Nedladdningen tar ytterligare 5–10 minuter att slutföra.

Gå till programmet Fabrikam Call Center med hjälp av klustrets URL-adress – till exempel *http://mycluster.westeurope.cloudapp.azure.com*

Nu när du har Fabrikam Call Center-lösningen i en behållare och har distribuerat den kan du öppna [Azure-portalen][link-azure-portal] och se programmet som körs i Service Fabric. Om du vill testa programmet öppnar du en webbläsare och går till URL-adressen för Service Fabric-klustret.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapar ett Docker-projekt i Visual Studio
> * Använder ett befintligt program i en behållare
> * Konfigurera kontinuerlig integrering med Visual Studio och VSTS

I nästa del av kursen lär du dig att konfigurera [övervakning för behållaren](service-fabric-tutorial-monitoring-wincontainers.md).

<!--   NOTE SURE WHAT WE SHOULD DO YET HERE

Advance to the next tutorial to learn how to bind a custom SSL certificate to it.

> [!div class="nextstepaction"]
> [Bind an existing custom SSL certificate to Azure Web Apps](app-service-web-tutorial-custom-ssl.md)

## Next steps

- [Container Tooling in Visual Studio][link-visualstudio-container-tools]
- [Get started with containers in Service Fabric][link-servicefabric-containers]
- [Creating Service Fabric applications][link-servicefabric-createapp]
-->

[link-debug-container]: /dotnet/articles/core/docker/visual-studio-tools-for-docker
[link-fabrikam-github]: https://aka.ms/fabrikamcontainer
[link-container-quickstart]: /virtualization/windowscontainers/quick-start/quick-start-windows-10
[link-visualstudio-container-tools]: /dotnet/articles/core/docker/visual-studio-tools-for-docker
[link-azure-powershell-install]: /powershell/azure/install-azurerm-ps
[link-servicefabric-create-secure-clusters]: service-fabric-cluster-creation-via-arm.md
[link-visualstudio-cd-extension]: https://aka.ms/cd4vs
[link-servicefabric-containers]: service-fabric-get-started-containers.md
[link-servicefabric-createapp]: service-fabric-create-your-first-application-in-visual-studio.md
[link-azure-portal]: https://portal.azure.com
[link-sf-clustertemplate]: https://aka.ms/securepreviewonelineclustertemplate
[link-azure-pricing-calculator]: https://azure.microsoft.com/en-us/pricing/calculator/
[link-azure-subscription]: https://azure.microsoft.com/en-us/free/
[link-vsts-account]: https://www.visualstudio.com/team-services/pricing/
[link-azure-sql]: /azure/sql-database/

[image-web-preview]: media/service-fabric-host-app-in-a-container/fabrikam-web-sample.png
[image-source-control]: media/service-fabric-host-app-in-a-container/add-to-source-control.png
[image-publish-repo]: media/service-fabric-host-app-in-a-container/publish-repo.png
[image-setup-ci]: media/service-fabric-host-app-in-a-container/configure-continuous-integration.png
