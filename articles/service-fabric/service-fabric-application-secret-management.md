---
title: Hantera Azure Service Fabric application hemligheter | Microsoft Docs
description: Lär dig att skydda hemliga värden i ett Service Fabric-program.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: 94a67e45-7094-4fbd-9c88-51f4fc3c523a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/21/2018
ms.author: vturecek
ms.openlocfilehash: 85eb1cd40986bd6fb83c80a274046bbae3756b7e
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2018
ms.locfileid: "36295461"
---
# <a name="manage-secrets-in-service-fabric-applications"></a>Hantera hemligheter i Service Fabric-program
Den här guiden vägleder dig genom stegen för att hantera hemligheter i ett Service Fabric-program. Hemligheter kan vara känslig information, till exempel storage-anslutningssträngar, lösenord eller andra värden som inte ska hanteras i oformaterad text.

[Azure Key Vault] [ key-vault-get-started] används här som en säker lagringsplats för certifikat och som ett sätt att få certifikat installeras på Service Fabric-kluster i Azure. Om du inte distribuerar till Azure, behöver du inte använda Nyckelvalv för att hantera hemligheter i Service Fabric-program. Dock *med* hemligheter i ett program är molnet plattformsoberoende så att program som ska distribueras till ett kluster som värd var som helst. 

## <a name="obtain-a-data-encipherment-certificate"></a>Skaffa ett certifikat för chiffrering av data
Data chiffrering certifikat används enbart för kryptering och dekryptering av konfiguration av värdena i en tjänst Settings.xml och är inte användas för autentisering eller signering av chiffertext. Certifikatet måste uppfylla följande krav:

* Certifikatet måste innehålla en privat nyckel.
* Certifikatet måste skapas för nyckelutbyte, kan exporteras till en Personal Information Exchange (.pfx)-fil.
* Nyckelanvändning för certifikatet måste innehålla Data chiffrering (10) och får inte innehålla serverautentisering och klientautentisering. 
  
  Till exempel när du skapar ett självsignerat certifikat med hjälp av PowerShell, den `KeyUsage` flagga måste anges till `DataEncipherment`:
  
  ```powershell
  New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject mydataenciphermentcert -Provider 'Microsoft Enhanced Cryptographic Provider v1.0'
  ```

## <a name="install-the-certificate-in-your-cluster"></a>Installera certifikatet i klustret
Det här certifikatet måste installeras på varje nod i klustret. Den används vid körning för att dekryptera värden som lagras i Settings.xml för en tjänst. Se [hur du skapar ett kluster med Azure Resource Manager] [ service-fabric-cluster-creation-via-arm] för instruktioner. 

## <a name="encrypt-application-secrets"></a>Kryptera hemligheter i programmet
När du distribuerar ett program kan kryptera hemliga värden med certifikatet och mata in dem i en tjänst Settings.xml konfigurationsfilen. Fabric-SDK-tjänsten har inbyggda hemliga funktioner för kryptering och dekryptering. Hemliga värden kan krypteras vid byggning och sedan dekryptera och läsa programmässigt i Tjänstkod. 

Följande PowerShell-kommando används för att kryptera en hemlighet. Det här kommandot endast krypterar värde. Det gör **inte** logga chiffertext. Du måste använda samma chiffrering av certifikat som installeras i klustret för att producera chiffertext för hemliga värden:

```powershell
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint "<thumbprint>" -Text "mysecret" -StoreLocation CurrentUser -StoreName My
```

Base64-kodade strängen innehåller både den hemliga ciphertext samt information om det certifikat som användes för att kryptera den.  Base64-kodad sträng kan infogas i en parameter i konfigurationsfilen för din tjänst Settings.xml med den `IsEncrypted` -attributet inställt på `true`:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </Section>
</Settings>
```

### <a name="inject-application-secrets-into-application-instances"></a>Mata in programmet hemligheter i programinstanser
Vi rekommenderar att distributionen till olika miljöer är som automatisk som möjligt. Detta kan åstadkommas genom att utföra hemliga kryptering i en kompileringsmiljö och krypterade hemligheter som parametrar när du skapar instanser av programmet.

#### <a name="use-overridable-parameters-in-settingsxml"></a>Använd åsidosättningsbara parametrar i Settings.xml
Konfigurationsfilen Settings.xml kan åsidosättningsbara parametrar som kan tillhandahållas vid skapandet för programmet. Använd den `MustOverride` attribut i stället för att tillhandahålla ett värde för en parameter:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="" MustOverride="true" />
  </Section>
</Settings>
```

Om du vill åsidosätta värden i Settings.xml deklarera override-parametern för tjänsten i ApplicationManifest.xml:

```xml
<ApplicationManifest ... >
  <Parameters>
    <Parameter Name="MySecret" DefaultValue="" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides>
      <ConfigOverride Name="Config">
        <Settings>
          <Section Name="MySettings">
            <Parameter Name="MySecret" Value="[MySecret]" IsEncrypted="true" />
          </Section>
        </Settings>
      </ConfigOverride>
    </ConfigOverrides>
  </ServiceManifestImport>
 ```

Nu värdet kan anges som en *programmet parametern* när du skapar en instans av programmet. Skapa en instans av programmet kan skriptas med hjälp av PowerShell eller skrivna i C# för enkel integrering i en build-process.

Med PowerShell parametern anges den `New-ServiceFabricApplication` kommandot som en [hash-tabell](https://technet.microsoft.com/library/ee692803.aspx):

```powershell
PS C:\Users\vturecek> New-ServiceFabricApplication -ApplicationName fabric:/MyApp -ApplicationTypeName MyAppType -ApplicationTypeVersion 1.0.0 -ApplicationParameter @{"MySecret" = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM="}
```

Med hjälp av C# programmet parametrar har angetts i en `ApplicationDescription` som en `NameValueCollection`:

```csharp
FabricClient fabricClient = new FabricClient();

NameValueCollection applicationParameters = new NameValueCollection();
applicationParameters["MySecret"] = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=";

ApplicationDescription applicationDescription = new ApplicationDescription(
    applicationName: new Uri("fabric:/MyApp"),
    applicationTypeName: "MyAppType",
    applicationTypeVersion: "1.0.0",
    applicationParameters: applicationParameters)
);

await fabricClient.ApplicationManager.CreateApplicationAsync(applicationDescription);
```

## <a name="decrypt-secrets-from-service-code"></a>Dekryptera hemligheter från Tjänstkod
Du kan läsa krypterade värden utanför Settings.xml genom att dekryptera dem med chiffrering av certifikatet som används för att kryptera hemligheten. Tjänster i Service Fabric körs under NÄTVERKSTJÄNST som standard i Windows och har inte tillgång till certifikat som är installerade på nod utan några extra installationen.

När använder ett certifikat för chiffrering av data, måste du se till att NETWORK SERVICE eller det användarkonto som tjänsten körs under har åtkomst till certifikatets privata nyckel. Service Fabric hanterar åtkomst beviljades för din tjänst automatiskt om du konfigurerar den för att göra det. Den här konfigurationen kan göras i ApplicationManifest.xml genom att definiera användare och säkerhetsprinciper för certifikat. I följande exempel ges kontot NETWORK SERVICE tillgång till läsåtkomst till ett certifikat som definieras av dess tumavtryck:

```xml
<ApplicationManifest … >
    <Principals>
        <Users>
            <User Name="Service1" AccountType="NetworkService" />
        </Users>
    </Principals>
  <Policies>
    <SecurityAccessPolicies>
      <SecurityAccessPolicy GrantRights=”Read” PrincipalRef="Service1" ResourceRef="MyCert" ResourceType="Certificate"/>
    </SecurityAccessPolicies>
  </Policies>
  <Certificates>
    <SecretsCertificate Name="MyCert" X509FindType="FindByThumbprint" X509FindValue="[YourCertThumbrint]"/>
  </Certificates>
</ApplicationManifest>
```

> [!NOTE]
> När du kopierar ett tumavtryck för certifikat från store snapin-modulen i Windows, placeras ett osynligt tecken i början av strängen tumavtryck. Osynliga tecknet kan orsaka ett fel vid försök att hitta ett certifikat med tumavtrycket, så var noga med att ta bort den här extra tecken.
> 
> 

### <a name="use-application-secrets-in-service-code"></a>Använd programmet hemligheter i service-kod
API för åtkomst till konfigurationsvärden från Settings.xml i ett konfigurationspaket kan enkelt dekryptering av värden som har den `IsEncrypted` -attributet inställt på `true`. Eftersom krypterade texten innehåller information om certifikatet som används för kryptering, behöver du inte att hitta certifikatet manuellt. Certifikatet behöver bara installeras på den nod som tjänsten körs på. Anropar den `DecryptValue()` metod för att hämta det ursprungliga hemligt värdet:

```csharp
ConfigurationPackage configPackage = this.Context.CodePackageActivationContext.GetConfigurationPackageObject("Config");
SecureString mySecretValue = configPackage.Settings.Sections["MySettings"].Parameters["MySecret"].DecryptValue()
```

## <a name="next-steps"></a>Nästa steg
Lär dig mer om [program och tjänster](service-fabric-application-and-service-security.md)

<!-- Links -->
[key-vault-get-started]:../key-vault/key-vault-get-started.md
[config-package]: service-fabric-application-and-service-manifests.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md

<!-- Images -->
[overview]:./media/service-fabric-application-secret-management/overview.png
