---
title: Konfigurera ett anpassat domännamn för din Azure API Management-instans | Microsoft Docs
description: Det här avsnittet beskriver hur du konfigurerar ett anpassat domännamn för din Azure API Management-instans.
services: api-management
documentationcenter: ''
author: vladvino
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 07/01/2019
ms.author: apimpm
ms.openlocfilehash: 59b44dcc9ec3a1f7c274f426a19aa8ed2258db3e
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509309"
---
# <a name="configure-a-custom-domain-name"></a>Konfigurera ett anpassat domännamn

När du skapar en Azure API Management-tjänstinstans Azure tilldelar den till en underdomän till azure-api.net (till exempel `apim-service-name.azure-api.net`). Men du kan lägga upp din API Management-slutpunkter med ditt eget domännamn, till exempel **contoso.com**. Den här självstudien visar hur du mappar ett befintligt anpassat DNS-namn till slutpunkter som exponeras av en API Management-instans.

> [!WARNING]
> Kunder som vill använda certifikat fästa för att förbättra säkerheten för sina program måste använda ett anpassat domännamn > och certifikat som de hanterar inte standardcertifikatet. Kunder som fästa standardcertifikatet i stället att > tar ett fast beroende på egenskaperna för det certifikat som de inte har kontroll över, vilket inte är en rekommendation.

## <a name="prerequisites"></a>Förutsättningar

Om du vill utföra stegen som beskrivs i den här artikeln, måste du ha:

-   En aktiv Azure-prenumeration.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   En API Management-instans. Mer information finns i [skapa en Azure API Management-instans](get-started-create-service-instance.md).
-   Ett anpassat domännamn som ägs av dig. Det anpassade domännamnet som du vill använda, måste skaffat separat och finns på en DNS-server. Det här avsnittet ger inte instruktioner om hur du vara värd för ett anpassat domännamn.
-   Du måste ha ett giltigt certifikat med en offentlig och privat nyckel (. PFX). Ämnet eller alternativt namn för certifikatmottagare (SAN) måste matcha domännamnet (Detta möjliggör API Management-instans på ett säkert sätt exponera URL: er via SSL).

## <a name="use-the-azure-portal-to-set-a-custom-domain-name"></a>Använd Azure portal för att ange ett anpassat domännamn

1. Gå till din API Management-instans i den [Azure-portalen](https://portal.azure.com/).
1. Välj **anpassade domäner och SSL**.

    Det finns ett antal slutpunkter som du kan tilldela ett anpassat domännamn. För närvarande finns följande slutpunkter:

    - **Proxy** (standardvärdet är: `<apim-service-name>.azure-api.net`),
    - **Portalen** (standardvärdet är: `<apim-service-name>.portal.azure-api.net`),
    - **Hantering av** (standardvärdet är: `<apim-service-name>.management.azure-api.net`),
    - **SCM** (standardvärdet är: `<apim-service-name>.scm.azure-api.net`).

    > [!NOTE]
    > Du kan uppdatera alla slutpunkter eller några av dem. Ofta, kunder uppdatera **Proxy** (den här URL: en används för att anropa API exponeras via API Management) och **Portal** (utvecklarportalen URL: en). **Hantering av** och **SCM** slutpunkter används internt av API Management-instans ägarna endast och därmed tilldelas mindre ofta ett anpassat domännamn. I de flesta fall kan du ange bara ett enda anpassat domännamn för en viss slutpunkt. Men den **Premium** nivån stöder att flera värdnamn för den **Proxy** slutpunkt.

1. Välj den slutpunkt som du vill uppdatera.
1. I fönstret till höger klickar du på **anpassad**.

    - I den **anpassade domännamn**, anger du namnet som du vill använda. Till exempel `api.contoso.com`. Domännamn med jokertecken (till exempel \*. domain.com) stöds också.
    - I den **certifikat**, Välj ett certifikat från Key Vault. Du kan också ladda upp en giltig. PFX fil och ge dess **lösenord**om certifikatet är skyddat med ett lösenord.

    > [!TIP]
    > Vi rekommenderar att du använder Azure Key Vault för att hantera certifikat och ställa in dem på autorotate.
    > Om du använder Azure Key Vault för att hantera SSL-certifikat för anpassad domän, se till att certifikatet infogas i Key Vault [som en _certifikat_](https://docs.microsoft.com/rest/api/keyvault/CreateCertificate/CreateCertificate), inte en _hemlighet_.
    >
    > För att hämta ett SSL-certifikat, måste API Management ha listan behörigheter en get-hemligheter för Azure Key Vault som innehåller certifikatet. När du använder Azure-portalen alla nödvändiga konfigurationssteg slutförs automatiskt. När du använder kommandoradsverktyg eller hanterings-API, måste dessa behörigheter tilldelas manuellt. Detta görs i två steg. Använd först hanterade identiteter sida på din API Management-instans för att se till att hanterade identiteter är aktiverad och anteckna huvudkontots id visas på sidan. Dessutom ger behörighetslistan och få hemligheter behörighet till den här ägar-id för Azure Key Vault som innehåller certifikatet.
    >
    > Om certifikatet har angetts till autorotate ska API Management hämta den senaste versionen automatiskt utan någon avbrottstid till tjänsten (om din API Management-nivå har SLA - dvs. på alla nivåer utom Developer-nivån).

1. Klicka på tillämpa.

    > [!NOTE]
    > Processen för att ge certifikatet kan ta 15 minuter eller mer beroende på storleken på distributionen. Developer SKU har driftstopp, grundläggande och högre SKU: er har inte driftstopp.

[!INCLUDE [api-management-custom-domain](../../includes/api-management-custom-domain.md)]

## <a name="next-steps"></a>Nästa steg

[Uppgradera och skala din tjänst](upgrade-and-scale.md)
