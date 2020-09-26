---
title: Skapa för hands version av anslutnings övervakare – Azure Portal
titleSuffix: Azure Network Watcher
description: Lär dig hur du skapar anslutnings övervakaren (för hands version) med hjälp av Azure Portal.
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/30/2020
ms.author: vinigam
ms.openlocfilehash: b783a1434ebf6f8e1ad645d69f9f54a4c9c03e7f
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91363020"
---
# <a name="create-a-connection-monitor-preview-using-the-azure-portal"></a>Skapa en anslutnings övervakare (för hands version) med hjälp av Azure Portal

Lär dig hur du skapar anslutnings övervakaren (för hands version) för att övervaka kommunikation mellan dina resurser med hjälp av Azure Portal. Det stöder hybrid-och Azure Cloud-distributioner.

## <a name="before-you-begin"></a>Innan du börjar 

I anslutnings Övervakare som du skapar i anslutnings övervakaren (för hands version) kan du lägga till både lokala datorer och virtuella Azure-datorer som källor. Dessa anslutnings övervakare kan också övervaka anslutningar till slut punkter. Slut punkterna kan vara på Azure eller någon annan URL eller IP-adress.

Anslutnings övervakaren (för hands version) innehåller följande entiteter:


* **Anslutnings övervaknings resurs** – en regions-/regionsspecifika Azure-resurs. Alla följande entiteter är egenskaper för en anslutnings övervaknings resurs.
* **Slut punkt** – en källa eller ett mål som deltar i anslutnings kontroller. Exempel på slut punkter är 
    * Virtuella Azure-datorer
    * Azure-virtuella nätverk
    * Azure-undernät
    * Lokala agenter
    * Lokala undernät
    * Lokalt anpassat nätverk som består av flera undernät
    * URL: er och IP-adresser 
* **Test konfiguration** – en plattformsspecifik konfiguration för ett test. Utifrån det protokoll du väljer kan du definiera port, tröskelvärden, test frekvens och andra parametrar.
* **Test grupp** – gruppen som innehåller käll slut punkter, mål slut punkter och testkonfigurationer. En anslutnings övervakare kan innehålla fler än en test grupp.
* **Test** – kombinationen av en käll slut punkt, mål slut punkt och test konfiguration. Ett test är den mest detaljerade nivån där övervaknings data är tillgängliga. Övervaknings data innehåller procent andelen kontroller som misslyckades och tur och retur-tiden.

    ![Diagram som visar en anslutnings Övervakare som definierar relationen mellan test grupper och tester](./media/connection-monitor-2-preview/cm-tg-2.png)

## <a name="steps-to-create"></a>Steg för att skapa

Om du vill skapa en anslutnings övervakare (för hands version) med Azure Portal följer du stegen nedan:

1. På Azure Portal start sida går du till **Network Watcher**.
1. Till vänster i avsnittet **övervakning** väljer du **anslutnings övervakare (för hands version)**.
1. Du ser alla anslutnings Övervakare som skapades i anslutnings övervakaren (förhands granskning). Om du vill se de anslutnings Övervakare som skapades i den klassiska upplevelsen av anslutnings övervakaren går du till fliken **anslutnings övervakare** .

    ![Skärm bild som visar anslutnings Övervakare som har skapats i anslutnings övervakaren (förhands granskning)](./media/connection-monitor-2-preview/cm-resource-view.png)   
    
1. På instrument panelen för **anslutnings övervakaren (förhands granskning)** i det övre vänstra hörnet väljer du **skapa**.

   ![Skärm bild som visar fliken grundläggande i anslutnings övervakaren](./media/connection-monitor-2-preview/create-cm-basics.png)

1. På fliken **grundläggande** anger du information för anslutnings övervakaren:
   * **Namn på anslutnings övervakare** – Lägg till namnet på anslutnings övervakaren. Använd standard namngivnings reglerna för Azure-resurser.
   * **Prenumeration** – Välj en prenumeration för anslutnings övervakaren.
   * **Region** – Välj en region för anslutnings övervakaren. Du kan bara välja de virtuella käll datorer som skapas i den här regionen.
   * **Konfiguration av arbets yta** – din arbets yta innehåller dina övervaknings data. Du kan använda en anpassad arbets yta eller standard arbets ytan. 
       * Markera kryss rutan om du vill använda standard arbets ytan. 
       * Om du vill välja en anpassad arbets yta avmarkerar du kryss rutan. Välj sedan prenumeration och region för din anpassade arbets yta. 
1. Längst ned på fliken väljer du **Nästa: test grupper**.

   ![Skärm bild som visar fliken Skapa test grupp i anslutnings övervakaren](./media/connection-monitor-2-preview/create-tg.png)

1. Lägg till källor, destinationer och testa konfigurationer i dina test grupper. Information om hur du konfigurerar test grupper finns i [skapa test grupper i anslutnings övervakaren](#create-test-groups-in-a-connection-monitor). 
1. Längst ned på fliken väljer du **Nästa: skapa aviseringar**.

   ![Skärm bild som visar fönstret där du skapar aviseringar](./media/connection-monitor-2-preview/create-alert.png)

1. Information om hur du skapar aviseringar finns [i skapa aviseringar i anslutnings övervakaren](#create-alerts-in-connection-monitor)
1. Längst ned på fliken väljer du **Nästa: granska + skapa**.

  ![Skärm bild av anslutnings övervakaren som visar fliken Granska + skapa](./media/connection-monitor-2-preview/review-create-cm.png)

1. På fliken **Granska + skapa** granskar du grundläggande information och test grupper innan du skapar anslutnings övervakaren. Om du behöver redigera anslutnings övervakaren:
   * Om du vill redigera grundläggande information väljer du Penn ikonen.
   * Om du vill redigera en test grupp väljer du den.

   > [!NOTE] 
   > På fliken **Granska + skapa** visas kostnaden per månad under förhands gransknings fasen för anslutnings övervakaren. För närvarande visar kolumnen **aktuell kostnad** ingen avgift. När anslutnings övervakaren blir allmänt tillgänglig visas en månatlig avgift i den här kolumnen. 
   > 
   > Även i förhands gransknings fasen av anslutnings övervakaren gäller Log Analytics inmatnings avgifter.

1. När du är redo att skapa anslutnings övervakaren väljer du **skapa**längst ned på fliken **Granska + skapa** .

Anslutnings övervakaren (för hands version) skapar anslutnings övervaknings resursen i bakgrunden.

## <a name="create-test-groups-in-a-connection-monitor"></a>Skapa test grupper i en anslutnings övervakare

Varje test grupp i en anslutnings övervakare innehåller källor och mål som testas på nätverks parametrar. De testas för procent andelen kontroller som inte kan utföras och sökning efter testkonfigurationer.

Skapa en test grupp i en anslutnings övervakare genom att ange värden för följande fält från Azure Portal:

* **Inaktivera test grupp** – du kan välja det här fältet om du vill inaktivera övervakning för alla källor och mål som test gruppen anger. Detta val rensas som standard.
* **Namn** – ge test gruppen ett namn.
* **Källor** – du kan ange både virtuella Azure-datorer och lokala datorer som källor om agenter är installerade på dem. Information om hur du installerar en agent för din källa finns i [Installera övervaknings agenter](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#install-monitoring-agents).
   * Välj Azure-agenter genom att välja fliken **Azure-slutpunkter** . Här ser du bara virtuella datorer som är kopplade till den region som du angav när du skapade anslutnings övervakaren. Som standard grupperas de virtuella datorerna i den prenumeration som de tillhör. De här grupperna är komprimerade. 
   
       Du kan öka detalj nivån från prenumerations nivån till andra nivåer i hierarkin:

      **Prenumeration**  >  **Resurs grupper**  >  **Virtuella nätverk**  >  **Undernät**  >  **Virtuella datorer med agenter**

      Du kan också ändra värdet för fältet **Gruppera efter** för att starta trädet från en annan nivå. Om du till exempel grupperar efter virtuellt nätverk ser du de virtuella datorer som har agenter i hierarkin **virtuella nätverk**  >  **undernät**  >  **med agenter**.
       Om du väljer ett virtuellt nätverk, undernät eller en enskild virtuell dator anges motsvarande resurs-ID som slut punkt. Som standard deltar alla virtuella datorer i det valda VNET eller under nätet med Network Watcher tillägget i övervakning. Du kan minska omfattningen genom att antingen välja vissa undernät/agenter eller ändra värdet för egenskapen omfattning. 

      ![Skärm bild av anslutnings övervakaren, som visar panelen Lägg till källor och fliken Azure-agenter](./media/connection-monitor-2-preview/add-azure-sources.png)

   * Välj fliken **ej – Azure-agenter** om du vill välja lokala agenter. Som standard grupperas agenter i arbets ytor efter region. Den Övervakare av nätverksprestanda-lösningen har kon figurer ATS för alla dessa arbets ytor. 
   
       Om du behöver lägga till Övervakare av nätverksprestanda i din arbets yta kan du hämta den från [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview). Information om hur du lägger till Övervakare av nätverksprestanda finns [i övervaknings lösningar i Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/solutions). 
   
       I vyn **skapa anslutnings övervakare** , på fliken **grundläggande** , väljs standard region. Om du ändrar region kan du välja agenter från arbets ytor i den nya regionen.  Du kan välja en eller flera agenter eller undernät. I vyn undernät kan du välja vissa IP-adresser för övervakning. Om du lägger till flera undernät skapas ett anpassat lokalt nätverk, nämligen "OnPremises_Network_1". Du kan också ändra värdet för fältet **Gruppera efter** för att gruppera efter undernät.

      ![Skärm bild av anslutnings övervakaren, som visar panelen Lägg till källor och fliken icke-Azure-agenter](./media/connection-monitor-2-preview/add-non-azure-sources.png)

   * Du kan också välja nyligen använda slut punkter med hjälp av fliken **senaste slut punkt** 
   
   * När du har konfigurerat källorna väljer du **klar**. Du kan fortfarande redigera grundläggande egenskaper som slut punkts namn genom att klicka på slut punkten i vyn Skapa test grupp. 

* **Destinationer** – du kan övervaka anslutningar till virtuella Azure-datorer, lokala datorer eller slut punkter (en offentlig IP-adress, URL eller FQDN) genom att ange dem som mål. I en enda test grupp kan du lägga till virtuella Azure-datorer, lokala datorer, Office 365-URL: er, Dynamics 365-URL: er och anpassade slut punkter.

    * Välj fliken **Azure-slutpunkter** om du vill välja virtuella Azure-datorer som mål. Som standard grupperas de virtuella Azure-datorerna i en prenumerationsbegäran i samma region som du valde i vyn **skapa anslutnings övervakare** på fliken **grundläggande** . Du kan ändra region och välja virtuella Azure-datorer från den nyligen valda regionen. Sedan kan du öka detalj nivån från prenumerations nivån till andra nivåer i hierarkin, precis som källan Azure-slutpunkter.
     Du kan välja virtuella nätverk, undernät eller enskilda virtuella datorer, på samma sätt som Azure-slutpunkter. Om du väljer ett virtuellt nätverk, undernät eller en enskild virtuell dator anges motsvarande resurs-ID som slut punkt. Som standard deltar alla virtuella datorer i det valda VNET eller under nätet med Network Watcher tillägget i övervakning. Du kan minska omfattningen genom att antingen välja vissa undernät/agenter eller ändra värdet för egenskapen omfattning. 

       ![Skärm bild av fönstret Lägg till destinationer, som visar fliken virtuella Azure-datorer](./media/connection-monitor-2-preview/add-azure-dests1.png)

       ![Skärm bild av fönstret Lägg till destinationer som visar prenumerations nivån](./media/connection-monitor-2-preview/add-azure-dests2.png)
    
    * Välj fliken icke-Azure-slutpunkter om du vill välja icke-Azure-agenter som mål. Som standard grupperas agenter i arbets ytor efter region. Den Övervakare av nätverksprestanda-lösningen har kon figurer ATS för alla dessa arbets ytor. Om du behöver lägga till Övervakare av nätverksprestanda i din arbets yta kan du hämta den från Azure Marketplace. Information om hur du lägger till Övervakare av nätverksprestanda finns i övervaknings lösningar i Azure Monitor. I vyn Skapa anslutnings övervakare, på fliken grundläggande, väljs standard region. Om du ändrar region kan du välja agenter från arbets ytor i den nya regionen. Du kan välja en eller flera agenter eller undernät. I vyn undernät kan du välja vissa IP-adresser för övervakning. Om du lägger till flera undernät skapas ett anpassat lokalt nätverk, nämligen "OnPremises_Network_1".  
    
     ![Skärm bild av fönstret Lägg till icke-Azure-mål](./media/connection-monitor-2-preview/add-non-azure-dest.png)
    
    * Välj fliken **externa adresser** om du vill välja offentliga slut punkter som mål. I listan över slut punkter ingår Office 365-test-URL: er och Dynamics 365-test-URL: er, grupperat efter namn. Förutom dessa slut punkter kan du välja en slut punkt som skapades i andra test grupper i samma anslutnings övervakare. 
    
        Om du vill lägga till en ny slut punkt går du till det övre högra hörnet och väljer **+ slut punkter**. Ange sedan ett slut punkts namn och en URL, IP eller FQDN.

       ![Skärm bild som visar var du kan lägga till offentliga slut punkter som mål i anslutnings övervakaren](./media/connection-monitor-2-preview/add-endpoints.png)

    * Om du vill välja nyligen använda slut punkter går du till fliken **senaste slut punkter**   .
    * När du är klar med att välja mål väljer du **klar**. Du kan fortfarande redigera grundläggande egenskaper som slut punkts namn genom att klicka på slut punkten i vyn Skapa test grupp. 

* **Testa konfigurationer** – du kan associera en eller flera testkonfigurationer i en test grupp. Skapa en ny test konfiguration med hjälp av fliken "ny konfiguration" eller Använd en test konfiguration som används i andra test grupper i samma anslutnings övervakare via fliken "Välj befintlig".

    * **Namn** – ge test konfigurationen ett namn.
    * **Protokoll** – Välj TCP, ICMP eller http. Om du vill ändra HTTP till HTTPS väljer du **http** som protokoll och väljer **443** som port.
        * **Skapa nätverks test konfiguration** – den här kryss rutan visas bara om du väljer **http** i fältet **protokoll** . Markera den här rutan om du vill skapa en annan test konfiguration som använder samma källor och mål som du har angett någon annan stans i konfigurationen. Den nyligen skapade test konfigurationen heter `<the name of your test configuration>_networkTestConfig` .
        * **Inaktivera traceroute** – det här fältet gäller när protokollet är TCP eller ICMP. Markera den här rutan om du vill stoppa källor från att upptäcka topologi och hitta hopp efter hopp.
    * **Målport** – du kan anpassa det här fältet med en valfri mål Port.
        * Lyssna på port – det här fältet gäller när protokollet är TCP. Markera den här rutan om du vill öppna den valda TCP-porten om den inte redan är öppen. 
    * **Test frekvens** – Använd det här fältet för att välja hur ofta källor ska pinga mål på det protokoll och den port som du har angett. Du kan välja 30 sekunder, 1 minut, 5 minuter, 15 minuter eller 30 minuter. Välj anpassad om du vill ange en valfri frekvens mellan 30 sekunder och 30 minuter. Källorna kommer att testa anslutningen till destinationer utifrån det värde som du väljer.  Om du till exempel väljer 30 sekunder kommer källorna att kontrol lera anslutningen till målet minst en gång under en 30-sekunders period.
    * **Tröskelvärde för lyckad** – du kan ange tröskelvärden för följande nätverks parametrar:
       * **Misslyckade kontroller** – ange procent andelen kontroller som kan Miss lyckas när källor kontrollerar anslutningen till destinationer genom att använda de villkor som du har angett. Procent andelen misslyckade kontroller i TCP-eller ICMP-protokollet kan likställas med procent andelen av paket förlusten. För HTTP-protokoll representerar det här fältet den procent andel av HTTP-begäranden som inte fick något svar.
       * **Tur och retur-tid** – Ställ in efter hur lång tid det tar att ansluta till målet via test konfigurationen.
    
       ![Skärm bild som visar var du konfigurerar en test konfiguration i anslutnings övervakaren](./media/connection-monitor-2-preview/add-test-config.png)
       
## <a name="create-alerts-in-connection-monitor"></a>Skapa aviseringar i anslutnings övervakaren

Du kan konfigurera aviseringar om tester som inte fungerar baserat på de tröskelvärden som angetts i testkonfigurationerna.   

Skapa aviseringar i en anslutnings övervakare genom att ange värden för följande fält från Azure Portal: 

- Skapa avisering – du kan välja det här fältet om du vill skapa en mått avisering i Azure Monitor. Om du väljer detta aktive ras de andra fälten för redigering. Ytterligare avgifter för aviseringen gäller utifrån [prissättningen för aviseringar](https://azure.microsoft.com/pricing/details/monitor/) 

- Omfattning – > resurs och omfattning – > hierarki – detta är förifyllt baserat på de värden som anges i fliken grundläggande 

- Villkor – > aviseringen skapas på måttet "test resultat (förhands granskning)". När test resultatet av anslutnings övervakaren misslyckades kommer varnings regeln att aktive ras. 

- Åtgärds grupp – du kan välja att ange din e-postadress direkt eller så kan du välja att skapa aviseringar via åtgärds grupper. Om du väljer att ange din e-postadress direkt skapas en åtgärds grupp med namnet NPM e-ActionGroup och e-post-ID: t läggs till i den åtgärds gruppen. Om du väljer att använda åtgärds grupper måste du välja en tidigare skapad åtgärds grupp. Du kan lära dig hur du skapar en åtgärds grupp här. När aviseringen har skapats kan du använda länken Hantera aviseringar för att hantera dina aviseringar. 

- Varnings regel namn – namnet på anslutnings övervakaren 

- Aktivera regel vid skapande – Detta aktiverar varnings regeln baserat på villkoret. Inaktivera detta om du vill skapa regeln men inte aktivera den. 

    ![Skärm bild som visar hur du skapar en avisering i fönstret anslutnings övervakare](./media/connection-monitor-2-preview/create-alert-filled.png)

## <a name="scale-limits"></a>Skalnings gränser

Anslutnings övervakare har följande skalnings gränser:

* Högsta antal anslutnings övervakare per prenumeration per region: 100
* Högsta antal test grupper per anslutnings Övervakare: 20
* Högsta antal källor och mål per anslutnings Övervakare: 100
* Högsta antal testkonfigurationer per anslutnings Övervakare: 2 via Azure Portal

## <a name="next-steps"></a>Nästa steg

* Lär dig [hur du analyserar övervaknings data och ställer in aviseringar](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#analyze-monitoring-data-and-set-alerts)
* Lär dig [hur du diagnostiserar problem i nätverket](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#diagnose-issues-in-your-network)
