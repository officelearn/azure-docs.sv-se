---
title: Konfigurera eget domän namn för Azure API Management instans
titleSuffix: Azure API Management
description: I det här avsnittet beskrivs hur du konfigurerar ett anpassat domän namn för Azure API Management-instansen.
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
ms.openlocfilehash: 72075d4eff336af625bbf6d62f1276d2997bfed4
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2020
ms.locfileid: "77251218"
---
# <a name="configure-a-custom-domain-name"></a>Konfigurera ett anpassat domännamn

När du skapar en Azure API Management-tjänstinstans tilldelar Azure den en under domän till `azure-api.net` (till exempel `apim-service-name.azure-api.net`). Du kan dock exponera API Management-slutpunkter med ditt eget anpassade domän namn, till exempel **contoso.com**. Den här självstudien visar hur du mappar ett befintligt anpassat DNS-namn till slut punkter som exponeras av en API Management instans.

> [!IMPORTANT]
> API Management accepterar endast begär Anden med [värd huvud](https://tools.ietf.org/html/rfc2616#section-14.23) värden som matchar standard domän namnet eller något av de konfigurerade anpassade domän namnen.

> [!WARNING]
> Kunder som vill använda certifikat låsning för att förbättra säkerheten för sina program måste använda ett anpassat domän namn och certifikat som de hanterar, inte standard certifikatet. Kunder som fäster standard certifikatet i stället kommer att ta ett hårt beroende på egenskaperna för certifikatet som de inte styr, vilket inte är en rekommenderad metod.

## <a name="prerequisites"></a>Förutsättningar

För att utföra stegen som beskrivs i den här artikeln måste du ha:

-   En aktiv Azure-prenumeration.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   En API Management-instans. Mer information finns i [skapa en Azure API Management-instans](get-started-create-service-instance.md).
-   Ett anpassat domän namn som ägs av dig eller din organisation. Det här avsnittet innehåller inga instruktioner för hur du kan köpa ett anpassat domän namn.
-   En CNAME-post som finns på en DNS-server som mappar det anpassade domän namnet till standard domän namnet för din API Management-instans. Det här avsnittet innehåller inga instruktioner för hur du kan vara värd för en CNAME-post.
-   Du måste ha ett giltigt certifikat med en offentlig och privat nyckel (. PFX). Alternativt namn för certifikat mottagare (SAN) måste matcha domän namnet (Detta aktiverar API Management instans för att på ett säkert sätt exponera URL: er över SSL).

## <a name="use-the-azure-portal-to-set-a-custom-domain-name"></a>Använd Azure Portal för att ange ett anpassat domän namn

1. Navigera till din API Management-instans i [Azure Portal](https://portal.azure.com/).
1. Välj **anpassade domäner**.

    Det finns ett antal slut punkter som du kan tilldela ett anpassat domän namn. För närvarande är följande slut punkter tillgängliga:

    - **Gateway** (standard är: `<apim-service-name>.azure-api.net`),
    - **Portal** (standard är: `<apim-service-name>.portal.azure-api.net`),
    - **Hantering** (standard är: `<apim-service-name>.management.azure-api.net`),
    - **SCM** (standard är: `<apim-service-name>.scm.azure-api.net`).

    > [!NOTE]
    > Endast **Gateway** -slutpunkten är tillgänglig för konfiguration i förbruknings nivån.
    > Du kan uppdatera alla slut punkter eller några av dem. Kunder som uppdaterar **gatewayen** (denna URL används ofta för att anropa API: et som exponeras via API Management) och **portalen** (URL: en Developer Portal).
    > **Hanterings** -och **SCM** -slutpunkter används internt av API Management instansens ägare och därför är de mindre ofta tilldelade till ett anpassat domän namn.
    > **Premium** -nivån stöder inställning av flera värdnamn för **Gateway** -slutpunkten.

1. Välj den slut punkt som du vill uppdatera.
1. I fönstret till höger klickar du på **anpassad**.

    - I det **anpassade domän namnet**anger du det namn som du vill använda. Till exempel `api.contoso.com`.
    - I **certifikatet**väljer du ett certifikat från Key Vault. Du kan också ladda upp en giltig. PFX-fil och ange **lösen ord**, om certifikatet är skyddat med ett lösen ord.

    > [!NOTE]
    > Domän namn för jokertecken, t. ex. `*.contoso.com` stöds på alla nivåer förutom förbruknings nivån.

    > [!TIP]
    > Vi rekommenderar att du använder Azure Key Vault för att hantera certifikat och ställa in dem för automatisk rotation.
    > Om du använder Azure Key Vault för att hantera SSL-certifikatet för den anpassade domänen kontrollerar du att certifikatet har infogats i Key Vault [som ett _certifikat_](https://docs.microsoft.com/rest/api/keyvault/CreateCertificate/CreateCertificate), inte en _hemlighet_.
    >
    > Om du vill hämta ett SSL-certifikat måste API Management ha behörigheterna lista och hämta hemligheter på den Azure Key Vault som innehåller certifikatet. När du använder Azure Portal alla de nödvändiga konfigurations stegen att utföras automatiskt. När du använder kommando rads verktyg eller hanterings-API måste dessa behörigheter beviljas manuellt. Detta görs i två steg. Använd först sidan hanterade identiteter på din API Management-instans för att se till att den hanterade identiteten är aktive rad och anteckna det huvud-ID som visas på sidan. Andra, ge behörighets listan och hämta hemligheter till detta huvud namns-ID på den Azure Key Vault som innehåller certifikatet.
    >
    > Om certifikatet är inställt på automatisk rotation, kommer API Management automatiskt att hämta den senaste versionen utan drift avbrott till tjänsten (om din API Management-nivå har SLA-i. e. i alla nivåer förutom utvecklings nivån).

1. Klicka på Tillämpa.

    > [!NOTE]
    > Processen för att tilldela certifikatet kan ta 15 minuter eller mer beroende på storleken på distributionen. Developer SKU: n har avbrott, Basic och högre SKU: er har inte någon nedtid.

[!INCLUDE [api-management-custom-domain](../../includes/api-management-custom-domain.md)]

## <a name="dns-configuration"></a>DNS-konfiguration

När du konfigurerar DNS för det anpassade domän namnet har du två alternativ:

-   Konfigurera en CNAME-post som pekar på slut punkten för ditt konfigurerade anpassade domän namn.
-   Konfigurera en A-post som pekar på din API Management Gateway-IP-adress.

> [!NOTE]
> Även om IP-adressen för API-hanterings instansen är statisk, kan den ändras i några få scenarier. På grund av detta rekommenderar vi att du använder CNAME när du konfigurerar en anpassad domän. Ta hänsyn till när du väljer DNS-konfigurationsfil. Läs mer i [vanliga frågor och svar om API Management](api-management-faq.md#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services).

## <a name="next-steps"></a>Nästa steg

[Uppgradera och skala din tjänst](upgrade-and-scale.md)
