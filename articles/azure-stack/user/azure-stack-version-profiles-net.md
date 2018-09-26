---
title: Med hjälp av API-versionsprofiler med .NET SDK i Azure Stack | Microsoft Docs
description: Lär dig mer om hur du använder API-versionsprofiler med .NET i Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2018
ms.author: sethm
ms.reviewer: sijuman
ms.openlocfilehash: 35329468ee01d5b70d654c1eb4a908db9d3fcb5d
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2018
ms.locfileid: "47184906"
---
# <a name="use-api-version-profiles-with-net-in-azure-stack"></a>Använda API-versionsprofiler med .NET i Azure Stack

*Gäller för: integrerade Azure Stack-system och Azure Stack Development Kit*

.NET SDK för Azure Stack Resource Manager innehåller verktyg som hjälper dig att skapa och hantera infrastrukturen. Resursprovidrar i SDK innehåller beräkning, nätverk, lagring, app services och [KeyVault](../../key-vault/key-vault-whatis.md). .NET SDK innehåller 14 NuGet-paket måste laddas ned i projektet lösningen varje gång som införlivar profilinformation. Men du kan hämta vilka resursprovidern specifikt du ska använda för 2018-03-01-hybrid eller 2017-03-09-profile för att optimera minnet för ditt program. Varje paket består av en resursleverantör, respektive API-versionen och API-profilen som den tillhör. API-profiler i .NET SDK aktivera hybrid molnutveckling genom att hjälpa dig att växla mellan globala Azure-resurser och resurser på Azure Stack.

## <a name="net-and-api-version-profiles"></a>.NET och API-versionsprofiler

En API-profil är en kombination av resursprovidrar och API-versioner. Du kan använda en API-profil för att hämta den senaste och mest stabila versionen av varje resurstyp i ett paket för resource provider.

-   Att göra använder de senaste versionerna av alla tjänster kan använda den **senaste** profil paket. Den här profilen är en del av den **Microsoft.Azure.Management** NuGet-paketet.

-   Om du vill använda tjänsterna som är kompatibla med Azure Stack, använda den **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01. *ResourceProvider*. 0.9.0-preview.nupkg** eller **Microsoft.Azure.Management.Profiles.hybrid\_2017\_03\_09. *ResourceProvider*. 0.9.0-preview.nupkg** paket.

    -   Det finns två paket för varje resursprovider för varje profil.

    -   Se till att den **ResourceProvider** delen av ovanstående NuGet-paketet har ändrats till rätt-providern.

-   Om du vill använda den senaste API-versionen av en tjänst, använda den **senaste** profilen för det specifika NuGet-paketet. Exempel: Om du vill använda den **senaste API** version av beräkningstjänsten ensamt och Använd den **senaste** profilen för den **compute** paketet. Den **senaste** profil är en del av den **Microsoft.Azure.Management** NuGet-paketet.

-   Om du vill använda specifika API-versioner för en resurstyp i en viss resursprovider, använder du de specifika API-versioner som definierats i paketet.

Observera att du kan kombinera alla alternativ i samma program.

## <a name="install-the-azure-net-sdk"></a>Installera Azure .NET SDK

1.  Installera Git. Anvisningar finns i [Komma igång – Installera Git][].

2.  Om du vill installera rätt NuGet-paketen, se [Söka efter och installera ett paket][].

3.  De paket som måste installeras beror på vilken profil-version som du vill använda. Paketnamnet för profilversioner är:

    1.  **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01. *ResourceProvider*. 0.9.0-preview.nupkg**

    2.  **Microsoft.Azure.Management.Profiles.hybrid\_2017\_03\_09. *ResourceProvider*. 0.9.0-preview.nupkg**

4.  Om du vill installera rätt NuGet-paket för Visual Studio Code, se länken nedan för att ladda ned den [NuGet-Pakethanteraren instruktioner][].

5.  Om det inte finns skapar du en prenumeration och spara prenumerations-ID som ska användas senare. Anvisningar för att skapa en prenumeration finns i [skapa prenumerationer för erbjudanden i Azure Stack][].

6.  Skapa ett huvudnamn för tjänsten och spara klient-ID och Klienthemlighet. Anvisningar om hur du skapar ett huvudnamn för tjänsten för Azure Stack finns i [ge program åtkomst till Azure Stack][]. Observera att klient-ID är även känd som program-ID när du skapar ett huvudnamn för tjänsten.

7.  Kontrollera att tjänstens huvudnamn har rollen deltagare/ägare av prenumerationen. Anvisningar om hur du tilldelar en roll till tjänstens huvudnamn finns i [ge program åtkomst till Azure Stack][].

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda Azure-SDK för .NET med Azure Stack, måste du ange följande värden och ange sedan värden med miljövariabler. Se anvisningarna efter tabellen för ditt operativsystem för att ställa in miljövariabler.

| Värde                     | Miljövariabler   | Beskrivning                                                                                                             |
|---------------------------|-------------------------|-------------------------------------------------------------------------------------------------------------------------|
| Klient-ID:t                 | AZURE_TENANT_ID       | Värdet för Azure Stack [ *klient-ID*][].                                                                          |
| Klient-ID                 | AZURE_CLIENT_ID       | Tjänsten huvudnamn program-ID sparas när tjänstens huvudnamn skapades i föregående avsnitt i den här artikeln. |
| Prenumerations-ID:t           | AZURE_SUBSCRIPTION_ID | Den [ *prenumerations-ID* ][] är hur du kommer åt erbjudanden i Azure Stack.                                                      |
| Klienthemlighet             | AZURE_CLIENT_SECRET   | Tjänstens huvudnamn programhemlighet sparas när tjänstens huvudnamn har skapats.                                      |
| Resource Manager-slutpunkten | ARM_ENDPOINT           | Se [ *Azure Stack resource manager-slutpunkten*][].                                                                    |

Du hittar klient-ID för Azure Stack, följer du instruktionerna [här](../azure-stack-csp-ref-operations.md). Ange din miljövariabler genom att göra följande:

### <a name="microsoft-windows"></a>Microsoft Windows

Om du vill ange miljövariabler i en kommandotolk i Windows, använder du följande format:

```shell
Set Azure_Tenant_ID=Your_Tenant_ID
```

### <a name="macos-linux-and-unix-based-systems"></a>macOS, Linux och Unix-baserade system

I Unix-baserat system, kan du använda följande kommando:

```shell
Export Azure_Tenant_ID=Your_Tenant_ID
```

### <a name="the-azure-stack-resource-manager-endpoint"></a>Azure Stack resource manager-slutpunkt

Microsoft Azure Resource Manager är en management-ramverk som gör att administratörer kan distribuera, hantera och övervaka Azure-resurser. Azure Resource Manager kan hantera dessa uppgifter som en grupp i stället, i en enda åtgärd.

Du kan hämta metadata-information från Resource Manager-slutpunkten. Slutpunkten som returnerar en JSON-fil med den information som krävs för att köra din kod.

Observera följande överväganden:

- Den **ResourceManagerUrl** i Azure Stack Development Kit (ASDK) är: https://management.local.azurestack.external/

- Den **ResourceManagerUrl** integrerade system är: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/` att hämta de metadata som krävs: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`

JSON-exempelfilen:

```json
{ 
   "galleryEndpoint": "https://portal.local.azurestack.external:30015/",
   "graphEndpoint": "https://graph.windows.net/",
   "portal Endpoint": "https://portal.local.azurestack.external/",
   "authentication": 
      {
      "loginEndpoint": "https://login.windows.net/",
      "audiences": ["https://management.yourtenant.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
      }
}
```

## <a name="existing-api-profiles"></a>Befintliga API-profiler

1.  **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01. *ResourceProvider*. 0.9.0-preview.nupkg**: senaste profil som skapats för Azure Stack. Använd den här profilen för tjänster som är mest kompatibla med Azure Stack så länge du är på 1808 stämpel eller ytterligare.

2.  **Microsoft.Azure.Management.Profiles.hybrid\_2017\_03\_09. *ResourceProvider*. 0.9.0-preview.nupkg**: Om du har en tidsstämpel som är lägre än den 1808-versionen kan använda den här profilen.

3.  **Senaste**: profilen som består av de senaste versionerna av alla tjänster. Använd de senaste versionerna av alla tjänster. Den här profilen är en del av den **Microsoft.Azure.Management** NuGet-paketet.

Mer information om Azure Stack och API-profiler finns i en [sammanfattning av API-profiler][].

## <a name="azure-net-sdk-api-profile-usage"></a>Användningen av Azure .NET SDK API-profil

Följande kod ska användas för att skapa en instans av en profil-klient. Den här parametern är endast krävs för Azure Stack eller andra privata moln. Global Azure har redan inställningarna som standard.

Följande kod behövs för att autentisera tjänstobjektet i Azure Stack. Skapas en token genom att klienten-ID och grundläggande autentisering, som är specifika för Azure Stack.

```csharp
public class CustomLoginCredentials : ServiceClientCredentials
{
    private string clientId;
    private string clientSecret;
    private string resourceId;
    private string tenantId;

    private const string authenticationBase = "https://login.windows.net/{0}";

    public CustomLoginCredentials(string servicePrincipalId, string servicePrincipalSecret, string azureEnvironmentResourceId, string azureEnvironmentTenandId)
    {
        clientId = servicePrincipalId;
        clientSecret = servicePrincipalSecret;
        resourceId = azureEnvironmentResourceId;
        tenantId = azureEnvironmentTenandId;
    }
```

Detta kan du använda API-profilen NuGet-paket för att distribuera programmet till Azure Stack.

## <a name="define-azure-stack-environment-setting-functions"></a>Definiera Azure Stack-miljön inställningen funktioner

Använd följande kod för att autentisera tjänstobjektet i Azure Stack-miljön:

```csharp
private string AuthenticationToken { get; set; }
public override void InitializeServiceClient<T>(ServiceClient<T> client)
{
    var authenticationContext = new AuthenticationContext(String.Format(authenticationBase, tenantId));
    var credential = new ClientCredential(clientId, clientSecret);
    var result = authenticationContext.AcquireTokenAsync(resource: resourceId,
    clientCredential: credential).Result;
    if (result == null)
    {
        throw new InvalidOperationException("Failed to obtain the JWT token");
    }
    AuthenticationToken = result.AccessToken;
}
```

Detta åsidosätter initiera tjänstklienten autentisera till Azure Stack.

## <a name="samples-using-api-profiles"></a>Exempel med hjälp av API-profiler

Du kan använda följande exempel finns i GitHub-lagringsplatser som referens för att skapa lösningar med .NET och Azure Stack-API-profiler.

-   [Testa projektet för att virtuella datorer, virtuella nätverk, resursgrupper och storage-konto][]
-   Hantera virtuella datorer med hjälp av .NET

### <a name="sample-unit-test-project"></a>Exempelprojektet enhet Test 

1.  Klona databasen med hjälp av följande kommando:

    `git clone <https://github.com/seyadava/azure-sdk-for-net-samples/tree/master/TestProject>`

2.  Skapa en Azure-tjänstens huvudnamn och tilldela en roll får åtkomst till prenumerationen. Anvisningar om hur du skapar ett huvudnamn för tjänsten finns i [Använda Azure PowerShell för att skapa ett huvudnamn för tjänsten med ett certifikat][].

3.  Hämta följande nödvändiga värden:

    1.  Klient-ID:t
    2.  Klient-ID
    3.  Klienthemlighet
    4.  Prenumerations-ID:t
    5.  Resource Manager-slutpunkten

4.  Ange följande miljövariabler med hjälp av informationen som du hämtade från tjänstens huvudnamn du skapat med hjälp av Kommandotolken:

    1.  Exportera AZURE_TENANT_ID = {ditt klient-id}
    2.  Exportera AZURE_CLIENT_ID = {din klient-id}
    3.  Exportera AZURE_CLIENT_SECRET = {klienthemlighet}
    4.  Exportera AZURE_SUBSCRIPTION_ID = {ditt prenumerations-id}
    5.  Exportera ARM_ENDPOINT = {din Azure Stack Resource manager-URL}

   I Windows, använder **ange** i stället för **exportera**.

5.  Kontrollera plats-variabeln anges till din Azure Stack-plats. Till exempel LOCAL = ”local”.

6.  Ange anpassade inloggningsuppgifterna som gör att du kan autentisera till Azure Stack. Observera att den här delen av kod som ingår i det här exemplet i mappen auktorisering.

   ```csharp
   public class CustomLoginCredentials : ServiceClientCredentials
   {
       private string clientId;
       private string clientSecret;
       private string resourceId;
       private string tenantId;
       private const string authenticationBase = "https://login.windows.net/{0}";
       public CustomLoginCredentials(string servicePrincipalId, string servicePrincipalSecret, string azureEnvironmentResourceId, string azureEnvironmentTenandId)
       {
           clientId = servicePrincipalId;
           clientSecret = servicePrincipalSecret;
           resourceId = azureEnvironmentResourceId;
           tenantId = azureEnvironmentTenandId;
       }
   private string AuthenticationToken { get; set; }
   ```

7.  Lägg till följande kod om du använder Azure Stack för att åsidosätta initiera tjänstklienten autentisera till Azure Stack. Observera att en del av koden ingår redan i det här exemplet i mappen auktorisering.

   ```csharp
   public override void InitializeServiceClient<T>(ServiceClient<T> client)
   {
      var authenticationContext = new AuthenticationContext(String.Format(authenticationBase, tenantId));
      var credential = new ClientCredential(clientId, clientSecret);
      var result = authenticationContext.AcquireTokenAsync(resource: resourceId,
                clientCredential: credential).Result;
      if (result == null)
      {
          throw new InvalidOperationException("Failed to obtain the JWT token");
      }
      AuthenticationToken = result.AccessToken;
   }
   ```
 
8.  Med hjälp av NuGet-Pakethanteraren, Sök efter ”2018-03-01-hybrid” och installera de paket som är associerade med den här profilen för beräkning, nätverk, lagring, KeyVault och App Services-resursprovidrar.

2.  Ange de parametrar som krävs för att arbeta med Azure Stack i varje aktivitet i filen .cs. Ett exempel illustreras på följande sätt för aktiviteten `CreateResourceGroupTest`:

   ```csharp
   var location = Environment.GetEnvironmentVariable("AZURE_LOCATION");
   var baseUriString = Environment.GetEnvironmentVariable("AZURE_BASE_URL");
   var resourceGroupName = Environment.GetEnvironmentVariable("AZURE_RESOURCEGROUP");
   var servicePrincipalId = Environment.GetEnvironmentVariable("AZURE_CLIENT_ID");
   var servicePrincipalSecret = Environment.GetEnvironmentVariable("AZURE_CLIENT_SECRET");
   var azureResourceId = Environment.GetEnvironmentVariable("AZURE_RESOURCE_ID");
   var tenantId = Environment.GetEnvironmentVariable("AZURE_TENANT_ID");
   var subscriptionId = Environment.GetEnvironmentVariable("AZURE_SUBSCRIPTION_ID");
   var credentials = new CustomLoginCredentials(servicePrincipalId, servicePrincipalSecret, azureResourceId, tenantId);
   ```

1.  Högerklicka på varje uppgift och välj **Kör test**.

    1.  I fönstret sida fönstret gröna bockmarkeringar aviserar att varje uppgift har skapats har baserat på de parametrar som anges. Kontrollera Azure Stack-prenumerationen för att säkerställa att resurserna som har skapats.

    2.  Läs mer om hur du kör enhetstester [köra enhetstester med Test Explorer.][]

## <a name="next-steps"></a>Nästa steg

Mer information om API-profiler finns:

- [Hantera API-versionsprofiler i Azure Stack](azure-stack-version-profiles.md)
- [Resource provider API-versioner som stöds av profiler](azure-stack-profiles-azure-resource-manager-versions.md)

  [Komma igång – Installera Git]: https://git-scm.com/book/en/v2/Getting-Started-Installing-Git
  [Söka efter och installera ett paket]: /nuget/tools/package-manager-ui
  [NuGet-Pakethanteraren instruktioner]: https://marketplace.visualstudio.com/items?itemName=jmrog.vscode-nuget-package-manager
  [Skapa prenumerationer för erbjudanden i Azure Stack]: ../azure-stack-subscribe-plan-provision-vm.md
  [Ge program åtkomst till Azure Stack]: ../azure-stack-create-service-principals.md
  [* klient -ID *]: ../azure-stack-identity-overview.md
  [* prenumerations -ID *]: ../azure-stack-plan-offer-quota-overview.md#subscriptions
  [* de Azure Stack resource manager-slutpunkten *]: ../user/azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint
  [Sammanfattning av API-profiler]: ../user/azure-stack-version-profiles.md#summary-of-api-profiles
  [Testa projektet för att virtuella datorer, virtuella nätverk, resursgrupper och storage-konto]: https://github.com/seyadava/azure-sdk-for-net-samples/tree/master/TestProject
  [Använda Azure PowerShell för att skapa ett huvudnamn för tjänsten med ett certifikat]: ../azure-stack-create-service-principals.md
  [Köra enhetstester med Test Explorer.]: /visualstudio/test/run-unit-tests-with-test-explorer?view=vs-2017
