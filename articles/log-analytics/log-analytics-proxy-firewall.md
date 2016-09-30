<properties
    pageTitle="Konfigurera inställningar för proxy och brandvägg i Log Analytics | Microsoft Azure"
    description="Konfigurera inställningar för proxy och brandvägg när dina agenter eller OMS-tjänster behöver använda specifika portar."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/23/2016"
    ms.author="banders;magoedte"/>


# Konfigurera inställningar för proxy och brandvägg i Log Analytics

Åtgärder som krävs för att konfigurera proxy- och brandväggsinställningar för Log Analytics i OMS skiljer sig åt när du använder Operations Manager och dess agenter, jämfört med Microsoft Monitoring Agents som ansluter direkt till servrar. Läs följande avsnitt för den typ av agent som du använder.

## Konfigurera inställningar för proxy och brandvägg med Microsoft Monitoring Agent

För att Microsoft Monitoring Agent ska kunna ansluta till och registrera med OMS-tjänsten, måste den ha åtkomst till portnumret för dina domäner och URL:erna. Om du använder en proxyserver för kommunikation mellan agenten och OMS-tjänsten måste du se till att lämpliga resurser är tillgängliga. Om du använder en brandvägg för att begränsa åtkomsten till Internet, måste du konfigurera brandväggen att tillåta åtkomst till OMS. Följande tabeller visar vilka portar som OMS behöver.

|**Agentresurs**|**Portar**|**Kringgå HTTPS-kontroll**|
|--------------|-----|--------------|
|\*.ods.opinsights.azure.com|443|Ja|
|\*.oms.opinsights.azure.com|443|Ja|
|\*.blob.core.windows.net|443|Ja|
|ods.systemcenteradvisor.com|443| |

Du kan använda följande procedur för att konfigurera proxyinställningar för Microsoft Monitoring Agent med Kontrollpanelen. Du behöver använda proceduren för varje server. Om du har många servrar att konfigurera är det kanske enklare att använda ett skript för att automatisera processen. I så fall kan du läsa mer i nästa procedur [Konfigurera proxyinställningar för Microsoft Monitoring Agent med hjälp av ett skript](#to-configure-proxy-settings-for-the-microsoft-monitoring-agent-using-a-script).

### Konfigurera proxyinställningar för Microsoft Monitoring Agent med hjälp av Kontrollpanelen

1. Öppna **Kontrollpanelen**.

2. Öppna **Microsoft Monitoring Agent**.

3. Klicka på fliken **Proxyinställningar**.<br>  
  ![fliken proxyinställningar](./media/log-analytics-proxy-firewall/proxy-direct-agent-proxy.png)

4. Välj **Använd en proxyserver** och ange URL och portnummer, om det behövs, som i exemplet nedan. Om proxyservern kräver autentisering anger du användarnamn och lösenord för att få åtkomst till proxyservern.

Använd följande procedur för att skapa ett PowerShell-skript som du kan köra för att ange proxyinställningarna för varje agent som ansluter direkt till servrarna.

### Konfigurera proxyinställningar för Microsoft Monitoring Agent med hjälp av ett skript

Kopiera följande exempel, uppdatera den information som är specifik för din miljö, spara filen med filnamnstillägget PS1 och kör sedan skriptet på varje dator som ansluter direkt till OMS-tjänsten.

        
    param($ProxyDomainName="http://proxy.contoso.com:80", $cred=(Get-Credential))

    # First we get the Health Service configuration object.  We need to determine if we
    #have the right update rollup with the API we need.  If not, no need to run the rest of the script.
    $healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'

    $proxyMethod = $healthServiceSettings | Get-Member -Name 'SetProxyInfo'

    if (!$proxyMethod)
    {
         Write-Output 'Health Service proxy API not present, will not update settings.'
         return
    }

    Write-Output "Clearing proxy settings."
    $healthServiceSettings.SetProxyInfo('', '', '')

    $ProxyUserName = $cred.username

    Write-Output "Setting proxy to $ProxyDomainName with proxy username $ProxyUserName."
    $healthServiceSettings.SetProxyInfo($ProxyDomainName, $ProxyUserName, $cred.GetNetworkCredential().password)
        

## Konfigurera inställningar för proxy och brandvägg med Operations Manager

För att Operations Manager-hanteringsgruppen ska kunna ansluta till och registrera med OMS-tjänsten, måste den ha åtkomst till portnumren för dina domäner och URL:er. Om du använder en proxyserver för kommunikation mellan Operations Manager-hanteringsservern och OMS-tjänsten, måste du se till att lämpliga resurser är tillgängliga. Om du använder en brandvägg för att begränsa åtkomsten till Internet, måste du konfigurera brandväggen att tillåta åtkomst till OMS. Även om en Operations Manager-hanteringsserver inte finns bakom en proxyserver kanske dess agenter gör det. I det här fallet ska proxyservern konfigureras på samma sätt som agenterna för att kunna aktivera och tillåta att säkerhets- och logghanteringsdata skickas till OMS-webbtjänsten.

För att Operations Manager-agenter ska kunna kommunicera med OMS-tjänsten måste Operations Manager-infrastrukturen (inklusive agenter) ha rätt proxyinställningar och version. Proxyinställningen för agenter har angetts i Operations Manager-konsolen. Din version måste vara något av följande:

- Operations Manager 2012 SP1, samlad uppdatering 7 eller senare
- Operations Manager 2012 R2, samlad uppdatering 3 eller senare


Följande tabeller visar vilka portar som är relaterade till dessa aktiviteter.

>[AZURE.NOTE] Några av följande resurser nämner Advisor och Operational Insights, som båda var tidigare versioner av OMS. Resurserna i listan kommer dock att ändras i framtiden.

Här är en lista med resurser och portar för agenten:<br>

|**Agentresurs**|**Portar**|
|--------------|-----|
|\*.ods.opinsights.azure.com|443|
|\*.oms.opinsights.azure.com|443|
|\*.blob.core.windows.net/\*|443|
|ods.systemcenteradvisor.com|443|
<br>
Här är en lista med resurser och portar för hanteringsservern:<br>

|**Hanteringsserverresurs**|**Portar**|**Kringgå HTTPS-kontroll**|
|--------------|-----|--------------|
|service.systemcenteradvisor.com|443| |
|\*.service.opinsights.azure.com|443| |
|\*.blob.core.windows.net|443|Ja| 
|data.systemcenteradvisor.com|443| | 
|ods.systemcenteradvisor.com|443| | 
|\*.ods.opinsights.azure.com|443|Ja| 
<br>
Här är en lista med resurser och portar för OMS och Operations Manager-konsolen.<br>

|**Resursen OMS och Operations Manager-konsolen**|**Portar**|
|----|----|
|service.systemcenteradvisor.com|443|
|\*.service.opinsights.azure.com|443|
|\*.live.com|Port 80 och 443|
|\*.microsoft.com|Port 80 och 443|
|\*.microsoftonline.com|Port 80 och 443|
|\*.mms.microsoft.com|Port 80 och 443|
|login.windows.net|Port 80 och 443|
<br>

Använd följande procedurer för att registrera din Operations Manager-hanteringsgrupp med OMS-tjänsten. Om du har kommunikationsproblem mellan hanteringsgruppen och OMS-tjänsten använder du verifieringsstegen för att felsöka dataöverföringen till OMS-tjänsten.

### Så här begär du undantag för OMS-tjänstens slutpunkter

1. Använd informationen från den första tabellen som visades tidigare för att säkerställa att de resurser som krävs för Operations Manager-hanteringsservern kan nås via eventuella brandväggar du kanske har.
2. Använd informationen från den andra tabellen som visas tidigare för att säkerställa att de resurser som krävs för Operations-konsolen i Operations Manager och OMS kan nås via eventuella brandväggar du kanske har.
3. Om du använder en proxyserver med Internet Explorer, bör du kontrollera att den har konfigurerats och fungerar korrekt. Kontrollera detta genom att öppna en säker webbanslutning (HTTPS), till exempel [https://bing.com](https://bing.com). Om den säkra webbanslutningen inte fungerar i en webbläsare, fungerar det förmodligen inte i Operations Manager-hanteringskonsolen med webbtjänster i molnet.

### Konfigurera proxyservern i Operations Manager-konsolen

1. Öppna Operations Manager-konsolen och välj arbetsytan **Administration**.

2. Expandera **Operational Insights** och välj sedan **Anslutning till Operational Insights**.<br>  
    ![OMS-anslutning i Operations Manager](./media/log-analytics-proxy-firewall/proxy-om01.png)
3. I vyn OMS-anslutning klickar du på **Konfigurera proxyserver**.<br>  
    ![Konfigurera proxyserver för OMS-anslutningen i Operations Manager](./media/log-analytics-proxy-firewall/proxy-om02.png)
4. I guiden Operational Insights-inställningar: Proxyserver väljer du **Använd en proxyserver för att få åtkomst till Operational Insights-webbtjänsten**. Skriv sedan webbadressen med portnumret, till exempel **http://myproxy:80**.<br>  
    ![OMS-proxyadress i Operations Manager](./media/log-analytics-proxy-firewall/proxy-om03.png)


### Ange autentiseringsuppgifter om proxyservern kräver autentisering
 Inställningar och autentiseringsuppgifter för proxyservern måste spridas till hanterade datorer som rapporterar till OMS. Servrarna måste finnas i *Övervakning av servergrupp i Microsoft System Center Advisor*. Autentiseringsuppgifterna krypteras i registret på varje server i gruppen.

1. Öppna Operations Manager-konsolen och välj arbetsytan **Administration**.
2. Under **Kör som-konfiguration** väljer du **Profiler**.
3. Öppna profilen **Kör som-profilproxy för System Center Advisor**.  
    ![bild av Kör som-profilproxy för System Center Advisor](./media/log-analytics-proxy-firewall/proxy-proxyacct1.png)
4. I guiden Kör som-profil klickar du på **Lägg till** för att använda ett kör som-konto. Du kan skapa ett nytt kör som-konto eller använda ett befintligt konto. Det här kontot måste ha tillräcklig behörighet för att kunna passera genom proxyservern.  
    ![bild av guiden Kör som-profil](./media/log-analytics-proxy-firewall/proxy-proxyacct2.png)
5. Ange vilket konto som ska hanteras genom att välja **En vald klass, grupp eller objekt** för att öppna rutan Sök efter objekt.  
    ![bild av guiden Kör som-profil](./media/log-analytics-proxy-firewall/proxy-proxyacct2-1.png)
6. Sök och välj sedan **Övervakning av servergrupp i Microsoft System Center Advisor**.  
    ![bild av rutan Sök efter objekt](./media/log-analytics-proxy-firewall/proxy-proxyacct3.png)
7. Klicka på **OK** för att stänga rutan Lägg till ett kör som-konto.  
    ![bild av guiden Kör som-profil](./media/log-analytics-proxy-firewall/proxy-proxyacct4.png)
8. Slutför guiden och spara ändringarna.  
    ![bild av guiden Kör som-profil](./media/log-analytics-proxy-firewall/proxy-proxyacct5.png)


### Kontrollera att OMS-hanteringspaketen laddas ned

Om du har lagt till lösningar i OMS, kan du visa dem i Operations Manager-konsolen som hanteringspaket under **Administration**. Sök efter *System Center Advisor* för att snabbt hitta dem.  
    ![hanteringspaket som laddats ner](./media/log-analytics-proxy-firewall/proxy-mpdownloaded.png) Du kan också leta efter OMS-hanteringspaket genom att använda följande Windows PowerShell-kommando i Operations Manager-hanteringsservern:

    ```
    Get-ScomManagementPack | where {$_.DisplayName -match 'Advisor'} | select Name,DisplayName,Version,KeyToken
    ```

### Kontrollera att Operations Manager skickar data till OMS-tjänsten

1. Öppna Prestandaövervakaren (perfmon.exe) i Operations Manager-hanteringsservern och välj **Prestandaövervakaren**.
2. Klicka på **Lägg till** och välj sedan **Hanteringsgrupper för hälsotillståndstjänst**.
3. Lägg till alla räknare som börjar med **HTTP**.  
    ![lägga till räknare](./media/log-analytics-proxy-firewall/proxy-sendingdata1.png)
4. Om din Operations Manager-konfiguration är bra ser du aktiviteten för hälsotillståndstjänstens räknare för händelser och andra dataobjekt, baserat på de hanteringspaket som du lade till i OMS och den konfigurerade loggsamlingsprincipen.  
    ![Visa aktivitet för prestandaövervakaren](./media/log-analytics-proxy-firewall/proxy-sendingdata2.png)


## Nästa steg

- [Lägg till Log Analytics-lösningar från lösningsgalleriet](log-analytics-add-solutions.md) för att lägga till funktioner och samla in data.
- Bekanta dig med [loggsökningar](log-analytics-log-searches.md) för att visa detaljerad information som samlas in av lösningar.



<!--HONumber=Sep16_HO3-->


