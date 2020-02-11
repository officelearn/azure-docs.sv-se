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
ms.openlocfilehash: ff8772f7c3c3213c010b0bdbd0d0aa8897404bac
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2020
ms.locfileid: "77119993"
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

Kontrol lera om det finns `certs` under katalog under OPENSSLDIR. I exemplet ovan är det `/usr/lib/ssl/certs`.

* Om det finns `/usr/lib/ssl/certs` och den innehåller många enskilda certifikatfiler (med `.crt` eller `.pem` tillägget) behöver du inte göra några ytterligare åtgärder.

* Om OPENSSLDIR är något annat än `/usr/lib/ssl` och/eller om det finns en enda certifikat paket fil i stället för flera enskilda filer, måste du ange en lämplig SSL-miljövariabel för att indikera var certifikaten finns.

## <a name="examples"></a>Exempel

- OPENSSLDIR är `/opt/ssl`. Det finns `certs` under katalog med många `.crt`-eller `.pem`-filer.
Ange miljövariabeln `SSL_CERT_DIR` för att peka på `/opt/ssl/certs` innan du kör ett program som använder tal-SDK: n. Några exempel:
```bash
SSL_CERT_DIR=/opt/ssl/certs ./helloworld
```

- OPENSSLDIR är `/etc/pki/tls`. Det finns en certifikat bunts fil, till exempel `ca-bundle.pem` eller `ca-bundle.crt`.
Ange miljövariabeln `SSL_CERT_FILE` för att peka på `/etc/pki/tls/ca-bundle.pem` innan du kör ett program som använder tal-SDK: n. Några exempel:
```bash
SSL_CERT_FILE=/etc/pki/tls/ca-bundle.pem ./helloworld
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Om tal-SDK](speech-sdk.md)
