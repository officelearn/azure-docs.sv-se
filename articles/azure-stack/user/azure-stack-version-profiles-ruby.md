---
title: Med hjälp av API-version profiler med Ruby i Azure-stacken | Microsoft Docs
description: Lär dig mer om att använda profiler för API-version med Ruby i Azure-stacken.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: B82E4979-FB78-4522-B9A1-84222D4F854B
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.openlocfilehash: dd8130ac12f9c7c2095f9329dc4ce8a34187cf62
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
ms.locfileid: "34011228"
---
# <a name="use-api-version-profiles-with-ruby-in-azure-stack"></a>Använda profiler för API-version med Ruby i Azure-stacken

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

## <a name="ruby-and-api-version-profiles"></a>Profiler för Ruby och API-version

Ruby-SDK för Azure Stack Resource Manager innehåller verktyg som hjälper dig att skapa och hantera infrastrukturen. Resursproviders i SDK innehåller beräkning, virtuella nätverk och lagring med Ruby språk. API-profiler i SDK för Ruby aktivera hybrid cloud utveckling genom att hjälpa dig att växla mellan globala Azure-resurser och resurser på Azure-stacken.

En API-profil är en kombination av resursleverantörer och service-versioner. Du kan använda en API-profil för att kombinera olika resurstyper.

 - För att använda de senaste versionerna av alla tjänster som använder den **senaste** profil av Azure SDK samlad symbolen.
 - Om du vill använda tjänsterna som är kompatibla med Azure-stacken, den **V2017_03_09** profil av Azure SDK samlad symbolen.
 - Om du vill använda den senaste api-versionen av en tjänst i **senaste** profilen för specifika symbolen. Till exempel om du vill använda den senaste api-versionen av beräkning service enbart använda de **senaste** profilen för den **Compute** symbolen.
 - Om du vill använda specifika api-versionen för en tjänst använder du specifika API-versioner som definierats i symbolen.

> [!Note]   
> Du kan kombinera alla alternativ i samma program.

## <a name="install-the-azure-ruby-sdk"></a>Installera Azure SDK för Ruby

 - Följ officiella instruktionerna för att installera [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
 - Följ officiella instruktionerna för att installera [Ruby](https://www.ruby-lang.org/en/documentation/installation/).
    - När du installerar väljer **lägga till Ruby PATH-variabeln**
    - Installera Dev kit under Ruby installationen när du tillfrågas.
    - Installera bundler med följande kommando:  
      `Gem install bundler`
 - Om det inte finns kan du skapa en prenumeration och spara prenumerations-ID som ska användas senare. Instruktioner för att skapa en prenumeration är [här](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm). 
 - Skapa ett huvudnamn för tjänsten och spara dess ID och hemlighet. Instruktioner för att skapa ett huvudnamn för tjänsten för Azure-stacken är [här](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals). 
 - Kontrollera att din tjänstens huvudnamn har deltagare/ägarrollen på din prenumeration. Instruktioner om hur du tilldelar rollen till tjänstens huvudnamn är [här](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals).

## <a name="install-the-rubygem-packages"></a>Installera rubygem-paket

Du kan installera azure rubygem paket direkt.

````Ruby  
gem install azure_mgmt_compute
gem install azure_mgmt_storage
gem install azure_mgmt_resources
gem install azure_mgmt_network
Or use them in your Gemfile.
gem 'azure_mgmt_storage'
gem 'azure_mgmt_compute'
gem 'azure_mgmt_resources'
gem 'azure_mgmt_network'
````

Tänk på Azure Resource Manager Ruby SDK är en förhandsversion och kommer troligen har bryta gränssnittet ändras i kommande versioner. Många i delversion kan indikera viktiga förändringar.

## <a name="usage-of-the-azuresdk-gem"></a>Användning av azure_sdk symbolen

Symbolen azure_sdk, är en sammanfattning av alla stöds gems i Ruby-SDK. Den här symbolen består av en **senaste** profil som har stöd för den senaste versionen av alla tjänster. Det inför en profil för en ny version **V2017_03_09** profil som är utformat för Azure-stacken.

Du kan installera azure_sdk samlad symbolen med följande kommando:  

````Ruby  
  gem install 'azure_sdk
````

## <a name="prerequisite"></a>Krav

För att kunna använda Azure SDK för Ruby med Azure-stacken, måste du ange följande värden och ange sedan värden med miljövariabler. Se anvisningarna efter tabellen för operativsystemet på inställningen i miljövariabler. 

| Värde | Miljövariabler | Beskrivning | 
| --- | --- | --- | --- |
| Klient-ID:t | AZURE_TENANT_ID | Värdet för Azure-stacken [klient-ID](https://docs.microsoft.com/azure/azure-stack/azure-stack-identity-overview). |
| Klient-ID | AZURE_CLIENT_ID | Tjänsten huvudnamn program-ID sparas när tjänstens huvudnamn skapades i föregående avsnitt i det här dokumentet.  |
| Prenumerations-ID:t | AZURE_SUBSCRIPTION_ID | Den [prenumerations-ID](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview#subscriptions) är hur du kommer åt erbjudanden i Azure-stacken. |
| Klienthemlighet | AZURE_CLIENT_SECRET | Huvudnamn tjänstprogrammet hemlighet sparas när tjänstens huvudnamn skapades. |
| Resource Manager-slutpunkt | ARM_ENDPOINT | Se [i Azure-stacken resource manager endpoin](#The-azure-stack-resource-manager-endpoint).  |

### <a name="the-azure-stack-resource-manager-endpoint"></a>Stacken för Azure resource manager-slutpunkt

Microsoft Azure Resource Manager är ett ramverk för hantering som gör att administratörer kan distribuera, hantera och övervaka Azure-resurser. Azure Resource Manager kan hantera dessa uppgifter som en grupp i stället för individuellt, i en enda åtgärd.

Du kan hämta metadata-information från Resource Manager-slutpunkten. Slutpunkten returnerar en JSON-fil med den information som krävs för att köra din kod.

  > [!Note]  
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

### <a name="set-environmental-variables"></a>Uppsättning miljövariabler

**Microsoft Windows**  
Om du vill ange miljövariabler, i Windows kommandotolk, använder du följande format:  
`set AZURE_TENANT_ID=<YOUR_TENANT_ID>`

**macOS, Linux och Unix-baserade system**  
För Unix-baserade datorer, kan du använda kommandot som:  
`export AZURE_TENANT_ID=<YOUR_TENANT_ID>`

## <a name="existing-api-profiles"></a>Befintliga API-profiler

Azure_sdk samlad symbolen har följande två profiler:

1. **V2017_03_09**  
  Profil som skapats för Azure-stacken. Använd den här profilen för tjänster för att vara mest kompatibel med Azure-stacken.
2. **senaste**  
  Profil består av senaste versionerna av alla tjänster. Använd de senaste versionerna av alla tjänster.

Mer information om Azure-stacken och API-profiler finns en [sammanfattning av API-profiler](azure-stack-version-profiles.md#summary-of-api-profiles).

## <a name="azure-ruby-sdk-api-profile-usage"></a>Användning av Azure Ruby SDK API-profil

Följande rader ska användas för att skapa en instans av en profil-klient. Den här parametern är endast krävs för Azure Stack eller andra privata moln. Globala Azure har redan inställningarna som standard.

````Ruby  
active_directory_settings = get_active_directory_settings(ENV['ARM_ENDPOINT'])

provider = MsRestAzure::ApplicationTokenProvider.new(
    ENV['AZURE_TENANT_ID'],
    ENV['AZURE_CLIENT_ID'],
    ENV['AZURE_CLIENT_SECRET'],
    active_directory_settings
)
credentials = MsRest::TokenCredentials.new(provider)
options = {
    credentials: credentials,
    subscription_id: subscription_id,
    active_directory_settings: active_directory_settings,
    base_url: ENV['ARM_ENDPOINT']
}

# Target profile built for Azure Stack
client = Azure::Resources::Profiles::V2017_03_09::Mgmt::Client.new(options)
````

Profil-klienten kan användas för att få åtkomst till en enskild resursleverantörer, till exempel bearbetning, lagring och nätverk.

````Ruby  
# To access the operations associated with Compute
profile_client.compute.virtual_machines.get 'RESOURCE_GROUP_NAME', 'VIRTUAL_MACHINE_NAME'

# Option 1: To access the models associated with Compute
purchase_plan_obj = profile_client.compute.model_classes.purchase_plan.new

# Option 2: To access the models associated with Compute
# Notice Namespace: Azure::Profiles::<Profile Name>::<Service Name>::Mgmt::Models::<Model Name>
purchase_plan_obj = Azure::Profiles::V2017_03_09::Compute::Mgmt::Models::PurchasePlan.new
````

## <a name="define-azurestack-environment-setting-functions"></a>Definiera AzureStack miljö inställningen funktioner

Om du vill autentisera tjänstens huvudnamn i Azure Stack-miljön, definiera slutpunkter med hjälp av **get_active_directory_settings()**. Den här metoden använder den **ARM_Endpoint** miljövariabel som du ställer in när din miljövariabler.

````Ruby  
# Get Authentication endpoints using Arm Metadata Endpoints
def get_active_directory_settings(armEndpoint)
    settings = MsRestAzure::ActiveDirectoryServiceSettings.new
    response = Net::HTTP.get_response(URI("#{armEndpoint}/metadata/endpoints?api-version=1.0"))
    status_code = response.code
    response_content = response.body
    unless status_code == "200"
        error_model = JSON.load(response_content)
        fail MsRestAzure::AzureOperationError.new("Getting Azure Stack Metadata Endpoints", response, error_model)
    end
    result = JSON.load(response_content)
    settings.authentication_endpoint = result['authentication']['loginEndpoint'] unless result['authentication']['loginEndpoint'].nil?
    settings.token_audience = result['authentication']['audiences'][0] unless result['authentication']['audiences'][0].nil?
    settings
end
````

## <a name="samples-using-api-profiles"></a>Exempel som använder API-profiler

Du kan använda i följande exempel finns i GitHub repositoreis som referens skapa lösningar med Ruby och Azure-stacken API-profiler:

 - [Hantera Azure-resurser och -resursgrupper med Ruby](https://github.com/Azure-Samples/resource-manager-ruby-resources-and-groups/tree/master/Hybrid)
 - [Hantera virtuella datorer med Ruby](https://github.com/Azure-Samples/compute-ruby-manage-vm/tree/master/Hybrid)
 - [Distribuera en SSH aktiverad virtuell dator med en mall i Ruby](https://github.com/Azure-Samples/resource-manager-ruby-template-deployment/tree/master/Hybrid)

### <a name="sample-resource-manager-and-groups"></a>Exempel Resource Manager och grupper

Se till att du har installerat Ruby om du vill köra exemplet. Om du använder Visual Studio Code kan hämta SDK för Ruby som ett tillägg samt. 

> [!Note]  
> Du kan hämta databasen för exempel på ”[hantera Azure-resurser och resursgrupper med Ruby](https://github.com/Azure-Samples/resource-manager-ruby-resources-and-groups/tree/master/Hybrid)”.

1. Klona lagringsplatsen.

    ````Bash
    git clone https://github.com/Azure-Samples/resource-manager-ruby-resources-and-groups.git
    ````

2. Installera beroenden med hjälp av paket.

    ````Bash
    cd resource-manager-ruby-resources-and-groups\Hybrid\
    bundle install
    ````

3. Skapa ett Azure tjänstens huvudnamn med hjälp av PowerShell och hämta de värden som behövs. 

  Anvisningar om hur du skapar ett huvudnamn för tjänsten finns [Använd Azure PowerShell för att skapa ett huvudnamn för tjänsten med ett certifikat](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals).

  Värden som behövs är:
  - Klient-ID:t
  - Klient-ID
  - Klienthemlighet
  - Prenumerations-ID:t
  - Resource Manager-slutpunkt

  Ange följande miljövariablerna med hjälp av information som du hämtade från tjänstens huvudnamn du skapat.

  - Exportera AZURE_TENANT_ID = {din klient-id}
  - Exportera AZURE_CLIENT_ID = {klient-id}
  - Exportera AZURE_CLIENT_SECRET = {klienthemlighet}
  - Exportera AZURE_SUBSCRIPTION_ID = {ditt prenumerations-id}
  - Exportera ARM_ENDPOINT = {AzureStack Resource manager URL: en}

  > [!Note]  
  > I Windows, Använd i stället för export.

4. Kontrollera plats-variabeln anges till AzureStack-plats. Till exempel lokala = ”lokalt”

5. Lägg till i följande rad med kod om du använder Azure Stack eller andra privata moln som mål rätt active directory-slutpunkter.

  ````Ruby  
  active_directory_settings = get_active_directory_settings(ENV['ARM_ENDPOINT'])
  ````

6. Lägga till inställningarna för active directory och den grundläggande Webbadressen för att arbeta med Azure-stacken i variabeln alternativ. 

  ````Ruby  
  options = {
    credentials: credentials,
    subscription_id: subscription_id,
    active_directory_settings: active_directory_settings,
    base_url: ENV['ARM_ENDPOINT']
  }
  ````

7. Skapa profil klienten som riktas mot Azure Stack-profil:

  ````Ruby  
    client = Azure::Resources::Profiles::V2017_03_09::Mgmt::Client.new(options)
  ````

8. För att autentisera tjänstens huvudnamn med Azure Stack slutpunkterna ska definieras med **get_active_directory_settings()**. Den här metoden använder den **ARM_Endpoint** miljövariabel som du ställer in när din miljövariabler.

  ````Ruby  
  def get_active_directory_settings(armEndpoint)
    settings = MsRestAzure::ActiveDirectoryServiceSettings.new
    response = Net::HTTP.get_response(URI("#{armEndpoint}/metadata/endpoints?api-version=1.0"))
    status_code = response.code
    response_content = response.body
    unless status_code == "200"
      error_model = JSON.load(response_content)
      fail MsRestAzure::AzureOperationError.new("Getting Azure Stack Metadata Endpoints", response, error_model)
    end
    result = JSON.load(response_content)
    settings.authentication_endpoint = result['authentication']['loginEndpoint'] unless result['authentication']['loginEndpoint'].nil?
    settings.token_audience = result['authentication']['audiences'][0] unless result['authentication']['audiences'][0].nil?
    settings
  end
  ````

9. Kör exemplet.

  ````Ruby
    bundle exec ruby example.rb
  ````

## 

## <a name="next-steps"></a>Nästa steg

* [Installera PowerShell för Azure Stack](azure-stack-powershell-install.md)
* [Konfigurera Azure Stack användarens PowerShell-miljö](azure-stack-powershell-configure-user.md)  
