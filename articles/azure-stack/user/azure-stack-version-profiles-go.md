---
title: Med hjälp av API-versionsprofiler med GO i Azure Stack | Microsoft Docs
description: Lär dig mer om hur du använder API-versionsprofiler med GO i Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2018
ms.author: sethm
ms.reviewer: sijuman
ms.openlocfilehash: 9ad4402098e938f72cf4b8c61cce8d0d46b5a147
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2018
ms.locfileid: "42055200"
---
# <a name="use-api-version-profiles-with-go-in-azure-stack"></a>Använd API-versionsprofiler med Go i Azure Stack

*Gäller för: integrerade Azure Stack-system och Azure Stack Development Kit*

## <a name="go-and-version-profiles"></a>Profiler för go och version

En profil är en kombination av olika resurstyper med olika versioner från olika tjänster. Genom att använda en profil kan du blanda och matcha mellan olika resurstyper. Profiler kan ange:

 - Stabiliteten för ditt program genom att låsa till specifika API-versioner.
 - Kompatibilitet för ditt program med Azure Stack och regionala Azure-datacenter.

I Go-SDK profiler är tillgängliga under profilerna / sökväg med versionerna i den **åååå-MM-DD** format. Just nu den senaste Azure Stack är profilversionen **2017-03-09**. Om du vill importera en viss tjänst från en profil, måste du importera den motsvarande modulen från profilen. Till exempel för att importera **Compute** tjänsten från **2017-03-09** profil:

````go
import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/compute/mgmt/compute" 
````

## <a name="install-azure-sdk-for-go"></a>Installera Azure SDK för Go

  1. Installera Git. Anvisningar finns i [komma igång - installerar Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
  2. Installera den [programmering språket Go](https://golang.org/dl).  
  API-profiler för Azure kräver Go version 1.9 eller senare.
  3. Installera Go Azure SDK och dess beroenden genom att köra följande bash-kommando:
  ```
    go get -u -d github.com/Azure/azure-sdk-for-go/...
  ```

### <a name="the-go-sdk"></a>GO-SDK

Du hittar mer information om Azure GO SDK på:
- Azure går SDK på [installera Azure SDK för Go](https://docs.microsoft.com/go/azure/azure-sdk-go-install).
- Azure Go SDK finns på GitHub på [azure sdk för go](https://github.com/Azure/azure-sdk-for-go).

### <a name="go-autorest-dependencies"></a>Go-AutoRest-beroenden

GO SDK är beroende av Azure Go-AutoRest-moduler för att skicka REST-begäranden till Azure Resource Manager-slutpunkter. Du måste importera Azure Go-AutoRest modulberoenden från [Azure Go-AutoRest på GitHub](https://github.com/Azure/go-autorest). Du kan hitta install bash-kommandon i den **installera** avsnittet.

## <a name="how-to-use-go-sdk-profiles-on-azure-stack"></a>Använd profiler för GO SDK på Azure Stack

Så här kör du ett exempel på Go-koden på Azure Stack:
  1. Installera Azure SDK för Go och dess beroenden. Anvisningar finns i avsnittet ovan [installera Azure SDK för Go](#install-azure-sdk-for-go).
  2. Hämta metadata-information från Resource Manager-slutpunkten. Slutpunkten som returnerar en JSON-fil med den information som krävs för att köra Go-kod.

  > [!Note]  
  > Den **ResourceManagerUrl** i Azure Stack Development Kit (ASDK) är: `https://management.local.azurestack.external/`  
  > Den **ResourceManagerUrl** integrerade system är: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/`  
  > Att hämta de metadata som krävs: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`
  
  JSON-exempelfilen:

  ```json
  { "galleryEndpoint": "https://portal.local.azurestack.external:30015/",  
    "graphEndpoint": "https://graph.windows.net/",  
    "portal Endpoint": "https://portal.local.azurestack.external/", 
    "authentication": {
      "loginEndpoint": "https://login.windows.net/", 
      "audiences": ["https://management.<yourtenant>.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
    }
  }
  ```

  3. Om det inte finns skapar du en prenumeration och spara prenumerations-ID som ska användas senare. Information om hur du skapar en prenumeration finns i [skapa prenumerationer för erbjudanden i Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm). 
  4. Skapa ett huvudnamn för tjänsten med ”prenumeration” omfattning och **ägare** roll. Spara tjänstens huvudnamn-ID och hemlighet. Information om hur du skapar ett huvudnamn för tjänsten för Azure Stack finns i [skapa tjänstens huvudnamn](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#create-service-principal-for-azure-ad). Azure Stack-miljön har ställts in.
  5. Importera en service-modul från Go SDK-profil i din kod. Den aktuella versionen av Azure Stack-profil är **2017-03-09**. Till exempel för att importera modulen Utjämning från **2017-03-09** Profiltyp: 

  ````go
    package main 
    import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"
  ````
  
  6. Skapa i din funktion och autentisera en klient med en **New** klienten funktionsanrop. Du kan använda följande kod för att skapa en vnet-klient:  

  ````go
  package main 

  import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network" 

  func main() { 
      vnetClient := network.NewVirtualNetworksClientWithBaseURI("<baseURI>", "(subscriptionID>")
      vnetClient.Authorizer = autorest.NewBearerAuthorizer(token)
  ````

  Ange `<baseURI>` till den **ResourceManagerUrl** värde som används i steg två.
  Ange `<subscriptionID>` till den **SubscriptionID** värdet sparades steg tre.
  För att skapa token, se autentisering nedan.  

  7. Anropa API-metoder med hjälp av klienten som du skapade i föregående steg. Till exempel vill skapa ett virtuellt nätverk med hjälp av våra klient från föregående steg: 
  
````go
package main

import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network" 
func main() { 
  vnetC1ient := network.NewVirtualNetworksClientWithBaseURI("<baseURI>", "(subscriptionID>")
  vnetClient .Authorizer = autorest.NewBearerAuthorizer(token)

  vnetClient .CreateOrUpdate( ) 
````
  
  Ett komplett exempel för att skapa ett virtuellt nätverk för Azure Stack med hjälp av Go SDK-profil, se [exempel](#example).

## <a name="authentication"></a>Autentisering

Installera Go-AutoRest-moduler för att få Authorizer-egenskapen från Azure Active Directory med hjälp av Go SDK. Dessa moduler bör har redan installerats med ”Go SDK”-installation. Om inte, installerar den [autentiseringspaket på GitHub](https://github.com/Azure/go-autorest/tree/master/autorest/adal).

Authorizer måste anges som authorizer för resurs-klienten. Det finns olika metoder för att få en Authorizer; en fullständig lista finns här.

Det här avsnittet presenteras ett vanligt sätt att hämta authorizer-token i Azure Stack med klientens autentiseringsuppgifter:

  1. Om ett huvudnamn för tjänsten med ägarrollen för prenumerationen är tillgänglig kan du hoppa över det här steget. Annars skapar ett huvudnamn för tjänsten [instruktioner]( https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals) och tilldela den en ”ägare”-roll som är begränsade till din prenumeration [instruktioner]( https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#assign-role-to-service-principal). Spara service principal program-ID och hemlighet. 

  2. Importera **adal** paket från Go-AutoRest i din kod. 
  
  ````go
  package main
  import "github.com/Azure/go-autorest/autorest/adal" 
  ````

  3. Skapa en oauthConfig med NewOAuthConfig metod från **adal** modulen. 
  
  ````go
  package main 

  import "github.com/Azure/go-autorest/autorest/ada1" 

  func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
  ````
   
  Ange `<activeDirectoryEndpoint>` till värdet för ”loginEndpoint” egenskap från ResourceManagerUrl metadata som hämtades i föregående avsnitt i det här dokumentet.
  Ange `<tenantID>` värde till din Azure Stack-klient-ID. 

  4. Skapa slutligen en service principal-token med metoden NewServicePrincipalToken från adal-modulen. 

  ````go
  package main 

  import "github.com/Azure/go-autorest/autorest/adal" 

  func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
      token, err = adal.NewServicePrincipalToken(
          *oauthConfig,
          clientID,
          clientSecret,
          activeDirectoryResourceID)
      return token, err
  ````
  
  Ange `<activeDirectoryResourceID>` till något av värdena i ”målgruppen” hämta listan från ResourceManagerUrl metadata på den första delen av det här dokumentet.  
  Ange `<clientID>` till tjänstens huvudnamn programmet ID sparas när tjänstens huvudnamn har skapats i föregående avsnitt i det här dokumentet.  
  Ange `<clientSecret>` hemlighet till tjänstens huvudnamn programmet sparas när tjänstens huvudnamn har skapats i föregående avsnitt i det här dokumentet.  

## <a name="example"></a>Exempel

Det här avsnittet visas ett exempel på Go-kod för att skapa virtuellt nätverk i Azure Stack. Mer komplett exempel på Go SDK finns i [Go SDk för Azure-lagringsplats med exempel](https://github.com/Azure-Samples/azure-sdk-for-go-samples). Azure Stack-exempel finns under hybrid / sökväg inuti tjänstemapparna av databasen.

> [!Note]  
> Om du vill köra koden i det här exemplet kontrollerar du att den prenumeration som används har **nätverk** resursprovidern som listas som **registrerad**. Verifiera det se ut för prenumerationen på Azure Stack-portalen och klicka på **resursprovidrar.**

1. Importera paket som krävs i din kod. Importera nätverksmodulen bör du använda den senaste tillgängliga profilen på Azure Stack. 
  
  ````go
  package main

  import (
      "context"
      "fmt"
      "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"
      "github.com/Azure/go-autorest/autorest"
      "github.com/Azure/go-autorest/autorest/adal"
      "github.com/Azure/go-autorest/autorest/to"
  )
  ````

2. Definiera miljövariabler för din. Du måste ha en resursgrupp för att skapa ett virtuellt nätverk. 

  ````go
  var (
      activeDirectoryEndpoint = "yourLoginEndpointFromResourceManagerUrlMetadata"
      tenantID = "yourAzureStackTenantID"
      clientID = "yourServicePrincipalApplicationID"
      clientSecret = "yourServicePrincipalSecret"
      activeDirectoryResourceID = "yourAudienceFromResourceManagerUrlMetadata"
      subscriptionID = "yourSubscriptionID"
      baseURI = "yourResourceManagerURL"
      resourceGroupName = "existingResourceGroupName"
  )
  ````

3. Nu när du har definierat dina miljövariabler, lägger du till en metod för att skapa token för autentisering med hjälp av **adal** paketet. Se information om autentisering i föregående avsnitt.
  
  ````go
  //CreateToken creates a service principal token
  func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
      token, err = adal.NewServicePrincipalToken(
          *oauthConfig,
          clientID,
          clientSecret,
          activeDirectoryResourceID)
      return token, err
  }
  ````

4. Lägg till main-metoden. Main-metoden får först en token genom att använda den metod som har definierats i föregående steg. Sedan skapas en klient med hjälp av modulen Utjämning från profilen. Dessutom skapas ett virtuellt nätverk. 
  
````go
package main

import (
    "context"
    "fmt"
    "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"
    "github.com/Azure/go-autorest/autorest"
    "github.com/Azure/go-autorest/autorest/adal"
    "github.com/Azure/go-autorest/autorest/to"
)

var (
    activeDirectoryEndpoint = "yourLoginEndpointFromResourceManagerUrlMetadata"
    tenantID = "yourAzureStackTenantID"
    clientID = "yourServicePrincipalApplicationID"
    clientSecret = "yourServicePrincipalSecret"
    activeDirectoryResourceID = "yourAudienceFromResourceManagerUrlMetadata"
    subscriptionID = "yourSubscriptionID"
    baseURI = "yourResourceManagerURL"
    resourceGroupName = "existingResourceGroupName"
)

//CreateToken creates a service principal token
func CreateToken() (adal.OAuthTokenProvider, error) {
    var token adal.OAuthTokenProvider
    oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
    token, err = adal.NewServicePrincipalToken(
        *oauthConfig,
        clientID,
        clientSecret,
        activeDirectoryResourceID)
    return token, err
}

func main() {
    token, _ := CreateToken()
    vnetClient := network.NewVirtualNetworksClientWithBaseURI(baseURI, subscriptionID)
    vnetClient.Authorizer = autorest.NewBearerAuthorizer(token)
    future, _ := vnetClient.CreateOrUpdate(
        context.Background(),
        resourceGroupName,
        "sampleVnetName",
        network.VirtualNetwork{
            Location: to.StringPtr("local"),
            VirtualNetworkPropertiesFormat: &network.VirtualNetworkPropertiesFormat{
                AddressSpace: &network.AddressSpace{
                    AddressPrefixes: &[]string{"10.0.0.0/8"},
                },
                Subnets: &[]network.Subnet{
                    {
                        Name: to.StringPtr("subnetName"),
                        SubnetPropertiesFormat: &network.SubnetPropertiesFormat{
                            AddressPrefix: to.StringPtr("10.0.0.0/16"),
                        },
                    },
                },
            },
        })
    err := future.WaitForCompletion(context.Background(), vnetClient.Client)
    if err != nil {
        fmt.Printf(err.Error())
        return
    }
}

````

## <a name="next-steps"></a>Nästa steg
* [Installera PowerShell för Azure Stack](azure-stack-powershell-install.md)
* [Konfigurera PowerShell-miljö för Azure Stack-användare](azure-stack-powershell-configure-user.md)  
