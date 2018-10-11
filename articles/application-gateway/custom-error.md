---
title: Skapa anpassade felsidor för Azure Application Gateway
description: Den här artikeln visar hur du skapar anpassade felsidor för Application Gateway.
services: application-gateway
author: amitsriva
ms.service: application-gateway
ms.topic: article
ms.date: 10/11/2018
ms.author: victorh
ms.openlocfilehash: 2f76347105743538e9fc1d7588ecb949f2675696
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2018
ms.locfileid: "49071297"
---
# <a name="create-application-gateway-custom-error-pages"></a>Skapa anpassade felsidor för Application Gateway

Application Gateway kan du skapa anpassade felsidor i stället för den standard-felsidor. Du kan använda din egen anpassning och layout med hjälp av en anpassad felsida.

Du kan till exempel definiera egna underhållssidan om ditt webbprogram inte går att nå. Du kan också skapa en sida för obehörig åtkomst om en obehörig begäran har skickats till ett webbprogram.

Anpassade felsidor har stöd för följande två scenarier:

- **Underhållssidan** – den här anpassade felsidan skickas i stället för en 502 Felaktig gateway-sida. Den visas när Application Gateway har inga serverdelen för att dirigera trafik till. Till exempel när det har schemalagt underhåll eller när ett oförutsedda problem påverkar backend-pool-åtkomst.
- **Obehörig åtkomst till sidan** – den här anpassade felsidan skickas i stället för en sida 403 för obehörig åtkomst. Den visas när Application Gateway WAF identifierar skadlig trafik och blockerar den.

Om ett fel kommer från backend-servrarna, sedan skickas det vidare oförändrade tillbaka till anroparen. En anpassad felsida visas inte. Application gateway kan visa en anpassad felsida när en begäran inte kan nå serverdelen.

Anpassade felsidor kan definieras på global nivå och den lyssnare:

- **Global nivå** -felsidan gäller för trafik för alla webbprogram som distribueras på den application gatewayen.
- **Lyssnaren nivå** -felsidan gäller för trafik som tas emot på den lyssnaren.
- **Båda** -den anpassade felsidan som definierats på nivån lyssnare åsidosätter en uppsättning på global nivå.

Om du vill skapa en anpassad felsida, måste du ha:
- en HTTP-statuskod i svaret.
- motsvarande plats för felsidan. 
- en offentligt tillgänglig Azure storage-blob för platsen.
- en *.htm eller *.html utökningstyp. 

Storleken på felsidan måste vara mindre än 1 MB. Om det finns bilder som är länkade i felsidan, måste de vara offentligt tillgänglig absoluta URL: er eller base64-kodad infogade för bilden i den anpassade felsidan. Relativa länkar med bilder i samma blobbplats stöds inte för närvarande. 

När du har angett en felsida application gateway hämtar den från lagringsplatsen för blob och sparar det på det lokala gatewayen cacheminnet. Sedan hämtas felsidan direkt från application gateway. Om du vill ändra en befintlig anpassad felsida, måste du peka till en annan blob-plats i application gateway-konfigurationen. Application gateway kontrollerar inte regelbundet blobbplats för att hämta nya versioner.

## <a name="portal-configuration"></a>Portalkonfiguration

1. Gå till Application Gateway i portalen och välj en application gateway.

    ![AG-översikt](media/custom-error/ag-overview.png)
2. Klicka på **lyssnare** och navigera till en viss lyssnare där du vill ange en felsida.

    ![Application Gateway-lyssnare](media/custom-error/ag-listener.png)
3. Konfigurera en anpassad felsida för ett 403 WAF-fel eller en 502 underhållssidan på nivån lyssnare.

    > [!NOTE]
    > Skapa anpassade felsidor för global nivå från Azure portal stöds för närvarande inte.

4. Ange en offentligt tillgänglig blob-URL för en viss felkod status och klicka på **spara**. Application Gateway har konfigurerats med den anpassade felsidan.

   ![Felkoder för Application Gateway](media/custom-error/ag-error-codes.png)
## <a name="next-steps"></a>Nästa steg
Läs om hur Application Gateway-diagnostik [backend-hälsotillstånd, diagnostikloggar och mått för Application Gateway](application-gateway-diagnostics.md).