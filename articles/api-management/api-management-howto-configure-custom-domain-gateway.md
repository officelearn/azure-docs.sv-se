---
title: Konfigurera ett anpassat domännamn för din självvärderade Azure API Management-gateway | Microsoft-dokument
description: I det här avsnittet beskrivs stegen för att konfigurera ett anpassat domännamn för självvärd azure API Management gateway.
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: 1f2184c7c62887a98a76877528b167d173c3d75b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335937"
---
# <a name="configure-a-custom-domain-name"></a>Konfigurera ett anpassat domännamn

När du etablerar en [självvärd Azure API Management-gateway](self-hosted-gateway-overview.md) tilldelas den inte värdnamnet och måste refereras av dess IP-adress. Den här artikeln visar hur du mappar ett befintligt anpassat DNS-namn (kallas även värdnamn) en självvärd gateway.

> [!NOTE]
> Den självvärdbaserade gatewayfunktionen är i förhandsversion. Under förhandsversionen är den självvärdbaserade gatewayen endast tillgänglig på utvecklar- och premiumnivåerna utan extra kostnad. Utvecklarnivån är begränsad till en enda självvärd gateway-distribution.

## <a name="prerequisites"></a>Krav

Om du vill utföra stegen som beskrivs i den här artikeln måste du ha:

-   En aktiv Azure-prenumeration.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   En API Management-instans. Mer information finns i [Skapa en Azure API Management-instans](get-started-create-service-instance.md).
- En självvärd gateway. Mer information finns i Så här etablerar du [självvärdbaserad gateway](api-management-howto-provision-self-hosted-gateway.md)
-   Ett eget domännamn som ägs av dig eller din organisation. Det här avsnittet innehåller inga instruktioner om hur du skaffar ett eget domännamn.
-   En DNS-post som finns på en DNS-server som mappar det anpassade domännamnet till den självvärderade gatewayens IP-adress. Det här avsnittet innehåller inga instruktioner om hur du är värd för en DNS-post.
-   Du måste ha ett giltigt certifikat med en offentlig och privat nyckel (. PFX). Ämne eller ämne alternativt namn (SAN) måste matcha domännamnet (detta gör att API Management-instansen på ett säkert sätt kan exponera webbadresser över TLS).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="add-custom-domain-certificate-to-your-api-management-service"></a>Lägga till anpassat domäncertifikat i API Management-tjänsten

1. Välj **Certifikat** från under **Säkerhet**.
2. Välj **+ Lägg till**.
3. Ange ett resursnamn för certifikatet i **ID-fältet.**
4. Markera filen som innehåller certifikatet (. PFX) genom att välja **fältet Certifikat** eller mappikonen intill det.
5. Ange lösenordet för certifikatet i fältet **Lösenord.**
6. Välj **Skapa** om du vill lägga till certifikatet i API Management-tjänsten.

## <a name="use-the-azure-portal-to-set-a-custom-domain-name-for-your-self-hosted-gateway"></a>Använd Azure-portalen för att ange ett anpassat domännamn för din självvärderade gateway

1. Välj **gateways** under **Inställningar**.
2. Välj den självvärderade gateway som du vill konfigurera domännamnet för.
3. Välj **Värdnamn** under **Inställningar**.
4. Välj **+ Lägg till**
5. Ange resursnamn för värdnamnet i fältet **Namn.**
6. Ange domännamn i fältet **Värdnamn.**
7. Välj ett certifikat i listrutan **Certifikat.**
8. Välj **Kryssrutan Förhandla fram klientcertifikat** om någon av API:erna som exponeras via den här gatewayen använder klientcertifikatautentisering.
    > [!WARNING]
    > Den här inställningen delas av alla domännamn som konfigurerats för gatewayen.
9. Välj **Lägg till** om du vill tilldela det anpassade domännamnet till den valda självvärderade gatewayen.

## <a name="next-steps"></a>Nästa steg

[Uppgradera och skala din tjänst](upgrade-and-scale.md)
