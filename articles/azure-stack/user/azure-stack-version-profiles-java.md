---
title: Med hjälp av API-versionsprofiler med Java i Azure Stack | Microsoft Docs
description: Lär dig mer om hur du använder API-versionsprofiler med Java i Azure Stack.
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
ms.openlocfilehash: b5a876ea8b5cc70ee0ca0dcac8628c12dc2b009b
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2018
ms.locfileid: "47414531"
---
# <a name="use-api-version-profiles-with-java-in-azure-stack"></a>Använd API-versionsprofiler med Java i Azure Stack

*Gäller för: integrerade Azure Stack-system och Azure Stack Development Kit*

Java SDK för Azure Stack Resource Manager innehåller verktyg som hjälper dig att skapa och hantera infrastrukturen. Resursprovidrar i SDK innehåller beräkning, nätverk, lagring, app services och [KeyVault](../../key-vault/key-vault-whatis.md). Java SDK innehåller API-profiler genom att inkludera beroenden i Pom.xml-filen som läser in de rätta modulerna i den .java-fil. Men du kan lägga till flera profiler som beroenden, till exempel den **2018-03-01-hybrid**, eller **senaste** som Azure-profil. Med hjälp av dessa beroenden läses in rätt modul så att när du skapar din resurstyp, du kan välja vilka API-versionen från profilerna som du vill använda. På så sätt kan du använda de senaste versionerna i Azure, när du utvecklar mot de senaste API-versionerna för Azure Stack. Med hjälp av Java SDK kan en verklig utvecklarupplevelse för molnet. API-profiler i Java SDK aktivera hybrid molnutveckling genom att hjälpa dig att växla mellan globala Azure-resurser och resurser i Azure Stack.

## <a name="java-and-api-version-profiles"></a>Java- och API-versionsprofiler

En API-profil är en kombination av resursprovidrar och API-versioner. Du kan använda en API-profil för att hämta den senaste och mest stabila versionen av varje resurstyp i ett paket för resource provider.

- Om du vill använda de senaste versionerna av alla tjänster, använda den **senaste** profilen beroendet.
    
   - Om du vill använda den senaste profilen beroendet är **com.microsoft.azure**.

   - Om du vill använda tjänsterna som är kompatibla med Azure Stack, använda den **com.microsoft.azure.profile\_2018\_03\_01\_hybrid** profil.
    
      - Det här är anges i Pom.xml-filen som ett beroende som läser in moduler automatiskt om du väljer rätt klassen från den nedrullningsbara listan precis som med .NET.
        
          - Upp i varje modul visas på följande sätt:         
           `Import com.microsoft.azure.management.resources.v2018_03_01.ResourceGroup`
             

  - Beroenden visas på följande sätt:
     ```xml
     <dependency>
     <groupId>com.microsoft.azure.profile_2018_03_01_hybrid</groupId>
     <artifactId>azure</artifactId>
     <version>1.0.0-beta</version>
     </dependency>
     ```

  - Om du vill använda specifika API-versioner för en resurstyp i en viss resursprovider, använder du de specifika API-versioner som definieras via intellisense.

Observera att du kan kombinera alla alternativ i samma program.

## <a name="install-the-azure-java-sdk"></a>Installera Azure Java SDK

Använd följande steg för att installera Java SDK:

1.  Följ officiella anvisningarna för att installera Git. Anvisningar finns i [komma igång - installerar Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).

2.  Följ officiella anvisningarna för att installera den [Java SDK](http://zulu.org/download/)) och [Maven](https://maven.apache.org/). Rätt version är version 8 av Java Developer Kit. Rätt Apache Maven är version 3.0 eller senare. Miljövariabeln JAVA_HOME måste anges till installationsplatsen för Java Development Kit för att slutföra snabbstarten. Mer information finns i [skapa din första funktion med Java och Maven](../../azure-functions/functions-create-first-java-maven.md).

3.  Installera rätt beroendepaketen genom att öppna filen Pom.xml i ditt Java-program. Lägg till ett beroende som visas i följande kod:

   ```xml  
   <dependency>
   <groupId>com.microsoft.azure.profile_2018_03_01_hybrid</groupId>
   <artifactId>azure</artifactId>
   <version>1.0.0-beta</version>
   </dependency>
   ```

4.  De paket som måste installeras beror på vilken profil-version som du vill använda. Paketnamn för profilversioner är:
    
   - **com.microsoft.Azure.profile\_2018\_03\_01\_hybrid**
   - **com.microsoft.Azure**
      - **senaste**

5.  Om det inte finns skapar du en prenumeration och spara prenumerations-ID för senare användning. Anvisningar om hur du skapar en prenumeration finns i [skapa prenumerationer för erbjudanden i Azure Stack](../azure-stack-subscribe-plan-provision-vm.md).

6.  Skapa ett huvudnamn för tjänsten och spara klient-ID och Klienthemlighet. Anvisningar om hur du skapar ett huvudnamn för tjänsten för Azure Stack finns i [ge program åtkomst till Azure Stack](../azure-stack-create-service-principals.md). Observera att klient-ID är även känd som program-ID när du skapar ett huvudnamn för tjänsten.

7.  Kontrollera att tjänstens huvudnamn har rollen deltagare/ägare av prenumerationen. Anvisningar om hur du tilldelar en roll till tjänstens huvudnamn finns i [ge program åtkomst till Azure Stack](../azure-stack-create-service-principals.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda Azure-SDK för .NET med Azure Stack, måste du ange följande värden och ange sedan värden med miljövariabler. Se anvisningarna efter tabellen för ditt operativsystem för att ställa in miljövariabler.

| Värde                     | Miljövariabler | Beskrivning                                                                                                                                                                                                          |
| ------------------------- | --------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Klient-ID:t                 | TENANT_ID            | Värdet för Azure Stack [ <span class="underline">klient-ID</span>](../azure-stack-identity-overview.md).                                                          |
| Klient-ID                 | CLIENT_ID             | Tjänsten huvudnamn program-ID sparas när tjänstens huvudnamn har skapats i föregående avsnitt i det här dokumentet.                                                                                              |
| Prenumerations-ID:t           | SUBSCRIPTION_ID      | Den [ <span class="underline">prenumerations-ID</span> ](../azure-stack-plan-offer-quota-overview.md#subscriptions) är hur du kommer åt erbjudanden i Azure Stack.                |
| Klienthemlighet             | CLIENT_SECRET        | Huvudnamn tjänstprogrammet hemlighet sparas när tjänstens huvudnamn har skapats.                                                                                                                                   |
| Resource Manager-slutpunkten | SLUTPUNKT              | Se [ <span class="underline">Azure Stack resource manager-slutpunkten</span>](../user/azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint). |
| Plats                  | RESOURCE_LOCATION    | Lokal för Azure Stack                                                                                                                                                                                                |

Du hittar klient-ID för Azure Stack, följer du instruktionerna [här](../azure-stack-csp-ref-operations.md). Ange din miljövariabler genom att göra följande:

### <a name="microsoft-windows"></a>Microsoft Windows

Om du vill ange miljövariabler i en kommandotolk i Windows, använder du följande format:

```shell
Set Azure_Tenant_ID=<Your_Tenant_ID>
```

### <a name="macos-linux-and-unix-based-systems"></a>macOS, Linux och Unix-baserade system

I Unix-baserat system, kan du använda följande kommando:

```shell
Export Azure_Tenant_ID=<Your_Tenant_ID>
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
      "audiences": ["https://management.<yourtenant>.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
      }
}
```

## <a name="existing-api-profiles"></a>Befintliga API-profiler

1.  **com.microsoft.Azure.profile\_2018\_03\_01\_hybrid**: senaste profil som skapats för Azure Stack. Använd den här profilen för tjänster som är mest kompatibla med Azure Stack så länge du är på 1808 stämpel eller ytterligare.

2.  **com.microsoft.Azure.profile\_2017\_03\_09\_profil**: Om du har en tidsstämpel som är lägre än den 1808-versionen kan använda den här profilen.

3.  **com.microsoft.Azure**: profilen som består av de senaste versionerna av alla tjänster. Använd de senaste versionerna av alla tjänster.

Mer information om Azure Stack och API-profiler finns i den [sammanfattning av API-profiler](../user/azure-stack-version-profiles.md#summary-of-api-profiles).

## <a name="azure-java-sdk-api-profile-usage"></a>Användningen av Azure Java SDK API-profil

Följande kod autentiserar tjänstens huvudnamn i Azure Stack. Skapas en token genom att klienten-ID och grundläggande autentisering, som är specifika för Azure Stack:

```java
AzureTokenCredentials credentials = new ApplicationTokenCredentials(client, tenant, key, AZURE_STACK)
                    .withDefaultSubscriptionId(subscriptionId);
            Azure azureStack = Azure.configure().withLogLevel(com.microsoft.rest.LogLevel.BASIC)
                    .authenticate(credentials, credentials.defaultSubscriptionId());
```

På så sätt kan du använda beroenden för API-profil för att distribuera programmet till Azure Stack.

## <a name="define-azure-stack-environment-setting-functions"></a>Definiera Azure Stack-miljön inställningen funktioner

Om du vill registrera Azure Stack-moln med rätt slutpunkterna, Använd följande kod:

```java
AzureEnvironment AZURE_STACK = new AzureEnvironment(new HashMap<String, String>() {
                {
                    put("managementEndpointUrl", settings.get("audience"));
                    put("resourceManagerEndpointUrl", armEndpoint);
                    put("galleryEndpointUrl", settings.get("galleryEndpoint"));
                    put("activeDirectoryEndpointUrl", settings.get("login_endpoint"));
                    put("activeDirectoryResourceId", settings.get("audience"));
                    put("activeDirectoryGraphResourceId", settings.get("graphEndpoint"));
                    put("storageEndpointSuffix", armEndpoint.substring(armEndpoint.indexOf('.')));
                    put("keyVaultDnsSuffix", ".adminvault" + armEndpoint.substring(armEndpoint.indexOf('.')));
                }
            });
```

Den `getActiveDirectorySettings` anropet i följande kod hämtar slutpunkterna från Metadataslutpunkter. Den anger miljövariabler vid anrop som görs:

```java
public static HashMap<String, String>
getActiveDirectorySettings(String armEndpoint) {

HashMap<String, String> adSettings = new HashMap<String, String>();

try {

// create HTTP Client
HttpClient httpClient = HttpClientBuilder.create().build();

// Create new getRequest with below mentioned URL
HttpGet getRequest = new
HttpGet(String.format("%s/metadata/endpoints?api-version=1.0",
armEndpoint));

// Add additional header to getRequest which accepts application/xml
data
getRequest.addHeader("accept", "application/xml");

// Execute request and catch response
HttpResponse response = httpClient.execute(getRequest);
```

## <a name="samples-using-api-profiles"></a>Exempel med hjälp av API-profiler

Du kan använda följande GitHub-exempel som referens för att skapa lösningar med .NET och Azure Stack-API-profiler:

  - [Hantera resursgrupper](https://github.com/viananth/resources-java-manage-resource-group/tree/stack/Hybrid)

  - [Hantera Lagringskonton](https://github.com/viananth/storage-java-manage-storage-accounts/tree/stack/Hybrid)

  - [Hantera en virtuell dator](https://github.com/viananth/compute-java-manage-vm/tree/stack/Hybrid)

### <a name="sample-unit-test-project"></a>Exempelprojektet enhet Test 

1.  Klona databasen med hjälp av följande kommando:
    
    `git clone https://github.com/viananth/resources-java-manage-resource-group/tree/stack/Hybrid`

2.  Skapa en Azure-tjänstens huvudnamn och tilldela en roll får åtkomst till prenumerationen. Anvisningar om hur du skapar ett huvudnamn för tjänsten finns i [med Azure PowerShell för att skapa ett huvudnamn för tjänsten med ett certifikat](../azure-stack-create-service-principals.md).

3.  Hämta följande nödvändiga värden:
    
   1.  Klient-ID:t
   2.  Klient-ID
   3.  Klienthemlighet
   4.  Prenumerations-ID:t
   5.  Resource Manager-slutpunkten
   6.  Resursplats

4.  Ange följande miljövariabler med hjälp av informationen som du hämtade från tjänstens huvudnamn du skapat med hjälp av Kommandotolken:
    
   1. Exportera TENANT_ID = {ditt klient-id}
   2. Exportera CLIENT_ID = {din klient-id}
   3. Exportera CLIENT_SECRET = {klienthemlighet}
   4. Exportera SUBSCRIPTION_ID = {ditt prenumerations-id}
   5. Exportera ARM_ENDPOINT = {din Azure Stack Resource manager-URL}
   6. Exportera RESOURCE_LOCATION = {platsen för Azure Stack}

   I Windows, använder **ange** i stället för **exportera**.

5.  Använd den `getactivedirectorysettings` kod för att hämta slutpunkten för arm-metadata och Ställ in information om slutpunkten med hjälp av HTTP-klienten.

   ```java
   public static HashMap<String, String> getActiveDirectorySettings(String armEndpoint) {
   HashMap<String, String> adSettings = new HashMap<String,> String>();

   try {

   // create HTTP Client
   HttpClient httpClient = HttpClientBuilder.create().build();

   // Create new getRequest with below mentioned URL
   HttpGet getRequest = new
   HttpGet(String.format("%s/metadata/endpoints?api-version=1.0", armEndpoint));

   // Add additional header to getRequest which accepts application/xml data
   getRequest.addHeader("accept", "application/xml");

   // Execute request and catch response
   HttpResponse response = httpClient.execute(getRequest);
   ```

7.  Lägg till beroende nedan om du vill använda 2018-03-01-hybrid-profil för Azure Stack i pom.xml-filen. Det här beroendet installerar moduler som är associerade med den här profilen för beräkning, nätverk, lagring, KeyVault och App Services-resursprovidrar.
    
   Observera att du kan använda senaste profil till målet för Azure:
        
   ```xml
   <dependency>
   <groupId>com.microsoft.azure.profile_2018_03_01_hybrid</groupId>
   <artifactId>azure</artifactId>
   <version>1.0.0-beta</version>
   </dependency>
   ```

8.  Ange följande rad i Kommandotolken som öppnades att ställa in miljövariablerna som:
    
   ```shell
   mvn clean compile exec:java
   ```

## <a name="next-steps"></a>Nästa steg

Mer information om API-profiler finns:

- [Hantera API-versionsprofiler i Azure Stack](azure-stack-version-profiles.md)
- [Resource provider API-versioner som stöds av profiler](azure-stack-profiles-azure-resource-manager-versions.md)
