---
title: Vanliga frågor och svar om Azure-trafik analytics | Microsoft Docs
description: Få svar på några av de vanligaste frågorna om trafik analytics.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2018
ms.author: jdial
ms.openlocfilehash: f7e456c76dcf67a40777e32b100b900b859e210e
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34736804"
---
# <a name="traffic-analytics-frequently-asked-questions"></a>Vanliga och frågor svar om trafik analytics

1.  Vilka är kraven att använda trafik analytics?

    Trafik Analytics kräver följande förutsättningar:

    - En prenumeration Nätverksbevakaren aktiverad
    - NSG-flöde loggar aktiverad för NSG: er som du vill övervaka
    - Ett Azure Storage-konto för lagring av rådata flog loggar
    - En arbetsyta med läs- och skrivbehörighet logganalys (OMS)
    - Ditt konto ha tilldelats med någon av följande behörigheter på prenumerationsnivån:
    
            All permissions *
            All Read permissions */read
            All network permissions Microsoft.Network/*
            All network read permissions Microsoft.Network/*/read

    Eller ditt konto ha tilldelats med följande alla åtgärder på prenumerationsnivån: 

        - Microsoft.Network/applicationGateways/read
        - Microsoft.Network/connections/read
        - Microsoft.Network/loadBalancers/read 
        - Microsoft.Network/localNetworkGateways/read 
        - Microsoft.Network/networkInterfaces/read 
        - Microsoft.Network/networkSecurityGroups/read 
        - Microsoft.Network/publicIPAddresses/read
        - Microsoft.Network/routeTables/read
        - Microsoft.Network/virtualNetworkGateways/read 
        - Microsoft.Network/virtualNetworks/read

2.  Vilka Azure-regioner är trafik analytics?

    I förhandsversionen kan du använda trafik analytics för NSG: er i något av följande **regioner som stöds**: Väst centrala USA, östra USA, östra USA 2, norra centrala USA, södra centrala USA, centrala USA, västra USA, västra USA 2, Västeuropa, Norra Europa , Västra Storbritannien, Syd Storbritannien, Östra Australien och Australien sydost. Logganalys-arbetsytan måste finnas i i West centrala USA, östra USA, västra Europa, Östra Australien, sydost eller Syd Storbritannien region.

3.  Kan NSG: er som jag aktivera flödet loggar för finnas i olika regioner än OMS-arbetsytan?

    Ja

4.  Kan flera NSG: er konfigureras i en enda arbetsyta?

    Ja

5.  Kan jag använda en befintlig OMS-arbetsyta?

    Ja, om du väljer en befintlig arbetsyta, kontrollera att den har migrerats till det nya språket i fråga. Om du inte vill uppgradera arbetsytan; Du måste skapa en ny. Mer information om nya frågespråket finns [Azure logganalys uppgradera till ny logg sökning](../log-analytics/log-analytics-log-search-upgrade.md).

6.  Kan min Azure Storage-konto i en prenumeration och min OMS-arbetsyta vara i en annan prenumeration?

    Ja

7.  Kan jag lagra loggarna i olika Lagringskonto i en annan prenumeration?

    Nej. Du kan lagra loggarna i alla storage-konto där en NSG är aktiverat för flöde loggar, men både storage-konto och på loggarna måste vara i samma prenumeration och region.

8.  Om jag får felmeddelandet ”kunde inte hittas” när du konfigurerar en NSG för analys av trafik, hur kan jag lösa det.?

    Välj en region som stöds anges i fråga 2. Om du väljer en område som inte stöds visas felmeddelandet ”kunde inte hittas”.

9.  Under NSG flödet loggar jag får NSG status som ”det gick inte att läsa in”, vad du ska göra?

    Providern Microsoft.Insights måste registreras för flöde loggning för att fungera korrekt. Om du inte är säker på om Microsoft.Insights-provider har registrerats eller inte för din prenumeration, Ersätt *xxxxx-xxxxx-xxxxxx-xxxx* i följande kommando och kör sedan följande kommandon från PowerShell:

    ```powershell-interactive
    **Select-AzureRmSubscription** -SubscriptionId xxxxx-xxxxx-xxxxxx-xxxx
    **Register-AzureRmResourceProvider** -ProviderNamespace Microsoft.Insights
    ```

10. Jag har konfigurerat lösningen. Varför inte visas något på instrumentpanelen?

    Instrumentpanelen kan ta upp till 30 minuter att visas första gången. Lösningen måste först aggregera tillräckligt med data att härleda meningsfulla insikter innan några rapporter genereras. 

11.  Om följande meddelande: ”Vi kunde inte hitta några data i den här arbetsytan för valda tidsintervallet. Försök att ändra tidsintervallet eller välj en annan arbetsyta ”, hur löser jag det?

        Prova följande alternativ:
        - Försök att ändra tidsintervallet i det övre fältet.
        - Välj en annan Log Analytics-arbetsyta i den övre stapeln
        - Försök att öppna trafik Analytics efter 30 minuter om det nyligen har aktiverats
    
        Om problem kvarstår höja frågor i den [röst användarforum](https://feedback.azure.com/forums/217313-networking?category_id=195844).

12.  Om följande meddelande: ”1) analysera din NSG trafikflödet loggar för första gången. Den här processen kan ta 20 – 30 minuter att slutföra. Kontrollera igen efter en stund. 2) om steget ovan fungerar inte och din arbetsyta är under den kostnadsfria SKU: N och sedan kontrollera din användning av arbetsytan här för att verifiera över kvoten, eller Läs i vanliga frågor och svar för ytterligare information ”, hur löser jag det?

        Du får felet av följande skäl:
        - Trafik analytics kan har nyligen ändrats och kan inte ännu har sammanställd tillräckligt med data att härleda insikter som beskrivande.
        - Din OMS-arbetsyta är under den kostnadsfria SKU: N och det utsatts för intrång kvotgränserna. I det här fallet kan du behöva använda en arbetsyta i en SKU med större kapacitet.
    
        Om problem kvarstår höja frågor i den [röst användarforum](https://feedback.azure.com/forums/217313-networking?category_id=195844).
    
13.  Om följande meddelande: ”verkar som om vi har resursdata (topologin) och ingen information om flöden. Under tiden Klicka här om du vill se resursdata och ytterligare information finns i vanliga frågor och svar ”, hur löser jag det?

        Du ser resurser information på instrumentpanelen; dock finns inga flödet-relaterad statistik. Data finns inte på grund av ingen kommunikation som flödar mellan resurser. Vänta 60 minuter och kontrollera status. Om du är säker på att kommunikationsflöden mellan resurserna finns, och sedan öka frågor i den [röst användarforum](https://feedback.azure.com/forums/217313-networking?category_id=195844).

14. Kan jag konfigurera trafik analytics med hjälp av PowerShell eller en mall för Azure Resource Manager?

    Nej, trafik analytics endast konfigureras med Azure-portalen.

15.  Hur prissätts trafik analytics?

        Trafik analytics förbrukade för att utöka minskade loggar och lagra förbättrad loggar i logganalys-arbetsytan. Under förhandsgranskning, debiteras trafiken analytics inte för att utöka minskade loggarna, men förvaring av data i en arbetsyta regleras fakturering på publicerade priser. Det här svaret kommer att uppdateras när priser för trafik analytics är tillgänglig.

16.  Hur kan jag navigera på tangentbordet i Geo kartvyn?

        Sidan geo-mappning innehåller två huvudavsnitt:
    
        - **Banderoll**: banderoll placeras högst upp i kartan Geo ger möjlighet att välja trafikfilter distribution via knappar som Deployment/No distribution/Active/Inactive/trafik Analytics aktiverad/trafik Analytics inte aktiverat/trafik från länder/Benign/skadliga/tillåtna skadlig trafik och förklaringen information. Valet av definierade knappar tillämpas motsvarande filter på kartan, t.ex. Om en användare väljer knappen ”aktiv” filter under banderollen, och sedan kartan visar ”aktiv” datacenter i distributionen.
        - **Kartan**: I kartan avsnittet placeras under banderollen visar Trafikfördelningen mellan Azure-datacenter och länder.
    
        **Tangentbordsnavigering på banderoll**
    
        - Som standard är val på sidan geo-mappning för popup-meddelandet ”Azure domänkontrollanter”-knappen.
        - Om du vill navigera till en annan filter-knapp som du kan använda den `Tab` eller `Right arrow` för att flytta nästa. Om du vill navigera bakåt kan du använda antingen `Shift+Tab` eller `Left arrow` nyckel. Navigering framåt riktning prioritet från vänster till höger, följt av längst upp och ned.
        - Tryck på den `Enter` eller `Down` för att använda det valda filtret. Utifrån filterval och distribution av markeras en eller flera noder under avsnittet kartan.
            - Växla mellan **banderoll** och **kartan**, tryck på `Ctrl+F6`.
        
        **Tangentbordsnavigering på kartan**
    
        - När du har valt filter i popup-meddelandet och trycks ned `Ctrl+F6`, fokus flyttas till en av de markerade noderna (**Azure-datacenter** eller **Land/Region**) i kartvyn.
        - Navigera till andra noder som är markerade i kartan du kan använda den `Tab` eller `Right arrow` nycklar för flyttning framåt, och `Shift+Tab` eller `Left arrow` nyckel för bakåt rörelse.
        - Markera alla markerade noder i kartan med hjälp av `Enter` eller `Down arrow` nyckel.
        - På val av dessa noder flyttar fokus till den **Information verktygslådan** för noden. Som standard fokus flyttas till knappen stängd den **Information verktygslådan**. Ytterligare navigera i **rutan** visa använder `Right` och `Left arrow` för att gå framåt och bakåt, respektive. När du trycker på `Enter` har samma effekt som att välja knappen fokuserad i den **Information verktygslådan**.
        - När du trycker på `Tab` när fokus ligger på den **Information verktygslådan**, fokus flyttas till slutpunkter i samma kontinent som den markerade noden. Du kan använda den `Right` och `Left arrow` för att navigera till dessa slutpunkter.
        - Om du vill navigera till andra flödet slutpunkter/kontinenter klustret använder `Tab` för flyttning framåt och `Shift+Tab` för bakåt rörelse.
        - När fokus ligger på `Continent clusters`, använda den `Enter` eller `Down` för att markera slutpunkterna i det kontinent klustret. För att navigera genom slutpunkter och knappen Stäng i informationsrutan i det kontinent klustret, kan du använda antingen den `Right` eller `Left arrow` nyckel för framåt och bakåt flytt respektive. Du kan använda på valfri slutpunkt `Shift+L` växla till raden anslutning från den markerade noden till slutpunkten. När du trycker på `Shift+L` igen navigerar du till den valda slutpunkten.
        
        När som helst:
    
        - `ESC` döljer expanderade markeringen.
        - Den `UP Arrow` nyckel utför samma åtgärd som `ESC`. Den `Down arrow` nyckel utför samma åtgärd som `Enter`.
        - Använd `Shift+Plus` att zooma in, och `Shift+Minus` att zooma ut.
