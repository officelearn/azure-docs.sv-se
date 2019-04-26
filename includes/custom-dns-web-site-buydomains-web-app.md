---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/09/2018
ms.author: cephalin
ms.openlocfilehash: ce949caa2b80c08f1015ee21c00197d6a95103c2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60531092"
---
Om du vill att en domän kan du köpa domäner på [Azure-hanteringsportalen](https://portal.azure.com) direkt. Använd följande steg för att köpa domännamn och tilldela till din webbapp.

1. I webbläsaren, öppna den [Azure-hanteringsportalen](https://portal.azure.com).
2. I den **Webbappar** klickar du på namnet på din webbapp, Välj **inställningar**, och välj sedan **anpassade domäner och SSL**
   
    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)
3. I den **anpassade domäner och SSL** bladet klickar du på **köp domäner**.
   
    ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-1.png)
4. I den **köp domäner** bladet använda textrutan för att ange domännamnet som du vill köpa. Föreslagna tillgängliga domäner kommer att visas bara blåsa textrutan. Välj vilken domän som du vill köpa.
   
   ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-2.png)
5. Klicka på den **kontaktuppgifter** och fyll domänens kontaktinformation formuläret.
   
   ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-3.png)
6. Klicka på den **Välj** på **köp domäner** bladet och sedan visas köpinformationen på **Köpbekräftelse** bladet. Om du godkänner de juridiska villkoren och klickar på **köpa**, din beställning skickas och du kan övervaka inköpsprocessen på **meddelande**.
   
   ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-4.png)
   
   ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-5.png)
7. Om du har beställt en domän, kan du hantera domänen och tilldela till din webbapp. Klicka på den **”...”** på höger sida av din domän. Sedan kan du **avbryta köpet** eller **hantera domän**. Klicka på **hantera domän**, och vi kan binda **underdomän** till webbappen på **hantera domän** bladet.
   
    ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-6.png)
   
    När konfigurationen är klar visas det anpassade domännamnet i den **värdnamnsbindningar** delen av din webbapp.

Du bör nu att kunna ange det anpassade domännamnet i din webbläsare och se att det har tar dig till din webbapp.

