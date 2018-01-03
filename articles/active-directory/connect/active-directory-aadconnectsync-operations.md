---
title: "Azure AD Connect-synkronisering: driftåtgärder och saker | Microsoft Docs"
description: "Det här avsnittet beskriver åtgärder för Azure AD Connect-synkronisering och hur du förbereder för användning av den här komponenten."
services: active-directory
documentationcenter: 
author: AndKjell
manager: mtillman
editor: 
ms.assetid: b29c1790-37a3-470f-ab69-3cee824d220d
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: 4252a5ff3dd985185745060073cbe5432dba726a
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/20/2017
---
# <a name="azure-ad-connect-sync-operational-tasks-and-consideration"></a>Azure AD Connect-synkronisering: driftåtgärder och beräkningen
Syftet med det här avsnittet är att beskriva operativa uppgifter för Azure AD Connect-synkronisering.

## <a name="staging-mode"></a>Mellanlagringsläge
Mellanlagringsläge kan användas för flera olika scenarier, inklusive:

* Hög tillgänglighet.
* Testa och distribuera nya konfigurationsändringar.
* Introducerar en ny server och inaktivera gammalt.

Du kan göra ändringar i konfigurationen och förhandsgranska ändringarna innan du gör att servern är aktiv med en server i mellanlagringsläge. Du kan också köra fullständig import och fullständig synkronisering för att kontrollera att alla ändringar som förväntat innan du gör dessa ändringar till din produktionsmiljö.

Under installationen, väljer du den server som ska vara i **mellanlagringsläge**. Den här åtgärden gör servern aktivt för import och synkronisering, men några exporter körs inte. En server i fristående läge körs inte Lösenordssynkronisering och tillbakaskrivning av lösenord, även om dessa funktioner har valts under installationen. När du inaktiverar mellanlagringsläget börjar exporten, aktiverar Lösenordssynkronisering och aktiverar tillbakaskrivning av lösenord.

> [!NOTE]
> Anta att du har en Azure AD Connect för synkronisering av lösenords-hash-funktionen är aktiverad. När du aktiverar mellanlagringsläge, server-slutar som synkroniserar lösenord ändras från lokala AD. När du inaktiverar mellanlagringsläget återupptar servern Synkronisera lösenordsändringar från där den senast avbröts. Om servern är kvar i mellanlagringsläge under en längre tidsperiod, kan den ta en stund för servern för att synkronisera alla lösenordsändringar som inträffat under tidsperioden.
>
>

Du kan fortfarande framtvinga en export med hjälp av hanteraren för synkroniseringstjänsten.

En server i mellanlagringsläge fortsätter att ta emot ändringar från Active Directory och Azure AD. Det har alltid en kopia av de senaste ändringarna och kan snabbt ta över ansvar för en annan server. Om du gör konfigurationsändringar till den primära servern är ditt ansvar att göra samma ändringar till servern i mellanlagringsläge.

I mellanlagringsläge skiljer sig för de med kunskap om äldre sync-tekniker, eftersom servern har en egen SQL-databas. Den här arkitekturen kan testservern läge att ska finnas i olika datacenter.

### <a name="verify-the-configuration-of-a-server"></a>Kontrollera konfigurationen av en server
Följ dessa steg om du vill använda den här metoden:

1. [Förbereda](#prepare)
2. [Konfiguration](#configuration)
3. [Importera och synkronisera](#import-and-synchronize)
4. [Kontrollera](#verify)
5. [Växeln active server](#switch-active-server)

#### <a name="prepare"></a>Förbereda
1. Installera Azure AD Connect, Välj **mellanlagringsläge**, och avmarkera **starta synkroniseringen** på den sista sidan i installationsguiden. Det här läget kan du köra Synkroniseringsmotorn manuellt.
   ![ReadyToConfigure](./media/active-directory-aadconnectsync-operations/readytoconfigure.png)
2. Logga ut/logga i och från start-menyn väljer **synkroniseringstjänsten**.

#### <a name="configuration"></a>Konfiguration
Om du har gjort egna ändringar till den primära servern och vill jämföra konfigurationen med fristående server, använder du [Azure AD Connect configuration Dokumenteraren](https://github.com/Microsoft/AADConnectConfigDocumenter).

#### <a name="import-and-synchronize"></a>Importera och synkronisera
1. Välj **kopplingar**, och välj den första anslutningen med typen **Active Directory Domain Services**. Klicka på **kör**väljer **fullständig import**, och **OK**. Utföra de här stegen för alla kopplingar av den här typen.
2. Välj kopplingen med typen **Azure Active Directory (Microsoft)**. Klicka på **kör**väljer **fullständig import**, och **OK**.
3. Kontrollera att fliken kopplingar fortfarande är markerad. För varje koppling med typen **Active Directory Domain Services**, klickar du på **kör**väljer **Deltasynkronisering**, och **OK**.
4. Välj kopplingen med typen **Azure Active Directory (Microsoft)**. Klicka på **kör**väljer **Deltasynkronisering**, och **OK**.

Du har nu mellanlagrade export ändringar till Azure AD och lokala AD (om du använder Exchange-hybridinstallation). Nästa steg kan du granska vad är på väg att ändra innan du startar export till kataloger.

#### <a name="verify"></a>Verifiera
1. Starta en kommandotolk och gå till`%ProgramFiles%\Microsoft Azure AD Sync\bin`
2. Kör: `csexport "Name of Connector" %temp%\export.xml /f:x` namnet på anslutningen finns i synkroniseringstjänsten. Den har ett namn som liknar ”contoso.com – AAD” för Azure AD.
3. Kör: `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv` du har en fil i % temp % med namnet export.csv som kan granskas i Microsoft Excel. Den här filen innehåller alla ändringar som ska exporteras.
4. Gör nödvändiga ändringar i informationen eller konfigurationen och köra de här stegen igen (importera och synkronisera och kontrollera) tills de ändringar som är på väg att exporteras förväntas.

**Förstå filen export.csv** de flesta av filen är självförklarande. Vissa förkortningar att förstå innehållet:
* OMODT – objekttypen ändras. Anger om åtgärden på objektnivå har en Add-, Update- eller Delete.
* AMODT – attributtypen ändras. Anger om åtgärden på en attributnivå har en Add-, Update- eller delete.

**Hämta vanliga identifierare** export.csv-filen innehåller alla ändringar som ska exporteras. Varje rad motsvarar en ändring för ett objekt i anslutningsplatsen och objektet identifieras av DN-attributet. DN-attributet är en unik identifierare för ett objekt i anslutningsplatsen. När du har många rader/ändringar i export.csv att analysera vara det svårt att ta reda på vilka objekt ändras baserat på enbart DN-attribut. Använd csanalyzer.ps1 PowerShell-skript för att förenkla processen för att analysera ändringarna. Skriptet hämtar vanliga identifierare (till exempel displayName, userPrincipalName) för objekt. Du använder skriptet:
1. Kopiera PowerShell-skriptet från avsnittet [CSAnalyzer](#appendix-csanalyzer) till en fil med namnet `csanalyzer.ps1`.
2. Öppna ett PowerShell-fönster och bläddra till mappen där du skapade PowerShell-skript.
3. Kör: `.\csanalyzer.ps1 -xmltoimport %temp%\export.xml`.
4. Nu har du en fil med namnet **processedusers1.csv** som kan granskas i Microsoft Excel. Observera att filen innehåller mappning från DN-attribut till vanliga identifierare (till exempel displayName och userPrincipalName). För närvarande innehåller inte de faktiska attributändringar som ska exporteras.

#### <a name="switch-active-server"></a>Växeln active server
1. Inaktivera server (DirSync/FIM/Azure AD Sync) så att den inte exporterar till Azure AD eller Ställ in den i mellanlagringsläge (Azure AD Connect) på servern för närvarande är aktiva.
2. Kör installationsguiden på servern i **mellanlagringsläge** och inaktivera **mellanlagringsläge**.
   ![ReadyToConfigure](./media/active-directory-aadconnectsync-operations/additionaltasks.png)

## <a name="disaster-recovery"></a>Haveriberedskap
En del av implementeringen designen är att planera för vad du ska göra om det är en katastrof där du förlorar synkroniseringsservern. Det finns olika modeller ska användas och vilket som ska användas beror på flera faktorer, inklusive:

* Vad är din tolerans för som inte kan göra ändringar för objekt i Azure AD under avbrottstiden?
* Om du använder Lösenordssynkronisering måste användarna accepterar de behöver använda det gamla lösenordet i Azure AD, om de ändra den lokala?
* Har du ett beroende i realtid åtgärder, till exempel tillbakaskrivning av lösenord?

Beroende på svaren på dessa frågor och organisationens princip, kan något av följande strategier implementeras:

* Återskapa vid behov.
* En ledig belägna standby-servern, kallas även **mellanlagringsläge**.
* Använda virtuella datorer.

Om du inte använder inbyggda SQL Express-databasen, så du bör även se den [hög tillgänglighet för SQL](#sql-high-availability) avsnitt.

### <a name="rebuild-when-needed"></a>Återskapa vid behov
En lönsam strategi är att planera för återskapning av en server när det behövs. Installera vanligtvis Synkroniseringsmotorn och gör den inledande importen och synkroniseringen kan slutföras inom några timmar. Om det inte en extra server, är det möjligt att tillfälligt använda en domänkontrollant som värd för Synkroniseringsmotorn.

Motorn synkroniseringsserver lagrar inte någon status om objekt så att databasen kan byggas från data i Active Directory och Azure AD. Den **sourceAnchor** attributet används för att ansluta till objekt från molnet och lokalt. Om du återskapa servern med befintliga objekt lokalt och i molnet matchar Synkroniseringsmotorn de här objekten tillsammans igen på ominstallation. Saker du behöver dokumentera och spara är konfigurationsändringar på servern, till exempel filtrering och Synkroniseringsregler. Dessa anpassade konfigurationer måste återställas innan du startar synkroniseringen.

### <a name="have-a-spare-standby-server---staging-mode"></a>Har en ledig standby server - mellanlagringsläge
Om du har en mer komplex miljö rekommenderas med en eller flera reservservrar. Under installationen, kan du aktivera en server i **mellanlagringsläge**.

Mer information finns i [mellanlagringsläge](#staging-mode).

### <a name="use-virtual-machines"></a>Använda virtuella datorer
En gemensam och stöds metod är att köra Synkroniseringsmotorn i en virtuell dator. Om värden har ett problem, kan avbildningen med motorn synkroniseringsserver migreras till en annan server.

### <a name="sql-high-availability"></a>Hög tillgänglighet för SQL
Om du inte använder den SQL Server Express som medföljer Azure AD Connect, bör sedan hög tillgänglighet för SQL Server också övervägas. Lösningar för hög tillgänglighet som stöds är SQL-kluster och AOA (alltid på Tillgänglighetsgrupper). Stöds inte lösningar omfattar spegling.

Stöd för SQL AOA har lagts till Azure AD Connect i version 1.1.524.0. Du måste aktivera SQL AOA innan du installerar Azure AD Connect. Under installationen av identifierar Azure AD Connect om den angivna SQL-instansen är aktiverat för SQL AOA eller inte. Om SQL AOA är aktiverat räknat Azure AD Connect ytterligare ut om SQL AOA är konfigurerad för att använda synkron eller asynkron replikeringen. När du ställer in tillgänglighetsgruppens lyssnare, rekommenderas att du anger egenskapen RegisterAllProvidersIP till 0. Detta beror på att Azure AD Connect används för närvarande SQL Native Client för anslutning till SQL och SQL Native Client stöder inte användning av MultiSubNetFailover-egenskapen.

## <a name="appendix-csanalyzer"></a>Bilaga CSAnalyzer
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
$result=$reader = [System.Xml.XmlReader]::Create($resolvedXMLtoimport) 
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

        #export the collection of users as as CSV
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

* [Azure AD Connect-synkronisering: Förstå och anpassa synkronisering](active-directory-aadconnectsync-whatis.md)  
* [Integrera dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md)  
