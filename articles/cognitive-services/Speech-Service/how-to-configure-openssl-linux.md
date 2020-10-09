---
title: Så här konfigurerar du OpenSSL för Linux
titleSuffix: Azure Cognitive Services
description: Lär dig hur du konfigurerar OpenSSL för Linux.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: jhakulin
ms.openlocfilehash: 42960c25c4124203b64646fdc5cbca833b246e21
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "81683169"
---
# <a name="configure-openssl-for-linux"></a>Konfigurera OpenSSL för Linux

När du använder en Speech SDK-version innan 1.9.0, konfigureras [openssl](https://www.openssl.org) dynamiskt till värd system versionen. I senare versioner av talet SDK är OpenSSL (version [1.1.1 b](https://mta.openssl.org/pipermail/openssl-announce/2019-February/000147.html)) statiskt länkat till kärn biblioteket i tal-SDK: n.

Kontrol lera att OpenSSL-certifikat har installerats i systemet för att säkerställa anslutning. Kör ett kommando:
```bash
openssl version -d
```

Utdata på Ubuntu/Debian-baserade system bör vara:
```
OPENSSLDIR: "/usr/lib/ssl"
```

Kontrol lera om det finns en `certs` under katalog under OPENSSLDIR. I exemplet ovan skulle det vara `/usr/lib/ssl/certs` .

* Om det finns `/usr/lib/ssl/certs` och innehåller många enskilda certifikatfiler (med `.crt` eller `.pem` tillägg) behöver du inte längre utföra några ytterligare åtgärder.

* Om OPENSSLDIR är något annat än `/usr/lib/ssl` och/eller om det finns en enda certifikat paket fil i stället för flera enskilda filer, måste du ange en lämplig SSL-miljövariabel för att indikera var certifikaten finns.

## <a name="examples"></a>Exempel

- OPENSSLDIR är `/opt/ssl` . Det finns en `certs` under katalog med många `.crt` eller- `.pem` filer.
Ställ in miljövariabeln på `SSL_CERT_DIR` att peka på `/opt/ssl/certs` innan ett program som använder tal-SDK: n körs. Exempel:
```bash
export SSL_CERT_DIR=/opt/ssl/certs
```

- OPENSSLDIR är `/etc/pki/tls` (t. ex. på RHEL/CentOS-baserade system). Det finns `certs` till exempel en under katalog med en certifikat paket fil `ca-bundle.crt` .
Ange en miljö variabel `SSL_CERT_FILE` för att peka på den filen innan du kör ett program som använder tal-SDK: n. Exempel:
```bash
export SSL_CERT_FILE=/etc/pki/tls/certs/ca-bundle.crt
```
> [!NOTE]
> Det är också värt att notera att vissa distributioner av Linux inte har en definierad TMP-eller TMPDIR-miljö. Detta leder till att tal-SDK: n hämtar listan över återkallade certifikat (CRL) varje gång, i stället för att cachelagra CRL: en på disken för åter användning tills de går ut. För att förbättra den första anslutnings prestandan kan du [skapa en miljö variabel med namnet tmpdir och ange den som sökväg till den temporära katalog som du har valt.](https://help.ubuntu.com/community/EnvironmentVariables)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Om Speech SDK](speech-sdk.md)
