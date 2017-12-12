---
title: PowerShell Connector | Microsoft Docs
description: "Den här artikeln beskriver hur du konfigurerar Microsoft Windows PowerShell Connector."
services: active-directory
documentationcenter: 
author: AndKjell
manager: mtillman
editor: 
ms.assetid: 6dba8e34-a874-4ff0-90bc-bd2b0a4199b5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 27ca89a2032c82a8be909349b38a64fc6aa9579e
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="windows-powershell-connector-technical-reference"></a>Teknisk referens för Windows PowerShell Connector
Den här artikeln beskriver Windows PowerShell Connector. Artikeln gäller för följande produkter:

* Microsoft Identity Manager 2016 (MIM2016)
* Forefront Identity Manager 2010 R2 (FIM2010R2)
  * Måste använda snabbkorrigering 4.1.3671.0 eller senare [KB3092178](https://support.microsoft.com/kb/3092178).

För MIM2016 och FIM2010R2 kopplingen är tillgänglig för hämtning från den [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=717495).

## <a name="overview-of-the-powershell-connector"></a>Översikt över PowerShell Connector
PowerShell Connector kan du integrera synkroniseringstjänsten med externa system som tillhandahåller Windows PowerShell-baserade API: er. Anslutningen ger en brygga mellan funktionerna i hanteringsagenten anropsbaserade extensible connectivity 2 (ECMA2) framework och Windows PowerShell. Mer information om ECMA framework finns på [Extensible Connectivity 2.2 Management Agent referens](https://msdn.microsoft.com/library/windows/desktop/hh859557.aspx).

### <a name="prerequisites"></a>Krav
Innan du använder anslutningen kan du kontrollera att du har följande på synkroniseringsservern:

* 4.5.2 för Microsoft .NET Framework eller senare
* Windows PowerShell 2.0, 3.0 eller 4.0

Körningsprincipen på servern synkroniseringstjänsten måste konfigureras för att tillåta kopplingen för att köra Windows PowerShell-skript. Om de skript som körs för anslutningen är digitalt signerade måste konfigurera körningsprincipen genom att köra det här kommandot:  
`Set-ExecutionPolicy -ExecutionPolicy RemoteSigned`

## <a name="create-a-new-connector"></a>Skapa en ny koppling
Du måste ange en serie med Windows PowerShell-skript som kör de steg som begärs av synkroniseringstjänsten för att skapa en Windows PowerShell-koppling i synkroniseringstjänsten. Skripten måste du implementera varierar beroende på den datakälla som du ansluter till och vilka funktioner du behöver. Det här avsnittet ger en översikt över skript som kan implementeras och när de är obligatoriska.

Windows PowerShell-kopplingen är utformad för att lagra alla skript i databasen för synkroniseringstjänsten. Det är möjligt att köra skript som är lagrade i filsystemet, är det lättare att infoga varje skript direkt i dess konfiguration.

Skapa en PowerShell-koppling i **synkroniseringstjänsten** Välj **hanteringsagenten** och **skapa**. Välj den **PowerShell (Microsoft)** koppling.

![Skapa koppling](./media/active-directory-aadconnectsync-connector-powershell/createconnector.png)

### <a name="connectivity"></a>Anslutning
Ange konfigurationsparametrar för att ansluta till ett fjärrsystem. Värdena lagras av synkroniseringstjänsten och få tillgång till Windows PowerShell-skript när kopplingen körs på ett säkert sätt.

![Anslutning](./media/active-directory-aadconnectsync-connector-powershell/connectivity.png)

Du kan konfigurera följande parametrar för anslutningen:

**Anslutning**

| Parameter | Standardvärde | Syfte |
| --- | --- | --- |
| Server |<Blank> |Namn på server som anslutningen ska ansluta till. |
| Domän |<Blank> |Domän för autentiseringsuppgifterna för att lagra för användning när kopplingen körs. |
| Användare |<Blank> |Användarnamnet för autentiseringsuppgifterna för att lagra för användning när kopplingen körs. |
| Lösenord |<Blank> |Lösenordet för autentiseringsuppgifterna för att lagra för användning när kopplingen körs. |
| Personifiera Connector-kontot |False |Om värdet är true körs synkroniseringstjänsten Windows PowerShell-skript i samband med autentiseringsuppgifterna. När det är möjligt bör den **$Credentials** -parameter har skickats till varje skript används i stället för personifiering. Mer information om ytterligare behörigheter som krävs för att använda det här alternativet finns [ytterligare konfiguration för personifiering](#additional-configuration-for-impersonation). |
| Läs in användarprofil vid personifiering |False |Anger att Windows att läsa in användarprofilen kopplingens autentiseringsuppgifter under personifieringen. Om den personifierade användaren har en central användarprofil, laddar kopplingen inte den centrala profilen. Mer information om ytterligare behörigheter som krävs för att använda den här parametern finns [ytterligare konfiguration för personifiering](#additional-configuration-for-impersonation). |
| Inloggningstyp vid personifiering |Ingen |Inloggningstyp under personifieringen. Mer information finns i [dwLogonType] [ dw] dokumentation. |
| Signerade skript |False |Om värdet är true, verifierar Windows PowerShell-koppling att varje skript har en giltig digital signatur. Om värdet är false, se till att servern synkroniseringstjänst för Windows PowerShell-körningsprincipen RemoteSigned eller obegränsat. |

**Allmänna modulen**  
Anslutningen kan du lagra en delad Windows PowerShell-modul i konfigurationen. När kopplingen körs ett skript, ska Windows PowerShell-modulen extraheras till filsystemet så att den kan importeras till varje skript.

Vanliga modulen extraheras till kopplingens MAData mapp för Import, Export och synkronisering av lösenords-skript. Vanliga modulen extraheras till mappen % TEMP % för schemat, verifiering, hierarkin och Partition skript för identifiering. I båda fallen skriptet extraherade allmänna modulen med namnet enligt inställningen vanliga Modulnamn skript.

Om du vill läsa in en modul som kallas FIMPowerShellConnectorModule.psm1 från mappen MAData, använder du följande instruktion:`Import-Module (Join-Path -Path [Microsoft.MetadirectoryServices.MAUtils]::MAFolder -ChildPath "FIMPowerShellConnectorModule.psm1")`

Om du vill läsa in en anropas FIMPowerShellConnectorModule.psm1 från mappen % TEMP %-modulen, använder du följande instruktion:`Import-Module (Join-Path -Path $env:TEMP -ChildPath "FIMPowerShellConnectorModule.psm1")`

**Verifieringen av parametern**  
Verifieringsskriptet är en valfri Windows PowerShell-skript som kan användas för att kontrollera att connector konfigurationsparametrar som tillhandahålls av administratören är giltiga. Validera server, autentiseringsuppgifter för anslutning och anslutningen parametrar är vanliga användningsområden för verifieringsskriptet. Verifieringsskriptet anropas efter följande flikar och dialogrutor ändras:

* Anslutning
* Globala parametrar
* Partitionen konfiguration

Verifieringsskriptet får följande parametrar från anslutningen:

| Namn | Datatyp | Beskrivning |
| --- | --- | --- |
| ConfigParameterPage |[ConfigParameterPage][cpp] |Konfigurationsfliken eller dialogruta som utlöste validering begäran. |
| ConfigParameters |[KeyedCollection] [ keyk] [sträng, [ConfigParameter][cp]] |Tabell konfigurationsparametrar för anslutningen. |
| Autentiseringsuppgift |[PSCredential][pscred] |Innehåller alla autentiseringsuppgifter som angetts av administratören på fliken anslutning. |

Verifieringsskriptet ska returnera ett ParameterValidationResult objekt till transportledningen.

**Identifiering av schemat**  
Schemat identifieringsskriptet är obligatoriskt. Det här skriptet returnerar objekttyper, attribut och attributet begränsningar som synkroniseringstjänsten använder när du konfigurerar regler för flödet av attribut. Schemat identifieringsskriptet körs under anslutning skapas och fylls kopplingens schemat. Det används också av åtgärden Uppdatera Schema i hanteraren för synkroniseringstjänsten.

Identifieringsskriptet schemat får följande parametrar från anslutningen:

| Namn | Datatyp | Beskrivning |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection] [ keyk] [sträng, [ConfigParameter][cp]] |Tabell konfigurationsparametrar för anslutningen. |
| Autentiseringsuppgift |[PSCredential][pscred] |Innehåller alla autentiseringsuppgifter som angetts av administratören på fliken anslutning. |

Skriptet måste returnera en enda [schemat] [ schema] till transportledningen. Schema-objekt består av [SchemaType] [ schemaT] objekt som representerar objekttyper (till exempel: användare och grupper). Objektet SchemaType innehåller en samling [SchemaAttribute] [ schemaA] objekt som representerar attribut (till exempel: Förnamn, efternamn och postadress) av typen.

**Ytterligare parametrar**  
Utöver standard konfigurationsinställningarna kan du definiera ytterligare anpassade inställningar som är specifika för instansen av kopplingen. Dessa parametrar kan anges i kopplingen partition, eller kör steg nivåer och nås från det aktuella Windows PowerShell-skriptet. Anpassade konfigurationsinställningar kan lagras i databasen synkroniseringstjänsten oformaterad text eller kan vara krypterat. Synkroniseringstjänsten krypterar automatiskt och dekrypterar säker konfigurationsinställningar vid behov.

Om du vill ange anpassade konfigurationsinställningar avgränsa namnet på varje parameter med kommatecken (,).

För att komma åt anpassade konfigurationsinställningar från ett skript som du suffix namn med ett understreck ( \_ ) och omfång för parametern (Global, Partition eller RunStep). Till exempel använda det här kodstycket för att komma åt globala FileName-parameter:`$ConfigurationParameters["FileName_Global"].Value`

### <a name="capabilities"></a>Funktioner
Fliken funktioner i Management Agent Designer definierar beteendet och funktionerna i kopplingen. De val du gjorde på den här fliken kan inte ändras när anslutningen har skapats. Den här tabellen innehåller kapacitetsinställningarna.

![Funktioner](./media/active-directory-aadconnectsync-connector-powershell/capabilities.png)

| Funktion | Beskrivning |
| --- | --- |
| [Huvudnamnet format][dnstyle] |Anger om anslutningen stöder unika namn och därför vad format. |
| [Exportera typ][exportT] |Anger vilken typ av objekt som visas för Export-skriptet. <li>AttributeReplace – innehåller en fullständig uppsättning värden för ett flervärdesattribut när attributet ändras.</li><li>AttributeUpdate – innehåller endast går att ett flervärdesattribut när attributet ändras.</li><li>MultivaluedReferenceAttributeUpdate - innehåller en fullständig uppsättning värden för icke-referens med flera värden attribut och endast går för flera värden referensattribut.</li><li>ObjectReplace – innehåller alla attribut för ett objekt när någon attributändringar</li> |
| [Databasnormalisering][DataNorm] |Instruerar synkroniseringstjänsten normalisera fästpunkt attribut innan de har angetts för skript. |
| [Bekräftelse av objekt][oconf] |Konfigurerar väntande import i synkroniseringstjänsten. <li>Normal – standardbeteendet som förväntar sig alla exporterade ändringar bekräftas via import</li><li>NoDeleteConfirmation – finns när ett objekt tas bort, det inga väntande import genereras.</li><li>NoAddAndDeleteConfirmation – finns när ett objekt skapas eller tas bort, det inga väntande import genereras.</li> |
| Använda DN som ankare |Om det unika namnet format är LDAP är också fästpunktsattributet för anslutningsplatsen unika namn. |
| Samtidiga åtgärder av flera kopplingar |När alternativet är markerat kan flera Windows PowerShell-kopplingar köras samtidigt. |
| Partitioner |När alternativet är markerat, stöder anslutningen flera partitioner och identifiering av partition. |
| Hierarki |När alternativet är markerat, stöder anslutningen en hierarkisk struktur för LDAP-format. |
| Aktivera Import |När alternativet är markerat importerar kopplingen data via skript för import. |
| Aktivera Deltaimport |När alternativet är markerat kan kopplingen begära går från skript för import. |
| Aktivera Export |När alternativet är markerat exporterar kopplingen data via export skript. |
| Aktivera fullständig Export |När alternativet är markerat stöder export skript export hela anslutningsplatsen. Om du vill använda det här alternativet måste aktivera exportera vara markerad. |
| Ingen referensvärden i första Export skickas |När alternativet är markerat exporteras referensattribut i ett andra steg i exporten. |
| Aktivera objektet Byt namn |När alternativet är markerat kan du ändra unika namn. |
| Ta bort-lägga till som ersätter |När alternativet är markerat, delete-Lägg till åtgärder som exporteras som en enda ersättning. |
| Aktivera Lösenordsåtgärder |När alternativet är markerat stöds skript för synkronisering av lösenord. |
| Aktivera Export av lösenord i första steget |När alternativet är markerat exporteras lösenord under etablering när objektet skapas. |

### <a name="global-parameters"></a>Globala parametrar
Fliken globala parametrar i Management Agent Designer kan du konfigurera Windows PowerShell-skript som körs av kopplingen. Du kan också konfigurera globala värden för anpassade konfigurationsinställningar som definierats på fliken anslutning.

**Identifiering av partition**  
En partition är en separat namnområde inom ett delat schema. Till exempel är alla domäner i Active Directory en partition inom en skog. En partition är en logisk gruppering för import och export åtgärder. Importera och exportera ha partition som en kontext och alla åtgärder som sker i den här kontexten. Partitioner som ska representera en hierarki i LDAP. Det unika namnet för en partition används i import för att kontrollera att alla objekt som returnerades är inom omfånget för en partition. Det unika namnet för partitionen används också under etablering från metaversumsökning till anslutningsplatsen för att fastställa den partition som ett objekt ska associeras med under exporten.

Identifieringsskriptet partitionen tar emot följande parametrar från anslutningen:

| Namn | Datatyp | Beskrivning |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk][sträng, [ConfigParameter][cp]] |Tabell konfigurationsparametrar för anslutningen. |
| Autentiseringsuppgift |[PSCredential][pscred] |Innehåller alla autentiseringsuppgifter som angetts av administratören på fliken anslutning. |

Skriptet måste returnera en antingen en enskild [Partition] [ part] objekt eller en lista [T] med partitionsobjekt till transportledningen.

**Hierarkiidentifiering**  
Identifieringsskriptet hierarkin används bara när den unika namn Style-kapaciteten är LDAP. Skriptet används kan du bläddra och välj en uppsättning behållare som anses i eller utanför omfånget för import och export åtgärder. Skriptet ska endast ange en lista med noder som är direkt underordnade för rotnoden som angetts för skriptet.

Identifieringsskript hierarki tar emot följande parametrar från anslutningen:

| Namn | Datatyp | Beskrivning |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk][sträng, [ConfigParameter][cp]] |Tabell konfigurationsparametrar för anslutningen. |
| Autentiseringsuppgift |[PSCredential][pscred] |Innehåller alla autentiseringsuppgifter som angetts av administratören på fliken anslutning. |
| ParentNode |[HierarchyNode][hn] |Rotnoden i hierarkin som skriptet ska returnera direkt underordnade objekt. |

Skriptet måste returnera en antingen ett enda underordnat HierarchyNode objekt eller en lista [T] underordnade HierarchyNode objekt till transportledningen.

#### <a name="import"></a>Importera
Kopplingar som har stöd för importåtgärder måste implementera tre skript.

**Påbörja Import**  
Börja importera skript körs i början av en import kör steget. Under det här steget kan du upprätta en anslutning till källsystemet och göra förberedande steg innan du importerar data från det anslutna systemet.

Börja importera skript får följande parametrar från anslutningen:

| Namn | Datatyp | Beskrivning |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk][sträng, [ConfigParameter][cp]] |Tabell konfigurationsparametrar för anslutningen. |
| Autentiseringsuppgift |[PSCredential][pscred] |Innehåller alla autentiseringsuppgifter som angetts av administratören på fliken anslutning. |
| OpenImportConnectionRunStep |[OpenImportConnectionRunStep][oicrs] |Informerar skriptet om vilken typ av import kör (delta eller fullständig) partition, hierarkin, vattenstämpel och förväntade storlek. |
| Typer |[Schemat][schema] |Schemat för anslutningsplatsen som importeras. |

Skriptet måste returnera en enda [OpenImportConnectionResults] [ oicres] objekt-pipelinen, till exempel:`Write-Output (New-Object Microsoft.MetadirectoryServices.OpenImportConnectionResults)`

**Importera Data**  
Importera Dataskript kallas av anslutningen förrän skriptet anger att det finns inga fler data som ska importeras. Windows PowerShell-koppling har en sidstorlek 9 999 objekt. Om skriptet returnerar mer än 9 999 objekt som ska importeras, du måste ha stöd för sidindelning. Koppling visar som kallas för en egenskap för anpassade data som du kan använda en butik en vattenstämpel så att varje gång data importskriptet, skriptet fortsätter med att importera objekt där den avbröts.

Importera Dataskript får följande parametrar från anslutningen:

| Namn | Datatyp | Beskrivning |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk][sträng, [ConfigParameter][cp]] |Tabell konfigurationsparametrar för anslutningen. |
| Autentiseringsuppgift |[PSCredential][pscred] |Innehåller alla autentiseringsuppgifter som angetts av administratören på fliken anslutning. |
| GetImportEntriesRunStep |[ImportRunStep][irs] |Innehåller vattenstämpel (CustomData) som kan användas vid växlingsbart import och delta-import. |
| OpenImportConnectionRunStep |[OpenImportConnectionRunStep][oicrs] |Informerar skriptet om vilken typ av import kör (delta eller fullständig) partition, hierarkin, vattenstämpel och förväntade storlek. |
| Typer |[Schemat][schema] |Schemat för anslutningsplatsen som importeras. |

Importera Dataskript måste skriva en lista [[CSEntryChange][csec]] till transportledningen. Den här samlingen består av CSEntryChange attribut som representerar alla objekt som importeras. Den här samlingen bör ha en fullständig uppsättning CSEntryChange objekt som har alla attribut för varje objekt under en fullständig Import-körning. Under en Delta importerar kan innehålla antingen CSEntryChange objektet ska nivån går attribut för varje objekt att importera eller en fullständig representation av objekt som har ändrats (Ersättningsläge).

**End-Import**  
När importen kör, körs importera End-skript. Det här skriptet ska utföra någon rensning nödvändiga åtgärder (till exempel Stäng anslutningar till system) och svara på fel.

Importskriptet slut tar emot följande parametrar från anslutningen:

| Namn | Datatyp | Beskrivning |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk][sträng, [ConfigParameter][cp]] |Tabell konfigurationsparametrar för anslutningen. |
| Autentiseringsuppgift |[PSCredential][pscred] |Innehåller alla autentiseringsuppgifter som angetts av administratören på fliken anslutning. |
| OpenImportConnectionRunStep |[OpenImportConnectionRunStep][oicrs] |Informerar skriptet om vilken typ av import kör (delta eller fullständig) partition, hierarkin, vattenstämpel och förväntade storlek. |
| CloseImportConnectionRunStep |[CloseImportConnectionRunStep][cecrs] |Informerar skriptet om orsaken till att importera avslutades. |

Skriptet måste returnera en enda [CloseImportConnectionResults] [ cicres] objekt-pipelinen, till exempel:`Write-Output (New-Object Microsoft.MetadirectoryServices.CloseImportConnectionResults)`

#### <a name="export"></a>Exportera
Identiska med import-arkitekturen för anslutningen, kopplingar som har stöd för Export måste implementera tre skript.

**Börja exportera**  
Börja exportera skript körs i början av ett export kör steg. Det här steget kan du upprätta en anslutning till källsystemet och genomföra alla förberedande steg innan du exporterar data till det anslutna systemet.

Börja exportera skriptet tar emot följande parametrar från anslutningen:

| Namn | Datatyp | Beskrivning |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk][sträng, [ConfigParameter][cp]] |Tabell konfigurationsparametrar för anslutningen. |
| Autentiseringsuppgift |[PSCredential][pscred] |Innehåller alla autentiseringsuppgifter som angetts av administratören på fliken anslutning. |
| OpenExportConnectionRunStep |[OpenExportConnectionRunStep][oecrs] |Informerar skriptet om vilken typ av export kör (delta eller fullständig) partition, hierarkin och förväntade sidstorlek. |
| Typer |[Schemat][schema] |Schemat för anslutningsplatsen som exporteras. |

Skriptet ska inte returnerar några utdata till transportledningen.

**Exportera Data**  
Synkroniseringstjänsten anropar skriptet exportera Data så många gånger som krävs för att bearbeta alla väntande exporter. Om anslutningsplatsen har flera väntande exporter än kopplingens sidstorleken, kan exportera data skriptet anropas flera gånger och eventuellt flera gånger för samma objekt.

Exportera data skriptet tar emot följande parametrar från anslutningen:

| Namn | Datatyp | Beskrivning |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk][sträng, [ConfigParameter][cp]] |Tabell konfigurationsparametrar för anslutningen. |
| Autentiseringsuppgift |[PSCredential][pscred] |Innehåller alla autentiseringsuppgifter som angetts av administratören på fliken anslutning. |
| CSEntries |IList[CSEntryChange][csec] |Lista över alla anslutningsplatsen objekt med väntande exporter bearbetas under det här steget. |
| OpenExportConnectionRunStep |[OpenExportConnectionRunStep][oecrs] |Informerar skriptet om vilken typ av export kör (delta eller fullständig) partition, hierarkin och förväntade sidstorlek. |
| Typer |[Schemat][schema] |Schemat för anslutningsplatsen som exporteras. |

Exportera data skriptet måste returnera en [PutExportEntriesResults] [ peeres] till transportledningen. Det här objektet behöver inte inkludera resultat information för varje exporterad koppling såvida inte inträffar ett fel eller en ändring av fästpunktsattributet. Till exempel för att returnera ett PutExportEntriesResults-objekt till pipelinen:`Write-Output (New-Object Microsoft.MetadirectoryServices.PutExportEntriesResults)`

**End-Export**  
När exporten kör slutet exporterar skriptet ska köras. Det här skriptet ska utföra någon rensning nödvändiga åtgärder (till exempel Stäng anslutningar till system) och svara på fel.

Exportskriptet slut tar emot följande parametrar från anslutningen:

| Namn | Datatyp | Beskrivning |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk][sträng, [ConfigParameter][cp]] |Tabell konfigurationsparametrar för anslutningen. |
| Autentiseringsuppgift |[PSCredential][pscred] |Innehåller alla autentiseringsuppgifter som angetts av administratören på fliken anslutning. |
| OpenExportConnectionRunStep |[OpenExportConnectionRunStep][oecrs] |Informerar skriptet om vilken typ av export kör (delta eller fullständig) partition, hierarkin och förväntade sidstorlek. |
| CloseExportConnectionRunStep |[CloseExportConnectionRunStep][cecrs] |Informerar skriptet om orsaken till exporten avslutades. |

Skriptet ska inte returnerar några utdata till transportledningen.

#### <a name="password-synchronization"></a>Lösenordssynkronisering
Windows PowerShell-kopplingar kan användas som mål för ändringar/lösenordsåterställning.

Skriptet lösenord får följande parametrar från anslutningen:

| Namn | Datatyp | Beskrivning |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk][sträng, [ConfigParameter][cp]] |Tabell konfigurationsparametrar för anslutningen. |
| Autentiseringsuppgift |[PSCredential][pscred] |Innehåller alla autentiseringsuppgifter som angetts av administratören på fliken anslutning. |
| Partition |[Partition][part] |Katalogpartition som CSEntry finns i. |
| CSEntry |[CSEntry][cse] |Kopplingen utrymme post för objektet som är tog emot en lösenordsändring eller återställning. |
| Åtgärdstyp |Sträng |Anger om åtgärden har en återställning (**SetPassword**) eller en ändring (**ChangePassword**). |
| PasswordOptions |[PasswordOptions][pwdopt] |Flaggor som anger det avsedda lösenordet återställa beteende. Den här parametern är endast tillgänglig om OperationType är **SetPassword**. |
| Gammalt lösenord |Sträng |Fylls i med objektets gamla lösenord för ändring av lösenord. Den här parametern är endast tillgänglig om OperationType är **ChangePassword**. |
| Nytt lösenord |Sträng |Fylls i med objektets nytt lösenord som skriptet ska ange. |

Skriptet lösenord förväntas inte att returnera alla resultat för Windows PowerShell-pipeline. Om ett fel uppstår i skriptet med lösenord, utlösa skriptet en av följande undantag för att informera synkroniseringstjänsten om problemet:

* [PasswordPolicyViolationException] [ pwdex1] – genereras om lösenordet inte uppfyller lösenordsprincipen i det anslutna systemet.
* [PasswordIllFormedException] [ pwdex2] – genereras om lösenordet inte är godkänd för anslutna system.
* [PasswordExtension] [ pwdex3] – utlöstes andra fel i skriptet med lösenord.

## <a name="sample-connectors"></a>Exempel kopplingar
En fullständig översikt över tillgängliga exempel kopplingar, se [Connector för Windows PowerShell Connector provtagning][samp].

## <a name="other-notes"></a>Anmärkningar
### <a name="additional-configuration-for-impersonation"></a>Ytterligare konfiguration för personifiering
Bevilja användaren som är personifierad följande behörigheter på synkroniseringstjänsten-server:

Läsbehörighet till följande registernycklar:

* HKEY_USERS\\[SynchronizationServiceServiceAccountSID] \Software\Microsoft\PowerShell
* HKEY_USERS\\[SynchronizationServiceServiceAccountSID] \Environment

Kör följande PowerShell-kommandon för att fastställa den säkerhetsidentifierare (SID) för kontot synkroniseringstjänsten:

```
$account = New-Object System.Security.Principal.NTAccount "<domain>\<username>"
$account.Translate([System.Security.Principal.SecurityIdentifier]).Value
```

Läsbehörighet till mapparna system följande fil:

* %ProgramFiles%\Microsoft forefront Identity Manager\2010\Synchronization Service\Extensions
* %ProgramFiles%\Microsoft forefront Identity Manager\2010\Synchronization Service\ExtensionsCache
* %ProgramFiles%\Microsoft forefront Identity Manager\2010\Synchronization Service\MaData\\{ConnectorName}

Ersätt namnet på Windows PowerShell connector för platshållaren {ConnectorName}.

## <a name="troubleshooting"></a>Felsökning
* Information om hur du aktiverar loggning för att felsöka anslutningen finns i [hur du aktiverar ETW-spårning för kopplingar](http://go.microsoft.com/fwlink/?LinkId=335731).

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[cpp]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.configparameterpage.aspx
[keyk]: https://msdn.microsoft.com/library/ms132438.aspx
[cp]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.configparameter.aspx
[pscred]: https://msdn.microsoft.com/library/system.management.automation.pscredential.aspx
[schema]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.schema.aspx
[schemaT]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.schematype.aspx
[schemaA]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.schemaattribute.aspx
[dnstyle]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.madistinguishednamestyle.aspx
[exportT]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.maexporttype.aspx
[DataNorm]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.manormalizations.aspx
[oconf]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.maobjectconfirmation.aspx
[dw]: https://msdn.microsoft.com/library/windows/desktop/aa378184.aspx
[part]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.partition.aspx
[hn]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.hierarchynode.aspx
[oicrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.openimportconnectionrunstep.aspx
[cecrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.closeexportconnectionrunstep.aspx
[oicres]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.openimportconnectionresults.aspx
[cecrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.closeexportconnectionrunstep.aspx
[cicres]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.closeimportconnectionresults.aspx
[oecrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.openexportconnectionrunstep.aspx
[irs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.importrunstep.aspx
[cse]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.csentry.aspx
[csec]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.csentrychange.aspx
[peeres]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.putexportentriesresults.aspx
[pwdopt]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordoptions.aspx
[pwdex1]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordpolicyviolationexception.aspx
[pwdex2]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordillformedexception.aspx
[pwdex3]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordextensionexception.aspx
[samp]: http://go.microsoft.com/fwlink/?LinkId=394291
