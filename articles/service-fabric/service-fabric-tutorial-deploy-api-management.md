---
title: Integrera Azure Service Fabric med API-hantering | Microsoft Docs
description: "Lär dig att snabbt komma igång med Azure API Management och Service Fabric."
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
ms.date: 11/10/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 722a3f0f428bb972b2835df65a67707bf4d8e7d7
ms.sourcegitcommit: 4ac89872f4c86c612a71eb7ec30b755e7df89722
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2017
---
# <a name="deploy-api-management-with-service-fabric"></a>Distribuera API Management med Service Fabric
Den här kursen ingår fyra i en serie.  Distribuera Azure API Management med Service Fabric är ett avancerat scenario.  API-hantering är användbart när du vill publicera API: er med en omfattande uppsättning regler för vidarebefordran för backend-Service Fabric-tjänster. Molnprogram måste vanligtvis en frontend-gateway att tillhandahålla en enda åtkomstpunkt för inkommande trafik för användare, enheter eller andra program. En gateway kan vara alla tillståndslösa tjänster som utformats för trafik ingång, till exempel en APP.NET Core programmet Event Hubs, IoT-hubb eller Azure API Management i Service Fabric. 

Den här kursen visar hur du ställer in [Azure API Management](../api-management/api-management-key-concepts.md) med Service Fabric att dirigera trafik till en backend-tjänst i Service Fabric.  När du är klar kan har du distribuerat API-hantering till ett virtuellt nätverk, konfigurerat en API-åtgärd för att skicka trafik till backend-tillståndslösa tjänster. Mer information om Azure API Management scenarier med Service Fabric finns i [översikt](service-fabric-api-management-overview.md) artikel.

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Distribuera API Management
> * Konfigurera API-hantering
> * Skapa en API-åtgärd
> * Konfigurera en backend-princip
> * Lägg till API: et för en produkt

I den här självstudiekursen serien lär du dig hur du:
> [!div class="checklist"]
> * Skapa en säker [Windows-kluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md) eller [Linux-kluster](service-fabric-tutorial-create-vnet-and-linux-cluster.md) på Azure med hjälp av en mall
> * [Skala ett kluster in eller ut](/service-fabric-tutorial-scale-cluster.md)
> * [Uppgraderingen av körtiden för ett kluster](service-fabric-tutorial-upgrade-cluster.md)
> * Distribuera API Management med Service Fabric

## <a name="prerequisites"></a>Krav
Innan du börjar den här kursen:
- Om du inte har en Azure-prenumeration kan du skapa en [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Installera den [Azure Powershell Modulversion 4.1 eller högre](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) eller [Azure CLI 2.0](/cli/azure/install-azure-cli).
- Skapa en säker [Windows-kluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md) eller [Linux-kluster](service-fabric-tutorial-create-vnet-and-linux-cluster.md) på Azure
- Om du distribuerar ett Windows-kluster måste du konfigurera en Windows-utvecklingsmiljö. Installera [Visual Studio 2017](http://www.visualstudio.com) och **Azure-utveckling**, **ASP.NET och web development**, och **.NET Core plattformsoberoende development**arbetsbelastningar.  Ställa in en [.NET utvecklingsmiljö](service-fabric-get-started.md).
- Om du distribuerar en Linux-kluster måste du ställa in en Java-utvecklingsmiljö på [Linux](service-fabric-get-started-linux.md) eller [MacOS](service-fabric-get-started-mac.md).  Installera den [Service Fabric CLI](service-fabric-cli.md). 

## <a name="network-topology"></a>Nätverkstopologi
Nu när du har en säker [Windows-kluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md) eller [Linux-kluster](service-fabric-tutorial-create-vnet-and-linux-cluster.md) på Azure, distribuera API-hantering för virtuella nätverk (VNET) i undernätet och NSG för API-hantering. I den här självstudien API Management Resource Manager-mall som är förkonfigurerad att använda namnen på virtuella nätverk, undernät och NSG: N som du skapade i föregående [Windows kluster kursen](service-fabric-tutorial-create-vnet-and-windows-cluster.md) eller [Linux-vägledningen för klustret](service-fabric-tutorial-create-vnet-and-linux-cluster.md). Den här kursen distribuerar följande topologin till Azure som API-hantering och Service Fabric finns i undernät i samma virtuella nätverk:

 ![Bildrubrik][sf-apim-topology-overview]

## <a name="sign-in-to-azure-and-select-your-subscription"></a>Logga in på Azure och välja din prenumeration
Logga in på ditt Azure-konto väljer din prenumeration innan du kan köra kommandon för Azure.

```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

```azurecli
az login
az account set --subscription <guid>
```

## <a name="deploy-a-service-fabric-back-end-service"></a>Distribuera en backend-tjänst för Service Fabric

Innan du konfigurerar API-hantering för att dirigera trafik till en backend-tjänst för Service Fabric, måste du först en tjänst som körs för att acceptera begäranden.  Om du tidigare har skapat en [Windows-kluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md), distribuerar du en .NET Service Fabric-tjänst.  Om du tidigare har skapat en [Linux-kluster](service-fabric-tutorial-create-vnet-and-linux-cluster.md), distribuera en Java Service Fabric-tjänst.

### <a name="deploy-a-net-service-fabric-service"></a>Distribuera en .NET Service Fabric-tjänst

Skapa en grundläggande tillståndslös ASP.NET Core tillförlitlig tjänst med standardmallen för Web API-projekt för den här självstudiekursen. Detta skapar en HTTP-slutpunkt för din tjänst som du exponera via Azure API Management.

Starta Visual Studio som administratör och skapa en ASP.NET Core-tjänst:

 1. Välj Arkiv -> Nytt projekt i Visual Studio.
 2. Välj mall för Service Fabric program i molnet och ger den namnet **”ApiApplication”**.
 3. Välj mallen ASP.NET Core-tjänst och namnge projektet **”WebApiService”**.
 4. Välj mall för Web API ASP.NET Core 1.1-projekt.
 5. När projektet har skapats kan du öppna `PackageRoot\ServiceManifest.xml` och ta bort den `Port` attribut från slutpunktskonfigurationen för resursen:
 
    ```xml
    <Resources>
      <Endpoints>
        <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" />
      </Endpoints>
    </Resources>
    ```

    Om du tar bort porten kan Service Fabric anger vilken port dynamiskt från portintervallet programmet öppnas via Nätverkssäkerhetsgruppen i klustret Resource Manager-mall så att trafik till den från API-hantering.
 
 6. Tryck på F5 i Visual Studio för att verifiera ditt webb-API finns lokalt. 

    Öppna Service Fabric Explorer och detaljnivån ned till en specifik instans av ASP.NET Core-tjänsten för att se basadressen tjänsten lyssnar på. Lägg till `/api/values` för basen som adressen och öppna den i en webbläsare, som anropar hämta-metoden på ValuesController i Web API-mallen. Standardsvar som tillhandahålls av mallen, en JSON-matris som innehåller två strängar returnerar:

    ```json
    ["value1", "value2"]`
    ```

    Det här är den slutpunkt som du exponera via API-hantering i Azure.

 7. Slutligen kan du distribuera programmet till klustret i Azure. Högerklicka på det program-projektet i Visual Studio och välj **publicera**. Ange din klusterslutpunkten (till exempel `mycluster.southcentralus.cloudapp.azure.com:19000`) att distribuera programmet till Service Fabric-kluster i Azure.

En ASP.NET Core tillståndslösa tjänsten med namnet `fabric:/ApiApplication/WebApiService` bör körs i Service Fabric-kluster i Azure.

### <a name="create-a-java-service-fabric-service"></a>Skapa en Java Service Fabric-tjänst
För den här självstudiekursen distribuerar en enkel webbserver som ekon tillbaka till användaren. Exempelprogrammet echo-servern innehåller ett HTTP-slutpunkten för din tjänst som du exponera via Azure API Management.

1. Klona Java komma igång prover.

   ```bash
   git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git
   cd service-fabric-java-getting-started
   ```

2. Redigera *Services/EchoServer/EchoServer1.0/EchoServerApplication/EchoServerPkg/ServiceManifest.xml*. Uppdatera slutpunkten så att tjänsten lyssnar på port 8081.

   ```xml
   <Endpoint Name="WebEndpoint" Protocol="http" Port="8081" />
   ```

3. Spara *ServiceManifest.xml*, skapa EchoServer1.0 programmet.

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

   En tillståndslös Java-tjänst med namnet `fabric:/EchoServerApplication/EchoServerService` bör körs i Service Fabric-kluster i Azure.

5. Öppna en webbläsare och Skriv i http://mycluster.southcentralus.cloudapp.azure.com:8081/getMessage, bör du se ”[version 1.0] Hello World”! visas.

## <a name="download-and-understand-the-resource-manager-template"></a>Hämta och förstå Resource Manager-mall
Hämta och spara följande Resource Manager-mall och parametrar:
 
- [APIM.JSON][apim-arm]
- [APIM.parameters.JSON][apim-parameters-arm]

I följande avsnitt beskrivs de resurser som definieras av den *apim.json* mall. Följ länkarna till referensdokumentationen mall i varje avsnitt för mer information. De konfigurerbara parametrar som definierats i den *apim.parameters.json* parameterfilen ställs senare i den här artikeln.

### <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service
[Microsoft.ApiManagement/service](/azure/templates/microsoft.apimanagement/service) beskriver API Management service-instans: namn, SKU eller nivå, resursgruppens plats, utgivarinformation och virtuella nätverk.

### <a name="microsoftapimanagementservicecertificates"></a>Microsoft.ApiManagement/service/certificates
[Microsoft.ApiManagement/service/certificates](/azure/templates/microsoft.apimanagement/service/certificates) konfigureras API-hantering. API Management måste autentisera med Service Fabric-klustret för identifiering av tjänst använder ett klientcertifikat som har åtkomst till klustret. Den här kursen använder samma certifikat som anges tidigare när du skapar den [Windows-kluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md#createvaultandcert_anchor) eller [Linux-kluster](service-fabric-tutorial-create-vnet-and-linux-cluster.md#createvaultandcert_anchor), vilket som standard kan användas för åtkomst till klustret. 

Den här kursen använder samma certifikat för klientautentisering och kluster nod till noden säkerhet. Du kan använda ett separat klientcertifikat om du har en konfigurerad för att komma åt Service Fabric-klustret. Ange den **namn**, **lösenord**, och **data** (base64-kodad sträng) för privat nyckel fil (.pfx) för kluster-certifikatet som du angav när du skapar din Service Fabric-klustret.

### <a name="microsoftapimanagementservicebackends"></a>Microsoft.ApiManagement/service/backends
[Microsoft.ApiManagement/service/backends](/azure/templates/microsoft.apimanagement/service/backends) beskriver serverdelstjänsten trafik vidarebefordras till. 

Service Fabric-klustret är serverdelen i stället för en specifik Service Fabric-tjänst för Service Fabric serverdelar. På så sätt kan en enda princip att dirigera till mer än en tjänst i klustret. Den **url** fältet här är ett fullständigt kvalificerade namn för en tjänst i klustret som alla begäranden som dirigeras till som standard om inget namn anges i en backend-princip. Du kan använda falska tjänstnamn, till exempel ”fabric: / falska/service” om du inte vill ha en återställningsplats tjänst. **resourceId** anger klusterslutpunkten för hantering.  **clientCertificateThumbprint** och **serverCertificateThumbprints** identifiera certifikat som används för att autentisera med klustret.

### <a name="microsoftapimanagementserviceproducts"></a>Microsoft.ApiManagement/service/products
[Microsoft.ApiManagement/service/products](/azure/templates/microsoft.apimanagement/service/products) skapar en produkt. En produkt innehåller en eller flera API: er som en kvot för användning och villkor för användning i Azure API Management. När en produkt har publicerats kan utvecklare prenumererar på produkten och börjar använda produktens API: er. 

Ange ett beskrivande **displayName** och **beskrivning** för produkten. En prenumeration krävs men prenumeration godkännande av en administratör är inte i den här självstudien.  Den här produkten **tillstånd** ”publiceras” och är synligt för prenumeranter. 

### <a name="microsoftapimanagementserviceapis"></a>Microsoft.ApiManagement/service/apis
[Microsoft.ApiManagement/service/apis](/azure/templates/microsoft.apimanagement/service/apis) skapar en API. En API i API Management representerar en uppsättning åtgärder som kan anropas av klientprogram. När åtgärderna har lagts till API: et har lagts till i en produkt och kan publiceras. När en API som har publicerats kan den prenumererar på och användas av utvecklare.

- **displayName** kan vara vilket namn för din API. Använd ”Service Fabric-App” för den här kursen.
- **namnet** ger ett unikt och beskrivande namn för API: et, till exempel ”service fabric-app”. Den visas i portaler utvecklare och utgivare. 
- **serviceUrl** refererar till HTTP-tjänsten implementerar API: et. API-hantering vidarebefordrar begäranden till den här adressen. Den här URL-värdet används inte för Service Fabric serverdelar. Du kan ange ett värde här. Den här självstudiekursen, till exempel ”http://servicefabric”. 
- **sökvägen** läggs till en bas-URL för API management-tjänsten. Bas som URL: en är gemensamma för alla API: er som en instans för API Management-tjänsten är värd för. API Management särskiljer API: er av deras suffix och därmed suffixet måste vara unikt för alla API: et för en viss utgivare. 
- **protokoll** bestämma vilket protokoll som kan användas för åtkomst till API. Den här självstudiekursen listan **http** och **https**.
- **sökvägen** är ett suffix för API: et. Använd ”myapp” för den här kursen.

### <a name="microsoftapimanagementserviceapisoperations"></a>Microsoft.ApiManagement/service/apis/operations
[Microsoft.ApiManagement/service/apis/operations](/azure/templates/microsoft.apimanagement/service/apis/operations) innan en API i API Management kan användas, åtgärder måste läggas till API: et.  Externa klienter använder en åtgärd för att kommunicera med ASP.NET Core tillståndslösa tjänsten som körs i Service Fabric-klustret.

Fyll i värden för att lägga till en frontend API-åtgärd:

- **displayName** och **beskrivning** beskrivs åtgärden. Använd ”värdena” för den här självstudiekursen.
- **metoden** anger HTTP-verbet.  Den här självstudiekursen, ange **hämta**.
- **urlTemplate** läggs till i den grundläggande Webbadressen för API: et och identifierar en enskild HTTP-åtgärd.  Den här kursen använder `/api/values` om du har lagt till serverdelstjänsten .NET eller `getMessage` om du har lagt till serverdelstjänsten Java.  URL-sökväg anges här skickas URL-sökvägen till serverdelen Service Fabric-tjänsten. Om du använder samma URL-sökvägen här som tjänsten använder, till exempel ”/ api/värden”, fungerar igen utan ytterligare ändringar. Du kan också ange en URL-sökväg här som skiljer sig från URL-sökvägen som används av din serverdel Service Fabric-tjänsten, i vilket fall du måste också ange en sökväg omarbetning i principen för åtgärden senare.

### <a name="microsoftapimanagementserviceapispolicies"></a>Microsoft.ApiManagement/service/apis/policies
[Microsoft.ApiManagement/service/apis/policies](/azure/templates/microsoft.apimanagement/service/apis/policies) skapar en princip för serverdelen som kopplar samman allt tillsammans. Det är där du konfigurerar Service Fabric serverdelstjänsten begäranden dirigeras. Du kan använda den här principen för alla API-åtgärder.  Mer information finns i [översikt över principer](/azure/api-management/api-management-howto-policies). 

Den [backend-konfiguration för Service Fabric](/azure/api-management/api-management-transformation-policies#SetBackendService) innehåller följande begär routning kontroller: 
 - Instansurval av-tjänsten genom att ange ett Service Fabric-tjänstnamn, antingen hårdkodad (till exempel `"fabric:/myapp/myservice"`) eller genereras från HTTP-begäran (till exempel `"fabric:/myapp/users/" + context.Request.MatchedParameters["name"]`).
 - Partitionen lösning genom att generera en partitionsnyckel med alla Service Fabric-partitioneringsschema.
 - Val av replik för tillståndskänsliga tjänster.
 - Lösning försök villkor som gör att du kan ange villkor för återlösa på en plats och skicka en begäran.

**policyContent** är Json föregås av XML-innehållet i principen.  För den här självstudiekursen skapar du en backend-princip på vägen begäranden direkt till .NET eller Java tillståndslösa tjänsten har distribuerats tidigare. Lägg till en `set-backend-service` princip under inkommande principer.  Ersätt ”tjänstnamn” med `fabric:/ApiApplication/WebApiService` om du tidigare har distribuerat serverdelstjänst .NET eller `fabric:/EchoServerApplication/EchoServerService` om du har distribuerat Java-tjänsten.
    
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

För en fullständig uppsättning attribut för Service Fabric-backend-principen, referera till den [API Management backend-dokumentation](https://docs.microsoft.com/azure/api-management/api-management-transformation-policies#SetBackendService)

## <a name="set-parameters-and-deploy-api-management"></a>Ange parametrar och distribuera API Management
Fyll i följande tomma parametrar i den *apim.parameters.json* för din distribution. 

|Parameter|Värde|
|---|---|
|apimInstanceName|SA-apim|
|apimPublisherEmail|myemail@contosos.com|
|apimSku|Developer|
|serviceFabricCertificateName|sfclustertutorialgroup320171031144217|
|CertificatePassword|q6D7nN %6ck@6| 
|serviceFabricCertificateThumbprint|C4C1E541AD512B8065280292A8BA6079C3F26F10 |
|serviceFabricCertificate|&lt;Base64-kodad sträng&gt;|
|URL-sökväg|/ api/värden|
|clusterHttpManagementEndpoint|https://mysfcluster.southcentralus.cloudapp.Azure.com:19080|
|inbound_policy|&lt;XML-strängen&gt;|

*certificatePassword* och *serviceFabricCertificateThumbprint* måste matcha klustret certifikatet som används för att skapa klustret.  

*serviceFabricCertificate* är certifikatet som en Base64-kodad sträng, vilken kan genereras med hjälp av följande skript:

```powershell
$bytes = [System.IO.File]::ReadAllBytes("C:\mycertificates\sfclustertutorialgroup220171109113527.pfx");
$b64 = [System.Convert]::ToBase64String($bytes);
[System.Io.File]::WriteAllText("C:\mycertificates\sfclustertutorialgroup220171109113527.txt", $b64);
```

I *inbound_policy*, Ersätt ”tjänstnamn” med `fabric:/ApiApplication/WebApiService` om du tidigare har distribuerat serverdelstjänst .NET eller `fabric:/EchoServerApplication/EchoServerService` om du har distribuerat Java-tjänsten.

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

Använd följande skript för att distribuera Resource Manager-mall och parametern filer för API-hantering:

```powershell
$ResourceGroupName = "sfclustertutorialgroup"
New-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile .\apim.json -TemplateParameterFile .\apim.parameters.json -Verbose
```

```azurecli
ResourceGroupName="sfclustertutorialgroup"
az group deployment create --name ApiMgmtDeployment --resource-group $ResourceGroupName --template-file apim.json --parameters @apim.parameters.json 
```

## <a name="test-it"></a>testa den

Du kan prova att skicka en begäran till backend-tjänsten i Service Fabric via API Management direkt från den [Azure-portalen](https://portal.azure.com).

 1. Välj i API Management-tjänsten **API**.
 2. I den **Service Fabric-appen** API som du skapade i föregående steg, Välj den **Test** fliken och sedan den **värden** igen.
 3. Klicka på den **skicka** knappen Skicka ett testbegäran till backend-tjänsten.  Du bör se en HTTP-svar som liknar:

    ```http
    HTTP/1.1 200 OK

    Transfer-Encoding: chunked

    Content-Type: application/json; charset=utf-8

    Vary: Origin

    Access-Control-Allow-Origin: https://apimanagement.hosting.portal.azure.net

    Access-Control-Allow-Credentials: true

    Access-Control-Expose-Headers: Transfer-Encoding,Date,Server,Vary,Ocp-Apim-Trace-Location

    Ocp-Apim-Trace-Location: https://apimgmtstuvyx3wa3oqhdbwy.blob.core.windows.net/apiinspectorcontainer/RaVVuJBQ9yxtdyH55BAsjQ2-1?sv=2015-07-08&sr=b&sig=Ab6dPyLpTGAU6TdmlEVu32DMfdCXTiKAASUlwSq3jcY%3D&se=2017-09-15T05%3A49%3A53Z&sp=r&traceId=ed9f1f4332e34883a774c34aa899b832

    Date: Thu, 14 Sep 2017 05:49:56 GMT


    [
    "value1",
    "value2"
    ]
    ```

## <a name="clean-up-resources"></a>Rensa resurser

Ett kluster består av andra Azure-resurser förutom själva klusterresursen. Det enklaste sättet att ta bort klustret och alla de resurser det använder är att ta bort resursgruppen.

Logga in på Azure och välj prenumerations-ID som du vill ta bort klustret.  Du hittar prenumerations-ID genom att logga in på [Azure-portalen](http://portal.azure.com). Ta bort resursgruppen och alla klusterresurser med hjälp av den [cmdlet Remove-AzureRMResourceGroup](/en-us/powershell/module/azurerm.resources/remove-azurermresourcegroup).

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
> * Konfigurera API-hantering
> * Skapa en API-åtgärd
> * Konfigurera en backend-princip
> * Lägg till API: et för en produkt

[azure-powershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/

[apim-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/apim.json
[apim-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/apim.parameters.json

[network-arm]: https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.json
[network-parameters-arm]: https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.parameters.json

[cluster-arm]: https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.json
[cluster-parameters-arm]: https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.parameters.json

<!-- pics -->
[sf-apim-topology-overview]: ./media/service-fabric-tutorial-deploy-api-management/sf-apim-topology-overview.png
