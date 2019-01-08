---
title: Hantera programhemligheter i Azure Service Fabric | Microsoft Docs
description: Lär dig hur du skyddar hemliga värden i ett Service Fabric-program (plattformsagnostiska).
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
ms.date: 01/04/2019
ms.author: vturecek
ms.openlocfilehash: a0003ee02c09ad8c99d6fa94935f96527c146e7d
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/07/2019
ms.locfileid: "54063819"
---
# <a name="manage-encrypted-secrets-in-service-fabric-applications"></a>Hantera krypterade hemligheter i Service Fabric-program
Den här guiden leder dig genom stegen för att hantera hemligheter i ett Service Fabric-program. Hemligheter kan vara känslig information, till exempel storage-anslutningssträngar, lösenord och andra värden som inte ska hanteras i oformaterad text.

Använda krypterade hemligheter i ett Service Fabric-program omfattar tre steg:
* Ställ in ett krypteringscertifikat och kryptera hemligheter.
* Ange krypterade hemligheter i ett program.
* Dekryptera krypterade hemligheter från koden.

## <a name="set-up-an-encryption-certificate-and-encrypt-secrets"></a>Konfigurera ett krypteringscertifikat och kryptera hemligheter
Konfigurera ett krypteringscertifikat och använder det för att kryptera hemligheter varierar mellan Windows och Linux.
* [Ställ in ett krypteringscertifikat och kryptera hemligheter på Windows-kluster.][secret-management-windows-specific-link]
* [Ställ in ett krypteringscertifikat och kryptera hemligheter i Linux-kluster.][secret-management-linux-specific-link]

## <a name="specify-encrypted-secrets-in-an-application"></a>Ange krypterade hemligheter i ett program
Föregående steg beskriver hur du krypterar en hemlighet med ett certifikat och producerar en Base64-kodad sträng för användning i ett program. Den här Base64-kodad sträng kan anges som ett krypterat [parametern] [ parameters-link] i Settings.xml för en tjänst eller som ett krypterat [miljövariabeln] [ environment-variables-link] i en tjänst ServiceManifest.xml.

Ange ett krypterat [parametern] [ parameters-link] i konfigurationsfilen för din tjänst Settings.xml med den `IsEncrypted` attributet inställt på `true`:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </Section>
</Settings>
```
Ange ett krypterat [miljövariabeln] [ environment-variables-link] i din tjänst servicemanifest.XML med den `Type` attributet inställt på `Encrypted`:
```xml
<CodePackage Name="Code" Version="1.0.0">
  <EnvironmentVariables>
    <EnvironmentVariable Name="MyEnvVariable" Type="Encrypted" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </EnvironmentVariables>
</CodePackage>
```

### <a name="inject-application-secrets-into-application-instances"></a>Mata in programhemligheter i instanser av programmet
Vi rekommenderar att distributionen till olika miljöer är som automatiserade som möjligt. Detta kan åstadkommas genom att utföra hemliga kryptering i en kompileringsmiljö och att tillhandahålla krypterade hemligheter som parametrar när du skapar instanser av programmet.

#### <a name="use-overridable-parameters-in-settingsxml"></a>Använd åsidosättningsbara parametrar i Settings.xml
Konfigurationsfilen Settings.xml kan åsidosättningsbara parametrar som kan anges vid tidpunkten för skapandet av programmet. Använd den `MustOverride` attribut i stället för att tillhandahålla ett värde för en parameter:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="" MustOverride="true" />
  </Section>
</Settings>
```

Om du vill åsidosätta värden i Settings.xml deklarerar du en åsidosättning parameter för tjänsten i ApplicationManifest.xml:

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

Nu värdet kan anges som en *parametr aplikace* när du skapar en instans av programmet. Skapa en programinstans kan skriptas med hjälp av PowerShell, eller skriftliga i C#, för enkel integrering i en build-process.

Med hjälp av PowerShell, parametern anges till den `New-ServiceFabricApplication` kommandot som en [hash-tabell](https://technet.microsoft.com/library/ee692803.aspx):

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/MyApp -ApplicationTypeName MyAppType -ApplicationTypeVersion 1.0.0 -ApplicationParameter @{"MySecret" = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM="}
```

Med hjälp av C#, programparametrar anges i en `ApplicationDescription` som en `NameValueCollection`:

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

## <a name="decrypt-encrypted-secrets-from-service-code"></a>Dekryptera krypterade hemligheter från koden
API: er för att komma åt [parametrar] [ parameters-link] och [miljövariabler] [ environment-variables-link] tillåter enkel dekryptering av krypterade värden. Eftersom krypterad sträng innehåller information om certifikatet som används för kryptering, behöver du inte ange certifikatet manuellt. Certifikatet behöver bara installeras på den nod som tjänsten körs på.

```csharp
// Access decrypted parameters from Settings.xml
ConfigurationPackage configPackage = FabricRuntime.GetActivationContext().GetConfigurationPackageObject("Config");
bool MySecretIsEncrypted = configPackage.Settings.Sections["MySettings"].Parameters["MySecret"].IsEncrypted;
if (MySecretIsEncrypted)
{
    SecureString MySecretDecryptedValue = configPackage.Settings.Sections["MySettings"].Parameters["MySecret"].DecryptValue();
}

// Access decrypted environment variables from ServiceManifest.xml
// Note: you do not have to call any explicit API to decrypt the environment variable.
string MyEnvVariable = Environment.GetEnvironmentVariable("MyEnvVariable");
```

## <a name="next-steps"></a>Nästa steg
Läs mer om [program- och Tjänstsäkerhet](service-fabric-application-and-service-security.md)

<!-- Links -->
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-windows-specific-link]: service-fabric-application-secret-management-windows.md
[secret-management-linux-specific-link]: service-fabric-application-secret-management-linux.md
