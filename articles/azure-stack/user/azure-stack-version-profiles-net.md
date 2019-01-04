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
ms.date: 12/07/2018
ms.author: sethm
ms.reviewer: sijuman
ms.openlocfilehash: 20e96ad7a99fdb8c90f3b7990965d7225aef8be0
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2018
ms.locfileid: "53555021"
---
# <a name="use-api-version-profiles-with-net-in-azure-stack"></a>Använda API-versionsprofiler med .NET i Azure Stack

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

.NET SDK för Azure Stack Resource Manager innehåller verktyg som hjälper dig att skapa och hantera infrastrukturen. Resursprovidrar i SDK innehåller beräkning, nätverk, lagring, app services och [KeyVault](../../key-vault/key-vault-whatis.md). .NET SDK innehåller 14 NuGet-paket. Dessa paket måste laddas ned till ditt projektlösning varje gång som införlivar profilinformation. Men du kan hämta vilka resursprovidern specifikt du ska använda för 2018-03-01-hybrid eller 2017-03-09-profile för att optimera minnet för ditt program. Varje paket består av en resursleverantör, respektive API-versionen och API-profilen som den tillhör. API-profiler i .NET SDK aktivera hybrid molnutveckling genom att hjälpa dig att växla mellan globala Azure-resurser och resurser på Azure Stack.

## <a name="net-and-api-version-profiles"></a>.NET och API-versionsprofiler

En API-profil är en kombination av resursprovidrar och API-versioner. Du kan använda en API-profil för att hämta den senaste och mest stabila versionen av varje resurstyp i ett paket för resource provider.

-   Att göra använder de senaste versionerna av alla tjänster kan använda den **senaste** profil paket. Den här profilen är en del av den **Microsoft.Azure.Management** NuGet-paketet.

-   Om du vill använda tjänsterna som är kompatibla med Azure Stack, använda den **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01. *ResourceProvider*. 0.9.0-preview.nupkg** eller **Microsoft.Azure.Management.Profiles.hybrid\_2017\_03\_09. *ResourceProvider*. 0.9.0-preview.nupkg** paket.

    -   Det finns två paket för varje resursprovider för varje profil.

    -   Se till att den **ResourceProvider** delen av ovanstående NuGet-paketet har ändrats till rätt-providern.

-   Om du vill använda den senaste API-versionen av en tjänst, använda den **senaste** profilen för det specifika NuGet-paketet. Exempel: Om du vill använda den **senaste API** version av beräkningstjänsten ensamt och Använd den **senaste** profilen för den **compute** paketet. Den **senaste** profil är en del av den **Microsoft.Azure.Management** NuGet-paketet.

-   Om du vill använda specifika API-versioner för en resurstyp i en viss resursprovider, använder du de specifika API-versioner som definierats i paketet.

Du kan kombinera alla alternativ i samma program.

## <a name="install-the-azure-net-sdk"></a>Installera Azure .NET SDK

1.  Installera Git. Anvisningar finns i [Komma igång – Installera Git][].

2.  Om du vill installera rätt NuGet-paketen, se [Söka efter och installera ett paket][].

3.  De paket som måste installeras beror på vilken profil-version som du vill använda. Paketnamn för profilversioner är:

    1.  **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01. *ResourceProvider*. 0.9.0-preview.nupkg**

    2.  **Microsoft.Azure.Management.Profiles.hybrid\_2017\_03\_09. *ResourceProvider*. 0.9.0-preview.nupkg**

4.  Om du vill installera rätt NuGet-paket för Visual Studio Code, se länken nedan för att ladda ned den [NuGet-Pakethanteraren instruktioner][].

5.  Om det inte finns skapar du en prenumeration och spara prenumerations-ID som ska användas senare. Anvisningar för att skapa en prenumeration finns i [skapa prenumerationer för erbjudanden i Azure Stack][].

6.  Skapa ett huvudnamn för tjänsten och spara klient-ID och Klienthemlighet. Anvisningar om hur du skapar ett huvudnamn för tjänsten för Azure Stack finns i [ge program åtkomst till Azure Stack][]. Klient-ID är även känd som program-ID när du skapar ett tjänstens huvudnamn.

7.  Kontrollera att tjänstens huvudnamn har rollen deltagare/ägare av prenumerationen. Anvisningar om hur du tilldelar en roll till tjänstens huvudnamn finns i [ge program åtkomst till Azure Stack][].

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda Azure-SDK för .NET med Azure Stack, måste du ange följande värden och ange sedan värden med miljövariabler. Se anvisningarna efter tabellen för ditt operativsystem för att ställa in miljövariabler.

| Värde                     | Miljövariabler   | Beskrivning                                                                                                             |
|---------------------------|-------------------------|-------------------------------------------------------------------------------------------------------------------------|
| Klient-ID:t                 | AZURE_TENANT_ID       | Värdet för Azure Stack [ *klient-ID*][].                                                                          |
| Klientorganisations-ID                 | AZURE_CLIENT_ID       | Tjänsten huvudnamn program-ID sparas när tjänstens huvudnamn skapades i föregående avsnitt i den här artikeln. |
| Prenumerations-ID:t           | AZURE_SUBSCRIPTION_ID | Den [ *prenumerations-ID* ][] är hur du kommer åt erbjudanden i Azure Stack.                                                      |
| Klienthemlighet             | AZURE_CLIENT_SECRET   | Tjänstens huvudnamn programhemlighet sparas när tjänstens huvudnamn har skapats.                                      |
| Resource Manager-slutpunkten | ARM_ENDPOINT           | Se [ *Azure Stack Resource Manager-slutpunkten*][].                                                                    |
| Plats                  | RESOURCE_LOCATION     | Plats för Azure Stack.

Du hittar klient-ID för Azure Stack, följer du instruktionerna [här](../azure-stack-csp-ref-operations.md). Om du vill ställa in din miljövariabler, gör du följande:

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

### <a name="the-azure-stack-resource-manager-endpoint"></a>Azure Stack Resource Manager-slutpunkten

Microsoft Azure Resource Manager är en management-ramverk som gör att administratörer kan distribuera, hantera och övervaka Azure-resurser. Azure Resource Manager kan hantera dessa uppgifter som en grupp i stället, i en enda åtgärd.

Du kan hämta metadata-information från Resource Manager-slutpunkten. Slutpunkten som returnerar en JSON-fil med den information som krävs för att köra din kod.

Observera följande överväganden:

- Den **ResourceManagerUrl** i Azure Stack Development Kit (ASDK) är: https://management.local.azurestack.external/

- Den **ResourceManagerUrl** integrerade system är: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/` Att hämta de metadata som krävs: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`

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

1.  **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01. *ResourceProvider*. 0.9.0-preview.nupkg**: Senaste profil har skapats för Azure Stack. Använd den här profilen för tjänster som är mest kompatibla med Azure Stack så länge du är på 1808 stämpel eller ytterligare.

2.  **Microsoft.Azure.Management.Profiles.hybrid\_2017\_03\_09. *ResourceProvider*. 0.9.0-preview.nupkg**: Om du har en tidsstämpel som är lägre än den 1808-versionen kan du använda den här profilen.

3.  **Senaste**: Profil som består av de senaste versionerna av alla tjänster. Använd de senaste versionerna av alla tjänster. Den här profilen är en del av den **Microsoft.Azure.Management** NuGet-paketet.

Mer information om Azure Stack och API-profiler finns i en [sammanfattning av API-profiler][].

## <a name="azure-net-sdk-api-profile-usage"></a>Användningen av Azure .NET SDK API-profil

Följande kod ska användas för att skapa en instans av en resource management-klienten. Liknande kod kan användas för att skapa en instans av andra provider för nätverksresurser (till exempel beräkning, nätverk och lagring) klienter. 

```csharp
var client = new ResourceManagementClient(armEndpoint, credentials)
{
    SubscriptionId = subscriptionId
};
```

Den `credentials` parametern i koden ovan krävs för att skapa en instans av en klient. Följande kod genererar en autentiseringstoken av klient-ID och tjänstens huvudnamn.

```csharp
var azureStackSettings = getActiveDirectoryServiceSettings(armEndpoint);
var credentials = ApplicationTokenProvider.LoginSilentAsync(tenantId, servicePrincipalId, servicePrincipalSecret, azureStackSettings).GetAwaiter().GetResult();
```
Den `getActiveDirectoryServiceSettings` anropet i koden hämtar Azure Stack-slutpunkter från slutpunkten för metadata. Den anger miljövariabler vid anrop som görs: 

```csharp
public static ActiveDirectoryServiceSettings getActiveDirectoryServiceSettings(string armEndpoint)
{
    var settings = new ActiveDirectoryServiceSettings();
    try
    {
        var request = (HttpWebRequest)HttpWebRequest.Create(string.Format("{0}/metadata/endpoints?api-version=1.0", armEndpoint));
        request.Method = "GET";
        request.UserAgent = ComponentName;
        request.Accept = "application/xml";
        using (HttpWebResponse response = (HttpWebResponse)request.GetResponse())
        {
            using (StreamReader sr = new StreamReader(response.GetResponseStream()))
            {
                var rawResponse = sr.ReadToEnd();
                var deserialized = JObject.Parse(rawResponse);
                var authenticationObj = deserialized.GetValue("authentication").Value<JObject>();
                var loginEndpoint = authenticationObj.GetValue("loginEndpoint").Value<string>();
                var audiencesObj = authenticationObj.GetValue("audiences").Value<JArray>();
                settings.AuthenticationEndpoint = new Uri(loginEndpoint);
                settings.TokenAudience = new Uri(audiencesObj[0].Value<string>());
                settings.ValidateAuthority = loginEndpoint.TrimEnd('/').EndsWith("/adfs", StringComparison.OrdinalIgnoreCase) ? false : true;
            }
        }
    }
    catch (Exception ex)
    {
        Console.WriteLine(String.Format("Could not get AD service settings. Exception: {0}", ex.Message));
    }
    return settings;
}
```
Detta kan du använda API-profilen NuGet-paket för att distribuera programmet till Azure Stack.

## <a name="samples-using-api-profiles"></a>Exempel med hjälp av API-profiler

Följande exempel kan användas som referens för att skapa lösningar med .NET och Azure Stack-API-profiler.
- [Hantera resursgrupper](https://github.com/Azure-Samples/hybrid-resources-dotnet-manage-resource-group)
- [Hantera Lagringskonton](https://github.com/Azure-Samples/hybird-storage-dotnet-manage-storage-accounts)
- [Hantera en virtuell dator](https://github.com/Azure-Samples/hybrid-compute-dotnet-manage-vm)

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
  [* de Azure Stack Resource Manager-slutpunkten *]: ../user/azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint
  [Sammanfattning av API-profiler]: ../user/azure-stack-version-profiles.md#summary-of-api-profiles
  [Test Project to Virtual Machine, vNet, resource groups, and storage account]: https://github.com/seyadava/azure-sdk-for-net-samples/tree/master/TestProject
  [Use Azure PowerShell to create a service principal with a certificate]: ../azure-stack-create-service-principals.md
  [Run unit tests with Test Explorer.]: /visualstudio/test/run-unit-tests-with-test-explorer?view=vs-2017
