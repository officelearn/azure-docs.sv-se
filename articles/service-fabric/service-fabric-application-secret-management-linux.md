---
title: Konfigurera ett krypteringscert på Linux-kluster
description: Lär dig hur du konfigurerar ett krypteringscertifikat och krypterar hemligheter på Linux-kluster.
author: shsha
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: shsha
ms.openlocfilehash: b8e0a19e3f654fc561e7c7e26c6a2da463e24d5f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969039"
---
# <a name="set-up-an-encryption-certificate-and-encrypt-secrets-on-linux-clusters"></a>Konfigurera ett krypteringscertifikat och kryptera hemligheter på Linux-kluster
Den här artikeln visar hur du konfigurerar ett krypteringscertifikat och använder det för att kryptera hemligheter på Linux-kluster. För Windows-kluster finns i [Konfigurera ett krypteringscertifikat och kryptera hemligheter i Windows-kluster][secret-management-windows-specific-link].

## <a name="obtain-a-data-encipherment-certificate"></a>Hämta ett dataåtkomstcertifikat
Ett datanvisningscertifikat används strikt för kryptering och dekryptering av [parametrar][parameters-link] i en tjänsts Settings.xml- och [miljövariabler][environment-variables-link] i tjänstens ServiceManifest.xml. Den används inte för autentisering eller signering av chiffertext. Intyget skall uppfylla följande krav:

* Certifikatet måste innehålla en privat nyckel.
* Certifikatnyckelanvändningen måste innehålla dataidentientering (10) och bör inte innehålla serverautentisering eller klientautentisering.

  Följande kommandon kan till exempel användas för att generera det certifikat som krävs med OpenSSL:
  
  ```console
  user@linux:~$ openssl req -newkey rsa:2048 -nodes -keyout TestCert.prv -x509 -days 365 -out TestCert.pem
  user@linux:~$ cat TestCert.prv >> TestCert.pem
  ```

## <a name="install-the-certificate-in-your-cluster"></a>Installera certifikatet i klustret
Certifikatet måste vara installerat på varje `/var/lib/sfcerts`nod i klustret under . Användarkontot som tjänsten körs under (sfuser som standard) **ska ha läsbehörighet** till det installerade certifikatet (det vill säga `/var/lib/sfcerts/TestCert.pem` för det aktuella exemplet).

## <a name="encrypt-secrets"></a>Kryptera hemligheter
Följande kodavsnitt kan användas för att kryptera en hemlighet. Det här kodavsnittet krypterar bara värdet. Den signerar **inte** chiffertexten. **Du måste använda** samma informationscertifikat som är installerat i klustret för att skapa chiffertext för hemliga värden.

```console
user@linux:$ echo "Hello World!" > plaintext.txt
user@linux:$ iconv -f ASCII -t UTF-16LE plaintext.txt | tr -d '\n' > plaintext_UTF-16.txt
user@linux:$ openssl smime -encrypt -in plaintext_UTF-16.txt -binary -outform der TestCert.pem | base64 > encrypted.txt
```
Den resulterande bas-64 kodade strängutdata till encrypted.txt innehåller både den hemliga chiffertexten samt information om certifikatet som användes för att kryptera den. Du kan verifiera dess giltighet genom att dekryptera den med OpenSSL.
```console
user@linux:$ cat encrypted.txt | base64 -d | openssl smime -decrypt -inform der -inkey TestCert.prv
```

## <a name="next-steps"></a>Nästa steg
Lär dig hur du [anger krypterade hemligheter i ett program.][secret-management-specify-encrypted-secrets-link]

<!-- Links -->
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-windows-specific-link]: service-fabric-application-secret-management-windows.md
[secret-management-specify-encrypted-secrets-link]: service-fabric-application-secret-management.md#specify-encrypted-secrets-in-an-application
