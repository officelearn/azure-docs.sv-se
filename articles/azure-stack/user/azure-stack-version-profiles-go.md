---
title: Med hjälp av API-version profiler med gå i Azure-stacken | Microsoft Docs
description: Lär dig mer om att använda profiler för API-version med gå i Azure-stacken.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/30/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.openlocfilehash: 84ca616856f363e4d3d68ab1cc45b97f7c589185
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/01/2018
---
# <a name="use-api-version-profiles-with-go-in-azure-stack"></a>Använda profiler för API-version med gå i Azure-stacken

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

## <a name="go-and-version-profiles"></a>Gå och version profiler

En profil är en kombination av olika resurstyper med olika versioner från olika tjänster. Använder en profil kan du blanda och matcha mellan olika resurstyper. Profiler kan ange:

 - Stabiliteten för ditt program genom att låsa till särskilda API-versioner.
 - Kompatibilitet för ditt program med Azure-stacken och regionala Azure-datacenter.

I SDK gå profiler är tillgängliga under profilerna / sökväg med deras version i den **åååå-MM-DD** format. Just nu den senaste Azure stacken profilversionen är **2017-03-09**. Om du vill importera en viss tjänst från en profil måste du importera den motsvarande modulen från profilen. Till exempel för att importera **Compute** från **2017-03-09** profil:

````go
import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/compute/mgmt/compute" 
````

## <a name="install-azure-sdk-for-go"></a>Installera Azure SDK för gå

  1. Installera Git. Instruktioner finns i [komma igång - installerar Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
  2. Installera den [gå programmeringsspråk](https://golang.org/dl).  
  API-profiler för Azure kräver gå version 1,9 eller nyare.
  3. Installera Azure SDK gå och dess beroenden genom att köra följande kommando för bash:
  ```
    go get -u -d github.com/Azure/azure-sdk-for-go/...
  ```

### <a name="the-go-sdk"></a>Gå SDK

Du hittar mer information om Azure gå SDK på:
- Azure gå SDK på [installera Azure SDK för Gå](https://docs.microsoft.com/go/azure/azure-sdk-go-install).
- Azure gå SDK är allmänt tillgänglig på GitHub på [azure sdk för Gå](https://github.com/Azure/azure-sdk-for-go).

### <a name="go-autorest-dependencies"></a>Gå AutoRest beroenden

SDK gå beror på Azure gå-AutoRest-moduler att skicka REST-begäranden till Azure Resource Manager-slutpunkter. Du måste importera Azure gå-AutoRest modulen beroenden från [Azure gå-AutoRest på GitHub](https://github.com/Azure/go-autorest). Du kan hitta installera bash kommandon i den **installera** avsnitt.

## <a name="how-to-use-go-sdk-profiles-on-azure-stack"></a>Hur du använder gå SDK profiler på Azure-stacken

Att köra ett exempel gå kod på Azure Stack:
  1. Installera Azure SDK för gå och dess beroenden. Anvisningar finns i föregående avsnitt, [installera Azure SDK för Gå](#install-azure-sdk-for-go).
  2. Hämta metadata-information från Resource Manager-slutpunkten. Slutpunkten returnerar en JSON-fil med den information som krävs för att köra koden gå.
  > [!note]  
  > Den **ResourceManagerUrl** i Azure Stack Development Kit (ASDK) är: `https://management.local.azurestack.external/`  
  > Den **ResourceManagerUrl** i integrerade system är: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/`  
  > Att hämta de metadata som krävs: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`
  
  JSON-exempelfil:

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

  3. Om det inte finns kan du skapa en prenumeration och spara prenumerations-ID som ska användas senare. Information om hur du skapar en prenumeration finns [skapa prenumerationer erbjudanden i Azure-stacken](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm). 
  4. Skapa ett huvudnamn för tjänsten med ”prenumeration” scope och **ägare** roll. Spara tjänstens huvudnamn ID och hemlighet. Information om hur du skapar ett huvudnamn för tjänsten för Azure-Stack finns [skapa tjänstens huvudnamn](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#create-service-principal-for-azure-ad). Din Azure Stack-miljö har ställts in.
  5. Importera en modul för tjänsten från gå SDK-profil i din kod. Den aktuella versionen av Azure-stacken profil är **2017-03-09**. Till exempel för att importera modulen Utjämning från **2017-03-09** Profiltyp: 

  ````go
    package main 
    import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"
  ````
  
  6. Skapa i din funktion och autentisera en klient med en **ny** klienten funktionsanrop. Du kan använda följande kod för att skapa ett virtuellt nätverk-klienten:  

  ````go
  package main 

  import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network" 

  func main() { 
      vnetClient := network.NewVirtualNetworksClientWithBaseURI("<baseURI>", "(subscriptionID>")
      vnetClient.Authorizer = autorest.NewBearerAuthorizer(token)
  ````

  Ange `<baseURI>` till den **ResourceManagerUrl** värde som används i steg två.
  Ange `<subscriptionID>` till den **SubscriptionID** värdet som sparats i steg tre.
  Om du vill skapa token finns autentisering nedan.  

  7. Anropa API-metoder med klienten som du skapade i föregående steg. Till exempel skapa ett virtuellt nätverk med hjälp av vår klienten från föregående steg: 
  
````go
package main

import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network" 
func main() { 
  vnetC1ient := network.NewVirtualNetworksClientWithBaseURI("<baseURI>", "(subscriptionID>")
  vnetClient .Authorizer = autorest.NewBearerAuthorizer(token)

  vnetClient .CreateOrUpdate( ) 
````
  
  En fullständig exempel för att skapa ett virtuellt nätverk på Azure-stacken genom att använda gå SDK-profilen finns [exempel](#example).

## <a name="authentication"></a>Autentisering

Installera gå AutoRest-moduler för att hämta egenskapen Authorizer från Azure Active Directory med hjälp av gå SDK. Dessa moduler bör har redan installerats med ”gå SDK”-installation. Om inte, installerar du den [autentiseringspaket på GitHub](https://github.com/Azure/go-autorest/tree/master/autorest/adal).

Authorizer måste anges som authorizer för resurs-klienten. Det finns olika metoder för att hämta en Authorizer; en fullständig lista finns här.

Det här avsnittet presenteras ett vanligt sätt att hämta authorizer token på Azure-stacken genom att använda klientens autentiseringsuppgifter:

  1. Om det finns ett huvudnamn för tjänsten med ägarrollen på prenumerationen kan du hoppa över detta steg. Annars skapar ett huvudnamn för tjänsten [instruktioner]( https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals) och tilldela den en ”ägare”-roll som är begränsade till din prenumeration [instruktioner]( https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#assign-role-to-service-principal). Spara tjänstens huvudnamn program-ID och hemlighet. 

  2. Importera **adal** paket från gå AutoRest i koden. 
  
  ````go
  package main
  import "github.com/Azure/go-autorest/autorest/adal" 
  ````

  3. Skapa en oauthConfig med metoden NewOAuthConfig från **adal** modul. 
  
  ````go
  package main 

  import "github.com/Azure/go-autorest/autorest/ada1" 

  func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
  ````
   
  Ange `<activeDirectoryEndpoint>` till värdet för ”loginEndpoint” egenskap från ResourceManagerUrl metadata hämtades i föregående avsnitt i det här dokumentet.
  Ange `<tenantID>` värde till din Azure-stacken klient-ID. 

  4. Skapa slutligen en service principal token med metoden NewServicePrincipalToken från adal-modulen. 

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
  
  Ange `<activeDirectoryResourceID>` till något av värdena i ”målgruppen” hämta listan från ResourceManagerUrl metadata i föregående avsnitt i det här dokumentet.  
  Ange `<clientID>` till UPN-tjänstprogrammet ID sparas när tjänstens huvudnamn skapades i föregående avsnitt i det här dokumentet.  
  Ange `<clientSecret>` till UPN-tjänstprogrammet hemlighet sparas när tjänstens huvudnamn skapades i föregående avsnitt i det här dokumentet.  

## <a name="example"></a>Exempel

Det här avsnittet visas ett exempel gå kod för att skapa virtuella nätverk på Azure-stacken. Mer komplett exempel på Gå SDK finns [Azure gå SDk-exempel databasen](https://github.com/Azure-Samples/azure-sdk-for-go-samples). Azure Stack-exempel finns under hybrid / sökväg inuti tjänstemapparna av databasen.
> [!note]  
> Om du vill köra koden i det här exemplet, kontrollera att prenumerationen används **nätverk** resursprovidern som **registrerade**. För att verifiera den ser ut för prenumerationen på Azure Stack-portalen och klicka på **resursleverantörer.**

1. Importera nödvändiga paketen i koden. Du bör använda den senaste profilen på Azure-stacken för att importera nätverksmodulen. 
  
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

2. Definiera din miljövariabler. Observera att om du vill skapa ett virtuellt nätverk måste ha en resursgrupp. 

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

3. Nu när du har definierat din miljövariabler, lägger du till en metod för att skapa token för autentisering med hjälp av **adal** paketet. Se information om autentisering i föregående avsnitt.
  
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

4. Lägg till main-metoden. Main-metoden får först en token genom att använda den metod som har definierats i föregående steg. Sedan skapar den en klient med hjälp av nätverket modul från profilen. Slutligen skapas ett virtuellt nätverk. 
  
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
* [Konfigurera Azure Stack användarens PowerShell-miljö](azure-stack-powershell-configure-user.md)  
