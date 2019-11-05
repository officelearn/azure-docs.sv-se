---
title: Etablera en gateway för egen värd i Azure API Management | Microsoft Docs
description: Lär dig hur du etablerar en gateway för egen värd i Azure API Management.
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: ee9594e7aa3b55f7eb6fc7ba41783708555bd15e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513775"
---
# <a name="provision-a-self-hosted-gateway-in-azure-api-management"></a>Etablera en gateway för egen värd i Azure API Management

Att tillhandahålla en gateway-resurs i Azure API Management-instansen är en förutsättning för att distribuera en gateway med egen värd. Den här artikeln vägleder dig genom stegen för att etablera en gateway-resurs i API Management.

> [!NOTE]
> Funktionen för lokal gateway är i för hands version. Under för hands versionen är den egna värdbaserade gatewayen bara tillgänglig på utvecklings-och Premium-nivåerna utan extra kostnad. Developer-nivån är begränsad till en enda lokal gateway-distribution.

## <a name="prerequisites"></a>Förutsättningar

Slutför följande snabbstart: [Skapa en Azure API Management-instans](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="provision-a-self-hosted-gateway"></a>Etablera en gateway för egen värd

1. Välj **gatewayer** från **Inställningar**.
2. Klicka på **+ Lägg till**.
3. Ange **namn** och **region** för gatewayen.
> [!TIP]
> **Region** anger platsen för de Gateway-noder som ska associeras med denna gateway-resurs. Den är semantiskt likvärdig med en liknande egenskap som är kopplad till en Azure-resurs, men kan tilldelas ett godtyckligt sträng värde.
4. Alternativt kan du ange en **Beskrivning** av Gateway-resursen.
5. Du kan också välja **+** under **API: er** för att koppla en eller flera API: er till denna gateway-resurs.
> [!TIP]
> Du kan associera och ta bort ett API från en gateway på fliken **Inställningar** i API.

> [!IMPORTANT]
> Som standard kommer inga befintliga API: er att associeras med den nya gateway-resursen. Försök att anropa dem via den nya gatewayen leder därför till `404 Resource Not Found` svar.
6. Klicka på **Lägg till**.

Nu har Gateway-resursen etablerats i API Management-instansen. Du kan fortsätta att distribuera gatewayen.

## <a name="next-steps"></a>Nästa steg

* Mer information om den egen värdbaserade gatewayen finns i [Översikt över Azure API Management egen Gateway](self-hosted-gateway-overview.md)
* Lär dig mer om hur du [distribuerar en egen värd-Gateway till Kubernetes](api-management-howto-deploy-self-hosted-gateway-to-k8s.md)
* Lär dig mer om hur du [distribuerar en egen värd-Gateway till Docker](api-management-howto-deploy-self-hosted-gateway-to-docker.md)
