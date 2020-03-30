---
title: Ansluta Operations Manager till Azure Monitor | Microsoft-dokument
description: För att underhålla din befintliga investering i System Center Operations Manager och använda utökade funktioner med Log Analytics, kan du integrera Operations Manager med ditt arbetsområde.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/13/2019
ms.openlocfilehash: 92b6737f48d8d8704f461c9adac92284b323b05f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274351"
---
# <a name="connect-operations-manager-to-azure-monitor"></a>Ansluta Operations Manager till Azure Monitor

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Om du vill behålla din befintliga investering i [System Center Operations Manager](https://docs.microsoft.com/system-center/scom/key-concepts?view=sc-om-1807) och använda utökade funktioner med Azure Monitor kan du integrera Operations Manager med din Log Analytics-arbetsyta. På så sätt kan du utnyttja möjligheterna med loggar i Azure Monitor samtidigt som du fortsätter att använda Operations Manager för att:

* Övervaka hälsotillståndet för dina IT-tjänster med Operations Manager
* Underhålla integrationen med dina ITSM-lösningar med stöd för hantering av incidenter och problem
* Hantera livscykeln för agenter som distribuerats lokalt och till virtuella IaaS-datorer i det offentliga molnet som du övervakar med Operations Manager

Integrering med System Center Operations Manager ger mervärde till din servicedriftstrategi genom att använda hastigheten och effektiviteten hos Azure Monitor för att samla in, lagra och analysera loggdata från Operations Manager. Azure Monitor-loggfrågor hjälper till att korrelera och arbeta för att identifiera fel på problem och ytbeläggningsfel till stöd för din befintliga problemhanteringsprocess. Flexibiliteten i frågemotorn för att undersöka prestanda, händelse- och varningsdata, med omfattande instrumentpaneler och rapporteringsfunktioner för att exponera dessa data på ett meningsfullt sätt, visar styrkan som Azure Monitor ger i att komplettera Operations Manager.

De agenter som rapporterar till Hanteringsgruppen för Operations Manager samlar in data från dina servrar baserat på de [logganalysdatakällor](agent-data-sources.md) och lösningar som du har aktiverat på arbetsytan. Beroende på vilka lösningar som är aktiverade skickas deras data antingen direkt från en Hanteringsserver för Operations Manager till tjänsten eller på grund av mängden data som samlas in i det agenthanterade systemet skickas direkt från agenten till en Log Analytics-arbetsyta. Hanteringsservern vidarebefordrar data direkt till tjänsten, de skrivs aldrig till den operativa databasen eller informationslagerdatabasen. När en hanteringsserver förlorar anslutningen till Azure Monitor cachelagras data lokalt tills kommunikationen återupprättas. Om hanteringsservern är offline på grund av planerat underhåll eller oplanerade avbrott återupptar en annan hanteringsserver i hanteringsgruppen anslutningen till Azure Monitor.  

Följande diagram visar anslutningen mellan hanteringsservrarna och agenter i en System Center Operations Manager-hanteringsgrupp och Azure Monitor, inklusive riktning och portar.

![oms-operations-manager-integration-diagram](./media/om-agents/oms-operations-manager-connection.png)

Om dina IT-säkerhetsprinciper inte tillåter att datorer i nätverket ansluter till Internet kan hanteringsservrar konfigureras för att ansluta till Log Analytics-gatewayen för att ta emot konfigurationsinformation och skicka insamlade data beroende på lösningarna Aktiverat. Mer information och anvisningar om hur du konfigurerar hanteringsgruppen för Operations Manager för att kommunicera via en Log Analytics-gateway till Azure Monitor finns i [Ansluta datorer till Azure Monitor med log Analytics-gatewayen](../../azure-monitor/platform/gateway.md).  

## <a name="prerequisites"></a>Krav

Innan du börjar bör du granska följande krav.

* Azure Monitor stöder endast System Center Operations Manager 2016 eller senare, Operations Manager 2012 SP1 UR6 eller senare och Operations Manager 2012 R2 UR2 eller senare. Stöd för proxy har lagts till i Operations Manager 2012 SP1 UR7 och Operations Manager 2012 R2 UR3.
* Integrera System Center Operations Manager 2016 med us Government moln kräver en uppdaterad Advisor management pack som ingår i Samlad uppdatering 2 eller senare. System Center Operations Manager 2012 R2 kräver ett uppdaterat Advisor-hanteringspaket som ingår i samlad uppdatering 3 eller senare.
* Alla Operations Manager-agenter måste uppfylla minimikraven. Kontrollera att agenter är på minsta uppdatering, annars windows agent kommunikation kan misslyckas och generera fel i Operations Manager händelseloggen.
* En Log Analytics-arbetsyta. Mer information finns i [Översikt över Logganalysarbetsyta](design-logs-deployment.md). 
* Du autentiserar till Azure med ett konto som är medlem i [rollen Log Analytics Contributor](manage-access.md#manage-access-using-azure-permissions).

* Regioner som stöds – Endast följande Azure-regioner stöds av System Center Operations Manager för att ansluta till en Log Analytics-arbetsyta:
    - USA, västra centrala
    - Sydöstra Australien
    - Europa, västra
    - USA, östra
    - Sydostasien
    - Japan, östra
    - Storbritannien, södra
    - Indien, centrala
    - Kanada, centrala
    - USA, västra 2

>[!NOTE]
>De senaste ändringarna i Azure-API:er förhindrar att kunder kan konfigurera integreringen mellan sin hanteringsgrupp och Azure Monitor för första gången. För kunder som redan har integrerat sin hanteringsgrupp med tjänsten påverkas du inte om du inte behöver konfigurera om din befintliga anslutning.  
>Ett nytt hanteringspaket har släppts för följande versioner av Operations Manager:
> - För System Center Operations Manager 2019 ingår det här hanteringspaketet i källmediet och installeras under installationen av en ny hanteringsgrupp eller under en uppgradering.
>- Operations Manager 1801 management pack gäller även för Operations Manager 1807.
>- För System Center Operations Manager 1801 laddar du ned hanteringspaketet [härifrån](https://www.microsoft.com/download/details.aspx?id=57173).
>- För System Center 2016 - Operations Manager laddar du ner hanteringspaketet [härifrån](https://www.microsoft.com/download/details.aspx?id=57172).  
>- För System Center Operations Manager 2012 R2 laddar du ned hanteringspaketet [härifrån](https://www.microsoft.com/download/details.aspx?id=57171).  


### <a name="network"></a>Nätverk

Informationen nedan visar den proxy- och brandväggskonfigurationsinformation som krävs för operationshanterarens agent, hanteringsservrar och driftkonsolen för att kommunicera med Azure Monitor. Trafik från varje komponent är utgående från nätverket till Azure Monitor.

|Resurs | Portnummer| Kringgå HTTPS-kontroll|  
|---------|------|-----------------------|  
|**Agent**|||  
|\*.ods.opinsights.azure.com| 443 |Ja|  
|\*.oms.opinsights.azure.com| 443|Ja|  
|\*.blob.core.windows.net| 443|Ja|  
|\*.azure-automation.net| 443|Ja|  
|**Hanteringsserver**|||  
|\*.service.opinsights.azure.com| 443||  
|\*.blob.core.windows.net| 443| Ja|  
|\*.ods.opinsights.azure.com| 443| Ja|  
|*.azure-automation.net | 443| Ja|  
|**Operations Manager-konsolen till Azure Monitor**|||  
|service.systemcenteradvisor.com| 443||  
|\*.service.opinsights.azure.com| 443||  
|\*.live.com| 80 och 443||  
|\*.microsoft.com| 80 och 443||  
|\*.microsoftonline.com| 80 och 443||  
|\*.mms.microsoft.com| 80 och 443||  
|login.windows.net| 80 och 443||  
|portal.loganalytics.io| 80 och 443||
|api.loganalytics.io| 80 och 443||
|docs.loganalytics.io| 80 och 443||  

### <a name="tls-12-protocol"></a>TLS 1.2-protokoll

För att försäkra säkerheten för data under överföring till Azure Monitor rekommenderar vi starkt att du konfigurerar agent- och hanteringsgruppen så att den använder minst TLS (Transport Layer Security) 1.2. Äldre versioner av TLS/Secure Sockets Layer (SSL) har visat sig vara sårbara och medan de fortfarande arbetar för att tillåta bakåtkompatibilitet rekommenderas de **inte**. Mer information finns i [Skicka data på ett säkert sätt med TLS 1.2](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12).

## <a name="connecting-operations-manager-to-azure-monitor"></a>Ansluta Operations Manager till Azure Monitor

Utför följande steg för att konfigurera hanteringsgruppen för Operations Manager för att ansluta till en av dina Log Analytics-arbetsytor.

Vid den första registreringen av hanteringsgruppen för Operations Manager med en Log Analytics-arbetsyta är alternativet att ange proxykonfigurationen för hanteringsgruppen inte tillgängligt i driftkonsolen.  Hanteringsgruppen måste registrerats med tjänsten innan det här alternativet är tillgängligt.  För att komma runt detta måste du uppdatera systemproxykonfigurationen med Netsh på det system som körs driftkonsolen från för att konfigurera integrering och alla hanteringsservrar i hanteringsgruppen.  

1. Öppna en upphöjd kommandotolk.
   a. Gå till **Start** och skriv **cmd**.
   b. Högerklicka på **Kommandotolken** och välj Kör som administratör**.
1. Ange följande kommando och tryck på **Enter**:

    `netsh winhttp set proxy <proxy>:<port>`

När du har slutfört följande steg för att integrera `netsh winhttp reset proxy` med Azure Monitor kan du ta bort konfigurationen genom att köra och sedan använda alternativet **Konfigurera proxyserver** i driftkonsolen för att ange proxy- eller Logganalysgatewayservern.

1. Välj arbetsytan **Administration** i Operations Manager-konsolen.
1. Expandera noden Operations Management Suite och klicka på **Anslutning**.
1. Klicka på länken för att **registrera till Operations Management Suite**.
1. På sidan **Guiden Operations Management Suite Onboarding: Autentisering** anger du e-postadress eller telefonnummer och lösenordet för administratörskontot som är kopplat till din OMS-prenumeration och klickar på **Logga in**.

   >[!NOTE]
   >Namnet på Operations Management Suite har dragits tillbaka.

1. När du har autentiserats uppmanas du att välja arbetsytan För Azure-klientorganisation, prenumeration och Logganalys på **sidan Drifthanteringspaket.** Om du har mer än en arbetsyta väljer du arbetsytan som du vill registrera med Operations Manager-hanteringsgruppen från listrutan och klickar på **Nästa**.

   > [!NOTE]
   > Operations Manager stöder bara en Log Analytics-arbetsyta i taget. Anslutningen och datorerna som registrerades på Azure Monitor med föregående arbetsyta tas bort från Azure Monitor.
   >
   >
1. På sidan **Guiden Operations Management Suite Onboarding: Sammanfattning** bekräftar du dina inställningar och om de är korrekta klickar du på **Skapa**.
1. På sidan **Guiden Operations Management Suite Onboarding: Slutför** klickar du på **Stäng**.

### <a name="add-agent-managed-computers"></a>Lägg till datorer som hanteras med agent

När du har konfigurerat integreringen med din Log Analytics-arbetsyta upprättas bara en anslutning till tjänsten, inga data samlas in från agenterna som rapporterar till din hanteringsgrupp. Detta sker inte förrän du har konfigurerat vilka specifika agenthanterade datorer som samlar in loggdata för Azure Monitor. Du kan antingen välja datorobjekt individuellt eller välja en grupp som innehåller Windows-datorobjekt. Du kan inte välja en grupp som innehåller instanser av en annan klass, till exempel logiska diskar eller SQL-databaser.

1. Öppna Operations Manager-konsolen och välj arbetsytan **Administration**.
1. Expandera noden Operations Management Suite och klicka på **Anslutning**.
1. Klicka på länken **Lägg till en dator/grupp** under rubriken Åtgärder på höger sida av fönstret.
1. I dialogrutan **Datorsökning** kan du söka efter datorer eller grupper som övervakas av Operations Manager. Välj datorer eller grupper, inklusive Operations Manager Management Server som ska finnas ombord på Azure Monitor, klicka på **Lägg till**och klicka sedan på **OK**.

Du kan visa datorer och grupper som konfigurerats för att samla in data från noden för hanterade datorer under Operations Management Suite i arbetsytan **Administration** i Operations-konsolen. Härifrån kan du lägga till eller ta bort datorer och grupper efter behov.

### <a name="configure-proxy-settings-in-the-operations-console"></a>Konfigurera proxyinställningar i Operations-konsolen

Utför följande steg om en intern proxyserver finns mellan hanteringsgruppen och Azure Monitor. Dessa inställningar hanteras centralt från hanteringsgruppen och distribueras till agenthanterade system som ingår i omfånget för att samla in loggdata för Azure Monitor.  Detta är fördelaktigt när vissa lösningar förbigår hanteringsservern och skickar data direkt till tjänsten.

1. Öppna Operations Manager-konsolen och välj arbetsytan **Administration**.
1. Expandera Operations Management Suite och klicka sedan på **Anslutningar**.
1. I vyn OMS-anslutning klickar du på **Konfigurera proxyserver**.
1. På sidan med **guiden Operations Management Suite: Proxyserver** väljer du alternativet för att **använda en proxyserver för att få åtkomst till Operational Management Suite** och anger sedan webbadressen med portnumret, till exempel http://corpproxy:80 och klickar på **Slutför**.

Om proxyservern kräver autentisering utför du följande steg för att konfigurera autentiseringsuppgifter och inställningar som behöver spridas till hanterade datorer som rapporterar till Azure Monitor i hanteringsgruppen.

1. Öppna Operations Manager-konsolen och välj arbetsytan **Administration**.
1. Under **Kör som-konfiguration** väljer du **Profiler**.
1. Öppna profilen **Kör som-profilproxy för System Center Advisor**.
1. I guiden Kör som-profil klickar du på Lägg till för att använda ett Kör som-konto. Du kan skapa ett [Kör som-konto](https://technet.microsoft.com/library/hh321655.aspx) eller använda ett befintligt konto. Det här kontot måste ha tillräcklig behörighet för att kunna passera genom proxyservern.
1. Ange vilket konto som ska hanteras genom att välja **En vald klass, grupp eller objekt**, klicka på **Välj...** och sedan klicka på **Grupp...** för att öppna **Gruppsökning**.
1. Sök efter och välj **Övervakning av servergrupp i Microsoft System Center Advisor**. Klicka på **OK** när du har valt grupp för att stänga rutan **Gruppsökning**.
1. Klicka på **OK** för att stänga rutan **Lägg till ett kör som-konto.**
1. Klicka på **Spara** för att slutföra guiden och spara dina ändringar.

När anslutningen har skapats och du konfigurerar vilka agenter som ska samla in och rapportera loggdata till Azure Monitor, tillämpas följande konfiguration i hanteringsgruppen, inte nödvändigtvis i ordning:

* Kör som-kontot **Microsoft.SystemCenter.Advisor.RunAsAccount.Certificate** skapas. Det är associerat med Kör som-profilen **Microsoft System Center Advisor Run As Profile Blob** (Microsoft System Center Advisor Kör som-profilblob) och har två klasser som mål – **insamlingsserver** och **Operations Manager-hanteringsgrupp **.
* Två anslutningsprogram skapas.  Den första heter **Microsoft.SystemCenter.Advisor.DataConnector** och konfigureras automatiskt med en prenumeration som vidarebefordrar alla aviseringar som genereras från instanser av alla klasser i hanteringsgruppen till Azure Monitor. Den andra anslutningen är **Advisor Connector**, som ansvarar för att kommunicera med Azure Monitor och dela data.
* Agenter och grupper som du har valt för att samla in data i hanteringsgruppen läggs till i **Övervakning av servergrupp i Microsoft System Center Advisor**.

## <a name="management-pack-updates"></a>Uppdateringar av hanteringspaket

När konfigurationen är klar upprättar Hanteringsgruppen för Operations Manager en anslutning till Azure Monitor. Hanteringsservern synkroniserar med webbtjänsten och tar emot uppdaterad konfigurationsinformation i form av hanteringspaket för de lösningar du har aktiverat som integreras med Operations Manager. Operations Manager söker efter uppdateringar av dessa hanteringspaket och hämtar och importerar dem automatiskt när de är tillgängliga. Det är särskilt två regler som styr detta:

* **Microsoft.SystemCenter.Advisor.MPUpdate** - Uppdaterar de grundläggande Azure Monitor-hanteringspaketen. Kör som standard var 12:e timme.
* **Microsoft.SystemCenter.Advisor.Core.GetIntelligencePacksRule** – uppdaterar hanteringspaketen för lösningen som aktiverats i din arbetsyta. Körs som standard var 5:e minut.

Du kan åsidosätta dessa två regler för att antingen förhindra automatisk hämtning genom att inaktivera dem, eller ändra frekvensen för hur ofta hanteringsservern synkroniseras med Azure Monitor för att avgöra om ett nytt hanteringspaket är tillgängligt och bör hämtas. Följ [de här stegen för att åsidosätta en regel eller övervakare](https://technet.microsoft.com/library/hh212869.aspx) för att ändra parametern **Frekvens** (med ett värde i sekunder) för att justera synkroniseringsschemat eller ändra parametern **Aktiverad** för att avaktivera reglerna. Rikta åsidosättningarna mot alla objekt i klassen Operations Manager Management Group.

Om du vill fortsätta följa din befintliga ändringskontrollprocess för att kontrollera hanteringspaketversioner i produktionshanteringsgruppen kan du inaktivera reglerna och aktivera dem under specifika tider när uppdateringar tillåts. Om du har en utvecklings- eller kvalitetskontrollshanteringsgrupp i din miljö och den är ansluten till Internet, kan du konfigurera hanteringsgruppen med Log Analytics-arbetsytan för att stödja det här scenariot. På så sätt kan du granska och utvärdera iterativa versioner av Azure Monitor-hanteringspaketen innan du släpper dem till din produktionshanteringsgrupp.

## <a name="switch-an-operations-manager-group-to-a-new-log-analytics-workspace"></a>Växla en Operations Manager-grupp till en ny Log Analytics-arbetsyta

1. Logga in på Azure-portalen på [https://portal.azure.com](https://portal.azure.com).
1. I Azure Portal klickar du på knappen **Fler tjänster** längst upp till vänster. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Log Analytics** och skapa en arbetsyta.  
1. Öppna Operations Manager-konsolen med ett konto med rollen Operations Manager-administratör och välj arbetsytan **Administration**.
1. Expandera Logganalys och välj **Anslutningar**.
1. Välj länken för att **konfigurera om Operation Management Suite** i mitten av fönstret.
1. Följ **inloggningsguiden för logganalys** och ange e-postadressen eller telefonnumret och lösenordet för det administratörskonto som är kopplat till den nya logganalysarbetsytan.

   > [!NOTE]
   > På sidan **Guiden Operations Management Suite Onboarding: Välj arbetsyta** visas den befintliga arbetsytan som används.
   >
   >

## <a name="validate-operations-manager-integration-with-azure-monitor"></a>Verifiera Integrering av Operations Manager med Azure Monitor

Det finns några olika sätt att kontrollera att Azure Monitor to Operations Manager-integreringen lyckas.

### <a name="to-confirm-integration-from-the-azure-portal"></a>Bekräfta integrationen från Azure Portal

1. I Azure Portal klickar du på knappen **Fler tjänster** längst upp till vänster. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver.
1. Välj arbetsyta i listan över Log Analytics-arbetsytor.  
1. Välj **Avancerade inställningar**, **Anslutna källor**, och sedan **System Center**.
1. Du bör se namnet på hanteringsgruppen tillsammans med antalet agenter och status när data togs emot senast i tabellen i avsnittet System Center Operations Manager.

   ![oms-settings-connectedsources](./media/om-agents/oms-settings-connectedsources.png)

### <a name="to-confirm-integration-from-the-operations-console"></a>Bekräfta integrationen från Operations-konsolen

1. Öppna Operations Manager-konsolen och välj arbetsytan **Administration**.
1. Välj **Hanteringspaket** och i textrutan för att **söka** skriver du **Advisor** eller **Intelligence**.
1. Beroende på vilka lösningar du har aktiverat kan du se motsvarande hanteringspaket i sökresultaten.  Om du har aktiverat lösningen för aviseringshantering visas hanteringspaket Microsoft System Center Advisor aviseringshantering i listan.
1. Från vyn **Övervakning** navigerar du till vyn **Operations Management Suite\Hälsotillstånd**.  Välj en hanteringsserver under fönstret för **hanteringsservertillstånd** och i fönstret **Detaljvy** kontrollerar du att värdet för egenskapen **Authentication service URI** (URI för autentiseringstjänst) matchar Log Analytics-arbetsytans ID.

   ![oms-opsmgr-mg-authsvcuri-property-ms](./media/om-agents/oms-opsmgr-mg-authsvcuri-property-ms.png)

## <a name="remove-integration-with-azure-monitor"></a>Ta bort integrering med Azure Monitor

Flera steg måste utföras för att ta bort anslutningen och konfigurationen korrekt i hanteringsgruppen när du inte längre behöver integreringen mellan din Operations Manager-hanteringsgrupp och Log Analytics-arbetsytan. Följande procedur har du uppdaterat din Log Analytics-arbetsyta genom att ta bort referensen för din hanteringsgrupp, ta bort Azure Monitor-kopplingar och sedan ta bort hanteringspaket som stöder integrering med tjänsten.  

Hanteringspaket för de lösningar som du har aktiverat som integreras med Operations Manager och de hanteringspaket som krävs för att stödja integrering med Azure Monitor kan inte enkelt tas bort från hanteringsgruppen. Detta beror på att vissa av Azure Monitor-hanteringspaketen har beroenden av andra relaterade hanteringspaket. För att ta bort hanteringspaket med beroenden i andra hanteringspaket laddar du ned skriptet [ för att ta bort hanteringspaket med ett beroende](https://gallery.technet.microsoft.com/scriptcenter/Script-to-remove-a-84f6873e) från TechNet Script Center.  

1. Öppna Operations Manager kommandotolken med ett konto som är medlem i rollen Operations Manager-administratörer.

    > [!WARNING]
    > Kontrollera att du inte har några anpassade hanteringspaket som innehåller ordet Advisor eller IntelligencePack i namnet innan du fortsätter. I annat fall kan du ta bort dem från hanteringsgruppen.
    >

1. Från kommandotolken skriver du: `Get-SCOMManagementPack -name "*Advisor*" | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
1. Skriv sedan `Get-SCOMManagementPack -name "*IntelligencePack*" | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
1. För att ta bort återstående hanteringspaket med beroenden till andra System Center Advisor-hanteringspaket använder du skriptet *RecursiveRemove.ps1* som du laddade ned innan från TechNet Script Center.  

    > [!NOTE]
    > Steget för att ta bort Advisor-hanteringspaketen med PowerShell tar inte automatiskt bort de interna hanteringspaketen för Microsoft System Center Advisor eller Microsoft System Center Advisor.  Försök inte ta bort dem.  
    >  

1. Öppna Operations-konsolen i Operations Manager med ett konto som har rollen Operations Manager-administratör.
1. Under **Administration** väljer du noden **Hanteringspaket** och i rutan för att **söka** skriver du **Advisor** och kontrollerar att följande hanteringspaket fortfarande importeras i hanteringsgruppen:

   * Microsoft System Center Advisor
   * Microsoft System Center Advisor Internal

1. Klicka på panelen **Inställningar** i Azure-portalen.
1. Välj **Anslutna källor**.
1. I tabellen under avsnittet System Center Operations Manager bör du se namnet på den hanteringsgrupp som du vill ta bort från arbetsytan. Under kolumnen **Senaste data** klickar du på **Ta bort**.  

    > [!NOTE]
    > Länken **Ta bort** blir inte tillgänglig förrän efter att den anslutna hanteringsgruppen känner av 14 dagar utan aktivitet.  
    >

1. Ett fönster visas där du uppmanas att bekräfta att du vill ta bort volymen.  Klicka på **Ja** för att gå vidare.

För att radera de två anslutningsprogrammen – Microsoft.SystemCenter.Advisor.DataConnector och Advisor Connector – sparar du PowerShell-skriptet nedan på datorn och kör det med följande exempel:

```
    .\OM2012_DeleteConnectors.ps1 "Advisor Connector" <ManagementServerName>
    .\OM2012_DeleteConnectors.ps1 "Microsoft.SystemCenter.Advisor.DataConnector" <ManagementServerName>
```

> [!NOTE]
> Den dator som du kör skriptet från (om det inte är en hanteringsserver) bör ha Operations Manager-kommandogränssnittet installerat, beroende på version av hanteringsgruppen.
>
>

```powershell
    param(
    [String] $connectorName,
    [String] $msName="localhost"
    )
    $mg = new-object Microsoft.EnterpriseManagement.ManagementGroup $msName
    $admin = $mg.GetConnectorFrameworkAdministration()
    ##########################################################################################
    # Configures a connector with the specified name.
    ##########################################################################################
    function New-Connector([String] $name)
    {
         $connectorForTest = $null;
         foreach($connector in $admin.GetMonitoringConnectors())
    {
    if($connectorName.Name -eq ${name})
    {
         $connectorForTest = Get-SCOMConnector -id $connector.id
    }
    }
    if ($connectorForTest -eq $null)
    {
         $testConnector = New-Object Microsoft.EnterpriseManagement.ConnectorFramework.ConnectorInfo
         $testConnector.Name = $name
         $testConnector.Description = "${name} Description"
         $testConnector.DiscoveryDataIsManaged = $false
         $connectorForTest = $admin.Setup($testConnector)
         $connectorForTest.Initialize();
    }
    return $connectorForTest
    }
    ##########################################################################################
    # Removes a connector with the specified name.
    ##########################################################################################
    function Remove-Connector([String] $name)
    {
        $testConnector = $null
        foreach($connector in $admin.GetMonitoringConnectors())
       {
        if($connector.Name -eq ${name})
       {
         $testConnector = Get-SCOMConnector -id $connector.id
       }
      }
     if ($testConnector -ne $null)
     {
        if($testConnector.Initialized)
     {
     foreach($alert in $testConnector.GetMonitoringAlerts())
     {
       $alert.ConnectorId = $null;
       $alert.Update("Delete Connector");
     }
     $testConnector.Uninitialize()
     }
     $connectorIdForTest = $admin.Cleanup($testConnector)
     }
    }
    ##########################################################################################
    # Delete a connector's Subscription
    ##########################################################################################
    function Delete-Subscription([String] $name)
    {
      foreach($testconnector in $admin.GetMonitoringConnectors())
      {
      if($testconnector.Name -eq $name)
      {
        $connector = Get-SCOMConnector -id $testconnector.id
      }
    }
    $subs = $admin.GetConnectorSubscriptions()
    foreach($sub in $subs)
    {
      if($sub.MonitoringConnectorId -eq $connector.id)
      {
        $admin.DeleteConnectorSubscription($admin.GetConnectorSubscription($sub.Id))
      }
     }
    }
    #New-Connector $connectorName
    write-host "Delete-Subscription"
    Delete-Subscription $connectorName
    write-host "Remove-Connector"
    Remove-Connector $connectorName
```

I framtiden om du planerar att återansluta hanteringsgruppen till en Log `Microsoft.SystemCenter.Advisor.Resources.\<Language>\.mpb` Analytics-arbetsyta måste du importera hanteringspaketfilen igen. Beroende på vilken version av System Center Operations Manager som har distribuerats i din miljö kan du hitta filen här:

* På källmediet under mappen `\ManagementPacks` för System Center 2016 – Operations Manager och högre.
* Från den senaste uppdateringen som tillämpats på din hanteringsgrupp. För Operations Manager 2012 är `%ProgramFiles%\Microsoft System Center 2012\Operations Manager\Server\Management Packs for Update Rollups` källmappen och för 2012 `System Center 2012 R2\Operations Manager\Server\Management Packs for Update Rollups`R2 finns den i .

## <a name="next-steps"></a>Nästa steg

Information om hur du lägger till funktioner och samlar in data finns i [Lägga till Azure Monitor-lösningar från lösningsgalleriet](../../azure-monitor/insights/solutions.md).
