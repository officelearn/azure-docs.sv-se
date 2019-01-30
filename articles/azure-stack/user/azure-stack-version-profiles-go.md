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
ms.date: 01/19/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 01/19/2019
ms.openlocfilehash: 9b66a7a176862fce687b7cc0b1ff3c14bda118d7
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55243897"
---
# <a name="use-api-version-profiles-with-go-in-azure-stack"></a>Använd API-versionsprofiler med Go i Azure Stack

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

## <a name="go-and-version-profiles"></a>Profiler för go och version

En profil är en kombination av olika resurstyper med olika versioner från olika tjänster. Med hjälp av en profil kan du blanda och matcha mellan olika resurstyper. Profiler kan ge följande fördelar:

- Stabiliteten för ditt program genom att låsa till specifika API-versioner.
- Kompatibilitet för ditt program med Azure Stack och regionala Azure-datacenter.

I Go-SDK profiler är tillgängliga under den profiler-sökvägen, med versionerna i den **åååå-MM-DD** format. Just nu är den senaste versionen av Azure Stack-API-profilen är **2017-03-09**. Importera den motsvarande modulen från profilen om du vill importera en viss tjänst från en profil. Till exempel för att importera **Compute** tjänsten från **2017-03-09** profil, Använd följande kod:

```go
import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/compute/mgmt/compute"
```

## <a name="install-azure-sdk-for-go"></a>Installera Azure SDK för Go

1. Installera Git. Anvisningar finns i [komma igång - installerar Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
2. Installera den [programmering språket Go](https://golang.org/dl). API-profiler för Azure kräver Go version 1.9 eller senare.
3. Installera Go Azure SDK och dess beroenden genom att köra följande bash-kommando:

   ```bash
   go get -u -d github.com/Azure/azure-sdk-for-go/...
   ```

### <a name="the-go-sdk"></a>The Go SDK

Du hittar mer information om SDK för Azure går du till följande länkar:

- Azure går SDK på [installera Azure SDK för Go](/go/azure/azure-sdk-go-install).
- Azure Go SDK finns på GitHub i den [azure sdk för go](https://github.com/Azure/azure-sdk-for-go) lagringsplatsen.

### <a name="go-autorest-dependencies"></a>Go-AutoRest-beroenden

Go SDK är beroende av Azure **Go-AutoRest** moduler för att skicka REST-begäranden till Azure Resource Manager-slutpunkter. Du måste importera Azure **Go-AutoRest** modulberoenden från [Azure Go-AutoRest på GitHub](https://github.com/Azure/go-autorest). Du kan hitta install bash-kommandon i den **installera** avsnittet.

## <a name="how-to-use-go-sdk-profiles-on-azure-stack"></a>Använd profiler för Go SDK på Azure Stack

Följ dessa steg om du vill köra ett exempel på Go-koden på Azure Stack:

1. Installera Azure SDK för Go och dess beroenden. Anvisningar finns i avsnittet ovan [installera Azure SDK för Go](#install-azure-sdk-for-go).
2. Hämta metadata-information från Resource Manager-slutpunkten. Slutpunkten som returnerar en JSON-fil med den information som krävs för att köra Go-kod.

   > [!NOTE]  
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

3. Om det inte finns skapar du en prenumeration och spara prenumerations-ID som ska användas senare. Information om hur du skapar en prenumeration finns i [skapa prenumerationer för erbjudanden i Azure Stack](../azure-stack-subscribe-plan-provision-vm.md).

4. Skapa ett tjänstobjekt med **prenumeration** omfång och **ägare** roll. Spara ID för tjänstens huvudnamn och hemlighet. Information om hur du skapar ett huvudnamn för tjänsten för Azure Stack finns i [skapa tjänstens huvudnamn](azure-stack-create-service-principals.md#create-service-principal-for-azure-ad). Azure Stack-miljön har konfigurerats.

5. Importera en service-modul från Go SDK-profil i din kod. Den aktuella versionen av Azure Stack-profil är **2017-03-09**. Till exempel för att importera modulen Utjämning från **2017-03-09** Profiltyp, Använd följande kod:

   ```go
   package main
    import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"
   ```

6. Skapa i din funktion och autentisera en klient med en **New** klienten funktionsanrop. Du kan använda följande kod för att skapa en vnet-klient:  

   ```go
   package main

   import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"

   func main() {
      vnetClient := network.NewVirtualNetworksClientWithBaseURI("<baseURI>", "(subscriptionID>")
      vnetClient.Authorizer = autorest.NewBearerAuthorizer(token)
   ```

   Ange `<baseURI>` till den **ResourceManagerUrl** värde som används i steg 2. Ange `<subscriptionID>` till den **SubscriptionID** värdet som sparats från steg 3.

   Se följande avsnitt för att skapa en token.  

7. Anropa API-metoder med hjälp av klienten som du skapade i föregående steg. Till exempel för att skapa ett virtuellt nätverk med hjälp av klienten från föregående steg, se följande exempel:

   ```go
   package main

   import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"
   func main() {
   vnetClient := network.NewVirtualNetworksClientWithBaseURI("<baseURI>", "(subscriptionID>")
   vnetClient .Authorizer = autorest.NewBearerAuthorizer(token)

   vnetClient .CreateOrUpdate( )
   ```

Ett komplett exempel för att skapa ett virtuellt nätverk för Azure Stack med hjälp av Go SDK-profil, finns det [exempel](#example).

## <a name="authentication"></a>Autentisering

Att hämta den **Authorizer** egenskap från Azure Active Directory med Go-SDK installerar den **Go-AutoRest** moduler. Dessa moduler bör har redan installerats med ”Go SDK”-installation. Om inte, installerar den [autentiseringspaket från GitHub](https://github.com/Azure/go-autorest/tree/master/autorest/adal).

Authorizer måste anges som authorizer för resurs-klienten. Det finns olika sätt att hämta authorizer-token i Azure Stack med klientens autentiseringsuppgifter:

1. Om ett huvudnamn för tjänsten med ägarrollen för prenumerationen är tillgänglig kan du hoppa över det här steget. Annars skapar en [tjänstens huvudnamn](azure-stack-create-service-principals.md) och tilldela den en ”ägare” roll [begränsade till din prenumeration](azure-stack-create-service-principals.md#assign-the-service-principal-to-a-role). Spara service principal program-ID och hemlighet.

2. Importera den **adal** paket från Go-AutoRest i din kod.

   ```go
   package main
   import "github.com/Azure/go-autorest/autorest/adal"
   ```

3. Skapa en **oauthConfig** med metoden NewOAuthConfig från **adal** modulen.

   ```go
   package main

   import "github.com/Azure/go-autorest/autorest/ada1"

   func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
   }
   ```

   Ange `<activeDirectoryEndpoint>` till värdet för den `loginEndpoint` egenskap från den `ResourceManagerUrl` metadata hämtades i föregående avsnitt i det här dokumentet. Ange den `<tenantID>` värde till din Azure Stack-klient-ID.

4. Skapa slutligen en service principal-token med hjälp av den `NewServicePrincipalToken` metod från den **adal** modulen:

   ```go
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
   ```

    Ange `<activeDirectoryResourceID>` till något av värdena i listan ”målgrupp” från den **ResourceManagerUrl** metadata som du hämtade i föregående avsnitt i den här artikeln.
    Ange `<clientID>` huvudnamn tjänstprogrammet ID sparade när tjänstens huvudnamn skapades i föregående avsnitt i den här artikeln.
    Ange `<clientSecret>` till tjänstens huvudnamn programhemlighet sparas när tjänstens huvudnamn skapades i föregående avsnitt i den här artikeln.

## <a name="example"></a>Exempel

Det här exemplet visar ett exempel på Go-kod som skapar ett virtuellt nätverk i Azure Stack. Komplett exempel på Go SDK finns den [Go SDK för Azure-lagringsplats med exempel](https://github.com/Azure-Samples/azure-sdk-for-go-samples). Azure Stack-exempel finns på den hybrid-sökvägen i tjänsten mappar på lagringsplatsen.

> [!NOTE]  
> Om du vill köra koden i det här exemplet kontrollerar du att den prenumeration som används har de **nätverk** resursprovidern som listas som **registrerad**. Verifiera, leta efter prenumeration i Azure Stack-portalen och välj **resursprovidrar.**

1. Importera de nödvändiga paketen i din kod. Använd den senaste tillgängliga profilen på Azure Stack för att importera nätverksmodulen:

   ```go
   package main

   import (
       "context"
       "fmt"
       "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"
       "github.com/Azure/go-autorest/autorest"
       "github.com/Azure/go-autorest/autorest/adal"
       "github.com/Azure/go-autorest/autorest/to"
   )
   ```

2. Definiera miljövariabler för din. Om du vill skapa ett virtuellt nätverk, måste du ha en resursgrupp.

   ```go
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
   ```

3. Nu när du har definierat dina miljövariabler, lägger du till en metod för att skapa en token för autentisering med hjälp av den **adal** paketet. Mer information om autentisering finns i föregående avsnitt.

   ```go
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
   ```

4. Lägg till den `main` metoden. Den `main` metoden först får en token genom att använda den metod som har definierats i föregående steg. Sedan skapas en klient med hjälp av en modul för nätverk från profilen. Dessutom skapas ett virtuellt nätverk.

   ```go
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
   ```

## <a name="next-steps"></a>Nästa steg

- [Installera PowerShell för Azure Stack](azure-stack-powershell-install.md)
- [Konfigurera PowerShell-miljö för Azure Stack-användare](azure-stack-powershell-configure-user.md)  
