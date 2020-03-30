---
title: Etablera en självvärd gateway i Azure API Management | Microsoft-dokument
description: Lär dig hur du etablerar en självvärd gateway i Azure API Management.
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
ms.openlocfilehash: d33c5f75234ad7165a9062ecc3bb2a00d502f8c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74075292"
---
# <a name="provision-a-self-hosted-gateway-in-azure-api-management"></a>Etablera en självvärd gateway i Azure API Management

Etablering av en gateway-resurs i Azure API Management-instansen är en förutsättning för att distribuera en självvärd gateway. Den här artikeln går igenom stegen för att etablera en gateway-resurs i API Management.

> [!NOTE]
> Den självvärdbaserade gatewayfunktionen är i förhandsversion. Under förhandsversionen är den självvärdbaserade gatewayen endast tillgänglig på utvecklar- och premiumnivåerna utan extra kostnad. Utvecklarnivån är begränsad till en enda självvärd gateway-distribution.

## <a name="prerequisites"></a>Krav

Slutför följande snabbstart: [Skapa en Azure API Management-instans](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="provision-a-self-hosted-gateway"></a>Etablera en gateway med egen värd

1. Välj **gateways** under **Inställningar**.
2. Klicka på **+ Lägg till**.
3. Ange **namnet** och **regionen** för gatewayen.
> [!TIP]
> **Region** anger avsedd plats för gatewaynoderna som ska associeras med den här gatewayresursen. Det är semantiskt likvärdigt med en liknande egenskap som är associerad med en Azure-resurs, men kan tilldelas ett godtyckligt strängvärde.

4. Du kan också ange en **beskrivning** av gatewayresursen.
5. Du kan **+** också välja under **API:er om** du vill associera en eller flera API:er med den här gatewayresursen.
> [!TIP]
> Du kan associera och ta bort ett API från en gateway på fliken **Inställningar för** API.

> [!IMPORTANT]
> Som standard kommer ingen av de befintliga API:erna att associeras med den nya gatewayresursen. Därför kommer försök att anropa dem via den `404 Resource Not Found` nya gatewayen att resultera i svar.

6. Klicka på **Lägg till**.

Nu har gatewayresursen etablerats i DIN API Management-instans. Du kan fortsätta att distribuera gatewayen.

## <a name="next-steps"></a>Nästa steg

* Mer information om den självvärderade gatewayen finns i [Azure API Management självvärderade gateway översikt](self-hosted-gateway-overview.md)
* Läs mer om hur [du distribuerar en självvärd gateway till Kubernetes](api-management-howto-deploy-self-hosted-gateway-to-k8s.md)
* Läs mer om hur [du distribuerar en självvärd gateway till Docker](api-management-howto-deploy-self-hosted-gateway-to-docker.md)
