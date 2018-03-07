---
title: Integrera Azure Service Fabric med API Management | Microsoft Docs
description: "I den här självstudiekursen lär du dig att snabbt komma igång med Azure API Management och Service Fabric."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/26/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 9277f2c8a4ff595e639f6332d61b936ce44aeb13
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/24/2018
---
# <a name="tutorial-deploy-api-management-with-service-fabric"></a>Självstudiekurs: Distribuera API Management med Service Fabric
Den här självstudien är del fyra i en serie.  Att distribuera Azure API Management med Service Fabric är ett avancerat scenario.  API Management är användbart när du vill publicera API: er med en omfattande uppsättning dirigeringsregler för dina Service Fabric-serverdelstjänster. Molnprogram behöver ofta en klientdelsgateway som enda åtkomstpunkt för ingång för användare, enheter och andra program. I Service Fabric kan en gateway vara valfri tillståndslös tjänst för ingångstrafik, till exempel ett ASP.NET Core-program, Event Hubs, IoT Hub eller Azure API Management. 

I den här kursen får du se hur du konfigurerar [Azure API Management](../api-management/api-management-key-concepts.md) med Service Fabric för att dirigera trafik till en serverdelstjänst i Service Fabric.  När du är klar kan har du distribuerat API Management till ett virtuellt nätverk och konfigurerat en API-åtgärd som dirigerar trafik till tillståndslösa servicedelstjänster. Mer information om Azure API Management-scenarier med Service Fabric finns i [översiktsartikeln](service-fabric-api-management-overview.md).

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Distribuera API Management
> * Konfigurera API Management
> * Skapa en API-åtgärd
> * Konfigurera en serverdelsprincip
> * Lägga till det nya API:et till en produkt

I den här självstudieserien får du lära du dig att:
> [!div class="checklist"]
> * Skapa ett säkert [Windows-kluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md) eller [Linux-kluster](service-fabric-tutorial-create-vnet-and-linux-cluster.md) på Azure med hjälp av en mall
> * [skala upp eller ned ett kluster](service-fabric-tutorial-scale-cluster.md)
> * [uppgradera körningen för ett kluster](service-fabric-tutorial-upgrade-cluster.md)
> * Distribuera API Management med Service Fabric

## <a name="prerequisites"></a>Nödvändiga komponenter
Innan du börjar den här självstudien:
- om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Installera [Azure Powershell-modulen version 4.1 eller senare](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) eller [Azure CLI 2.0](/cli/azure/install-azure-cli).
- Skapa ett säkert [Windows-kluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md) eller [Linux-kluster](service-fabric-tutorial-create-vnet-and-linux-cluster.md) på Azure
- Om du distribuerar ett Windows-kluster måste du konfigurera en Windows-utvecklingsmiljö. Installera [Visual Studio 2017](http://www.visualstudio.com) och arbetsbelastningarna **Azure Development**, **ASP.NET och webbutveckling** samt **.NET Core plattformsoberoende utveckling**.  Konfigurera sedan en [.NET-utvecklingsmiljö](service-fabric-get-started.md).
- Om du distribuerar ett Linux-kluster måste du konfigurera en Java-utvecklingsmiljö på [Linux](service-fabric-get-started-linux.md) eller [MacOS](service-fabric-get-started-mac.md).  Installera [Service Fabric CLI](service-fabric-cli.md). 

## <a name="network-topology"></a>Nätverkstopologi
Nu när du har ett säkert [Windows-kluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md) eller [Linux-kluster](service-fabric-tutorial-create-vnet-and-linux-cluster.md) på Azure, distribuerar du API Management till det virtuella nätverket (VNET) i undernätet och NSG för API Management. I den här självstudien har API Management Resource Manager-mallen förkonfigurerats att använda namnen på det VNET, undernät och NSG som du skapade i föregående [självstudiekurs om Windows-kluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md) eller [självstudiekursen om Linux-kluster](service-fabric-tutorial-create-vnet-and-linux-cluster.md). I den här kursen distribueras följande topologi till Azure, där API Management och Service Fabric finns i undernät i samma virtuella nätverk:

 ![Bildrubrik][sf-apim-topology-overview]

## <a name="sign-in-to-azure-and-select-your-subscription"></a>Logga in på Azure och välj din prenumeration
Logga in på ditt Azure-konto och välj din prenumeration innan du kör Azure-kommandon.

```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

```azurecli
az login
az account set --subscription <guid>
```

## <a name="deploy-a-service-fabric-back-end-service"></a>Distribuera en servicedelstjänst för Service Fabric

Innan du konfigurerar API Management att dirigera trafik till en servicedelstjänst för Service Fabric måste du ha en service som är igång och kan ta emot begäranden.  Om du tidigare har skapat ett [Windows-kluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md) distribuerar du en .NET Service Fabric-tjänst.  Om du tidigare har skapat ett [Linux-kluster](service-fabric-tutorial-create-vnet-and-linux-cluster.md) distribuerar du en Java Service Fabric-tjänst.

### <a name="deploy-a-net-service-fabric-service"></a>Distribuera en .NET Service Fabric-tjänst

I den är självstudien skapar du en grundläggande, tillståndslös och tillförlitlig ASP.NET Core-tjänst med standardmallen för Web API-projekt. Detta skapar en HTTP-slutpunkt för din tjänst, som du exponera via Azure API Management.

Starta Visual Studio som administratör och skapa en ASP.NET Core-tjänst:

 1. I Visual Studio väljer du Arkiv -> Nytt projekt.
 2. Välj Service Fabric-programmallen under Moln och ge den namnet **”ApiApplication”**.
 3. Välj den tillståndslösa ASP.NET Core-tjänstmallen och ge projektet namnet **”WebApiService”**.
 4. Välj projektmallen Web API ASP.NET Core 2.0.
 5. När projektet har skapats öppnar du `PackageRoot\ServiceManifest.xml` och ta bort attributet `Port` från slutpunktskonfigurationen för resursen:
 
    ```xml
    <Resources>
      <Endpoints>
        <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" />
      </Endpoints>
    </Resources>
    ```

    Om du tar bort porten kan Service Fabric ange en port dynamiskt från portintervallet för programmet, som öppnas via Nätverkssäkerhetsgruppen i klustrets Resource Manager-mall så att trafik kan flöda till den från API Management.
 
 6. Tryck på F5 i Visual Studio för att verifiera att ditt webb-API är tillgängligt lokalt. 

    Öppna Service Fabric Explorer och öka detaljnivån ned till en specifik instans av ASP.NET Core-tjänsten för att se den basadressen som tjänsten lyssnar på. Lägg till `/api/values` i basadressen och öppna den i en webbläsare. Detta anropar Get-metoden i ValuesController i Web API-mallen. Detta returnerar standardsvaret som tillhandahålls av mallen, en JSON-matris som innehåller två strängar:

    ```json
    ["value1", "value2"]`
    ```

    Det här är den slutpunkt som du exponerar via API Management i Azure.

 7. Till sist distribuerar du programmet till ditt kluster i Azure. Högerklicka på programprojektet i Visual Studio och välj **Publicera**. Ange din klusterslutpunkt (till exempel `mycluster.southcentralus.cloudapp.azure.com:19000`) för att distribuera programmet till ditt Service Fabric-kluster i Azure.

En tillståndslös ASP.NET Core-tjänst med namnet `fabric:/ApiApplication/WebApiService` bör nu köras i ditt Service Fabric-kluster i Azure.

### <a name="create-a-java-service-fabric-service"></a>Skapa en Java Service Fabric-tjänst
För den här självstudiekursen distribuerar du en enkel webbserver som ekar meddelanden tillbaka till användaren. Exempelprogrammet för ekoservern innehåller en HTTP-slutpunkt för din tjänst, som du exponera via Azure API Management.

1. Klona kom igång-exemplen för Java.

   ```bash
   git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git
   cd service-fabric-java-getting-started
   ```

2. Redigera *Services/EchoServer/EchoServer1.0/EchoServerApplication/EchoServerPkg/ServiceManifest.xml*. Uppdatera slutpunkten så att tjänsten lyssnar på port 8081.

   ```xml
   <Endpoint Name="WebEndpoint" Protocol="http" Port="8081" />
   ```

3. Spara *ServiceManifest.xml* och skapa sedan EchoServer 1.0-programmet.

   ```bash
   cd Services/EchoServer/EchoServer1.0/
   gradle
   ```

4. Distribuera programmet till klustret.

   ```bash
   cd Scripts
   sfctl cluster select --endpoint http://mycluster.southcentralus.cloudapp.azure.com:19080
   ./install.sh
   ```

   En tillståndslös Java-tjänst med namnet `fabric:/EchoServerApplication/EchoServerService` bör nu köras i ditt Service Fabric-kluster i Azure.

5. Öppna en webbläsare och skriv in http://mycluster.southcentralus.cloudapp.azure.com:8081/getMessage. Nu bör ”[version 1.0] Hello World!!!” visas.

## <a name="download-and-understand-the-resource-manager-templates"></a>Ladda ned och förstå Resource Manager-mallarna
Ladda ned och spara följande mallar och parameterfil för Resource Manager:
 
- [network-apim.json][network-arm]
- [network-apim.parameters.json][network-parameters-arm]
- [apim.json][apim-arm]
- [apim.parameters.json][apim-parameters-arm]

Mallen *network-apim.json* distribuerar ett nytt undernät och nätverkssäkerhetsgruppen i det virtuella nätverk där Service Fabric-klustret distribueras.

I följande avsnitt beskrivs de resurser som definieras av mallen *apim.json*. Om du vill ha mer information följer du länkarna till mallreferensdokumentationen i de olika avsnitten. De konfigurerbara parametrar som definierats i parameterfilen *apim.parameters.json* anges längre fram i den här artikeln.

### <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service
[Microsoft.ApiManagement/service](/azure/templates/microsoft.apimanagement/service) beskriver API Management- tjänstinstansen: namn, SKU eller nivå, resursgruppens plats, utgivarinformation och virtuellt nätverk.

### <a name="microsoftapimanagementservicecertificates"></a>Microsoft.ApiManagement/service/certificates
[Microsoft.ApiManagement/service/certificates](/azure/templates/microsoft.apimanagement/service/certificates) konfigurerar API Management-säkerheten. API Management måste autentisera med Service Fabric-klustret för tjänstidentifiering med ett klientcertifikat som har åtkomst till ditt kluster. I den här självstudiekursen används samma certifikat som angavs tidigare när du skapade [Windows-klustret](service-fabric-tutorial-create-vnet-and-windows-cluster.md#createvaultandcert_anchor) eller [Linux-klustret](service-fabric-tutorial-create-vnet-and-linux-cluster.md#createvaultandcert_anchor), vilket som standard kan användas för åtkomst till klustret. 

I den här självstudiekursen används samma certifikat för klientautentisering och nod-till-nod-säkerhet i klustret. Du kan använda ett separat klientcertifikat om du har ett konfigurerat för att åtkomst till ditt Service Fabric-kluster. Ange **namn**, **lösenord** och **data** (base-64-kodad sträng) för den privata nyckelfilen (.pfx) för det klustercertifikat som du angav när du skapade ditt Service Fabric-kluster.

### <a name="microsoftapimanagementservicebackends"></a>Microsoft.ApiManagement/service/backends
[Microsoft.ApiManagement/service/backends](/azure/templates/microsoft.apimanagement/service/backends) beskriver den serverdelstjänst som trafiken dirigeras till. 

För Service Fabric-serverdelar utgör Server Fabric-klustret serverdelen i stället för en specifik Service Fabric-tjänst för Service Fabric serverdelar. På så sätt kan en enda princip dirigera till flera tjänster i klustret. Fältet **URL** här är ett fullständigt kvalificerat namn på en tjänst i klustret som alla begäranden dirigeras till som standard om inget tjänstnamn anges i en serverdelsprincip. Du kan använda falska tjänstnamn, till exempel ”fabric:/fake/service”, om du inte vill ha en reservtjänst. **resourceId** specificerar slutpunkten för klusterhantering.  **clientCertificateThumbprint** och **serverCertificateThumbprints** identifierar certifikaten som används för att autentisera med klustret.

### <a name="microsoftapimanagementserviceproducts"></a>Microsoft.ApiManagement/service/products
[Microsoft.ApiManagement/service/products](/azure/templates/microsoft.apimanagement/service/products) skapar en produkt. En produkt innehåller en eller flera API:er samt en användningskvot och användningsvillkor i Azure API Management. När en produkt har publicerats kan utvecklarna prenumerera på produkten och börja använda produktens API: er. 

Ange ett beskrivande **DisplayName** och en **beskrivning** av produkten. För den här självstudien krävs en prenumeration, men prenumerationsgodkännande av en administratör krävs inte.  Den här produktens **status** är ”publicerad”, och den är synlig för prenumeranter. 

### <a name="microsoftapimanagementserviceapis"></a>Microsoft.ApiManagement/service/apis
[Microsoft.ApiManagement/service/apis](/azure/templates/microsoft.apimanagement/service/apis) skapar ett API. Ett API i API Management representerar en uppsättning åtgärder som kan anropas av klientprogram. När åtgärderna har lagts till läggs API:et till i en produkt och kan publiceras. När ett API har publicerats går det att prenumerera på och kan användas av utvecklare.

- **DisplayName** kan vara valfritt namn för din API. För den här självstudiekursen använder du ”Service Fabric App”.
- **name** är ett unikt och beskrivande namn för API:et, till exempel ”service-fabric-app”. Det visas i utvecklar- och utgivarportalerna. 
- **serviceUrl** refererar till HTTP-tjänsten som implementerar API:et. API-hanteringen vidarebefordrar begäranden till den här adressen. Det här URL-värdet används inte för Service Fabric-serverdelar. Du kan ange ett valfritt värde här. För den här självstudiekursen kan du till exempel ange ”http://servicefabric”. 
- **path** läggs till baswebbadressen för API-hanteringstjänsten. Baswebbadressen är gemensam för alla API:er som har en API Management-tjänstinstans som värd. API Management skiljer API:erna åt med hjälp av deras suffix, och suffixet måste därför vara unikt för alla API:er för en viss utgivare. 
- **protocol** anger vilka protokoll som kan användas för åtkomst till API:et. För den här självstudiekursen anger du **http** och **https**.
- **sökväg** är ett suffix för API:et. Använd ”myapp” för den här självstudiekursen.

### <a name="microsoftapimanagementserviceapisoperations"></a>Microsoft.ApiManagement/service/apis/operations
[Microsoft.ApiManagement/service/apis/operations](/azure/templates/microsoft.apimanagement/service/apis/operations) Innan en API i API Management kan användas måste åtgärder läggas till API:et.  Externa klienter använder en åtgärd för att kommunicera med den tillståndslösa ASP.NET Core-tjänsten som körs i Service Fabric-klustret.

Fyll i följande värden för att lägga till en API-åtgärd i klientdelen:

- I **displayName** och **description** beskrivs åtgärden. Använd ”Values” för den här självstudiekursen.
- **method** anger HTTP-verbet.  För den här självstudiekursen anger du **GET**.
- **urlTemplate** läggs till i baswebbadressen för API:et och identifierar en enskild HTTP-åtgärd.  I den här självstudiekursen använder du `/api/values` om du har lagt till .NET-serverdelstjänsten eller `getMessage` om du har lagt till Java-serverdelstjänsten.  Som standard gäller att den webbsökväg som anges här är den webbsökväg som skickas till Service Fabric- serverdelstjänsten. Om du använder samma webbsökväg här som tjänsten använder, till exempel ”/ api/values”, fungerar åtgärden utan ytterligare ändringar. Du kan också ange en annan webbsökväg här än den webbadress som används av Service Fabric-serverdelstjänsten. I så fall måste du också ange en sökvägsomskrivning i din åtgärdsprincip senare.

### <a name="microsoftapimanagementserviceapispolicies"></a>Microsoft.ApiManagement/service/apis/policies
[Microsoft.ApiManagement/service/apis/policies](/azure/templates/microsoft.apimanagement/service/apis/policies) skapar en serverdelsprincip som kopplar samman allt. Det är här du konfigurerar den Service Fabric-serverdelstjänst som begäranden dirigeras till. Du kan använda den här principen för alla API-åtgärder.  Mer information finns i [Principöversikt](/azure/api-management/api-management-howto-policies). 

[Serverdelskonfigurationen för Service Fabric](/azure/api-management/api-management-transformation-policies#SetBackendService) innehåller följande kontroller för dirigering av begäranden: 
 - Tjänstinstansurval genom att ett Service Fabric-tjänstinstansnamn anges, antingen hårdkodat (till exempel `"fabric:/myapp/myservice"`) eller genererat från HTTP-begäran (till exempel `"fabric:/myapp/users/" + context.Request.MatchedParameters["name"]`).
 - Partitionsupplösning genom att en partitionsnyckel genereras med hjälp av ett partitioneringsschema i Service Fabric.
 - Val av replik för tillståndskänsliga tjänster.
 - Villkor för upplösningsförsök som gör att du kan ange villkoren för återupplösning av en tjänstplats och skicka en begäran på nytt.

**policyContent** är det Json-undantagna XML-innehållet i principen.  För den här självstudiekursen skapar du en serverdelsprincip för att dirigera begäranden direkt till den tillståndslösa .NET- eller Java-tjänst som distribuerades tidigare. Lägg till en `set-backend-service`-princip under inkommande principer.  Byt ut värdet *sf-service-instance-name* mot `fabric:/ApiApplication/WebApiService` om du tidigare har distribuerat .NET-serverdelstjänsten eller `fabric:/EchoServerApplication/EchoServerService` om du har distribuerat Java-tjänsten.  *backend-id* refererar till en serverdelsresurs, i det här fallet den `Microsoft.ApiManagement/service/backends`-resurs som definierats i mallen *apim.json*. *backend-id* kan även referera till en annan serverdelsresurs som skapats med hjälp av API Management-API:erna. För den här självstudien anger du *backend-id* till värdet för parametern *service_fabric_backend_name*.
    
```xml
<policies>
  <inbound>
    <base/>
    <set-backend-service 
        backend-id="servicefabric"
        sf-service-instance-name="service-name"
        sf-resolve-condition="@((int)context.Response.StatusCode != 200)" />
  </inbound>
  <backend>
    <base/>
  </backend>
  <outbound>
    <base/>
  </outbound>
</policies>
```

En fullständig uppsättning attribut för Service Fabric-serverdelsprincipen finns i [serverdelsdokumentationen för API Management](https://docs.microsoft.com/azure/api-management/api-management-transformation-policies#SetBackendService)

## <a name="set-parameters-and-deploy-api-management"></a>Ange parametrar och distribuera API Management
Fyll i följande tomma parametrar i *apim.parameters.json* för din distribution. 

|Parameter|Värde|
|---|---|
|apimInstanceName|sf-apim|
|apimPublisherEmail|myemail@contosos.com|
|apimSku|Developer|
|serviceFabricCertificateName|sfclustertutorialgroup320171031144217|
|certificatePassword|q6D7nN %6ck@6| 
|serviceFabricCertificateThumbprint|C4C1E541AD512B8065280292A8BA6079C3F26F10 |
|serviceFabricCertificate|&lt;base64-kodad sträng&gt;|
|url_path|/api/values|
|clusterHttpManagementEndpoint|https://mysfcluster.southcentralus.cloudapp.azure.com:19080|
|inbound_policy|&lt;XML-sträng&gt;|

*certificatePassword* och *serviceFabricCertificateThumbprint* måste matcha det klustercertifikat som användes när klustret konfigurerades.  

*serviceFabricCertificate* är certifikatet som en base-64-kodad sträng, som kan genereras med hjälp av följande skript:

```powershell
$bytes = [System.IO.File]::ReadAllBytes("C:\mycertificates\sfclustertutorialgroup220171109113527.pfx");
$b64 = [System.Convert]::ToBase64String($bytes);
[System.Io.File]::WriteAllText("C:\mycertificates\sfclustertutorialgroup220171109113527.txt", $b64);
```

I *inbound_policy* byter du ut värdet för *sf-service-instance-name* mot `fabric:/ApiApplication/WebApiService` om du tidigare har distribuerat .NET-serverdelstjänsten eller `fabric:/EchoServerApplication/EchoServerService` om du har distribuerat Java-tjänsten. *backend-id* refererar till en serverdelsresurs, i det här fallet den `Microsoft.ApiManagement/service/backends`-resurs som definierats i mallen *apim.json*. *backend-id* kan även referera till en annan serverdelsresurs som skapats med hjälp av API Management-API:erna. För den här självstudien anger du *backend-id* till värdet för parametern *service_fabric_backend_name*.

```xml
<policies>
  <inbound>
    <base/>
    <set-backend-service 
        backend-id="servicefabric"
        sf-service-instance-name="service-name"
        sf-resolve-condition="@((int)context.Response.StatusCode != 200)" />
  </inbound>
  <backend>
    <base/>
  </backend>
  <outbound>
    <base/>
  </outbound>
</policies>
```

Använd följande skript för att distribuera Resource Manager-mallen och parameterfilerna för API Management:

```powershell
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"
$templatepath="C:\clustertemplates"

New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -TemplateFile "$templatepath\network-apim.json" -TemplateParameterFile "$templatepath\network-apim.parameters.json" -Verbose

New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -TemplateFile "$templatepath\apim.json" -TemplateParameterFile "$templatepath\apim.parameters.json" -Verbose
```

```azurecli
ResourceGroupName="sfclustertutorialgroup"
az group deployment create --name ApiMgmtNetworkDeployment --resource-group $ResourceGroupName --template-file network-apim.json --parameters @network-apim.parameters.json

az group deployment create --name ApiMgmtDeployment --resource-group $ResourceGroupName --template-file apim.json --parameters @apim.parameters.json 
```

## <a name="test-it"></a>Testa den

Nu kan du testa att skicka en begäran till din serverdelstjänst i Service Fabric via API Management direkt från [Azure-portalen](https://portal.azure.com).

 1. I API Management-tjänsten väljer du **API**.
 2. I det **Service Fabric APP**-API som du skapade i föregående steg väljer du fliken **Test** väljer sedan åtgärden **Values**.
 3. Klicka på knappen **Skicka** för att skicka en testbegäran till serverdelstjänsten.  Du bör se ett HTTP-svar som detta:

    ```http
    HTTP/1.1 200 OK

    Transfer-Encoding: chunked

    Content-Type: application/json; charset=utf-8

    Vary: Origin

    Ocp-Apim-Trace-Location: https://apimgmtstodhwklpry2xgkdj.blob.core.windows.net/apiinspectorcontainer/PWSQOq_FCDjGcaI1rdMn8w2-2?sv=2015-07-08&sr=b&sig=MhQhzk%2FEKzE5odlLXRjyVsgzltWGF8OkNzAKaf0B1P0%3D&se=2018-01-28T01%3A04%3A44Z&sp=r&traceId=9f8f1892121e445ea1ae4d2bc8449ce4

    Date: Sat, 27 Jan 2018 01:04:44 GMT

    
    ["value1", "value2"]
    ```

## <a name="clean-up-resources"></a>Rensa resurser

Ett kluster består av andra Azure-resurser förutom själva klusterresursen. Det enklaste sättet att ta bort klustret och alla de resurser det använder är att ta bort resursgruppen.

Logga in på Azure och välj det prenumerations-ID som du vill ta bort klustret för.  Du hittar ditt prenumerations-ID genom att logga in på [Azure Portal](http://portal.azure.com). Ta bort resursgruppen och alla klusterresurser med cmdleten [Remove-AzureRMResourceGroup](/en-us/powershell/module/azurerm.resources/remove-azurermresourcegroup).

```powershell
$ResourceGroupName = "sfclustertutorialgroup"
Remove-AzureRmResourceGroup -Name $ResourceGroupName -Force
```

```azurecli
ResourceGroupName="sfclustertutorialgroup"
az group delete --name $ResourceGroupName
```

## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Distribuera API Management
> * Konfigurera API Management
> * Skapa en API-åtgärd
> * Konfigurera en serverdelsprincip
> * Lägga till det nya API:et till en produkt

[azure-powershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/

[apim-arm]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/apim.json
[apim-parameters-arm]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/apim.parameters.json

[network-arm]: https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/network-apim.json
[network-parameters-arm]: https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/network-apim.parameters.json

<!-- pics -->
[sf-apim-topology-overview]: ./media/service-fabric-tutorial-deploy-api-management/sf-apim-topology-overview.png
