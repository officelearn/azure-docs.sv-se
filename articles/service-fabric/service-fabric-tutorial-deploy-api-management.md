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
ms.date: 09/13/2017
ms.author: ryanwi
ms.openlocfilehash: 1238863265a227c18ebef8ac2aaeabd802f877d4
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2017
---
# <a name="deploy-api-management-with-service-fabric"></a>Distribuera API Management med Service Fabric
Den här kursen ingår tre i en serie.  Distribuera Azure API Management med Service Fabric är ett avancerat scenario användbart när du vill publicera API: er med en omfattande uppsättning regler för routning till backend-Service Fabric-tjänster. Den här kursen visar hur du ställer in [Azure API Management](../api-management/api-management-key-concepts.md) med Service Fabric att dirigera trafik till en backend-tjänst i Service Fabric.  När du är klar kan har du distribuerat API-hantering till ett virtuellt nätverk, konfigurerat en API-åtgärd för att skicka trafik till backend-tillståndslösa tjänster. Mer information om Azure API Management scenarier med Service Fabric finns i [översikt](service-fabric-api-management-overview.md) artikel.

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
> * Distribuera API Management med Service Fabric

## <a name="prerequisites"></a>Krav
Innan du börjar den här kursen:
- Om du inte har en Azure-prenumeration kan du skapa en [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Installera den [Azure Powershell Modulversion 4.1 eller högre](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) eller [Azure CLI 2.0](/cli/azure/install-azure-cli).
- Skapa en säker [Windows-kluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md) eller [Linux-kluster](service-fabric-tutorial-create-vnet-and-linux-cluster.md) på Azure
- Om du distribuerar ett Windows-kluster måste du konfigurera en Windows-utvecklingsmiljö. Installera [Visual Studio 2017](http://www.visualstudio.com) och **Azure-utveckling**, **ASP.NET och web development**, och **.NET Core plattformsoberoende development**arbetsbelastningar.  Ställa in en [.NET utvecklingsmiljö](service-fabric-get-started.md).
- Om du distribuerar en Linux-kluster måste du ställa in en Java-utvecklingsmiljö på [Linux](service-fabric-get-started-linux.md) eller [MacOS](service-fabric-get-started-mac.md).  Installera den [Service Fabric CLI](service-fabric-cli.md). 

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

## <a name="deploy-api-management"></a>Distribuera API Management
Molnprogram måste vanligtvis en frontend-gateway att tillhandahålla en enda åtkomstpunkt för inkommande trafik för användare, enheter eller andra program. I Service Fabric kan en gateway vara alla tillståndslösa tjänster, till exempel ett ASP.NET Core-program eller en annan tjänst för meddelanden om ingångs trafik, till exempel Händelsehubbar, IoT-hubb eller Azure API Management. Den här kursen är en introduktion till Azure API Management som en gateway till Service Fabric-program. API Management integreras direkt med Service Fabric, så att du kan publicera API: er med en omfattande uppsättning regler för routning till backend-Service Fabric-tjänster. 

Nu när du har en säker [Windows-kluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md) eller [Linux-kluster](service-fabric-tutorial-create-vnet-and-linux-cluster.md) på Azure, distribuera API-hantering för virtuella nätverk (VNET) i undernätet och NSG för API-hantering. I den här självstudien API Management Resource Manager-mall som är förkonfigurerad att använda namnen på virtuella nätverk, undernät och NSG: N som du skapade i föregående [Windows kluster kursen](service-fabric-tutorial-create-vnet-and-windows-cluster.md) eller [Linux-vägledningen för klustret](service-fabric-tutorial-create-vnet-and-linux-cluster.md). 

Den här kursen distribuerar följande topologin till Azure som API-hantering och Service Fabric finns i undernät i samma virtuella nätverk:

 ![Bildrubrik][sf-apim-topology-overview]

Hämta följande Resource Manager-mall och parametrar filen:
 
- [APIM.JSON][apim-arm]
- [APIM.parameters.JSON][apim-parameters-arm]

Fyll i de tomma parametrarna i den `apim.parameters.json` för din distribution.

Använd följande skript för att distribuera Resource Manager-mall och parametern filer för API-hantering:

```powershell
$ResourceGroupName = "tutorialgroup"
New-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile .\apim.json -TemplateParameterFile .\apim.parameters.json -Verbose
```

```azurecli
ResourceGroupName="tutorialgroup"
az group deployment create --name ApiMgmtDeployment --resource-group $ResourceGroupName --template-file apim.json --parameters @apim.parameters.json 
```

## <a name="configure-api-management"></a>Konfigurera API-hantering

När din API-hantering och Service Fabric-klustret har distribuerats, kan du konfigurera säkerhetsinställningar och en Service Fabric-serverdel i API-hantering. På så sätt kan du skapa en princip för backend-tjänsten som skickar trafik till Service Fabric-klustret.

### <a name="configure-api-management-security"></a>Konfigurera säkerhet för API Management

Om du vill konfigurera Service Fabric-serverdelen måste du först konfigurera säkerhetsinställningar för API-hantering. Gå till din API Management-tjänst i Azure-portalen för att konfigurera säkerhetsinställningar.

#### <a name="enable-the-api-management-rest-api"></a>Aktivera API Management REST API

API Management REST API är för närvarande det enda sättet att konfigurera en backend-tjänst. Det första steget är att aktivera API Management REST API och skydda den.

 1. Välj i API Management-tjänsten **Management API** under **säkerhet**.
 2. Kontrollera den **aktivera API Management REST API** kryssrutan.
 3. Observera den **URL för API Management**, där vi senare konfigurera Service Fabric-serverdelen.
 4. Generera en **åtkomst-Token** genom att välja ett förfallodatum och en nyckel, klicka på den **generera** knappen längst ned på sidan.
 5. Kopiera den **åtkomsttoken** och spara den.  Vi använder den åtkomst-token i följande steg. Observera att detta skiljer sig från den primära och sekundära nyckeln.

#### <a name="upload-a-service-fabric-client-certificate"></a>Överföra ett certifikat för Service Fabric

API Management måste autentisera med Service Fabric-klustret för identifiering av tjänst använder ett klientcertifikat som har åtkomst till klustret. För enkelhetens skull den här kursen använder samma certifikat som anges tidigare när du skapar den [Windows-kluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md#createvaultandcert_anchor) eller [Linux-kluster](service-fabric-tutorial-create-vnet-and-linux-cluster.md#createvaultandcert_anchor), vilket som standard kan användas för åtkomst till klustret.

 1. Välj i API Management-tjänsten **klientcertifikat** under **säkerhet**.
 2. Klicka på den **+ Lägg till** knappen.
 2. Välj privat nyckel fil (.pfx) för kluster-certifikatet som du angav när du skapar Service Fabric-kluster, ger det ett namn och ange lösenordet för privata nyckeln.

> [!NOTE]
> Den här kursen använder samma certifikat för klientautentisering och kluster nod till noden säkerhet. Du kan använda ett separat klientcertifikat om du har en konfigurerad för att komma åt Service Fabric-klustret.

### <a name="configure-the-backend"></a>Konfigurera backend

Nu när API Management-säkerheten är konfigurerad, kan du konfigurera Service Fabric-serverdelen. För Service Fabric serverdelar är Service Fabric-klustret serverdelen i stället för en specifik tjänst för Service Fabric. På så sätt kan en enda princip att dirigera till mer än en tjänst i klustret.

Det här steget kräver åtkomst-token som du tidigare genererade och tumavtrycket för certifikatet klustret som du tidigare har överförts till API-hantering.

> [!NOTE]
> Om du använder ett separat klientcertifikat i föregående steg för API-hantering, behöver du tumavtrycket för klientcertifikatet förutom klustret certifikat-tumavtrycket i det här steget.

Skicka följande HTTP PUT begäran till URL: en för API Management API du antecknade tidigare när du aktiverar API Management REST API för att konfigurera tjänsten Service Fabric-serverdel. Du bör se en `HTTP 201 Created` svar när kommandot slutförs. Mer information om varje fält finns i API Management [backend API-referensdokumentation](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#a-namebackenda-backend).

HTTP-kommando och URL:
```http
PUT https://your-apim.management.azure-api.net/backends/servicefabric?api-version=2016-10-10
```

Huvuden för begäran:
```http
Authorization: SharedAccessSignature <your access token>
Content-Type: application/json
```

Begärandetexten:
```http
{
    "description": "<description>",
    "url": "<fallback service name>",
    "protocol": "http",
    "resourceId": "<cluster HTTP management endpoint>",
    "properties": {
        "serviceFabricCluster": {
            "managementEndpoints": [ "<cluster HTTP management endpoint>" ],
            "clientCertificateThumbprint": "<client cert thumbprint>",
            "serverCertificateThumbprints": [ "<cluster cert thumbprint>" ],
            "maxPartitionResolutionRetries" : 5
        }
    }
}
```

Den **url** här parametern är en fullständigt kvalificerade namn för en tjänst i klustret som alla begäranden som dirigeras till som standard om inget namn anges i en backend-princip. Du kan använda falska tjänstnamn, till exempel ”fabric: / falska/service” om du inte vill ha en återställningsplats tjänst.

Referera till API-hantering [backend API-referensdokumentation](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#a-namebackenda-backend) för mer information om varje fält.

```python
#import requests library for making REST calls
import requests
import json

#specify url
url = 'https://your-apim.management.azure-api.net/backends/servicefabric?api-version=2016-10-10'

token = "SharedAccessSignature integration&201710140514&Lqnbei7n2Sot6doiNtxMFPUi/m9LsNa+1ZK/PdxqFl49JFWjXh1wW5Gd99r/tIOeHp6dU8UV5iZUdOPfcrm5hg=="

payload = {
    "description": "My Service Fabric backend",
    "url": "fabric:/ApiApplication/ApiWebService",
    "protocol": "http",
    "resourceId": "https://tutorialcluster.eastus.cloudapp.azure.com:19080",
    "properties": {
        "serviceFabricCluster": {
            "managementEndpoints": [ "https://tutorialcluster.eastus.cloudapp.azure.com:19080" ],
            "clientCertificateThumbprint": "97EDD7E4979FB072AF3E480A5E5EE34B1B89DD80",
            "serverCertificateThumbprints": [ "97EDD7E4979FB072AF3E480A5E5EE34B1B89DD80" ],
            "maxPartitionResolutionRetries" : 5
        }
    }
}

headers = {'Authorization': token, "Content-Type": "application/json"}

#Call REST API
response = requests.put(url, data=json.dumps(payload), headers=headers)

#Print Response
print(response.status_code)
print(response.text)
```

## <a name="deploy-a-service-fabric-back-end-service"></a>Distribuera en backend-tjänst för Service Fabric

Nu när du har den Service Fabric som konfigurerats som en serverdel för API Management skapar du principer för serverdelen för dina API: er som skickar trafik till Service Fabric-tjänster. Men du måste först en tjänst som körs i Service Fabric att acceptera begäranden.  Om du tidigare har skapat en [Windows-kluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md), distribuerar du en .NET Service Fabric-tjänst.  Om du tidigare har skapat en [Linux-kluster](service-fabric-tutorial-create-vnet-and-linux-cluster.md), distribuera en Java Service Fabric-tjänst.

### <a name="deploy-a-net-service-fabric-service"></a>Distribuera en .NET Service Fabric-tjänst

Den här självstudiekursen skapar vi en grundläggande tillståndslös ASP.NET Core tillförlitlig tjänst med standardmallen för Web API-projekt. Detta skapar en HTTP-slutpunkt för din tjänst som du exponera via Azure API Management:

```
/api/values
```

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

    Detta gör att Service Fabric anger vilken port dynamiskt från programmet portintervall, som vi öppnas via Nätverkssäkerhetsgruppen i klustret Resource Manager-mall så att trafik till den från API-hantering.
 
 6. Tryck på F5 i Visual Studio för att verifiera ditt webb-API finns lokalt. 

    Öppna Service Fabric Explorer och detaljnivån ned till en specifik instans av ASP.NET Core-tjänsten för att se basadressen tjänsten lyssnar på. Lägg till `/api/values` för basen som adressen och öppna den i en webbläsare. Detta anropar hämta-metoden på ValuesController i Web API-mallen. Standardsvar som tillhandahålls av mallen, en JSON-matris som innehåller två strängar returnerar:

    ```json
    ["value1", "value2"]`
    ```

    Det här är den slutpunkt som du exponera via API-hantering i Azure.

 7. Slutligen kan du distribuera programmet till klustret i Azure. Högerklicka på det program-projektet i Visual Studio och välj **publicera**. Ange din klusterslutpunkten (till exempel `mycluster.southcentralus.cloudapp.azure.com:19000`) att distribuera programmet till Service Fabric-kluster i Azure.

En ASP.NET Core tillståndslösa tjänsten med namnet `fabric:/ApiApplication/WebApiService` bör körs i Service Fabric-kluster i Azure.

### <a name="create-a-java-service-fabric-service"></a>Skapa en Java Service Fabric-tjänst
Vi distribuera en enkel webbserver som ekon meddelanden tillbaka till användaren för den här kursen. Exempelprogrammet echo-servern innehåller ett HTTP-slutpunkten för din tjänst som du exponera via Azure API Management.

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


## <a name="create-an-api-operation"></a>Skapa en API-åtgärd

Vi är nu redo att skapa en åtgärd i API-hantering med externa klienter som kommunicerar med ASP.NET Core tillståndslösa tjänsten som körs i Service Fabric-klustret.

1. Logga in på Azure-portalen och navigera till din API Management service-distributionen.
2. I bladet API Management-tjänsten väljer **API: er**
3. Lägg till ett nytt API genom att klicka på **+ API**, sedan **tomt API** rutan och fylla i dialogrutan:

    - **Webbtjänstens URL**: för Service Fabric serverdelar används inte den här URL-värdet. Du kan ange ett värde här. Den här kursen använder: ”http://servicefabric”.
    - **Visningsnamn**: Ange ett namn för din API. Använd ”Service Fabric-App” för den här kursen.
    - **Namnet**: Ange ”service fabric-app”.
    - **URL-schema**: väljer du antingen **HTTP**, **HTTPS**, eller **båda**. Den här kursen använder **båda**.
    - **API-URL-Suffix**: Ange ett suffix för vårt API. Använd ”myapp” för den här kursen.
 
4. Välj **Service Fabric-appen** från listan över API: er och klicka på **+ Lägg till åtgärden** att lägga till en frontend API-åtgärd. Fyll i värden:
    
    - **URL: en**: Välj **hämta** och ange en URL-sökväg för API: et. Använd ”/ api/värdena” för den här självstudiekursen.  URL-sökväg anges här skickas URL-sökvägen till serverdelen Service Fabric-tjänsten. Om du använder samma URL-sökvägen här som tjänsten använder fungerar i det här fallet ”/ api/värden” och sedan åtgärden utan ytterligare ändringar. Du kan också ange en URL-sökväg här som skiljer sig från URL-sökvägen som används av din serverdel Service Fabric-tjänsten, i vilket fall du måste också ange en sökväg omarbetning i principen för åtgärden senare.
    - **Visningsnamn**: Ange ett namn för API: et. Använd ”värdena” för den här självstudiekursen.

5. Klicka på **Spara**.

## <a name="configure-a-backend-policy"></a>Konfigurera en backend-princip

Backend-principen kopplar samman allt tillsammans. Det är där du konfigurerar Service Fabric serverdelstjänsten begäranden dirigeras. Du kan använda den här principen för alla API-åtgärder. Den [backend-konfiguration för Service Fabric](https://docs.microsoft.com/azure/api-management/api-management-transformation-policies#SetBackendService) innehåller följande begär routning kontroller: 
 - Instansurval av-tjänsten genom att ange ett Service Fabric-tjänstnamn, antingen hårdkodad (till exempel `"fabric:/myapp/myservice"`) eller genereras från HTTP-begäran (till exempel `"fabric:/myapp/users/" + context.Request.MatchedParameters["name"]`).
 - Partitionen lösning genom att generera en partitionsnyckel med alla Service Fabric-partitioneringsschema.
 - Val av replik för tillståndskänsliga tjänster.
 - Lösning försök villkor som gör att du kan ange villkor för återlösa på en plats och skicka en begäran.

För den här självstudiekursen skapar du en backend-princip som omdirigerar begäranden direkt till ASP.NET Core tillståndslösa tjänsten har distribuerats tidigare:

 1. Markera och redigera den **inkommande principer** för värden igen genom att klicka på redigeringsikonen och sedan välja **kodvy**.
 2. I Redigeraren för grupprincipobjekt kod, lägger du till en `set-backend-service` princip under inkommande principer som visas här och klicka på den **spara** knappen:
    
    ```xml
    <policies>
      <inbound>
        <base/>
        <set-backend-service 
           backend-id="servicefabric"
           sf-service-instance-name="fabric:/ApiApplication/WebApiService"
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

### <a name="add-the-api-to-a-product"></a>Lägg till API: et för en produkt 

Innan du kan anropa API: et, måste den läggas till en produkt där du kan ge åtkomst till användare. 

 1. Välj i API Management-tjänsten **produkter**.
 2. Som standard API Management providers två produkter: Start och obegränsad. Välj obegränsade produkten.
 3. Välj **+ Lägg till API: er**.
 4. Välj den `Service Fabric App` API som du skapade i föregående steg och klicka på den **Välj** knappen.

### <a name="test-it"></a>testa den

Nu kan du försöka skicka en begäran till backend-tjänsten i Service Fabric via API Management direkt från Azure-portalen.

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
$ResourceGroupName = "tutorialgroup"
Remove-AzureRmResourceGroup -Name $ResourceGroupName -Force
```

```azurecli
ResourceGroupName="tutorialgroup"
az group delete --name $ResourceGroupName
```

## <a name="conclusion"></a>Slutsats
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
