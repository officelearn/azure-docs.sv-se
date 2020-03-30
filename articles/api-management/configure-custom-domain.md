---
title: Konfigurera anpassat domännamn för Azure API Management-instans
titleSuffix: Azure API Management
description: I det här avsnittet beskrivs hur du konfigurerar ett anpassat domännamn för din Azure API Management-instans.
services: api-management
documentationcenter: ''
author: vladvino
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 01/13/2020
ms.author: apimpm
ms.openlocfilehash: 4587909ad6fca6cdf21d54d11d89f797bbb29833
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335837"
---
# <a name="configure-a-custom-domain-name"></a>Konfigurera ett anpassat domännamn

När du skapar en Azure API Management-tjänstinstans tilldelar Azure den en underdomän av `azure-api.net` (till exempel `apim-service-name.azure-api.net`). Du kan dock visa dina API Management-slutpunkter med ditt eget eget domännamn, till exempel **contoso.com**. Den här självstudien visar hur du mappar ett befintligt anpassat DNS-namn till slutpunkter som exponeras av en API Management-instans.

> [!IMPORTANT]
> API Management accepterar endast begäranden med [värden för värdhuvudet](https://tools.ietf.org/html/rfc2616#section-14.23) som matchar standarddomännamnet eller något av de konfigurerade anpassade domännamnen.

> [!WARNING]
> Kunder som vill använda certifikatbeslag för att förbättra säkerheten för sina program måste använda ett eget domännamn och certifikat som de hanterar, inte standardcertifikatet. Kunder som fäster standardcertifikatet i stället kommer att ha ett hårt beroende av egenskaperna för certifikatet som de inte styr, vilket inte är en rekommenderad metod.

## <a name="prerequisites"></a>Krav

Om du vill utföra stegen som beskrivs i den här artikeln måste du ha:

-   En aktiv Azure-prenumeration.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   En API Management-instans. Mer information finns i [Skapa en Azure API Management-instans](get-started-create-service-instance.md).
-   Ett eget domännamn som ägs av dig eller din organisation. Det här avsnittet innehåller inga instruktioner om hur du skaffar ett eget domännamn.
-   En CNAME-post som finns på en DNS-server som mappar det anpassade domännamnet till standarddomännamnet för API Management-instansen. Det här avsnittet innehåller inga instruktioner om hur du är värd för en CNAME-post.
-   Du måste ha ett giltigt certifikat med en offentlig och privat nyckel (. PFX). Ämne eller ämne alternativt namn (SAN) måste matcha domännamnet (detta gör att API Management-instansen på ett säkert sätt kan exponera webbadresser över TLS).

## <a name="use-the-azure-portal-to-set-a-custom-domain-name"></a>Använda Azure-portalen för att ange ett eget domännamn

1. Navigera till din API Management-instans i [Azure-portalen](https://portal.azure.com/).
1. Välj **Anpassade domäner**.

    Det finns ett antal slutpunkter som du kan tilldela ett eget domännamn till. För närvarande är följande slutpunkter tillgängliga:

    - **Gateway** (standard `<apim-service-name>.azure-api.net`är: ),
    - **Portal** (standard `<apim-service-name>.portal.azure-api.net`är: ),
    - **Hantering** (standard `<apim-service-name>.management.azure-api.net`är: ),
    - **SCM** (standard `<apim-service-name>.scm.azure-api.net`är: ),
    - **NewPortal** (standard `<apim-service-name>.developer.azure-api.net`är: ).

    > [!NOTE]
    > Endast **gateway-slutpunkten** är tillgänglig för konfiguration på förbrukningsnivån.
    > Du kan uppdatera alla slutpunkter eller några av dem. Vanligtvis uppdaterar kunder **Gateway** (den här URL:en används för att anropa API:et som exponeras via API Management) och **Portal** (utvecklarportal-URL:en).
    > **Hanterings-** och **SCM-slutpunkter** används internt endast av API Management-instansägarna och tilldelas därför mindre ofta ett anpassat domännamn.
    > **Premium-nivån** stöder inställning av flera värdnamn för **slutpunkten för Gateway.**

1. Markera den slutpunkt som du vill uppdatera.
1. Klicka på **Anpassad**i fönstret till höger .

    - Ange det namn du vill använda i **det anpassade domännamnet.** Till exempel `api.contoso.com`.
    - Välj ett certifikat från Key Vault i **certifikatet.** Du kan också ladda upp en giltig . PFX-filen och ange sitt **lösenord**, om certifikatet är skyddat med ett lösenord.

    > [!NOTE]
    > Jokerteckendomännamn, t.ex. `*.contoso.com`

    > [!TIP]
    > Vi rekommenderar att du använder Azure Key Vault för att hantera certifikat och ställa in dem för att rotera automatiskt.
    > Om du använder Azure Key Vault för att hantera det anpassade TLS/SSL-certifikatet för domänen kontrollerar du att certifikatet infogas i Key Vault [som _ett certifikat_](https://docs.microsoft.com/rest/api/keyvault/CreateCertificate/CreateCertificate), inte en _hemlighet_.
    >
    > För att hämta ett TLS/SSL-certifikat måste API Management ha listan och få hemligheterbehörigheter på Azure Key Vault som innehåller certifikatet. När du använder Azure-portalen kommer alla nödvändiga konfigurationssteg att slutföras automatiskt. När du använder kommandoradsverktyg eller hanterings-API måste dessa behörigheter beviljas manuellt. Detta görs i två steg. Använd först sidan Hanterade identiteter på API Management-instansen för att kontrollera att hanterad identitet är aktiverad och anteckna huvud-ID som visas på sidan. För det andra, ge behörighetslista och få hemligheter behörigheter till detta huvud-ID på Azure Key Vault som innehåller certifikatet.
    >
    > Om certifikatet är inställt på att rotera automatiskt hämtar API Management den senaste versionen automatiskt utan avbrott till tjänsten (om din API Management-nivå har SLA - dvs på alla nivåer utom utvecklarnivån).

1. Klicka på Använd.

    > [!NOTE]
    > Processen att tilldela certifikatet kan ta 15 minuter eller mer beroende på distributionens storlek. Utvecklare SKU har driftstopp, Basic och högre SKU inte har driftstopp.

[!INCLUDE [api-management-custom-domain](../../includes/api-management-custom-domain.md)]

## <a name="dns-configuration"></a>DNS-konfiguration

När du konfigurerar DNS för ditt eget domännamn har du två alternativ:

-   Konfigurera en CNAME-post som pekar på slutpunkten för det konfigurerade domännamnet.
-   Konfigurera en A-post som pekar på IP-adressen för API Management gateway.

> [!NOTE]
> Även om IP-adressen FÖR API Managment-instansen är statisk kan den ändras i några få scenarier. På grund av detta rekommenderas att använda CNAME när du konfigurerar anpassad domän. Ta hänsyn till detta när du väljer DNS-konfigurationsmetod. Läs mer i [IP-dokumentationsartikeln](api-management-howto-ip-addresses.md#changes-to-the-ip-addresses) och [API Management FAQ](api-management-faq.md#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services).

## <a name="next-steps"></a>Nästa steg

[Uppgradera och skala din tjänst](upgrade-and-scale.md)
