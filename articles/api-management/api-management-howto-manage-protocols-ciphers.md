---
title: Hantera protokoll och chiffer i Azure API Management | Microsoft Docs
description: Lär dig hur du hanterar protokoll (TLS, SSL) och chiffer (DES) i Azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2019
ms.author: apimpm
ms.openlocfilehash: f809aaf872607e7fa61368832a3df74318f2a858
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66141510"
---
# <a name="manage-protocols-and-ciphers-in-azure-api-management"></a>Hantera protokoll och chiffer i Azure API Management

Azure API Management har stöd för flera versioner av TLS-protokollet för både klient och serverdel samt 3DES-chiffer.

Den här guiden visar hur du hanterar protokoll och blockchiffer konfigurationen för en Azure API Management-instans.

![Hantera protokoll och APIM-chiffer](./media/api-management-howto-manage-protocols-ciphers/api-management-protocols-ciphers.png)

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill följa stegen i den här artikeln, måste du ha:

* En API Management-instans

## <a name="how-to-manage-tls-protocols-and-3des-cipher"></a>Så här hanterar du TLS-protokoll och 3DES cipher

1. Gå till din **API Management-instans** i Azure-portalen.
2. Välj **protokollet inställningar** på menyn.  
3. Aktivera eller inaktivera önskat protokoll eller chiffer.
4. Klicka på **Spara**. Ändringarna tillämpas inom en timme.  

## <a name="next-steps"></a>Nästa steg

* Läs mer om [TLS (Transport Layer Security)](https://docs.microsoft.com/dotnet/framework/network-programming/tls).
* Kolla in mer [videor](https://azure.microsoft.com/documentation/videos/index/?services=api-management) om API Management.