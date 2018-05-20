---
title: Anslut Operations Manager till Log Analytics | Microsoft Docs
description: För att underhålla din befintliga investering i System Center Operations Manager och använda utökade funktioner med Log Analytics, kan du integrera Operations Manager med ditt arbetsområde.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: 245ef71e-15a2-4be8-81a1-60101ee2f6e6
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2018
ms.author: magoedte
ms.openlocfilehash: b11cffcb006ba4f0598bd7f5cf6ed13daad2db42
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="connect-operations-manager-to-log-analytics"></a>Anslut Operations Manager till Log Analytics
För att underhålla din befintliga investering i System Center Operations Manager och använda utökade funktioner med Log Analytics, kan du integrera Operations Manager med ditt Log Analytics-arbetsområde.  På så sätt kan du utnyttja möjligheterna i Log Analytics samtidigt som du använder Operations Manager till att:

* Övervaka hälsotillståndet för dina IT-tjänster med Operations Manager
* Underhålla integrationen med dina ITSM-lösningar med stöd för hantering av incidenter och problem
* Hantera livscykeln för agenter som distribuerats lokalt och till virtuella IaaS-datorer i det offentliga molnet som du övervakar med Operations Manager

Integration med System Center Operations Manager ökar värdet för din åtgärdsstrategi för tjänster genom att dra nytta av Log Analytics hastighet och effektivitet under insamling, lagring och analys av data från Operations Manager.  Log Analytics hjälper till att korrelera och arbeta för att identifiera orsaken till problem och visar upprepningar för support för din befintliga problemhantering.  Den flexibla sökmotorn kan användas för att granska prestanda, händelser och aviseringsdata. Den innehåller omfattande instrumentpaneler och rapporteringsfunktioner för att visa informationen på ett meningsfullt sätt. Allt detta visar kraften Log Analytics ger som komplement till Operations Manager.

Agenter som rapporterar till Operations Manager-hanteringsgruppen samlar in data från dina servrar baserat på de Log Analytics-datakällor och -lösningar som du har aktiverat i arbetsytan.  Beroende på vilka lösningar som aktiverats skickas data från dem antingen direkt från en hanteringsserver för Operations Manager till tjänsten eller, beroende på mängden data som samlas in på systemet agenten hanterar, direkt från agenten till Log Analytics. Hanteringsservern vidarebefordrar data direkt till tjänsten, de skrivs aldrig till den operativa databasen eller informationslagerdatabasen.  När en hanteringsserver tappar anslutningen till Log Analytics lagras data lokalt i cacheminnet tills kommunikationen med Log Analytics är återupprättad.  Om hanteringsservern är offline på grund av planerat underhåll eller oplanerade avbrott, skapar en annan hanteringsserver i hanteringsgruppen en anslutning till Log Analytics.  

Följande diagram visar anslutningen mellan hanteringsservrarna och agenterna i en System Center Operations Manager-hanteringsgrupp och Log Analytics, inklusive riktning och portar.   

![oms-operations-manager-integration-diagram](./media/log-analytics-om-agents/oms-operations-manager-connection.png)

Om dina IT-säkerhetsprinciper inte tillåter datorer i nätverket att ansluta till Internet, kan hanteringsservrar konfigureras för att ansluta till OMS-gatewayen för att ta emot konfigurationsinformation och skicka insamlade data beroende på de lösningar som är aktiverade.  Mer information och anvisningar om hur du konfigurerar din Operations Manager-hanteringsgrupp för kommunikation genom en OMS-gateway till Log Analytics-tjänsten finns i avsnittet om hur du [ansluter datorer till OMS med OMS-gatewayen](log-analytics-oms-gateway.md).  

## <a name="system-requirements"></a>Systemkrav
Granska följande information för att verifiera att förutsättningarna är uppfyllda innan du börjar.

* Log Analytics stödjer bara System Center Operations Manager 1801, Operations Manager 2016, Operations Manager 2012 SP1 UR6 eller högre samt Operations Manager 2012 R2 UR2 eller högre.  Stöd för proxy har lagts till i Operations Manager 2012 SP1 UR7 och Operations Manager 2012 R2 UR3.
* Alla Operations Manager-agenter måste uppfylla minimikraven. Kontrollera att agenterna har den lägsta uppdateringen, annars kan Windows-agenttrafiken misslyckas och Operations Manager-händelseloggen fyllas av fel.
* En Log Analytics-arbetsyta.  Mer information finns under [Komma igång med Log Analytics](log-analytics-get-started.md).

### <a name="network"></a>Nätverk
Informationen nedan listar konfigurationsinställningarna för proxy och brandvägg som krävs för att Operations Manager-agenten, hanteringsservrar och Operations-konsolen ska kunna kommunicera med Log Analytics.  Trafik från varje komponent går ut från nätverket till Log Analytics-tjänsten.     

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
|**Operations Manager-konsolen till OMS**|||  
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

## <a name="connecting-operations-manager-to-log-analytics"></a>Anslut Operations Manager till Log Analytics
Utför följande steg för att konfigurera hanteringsgruppen för Operations Manager för att ansluta till en av dina Log Analytics-arbetsytor.

Om det är första gången din Operations Manager-hanteringsgrupp registreras med en Log Analytics-arbetsyta och hanteringsservrarna måste kommunicera med tjänsten via en proxy- eller OMS gatewayserver är alternativet för att ange proxykonfiguration för hanteringsgruppen inte tillgängligt i Operations-konsolen.  Hanteringsgruppen måste registrerats med tjänsten innan det här alternativet är tillgängligt.  Du behöver uppdatera systemproxykonfigurationen med Netsh på systemet som Operations-konsolen körs från för att konfigurera integration och alla hanteringsservrar i hanteringsgruppen.  

1. Öppna en upphöjd kommandotolk.
1. Ange följande kommando och tryck på **Enter**:

    `netsh winhttp set proxy <proxy>:<port>`

När du har slutfört följande steg för att integrera med Log Analytics kan du ta bort konfigurationen genom att köra `netsh winhttp reset proxy` och sedan använda alternativet **Konfigurera proxyserver** i Operations-konsolen för att ange proxy- eller OMS-gatewayserver. 

1. Välj arbetsytan **Administration** i Operations Manager-konsolen.
2. Expandera noden Operations Management Suite och klicka på **Anslutning**.
3. Klicka på länken för att **registrera till Operations Management Suite**.
4. På sidan **Guiden Operations Management Suite Onboarding: Autentisering** anger du e-postadress eller telefonnummer och lösenordet för administratörskontot som är kopplat till din OMS-prenumeration och klickar på **Logga in**.
5. När du har autentiserats uppmanas du välja Log Analytics-arbetsyta på sidan **Guiden Operations Management Suite Onboarding: Välj arbetsyta**.  Om du har mer än en arbetsyta väljer du arbetsytan som du vill registrera med Operations Manager-hanteringsgruppen från listrutan och klickar på **Nästa**.
   
   > [!NOTE]
   > Operations Manager stöder bara en Log Analytics-arbetsyta i taget. Anslutningen och de datorer som registrerades till Log Analytics med den förra arbetsytan tas bort från Log Analytics.
   > 
   > 
6. På sidan **Guiden Operations Management Suite Onboarding: Sammanfattning** bekräftar du dina inställningar och om de är korrekta klickar du på **Skapa**.
7. På sidan **Guiden Operations Management Suite Onboarding: Slutför** klickar du på **Stäng**.

### <a name="add-agent-managed-computers"></a>Lägg till datorer som hanteras med agent
När du har konfigurerat integrationen med Log Analytics-arbetsytan har du bara upprättat en anslutning med tjänsten och inga data samlas in från agenter som rapporterar till hanteringsgruppen. Detta görs inte förrän du har konfigurerat vilka specifika datorer som hanteras med agenter som ska samla in data för Log Analytics. Du kan antingen välja datorobjekt individuellt eller välja en grupp som innehåller Windows-datorobjekt. Du kan inte välja en grupp som innehåller instanser av en annan klass, till exempel logiska diskar eller SQL-databaser.

1. Öppna Operations Manager-konsolen och välj arbetsytan **Administration**.
2. Expandera noden Operations Management Suite och klicka på **Anslutning**.
3. Klicka på länken **Lägg till en dator/grupp** under rubriken Åtgärder på höger sida av fönstret.
4. I dialogrutan **Datorsökning** kan du söka efter datorer eller grupper som övervakas av Operations Manager. Välj datorer eller grupper för att publicera dem i Log Analytics, klicka på **Lägg till** och sedan på **OK**.

Du kan visa datorer och grupper som konfigurerats för att samla in data från noden för hanterade datorer under Operations Management Suite i arbetsytan **Administration** i Operations-konsolen.  Härifrån kan du lägga till eller ta bort datorer och grupper efter behov.

### <a name="configure-proxy-settings-in-the-operations-console"></a>Konfigurera proxyinställningar i Operations-konsolen
Utför följande steg om en intern proxyserver finns mellan hanteringsgruppen och Log Analytics-tjänsten.  De här inställningarna hanteras centralt från hanteringsgruppen och distribueras till agenthanterade system som ingår i omfånget för att samla in data för Log Analytics.  Detta är fördelaktigt när vissa lösningar förbigår hanteringsservern och skickar data direkt till tjänsten.

1. Öppna Operations Manager-konsolen och välj arbetsytan **Administration**.
2. Expandera Operations Management Suite och klicka sedan på **Anslutningar**.
3. I vyn OMS-anslutning klickar du på **Konfigurera proxyserver**.
4. På sidan med **guiden Operations Management Suite: Proxyserver** väljer du alternativet för att **använda en proxyserver för att få åtkomst till Operational Management Suite** och anger sedan webbadressen med portnumret, till exempel http://corpproxy:80 och klickar på **Slutför**.

Om proxyservern kräver autentisering utför du följande steg för att konfigurera de autentiseringsuppgifter och inställningar som behöver spridas till hanterade datorer som rapporterar till OMS i hanteringsgruppen.

1. Öppna Operations Manager-konsolen och välj arbetsytan **Administration**.
2. Under **Kör som-konfiguration** väljer du **Profiler**.
3. Öppna profilen **Kör som-profilproxy för System Center Advisor**.
4. I guiden Kör som-profil klickar du på Lägg till för att använda ett Kör som-konto. Du kan skapa ett [Kör som-konto](https://technet.microsoft.com/library/hh321655.aspx) eller använda ett befintligt konto. Det här kontot måste ha tillräcklig behörighet för att kunna passera genom proxyservern.
5. Ange vilket konto som ska hanteras genom att välja **En vald klass, grupp eller objekt**, klicka på **Välj...** och sedan klicka på **Grupp...** för att öppna **Gruppsökning**.
6. Sök efter och välj **Övervakning av servergrupp i Microsoft System Center Advisor**.  Klicka på **OK** när du har valt grupp för att stänga rutan **Gruppsökning**.
7. Klicka på **OK** för att stänga rutan **Lägg till ett Kör som-konto**.
8. Klicka på **Spara** för att slutföra guiden och spara dina ändringar.

När anslutningen har skapats och du konfigurerar vilka agenter som ska samla in och rapportera data till Log Analytics, används följande konfiguration i hanteringsgruppen (inte nödvändigtvis i den här ordningen):

* Kör som-kontot **Microsoft.SystemCenter.Advisor.RunAsAccount.Certificate** skapas.  Det är associerat med Kör som-profilen **Microsoft System Center Advisor Run As Profile Blob** (Microsoft System Center Advisor Kör som-profilblob) och har två klasser som mål – **insamlingsserver** och **Operations Manager-hanteringsgrupp** .
* Två anslutningsprogram skapas.  Det första heter **Microsoft.SystemCenter.Advisor.DataConnector** och konfigureras automatiskt med en prenumeration som vidarebefordrar alla aviseringar som genereras av instanser för alla klasser i hanteringsgruppen till Log Analytics. Det andra anslutningsprogrammet är **Advisor Connector** och ansvarar för kommunikation med OMS-webbtjänsten och för delning av data.
* Agenter och grupper som du har valt för att samla in data i hanteringsgruppen läggs till i **Övervakning av servergrupp i Microsoft System Center Advisor**.

## <a name="management-pack-updates"></a>Uppdateringar av hanteringspaket
När konfigurationen är klar upprättar Operations Manager-hanteringsgruppen en anslutning med tjänsten Log Analytics.  Hanteringsservern synkroniserar med webbtjänsten och tar emot uppdaterad konfigurationsinformation i form av hanteringspaket för de lösningar du har aktiverat som integreras med Operations Manager.   Operations Manager söker efter uppdateringar för dessa hanteringspaket och laddar automatiskt ned och importerar dem när de är tillgängliga.  Det är särskilt två regler som styr detta:

* **Microsoft.SystemCenter.Advisor.MPUpdate** – uppdaterar det grundläggande hanteringspaketet för Log Analytics. Kör som standard var 12:e timme.
* **Microsoft.SystemCenter.Advisor.Core.GetIntelligencePacksRule** – uppdaterar hanteringspaketen för lösningen som aktiverats i din arbetsyta. Körs som standard var 5:e minut.

Du kan åsidosätta de här två reglerna för att förhindra automatisk hämtning genom att inaktivera dem eller ändra frekvensen för hur ofta hanteringsservern ska synkronisera med OMS för att kontrollera om ett nytt hanteringspaket finns tillgängligt och ska hämtas.  Följ [de här stegen för att åsidosätta en regel eller övervakare](https://technet.microsoft.com/library/hh212869.aspx) för att ändra parametern **Frekvens** (med ett värde i sekunder) för att justera synkroniseringsschemat eller ändra parametern **Aktiverad** för att avaktivera reglerna.  Rikta åsidosättningarna mot alla objekt i klassen Operations Manager Management Group.

Om du vill fortsätta att jobba efter dina befintliga processer för ändringskontroll för att styra releaser av hanteringspaket i din produktionshanteringsgrupp kan du avaktivera reglerna och aktivera dem vid specifika tidpunkter när uppdateringar är tillåtna. Om du har en utvecklings- eller kvalitetskontrollshanteringsgrupp i din miljö och den är ansluten till Internet, kan du konfigurera hanteringsgruppen med Log Analytics-arbetsytan för att stödja det här scenariot.  Då kan du granska och utvärdera iterativa versioner av Log Analytics-hanteringspaket innan du släpper dem till din hanteringsgrupp för produktion.

## <a name="switch-an-operations-manager-group-to-a-new-log-analytics-workspace"></a>Växla en Operations Manager-grupp till en ny Log Analytics-arbetsyta
1. Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).
2. I Azure Portal klickar du på knappen **Fler tjänster** längst upp till vänster. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Log Analytics** och skapa en arbetsyta.  
3. Öppna Operations Manager-konsolen med ett konto med rollen Operations Manager-administratör och välj arbetsytan **Administration**.
4. Expandera Operations Management Suite och klicka på **Anslutningar**.
5. Välj länken för att **konfigurera om Operation Management Suite** i mitten av fönstret.
6. Följ **Guiden Operations Management Suite Onboarding** och ange e-postadress eller telefonnummer och lösenordet för administratörskontot som är kopplat till din nya Log Analytics-arbetsyta.
   
   > [!NOTE]
   > På sidan **Guiden Operations Management Suite Onboarding: Välj arbetsyta** visas den befintliga arbetsytan som används.
   > 
   > 

## <a name="validate-operations-manager-integration-with-log-analytics"></a>Verifiera Operations Manager-integrationen med Log Analytics
Det finns olika sätt att verifiera att integrationen av Log Analytics med Operations Manager har lyckats.

### <a name="to-confirm-integration-from-the-azure-portal"></a>Bekräfta integrationen från Azure Portal
1. I Azure Portal klickar du på knappen **Fler tjänster** längst upp till vänster. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver.
2. Välj arbetsyta i listan över Log Analytics-arbetsytor.  
3. Välj **Avancerade inställningar**, **Anslutna källor**, och sedan **System Center**. 
4. Du bör se namnet på hanteringsgruppen tillsammans med antalet agenter och status när data togs emot senast i tabellen i avsnittet System Center Operations Manager.
   
   ![oms-settings-connectedsources](./media/log-analytics-om-agents/oms-settings-connectedsources.png)

### <a name="to-confirm-integration-from-the-operations-console"></a>Bekräfta integrationen från Operations-konsolen
1. Öppna Operations Manager-konsolen och välj arbetsytan **Administration**.
2. Välj **Hanteringspaket** och i textrutan för att **söka** skriver du **Advisor** eller **Intelligence**.
3. Beroende på vilka lösningar du har aktiverat kan du se motsvarande hanteringspaket i sökresultaten.  Om du har aktiverat lösningen för aviseringshantering visas hanteringspaket Microsoft System Center Advisor aviseringshantering i listan.
4. Från vyn **Övervakning** navigerar du till vyn **Operations Management Suite\Hälsotillstånd**.  Välj en hanteringsserver under fönstret för **hanteringsservertillstånd** och i fönstret **Detaljvy** kontrollerar du att värdet för egenskapen **Authentication service URI** (URI för autentiseringstjänst) matchar Log Analytics arbetsyte-ID.
   
   ![oms-opsmgr-mg-authsvcuri-property-ms](./media/log-analytics-om-agents/oms-opsmgr-mg-authsvcuri-property-ms.png)

## <a name="remove-integration-with-log-analytics"></a>Ta bort integrering med Log Analytics
Flera steg måste utföras för att ta bort anslutningen och konfigurationen korrekt i hanteringsgruppen när du inte längre behöver integreringen mellan din Operations Manager-hanteringsgrupp och Log Analytics-arbetsytan. Med följande procedur uppdateras Log Analytics-arbetsytan genom att du tar bort referenser till hanteringsgruppen, tar bort Log Analytics-kopplingar och sedan tar bort hanteringspaket som har stöd för integrering med tjänsten.   

Det är inte helt enkelt att ta bort hanteringspaketen för lösningarna som du har aktiverat som integrerar med Operations Manager och de hanteringspaket som krävs för att stödja integration med Log Analytics-tjänsten.  Det beror på att vissa Log Analytics-hanteringspaket har beroenden i andra relaterade hanteringspaket.  För att ta bort hanteringspaket med beroenden i andra hanteringspaket laddar du ned skriptet [ för att ta bort hanteringspaket med ett beroende](https://gallery.technet.microsoft.com/scriptcenter/Script-to-remove-a-84f6873e) från TechNet Script Center.  

1. Öppna Operations Manager kommandotolken med ett konto som är medlem i rollen Operations Manager-administratörer.
   
    > [!WARNING]
    > Kontrollera att du inte har några anpassade hanteringspaket som innehåller ordet Advisor eller IntelligencePack i namnet innan du fortsätter. I annat fall kan du ta bort dem från hanteringsgruppen.
    > 

2. Från kommandotolken skriver du: `Get-SCOMManagementPack -name "*Advisor*" | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
3. Skriv sedan `Get-SCOMManagementPack -name “*IntelligencePack*” | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
4. För att ta bort återstående hanteringspaket med beroenden till andra System Center Advisor-hanteringspaket använder du skriptet *RecursiveRemove.ps1* som du laddade ned innan från TechNet Script Center.  
 
    > [!NOTE]
    > Ta inte bort hanteringspaket för Microsoft System Center Advisor eller Microsoft System Center Advisor Internal.  
    >  

5. Öppna Operations-konsolen i Operations Manager med ett konto som har rollen Operations Manager-administratör.
6. Under **Administration** väljer du noden **Hanteringspaket** och i rutan för att **söka** skriver du **Advisor** och kontrollerar att följande hanteringspaket fortfarande importeras i hanteringsgruppen:
   
   * Microsoft System Center Advisor
   * Microsoft System Center Advisor Internal
1. Öppna menyn **Avancerade inställningar** för Log Analytics i Azure Portal.
1. Välj **Anslutna källor** och sedan **System Center**.
1. Du bör se namnet på hanteringsgruppen du vill ta bort från arbetsytan.  Under kolumnen **Senaste data** klickar du på **Ta bort**.  
   
    > [!NOTE]
    > Länken **Ta bort** blir inte tillgänglig förrän efter att den anslutna hanteringsgruppen känner av 14 dagar utan aktivitet.  
    > 

10. Ett fönster visas där du uppmanas att bekräfta att du vill ta bort volymen.  Klicka på **Ja** för att gå vidare. 

För att radera de två anslutningsprogrammen – Microsoft.SystemCenter.Advisor.DataConnector och Advisor Connector – sparar du PowerShell-skriptet nedan på datorn och kör det med följande exempel:

```
    .\OM2012_DeleteConnector.ps1 “Advisor Connector” <ManagementServerName>
    .\OM2012_DeleteConnectors.ps1 “Microsoft.SytemCenter.Advisor.DataConnector” <ManagementServerName>
```

> [!NOTE]
> Den dator som du kör skriptet från (om det inte är en hanteringsserver) bör ha Operations Manager-kommandogränssnittet installerat, beroende på version av hanteringsgruppen.
> 
> 

```
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

Om du planerar att återansluta din hanteringsgrupp till en Log Analytics-arbetsyta i framtiden måste du importera om hanteringspaketsfilen `Microsoft.SystemCenter.Advisor.Resources.\<Language>\.mpb`.  Beroende på vilken version av System Center Operations Manager som har distribuerats i din miljö kan du hitta filen här:

* På källmediet under mappen `\ManagementPacks` för System Center 2016 – Operations Manager och högre.
* Från den senaste uppdateringen som tillämpats på din hanteringsgrupp.  För Operations Manager 2012 är källmappen ` %ProgramFiles%\Microsoft System Center 2012\Operations Manager\Server\Management Packs for Update Rollups` och för 2012 R2 finns den i `System Center 2012 R2\Operations Manager\Server\Management Packs for Update Rollups`.

## <a name="next-steps"></a>Nästa steg
Se [Lägg till Log Analytics-lösningar från lösningsgalleriet](log-analytics-add-solutions.md) för att lägga till funktioner och samla in data.


