---
title: 'Azure AD Connect-synkronisering: Driftåtgärder och saker du | Microsoft Docs'
description: Det här avsnittet beskriver operativa uppgifter för Azure AD Connect-synkronisering och hur du förbereder för den här komponenten.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: b29c1790-37a3-470f-ab69-3cee824d220d
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 176b8509892ef16b631697a686471e7fa52bb380
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/01/2019
ms.locfileid: "57196371"
---
# <a name="azure-ad-connect-staging-server-and-disaster-recovery"></a>Azure AD Connect: Mellanlagringsserver och katastrofåterställning
Du kan göra ändringar i konfigurationen och granska ändringarna innan du gör att servern är aktiv med en server i mellanlagringsläge. Du kan också köra fullständig import och fullständig synkronisering för att kontrollera att alla ändringar som förväntat innan du gör dessa ändringar till din produktionsmiljö.

## <a name="staging-mode"></a>Mellanlagringsläge
Mellanlagringsläge kan användas för flera scenarier, inklusive:

* Hög tillgänglighet.
* Testa och distribuera nya konfigurationsändringar.
* Introducerar en ny server och inaktivera gammalt.

Under installationen väljer du server ska vara i **mellanlagringsläge**. Den här åtgärden gör servern som är aktiv för import och synkronisering, men inga exporter körs. En server i mellanlagringsläge är inte igång Lösenordssynkronisering eller tillbakaskrivning av lösenord, även om du har valt funktionerna under installationen. När du inaktiverar mellanlagringsläget servern börjar exporten, aktiverar synkronisering av lösenord och aktivera tillbakaskrivning av lösenord.

> [!NOTE]
> Anta att du har en Azure AD Connect för synkronisering av lösenords-hash-funktionen är aktiverad. När du aktiverar mellanlagringsläge, server-stoppar synkronisering lösenordet ändras från lokala AD. När du inaktiverar mellanlagringsläget återupptar servern Synkronisera lösenordsändringar från där den senast avbröts. Om servern är kvar i mellanlagringsläge under en längre tid, kan det ta en stund innan servern för att synkronisera alla lösenordsändringar som har inträffat under tidsperioden.
>
>

Du kan fortfarande tvinga en export med hjälp av hanteraren för synkroniseringstjänsten.

En server i mellanlagringsläge fortsätter att ta emot ändringar från Active Directory och Azure AD. Den har alltid en kopia av de senaste ändringarna och kan mycket snabbt ta över ansvaret för en annan server. Om du gör ändringar i konfigurationen i den primära servern, är det ditt ansvar att göra samma ändringar till servern i mellanlagringsläge.

För dig med kunskap om äldre sync-tekniker skiljer i mellanlagringsläge sig eftersom servern har en egen SQL-databas. Den här arkitekturen kan mellanlagringsserver läge för att finnas i ett annat datacenter.

### <a name="verify-the-configuration-of-a-server"></a>Kontrollera konfigurationen för en server
Följ dessa steg om du vill använda den här metoden:

1. [Förbereda](#prepare)
2. [Konfiguration](#configuration)
3. [Importera och synkronisera](#import-and-synchronize)
4. [Verifiera](#verify)
5. [Växla aktiv server](#switch-active-server)

#### <a name="prepare"></a>Förbereda
1. Installera Azure AD Connect, Välj **mellanlagringsläge**, och avmarkera **starta synkroniseringen** på den sista sidan i installationsguiden. Det här läget kan du köra Synkroniseringsmotorn manuellt.
   ![ReadyToConfigure](./media/how-to-connect-sync-staging-server/readytoconfigure.png)
2. Logga ut/logga i och start-menyn väljer **synkroniseringstjänsten**.

#### <a name="configuration"></a>Konfiguration
Om du har gjort egna ändringar till den primära servern och vill jämföra konfigurationen med fristående server, använder du [Azure AD Connect configuration Dokumenteraren](https://github.com/Microsoft/AADConnectConfigDocumenter).

#### <a name="import-and-synchronize"></a>Importera och synkronisera
1. Välj **Anslutningsappar**, och välj den första anslutningen med typen **Active Directory Domain Services**. Klicka på **kör**väljer **fullständig import**, och **OK**. Utföra de här stegen för alla kopplingar av den här typen.
2. Välj kopplingen med typen **Azure Active Directory (Microsoft)**. Klicka på **kör**väljer **fullständig import**, och **OK**.
3. Kontrollera att fliken kopplingar fortfarande är markerat. För varje koppling med typen **Active Directory Domain Services**, klickar du på **kör**väljer **Deltasynkronisering**, och **OK**.
4. Välj kopplingen med typen **Azure Active Directory (Microsoft)**. Klicka på **kör**väljer **Deltasynkronisering**, och **OK**.

Du har nu mellanlagrade export i Azure AD och lokalt AD (om du använder Exchange-hybridinstallation). Nästa steg kan du granska nyheter ska ändras innan du startar export till katalogerna.

#### <a name="verify"></a>Verifiera
1. Starta en kommandotolk och gå till `%ProgramFiles%\Microsoft Azure AD Sync\bin`
2. Kör: `csexport "Name of Connector" %temp%\export.xml /f:x` Namnet på anslutningen finns i synkroniseringstjänsten. Den har ett namn som liknar ”contoso.com – AAD” för Azure AD.
3. Kör: `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv` Du har en fil i % temp % med namnet export.csv som kan granskas i Microsoft Excel. Den här filen innehåller alla ändringar som är på väg att exporteras.
4. Gör nödvändiga ändringar i informationen eller konfigurationen och köra de här stegen igen (importera och synkronisera och verifiera) tills de ändringar som är på väg att exporteras förväntas.

**Förstå filen export.csv** merparten av filen är självförklarande. Vissa förkortningar att förstå innehållet:
* OMODT – ändring av objekttyp. Anger om åtgärden på objektnivå är en Lägg till, uppdatera eller ta bort.
* AMODT – ändring av attributtypen. Anger om åtgärden på attributnivå är en Lägg till, uppdatera eller ta bort.

**Hämta vanliga identifierare** export.csv-filen innehåller alla ändringar som är på väg att exporteras. Varje rad motsvarar en ändring för ett objekt i anslutarplatsen och objektet har identifierats av attributet DN. DN-attributet är en unik identifierare som tilldelats till ett objekt i anslutarplatsen. När du har många rader/ändringar i export.csv att analysera, kan det vara svårt att ta reda på vilka objekt ändringarna är för baserat på attributet DN enbart. Använd csanalyzer.ps1 PowerShell-skript för att förenkla analysera ändringarna. Skriptet hämtar vanliga identifierare (till exempel displayName, userPrincipalName) av objekt. Du använder skriptet:
1. Kopiera PowerShell-skriptet från avsnittet [CSAnalyzer](#appendix-csanalyzer) till en fil med namnet `csanalyzer.ps1`.
2. Öppna ett PowerShell-fönster och bläddra till mappen där du skapade PowerShell-skriptet.
3. Run: `.\csanalyzer.ps1 -xmltoimport %temp%\export.xml`.
4. Nu har du en fil med namnet **processedusers1.csv** som kan granskas i Microsoft Excel. Observera att filen innehåller en mappning från attributet DN till vanliga identifierare (till exempel displayName och userPrincipalName). För närvarande omfattar inte de faktiska attributändringar som ska exporteras.

#### <a name="switch-active-server"></a>Växla aktiv server
1. Inaktivera server (DirSync/FIM/Azure AD Sync) så att den inte exporteras till Azure AD eller ställa in den i mellanlagringsläge (Azure AD Connect) på den aktiva servern.
2. Kör installationsguiden på servern i **mellanlagringsläge** och inaktivera **mellanlagringsläge**.
   ![ReadyToConfigure](./media/how-to-connect-sync-staging-server/additionaltasks.png)

## <a name="disaster-recovery"></a>Haveriberedskap
En del av implementeringsdesign är att planera för vad du gör i händelse av ett haveri där du skulle förlora synkroniseringsservern. Det finns olika modeller ska användas och vilken jag ska använda beror på flera faktorer, inklusive:

* Vad är toleransen för att du inte är objekt kan göra ändringar i Azure AD under driftstoppet?
* Om du använder Lösenordssynkronisering måste användarna accepterar att de behöver använda det gamla lösenordet i Azure AD, om de ändra den lokala?
* Har du ett beroende på i realtid åtgärder, till exempel tillbakaskrivning av lösenord?

Beroende på svar på frågorna och organisationens princip, kan en av följande strategier implementeras:

* Återskapa när det behövs.
* Har en ledig standby-server, kallas **mellanlagringsläge**.
* Använda virtuella datorer.

Om du inte använder den inbyggda SQL Express-databasen så du bör också läsa igenom den [hög tillgänglighet för SQL](#sql-high-availability) avsnittet.

### <a name="rebuild-when-needed"></a>Återskapa vid behov
Ett genomförbart strategin är att planera för återskapning av en server när det behövs. Vanligtvis installera Synkroniseringsmotorn och gör den initiala importen och synkroniseringen kan slutföras inom några timmar. Om det finns inte en ledig server, är det möjligt att tillfälligt använda en domänkontrollant som värd Synkroniseringsmotorn.

Synkronisera engine-server lagrar inte några tillstånd om objekt så att databasen kan återskapas från data i Active Directory och Azure AD. Den **sourceAnchor** attributet används för att ansluta till objekt från både lokalt och molnet. Om du återskapa servern med befintliga objekt lokalt och molnet matchar Synkroniseringsmotorn objekten tillsammans igen på ominstallation. De saker som du behöver för att dokumentera och spara är konfigurationsändringar gjorda på servern, till exempel filtrering och Synkroniseringsregler. Dessa anpassade konfigurationer måste återställas innan du startar synkroniseringen.

### <a name="have-a-spare-standby-server---staging-mode"></a>Har en ledig standby-server – mellanlagringsläge
Om du har en mer komplex miljö kan rekommenderas ha en eller flera reservservrar. Under installationen kan du aktivera en server i **mellanlagringsläge**.

Mer information finns i [mellanlagringsläge](#staging-mode).

### <a name="use-virtual-machines"></a>Använda virtuella datorer
En gemensam och stöds metod är att köra Synkroniseringsmotorn i en virtuell dator. Om värden har ett problem, kan avbildningen med motorn synkroniseringsserver migreras till en annan server.

### <a name="sql-high-availability"></a>Hög tillgänglighet för SQL
Om du inte använder den SQL Server Express som medföljer Azure AD Connect, bör sedan hög tillgänglighet för SQL Server också övervägas. Lösningar för hög tillgänglighet som stöds är SQL-klustring och AOA (alltid på Tillgänglighetsgrupper). Stöds inte lösningar omfattar spegling.

Stöd för SQL AOA lades till i Azure AD Connect i version 1.1.524.0. Innan du installerar Azure AD Connect måste du aktivera SQL AOA. Under installationen identifierar Azure AD Connect om den angivna SQL-instansen är aktiverat för SQL AOA eller inte. Om SQL AOA är aktiverad, räknat Azure AD Connect ytterligare ut om SQL AOA har konfigurerats för att använda synkron replikering eller asynkron replikering. När du konfigurerar tillgänglighetsgruppens lyssnare, rekommenderas att du ställer in egenskapen RegisterAllProvidersIP till 0. Detta beror på Azure AD Connect för närvarande använder SQL Native Client för att ansluta till SQL och SQL Native Client stöder inte användning av egenskapen MultiSubNetFailover.

## <a name="appendix-csanalyzer"></a>Appendix CSAnalyzer
Se avsnittet [Kontrollera](#verify) om hur du använder det här skriptet.

```
Param(
    [Parameter(Mandatory=$true, HelpMessage="Must be a file generated using csexport 'Name of Connector' export.xml /f:x)")]
    [string]$xmltoimport="%temp%\exportedStage1a.xml",
    [Parameter(Mandatory=$false, HelpMessage="Maximum number of users per output file")][int]$batchsize=1000,
    [Parameter(Mandatory=$false, HelpMessage="Show console output")][bool]$showOutput=$false
)

#LINQ isn't loaded automatically, so force it
[Reflection.Assembly]::Load("System.Xml.Linq, Version=3.5.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089") | Out-Null

[int]$count=1
[int]$outputfilecount=1
[array]$objOutputUsers=@()

#XML must be generated using "csexport "Name of Connector" export.xml /f:x"
write-host "Importing XML" -ForegroundColor Yellow

#XmlReader.Create won't properly resolve the file location,
#so expand and then resolve it
$resolvedXMLtoimport=Resolve-Path -Path ([Environment]::ExpandEnvironmentVariables($xmltoimport))

#use an XmlReader to deal with even large files
$result=$reader = [System.Xml.XmlReader]::Create($resolvedXMLtoimport) 
$result=$reader.ReadToDescendant('cs-object')
do 
{
    #create the object placeholder
    #adding them up here means we can enforce consistency
    $objOutputUser=New-Object psobject
    Add-Member -InputObject $objOutputUser -MemberType NoteProperty -Name ID -Value ""
    Add-Member -InputObject $objOutputUser -MemberType NoteProperty -Name Type -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name DN -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name operation -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name UPN -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name displayName -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name sourceAnchor -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name alias -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name primarySMTP -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name onPremisesSamAccountName -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name mail -Value ""

    $user = [System.Xml.Linq.XElement]::ReadFrom($reader)
    if ($showOutput) {Write-Host Found an exported object... -ForegroundColor Green}

    #object id
    $outID=$user.Attribute('id').Value
    if ($showOutput) {Write-Host ID: $outID}
    $objOutputUser.ID=$outID

    #object type
    $outType=$user.Attribute('object-type').Value
    if ($showOutput) {Write-Host Type: $outType}
    $objOutputUser.Type=$outType

    #dn
    $outDN= $user.Element('unapplied-export').Element('delta').Attribute('dn').Value
    if ($showOutput) {Write-Host DN: $outDN}
    $objOutputUser.DN=$outDN

    #operation
    $outOperation= $user.Element('unapplied-export').Element('delta').Attribute('operation').Value
    if ($showOutput) {Write-Host Operation: $outOperation}
    $objOutputUser.operation=$outOperation

    #now that we have the basics, go get the details

    foreach ($attr in $user.Element('unapplied-export-hologram').Element('entry').Elements("attr"))
    {
        $attrvalue=$attr.Attribute('name').Value
        $internalvalue= $attr.Element('value').Value

        switch ($attrvalue)
        {
            "userPrincipalName"
            {
                if ($showOutput) {Write-Host UPN: $internalvalue}
                $objOutputUser.UPN=$internalvalue
            }
            "displayName"
            {
                if ($showOutput) {Write-Host displayName: $internalvalue}
                $objOutputUser.displayName=$internalvalue
            }
            "sourceAnchor"
            {
                if ($showOutput) {Write-Host sourceAnchor: $internalvalue}
                $objOutputUser.sourceAnchor=$internalvalue
            }
            "alias"
            {
                if ($showOutput) {Write-Host alias: $internalvalue}
                $objOutputUser.alias=$internalvalue
            }
            "proxyAddresses"
            {
                if ($showOutput) {Write-Host primarySMTP: ($internalvalue -replace "SMTP:","")}
                $objOutputUser.primarySMTP=$internalvalue -replace "SMTP:",""
            }
        }
    }

    $objOutputUsers += $objOutputUser

    Write-Progress -activity "Processing ${xmltoimport} in batches of ${batchsize}" -status "Batch ${outputfilecount}: " -percentComplete (($objOutputUsers.Count / $batchsize) * 100)

    #every so often, dump the processed users in case we blow up somewhere
    if ($count % $batchsize -eq 0)
    {
        Write-Host Hit the maximum users processed without completion... -ForegroundColor Yellow

        #export the collection of users as a CSV
        Write-Host Writing processedusers${outputfilecount}.csv -ForegroundColor Yellow
        $objOutputUsers | Export-Csv -path processedusers${outputfilecount}.csv -NoTypeInformation

        #increment the output file counter
        $outputfilecount+=1

        #reset the collection and the user counter
        $objOutputUsers = $null
        $count=0
    }

    $count+=1

    #need to bail out of the loop if no more users to process
    if ($reader.NodeType -eq [System.Xml.XmlNodeType]::EndElement)
    {
        break
    }

} while ($reader.Read)

#need to write out any users that didn't get picked up in a batch of 1000
#export the collection of users as CSV
Write-Host Writing processedusers${outputfilecount}.csv -ForegroundColor Yellow
$objOutputUsers | Export-Csv -path processedusers${outputfilecount}.csv -NoTypeInformation
```

## <a name="next-steps"></a>Nästa steg
**Översiktsavsnitt**  

* [Azure AD Connect-synkronisering: Förstå och anpassa synkronisering](how-to-connect-sync-whatis.md)  
* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)  
