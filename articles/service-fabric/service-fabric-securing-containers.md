---
title: Importera certifikat till en behållare
description: Lär dig nu att importera certifikatfiler till en Service Fabric-behållartjänst.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: da4babd8f9d1a25a8514d0c6f1526b43a9723854
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614119"
---
# <a name="import-a-certificate-file-into-a-container-running-on-service-fabric"></a>Importera en certifikatfil till en behållare som körs på Service Fabric

Du kan skydda behållartjänsterna genom att ange ett certifikat. Service Fabric tillhandahåller en mekanism för tjänster i en behållare för åtkomst till ett certifikat som är installerat på noderna i ett Windows- eller Linux-kluster (version 5.7 eller senare). Certifikatet måste vara installerat i ett certifikatarkiv under LocalMachine på alla noder i klustret. Den privata nyckel som motsvarar certifikatet måste vara tillgänglig, tillgänglig och - på Windows - kan exporteras. Certifikatinformationen finns i programmanifestet under taggen `ContainerHostPolicies` som följande utdrag visar:

```xml
  <ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
    <CertificateRef Name="MyCert1" X509StoreName="My" X509FindValue="[Thumbprint1]"/>
    <CertificateRef Name="MyCert2" X509FindValue="[Thumbprint2]"/>
 ```

När programmet startas exporterar runtime varje refererat certifikat och motsvarande privat nyckel till en PFX-fil, skyddad med ett slumpmässigt genererat lösenord när programmet startas. PFX- respektive lösenordsfilerna är tillgängliga i behållaren med hjälp av följande miljövariabler: 

* Certificates_ServicePackageName_CodePackageName_CertName_PFX
* Certificates_ServicePackageName_CodePackageName_CertName_Password

För Linux-kluster kopieras certifikaten (PEM) över från det arkiv som anges av X509StoreName på behållaren. Motsvarande miljövariabler på Linux är:

* Certificates_ServicePackageName_CodePackageName_CertName_PEM
* Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey

Om du redan har certifikaten i formuläret som krävs och vill komma åt dem i behållaren kan du också skapa ett datapaket i apppaketet och ange följande i programmanifestet:

```xml
<ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
  <CertificateRef Name="MyCert1" DataPackageRef="[DataPackageName]" DataPackageVersion="[Version]" RelativePath="[Relative Path to certificate inside DataPackage]" Password="[password]" IsPasswordEncrypted="[true/false]"/>
 ```

Behållartjänsten eller processen ansvarar för att importera certifikatfilerna till behållaren. Om du vill importera `setupentrypoint.sh` certifikatet kan du använda skript eller köra anpassad kod i behållarprocessen. Här är exempelkod i C# för import av PFX-filen:

```csharp
string certificateFilePath = Environment.GetEnvironmentVariable("Certificates_MyServicePackage_NodeContainerService.Code_MyCert1_PFX");
string passwordFilePath = Environment.GetEnvironmentVariable("Certificates_MyServicePackage_NodeContainerService.Code_MyCert1_Password");
X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
string password = File.ReadAllLines(passwordFilePath, Encoding.Default)[0];
password = password.Replace("\0", string.Empty);
X509Certificate2 cert = new X509Certificate2(certificateFilePath, password, X509KeyStorageFlags.MachineKeySet | X509KeyStorageFlags.PersistKeySet);
store.Open(OpenFlags.ReadWrite);
store.Add(cert);
store.Close();
```
Detta PFX-certifikat kan användas för att autentisera programmet eller tjänsten eller säker kommunikation med andra tjänster. Som standard är filerna endast acled till SYSTEM. Du kan ACL det till andra konton som krävs av tjänsten.

Som ett nästa steg läser du följande artiklar:

* [Distribuera en Windows-behållare till Service Fabric på Windows Server 2016](service-fabric-get-started-containers.md)
* [Distribuera en Docker-behållare till Service Fabric på Linux](service-fabric-get-started-containers-linux.md)
