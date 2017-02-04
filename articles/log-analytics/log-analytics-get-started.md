---
title: "Komma igång med Log Analytics | Microsoft Docs"
description: "Du kan komma igång med Log Analytics på bara några minuter."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 508716de-72d3-4c06-9218-1ede631f23a6
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/02/2017
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 49e624dd9bfc534fdbae25fd0c8646be36851635
ms.openlocfilehash: 4ab71b6ee09883abd4d095f2b1788cf69d44a219


---
# <a name="get-started-with-log-analytics"></a>Komma igång med Log Analytics
Du kan komma igång med Log Analytics i Microsoft Operations Management Suite (OMS) på bara några minuter. När du väljer hur du ska skapa en OMS-arbetsyta, vilket liknar ett konto, har du två alternativ:

* Webbplatsen Microsoft Operations Management Suite
* Microsoft Azure-prenumeration

Du kan skapa en kostnadsfri OMS-arbetsyta med hjälp av OMS-webbplats. Du kan också använda en Microsoft Azure-prenumeration för att skapa en kostnadsfri Log Analytics-arbetsyta. Arbetsytorna fungerar likadant oavsett hur de har skapats. Fria arbetsytor kan bara skicka 500 MB data per dag till OMS-tjänsten. Alla arbetsytor kräver en Azure-prenumeration, så du kan även använda din prenumeration för att få åtkomst till andra Azure-tjänster. Oavsett vilken metod som används för att skapa arbetsytan finns kan du skapa arbetsytan med ett Microsoft-konto eller organisationskonto.

Här är en titt på processen:

![förberedande diagram](./media/log-analytics-get-started/oms-onboard-diagram.png)

## <a name="log-analytics-prerequisites-and-deployment-considerations"></a>Log Analytics-krav och överväganden vid distribution
* Du behöver en betald prenumeration för Microsoft Azure för att helt kunna använda Log Analytics. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) som gäller under 30 dagar och som ger dig åtkomst till vilka Azure-tjänster du vill. Du kan också skapa ett kostnadsfritt OMS-konto på webbplatsen för [Operations Management Suite](http://microsoft.com/oms).
* Du måste skapa en ny arbetsyta
* Varje Windows-datorer som du vill samla in data från måste köra Windows Server 2008 SP1 eller senare
* [Brandvägg](log-analytics-proxy-firewall.md)såtkomst till OMS-webbtjänstens adresser
* Bestäm om datorerna ska ha direkt Internetåtkomst. Om inte kräver de en gateway-server för att komma åt OMS-webbtjänstens platser. All åtkomst sker via HTTPS. Du kan konfigurera en [OMS Gateway](log-analytics-oms-gateway.md)-server som vidarebefordrar trafik från servrar till OMS, om Internetåtkomst inte är tillgänglig från datorerna.
* Om du använder Operations Manager har Log Analytics stöd för Operations Manager 2012 SP1 UR6 och senare samt Operations Manager 2012 R2 UR2 och senare. Stöd för proxy har lagts till i Operations Manager 2012 SP1 UR7 och Operations Manager 2012 R2 UR3. Bestäm hur den ska integreras med OMS.
* Bestäm vilka tekniker och servrar som ska skicka data till OMS. Exempelvis domänkontrollanter, SQL Server osv.
* Bevilja behörighet till användare i OMS och Azure.
* Om du är orolig för dataanvändning, distribuerar du varje lösning individuellt och testar prestandapåverkan innan du lägger till ytterligare lösningar.
* Granska din dataanvändning och prestanda när du lägger till funktioner och lösningar i Log Analytics. Detta inkluderar händelseinsamling, logginsamling, insamling av prestandadata osv. Det är bättre att börja med minimal insamling innan dataanvändning eller prestandapåverkan har identifierats.
* Kontrollera att Windows-agenter inte också hanteras med hjälp av Operations Manager, annars kan dubblettvärden uppstå. Detta gäller även för Azure-baserade agenter som har aktiverat Azure-diagnostik.
* När du har installerat agenter bör du kontrollera att agenten fungerar korrekt. Om inte, kontrollera att CNG (Cryptography API: Next Generation) Key Isolation inte inaktiveras med en grupprincip.
* Vissa Log Analytics-lösningar har ytterligare krav

## <a name="sign-up-in-3-steps-using-oms"></a>Registrera dig i 3 steg med hjälp av OMS
1. Gå till webbplatsen för [Operations Management Suite](http://microsoft.com/oms). Logga in med det Microsoft-konto, till exempel Outlook.com eller med ett organisationskonto från ditt företag eller din utbildningsinstitution, som ska användas med Office 365 eller andra Microsoft-tjänster.
2. Ange ett unikt arbetsytenamn. En arbetsyta är en logisk behållare där dina hanteringsdata lagras. Med den kan du partitionera data mellan olika team i organisationen, när datan endast finns på sin arbetsyta. Ange en e-postadress och den region där du vill att datan ska lagras.  
    ![skapa arbetsyta och länka prenumeration](./media/log-analytics-get-started/oms-onboard-create-workspace-link01.png)
3. Du kan sedan skapa en ny kostnadsfri Azure-prenumeration eller länka till en befintlig Azure-prenumeration.  
   ![skapa arbetsyta och länka prenumeration](./media/log-analytics-get-started/oms-onboard-create-workspace-link02.png)

Du är redo att komma igång med Operations Management Suite-portalen.

Du kan lära dig mer om att konfigurera din arbetsyta och länka befintliga Azure-konton till arbetsytor som skapats med Operations Management Suite i [Hantera arbetsytor](log-analytics-manage-access.md).

## <a name="sign-up-quickly-using-microsoft-azure"></a>Registrera dig snabbt med Microsoft Azure
1. Gå till [Azure Portal](https://portal.azure.com) och logga in, bläddra i listan över tjänster och välj sedan **Log Analytics**.  
    ![Azure Portal](./media/log-analytics-get-started/oms-onboard-azure-portal.png)
2. Klicka på **Lägg till** och välj alternativ för följande objekt:
   * Namn på **OMS-arbetsyta**
   * **Prenumeration** – Om du har flera prenumerationer väljer du den du vill associera med den nya arbetsytan.
   * **Resursgrupp**
   * **Plats**
   * **prisnivå**  
       ![snabbregistrering](./media/log-analytics-get-started/oms-onboard-quick-create.png)
3. Klicka på **OK**, så visas en lista över dina arbetsytor.
4. Välj en arbetsyta om du vill visa dess information i Azure Portal.       
    ![information om arbetsytan](./media/log-analytics-get-started/oms-onboard-workspace-details.png)         
5. Klicka på länken **OMS-portal** för att öppna webbplatsen Operations Management Suite med den nya arbetsytan.

Du är redo att börja använda Operations Management Suite-portalen.

Du kan lära dig mer om att konfigurera din arbetsyta och länka befintliga arbetsytor som skapats med Operations Management Suite till Azure-prenumerationer i [Hantera åtkomst till Log Analytics](log-analytics-manage-access.md).

## <a name="get-started-with-the-operations-management-suite-portal"></a>Komma igång med Operations Management Suite-portalen
Om du vill välja lösningar och ansluta servrar som du vill hantera, klickar du på ikonen **Inställningar** och följer anvisningarna i avsnittet.  

![komma igång](./media/log-analytics-get-started/oms-onboard-get-started.png)  

1. **Lägg till lösningar** – Visa dina installerade lösningar.  
    ![lösningar](./media/log-analytics-get-started/oms-onboard-solutions.png)  
    Klicka på **Besök galleriet** för att lägga till fler lösningar.  
    ![lösningar](./media/log-analytics-get-started/oms-onboard-solutions02.png)  
    Välj en lösning och klicka sedan på **Lägg till**.
2. **Ansluta en källa** – Välj hur du vill ansluta till din servermiljö för att samla in data:

   * Anslut en Windows Server eller en klient direkt genom att installera en agent.
   * Anslut Linux-servrar med OMS-agenten för Linux.
   * Använd ett Azure Storage-konto som konfigurerats med Windows eller Linux Azure-diagnostiska VM-tillägg.
   * Använd System Center Operations Manager om du vill ansluta dina hanteringsgrupper eller hela Operations Manager-distributionen.
   * Aktivera Windows-telemetri för att använda uppgraderingsanalys.
       ![Anslutna källor](./media/log-analytics-get-started/oms-onboard-data-sources.png)    
3. **Samla data** Konfigurera minst en datakälla för att lägga till data i din arbetsyta. När du är klar klickar du på **Spara**.    

    ![samla data](./media/log-analytics-get-started/oms-onboard-logs.png)    

## <a name="optionally-connect-windows-computers-by-installing-an-agent"></a>Du kan också ansluta Windows-datorer genom att installera en agent
I följande exempel visas hur du installerar en Windows-agent.

1. Klicka på panelen **Inställningar**, klicka sedan på fliken **Anslutna källor**, klicka på en flik för den typ av källa som du vill lägga till och antingen hämta en agent eller läs om hur du aktiverar en agent. Klicka till exempel på **Hämta Windows-agent (64-bitars)**. För Windows-agenter kan du bara installera agenten på Windows Server 2008 SP1 eller senare, samt på Windows 7 SP1 eller senare.
2. Installera agenten på en eller flera servrar. Du kan installera en agent i taget eller använda en mer automatiserad metod med ett [anpassat skript](log-analytics-windows-agents.md). Du kan också använda en befintlig distributionslösning för programvara som du kanske redan har.
3. När du har godkänt licensavtalet och valt installationsmapp väljer du **Anslut agenten till Azure Log Analytics (OMS)**.   
    ![agentinstallation](./media/log-analytics-get-started/oms-onboard-agent.png)
4. På nästa sida uppmanas du ange ditt arbetsyte-ID och arbetsytenyckel. Ditt arbetsyte-ID och nyckel visas på den skärm som du hämtade agentfilen till.  
    ![agentnycklar](./media/log-analytics-get-started/oms-onboard-mma-keys.png)  

    ![anslut servrar](./media/log-analytics-get-started/oms-onboard-key.png)
5. Under installationen kan du klicka på **Avancerat** om du vill konfigurera proxyservern och ange autentiseringsinformation. Klicka på knappen **Nästa** för att återgå till skärmen med arbetsytans information.
6. Klicka på **Nästa** för att verifiera ditt arbetsyte-ID och nyckel. Om det finns några fel kan du klicka på **Tillbaka** för att göra ändringar. När ditt arbetsyte-ID och nyckel verifierats, klickar du på **Installera** för att slutföra agentinstallationen.
7. Klicka på Microsoft Monitoring Agent > fliken Azure loggen Analytics (OMS) i kontrollpanelen. En grön bockikon visas när agenterna kommunicerar med Operations Management Suite-tjänsten. Första gången tar detta cirka 5–10 minuter.

> [!NOTE]
> Lösningar för kapacitetshantering och konfigurationskontroll stöds för närvarande inte av servrar som är direkt anslutna till Operations Management Suite.


Du kan också ansluta agenten till System Center Operations Manager 2012 SP1 och senare. Gör detta genom att välja **Anslut agenten till System Center Operations Manager**. När du väljer detta alternativ skickar du data till tjänsten utan att ytterligare maskinvara krävs eller att någon belastning görs på dina hanteringsgrupper.

Du kan läsa mer om att ansluta agenter till Operations Management Suite på [Anslut Windows-datorer till Log Analytics](log-analytics-windows-agents.md).

## <a name="optionally-connect-servers-using-system-center-operations-manager"></a>Du kan också ansluta servrar med System Center Operations Manager
1. I Operations Manager-konsolen väljer du **Administration**.
2. Expandera noden **Operational Insights** och välj sedan **Operational Insights-anslutning**.

   > [!NOTE]
   > Beroende på vilken samlad uppdatering av SCOM du använder, kan du se en nod för *System Center Advisor*, *Operational Insights* eller *Operations Management Suite*.
   >
   >
3. Klicka på länken **Registrera Operational Insights** överst till höger och följ instruktionerna.
4. När du har slutfört registreringsguiden klickar du på länken **Lägg till en dator/grupp**.
5. I dialogrutan **Datorsökning** kan du söka efter datorer eller grupper som övervakas av Operations Manager. Välj datorer eller grupper för att publicera dem i Log Analytics, klicka på **Lägg till** och klicka sedan på **OK**. Du kan kontrollera att OMS-tjänsten tar emot data genom att gå till ikonen **Användning** i Operations Management Suite-portalen. Datan bör visas i cirka 5–10 minuter.

Du kan läsa mer om att ansluta Operations Manager till Operations Management Suite i [Ansluta Operations Manager till Log Analytics](log-analytics-om-agents.md).

## <a name="optionally-analyze-data-from-cloud-services-in-microsoft-azure"></a>Du kan också analysera data från molntjänster i Microsoft Azure
Med Operations Management Suite kan du snabbt söka i händelser och IIS-loggar för molntjänster och virtuella datorer genom att aktivera diagnostik för Azure Cloud Services. Du kan också få fler funktioner för dina virtuella Azure-datorer genom att installera Microsoft Monitoring Agent. Du kan läsa mer om hur du konfigurerar Azure-miljön till att använda Operations Management Suite i [Anslut Azure-lagring till Log Analytics](log-analytics-azure-storage.md).

## <a name="next-steps"></a>Nästa steg
* [Lägg till Log Analytics-lösningar från lösningsgalleriet](log-analytics-add-solutions.md) för att lägga till funktioner och samla in data.
* Bekanta dig med [loggsökningar](log-analytics-log-searches.md) för att visa detaljerad information som samlas in av lösningar.
* Använd [instrumentpaneler](log-analytics-dashboards.md) att spara och visa dina egna anpassade sökningar.



<!--HONumber=Dec16_HO2-->


