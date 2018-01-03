---
title: "Skapa en sökväg-baserade regel för en Programgateway - Azure-portalen | Microsoft Docs"
description: "Lär dig hur du skapar en sökväg-baserade regler för en Programgateway med hjälp av Azure portal."
services: application-gateway
documentationcenter: na
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 87bd93bc-e1a6-45db-a226-555948f1feb7
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/03/2017
ms.author: davidmu
ms.openlocfilehash: b207e7e7bd83e56db68288190c7bedafa8b5b7fa
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2017
---
# <a name="create-a-path-based-rule-for-an-application-gateway-by-using-the-azure-portal"></a>Skapa en sökväg-baserade regel för en Programgateway med hjälp av Azure portal

> [!div class="op_single_selector"]
> * [Azure-portalen](application-gateway-create-url-route-portal.md)
> * [PowerShell och Azure Resource Manager](application-gateway-create-url-route-arm-ps.md)
> * [Azure CLI 2.0](application-gateway-create-url-route-cli.md)

Med URL-sökväg-baserade routning, kan du associera rutter baserat på HTTP-begäranden URL-sökvägen. Den kontrollerar om det finns en väg till poolen backend-server som konfigurerats för den URL som visas i programgatewayen och skickar sedan nätverkstrafiken till definierade poolen. Ett vanligt användningsområde för URL-sökväg-baserad routning är att belastningsutjämna förfrågningar för olika typer av innehåll till olika backend-serverpooler.

Programgatewayer har två regeltyper: basic och regler för URL-sökväg-baserade. Grundläggande regeltypen innehåller resursallokering för backend-pooler. Sökväg-baserade regler, förutom resursallokering, kan du också använda sökvägar för den begärda Webbadressen när du väljer i backend-poolen.

## <a name="scenario"></a>Scenario

Följande scenario skapar en sökväg-baserade regel i en befintlig gateway för programmet.
Det här scenariot förutsätter att du redan har följt stegen i [skapa en Programgateway med portalen](application-gateway-create-gateway-portal.md).

![URL-väg][scenario]

## <a name="createrule"></a>Skapa regel sökväg-baserade

En sökväg-baserade regel kräver sin egen lyssnare. Innan du skapar regeln bör du kontrollera att du har en tillgänglig lyssnare ska användas.

### <a name="step-1"></a>Steg 1

Gå till den [Azure-portalen](http://portal.azure.com) och välj en befintlig gateway för programmet. Klicka på **regler**.

![Översikt över Application Gateway][1]

### <a name="step-2"></a>Steg 2

Klicka på den **sökväg-baserade** för att lägga till en ny sökväg-baserade regel.

### <a name="step-3"></a>Steg 3

Den **Lägg till sökväg-baserade regler** bladet innehåller två avsnitt. Det första avsnittet är där du har definierat lyssnaren, namnet på regeln och standardinställningarna för sökvägen. Standardinställningarna för sökvägen är för vägar som inte omfattas av anpassade sökväg-baserade vägen. Det andra avsnittet av den **Lägg till sökväg-baserade regler** bladet är där du kan definiera regler baserat på sökvägen sig själva.

**Grundläggande inställningar**

* **Namnet**: ett eget namn för regeln som är tillgänglig i portalen.
* **Lyssnare**: lyssnaren som används för regeln.
* **Standard serverdelspool**: serverdelen som ska användas för Standardregeln.
* **Standardinställningarna för HTTP-**: HTTP-inställningar som ska användas för Standardregeln.

**Sökväg-baserade regelinställningar**

* **Namnet**: ett eget namn för regeln baserat på sökvägen.
* **Sökvägar**: sökvägen regeln söker efter när vidarebefordrar trafik.
* **Serverdelspool**: serverdelen som ska användas för regeln.
* **HTTP-inställningen**: HTTP-inställningar som ska användas för regeln.

> [!IMPORTANT]
> Den **sökvägar** inställningen är listan över sökvägen mönster som matchar. Varje mönster måste börja med ett snedstreck och en asterisk tillåts endast i slutet. Giltiga exempel: /xyz, /xyz*, och /xyz/*.  

![Lägg till sökväg-baserade regler bladet med information som har fyllt i][2]

Lägger till en sökväg-baserade regel i en befintlig Programgateway är en enkel process via Azure-portalen. När du skapar en regel för baserat på sökvägen kan du redigera den för att inkludera ytterligare regler. 

![Lägg till ytterligare sökväg-baserade regler][3]

Det här steget konfigurerar en väg baserat på sökvägen. Det är viktigt att förstå att begäranden inte på nytt. Begäranden finns, programgatewayen kontrollerar begäran och, baserat på URL-mönster, skickar en begäran till backend-poolen.

## <a name="next-steps"></a>Nästa steg

Information om hur du konfigurerar SSL-avlastning med Azure Programgateway finns [konfigurera en Programgateway för SSL-avlastning med hjälp av Azure portal](application-gateway-ssl-portal.md).

[1]: ./media/application-gateway-create-url-route-portal/figure1.png
[2]: ./media/application-gateway-create-url-route-portal/figure2.png
[3]: ./media/application-gateway-create-url-route-portal/figure3.png
[scenario]: ./media/application-gateway-create-url-route-portal/scenario.png
