---
title: Med hjälp av API-versionsprofiler med Ruby i Azure Stack | Microsoft Docs
description: Lär dig mer om hur du använder API-versionsprofiler med Ruby i Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: B82E4979-FB78-4522-B9A1-84222D4F854B
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 01/09/2019
ms.openlocfilehash: 73c770f45666ce76b770796ac9714d007c2870cb
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55247195"
---
# <a name="use-api-version-profiles-with-ruby-in-azure-stack"></a>Använd API-versionsprofiler med Ruby i Azure Stack

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

## <a name="ruby-and-api-version-profiles"></a>Ruby- och API-versionsprofiler

Ruby SDK för Azure Stack Resource Manager innehåller verktyg som hjälper dig att skapa och hantera infrastrukturen. Resursprovidrar i SDK innehåller beräkning, virtuella nätverk och lagring med Ruby-språket. API-profiler i Ruby SDK aktivera hybrid molnutveckling genom att hjälpa dig att växla mellan globala Azure-resurser och resurser på Azure Stack.

En API-profil är en kombination av resursprovidrar och tjänstversioner. Du kan använda en API-profil för att kombinera olika resurstyper.

- Om du vill använda de senaste versionerna av alla tjänster, använda den **senaste** profilen för Azure SDK samlad gem.
- Om du vill använda tjänsterna som är kompatibla med Azure Stack, använda den **V2018_03_01** profilen för Azure SDK samlad gem.
- Du använder senast **api-versionen** av en tjänst som använder den **senaste** profilen för den specifika gem. Exempel: Om du vill använda senast **api-versionen** av beräkningstjänst enbart använda de **senaste** profilen för den **Compute** gem.
- Du använder en viss **api-versionen** för en tjänst, använder du de specifika API-versioner som definierats i symbolen.

> [!NOTE]
> Du kan kombinera alla alternativ i samma program.

## <a name="install-the-azure-ruby-sdk"></a>Installera Azure SDK för Ruby

- Följ officiella anvisningarna för att installera [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
- Följ officiella anvisningarna för att installera [Ruby](https://www.ruby-lang.org/en/documentation/installation/).
  - Under installationen, Välj **lägga till Ruby PATH-variabeln**.
  - Installera i development kit under Ruby-installationen när du tillfrågas.
  - Installera bundler med följande kommando:  
    `Gem install bundler`
- Om det inte finns skapar du en prenumeration och spara prenumerations-ID som ska användas senare. Instruktioner för att skapa en prenumeration är [här](../azure-stack-subscribe-plan-provision-vm.md).
- Skapa ett huvudnamn för tjänsten och spara dess ID och hemlighet. Instruktioner för att skapa ett huvudnamn för tjänsten för Azure Stack är [här](../azure-stack-create-service-principals.md).
- Kontrollera att din tjänstens huvudnamn har rollen deltagare/ägare av prenumerationen. Instruktioner om hur du tilldelar roll till tjänstens huvudnamn är [här](../azure-stack-create-service-principals.md).

## <a name="install-the-rubygem-packages"></a>Installera Rubygem-paket

Du kan installera Azure Rubygem-paket direkt.

```Ruby  
gem install azure_mgmt_compute
gem install azure_mgmt_storage
gem install azure_mgmt_resources
gem install azure_mgmt_network
Or use them in your Gemfile.
gem 'azure_mgmt_storage'
gem 'azure_mgmt_compute'
gem 'azure_mgmt_resources'
gem 'azure_mgmt_network'
```

Azure Resource Manager Ruby SDK är en förhandsversion och kommer troligen att ha större ändringar i användargränssnittet i kommande versioner. Fler och fler i den lägre versionen kan tyda på större ändringar.

## <a name="use-the-azuresdk-gem"></a>Använd azure_sdk gem

Symbolen, **azure_sdk**, är en sammanslagning av alla stöds gems i Ruby SDK. Den här symbolen består av en **senaste** -profil som har stöd för den senaste versionen av alla tjänster. Det introducerar två versioner profil **V2017_03_09** och **V2018_03_01** profiler som är byggda med Azure Stack.

Du kan installera azure_sdk samlad symbolen med följande kommando:  

```Ruby  
gem install 'azure_sdk'
```

## <a name="prerequisites"></a>Förutsättningar

För att kunna använda Azure-SDK för Ruby med Azure Stack, måste du ange följande värden och ange värden med miljövariabler. Se anvisningarna efter tabellen för operativsystemet på ställa in miljövariabler.

| Value | Miljövariabler | Beskrivning |
| --- | --- | --- | --- |
| Klient-ID:t | AZURE_TENANT_ID | Värdet för Azure Stack [klient-ID](https://docs.microsoft.com/azure/azure-stack/azure-stack-identity-overview). |
| Klientorganisations-ID | AZURE_CLIENT_ID | Tjänsten huvudnamn program-ID sparas när tjänstens huvudnamn har skapats i föregående avsnitt i det här dokumentet.  |
| Prenumerations-ID:t | AZURE_SUBSCRIPTION_ID | Den [prenumerations-ID](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview#subscriptions) är hur du kommer åt erbjudanden i Azure Stack. |
| Klienthemlighet | AZURE_CLIENT_SECRET | Huvudnamn tjänstprogrammet hemlighet sparas när tjänstens huvudnamn har skapats. |
| Resource Manager-slutpunkten | ARM_ENDPOINT | Se [The Azure Stack resource manager-slutpunkten](#The-azure-stack-resource-manager-endpoint).  |

### <a name="the-azure-stack-resource-manager-endpoint"></a>Azure Stack resource manager-slutpunkt

Microsoft Azure Resource Manager är en management-ramverk som gör att administratörer kan distribuera, hantera och övervaka Azure-resurser. Azure Resource Manager kan hantera dessa uppgifter som en grupp i stället, i en enda åtgärd.

Du kan hämta metadata-information från Resource Manager-slutpunkten. Slutpunkten som returnerar en JSON-fil med den information som krävs för att köra din kod.

 > [!NOTE]  
 > Den **ResourceManagerUrl** i Azure Stack Development Kit (ASDK) är: `https://management.local.azurestack.external/` Den **ResourceManagerUrl** integrerade system är: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/`  
 > Att hämta de metadata som krävs: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`
  
 JSON-exempelfilen:

 ```json
 {
   "galleryEndpoint": "https://portal.local.azurestack.external:30015/",  
   "graphEndpoint": "https://graph.windows.net/",  
   "portal Endpoint": "https://portal.local.azurestack.external/",
   "authentication": {
     "loginEndpoint": "https://login.windows.net/",
     "audiences": ["https://management.<yourtenant>.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
   }
 }
```

### <a name="set-environmental-variables"></a>Ange miljövariabler

**Microsoft Windows**  
Ange miljövariabler, i en Windows-kommandotolk och använder du följande format:  
`set AZURE_TENANT_ID=<YOUR_TENANT_ID>`

**macOS, Linux och Unix-baserade system** i Unix-baserade system kan du använda följande kommando:  
`export AZURE_TENANT_ID=<YOUR_TENANT_ID>`

## <a name="existing-api-profiles"></a>Befintliga API-profiler

Azure_sdk samlad gem har följande tre profiler:

1. **V2018_03_01** profil som skapats för Azure Stack. Använd den här profilen för att använda de senaste versionerna av tjänster som är tillgängliga i Azure Stack.
2. **V2017_03_09**  
  Profil som skapats för Azure Stack. Använd den här profilen för tjänster som är mest kompatibla med Azure Stack.
3. **senaste**  
  Profil består av senaste versionerna av alla tjänster. Använd de senaste versionerna av alla tjänster.

Mer information om Azure Stack och API-profiler finns i den [sammanfattning av API-profiler](azure-stack-version-profiles.md#summary-of-api-profiles).

## <a name="azure-ruby-sdk-api-profile-usage"></a>Användningen av Azure Ruby SDK API-profil

Du kan använda följande kod för att skapa en instans av en profil-klient. Den här parametern är endast krävs för Azure Stack eller andra privata moln. Global Azure har redan inställningarna som standard.

```Ruby  
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
client = Azure::Resources::Profiles::V2018_03_01::Mgmt::Client.new(options)
```

Profil-klienten kan användas för åtkomst till enskilda resource-leverantörer, till exempel beräkning, lagring och nätverk:

```Ruby  
# To access the operations associated with Compute
profile_client.compute.virtual_machines.get 'RESOURCE_GROUP_NAME', 'VIRTUAL_MACHINE_NAME'

# Option 1: To access the models associated with Compute
purchase_plan_obj = profile_client.compute.model_classes.purchase_plan.new

# Option 2: To access the models associated with Compute
# Notice Namespace: Azure::Profiles::<Profile Name>::<Service Name>::Mgmt::Models::<Model Name>
purchase_plan_obj = Azure::Profiles::V2018_03_01::Compute::Mgmt::Models::PurchasePlan.new
```

## <a name="define-azure-stack-environment-setting-functions"></a>Definiera Azure Stack-miljön inställningen funktioner

För att autentisera tjänstobjektet i Azure Stack-miljön, definiera slutpunkter med `get_active_directory_settings()`. Den här metoden använder den **ARM_Endpoint** miljövariabeln som du anger när din miljövariabler:

```Ruby  
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
```

## <a name="samples-using-api-profiles"></a>Exempel med hjälp av API-profiler

Du kan använda följande exempel finns på GitHub som referens för att skapa lösningar med Ruby- och API: T för Azure Stack-profiler:

- [Hantera Azure-resurser och -resursgrupper med Ruby](https://github.com/Azure-Samples/Hybrid-Resource-Manager-Ruby-Resources-And-Groups)
- [Hantera virtuella datorer med hjälp av Ruby](https://github.com/Azure-Samples/Hybrid-Compute-Ruby-Manage-VM)
- [Distribuera en SSH aktiverad virtuell dator med en mall i Ruby](https://github.com/Azure-Samples/Hybrid-Resource-Manager-Ruby-Template-Deployment)

### <a name="sample-resource-manager-and-groups"></a>Exemplet Resource Manager och grupper

Se till att du har installerat Ruby för att köra exemplet. Om du använder Visual Studio Code kan du ladda ned tillägget Ruby SDK.

> [!NOTE]  
> Du kan hämta lagringsplatsen för exemplet på ”[hantera Azure-resurser och resursgrupper med Ruby](https://github.com/Azure-Samples/Hybrid-Resource-Manager-Ruby-Resources-And-Groups)”.

1. Klona databasen:

   ```bash
   git clone https://github.com/Azure-Samples/Hybrid-Resource-Manager-Ruby-Resources-And-Groups.git
   ```

2. Installera beroenden med hjälp av paket:

   ```Bash
   cd Hybrid-Resource-Manager-Ruby-Resources-And-Groups
   bundle install
   ```

3. Skapa en Azure-tjänstens huvudnamn med hjälp av PowerShell och hämta de värden som behövs.

   Anvisningar om hur du skapar ett huvudnamn för tjänsten finns i [med Azure PowerShell för att skapa ett huvudnamn för tjänsten med ett certifikat](../azure-stack-create-service-principals.md).

   Värden som behövs är:
   - Klient-ID:t
   - Klientorganisations-ID
   - Klienthemlighet
   - Prenumerations-ID:t
   - Resource Manager-slutpunkten

   Ange följande miljövariabler med hjälp av informationen som du hämtade från tjänstens huvudnamn du skapade.

   - Exportera AZURE_TENANT_ID = {ditt klient-id}
   - Exportera AZURE_CLIENT_ID = {din klient-id}
   - Exportera AZURE_CLIENT_SECRET = {klienthemlighet}
   - Exportera AZURE_SUBSCRIPTION_ID = {ditt prenumerations-id}
   - Exportera ARM_ENDPOINT = {din AzureStack Resource manager-url}

   > [!NOTE]  
   > På Windows, Använd i stället för export.

4. Kontrollera plats-variabeln anges till din Azure Stack-location; till exempel `LOCAL="local"`.

5. Lägg till följande rad med kod om du använder Azure Stack eller andra privata moln och ange rätt active directory-slutpunkter:

   ```Ruby  
   active_directory_settings = get_active_directory_settings(ENV['ARM_ENDPOINT'])
   ```

6. Lägg till inställningarna för Active Directory och den grundläggande Webbadressen ska fungera med Azure Stack i variabeln alternativ:

   ```ruby  
   options = {
   credentials: credentials,
   subscription_id: subscription_id,
   active_directory_settings: active_directory_settings,
   base_url: ENV['ARM_ENDPOINT']
   }
   ```

7. Skapa en profil-klient som riktar sig mot Azure Stack-profil:

   ```ruby  
   client = Azure::Resources::Profiles::V2018_03_01::Mgmt::Client.new(options)
   ```

8. För att autentisera tjänstens huvudnamn med Azure Stack slutpunkterna ska definieras med hjälp av **get_active_directory_settings()**. Den här metoden använder den **ARM_Endpoint** miljövariabeln som du anger när din miljövariabler:

   ```ruby  
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
   ```

9. Kör exemplet.

   ```ruby
   bundle exec ruby example.rb
   ```

## <a name="next-steps"></a>Nästa steg

- [Installera PowerShell för Azure Stack](azure-stack-powershell-install.md)
- [Konfigurera PowerShell-miljö för Azure Stack-användare](azure-stack-powershell-configure-user.md)  
