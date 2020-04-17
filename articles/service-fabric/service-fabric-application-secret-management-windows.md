---
title: Konfigurera ett krypteringscertifikat i Windows-kluster
description: Lär dig hur du konfigurerar ett krypteringscertifikat och krypterar hemligheter i Windows-kluster.
author: vturecek
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: vturecek
ms.openlocfilehash: d563b338169ab26649b42c73f5fb7ed2fe8c0312
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460196"
---
# <a name="set-up-an-encryption-certificate-and-encrypt-secrets-on-windows-clusters"></a>Konfigurera ett krypteringscertifikat och kryptera hemligheter i Windows-kluster
Den här artikeln visar hur du konfigurerar ett krypteringscertifikat och använder det för att kryptera hemligheter i Windows-kluster. För Linux-kluster finns i [Konfigurera ett krypteringscertifikat och kryptera hemligheter på Linux-kluster.][secret-management-linux-specific-link]

[Azure Key Vault][key-vault-get-started] används här som en säker lagringsplats för certifikat och som ett sätt att få certifikat installerade på Service Fabric-kluster i Azure. Om du inte distribuerar till Azure behöver du inte använda Key Vault för att hantera hemligheter i Service Fabric-program. Men *att använda* hemligheter i ett program är molnplattformsoberoende för att tillåta att program distribueras till ett kluster som finns var som helst. 

## <a name="obtain-a-data-encipherment-certificate"></a>Hämta ett dataåtkomstcertifikat
Ett datanvisningscertifikat används strikt för kryptering och dekryptering av [parametrar][parameters-link] i en tjänsts Settings.xml- och [miljövariabler][environment-variables-link] i tjänstens ServiceManifest.xml. Den används inte för autentisering eller signering av chiffertext. Intyget skall uppfylla följande krav:

* Certifikatet måste innehålla en privat nyckel.
* Certifikatet måste skapas för nyckelutbyte, som kan exporteras till en fil för personligt informationsutbyte (.pfx).
* Certifikatnyckelanvändningen måste innehålla dataidentientering (10) och bör inte innehålla serverautentisering eller klientautentisering. 
  
  När du till exempel skapar ett självsignerat `KeyUsage` certifikat med `DataEncipherment`PowerShell måste flaggan anges till :
  
  ```powershell
  New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject mydataenciphermentcert -Provider 'Microsoft Enhanced Cryptographic Provider v1.0'
  ```

## <a name="install-the-certificate-in-your-cluster"></a>Installera certifikatet i klustret
Certifikatet måste vara installerat på varje nod i klustret. Se [hur du skapar ett kluster med Hjälp av Azure Resource Manager][service-fabric-cluster-creation-via-arm] för installationsinstruktioner. 

## <a name="encrypt-application-secrets"></a>Kryptera programhemligheter
Följande PowerShell-kommando används för att kryptera en hemlighet. Det här kommandot krypterar bara värdet. Den signerar **inte** chiffertexten. Du måste använda samma ett krypteringscertifikat som är installerat i klustret för att skapa chiffertext för hemliga värden:

```powershell
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint "<thumbprint>" -Text "mysecret" -StoreLocation CurrentUser -StoreName My
```

Den resulterande bas-64-kodade strängen innehåller både den hemliga chiffertexten samt information om certifikatet som användes för att kryptera den.

## <a name="next-steps"></a>Nästa steg
Lär dig hur du [anger krypterade hemligheter i ett program.][secret-management-specify-encrypted-secrets-link]

<!-- Links -->
[key-vault-get-started]:../key-vault/general/overview.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-linux-specific-link]: service-fabric-application-secret-management-linux.md
[secret-management-specify-encrypted-secrets-link]: service-fabric-application-secret-management.md#specify-encrypted-secrets-in-an-application
