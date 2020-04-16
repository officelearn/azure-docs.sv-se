---
title: Hantera programhemligheter för Azure Service Fabric
description: Lär dig hur du skyddar hemliga värden i ett Service Fabric-program (plattformsoberoende).
author: vturecek
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: vturecek
ms.openlocfilehash: 4d2138935122b9e08b21963519fce3f72466ab1f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414516"
---
# <a name="manage-encrypted-secrets-in-service-fabric-applications"></a>Hantera krypterade hemligheter i Service Fabric-program
Den här guiden går igenom stegen för att hantera hemligheter i ett Service Fabric-program. Hemligheter kan vara känslig information, till exempel lagringsanslutningssträngar, lösenord eller andra värden som inte ska hanteras i oformaterad text.

Att använda krypterade hemligheter i ett Service Fabric-program innebär tre steg:
* Konfigurera ett krypteringscertifikat och kryptera hemligheter.
* Ange krypterade hemligheter i ett program.
* Dekryptera krypterade hemligheter från servicekod.

## <a name="set-up-an-encryption-certificate-and-encrypt-secrets"></a>Konfigurera ett krypteringscertifikat och kryptera hemligheter
Att konfigurera ett krypteringscertifikat och använda det för att kryptera hemligheter varierar mellan Windows och Linux.
* [Konfigurera ett krypteringscertifikat och kryptera hemligheter i Windows-kluster.][secret-management-windows-specific-link]
* [Konfigurera ett krypteringscertifikat och kryptera hemligheter på Linux-kluster.][secret-management-linux-specific-link]

## <a name="specify-encrypted-secrets-in-an-application"></a>Ange krypterade hemligheter i ett program
I föregående steg beskrivs hur du krypterar en hemlighet med ett certifikat och skapar en bas-64-kodad sträng för användning i ett program. Den här bas-64-kodade strängen kan anges som en krypterad [parameter][parameters-link] i en tjänsts Settings.xml eller som en krypterad [miljövariabel][environment-variables-link] i tjänstens ServiceManifest.xml.

Ange en krypterad [parameter][parameters-link] i tjänstens konfigurationsfil `IsEncrypted` Settings.xml med attributet inställt `true`på :

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </Section>
</Settings>
```
Ange en krypterad [miljövariabel][environment-variables-link] i tjänstens ServiceManifest.xml-fil med `Type` attributet inställt `Encrypted`på :
```xml
<CodePackage Name="Code" Version="1.0.0">
  <EnvironmentVariables>
    <EnvironmentVariable Name="MyEnvVariable" Type="Encrypted" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </EnvironmentVariables>
</CodePackage>
```

Hemligheterna bör också inkluderas i ditt Service Fabric-program genom att ange ett certifikat i ansökningsmanifestet. Lägg till ett **SecretsCertificate-element** i **ApplicationManifest.xml** och inkludera önskat certifikatets tumavtryck.

```xml
<ApplicationManifest … >
  ...
  <Certificates>
    <SecretsCertificate Name="MyCert" X509FindType="FindByThumbprint" X509FindValue="[YourCertThumbrint]"/>
  </Certificates>
</ApplicationManifest>
```
> [!NOTE]
> När du aktiverar ett program som anger ett SecretsCertificate hittar Service Fabric det matchande certifikatet och beviljar den identitet som programmet körs under fullständiga behörigheter till certifikatets privata nyckel. Service Fabric övervakar också certifikatet för ändringar och tillämpar behörigheterna på nytt i enlighet med detta. Om du vill identifiera ändringar för certifikat som deklarerats med gemensamt namn kör Service Fabric en periodisk uppgift som hittar alla matchande certifikat och jämför dem med en cachelagrad lista med tumavtryck. När ett nytt tumavtryck identifieras betyder det att ett certifikat av ämnet har förnyats. Aktiviteten körs en gång per minut på varje nod i klustret.
>
> Medan SecretsCertificate tillåter ämnesbaserade deklarationer, observera att de krypterade inställningarna är kopplade till nyckelparet som användes för att kryptera inställningen på klienten. Du måste se till att det ursprungliga krypteringscertifikatet (eller motsvarande) matchar den ämnesbaserade deklarationen och att det installeras, inklusive motsvarande privat nyckel, på varje nod i klustret som kan vara värd för programmet. Alla tids giltiga certifikat som matchar den ämnesbaserade deklarationen och som byggts från samma nyckelpar som det ursprungliga krypteringscertifikatet betraktas som motsvarigheter.
>

### <a name="inject-application-secrets-into-application-instances"></a>Injicera programhemligheter i programinstanser
Helst bör distributionen till olika miljöer vara så automatiserad som möjligt. Detta kan åstadkommas genom att utföra hemlig kryptering i en byggmiljö och tillhandahålla krypterade hemligheter som parametrar när du skapar programinstanser.

#### <a name="use-overridable-parameters-in-settingsxml"></a>Använda åsidosättbara parametrar i Settings.xml
Konfigurationsfilen Settings.xml tillåter tvingande parametrar som kan tillhandahållas vid skapande av program. Använd `MustOverride` attributet i stället för att ange ett värde för en parameter:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="" MustOverride="true" />
  </Section>
</Settings>
```

Om du vill åsidosätta värden i Settings.xml deklarerar du en åsidosättningsparameter för tjänsten i ApplicationManifest.xml:

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

Nu kan värdet anges som en *programparameter* när du skapar en instans av programmet. Skapa en programinstans kan skriptas med PowerShell, eller skrivas i C#, för enkel integrering i en byggprocess.

Med PowerShell anges parametern som `New-ServiceFabricApplication` en [hash-tabell:](https://technet.microsoft.com/library/ee692803.aspx)

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/MyApp -ApplicationTypeName MyAppType -ApplicationTypeVersion 1.0.0 -ApplicationParameter @{"MySecret" = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM="}
```

Med C#anges programparametrar `ApplicationDescription` i `NameValueCollection`en som en:

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

## <a name="decrypt-encrypted-secrets-from-service-code"></a>Dekryptera krypterade hemligheter från tjänstkod
API:erna för åtkomst till [parametrar][parameters-link] och [miljövariabler][environment-variables-link] möjliggör enkel dekryptering av krypterade värden. Eftersom den krypterade strängen innehåller information om certifikatet som används för kryptering behöver du inte ange certifikatet manuellt. Certifikatet behöver bara installeras på noden som tjänsten körs på.

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
* Service Fabric [Secrets Store](service-fabric-application-secret-store.md) 
* Läs mer om [program- och tjänstsäkerhet](service-fabric-application-and-service-security.md)

<!-- Links -->
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-windows-specific-link]: service-fabric-application-secret-management-windows.md
[secret-management-linux-specific-link]: service-fabric-application-secret-management-linux.md
[service fabric secrets store]: service-fabric-application-secret-store.md