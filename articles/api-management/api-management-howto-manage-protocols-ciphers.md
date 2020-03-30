---
title: Hantera protokoll och chiffer i Azure API Management | Microsoft-dokument
description: Lär dig hur du hanterar protokoll (TLS) och chiffer (DES) i Azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/29/2019
ms.author: apimpm
ms.openlocfilehash: 7f87389016286c9f1b91abc77155be9e94005371
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335874"
---
# <a name="manage-protocols-and-ciphers-in-azure-api-management"></a>Hantera protokoll och chiffer i Azure API Management

Azure API Management stöder flera versioner av TLS-protokollet för både klient- och serversidan samt 3DES-chiffer.

Den här guiden visar hur du hanterar protokoll och chifferkonfiguration för en Azure API Management-instans.

![Hantera protokoll och chiffer i APIM](./media/api-management-howto-manage-protocols-ciphers/api-management-protocols-ciphers.png)

## <a name="prerequisites"></a>Krav

Om du vill följa stegen i den här artikeln måste du ha:

* En API-hanteringsinstans

## <a name="how-to-manage-tls-protocols-and-3des-cipher"></a>Hantera TLS-protokoll och 3DES-chiffer

1. Navigera till din **API Management-instans** i Azure-portalen.
2. Välj **Protokollinställningar** på menyn.  
3. Aktivera eller inaktivera önskade protokoll eller chiffer.
4. Klicka på **Spara**. Ändringar kommer att tillämpas inom en timme.  

## <a name="next-steps"></a>Nästa steg

* Läs mer om [TLS (Transport Layer Security)](https://docs.microsoft.com/dotnet/framework/network-programming/tls).
* Kolla in fler [videor](https://azure.microsoft.com/documentation/videos/index/?services=api-management) om API Management.