---
title: Importera certifikat till en behållare som körs på Azure Service Fabric | Microsoft Docs
description: Lär dig att importera certifikatfiler till en tjänst för Service Fabric-behållaren.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: f234a6f6ca56d1833aac53f490feb5f667a6bf1b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="import-a-certificate-file-into-a-container-running-on-service-fabric"></a>Importera en certifikatfil till en behållare som körs på Service Fabric

Du kan skydda dina behållartjänster genom att ange ett certifikat. Service Fabric är en mekanism för tjänster i en behållare för att komma åt ett certifikat som är installerad på noder i ett Windows- eller Linux-kluster (version 5.7 eller högre). Certifikatet måste installeras i LocalMachine på alla noder i klustret. Certifikatinformationen som har angetts i applikationsmanifestet under den `ContainerHostPolicies` tagg som i följande fragment visas:

```xml
  <ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
    <CertificateRef Name="MyCert1" X509StoreName="My" X509FindValue="[Thumbprint1]"/>
    <CertificateRef Name="MyCert2" X509FindValue="[Thumbprint2]"/>
 ```

För Windows-kluster, när programmet startas, körningsmiljön läser certifikat och genererar en PFX-filen och lösenordet för varje certifikat. Det här PFX-filen och lösenordet är tillgängliga i behållaren med hjälp av följande miljövariabler: 

* Certificates_ServicePackageName_CodePackageName_CertName_PFX
* Certificates_ServicePackageName_CodePackageName_CertName_Password

För Linux-kluster kopieras certifikat (PEM) över från arkivet som anges av X509StoreName till behållaren. Motsvarande miljövariabler i Linux är:

* Certificates_ServicePackageName_CodePackageName_CertName_PEM
* Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey

Om du redan har certifikat i formuläret och vill komma åt i behållaren du också skapa ett datapaket i app-paket och ange följande i programmanifestet:

```xml
<ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
  <CertificateRef Name="MyCert1" DataPackageRef="[DataPackageName]" DataPackageVersion="[Version]" RelativePath="[Relative Path to certificate inside DataPackage]" Password="[password]" IsPasswordEncrypted="[true/false]"/>
 ```

Behållartjänsten eller process ansvarar för importerar certifikatfilerna i behållaren. Du kan använda för att importera certifikatet `setupentrypoint.sh` skript eller köra anpassad kod i behållaren-processen. Här är exempelkod i C# för att importera PFX-filen:

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
Den här PFX-certifikat kan användas för att autentisera program eller tjänst eller säker kommunikation med andra tjänster. Som standard är filerna ACLed endast till systemet. Du kan ACL det till andra konton som krävs av tjänsten.

Läs följande artiklar som ett nästa steg:

* [Distribuera en Windows-behållare till Service Fabric på Windows Server 2016](service-fabric-get-started-containers.md)
* [Distribuera en dockerbehållare till Service Fabric på Linux](service-fabric-get-started-containers-linux.md)
