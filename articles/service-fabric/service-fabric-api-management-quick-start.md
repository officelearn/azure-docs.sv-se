---
title: Azure Service Fabric med API Management Snabbstart | Microsoft Docs
description: "Den här guiden visar hur du snabbt kommer igång med Azure API Management och Service Fabric."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: 96176149-69bb-4b06-a72e-ebbfea84454b
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/01/2017
ms.author: vturecek
ms.openlocfilehash: 2969834713fc7c2f1a2e281a6c988158d803dc45
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="service-fabric-with-azure-api-management-quick-start"></a>Service Fabric med Azure API Management-Snabbstart

Den här guiden visar hur du ställer in Azure API Management med Service Fabric och konfigurera din första API-åtgärden för att skicka trafik till backend-tjänster i Service Fabric. Mer information om Azure API Management scenarier med Service Fabric finns i [översikt](service-fabric-api-management-overview.md) artikel. 

## <a name="deploy-api-management-and-service-fabric-to-azure"></a>Distribuera API Management och Service Fabric till Azure

Det första steget är att distribuera API-hantering och ett Service Fabric-kluster till Azure i ett delat virtuella nätverk. Detta gör att API-hantering att kommunicera direkt med Service Fabric så att den kan utföra identifiering, service partition upplösning och vidarebefordra trafik direkt till alla backend-tjänster i Service Fabric.

### <a name="topology"></a>topologi

Den här guiden distribuerar följande topologin till Azure som API-hantering och Service Fabric finns i undernät i samma virtuella nätverk:

 ![Bildrubrik][sf-apim-topology-overview]

Resource Manager-mallar har angetts för varje steg i distributionen för att snabbt komma igång:

 - Nätverkstopologi:
    - [Network.JSON][network-arm]
    - [Network.parameters.JSON][network-parameters-arm]
 - Service Fabric-kluster:
    - [cluster.JSON][cluster-arm]
    - [cluster.parameters.JSON][cluster-parameters-arm]
 - API-hantering:
    - [APIM.JSON][apim-arm]
    - [APIM.parameters.JSON][apim-parameters-arm]

### <a name="sign-in-to-azure-and-select-your-subscription"></a>Logga in på Azure och välja din prenumeration

Den här guiden använder [Azure PowerShell][azure-powershell]. När du startar en ny PowerShell-session, logga in på ditt Azure-konto och välja din prenumeration innan du kan köra kommandon för Azure.
 
Logga in på ditt Azure-konto:

```powershell
Login-AzureRmAccount
```

Välj din prenumeration:

```powershell
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en ny resursgrupp för din distribution. Ange ett namn och en plats.

```powershell
New-AzureRmResourceGroup -Name <my-resource-group> -Location westus
```

### <a name="deploy-the-network-topology"></a>Distribuera nätverkets topologi

Det första steget är att ställa in nätverkets topologi som API Management och Service Fabric-klustret kommer att distribueras. Den [network.json] [ network-arm] Resource Manager-mall har konfigurerats för att skapa virtuella nätverk (VNET) med två undernät och två Nätverkssäkerhetsgrupp grupper (NSG). 

Den [network.parameters.json] [ network-parameters-arm] parameterfilen innehåller namnen på undernät och NSG: er som API Management och Service Fabric ska distribueras till. Den här guiden behöver inte parametervärden som ska ändras. API-hantering och Service Fabric Resource Manager mallar att dessa värden, så om de ändras här måste du ändra dem i andra Resource Manager-mallar i enlighet med detta. 

 1. Hämta följande Resource Manager-mall och parametrar filen:

    - [Network.JSON][network-arm]
    - [Network.parameters.JSON][network-parameters-arm]

 2. Använd följande PowerShell-kommando för att distribuera Resource Manager-mall och parametern-filer för installation av nätverk:

    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName <my-resource-group> -TemplateFile .\network.json -TemplateParameterFile .\network.parameters.json -Verbose
    ```

### <a name="deploy-the-service-fabric-cluster"></a>Distribuera Service Fabric-kluster

När nätverksresurser är klar för distribution, är nästa steg att distribuera ett Service Fabric-kluster till VNET i undernät och NSG: N för Service Fabric-klustret. För den här kursen är Service Fabric Resource Manager-mallen förkonfigurerad att använda namnen på virtuella nätverk, undernät och NSG: N som du skapade i föregående steg. 

Resource Manager-mall Service Fabric-klustret har konfigurerats för att skapa en säker kluster med Certifikatsäkerhet. Certifikatet används för säker kommunikation nod till noden för klustret och för att hantera användarnas åtkomst till Service Fabric-klustret. API Management använder detta certifikat för att få åtkomst till Service Fabric Naming Service för identifiering av tjänst.

Det här steget kräver att ett certifikat i Key Vault för Klustersäkerhet. Mer information om hur du skapar en säker kluster med Key Vault finns [den här guiden om hur du skapar ett kluster i Azure med hjälp av hanteraren för filserverresurser](service-fabric-cluster-creation-via-arm.md)

> [!NOTE]
> Du kan lägga till Azure Active Directory-autentisering utöver det certifikat som används för åtkomst till klustret. Azure Active Directory är det rekommenderade sättet att hantera användarnas åtkomst till Service Fabric-kluster, men är inte nödvändigt att slutföra den här självstudiekursen. Ett certifikat krävs oavsett hur för klustret nod till nod säkerhet och Azure API Management-autentisering, vilket inte stöder för närvarande autentisera med Azure Active Directory för en Service Fabric-serverdel.

 1. Hämta följande Resource Manager-mall och parametrar filen:
 
    - [cluster.JSON][cluster-arm]
    - [cluster.parameters.JSON][cluster-parameters-arm]

 2. Fyll i de tomma parametrarna i den `cluster.parameters.json` filen för din distribution, inklusive den [Key Vault information](service-fabric-cluster-creation-via-arm.md#set-up-a-key-vault) för kluster-certifikatet.

 3. Använd följande PowerShell-kommando för att distribuera Resource Manager-mall och parametern-filer för att skapa Service Fabric-kluster:

    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName <my-resource-group> -TemplateFile .\cluster.json -TemplateParameterFile .\cluster.parameters.json -Verbose
    ```

### <a name="deploy-api-management"></a>Distribuera API Management

Slutligen kan du distribuera API-hantering till VNET i undernät och NSG för API-hantering. Du behöver inte vänta för Service Fabric-kluster-distributionen ska slutföras innan du distribuerar API-hantering. 

För den här kursen är API Management Resource Manager-mallen förkonfigurerad att använda namnen på virtuella nätverk, undernät och NSG: N som du skapade i föregående steg. 

 1. Hämta följande Resource Manager-mall och parametrar filen:
 
    - [APIM.JSON][apim-arm]
    - [APIM.parameters.JSON][apim-parameters-arm]

 2. Fyll i de tomma parametrarna i den `apim.parameters.json` för din distribution.

 3. Använd följande PowerShell-kommando för att distribuera mallen och parametern filer för Resource Manager för API-hantering:

    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName <my-resource-group> -TemplateFile .\apim.json -TemplateParameterFile .\apim.parameters.json -Verbose
    ```

## <a name="configure-api-management"></a>Konfigurera API-hantering

När din API-hantering och Service Fabric-klustret har distribuerats, kan du konfigurera en Service Fabric-serverdel i API-hantering. På så sätt kan du skapa en princip för backend-tjänsten som skickar trafik till Service Fabric-klustret.

### <a name="api-management-security"></a>API Management-säkerhet

Om du vill konfigurera Service Fabric-serverdelen måste du först konfigurera säkerhetsinställningar för API-hantering. Gå till din API Management-tjänst i Azure-portalen för att konfigurera säkerhetsinställningar.

#### <a name="enable-the-api-management-rest-api"></a>Aktivera API Management REST API

API Management REST API är för närvarande det enda sättet att konfigurera en backend-tjänst. Det första steget är att aktivera API Management REST API och skydda den.

 1. Välj i API Management-tjänsten **Management API - PREVIEW** under **säkerhet**.
 2. Kontrollera den **aktivera API Management REST API** kryssrutan.
 3. Observera Management API-URL - detta är den URL som vi använder senare att ställa in serverdelen Service Fabric
 4. Generera en **åtkomsttoken** genom att välja ett förfallodatum och en nyckel, klicka på den **generera** knappen längst ned på sidan.
 5. Kopiera den **åtkomsttoken** och spara den – vi använder informationen i följande steg. Observera att detta skiljer sig från den primära och sekundära nyckeln.

#### <a name="upload-a-service-fabric-client-certificate"></a>Överföra ett certifikat för Service Fabric

API Management måste autentisera med Service Fabric-klustret för identifiering av tjänst använder ett klientcertifikat som har åtkomst till klustret. För enkelhetens skull använder den här kursen samma certifikat som anges när du skapar Service Fabric-kluster, vilket som standard kan användas för åtkomst till klustret.

 1. Välj i API Management-tjänsten **klientcertifikat - PREVIEW** under **säkerhet**.
 2. Klicka på den **+ Lägg till** knappen
 2. Välj privat nyckel fil (.pfx) för kluster-certifikatet som du angav när du skapar Service Fabric-kluster, ger det ett namn och ange lösenordet för privata nyckeln.

> [!NOTE]
> Den här kursen använder samma certifikat för klientautentisering och kluster nod till noden säkerhet. Du kan använda ett separat klientcertifikat om du har en konfigurerad för att komma åt Service Fabric-klustret.

### <a name="configure-the-backend"></a>Konfigurera backend

Nu när API Management-säkerheten är konfigurerad, kan du konfigurera Service Fabric-serverdelen. För Service Fabric serverdelar är Service Fabric-klustret serverdelen i stället för en specifik tjänst för Service Fabric. På så sätt kan en enda princip att dirigera till mer än en tjänst i klustret.

Det här steget kräver åtkomst-token som du tidigare genererade och tumavtrycket för ditt kluster-certifikat som du har överfört till API-hantering i föregående steg.

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

Den **url** här parametern är en fullständigt kvalificerade namn för en tjänst i klustret som alla begäranden som dirigeras till som standard om inget namn anges i en backend-princip. Du kan använda falska tjänstnamn, till exempel ”fabric: / falska/service” om du inte vill ha en återställningsplats tjänst. Observera den **url** måste vara i formatet ”fabric: / app/service” även om det är en falsk återställningsplats tjänst.

Referera till API-hantering [backend API-referensdokumentation](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#a-namebackenda-backend) för mer information om varje fält.

#### <a name="example"></a>Exempel

```http
PUT https://your-apim.management.azure-api.net/backends/servicefabric?api-version=2016-10-10
Authorization: SharedAccessSignature 230948023984&Ld93cRGcNU6KZ4uVz7JlfTec4eX43Q9Nu8ndatOgBzs6+f559Pkf3iHX2cSge+r42pn35qGY3TitjrIl13hwcQ==
Content-Type: application/json

{
    "description": "My Service Fabric backend",
    "url": "fabric:/myapp/myservice",
    "protocol": "http",
    "resourceId": "https://your-cluster.westus.cloudapp.azure.com:19080",
    "properties": {
        "serviceFabricCluster": {
            "managementEndpoints": ["https://your-cluster.westus.cloudapp.azure.com:19080"],
            "clientCertificateThumbprint": "57bc463aba3aea3a12a18f36f44154f819f0fe32",
            "serverCertificateThumbprints": ["57bc463aba3aea3a12a18f36f44154f819f0fe32"],
            "maxPartitionResolutionRetries" : 5
        }
    }
}
```

## <a name="deploy-a-service-fabric-back-end-service"></a>Distribuera en backend-tjänst för Service Fabric

Nu när du har den Service Fabric som konfigurerats som en serverdel för API Management skapar du principer för serverdelen för dina API: er som skickar trafik till Service Fabric-tjänster. Men du måste först en tjänst som körs i Service Fabric att acceptera begäranden.

### <a name="create-a-service-fabric-service-with-an-http-endpoint"></a>Skapa ett Service Fabric-tjänsten med en HTTP-slutpunkt

Den här självstudiekursen skapar vi en grundläggande tillståndslös ASP.NET Core tillförlitlig tjänst med standardmallen för Web API-projekt. Detta skapar en HTTP-slutpunkt för din tjänst som du ska visa genom Azure API Management:

```
/api/values
```

Börja med [ställa in din utvecklingsmiljö för utveckling av ASP.NET Core](service-fabric-add-a-web-frontend.md#set-up-your-environment-for-aspnet-core).

När din utvecklingsmiljö har ställts in, starta Visual Studio som administratör och skapa en ASP.NET Core-tjänst:

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

    Det här är den slutpunkt som du ska visa genom API Management i Azure.

 7. Slutligen kan du distribuera programmet till klustret i Azure. [Med Visual Studio](service-fabric-publish-app-remote-cluster.md#to-publish-an-application-using-the-publish-service-fabric-application-dialog-box), högerklicka på projektet och välj **publicera**. Ange din klusterslutpunkten (till exempel `mycluster.westus.cloudapp.azure.com:19000`) att distribuera programmet till Service Fabric-kluster i Azure.

En ASP.NET Core tillståndslösa tjänsten med namnet `fabric:/ApiApplication/WebApiService` bör körs i Service Fabric-kluster i Azure.

## <a name="create-an-api-operation"></a>Skapa en API-åtgärd

Vi är nu redo att skapa en åtgärd i API-hantering med externa klienter som kommunicerar med ASP.NET Core tillståndslösa tjänsten som körs i Service Fabric-klustret.

 1. Logga in på Azure-portalen och navigera till din API Management service-distributionen.
 2. I bladet API Management-tjänsten väljer **API: er – förhandsgranskning**
 3. Lägg till ett nytt API genom att klicka på den **tomt API** rutan och fylla i dialogrutan:

     - **Webbtjänstens URL**: för Service Fabric serverdelar används inte den här URL-värdet. Du kan ange ett värde här. Den här kursen använder: `http://servicefabric`.
     - **Namnet**: Ange ett namn för din API. Den här kursen använder `Service Fabric App`.
     - **URL-schema**: Välj HTTP, HTTPS eller båda. Den här kursen använder `both`.
     - **API-URL-Suffix**: Ange ett suffix för vårt API. Den här kursen använder `myapp`.
 
 4. När API: N har skapats klickar du på **+ Lägg till åtgärden** att lägga till en frontend API-åtgärd. Fyll i värden:
    
     - **URL: en**: Välj `GET` och ange en URL-sökväg för API: et. Den här kursen använder `/api/values`.
     
       URL-sökväg anges här skickas URL-sökvägen till serverdelen Service Fabric-tjänsten. Om du använder samma URL-sökvägen här som tjänsten använder i det här fallet `/api/values`, sedan åtgärden fungerar utan ytterligare ändringar. Du kan också ange en URL-sökväg här som skiljer sig från URL-sökvägen som används av din serverdel Service Fabric-tjänsten, i så fall behöver du också ange en sökväg omarbetning i principen för åtgärden senare.
     - **Visningsnamn**: Ange ett namn för API: et. Den här kursen använder `Values`.

## <a name="configure-a-backend-policy"></a>Konfigurera en backend-princip

Backend-principen kopplar samman allt tillsammans. Det är där du konfigurerar Service Fabric serverdelstjänsten begäranden dirigeras. Du kan använda den här principen för alla API-åtgärder. Den [backend-konfiguration för Service Fabric](https://docs.microsoft.com/azure/api-management/api-management-transformation-policies#SetBackendService) innehåller följande begär routning kontroller: 
 - Instansurval av-tjänsten genom att ange ett Service Fabric-tjänstnamn, antingen hårdkodad (till exempel `"fabric:/myapp/myservice"`) eller genereras från HTTP-begäran (till exempel `"fabric:/myapp/users/" + context.Request.MatchedParameters["name"]`).
 - Partitionen lösning genom att generera en partitionsnyckel med alla Service Fabric-partitioneringsschema.
 - Val av replik för tillståndskänsliga tjänster.
 - Lösning försök villkor som gör att du kan ange villkor för återlösa på en plats och skicka en begäran.

För den här självstudiekursen skapar du en backend-princip som omdirigerar begäranden direkt till ASP.NET Core tillståndslösa tjänsten har distribuerats tidigare:

 1. Markera och redigera den **inkommande principer** för den `Values` igen genom att klicka på redigeringsikonen och sedan välja **kodvy**.
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

### <a name="add-the-api-to-a-product"></a>Lägg till API: et för en produkt. 

Innan du kan anropa API: et, måste den läggas till en produkt där du kan ge åtkomst till användare. 

 1. Välj i API Management-tjänsten **produkter - PREVIEW**.
 2. Som standard API Management providers två produkter: Start och obegränsad. Välj obegränsade produkten.
 3. Välj API: er.
 4. Klicka på den **+ Lägg till** knappen.
 5. Välj den `Service Fabric App` API som du skapade i föregående steg och klicka på den **Välj** knappen.

### <a name="test-it"></a>testa den

Nu kan du försöka skicka en begäran till backend-tjänsten i Service Fabric via API Management direkt från Azure-portalen.

 1. Välj i API Management-tjänsten **API - PREVIEW**.
 2. I den `Service Fabric App` API som du skapade i föregående steg, Välj den **Test** fliken.
 3. Klicka på den **skicka** knappen Skicka ett testbegäran till backend-tjänsten.

## <a name="next-steps"></a>Nästa steg

Du bör nu ha en grundläggande installation med Service Fabric och API-hantering.

Den här kursen använder grundläggande certifikatbaserad användarautentisering för Service Fabric-kluster som du kan komma igång snabbt. Mer avancerade användarautentisering för Service Fabric-kluster är bättre med [Azure Active Directory-autentisering](service-fabric-cluster-creation-via-arm.md#set-up-azure-active-directory-for-client-authentication). 

Nästa [skapa och konfigurera avancerad produktinställningarna i Azure API Management](https://docs.microsoft.com/azure/api-management/api-management-howto-product-with-rules) att förbereda ditt program för verkliga världen trafik.

<!-- links -->
[azure-powershell]:https://azure.microsoft.com/documentation/articles/powershell-install-configure/

[network-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.json
[network-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.parameters.json

[apim-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/apim.json
[apim-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/apim.parameters.json

[cluster-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.json
[cluster-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.parameters.json


<!-- pics -->
[sf-apim-topology-overview]: ./media/service-fabric-api-management-quickstart/sf-apim-topology-overview.png
