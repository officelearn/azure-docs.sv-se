---
title: Distribuera ett .NET-program i en container till Azure Service Fabric | Microsoft Docs
description: Lär dig hur du använder en container med ett befintligt .NET-program med hjälp av Visual Studio och hur du felsöker containrar i Service Fabric lokalt. Programmet i containern skickas via push-teknik till ett Azure-containerregister och distribueras till ett Service Fabric-kluster. När det har distribuerats till Azure använder programmet Azure SQL DB för att spara data.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/18/2018
ms.author: ryanwi
ms.openlocfilehash: 36b9a2e710a2a7f34ee9374e89f3fb19cc591ac3
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2018
ms.locfileid: "49429600"
---
# <a name="tutorial-deploy-a-net-application-in-a-windows-container-to-azure-service-fabric"></a>Självstudiekurs: Distribuera ett .NET-program i en Windows-container till Azure Service Fabric

I den här självstudiekursen lär du dig hur du skapar en container för ett befintligt ASP.NET-program och hur du paketerar programmet som ett Service Fabric-program.  Kör containrarna lokalt i Service Fabric-utvecklingsklustret och distribuera sedan programmet till Azure.  Programmet sparar data i [Azure SQL Database](/azure/sql-database/sql-database-technical-overview). 

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Använda en container med ett befintligt program med hjälp av Visual Studio
> * Skapa en Azure SQL Database
> * Skapa ett Azure-containerregister
> * Distribuera ett Service Fabric-program till Azure

## <a name="prerequisites"></a>Nödvändiga komponenter

1. Om du inte redan har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
2. Installera [Docker CE för Windows](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description) så att du kan köra containrar i Windows 10.
3. Installera [Service Fabric Runtime version 6.2 eller senare](service-fabric-get-started.md) och [Service Fabric SDK version 3.1](service-fabric-get-started.md) eller senare.
4. [Installera Visual Studio 2017 version 15.7](https://www.visualstudio.com/) eller senare med arbetsbelastningarna **Azure Development** och **ASP.NET och webbutveckling**.
5. Installera [Azure PowerShell][link-azure-powershell-install]
 

## <a name="download-and-run-fabrikam-fiber-callcenter"></a>Ladda ned och kör Fabrikam Fiber CallCenter
Ladda ned exempelprogrammet [Fabrikam Fiber CallCenter][link-fabrikam-github].  Klicka på länken för att **ladda ned arkiv**.  Gå till katalogen *sourceCode* i filen *fabrikam.zip* och extrahera filen *sourceCode.zip* och sedan katalogen *VS2015* till datorn.

Kontrollera att Fabrikam Fiber CallCenter-programmet skapas och körs utan fel.  Starta Visual Studio som **administratör** och öppna filen [FabrikamFiber.CallCenter.sln][link-fabrikam-github].  Tryck på F5 för att felsöka och köra programmet.

![Fabrikam-webbexempel][fabrikam-web-page]

## <a name="containerize-the-application"></a>Använd programmet med en container
Högerklicka på projektet **FabrikamFiber.Web** > **Lägg till** > **Stöd för Container Orchestrator**.  Välj **Service Fabric** som initierare för containern och klicka på **OK**.

Klicka på **Ja** för att byta Docker till Windows-containrar nu.

Ett nytt Service Fabric-programprojekt **FabrikamFiber.CallCenterApplication** skapas i lösningen.  En Dockerfile läggs till i det befintliga **FabrikamFiber.Web**-projektet.  En **PackageRoot**-katalog läggs också till i **FabrikamFiber.Web**-projektet, som innehåller tjänstmanifestet och inställningarna för den nya FabrikamFiber.Web-tjänsten. 

Containern är nu redo att byggas och paketeras i ett Service Fabric-program. När du har containeravbildningen klar på datorn kan du lägga den i valfritt containerregister och hämta den till valfri värd för körning.

## <a name="create-an-azure-sql-db"></a>Skapa en Azure SQL DB-databas
När du kör Fabrikam Fiber CallCenter-programmet i produktion måste data sparas i en databas. Det finns för närvarande inget sätt att garantera beständiga data i en container. Därför kan du inte lagra produktionsdata i SQL Server i en container.

Vi rekommenderar [Azure SQL Database](/azure/sql-database/sql-database-get-started-powershell). Konfigurera och kör en hanterad SQL Server-databas i Azure genom att köra följande skript.  Ändra skriptvariablerna efter behov. *clientIP* är utvecklingsdatorns IP-adress.

Om datorn finns bakom en företagsbrandvägg kanske utvecklingsdatorns IP-adress inte exponeras mot Internet. Kontrollera att databasen har rätt IP-adress för brandväggsregeln genom att gå till [Azure-portalen](https://portal.azure.com) och leta upp din databas i avsnittet för SQL-databaser. Klicka på dess namn. I avsnittet Översikt klickar du sedan på ”Konfigurera serverns brandvägg”. ”Klient-IP-adress” är utvecklingsdatorns IP-adress. Se till att den matchar IP-adressen i regeln ”AllowClient” (tillåt klient).

```powershell
$subscriptionID="<subscription ID>"

# Sign in to your Azure account and select your subscription.
Login-AzureRmAccount -SubscriptionId $subscriptionID 

# The data center and resource name for your resources.
$dbresourcegroupname = "fabrikam-fiber-db-group"
$location = "southcentralus"

# The logical server name: Use a random value or replace with your own value (do not capitalize).
$servername = "fab-fiber-$(Get-Random)"

# Set an admin login and password for your database.
# The login information for the server.
$adminlogin = "ServerAdmin"
$password = "Password@123"

# The IP address of your development computer that accesses the SQL DB.
$clientIP = "24.18.117.76"

# The database name.
$databasename = "call-center-db"

# Create a new resource group for your deployment and give it a name and a location.
New-AzureRmResourceGroup -Name $dbresourcegroupname -Location $location

# Create the SQL server.
New-AzureRmSqlServer -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

# Create the firewall rule to allow your development computer to access the server.
New-AzureRmSqlServerFirewallRule -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowClient" -StartIpAddress $clientIP -EndIpAddress $clientIP

# Creeate the database in the server.
New-AzureRmSqlDatabase  -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -DatabaseName $databasename `
    -RequestedServiceObjectiveName "S0"

Write-Host "Server name is $servername"
```

## <a name="update-the-web-config"></a>Uppdatera webbkonfiguration
I projektet **FabrikamFiber.Web** uppdaterar du anslutningssträngen i filen **web.config** så att den pekar på SQL Server i containern.  Uppdatera *Server*-delen i anslutningssträngen för servern som skapades med föregående skript. 

```xml
<add name="FabrikamFiber-Express" connectionString="Server=tcp:fab-fiber-1300282665.database.windows.net,1433;Initial Catalog=call-center-db;Persist Security Info=False;User ID=ServerAdmin;Password=Password@123;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;" providerName="System.Data.SqlClient" />
<add name="FabrikamFiber-DataWarehouse" connectionString="Server=tcp:fab-fiber-1300282665.database.windows.net,1433;Initial Catalog=call-center-db;Persist Security Info=False;User ID=ServerAdmin;Password=Password@123;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;" providerName="System.Data.SqlClient" />
  
```
>[!NOTE]
>Du kan använda vilken SQL-Server som helst för lokal felsökning så länge den kan nås från värden, men **localdb** stöder inte `container -> host`-kommunikation. Om du vill använda en annan SQL-databas när du skapar ett versionsbygge av ditt webbprogram, lägger du till ytterligare en anslutningssträng i filen *web.release.config*.

## <a name="run-the-containerized-application-locally"></a>Kör programmet i containern lokalt
Tryck på **F5** för att köra och felsöka programmet i en container i det lokala Service Fabric-utvecklingsklustret. Klicka på **Ja** om ett meddelande visas där du ombeds att ge gruppen 'ServiceFabricAllowedUsers' läs- och körbehörighet till katalogen Visual Studio-projekt.

## <a name="create-a-container-registry"></a>Skapa ett containerregister
Nu när programmet körs lokalt kan du börja förbereda distributionen till Azure.  Containeravbildningar måste lagras i ett containerregister.  Skapa ett [Azure-containerregister](/azure/container-registry/container-registry-intro) med hjälp av följande skript. Containerregisternamnet är synligt för andra Azure-prenumerationer så det måste vara unikt.
Innan du distribuerar programmet till Azure skickar du containeravbildningen via push-teknik till det här registret.  När programmet distribueras till klustret i Azure hämtas containeravbildningen från det här registret.

```powershell
# Variables
$acrresourcegroupname = "fabrikam-acr-group"
$location = "southcentralus"
$registryname="fabrikamregistry$(Get-Random)"

New-AzureRmResourceGroup -Name $acrresourcegroupname -Location $location

$registry = New-AzureRMContainerRegistry -ResourceGroupName $acrresourcegroupname -Name $registryname -EnableAdminUser -Sku Basic
```

## <a name="create-a-service-fabric-cluster-on-azure"></a>Skapa ett Service Fabric-kluster i Azure
Service Fabric-program körs i ett kluster, en nätverksansluten uppsättning virtuella eller fysiska datorer.  Innan du kan distribuera programmet till Azure måste du först skapa ett Service Fabric-kluster i Azure.

Du kan:
- Skapa ett testkluster från Visual Studio. Med det här alternativet kan du skapa ett säkert kluster direkt från Visual Studio med dina önskade konfigurationer. 
- [Skapa ett säkert kluster från en mall](service-fabric-tutorial-create-vnet-and-windows-cluster.md)

I den här självstudiekursen skapar du ett kluster från Visual Studio, vilket är idealiskt för testscenarier. Om du skapar ett kluster på något annat sätt eller om du använder ett befintligt kluster kan du kopiera och klistra in anslutningens slutpunkt eller välja den från din prenumeration. 

När du skapar klustret, väljer du en SKU som stöder körningscontainrar. Windows Server-operativsystemet på klusternoderna måste vara kompatibla med Windows Server-operativsystemet för din container. Mer information finns i [Kompatibilitet mellan operativsystem för Windows Server-containrar och värdoperativsystem](service-fabric-get-started-containers.md#windows-server-container-os-and-host-os-compatibility). Den här guiden skapar en Docker-avbildning baserat på Windows Server 2016 LTSC som standard. Containrar som bygger på den här avbildningen kan köras på kluster som skapas med Windows Server 2016 Datacenter med Containers. Om du skapar ett kluster eller använder ett befintligt kluster baserat på Windows Server Datacenter Core 1709 med Containers, måste du ändra Windows Server-OS-avbildningen som containern baseras på. Öppna **Dockerfile** i projektet **FabrikamFiber.Web**, kommentera den befintliga instruktionen `FROM` (baserat på `windowsservercore-ltsc`) och avkommentera instruktionen `FROM` baserat på `windowsservercore-1709`. 

1. Högerklicka på **FabrikamFiber.CallCenterApplication**-programprojektet i Solution Explorer och välj **Publicera**.

2. Logga in med ditt Azure-konto så att du får åtkomst till dina prenumerationer. 

3. Välj listrutan för **Anslutningens slutpunkt** och välj alternativet **Skapa nytt kluster**.    
        
4. Ändra följande inställningar i dialogrutan **Skapa kluster**:

    1. Ange namnet på klustret i fältet **Klusternamn**, samt den prenumeration och plats som du vill använda.
    2. Valfritt: Du kan ändra antalet noder. Som standard har du tre noder, vilket är det som krävs för att testa scenarier i Service Fabric.
    3. Välj fliken **Certifikat**. På den här fliken skriver du ett lösenord som ska skydda certifikatet i klustret. Med det här certifikatet blir klustret säkrare. Du kan också ändra sökvägen till den plats där du vill spara certifikatet. Visual Studio kan dessutom importera certifikatet åt dig, eftersom det är ett obligatoriskt steg för att kunna publicera programmet i klustret.
    4. Välj fliken med **information om den virtuella datorn**. Ange det lösenord som du vill använda för den Virtual Machines (VM) som ingår i klustret. Användarnamnet och lösenordet kan användas för att fjärransluta till de virtuella datorerna. Du måste också välja en virtuell datorstorlek och du kan ändra VM-avbildning om det behövs.
    5. På fliken **Avancerat** visar du programporten som ska öppnas i lastbalanseraren när klustret distribueras. Öppna FabrikamFiber.Web -> PackageRoot -> ServiceManifest.xml i Solution Explorer.  Porten för webbklientdelen visas i **Slutpunkt**.  Du kan också lägga till en befintlig Application Insights-nyckel som används för att dirigera programmets loggfiler.
    6. När du är klar med ändringen av inställningarna väljer du knappen **Skapa**. 
5. Åtgärden tar flera minuter. Utdatafönstret visar när klustret har skapats.
    

## <a name="allow-your-application-running-in-azure-to-access-the-sql-db"></a>Ge programmet som körs i Azure åtkomst till SQL DB-databasen
Tidigare skapade du en SQL-brandväggsregel som gav åtkomst till programmet som körs lokalt.  Nu måste du ge programmet som körs i Azure åtkomst till SQL DB-databasen.  Skapa en [tjänstslutpunkt för det virtuella nätverket](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview) för Service Fabric-klustret och skapa sedan en regel som ger slutpunkten åtkomst till SQL-databasen.

```powershell
# Create a virtual network service endpoint
$clusterresourcegroup = "fabrikamfiber.callcenterapplication_RG"
$resource = Get-AzureRmResource -ResourceGroupName $clusterresourcegroup -ResourceType Microsoft.Network/virtualNetworks | Select-Object -first 1
$vnetName = $resource.Name

Write-Host 'Virtual network name: ' $vnetName 

# Get the virtual network by name.
$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName $clusterresourcegroup `
  -Name              $vnetName

Write-Host "Get the subnet in the virtual network:"

# Get the subnet, assume the first subnet contains the Service Fabric cluster.
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet | Select-Object -first 1

$subnetName = $subnet.Name
$subnetID = $subnet.Id
$addressPrefix = $subnet.AddressPrefix

Write-Host "Subnet name: " $subnetName " Address prefix: " $addressPrefix " ID: " $subnetID

# Assign a Virtual Service endpoint 'Microsoft.Sql' to the subnet.
$vnet = Set-AzureRmVirtualNetworkSubnetConfig `
  -Name            $subnetName `
  -AddressPrefix   $addressPrefix `
  -VirtualNetwork  $vnet `
  -ServiceEndpoint Microsoft.Sql | Set-AzureRmVirtualNetwork

$vnet.Subnets[0].ServiceEndpoints;  # Display the first endpoint.

# Add a SQL DB firewall rule for the virtual network service endpoint
$subnet = Get-AzureRmVirtualNetworkSubnetConfig `
  -Name           $subnetName `
  -VirtualNetwork $vnet;

$VNetRuleName="ServiceFabricClusterVNetRule"
$vnetRuleObject1 = New-AzureRmSqlServerVirtualNetworkRule `
  -ResourceGroupName      $dbresourcegroupname `
  -ServerName             $servername `
  -VirtualNetworkRuleName $VNetRuleName `
  -VirtualNetworkSubnetId $subnetID;
```
## <a name="deploy-the-application-to-azure"></a>Distribuera programmet till Azure
Nu när programmet är klart kan du distribuera det till klustret i Azure direkt från Visual Studio.  Högerklicka på **FabrikamFiber.CallCenterApplication**-programprojektet i Solution Explorer och välj **Publicera**.  I **Anslutningens slutpunkt** väljer du slutpunkten för klustret som du skapade tidigare.  I **Azure Container Registry** väljer du behållarregistret som du skapade tidigare.  Publicera programmet till klustret i Azure genom att klicka på **Publicera**.

![Publicera programmet][publish-app]

Följ distributionsförloppet i utdatafönstret  När programmet har distribuerats öppnar du en webbläsare och anger klusteradressen och programporten. Till exempel http://fabrikamfibercallcenter.southcentralus.cloudapp.azure.com:8659/.

![Fabrikam-webbexempel][fabrikam-web-page-deployed]

## <a name="set-up-continuous-integration-and-deployment-cicd-with-a-service-fabric-cluster"></a>Konfigurera kontinuerlig integrering och distribution (CI/CD) med ett Service Fabric-kluster
Mer information om hur du använder Azure DevOps för att konfigurera en CI/CD-programdistribution till ett Service Fabric-kluster finns i [Självstudiekurs: Distribuera ett program med CI/CD till ett Service Fabric-kluster](service-fabric-tutorial-deploy-app-with-cicd-vsts.md). Den process som beskrivs i självstudien är densamma för det här projektet (FabrikamFiber), förutom att du hoppar nedladdningen av Voting-exemplet och använder FabrikamFiber som namnet på lagringsplatsen i stället för Voting.

## <a name="clean-up-resources"></a>Rensa resurser
När du är klar tar du bort alla resurser du skapat.  Det enklaste sättet att göra det är att ta bort resursgrupperna som innehåller Service Fabric-klustret, Azure SQL-databasen och Azure Container Registry.

```powershell
$dbresourcegroupname = "fabrikam-fiber-db-group"
$acrresourcegroupname = "fabrikam-acr-group"
$clusterresourcegroupname="fabrikamcallcentergroup"

# Remove the Azure SQL DB
Remove-AzureRmResourceGroup -Name $dbresourcegroupname

# Remove the container registry
Remove-AzureRmResourceGroup -Name $acrresourcegroupname

# Remove the Service Fabric cluster
Remove-AzureRmResourceGroup -Name $clusterresourcegroupname
```

## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Använda en container med ett befintligt program med hjälp av Visual Studio
> * Skapa en Azure SQL Database
> * Skapa ett Azure-containerregister
> * Distribuera ett Service Fabric-program till Azure

I nästa del av självstudierna lär du dig hur du [distribuerar ett containerprogram med CI/CD till ett Service Fabric-kluster](service-fabric-tutorial-deploy-container-app-with-cicd-vsts.md).

[link-fabrikam-github]: https://aka.ms/fabrikamcontainer
[link-azure-powershell-install]: /powershell/azure/install-azurerm-ps
[link-servicefabric-create-secure-clusters]: service-fabric-cluster-creation-via-arm.md
[link-visualstudio-cd-extension]: https://aka.ms/cd4vs
[link-servicefabric-containers]: service-fabric-get-started-containers.md
[link-servicefabric-createapp]: service-fabric-create-your-first-application-in-visual-studio.md
[link-azure-portal]: https://portal.azure.com
[link-sf-clustertemplate]: https://aka.ms/securepreviewonelineclustertemplate
[link-azure-pricing-calculator]: https://azure.microsoft.com/pricing/calculator/
[link-azure-subscription]: https://azure.microsoft.com/free/
[link-vsts-account]: https://www.visualstudio.com/team-services/pricing/
[link-azure-sql]: /azure/sql-database/

[fabrikam-web-page]: media/service-fabric-host-app-in-a-container/fabrikam-web-page.png
[fabrikam-web-page-deployed]: media/service-fabric-host-app-in-a-container/fabrikam-web-page-deployed.png
[publish-app]: media/service-fabric-host-app-in-a-container/publish-app.png
