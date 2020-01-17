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
ms.openlocfilehash: cadf31dede8ee81323076013d00b9431f597bda6
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2020
ms.locfileid: "76156496"
---
# <a name="configure-openssl-for-linux"></a>Konfigurera OpenSSL för Linux

När du använder en Speech SDK-version innan 1.9.0, konfigureras [openssl](https://www.openssl.org) dynamiskt till värd system versionen. I senare versioner av talet SDK är OpenSSL (version [1.1.1 b](https://mta.openssl.org/pipermail/openssl-announce/2019-February/000147.html)) statiskt länkat till kärn biblioteket i tal-SDK: n.

## <a name="troubleshoot-connectivity"></a>Felsökning av anslutningar

Om det uppstår anslutnings problem när du använder 1.9.0-versionen av talet SDK kontrollerar du att katalogen `ssl/certs` finns i `/usr/lib` Directory, som finns i fil systemet Linux. Om `ssl/certs` katalogen *inte finns*kontrollerar du var openssl är installerat i systemet med hjälp av följande kommando:

```bash
which openssl
```

Leta sedan reda på katalogen OpenSSL `certs` och kopiera innehållet i katalogen till `/usr/lib/ssl/certs` Directory. Försök sedan igen för att se om problem med anslutningen har lösts.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Om tal-SDK](speech-sdk.md)