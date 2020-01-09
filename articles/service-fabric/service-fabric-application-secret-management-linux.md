---
title: Konfigurera ett krypterings certifikat på Linux-kluster
description: Lär dig hur du konfigurerar ett krypterings certifikat och krypterar hemligheter på Linux-kluster.
author: shsha
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: shsha
ms.openlocfilehash: 350718e4ce890fcbfaa7f2b10cc4c47dfac4da90
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2020
ms.locfileid: "75614714"
---
# <a name="set-up-an-encryption-certificate-and-encrypt-secrets-on-linux-clusters"></a>Konfigurera ett krypterings certifikat och kryptera hemligheter på Linux-kluster
Den här artikeln visar hur du konfigurerar ett krypterings certifikat och använder det för att kryptera hemligheter på Linux-kluster. Information om Windows-kluster finns i [Konfigurera ett krypterings certifikat och kryptera hemligheter i Windows-kluster][secret-management-windows-specific-link].

## <a name="obtain-a-data-encipherment-certificate"></a>Hämta ett certifikat för data kryptering
Ett certifikat för datachiffer används enbart för kryptering och dekryptering av [parametrar][parameters-link] i en tjänsts inställningar. xml och [miljövariabler][environment-variables-link] i en tjänsts ServiceManifest. xml. Den används inte för autentisering eller signering av cipher-text. Certifikatet måste uppfylla följande krav:

* Certifikatet måste innehålla en privat nyckel.
* Användningen av certifikat nyckeln måste innehålla datachiffer (10) och ska inte omfatta serverautentisering eller klientautentisering.

  Följande kommandon kan till exempel användas för att generera det begärda certifikatet med OpenSSL:
  
  ```console
  user@linux:~$ openssl req -newkey rsa:2048 -nodes -keyout TestCert.prv -x509 -days 365 -out TestCert.pem
  user@linux:~$ cat TestCert.prv >> TestCert.pem
  ```

## <a name="install-the-certificate-in-your-cluster"></a>Installera certifikatet i klustret
Certifikatet måste installeras på varje nod i klustret under `/var/lib/sfcerts`. Användar kontot som tjänsten körs under (sfuser som standard) **måste ha Läs behörighet** till det installerade certifikatet (det vill säga `/var/lib/sfcerts/TestCert.pem` för det aktuella exemplet).

## <a name="encrypt-secrets"></a>Kryptera hemligheter
Följande kodfragment kan användas för att kryptera en hemlighet. Det här kodfragmentet krypterar bara värdet. krypterings texten signeras **inte** . **Du måste använda** samma krypterings certifikat som är installerat i klustret för att skapa chiffertexten för hemliga värden.

```console
user@linux:$ echo "Hello World!" > plaintext.txt
user@linux:$ iconv -f ASCII -t UTF-16LE plaintext.txt -o plaintext_UTF-16.txt
user@linux:$ openssl smime -encrypt -in plaintext_UTF-16.txt -binary -outform der TestCert.pem | base64 > encrypted.txt
```
Den resulterande Base-64-kodade sträng utmatningen till krypterad. txt innehåller både den hemliga chiffertexten och information om det certifikat som användes för att kryptera det. Du kan verifiera dess giltighet genom att dekryptera den med OpenSSL.
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
