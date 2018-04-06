---
title: Ansluta Operations Manager till logganalys | Microsoft Docs
description: För att underhålla din investering i System Center Operations Manager och använder utökade funktioner med logganalys, kan du integrera Operations Manager med din OMS-arbetsyta.
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
ms.date: 12/10/2017
ms.author: magoedte
ms.openlocfilehash: 6db47c7baa0a345a32d26d56e843acd0204ae50b
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="connect-operations-manager-to-log-analytics"></a>Ansluta Operations Manager till logganalys
För att underhålla din investering i System Center Operations Manager och använder utökade funktioner med logganalys, kan du integrera Operations Manager med din OMS-arbetsyta.  På så sätt kan du utnyttja möjligheter i OMS medan du använder Operations Manager till:

* Fortsätta övervaka hälsotillståndet för din IT-tjänster med Operations Manager
* Underhåll integration med dina ITSM lösningar stöd för hantering av incidenter och problem
* Hantera livscykeln för agenter som distribuerats till lokalt och offentliga moln IaaS-virtuella datorer som du övervakar med Operations Manager

Integrering med System Center Operations Manager tillför värde till din strategi för tjänsten åtgärder med hjälp av hastighet och prestanda för OMS i att samla in, lagra och analysera data från Operations Manager.  OMS hjälper korrelera och arbeta för att identifiera fel på problem och visa repetitioner stöd för din befintliga problem-hanteringen.   Möjlighet att sökmotor för att granska prestanda, händelser och aviseringsdata med omfattande instrumentpaneler och rapporteringsfunktioner att exponera informationen på ett meningsfullt sätt visar styrkan OMS ger i complimenting Operations Manager.

Agenter som rapporterar till Operations Manager-hanteringsgruppen samla in data från dina servrar baserat på logganalys-datakällor och lösningar som du har aktiverat i OMS-prenumeration.  Beroende på den lösning som du har aktiverat data från dessa lösningar antingen skickas direkt från en hanteringsserver för Operations Manager till OMS-webbtjänst eller på grund av mängden data som samlas in på datorn hanteras med agent, skickas direkt från agenten till OMS-webbtjänsten. Management-servern vidarebefordrar OMS-data direkt till OMS-webbtjänsten; den skrivs aldrig till databasen OperationsManager eller OperationsManagerDW.  När en hanteringsserver förlorar anslutningen till OMS-webbtjänsten, cachelagrar data lokalt tills kommunikation har återupprättats med OMS.  Om management-servern är offline på grund av planerat underhåll eller oplanerade avbrott, fortsätter anslutningen till OMS med en annan hanteringsserver i hanteringsgruppen.  

Följande diagram visar anslutningen mellan hanteringsservrarna och agenterna i en hanteringsgrupp för System Center Operations Manager och OMS, inklusive riktning och portar.   

![oms-operations-manager-integration-diagram](./media/log-analytics-om-agents/oms-operations-manager-connection.png)

Om din IT-säkerhetsprinciper inte tillåter datorer i nätverket för att ansluta till Internet, kan hanteringsservrar konfigureras för att ansluta till OMS-Gateway för att ta emot information om konfiguration och skicka insamlade data beroende på lösningen som du har aktiverat.  Mer information och anvisningar om hur du konfigurerar din Operations Manager-hanteringsgrupp att kommunicera via en OMS-Gateway till OMS-tjänsten finns [ansluta datorer till OMS med OMS-gatewayen](log-analytics-oms-gateway.md).  

## <a name="system-requirements"></a>Systemkrav
Granska följande information för att verifiera förutsättningar vara uppfyllda innan du börjar.

* OMS har bara stöd för Operations Manager 2016, UR6 för Operations Manager 2012 SP1 och senare, och Operations Manager 2012 R2 UR2 och större.  Stöd för proxy har lagts till i Operations Manager 2012 SP1 UR7 och Operations Manager 2012 R2 UR3.
* Alla Operations Manager-agenter måste uppfylla krav på minsta. Kontrollera att agenterna finns på den lägsta uppdateringen annars trafik för Windows-agenten misslyckas och många fel kan fylla i Operations Manager-händelseloggen.
* En OMS-prenumeration.  Mer information finns [Kom igång med logganalys](log-analytics-get-started.md).

### <a name="network"></a>Nätverk
Informationen nedan lista över proxy- och brandväggsinställningarna configuration information som krävs för Operations Manager-agenten, hanteringsservrar och driftkonsolen att kommunicera med OMS.  Trafik från varje komponent är utgående från nätverket till OMS-tjänsten.     

|Resurs | Portnummer| Kringgå http-kontroll|  
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


## <a name="connecting-operations-manager-to-oms"></a>Ansluta Operations Manager till OMS
Utför följande steg för att konfigurera hanteringsgruppen för Operations Manager för att ansluta till en av dina arbetsytor OMS-serien.

1. I Operations Manager-konsolen väljer du den **Administration** arbetsytan.
2. Expandera noden Operations Management Suite och klicka på **anslutning**.
3. Klicka på den **registrera till Operations Management Suite** länk.
4. På den **guiden Operations Management Suite Onboarding: autentisering** , ange e-postadress eller telefonnummer och lösenordet för administratörskontot som är associerad med din OMS-prenumeration och klicka på  **Logga in**.
5. När du har autentiserats, på den **guiden Operations Management Suite Onboarding: Välj arbetsyta** sidan uppmanas du för att välja din OMS-arbetsyta.  Om du har mer än en arbetsytan väljer du arbetsytan som du vill registrera med Operations Manager-hanteringsgruppen från den nedrullningsbara listan och klicka sedan på **nästa**.
   
   > [!NOTE]
   > Operations Manager stöder bara en OMS-arbetsytan i taget. Anslutningen och de datorer som registrerats till OMS med arbetsytan tidigare tas bort från OMS.
   > 
   > 
6. På den **guiden Operations Management Suite Onboarding: Sammanfattning** , bekräfta dina inställningar och om de är korrekt, klickar du på **skapa**.
7. På den **guiden Operations Management Suite Onboarding: Slutför** klickar du på **Stäng**.

### <a name="add-agent-managed-computers"></a>Lägg till datorer som hanteras med agent
När du har konfigurerat integrering med din OMS-arbetsyta detta endast upprättar en anslutning med OMS, inga data samlas in från agenter som rapporterar till hanteringsgruppen. Detta sker inte förrän efter att du konfigurerar vilka specifika datorer som hanteras med agent samlar in data för Log Analytics. Du kan antingen välja datorobjekt individuellt eller så kan du välja en grupp som innehåller Windows-datorobjekt. Du kan inte välja en grupp som innehåller instanser av en annan klass, till exempel logiska diskar eller SQL-databaser.

1. Öppna Operations Manager-konsolen och välj arbetsytan **Administration**.
2. Expandera noden Operations Management Suite och klicka på **anslutning**.
3. Klicka på den **lägga till en datorgrupp** länk under åtgärder rubriken på höger sida av fönstret.
4. I den **datorsökning** dialogrutan som du kan söka efter datorer eller grupper som övervakas av Operations Manager. Välj datorer eller grupper som ska publiceras till OMS, klicka på **Lägg till**, och klicka sedan på **OK**.

Du kan visa datorer och grupper som konfigurerats för att samla in data från datorer som hanteras med noden under Operations Management Suite i den **Administration** arbetsyta i driftkonsolen.  Härifrån kan du lägga till eller ta bort datorer och grupper som behövs.

### <a name="configure-oms-proxy-settings-in-the-operations-console"></a>Konfigurera proxyinställningar för OMS i Operations-konsolen
Utför följande steg om en intern proxyserver mellan hanteringsgruppen och OMS-webbtjänsten.  De här inställningarna hanteras från hanteringsgruppen och distribueras till agenthanterade datorer som ingår i omfånget för att samla in data för OMS centralt.  Detta är fördelaktigt för när vissa lösningar kringgå hanteringsservern och skicka data direkt till OMS-webbtjänsten.

1. Öppna Operations Manager-konsolen och välj arbetsytan **Administration**.
2. Expandera Operations Management Suite och klicka sedan på **anslutningar**.
3. I vyn OMS-anslutning klickar du på **Konfigurera proxyserver**.
4. På **guiden Operations Management Suite: proxyserver** väljer **använder en proxyserver för att få åtkomst till Operations Management Suite**, och ange sedan Webbadressen med portnummer, till exempel http://corpproxy:80 och klicka sedan på **Slutför**.

Om proxyservern kräver autentisering, utför följande steg för att konfigurera autentiseringsuppgifter och inställningar som måste tillämpas på hanterade datorer som rapporterar till OMS i hanteringsgruppen.

1. Öppna Operations Manager-konsolen och välj arbetsytan **Administration**.
2. Under **Kör som-konfiguration** väljer du **Profiler**.
3. Öppna profilen **Kör som-profilproxy för System Center Advisor**.
4. I guiden kör som-profil, klickar du på Lägg till om du vill använda ett kör som-konto. Du kan skapa en [kör som-konto](https://technet.microsoft.com/library/hh321655.aspx) eller Använd ett befintligt konto. Det här kontot måste ha tillräcklig behörighet för att kunna passera genom proxyservern.
5. Om du vill ange kontot som ska hantera Välj **en vald klass, grupp eller objekt**, klickar du på **Välj...** och klicka sedan på **grupp...** Öppna den **grupp Sök** rutan.
6. Sök efter och välj sedan **övervakning servergrupp för Microsoft System Center Advisor**.  Klicka på **OK** när du har valt gruppen för att stänga den **grupp Sök** rutan.
7. Klicka på **OK** att stänga den **Lägg till ett kör som-konto** rutan.
8. Klicka på **spara** att slutföra guiden och spara dina ändringar.

När anslutningen har skapats och du konfigurerar vilka agenter som samlar in och rapportera data till OMS, används följande konfiguration i hanteringsgruppen, inte nödvändigtvis i ordning:

* Kör som-kontot **Microsoft.SystemCenter.Advisor.RunAsAccount.Certificate** skapas.  Den är associerad med Kör som-profilen **Microsoft System Center Advisor kör som-profilen Blob** och är på två klasser - **insamlingsserver** och **Operations Manager-Hanteringsgrupp** .
* Två kopplingar skapas.  Först heter **Microsoft.SystemCenter.Advisor.DataConnector** och konfigureras automatiskt med en prenumeration som vidarebefordrar alla varningar som genereras av instanser av alla klasser i hanteringsgruppen till OMS logganalys. Den andra kopplingen är **Advisor Connector**, som ansvarar för kommunikation med OMS-webbtjänst och delning av data.
* Agenter och grupper som du har valt att samla in data i hanteringsgruppen läggs till i **övervakning servergrupp för Microsoft System Center Advisor**.

## <a name="management-pack-updates"></a>Uppdateringar av hanteringspaket
När konfigurationen är klar upprättar en anslutning med tjänsten OMS Operations Manager-hanteringsgruppen.  Hanteringsservern synkroniserar med webbtjänsten och ta emot uppdaterade konfigurationsinformation i form av hanteringspaket för lösningar som du har aktiverat som integreras med Operations Manager.   Operations Manager söker efter uppdateringar för dessa hanteringspaket och automatiskt hämta och importerar dem när de är tillgängliga.  Det finns två regler särskilt som styra detta:

* **Microsoft.SystemCenter.Advisor.MPUpdate** -uppdaterar grundläggande OMS-hanteringspaket. Som standard körs var 12: e timme.
* **Microsoft.SystemCenter.Advisor.Core.GetIntelligencePacksRule** -uppdaterar lösning hanteringspaket som är aktiverad i din arbetsyta. Körs var fem (5): e minut som standard.

Du kan åsidosätta de här två reglerna för att förhindra automatisk hämtning genom att inaktivera dem eller ändra frekvensen för hur ofta hanteringsservern synkroniserar med OMS att fastställa om ett nytt management pack är tillgänglig och att de ska hämtas.  Följ stegen [hur du åsidosätter en regel eller Övervakare](https://technet.microsoft.com/library/hh212869.aspx) att ändra den **frekvens** parametern med värdet i sekunder för att ändra synkroniseringsschemat eller ändra den **aktiverad**parametern för att inaktivera reglerna.  Rikta åsidosättningar för alla objekt i klassen Operations Manager-Hanteringsgrupp.

Om du vill fortsätta efter befintliga Ändra kontroll processen för att styra management pack-versioner i hanteringsgruppen för produktion kan du inaktivera reglerna och aktivera dem vid specifika tidpunkter när uppdateringar är tillåtna. Om du har en utvecklings- eller QA hanteringsgruppen i din miljö och den är ansluten till Internet, kan du konfigurera den hanteringsgruppen med en OMS-arbetsyta som stöd för det här scenariot.  På så sätt kan du granska och utvärdera iterativ versioner av hanteringspaket OMS innan du släpper dem till hanteringsgruppen för produktion.

## <a name="switch-an-operations-manager-group-to-a-new-oms-workspace"></a>Växla en Operations Manager-gruppen till en ny OMS-arbetsyta
1. Logga in på OMS-prenumeration och skapa en arbetsyta i [Microsoft Operations Management Suite](http://oms.microsoft.com/).
2. Öppna Operations Manager-konsolen med ett konto som är medlem i rollen Operations Manager-administratörer och välj den **Administration** arbetsytan.
3. Expandera Operations Management Suite och välj **anslutningar**.
4. Välj den **konfigurera åtgärden Management Suite** länk i mitten-sida av fönstret.
5. Följ den **guiden Operations Management Suite Onboarding** och ange e-postadress eller telefonnummer antal och lösenordet för administratörskontot som är associerad med den nya OMS-arbetsytan.
   
   > [!NOTE]
   > Den **guiden Operations Management Suite Onboarding: Välj arbetsyta** visar befintlig arbetsyta som används.
   > 
   > 

## <a name="validate-operations-manager-integration-with-oms"></a>Kontrollera Operations Manager Integration with OMS
Det finns några olika sätt som du kan kontrollera att din OMS till integrering av Operations Manager har lyckats.

### <a name="to-confirm-integration-from-the-oms-portal"></a>Bekräfta integration från OMS-portalen
1. I OMS-portalen klickar du på den **inställningar** panelen
2. Välj **anslutna källor**.
3. Du bör se namnet på hanteringsgruppen som visas i listan med antalet agenter och status när data togs emot senast i tabellen i avsnittet System Center Operations Manager.
   
   ![oms-settings-connectedsources](./media/log-analytics-om-agents/oms-settings-connectedsources.png)
4. Observera den **arbetsyte-ID** värde under vänster på sidan Inställningar.  Du kan verifiera den mot din Operations Manager-hanteringsgrupp nedan.  

### <a name="to-confirm-integration-from-the-operations-console"></a>Bekräfta integration från Operations-konsolen
1. Öppna Operations Manager-konsolen och välj arbetsytan **Administration**.
2. Välj **hanteringspaket** och i den **letar:** Skriv text **Advisor** eller **Intelligence**.
3. Beroende på de lösningar som du har aktiverat kan du se ett motsvarande hanteringspaket som visas i sökresultaten.  Om du har aktiverat aviseringen hanteringslösningen, till exempel är hanteringspaket Microsoft System Center Advisor Alert Management i listan.
4. Från den **övervakning** visa, navigera till den **Operations hanteringstillstånd Suite\Health** vyn.  Välj en hanteringsserver under den **Hanteringsservertillstånd** fönstret och i den **detaljvy** rutan Bekräfta värdet för egenskapen **URI för autentiseringstjänst** matchar OMS arbetsyte-ID.
   
   ![oms-opsmgr-mg-authsvcuri-property-ms](./media/log-analytics-om-agents/oms-opsmgr-mg-authsvcuri-property-ms.png)

## <a name="remove-integration-with-oms"></a>Ta bort Integration med OMS
Det finns flera steg som krävs för att ta bort anslutning och konfiguration korrekt i hanteringsgruppen när du inte längre behöver integrering mellan Operations Manager-hanteringsgruppen och OMS-arbetsyta. Följande procedur har du uppdatera din OMS-arbetsyta genom att ta bort referensen till hanteringsgruppen, ta bort OMS-kopplingar och ta sedan bort hanteringspaket stöder OMS.   

Hanteringspaketen för lösningarna som du har aktiverat som integreras med Operations Manager och de hanteringspaket som krävs för att stödja integration med OMS-tjänsten kan inte enkelt tas bort från hanteringsgruppen.  Det beror på att vissa av OMS-hanteringspaket har beroenden i andra relaterade management packs.  Hämta skriptet för att ta bort hanteringspaket med ett beroende på andra hanteringspaket [ta bort ett hanteringspaket med beroenden](https://gallery.technet.microsoft.com/scriptcenter/Script-to-remove-a-84f6873e) från TechNet Script Center.  

1. Öppna Kommandotolken för Operations Manager med ett konto som är medlem i rollen Operations Manager-administratörer.
   
    > [!WARNING]
    > Verifiera du inte har några anpassade hanteringspaket med word Advisor eller IntelligencePack i namnet innan du fortsätter, annars följande tas bort från hanteringsgruppen.
    > 

2. Från Kommandotolken, skriver du: `Get-SCOMManagementPack -name "*Advisor*" | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
3. Nästa typ `Get-SCOMManagementPack -name “*IntelligencePack*” | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
4. Ta bort återstående som är beroende av andra hanteringspaket för System Center Advisor management packs med skriptet *RecursiveRemove.ps1* du tidigare hämtade från TechNet Script Center.  
 
    > [!NOTE]
    > Ta inte bort Microsoft System Center Advisor eller Microsoft System Center Advisor internt hanteringspaket.  
    >  

5. Öppna Operations Manager Operations-konsolen med ett konto som är medlem i rollen Operations Manager-administratörer.
6. Under **Administration**, Välj den **hanteringspaket** nod och i den **letar:** skriver **Advisor** och kontrollera följande hanteringspaket importeras fortfarande i hanteringsgruppen:
   
   * Microsoft System Center Advisor
   * Microsoft System Center Advisor internt
7. Klicka på panelen **Inställningar** på OMS-portalen.
8. Välj **anslutna källor**.
9. Du bör se namnet på hanteringsgruppen som du vill ta bort från arbetsytan i tabellen i avsnittet System Center Operations Manager.  Under kolumnen **senaste Data**, klickar du på **ta bort**.  
   
    > [!NOTE]
    > Den **ta bort** länken blir inte tillgängliga förrän efter 14 dagar om ingen aktivitet upptäckts från den anslutna hanteringsgruppen.  
    > 

10. Ett fönster visas där du uppmanas att bekräfta att du vill ta bort volymen.  Klicka på **Ja** att fortsätta. 

Ta bort två kopplingar - Microsoft.SystemCenter.Advisor.DataConnector Advisor Connector spara PowerShell-skriptet nedan på datorn och utför med hjälp av följande exempel:

```
    .\OM2012_DeleteConnector.ps1 “Advisor Connector” <ManagementServerName>
    .\OM2012_DeleteConnectors.ps1 “Microsoft.SytemCenter.Advisor.DataConnector” <ManagementServerName>
```

> [!NOTE]
> Den dator som du kör skriptet från, om inte en hanteringsserver, bör ha Operations Manager-kommandogränssnittet installerat beroende på vilken version av hanteringsgruppen.
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

I framtiden om du planerar att ansluta din hanteringsgrupp till en OMS-arbetsyta, måste du importera den `Microsoft.SystemCenter.Advisor.Resources.\<Language>\.mpb` management pack-filen från den senaste samlade uppdateringen som tillämpas på din hanteringsgrupp.  Du hittar den här filen i den `%ProgramFiles%\Microsoft System Center 2012` eller `System Center 2012 R2\Operations Manager\Server\Management Packs for Update Rollups` mapp.

## <a name="next-steps"></a>Nästa steg
Om du vill lägga till funktioner och samla in data, se [lägga till logganalys lösningar från galleriet lösningar](log-analytics-add-solutions.md).


