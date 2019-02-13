---
title: Konfigurera ett krypteringscertifikat och kryptera hemligheter i Azure Service Fabric Windows-kluster | Microsoft Docs
description: Lär dig hur du konfigurerar ett krypteringscertifikat och kryptera hemligheter på Windows-kluster.
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
ms.openlocfilehash: 6aba94aa083127c2300481dbc8e30c224a1813a3
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56109273"
---
# <a name="set-up-an-encryption-certificate-and-encrypt-secrets-on-windows-clusters"></a>Konfigurera ett krypteringscertifikat och kryptera hemligheter på Windows-kluster
Den här artikeln visar hur du konfigurerar ett krypteringscertifikat och användas för att kryptera hemligheter på Windows-kluster. Linux-kluster, se [ange upp ett krypteringscertifikat och kryptera hemligheter i Linux-kluster.][secret-management-linux-specific-link]

[Azure Key Vault] [ key-vault-get-started] används här som en säker lagringsplats för certifikat och som ett sätt att få certifikat installerade på Service Fabric-kluster i Azure. Om du inte distribuerar till Azure, behöver du inte använda Key Vault för att hantera hemligheter i Service Fabric-program. Dock *med* hemligheter i ett program är molnbaserad plattformsagnostiska att tillåta program att distribueras till ett kluster som finns på olika platser. 

## <a name="obtain-a-data-encipherment-certificate"></a>Hämta en chiffreringscertifikatet för data
En data-chiffreringscertifikat bara används för kryptering och dekryptering av [parametrar] [ parameters-link] i en tjänst Settings.xml och [miljövariabler] [ environment-variables-link] i en tjänst ServiceManifest.xml. Det är inte användas för autentisering eller signering av chiffertext. Certifikatet måste uppfylla följande krav:

* Certifikatet måste innehålla en privat nyckel.
* Certifikatet måste skapas för nyckelutbyte, kan exporteras till en Personal Information Exchange (.pfx)-fil.
* Nyckelanvändning för certifikatet måste innehålla Datachiffrering (10) och får inte innehålla serverautentisering eller klientautentisering. 
  
  Till exempel när du skapar ett självsignerat certifikat med hjälp av PowerShell, den `KeyUsage` flaggan anges till `DataEncipherment`:
  
  ```powershell
  New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject mydataenciphermentcert -Provider 'Microsoft Enhanced Cryptographic Provider v1.0'
  ```

## <a name="install-the-certificate-in-your-cluster"></a>Installera certifikatet i ditt kluster
Det här certifikatet måste installeras på varje nod i klustret. Se [hur du skapar ett kluster med Azure Resource Manager] [ service-fabric-cluster-creation-via-arm] anvisningar för installation. 

## <a name="encrypt-application-secrets"></a>Kryptera programhemligheter
Följande PowerShell-kommando används för att kryptera en hemlighet. Det här kommandot endast krypterar värde. den gör **inte** logga chiffertexten. Du måste använda samma chiffreringscertifikatet som installeras i klustret för att producera chiffertext för hemliga värden:

```powershell
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint "<thumbprint>" -Text "mysecret" -StoreLocation CurrentUser -StoreName My
```

Base64-kodade strängen innehåller både den hemliga chiffertext samt information om det certifikat som användes för att kryptera den.

## <a name="next-steps"></a>Nästa steg
Lär dig hur du [ange krypterade hemligheter i ett program.][secret-management-specify-encrypted-secrets-link]

<!-- Links -->
[key-vault-get-started]:../key-vault/key-vault-overview.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-linux-specific-link]: service-fabric-application-secret-management-linux.md
[secret-management-specify-encrypted-secrets-link]: service-fabric-application-secret-management.md#specify-encrypted-secrets-in-an-application
