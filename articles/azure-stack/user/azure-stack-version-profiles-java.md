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
ms.date: 04/02/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 09/28/2018
ms.openlocfilehash: 0a2a42860ad4487f470aea9c4d2be8eba1fbe8ab
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2019
ms.locfileid: "58802855"
---
# <a name="use-api-version-profiles-with-java-in-azure-stack"></a>Använd API-versionsprofiler med Java i Azure Stack

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

Java SDK för Azure Stack Resource Manager innehåller verktyg som hjälper dig att skapa och hantera infrastrukturen. Resursprovidrar i SDK innehåller beräkning, nätverk, lagring, app services och [KeyVault](../../key-vault/key-vault-whatis.md). Java SDK innehåller API-profiler genom att inkludera beroenden i Pom.xml-filen som läser in de rätta modulerna i den .java-fil. Men du kan lägga till flera profiler som beroenden, till exempel den **2018-03-01-hybrid**, eller **senaste**, som Azure-profil. Med hjälp av dessa beroenden läses in rätt modul så att när du skapar din resurstyp, du kan välja vilka API-versionen från profilerna som du vill använda. På så sätt kan du använda de senaste versionerna i Azure, när du utvecklar mot de senaste API-versionerna för Azure Stack. Med hjälp av Java SDK kan en verklig utvecklarupplevelse för molnet. API-profiler i Java SDK aktivera hybrid molnutveckling genom att hjälpa dig att växla mellan globala Azure-resurser och resurser i Azure Stack.

## <a name="java-and-api-version-profiles"></a>Java- och API-versionsprofiler

En API-profil är en kombination av resursprovidrar och API-versioner. Du kan använda en API-profil för att hämta den senaste och mest stabila versionen av varje resurstyp i ett paket för resource provider.

- Om du vill använda de senaste versionerna av alla tjänster, använda den **senaste** profilen beroendet.

  - Om du vill använda den senaste profilen beroendet är **com.microsoft.azure**.

  - Om du vill använda tjänsterna som är kompatibla med Azure Stack, använda den **com.microsoft.azure.profile\_2018\_03\_01\_hybrid** profil.

    - Det här är anges i Pom.xml-filen som ett beroende som läser in moduler automatiskt om du väljer rätt klassen från den nedrullningsbara listan precis som med .NET.

    - Upp i varje modul visas på följande sätt:      `Import com.microsoft.azure.management.resources.v2018_03_01.ResourceGroup`

  - Beroenden visas på följande sätt:

     ```xml
     <dependency>
     <groupId>com.microsoft.azure.profile_2018_03_01_hybrid</groupId>
     <artifactId>azure</artifactId>
     <version>1.0.0-beta</version>
     </dependency>
     ```

  - Om du vill använda specifika API-versioner för en resurstyp i en viss resursprovider, använder du de specifika API-versioner som definieras via Intellisense.

Observera att du kan kombinera alla alternativ i samma program.

## <a name="install-the-azure-java-sdk"></a>Installera Azure Java SDK

Använd följande steg för att installera Java SDK:

1. Följ officiella anvisningarna för att installera Git. Anvisningar finns i [komma igång - installerar Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).

2. Följ anvisningarna för att installera den [Java SDK](https://zulu.org/download/) och [Maven](https://maven.apache.org/). Rätt version är version 8 av Java Developer Kit. Rätt Apache Maven är version 3.0 eller senare. Miljövariabeln JAVA_HOME måste anges till installationsplatsen för Java Development Kit för att slutföra snabbstarten. Mer information finns i [skapa din första funktion med Java och Maven](../../azure-functions/functions-create-first-java-maven.md).

3. Installera rätt beroendepaketen genom att öppna filen Pom.xml i ditt Java-program. Lägg till ett beroende som visas i följande kod:

   ```xml  
   <dependency>
   <groupId>com.microsoft.azure.profile_2018_03_01_hybrid</groupId>
   <artifactId>azure</artifactId>
   <version>1.0.0-beta</version>
   </dependency>
   ```

4. Uppsättningen med paket som måste installeras beror på vilken profil-version som du vill använda. Paketnamn för profilversioner är:

   - **com.microsoft.azure.profile\_2018\_03\_01\_hybrid**
   - **com.microsoft.azure**
     - **latest**

5. Om det inte finns skapar du en prenumeration och spara prenumerations-ID för senare användning. Anvisningar om hur du skapar en prenumeration finns i [skapa prenumerationer för erbjudanden i Azure Stack](../azure-stack-subscribe-plan-provision-vm.md).

6. Skapa ett huvudnamn för tjänsten och spara klienten-ID och klienthemlighet. Anvisningar om hur du skapar ett huvudnamn för tjänsten för Azure Stack finns i [ge program åtkomst till Azure Stack](../azure-stack-create-service-principals.md). Observera att klient-ID är även känd som program-ID när du skapar ett huvudnamn för tjänsten.

7. Kontrollera att tjänstens huvudnamn har rollen deltagare/ägare av prenumerationen. Anvisningar om hur du tilldelar en roll till tjänstens huvudnamn finns i [ge program åtkomst till Azure Stack](../azure-stack-create-service-principals.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda Azure Java SDK med Azure Stack, måste du ange följande värden och ange sedan värden med miljövariabler. Se anvisningarna efter tabellen för ditt operativsystem för att ställa in miljövariabler.

| Värde                     | Miljövariabler | Beskrivning                                                                                                                                                                                                          |
| ------------------------- | --------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Klient-ID:t                 | AZURE_TENANT_ID            | Värdet för Azure Stack [klient-ID](../azure-stack-identity-overview.md).                                                          |
| Klientorganisations-ID                 | AZURE_CLIENT_ID             | Tjänsten huvudnamn program-ID sparas när tjänstens huvudnamn skapades i föregående avsnitt.                                                                                              |
| Prenumerations-ID:t           | AZURE_SUBSCRIPTION_ID      | Den [prenumerations-ID](../azure-stack-plan-offer-quota-overview.md#subscriptions) är hur du kommer åt erbjudanden i Azure Stack.                |
| Klienthemlighet             | AZURE_CLIENT_SECRET        | Tjänstens huvudnamn programhemlighet sparas när tjänstens huvudnamn har skapats.                                                                                                                                   |
| Resource Manager-slutpunkten | ARM_ENDPOINT              | Se [Azure Stack Resource Manager-slutpunkten](../user/azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint). |
| Plats                  | RESOURCE_LOCATION    | **Lokala** för Azure Stack.                                                                                                                                                                                                |

Klient-ID för Azure Stack finns i instruktionerna [här](../azure-stack-csp-ref-operations.md). Ange din miljövariabler genom att göra följande:

### <a name="microsoft-windows"></a>Microsoft Windows

Om du vill ange miljövariabler i en kommandotolk i Windows, använder du följande format:

```shell
Set AZURE_TENANT_ID=<Your_Tenant_ID>
```

### <a name="macos-linux-and-unix-based-systems"></a>macOS, Linux och Unix-baserade system

I Unix-baserat system, använder du följande kommando:

```shell
Export AZURE_TENANT_ID=<Your_Tenant_ID>
```

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Lita på Azure Stack Certifikatutfärdarens rotcertifikat

Om du använder ASDK, måste du lita Certifikatutfärdarens rotcertifikat på din fjärrdatorn. Du behöver inte göra detta med integrerade system.

#### <a name="windows"></a>Windows

1. Exportera det självsignerade certifikatet för Azure Stack på skrivbordet.

1. I en kommandotolk ändrar du katalogen till % JAVA_HOME%\bin.

1. Kör följande kommando:

   ```shell
   .\keytool.exe -importcert -noprompt -file <location of the exported certificate here> -alias root -keystore %JAVA_HOME%\lib\security\cacerts -trustcacerts -storepass changeit
   ```

### <a name="the-azure-stack-resource-manager-endpoint"></a>Azure Stack Resource Manager-slutpunkten

Microsoft Azure Resource Manager är en management-ramverk som gör att administratörer kan distribuera, hantera och övervaka Azure-resurser. Azure Resource Manager kan hantera dessa uppgifter som en grupp i stället, i en enda åtgärd.

Du kan hämta metadata-information från Resource Manager-slutpunkten. Slutpunkten som returnerar en JSON-fil med den information som krävs för att köra din kod.

Observera följande överväganden:

- Den **ResourceManagerUrl** i Azure Stack Development Kit (ASDK) är: https://management.local.azurestack.external/.

- Den **ResourceManagerUrl** integrerade system är: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/`.

Att hämta de metadata som krävs: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`.

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

- **com.microsoft.azure.profile\_2018\_03\_01\_hybrid**: Senaste profil har skapats för Azure Stack. Använd den här profilen för tjänster som är mest kompatibla med Azure Stack så länge du är på 1808 stämpel eller ytterligare.

- **com.microsoft.azure**: Profil som består av de senaste versionerna av alla tjänster. Använd de senaste versionerna av alla tjänster.

Mer information om Azure Stack och API-profiler finns i den [sammanfattning av API-profiler](../user/azure-stack-version-profiles.md#summary-of-api-profiles).

## <a name="azure-java-sdk-api-profile-usage"></a>Användningen av Azure Java SDK API-profil

Följande kod autentiserar tjänstens huvudnamn i Azure Stack. Den skapar en token med hjälp av klient-ID och grundläggande autentisering, som är specifika för Azure Stack:

```java
AzureTokenCredentials credentials = new ApplicationTokenCredentials(client, tenant, key, AZURE_STACK)
                    .withDefaultSubscriptionID(subscriptionID);
Azure azureStack = Azure.configure()
                    .withLogLevel(com.microsoft.rest.LogLevel.BASIC)
                    .authenticate(credentials, credentials.defaultSubscriptionID());
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
                    put("activeDirectoryResourceID", settings.get("audience"));
                    put("activeDirectoryGraphResourceID", settings.get("graphEndpoint"));
                    put("storageEndpointSuffix", armEndpoint.substring(armEndpoint.indexOf('.')));
                    put("keyVaultDnsSuffix", ".vault" + armEndpoint.substring(armEndpoint.indexOf('.')));
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

// Add additional header to getRequest which accepts application/xml data
getRequest.addHeader("accept", "application/xml");

// Execute request and catch response
HttpResponse response = httpClient.execute(getRequest);
```

## <a name="samples-using-api-profiles"></a>Exempel med hjälp av API-profiler

Du kan använda följande GitHub-exempel som referens för att skapa lösningar med .NET och Azure Stack-API-profiler:

- [Hantera resursgrupper](https://github.com/Azure-Samples/Hybrid-resources-java-manage-resource-group)

- [Hantera Lagringskonton](https://github.com/Azure-Samples/hybrid-storage-java-manage-storage-accounts)

- [Hantera en virtuell dator](https://github.com/Azure-Samples/hybrid-compute-java-manage-vm)

### <a name="sample-unit-test-project"></a>Exempelprojektet enhet Test

1. Klona databasen med hjälp av följande kommando:

   `git clone https://github.com/Azure-Samples/Hybrid-resources-java-manage-resource-group.git`

2. Skapa en Azure-tjänstens huvudnamn och tilldela en roll får åtkomst till prenumerationen. Anvisningar om hur du skapar ett huvudnamn för tjänsten finns i [med Azure PowerShell för att skapa ett huvudnamn för tjänsten med ett certifikat](../azure-stack-create-service-principals.md).

3. Hämta de följande obligatoriska värdena för miljövariabler:

   - AZURE_TENANT_ID
   - AZURE_CLIENT_ID
   - AZURE_CLIENT_SECRET
   - AZURE_SUBSCRIPTION_ID
   - ARM_ENDPOINT
   - RESOURCE_LOCATION

4. Ange följande miljövariabler med hjälp av informationen som du hämtade från tjänstens huvudnamn du skapat med hjälp av Kommandotolken:

   - Exportera AZURE_TENANT_ID = {ditt klient-ID}
   - Exportera AZURE_CLIENT_ID = {din klient-ID}
   - Exportera AZURE_CLIENT_SECRET = {klienthemlighet}
   - Exportera AZURE_SUBSCRIPTION_ID = {ditt prenumerations-ID}
   - Exportera ARM_ENDPOINT = {Azure Stack Resource Manager URL}
   - Exportera RESOURCE_LOCATION = {platsen för Azure Stack}

   I Windows, använder **ange** i stället för **exportera**.

5. Använd den `getactivedirectorysettings` kod för att hämta slutpunkten för arm-metadata och Ställ in information om slutpunkten med hjälp av HTTP-klienten.

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

6. I Pom.xml-filen lägger du till följande beroende till att använda den **2018-03-01-hybrid** profil för Azure Stack. Det här beroendet installeras de moduler som är associerade med den här profilen för resursprovidrar beräkning, nätverk, lagring, KeyVault och App Services:

   ```xml
   <dependency>
   <groupId>com.microsoft.azure.profile_2018_03_01_hybrid</groupId>
   <artifactId>azure</artifactId>
   <vers1s.0.0-beta</version>
   </dependency>
   ```

7. Ange följande kommando i Kommandotolken som öppnades att ställa in miljövariablerna som:

   ```shell
   mvn clean compile exec:java
   ```

## <a name="next-steps"></a>Nästa steg

Mer information om API-profiler finns:

- Versionsprofiler i Azure Stack](azure-stack-version-profiles.md)
- [Resource provider API-versioner som stöds av profiler](azure-stack-profiles-azure-resource-manager-versions.md)
