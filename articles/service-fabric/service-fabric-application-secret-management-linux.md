---
title: Konfigurera ett krypteringscertifikat och kryptera hemligheter i Azure Service Fabric Linux-kluster | Microsoft Docs
description: Lär dig hur du konfigurerar ett krypteringscertifikat och kryptera hemligheter i Linux-kluster.
services: service-fabric
documentationcenter: .net
author: shsha
manager: ''
editor: ''
ms.assetid: 94a67e45-7094-4fbd-9c88-51f4fc3c523a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/04/2019
ms.author: shsha
ms.openlocfilehash: 9589d6ea69a2293d592a9e63f2b726f1a620bb9e
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/07/2019
ms.locfileid: "54068979"
---
# <a name="set-up-an-encryption-certificate-and-encrypt-secrets-on-linux-clusters"></a>Konfigurera ett krypteringscertifikat och kryptera hemligheter i Linux-kluster
Den här artikeln visar hur du konfigurerar ett krypteringscertifikat och användas för att kryptera hemligheter i Linux-kluster. Windows-kluster, se [ange upp ett krypteringscertifikat och kryptera hemligheter på Windows-kluster][secret-management-windows-specific-link].

## <a name="obtain-a-data-encipherment-certificate"></a>Hämta en chiffreringscertifikatet för data
En data-chiffreringscertifikat bara används för kryptering och dekryptering av [parametrar] [ parameters-link] i en tjänst Settings.xml och [miljövariabler] [ environment-variables-link] i en tjänst ServiceManifest.xml. Det är inte användas för autentisering eller signering av chiffertext. Certifikatet måste uppfylla följande krav:

* Certifikatet måste innehålla en privat nyckel.
* Nyckelanvändning för certifikatet måste innehålla Datachiffrering (10) och får inte innehålla serverautentisering eller klientautentisering.

  Följande kommandon kan till exempel användas för att generera certifikatet som krävs med hjälp av OpenSSL:
  
  ```console
  user@linux:~$ openssl req -newkey rsa:2048 -nodes -keyout TestCert.prv -x509 -days 365 -out TestCert.pem
  user@linux:~$ cat TestCert.prv >> TestCert.pem
  ```

## <a name="install-the-certificate-in-your-cluster"></a>Installera certifikatet i ditt kluster
Certifikatet måste installeras på varje nod i klustret under `/var/lib/sfcerts`. Användarkontot som tjänsten körs under (sfuser som standard) **ska ha läsåtkomst** till det installerade certifikatet (det vill säga `/var/lib/sfcerts/TestCert.pem` för det aktuella exemplet).

## <a name="encrypt-secrets"></a>Kryptera hemligheter
Följande kodavsnitt kan användas för att kryptera en hemlighet. Det här kodfragmentet endast krypterar värde. den gör **inte** logga chiffertexten. **Du måste använda** samma chiffreringscertifikatet som installeras i klustret för att producera chiffertext för hemliga värden.

```console
user@linux:$ echo "Hello World!" > plaintext.txt
user@linux:$ iconv -f ASCII -t UTF-16LE plaintext.txt -o plaintext_UTF-16.txt
user@linux:$ openssl smime -encrypt -in plaintext_UTF-16.txt -binary -outform der TestCert.pem | base64 > encrypted.txt
```
Base64-kodad sträng resultatet till encrypted.txt innehåller både den hemliga chiffertext samt information om det certifikat som användes för att kryptera den. Du kan verifiera dess giltighet genom att dekryptera med OpenSSL.
```console
user@linux:$ cat encrypted.txt | base64 -d | openssl smime -decrypt -inform der -inkey TestCert.prv
```

## <a name="next-steps"></a>Nästa steg
Lär dig hur du [ange krypterade hemligheter i ett program.][secret-management-specify-encrypted-secrets-link]

<!-- Links -->
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-windows-specific-link]: service-fabric-application-secret-management-windows.md
[secret-management-specify-encrypted-secrets-link]: service-fabric-application-secret-management.md#specify-encrypted-secrets-in-an-application
