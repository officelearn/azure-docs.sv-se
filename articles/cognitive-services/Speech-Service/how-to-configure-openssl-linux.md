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
ms.openlocfilehash: 350c2bf3c4d0fc0a16f1b393e7c8d8a372679797
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78331152"
---
# <a name="configure-openssl-for-linux"></a>Konfigurera OpenSSL för Linux

När du använder en Speech SDK-version före 1.9.0 konfigureras [OpenSSL](https://www.openssl.org) dynamiskt till värdsystemversionen. I senare versioner av Tal-SDK är OpenSSL (version [1.1.1b](https://mta.openssl.org/pipermail/openssl-announce/2019-February/000147.html)) statiskt länkat till huvudbiblioteket för Tal-SDK.

Kontrollera att OpenSSL-certifikat har installerats i systemet för att säkerställa anslutningen. Kör ett kommando:
```bash
openssl version -d
```

Utdata på Ubuntu/Debianbaserade system bör vara:
```
OPENSSLDIR: "/usr/lib/ssl"
```

Kontrollera om `certs` det finns underkatalog under OPENSSLDIR. I exemplet ovan skulle `/usr/lib/ssl/certs`det vara .

* Om det `/usr/lib/ssl/certs` finns och det innehåller många `.crt` `.pem` enskilda certifikatfiler (med eller tillägg), finns det inget behov av ytterligare åtgärder.

* Om OPENSSLDIR är `/usr/lib/ssl` något annat än och/eller om det finns en enda certifikatpaketfil i stället för flera enskilda filer måste du ange en lämplig SSL-miljövariabel för att ange var certifikaten finns.

## <a name="examples"></a>Exempel

- OPENSSLDIR `/opt/ssl`är . Det `certs` finns underkatalog `.crt` med `.pem` många eller filer.
Ange att `SSL_CERT_DIR` miljövariabeln ska peka på `/opt/ssl/certs` innan du kör ett program som använder Tal-SDK. Ett exempel:
```bash
export SSL_CERT_DIR=/opt/ssl/certs
```

- OPENSSLDIR `/etc/pki/tls` är (som på RHEL/CentOS-baserade system). Det `certs` finns underkatalog med en certifikatpaketfil, till exempel `ca-bundle.crt`.
Ange att `SSL_CERT_FILE` miljövariabeln ska peka på den filen innan du kör ett program som använder Tal-SDK. Ett exempel:
```bash
export SSL_CERT_FILE=/etc/pki/tls/certs/ca-bundle.crt
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Om Speech SDK](speech-sdk.md)
