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
ms.date: 06/15/2018
ms.author: apimpm
ms.openlocfilehash: a55d16a35b5eec1af2b24d02e158905493615999
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2018
ms.locfileid: "52441120"
---
# <a name="manage-protocols-and-ciphers-in-azure-api-management"></a>Hantera protokoll och chiffer i Azure API Management

Azure API Management har stöd för flera versioner av TLS-protokollet för både klient och serverdel samt 3DES-chiffer.

Den här guiden visar hur du hanterar protokoll och blockchiffer konfigurationen för en Azure API Management-instans.

![Hantera protokoll och APIM-chiffer](./media/api-management-howto-manage-protocols-ciphers/api-management-protocols-ciphers.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Förutsättningar

Om du vill följa stegen i den här artikeln, måste du ha:

* En API Management-instans

## <a name="how-to-manage-tls-protocols-and-3des-cipher"></a>Så här hanterar du TLS-protokoll och 3DES cipher

1. Gå till din **API Management-instans** i Azure-portalen.
2. Välj **SSL** på menyn.  
    ![Hantera protokoll och chiffer i APIM - menyn](./media/api-management-howto-manage-protocols-ciphers/api-management-menu.png)
3. Aktivera eller inaktivera önskat protokoll eller chiffer.
4. Klicka på **Spara**. Ändringarna tillämpas inom en timme.  
    ![Hantera protokoll och chiffer i APIM - spara](./media/api-management-howto-manage-protocols-ciphers/api-management-protocols-ciphers-save.png)

## <a name="next-steps"></a>Nästa steg

* Läs mer om [TLS (Transport Layer Security)](https://docs.microsoft.com/dotnet/framework/network-programming/tls).
* Kolla in mer [videor](https://azure.microsoft.com/documentation/videos/index/?services=api-management) om API Management.