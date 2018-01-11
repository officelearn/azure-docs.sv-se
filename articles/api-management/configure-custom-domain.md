---
title: "Konfigurera ett anpassat domännamn för din Azure API Management-instans | Microsoft Docs"
description: "Det här avsnittet beskriver hur du konfigurerar ett anpassat domännamn för din Azure API Management-instans."
services: api-management
documentationcenter: 
author: vladvino
manager: anneta
editor: 
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 12/14/2017
ms.author: apimpm
ms.openlocfilehash: cf8a3eb502a808945e97822e10e44d38137d1161
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2017
---
# <a name="configure-a-custom-domain-name"></a>Konfigurera ett anpassat domännamn 

När du skapar en instans för API Management (APIM) Azure tilldelar den en underdomän till azure-api.net (till exempel `apim-service-name.azure-api.net`). Du kan dock exponera dina APIM slutpunkter med hjälp av ditt eget domännamn, till exempel **contoso.com**. Den här kursen visar hur du mappar en befintlig anpassad DNS-namn till slutpunkter som exponeras av en Azure API Management-instans.

## <a name="prerequisites"></a>Krav

Om du vill utföra stegen som beskrivs i den här artikeln, måste du ha:

+ En aktiv Azure-prenumeration.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ En APIM-instans. Mer information finns i [skapa en instans av Azure API Management](get-started-create-service-instance.md).
+ Ett anpassat domännamn som ägs av dig. Det anpassade domännamnet som du vill använda, måste skaffat separat och finns på en DNS-server. Det här avsnittet ger instruktioner om hur du värd för ett anpassat domännamn.
+ Du måste ha ett giltigt certifikat med en offentlig och privat nyckel (. PFX). Ämne eller Alternativt ämnesnamn (SAN) måste matcha namnet på en domän (Detta gör APIM på ett säkert sätt exponera URL: er via SSL).

## <a name="use-the-azure-portal-to-set-a-custom-domain-name"></a>Använda Azure portal för att ange ett eget domännamn

1. Navigera till din APIM instans i den [Azure-portalen](https://portal.azure.com/).
2. Välj **anpassade domäner och SSL**.
    
    Det finns ett antal slutpunkter som du kan tilldela ett eget domännamn. Finns för närvarande följande slutpunkter: 
    + **Proxy** (standard är: `<apim-service-name>.azure-api.net`), 
    + **Portalen** (standard är: `<apim-service-name>.portal.azure-api.net`),     
    + **Hantering av** (standard är: `<apim-service-name>.management.azure-api.net`), 
    + **SCM** (standard är: `<apim-service-name>.scm.azure-api.net`).

    >[!NOTE]
    > Du kan uppdatera alla slutpunkter eller några av dem. Vanligtvis kunder uppdatera **Proxy** (URL: en används för att anropa API exponeras via API Management) och **Portal** (developer-portalen URL). **Hantering av** och **SCM** slutpunkter används internt av APIM kunder och därmed tilldelas mindre ofta ett anpassat domännamn.
3. Välj den slutpunkt som du vill uppdatera. 
4. I fönstret till höger klickar du på **anpassad**.

    + I den **anpassade domännamn**, anger du namnet som du vill använda. Till exempel `api.contoso.com`. <br/>Jokertecken domännamn (t.ex. *. domain.com) stöds också.
    + I den **certifikat**, ange en giltig. PFX-fil som du vill överföra. 
    + Om certifikatet har ett lösenord, anger du det i den **lösenord** fältet.
1. Klicka på Använd.

    >[!NOTE]
    >Processen för att ge certifikatet kan ta 15 minuter.

[!INCLUDE [api-management-custom-domain](../../includes/api-management-custom-domain.md)]

## <a name="next-steps"></a>Nästa steg

[Uppgradera och skala din tjänst](upgrade-and-scale.md)