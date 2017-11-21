---
title: "Distribuera en .NET-app i en behållare till Azure Service Fabric | Microsoft Docs"
description: "Lär dig hur du paketerar ett .NET-app i Visual Studio i en Docker-behållare. Den här nya ”behållare” appen distribueras sedan till ett Service Fabric-kluster."
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/19/2017
ms.author: mikhegn
ms.openlocfilehash: 31c1cee5ddc4c8893da729af884ae7b7b8a58093
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2017
---
# <a name="deploy-a-net-application-in-a-windows-container-to-azure-service-fabric"></a>Distribuera ett .NET-program i en Windows-behållare till Azure Service Fabric

Den här kursen visar hur du distribuerar ett befintligt ASP.NET-program i en Windows-behållare på Azure.

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Skapa en Docker-projekt i Visual Studio
> * Containerize ett befintligt program
> * Installationsprogrammet kontinuerlig integrering med Visual Studio och VSTS

## <a name="prerequisites"></a>Krav

1. Installera [Docker CE för Windows](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description) så att du kan köra behållare på Windows 10.
2. Bekanta dig med de [Windows 10 behållare quickstart][link-container-quickstart].
3. Hämta den [Fabrikam Fiber CallCenter] [ link-fabrikam-github] exempelprogrammet.
4. Installera [Azure PowerShell][link-azure-powershell-install]
5. Installera den [kontinuerlig leveransverktyg tillägget för Visual Studio 2017][link-visualstudio-cd-extension]
6. Skapa en [Azure-prenumeration] [ link-azure-subscription] och en [Visual Studio Team Services-konto][link-vsts-account]. 
7. [Skapa ett kluster i Azure](service-fabric-tutorial-create-vnet-and-windows-cluster.md)

## <a name="create-a-cluster-on-azure"></a>Skapa ett kluster på Azure
Service Fabric-program körs i ett kluster, en nätverksansluten uppsättning virtuella eller fysiska datorer. [Konfigurera ett Service Fabric-kluster som körs i Azure](service-fabric-tutorial-create-vnet-and-windows-cluster.md) innan du skapar och distribuerar ditt program. När du skapar klustret, väljer du en SKU som stöder körs behållare (till exempel Windows Server 2016 Datacenter med behållare).

## <a name="containerize-the-application"></a>Containerize programmet

Nu när du har ett Service Fabric-kluster som körs i Azure är du redo att skapa och distribuera en av programmet. Om du vill börja köra appen i en behållare, behöver vi lägga till **Docker stöd** till projektet i Visual Studio. När du lägger till **Docker stöd** till programmet, två saker. Först en _Dockerfile_ har lagts till i projektet. Den nya filen beskriver hur behållaren avbildningen skapas. Sedan andra, en ny _docker compose_ projektet läggs till i lösningen. Det nya projektet innehåller några docker compose filer. Docker compose filer kan användas för att beskriva hur behållaren körs.

Mer information om hur du arbetar med [verktyg för Visual Studio-behållaren][link-visualstudio-container-tools].

>[!NOTE]
>Om det är första gången du kör Windows behållaren bilder på datorn måste Docker CE hämtar grundläggande avbildningar för din behållare. Bilder som används i den här kursen är 14 GB. Gå vidare och kör kommandot terminal och hämtar grundläggande bilder:
>```cmd
>docker pull microsoft/mssql-server-windows-developer
>docker pull microsoft/aspnet:4.6.2
>```

### <a name="add-docker-support"></a>Lägga till Docker-stöd

Öppna den [FabrikamFiber.CallCenter.sln] [ link-fabrikam-github] filen i Visual Studio.

Högerklicka på den **FabrikamFiber.Web** project > **Lägg till** > **Docker Support**.

### <a name="add-support-for-sql"></a>Lägga till stöd för SQL

Det här programmet används SQL som DataProvider och en SQLServer krävs för att köra programmet. Referera till en SQL Server-behållaren avbildning i vår docker-compose.override.yml-filen.

Öppna i Visual Studio **Solution Explorer**, hitta **docker compose**, och öppna filen **docker compose.override.yml**.

Navigera till den `services:` nod, lägga till en nod med namnet `db:` som definierar den SQL Server-posten för behållaren.

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
>Du kan använda alla SQL-Server som du föredrar för lokala felsökning så länge den kan nås från värden. Dock **localdb** stöder inte `container -> host` kommunikation.

>[!WARNING]
>Kör SQL Server i en behållare stöder inte bestående data. När behållaren stoppar raderas dina data. Använd inte den här konfigurationen för produktion.

Navigera till den `fabrikamfiber.web:` nod och Lägg till en underordnad nod med namnet `depends_on:`. Detta säkerställer att den `db` (SQL Server-behållaren)-tjänsten startar före våra webbprogram (fabrikamfiber.web).

```yml
  fabrikamfiber.web:
    depends_on:
      - db
```

### <a name="update-the-web-config"></a>Uppdatera Webbkonfiguration

I den **FabrikamFiber.Web** projekt, uppdatera anslutningssträngen i den **web.config** fil att peka till SQL Server i behållaren.

```xml
<add name="FabrikamFiber-Express" connectionString="Data Source=db,1433;Database=FabrikamFiber;User Id=sa;Password=Password1;MultipleActiveResultSets=True" providerName="System.Data.SqlClient" />

<add name="FabrikamFiber-DataWarehouse" connectionString="Data Source=db,1433;Database=FabrikamFiber;User Id=sa;Password=Password1;MultipleActiveResultSets=True" providerName="System.Data.SqlClient" />
```

>[!NOTE]
>Om du vill använda en annan SQL Server när du skapar en Versionspost skapa för ditt webbprogram, lägga till ytterligare en anslutningssträng i filen web.release.config.

### <a name="test-your-container"></a>Testa din behållare

Tryck på **F5** att köra och felsöka program i din behållaren.

Edge öppnas programmets definierade startsida med IP-adressen för behållaren i det interna nätverket NAT (vanligtvis 172.x.x.x). Mer information om hur du felsöker program i behållare med hjälp av Visual Studio 2017 finns [i den här artikeln][link-debug-container].

![exempel på fabrikam i en behållare][image-web-preview]

Behållaren kan nu skapats och paketeras i en Service Fabric-programmet. När du har behållaren avbildningen bygger på din dator kan du dra den till valfri värd att köra push till alla behållare registret.

## <a name="get-the-application-ready-for-the-cloud"></a>Förbereda programmet för molnet

För att förbereda programmet för att köra i Service Fabric i Azure, måste vi du utföra två steg:

1. Visa den port där vi vill kunna nå vår webbprogram i Service Fabric-klustret.
2. Ange en klar SQL-databas för produktion för vårt program.

### <a name="expose-the-port-for-the-app"></a>Exponera porten för appen
Vi har konfigurerat Service Fabric-klustret har port *80* öppen som standard i Azure belastningsutjämnare, som balanserar inkommande trafik till klustret. Via vårt filen docker-compose.yml kan du använda vår behållaren på den här porten.

Öppna i Visual Studio **Solution Explorer**, hitta **docker compose**, och öppna filen **docker-compose.yml**.

Ändra den `fabrikamfiber.web:` nod, lägga till en underordnad nod med namnet `ports:`.

Lägg till en sträng som värde `- "80:80"`. Detta är hur filen docker-compose.yml ska se ut:

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

### <a name="use-a-production-sql-database"></a>Använd en SQL-databas för produktion
När du kör i produktion kan vi behöver våra data kvar i vår databas. Det finns för närvarande inget sätt att garantera beständiga data i en behållare, därför du lagra inte produktionsdata i SQL Server i en behållare.

Vi rekommenderar att du använder en Azure SQL Database. Om du vill konfigurera och köra en hanterad SQL Server i Azure finns i [Azure SQL Database Snabbstart] [ link-azure-sql] artikel.

>[!NOTE]
>Kom ihåg att ändra anslutningssträngar till SQLServer i den **web.release.config** filen i den **FabrikamFiber.Web** projekt.
>
>Det här programmet misslyckas programinstallation utan problem om inga SQL-databasen inte kan nås. Du kan välja att gå vidare och distribuera program med ingen SQLServer.

## <a name="deploy-with-visual-studio-team-services"></a>Distribuera med Visual Studio Team Services

Om du vill konfigurera distribution med Visual Studio Team Services måste du installera den [kontinuerlig leveransverktyg tillägget för Visual Studio-2017][link-visualstudio-cd-extension]. Det här tillägget gör det lätt att distribuera till Azure genom att konfigurera Visual Studio Team Services och hämta din app distribueras till Service Fabric-klustret.

Om du vill komma igång måste din kod finnas i källkontroll. Resten av det här avsnittet förutsätter **git** används.

### <a name="set-up-a-vsts-repo"></a>Konfigurera en VSTS repo
Det nedre högra hörnet av Visual Studio klickar du på **lägga till källkontroll** > **Git** (eller alternativet som du föredrar).

![Tryck på knappen käll-kontroll][image-source-control]

I den _Team Explorer_ rutan, tryck på **publicera Git Repo**.

Välj VSTS databasnamn och tryck på **databasen**.

![Publicera lagringsplatsen till VSTS][image-publish-repo]

Nu när koden synkroniseras med en VSTS källdatabasen, kan du konfigurera kontinuerlig integrering och kontinuerlig leverans.

### <a name="setup-continuous-delivery"></a>Installationsprogrammet kontinuerlig leverans

I _Solution Explorer_, högerklicka på den **lösning** > **konfigurera kontinuerlig leverans**.

Välj den Azure-prenumerationen.

Ange **värd typen** till **Service Fabric-kluster**.

Ange **målvärden** till service fabric-kluster som du skapade i föregående avsnitt.

Välj en **behållare registret** behållare för att publicera.

>[!TIP]
>Använd den **redigera** för att skapa en behållare för registret.

Tryck på **OK**.

![installationsprogrammet för service fabric kontinuerlig integration][image-setup-ci]
   
   När konfigurationen är klar, distribueras din behållaren till Service Fabric. När du push uppdateringar för databasen en ny version och utgåva utförs.
   
   >[!NOTE]
   >Skapa behållaren bilder tar ungefär 15 minuter.
   >Den första distributionen till Service Fabric-klustret gör grundläggande Windows Server Core behållaren bilder som ska hämtas. Hämtningen tar ytterligare 5-10 minuter att slutföra.

Bläddra till Fabrikam Callcenter programmet med hjälp av URL-adressen för klustret: till exempel *http://mycluster.westeurope.cloudapp.azure.com*

Nu när du har av och distribuerats Fabrikam Callcenter lösningen, kan du öppna den [Azure-portalen] [ link-azure-portal] och se det program som körs i Service Fabric. Om du vill testa programmet, öppna en webbläsare och gå till Service Fabric-kluster-URL.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa en Docker-projekt i Visual Studio
> * Containerize ett befintligt program
> * Installationsprogrammet kontinuerlig integrering med Visual Studio och VSTS

Nästa del av kursen lär du dig hur du ställer in [övervakning för din behållaren](service-fabric-tutorial-monitoring-wincontainers.md).

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
