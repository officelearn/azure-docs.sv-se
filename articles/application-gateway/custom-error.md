---
title: Skapa anpassade felsidor för Azure Application Gateway
description: I den här artikeln visas hur du skapar anpassade felsidor för Programgateway. Du kan använda din egen varumärkesanpassning och layout med hjälp av en anpassad felsida.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/16/2019
ms.author: victorh
ms.openlocfilehash: ff11f686287498fe12b31d15a630178bb12035ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74129864"
---
# <a name="create-application-gateway-custom-error-pages"></a>Skapa anpassade felsidor för programgateway

Med Application Gateway kan du skapa anpassade felsidor i stället för att visa standardmässiga felsidor. Du kan använda din egen varumärkesanpassning och layout med hjälp av en anpassad felsida.

Du kan till exempel definiera din egen underhållssida om webbprogrammet inte kan nås. Du kan också skapa en obehörig åtkomstsida om en skadlig begäran skickas till ett webbprogram.

Anpassade felsidor stöds för följande två scenarier:

- **Underhållssida** - Den här anpassade felsidan skickas i stället för en 502-felaktig gateway-sida. Den visas när Application Gateway inte har någon backend att dirigera trafik till. Till exempel när det finns schemalagt underhåll eller när ett oförutsett problem påverkar backend pool åtkomst.
- **Obehörig åtkomstsida** - Den här anpassade felsidan skickas i stället för en 403 obehörig åtkomstsida. Den visas när Application Gateway WAF upptäcker skadlig trafik och blockerar den.

Om ett fel kommer från serverd-servrarna skickas det inte bakåt till anroparen. En anpassad felsida visas inte. Programgateway kan visa en anpassad felsida när en begäran inte kan nå backend.

Anpassade felsidor kan definieras på global nivå och lyssnarnivå:

- **Global nivå** - felsidan gäller för trafik för alla webbprogram som distribueras på programgatewayen.
- **Lyssnarnivå** - felsidan tillämpas på trafik som tas emot på den lyssnaren.
- **Båda** - den anpassade felsidan som definierats på lyssnarnivå åsidosätter den som anges på global nivå.

Om du vill skapa en anpassad felsida måste du ha:

- statuskod för HTTP-svar.
- motsvarande plats för felsidan. 
- en offentligt tillgänglig Azure-lagringsblob för platsen.
- filnamnstypen *.htm eller *.html. 

Felsidans storlek måste vara mindre än 1 MB. Om det finns bilder länkade på felsidan måste de vara antingen allmänt tillgängliga absoluta webbadresser eller base64-kodad bildinline på den anpassade felsidan. Relativa länkar med bilder på samma blob-plats stöds för närvarande inte. 

När du har angett en felsida hämtar programgatewayen den från lagringsblobbplatsen och sparar den i den lokala programgatewaycachen. Sedan visas felsidan direkt från programgatewayen. Om du vill ändra en befintlig anpassad felsida måste du peka på en annan blob-plats i konfigurationen av programgatewayen. Programgatewayen kontrollerar inte regelbundet blob-platsen för att hämta nya versioner.

## <a name="portal-configuration"></a>Portal-konfiguration

1. Navigera till Application Gateway i portalen och välj en programgateway.

    ![ag-översikt](media/custom-error/ag-overview.png)
2. Klicka på **Lyssnare** och navigera till en viss lyssnare där du vill ange en felsida.

    ![Lyssnare för programgateway](media/custom-error/ag-listener.png)
3. Konfigurera en anpassad felsida för ett 403 WAF-fel eller en 502-underhållssida på lyssnarnivå.

    > [!NOTE]
    > Det går inte att skapa anpassade felsidor på global nivå från Azure-portalen.

4. Ange en offentligt tillgänglig blob-URL för en viss felstatuskod och klicka på **Spara**. Programgatewayen är nu konfigurerad med den anpassade felsidan.

   ![Felkoder för programgateway](media/custom-error/ag-error-codes.png)

## <a name="azure-powershell-configuration"></a>Azure PowerShell-konfiguration

Du kan använda Azure PowerShell för att konfigurera en anpassad felsida. Till exempel en global anpassad felsida:

`$updatedgateway = Add-AzApplicationGatewayCustomError -ApplicationGateway $appgw -StatusCode HttpStatus502 -CustomErrorPageUrl $customError502Url`

Eller en felsida på lyssnarnivå:

`$updatedlistener = Add-AzApplicationGatewayHttpListenerCustomError -HttpListener $listener01 -StatusCode HttpStatus502 -CustomErrorPageUrl $customError502Url`

Mer information finns i [Add-AzApplicationGatewayCustomError](https://docs.microsoft.com/powershell/module/az.network/add-azapplicationgatewaycustomerror?view=azps-1.2.0) och [Add-AzApplicationGatewayHttpListenerCustomError](https://docs.microsoft.com/powershell/module/az.network/add-azapplicationgatewayhttplistenercustomerror?view=azps-1.3.0).

## <a name="next-steps"></a>Nästa steg

Information om diagnostik för Application Gateway finns i [Backend-hälsa, diagnostikloggar och mått för Application Gateway](application-gateway-diagnostics.md).