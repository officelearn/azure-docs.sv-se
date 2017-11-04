---
title: Kopplingen versionshistorik | Microsoft Docs
description: "Det här avsnittet listar alla versioner av kopplingar för Forefront Identity Manager (FIM) och Microsoft Identity Manager (MIM)"
services: active-directory
documentationcenter: 
author: fimguy
manager: femila
editor: 
ms.assetid: 6a0c66ab-55df-4669-a0c7-1fe1a091a7f9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/06/2017
ms.author: fimguy
ms.openlocfilehash: e6df124a38c748294e92183df272dc266a0afc51
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2017
---
# <a name="connector-version-release-history"></a>Versionshistorik för anslutningsappen
Kopplingar för Forefront Identity Manager (FIM) och Microsoft Identity Manager (MIM) uppdateras ofta.

> [!NOTE]
> Det här avsnittet finns bara på FIM och MIM. Följande kopplingar stöds inte för installation på Azure AD Connect. Utgiven kopplingar är förinstallerade på AADConnect när du uppgraderar till angivna Build.


Det här avsnittet listas alla versioner av kopplingar som har släppts.

Relaterade länkar:

* [Hämta senaste kopplingar](http://go.microsoft.com/fwlink/?LinkId=717495)
* [Allmän LDAP Connector](active-directory-aadconnectsync-connector-genericldap.md) refererar dokumentationen
* [Allmän SQL Connector](active-directory-aadconnectsync-connector-genericsql.md) refererar dokumentationen
* [Web Services Connector](http://go.microsoft.com/fwlink/?LinkID=226245) refererar dokumentationen
* [PowerShell Connector](active-directory-aadconnectsync-connector-powershell.md) refererar dokumentationen
* [Kopplingen för Lotus Domino](active-directory-aadconnectsync-connector-domino.md) refererar dokumentationen

## <a name="116490-aadconnect-116490"></a>1.1.649.0 (AADConnect 1.1.649.0)

### <a name="fixed-issues"></a>Fast problem:

* Lotus Notes:
  * Anpassade certifiers filtreringsalternativ
  * Import av klassen ImportOperations åtgärdades definitionen av vilka åtgärder kan köras i läget 'Vyer' och som i ”Sök”-läge.
* Allmän LDAP:
  * OpenLDAP Directory använder DN som ankare i stället för entryUUI. Nya alternativet att GLDAP koppling som gör det möjligt för att ändra ankare
* Allmän SQL:
  * Fast export till fältet varbinary(max)-typ som har.
  * När du lägger till binära data från en datakälla till CSEntry objekt i DataTypeConversion funktionen misslyckades på noll byte. Klassen CSEntryOperationBase fast DataTypeConversion funktion.




### <a name="enhancements"></a>Förbättringar av:

* Allmän SQL:
  * Möjligheten att konfigurera läge för att utföra åtgärden lagrad procedur med namngivna parametrar eller namngivna inte har lagts till i ett fönster för konfiguration av allmänna SQL hanteringsagenten på sidan-globala parametrar'. På sidan-globala parametrar, det är kryssrutan med etiketten ”Använd namngivna parametrar att köra en lagrad procedur' som ansvarar för läge för execute lagrad procedur med namngivna parametrar eller inte.
    * Möjligheten att köra den lagrade proceduren med namngivna parametrar fungerar för närvarande endast för databaser IBM DB2 och MSSQL. Den här metoden fungerar inte för Oracle och MySQL-databaser: 
      * SQL-syntax för MySQL stöder inte namngivna parametrar i lagrade procedurer.
      * ODBC-drivrutin för Oracle stöder inte namngivna parametrar för namngivna parametrar i lagrade procedurer)

## <a name="116040-aadconnect-116140"></a>1.1.604.0 (AADConnect 1.1.614.0)


### <a name="fixed-issues"></a>Fast problem:

* Allmän Web Services:
  * Ett problem som förhindrar att ett SOAP-projekt som skapas när det finns två eller fler slutpunkter har åtgärdats.
* Allmän SQL:
  * I åtgärden för import av GSQL inte konvertering av tid korrekt när sparas till anslutningsplatsen. Standardformatet för datum och tid för anslutarplats för GSQL har ändrats från ”åååå-MM-dd: ssZ' till 'yyyy-MM-dd: ssZ'.

## <a name="115510-aadconnect-115530"></a>1.1.551.0 (AADConnect 1.1.553.0)

### <a name="fixed-issues"></a>Fast problem:

* Allmän Web Services:
  * Verktyget Wsconfig inte att konvertera korrekt Json-matris från ”exempelbegäran” för metoden REST-tjänsten. Detta orsakade problem med serialisering denna Json-matris för REST-begäran.
  * Web Service Connector Configuration Tool stöder inte användning av diskutrymme symboler i JSON-attributnamn 
    * Ett mönster för ersättning kan läggas till manuellt i filen WSConfigTool.exe.config t.ex.```<appSettings> <add key=”JSONSpaceNamePattern” value="__" /> </appSettings>```

* Lotus Notes:
  * När alternativet **Tillåt anpassade certifiers för enheter i organisationen/organiserad** inaktiveras misslyckas anslutningen under exporten (uppdatering) efter exporten flödet alla attribut som exporteras till Domino men vid tidpunkten för exporten en KeyNotFoundException returneras ska synkroniseras. 
    * Detta beror på att namnbytet misslyckas när den försöker ändra DN (användarnamn attribut) genom att ändra ett av attributen nedan:  
      - Efternamn
      - Förnamn
      - MiddleInitial
      - AltFullName
      - AltFullNameLanguage
      - organisationsenhet
      - altcommonname

  * När **Tillåt anpassade certifiers för enheter i organisationen/organiserad** alternativ är aktiverat, men krävs certifiers är fortfarande tom sedan KeyNotFoundException inträffar.

### <a name="enhancements"></a>Förbättringar av:

* Allmän SQL:
  * **Scenario: gjorts implementerat:** ”*” funktionen
  * **Lösningsbeskrivning av:** ändras tillvägagångssätt för [med flera värden referens attribut hantering](active-directory-aadconnectsync-connector-genericsql.md).


### <a name="fixed-issues"></a>Fast problem:

* Allmän Web Services:
  * Kan inte importera serverkonfigurationen om WebService koppling finns
  * WebService-anslutningen fungerar inte med flera webbtjänster

* Allmän SQL:
  * Inga objekt av typen listas för enstaka värde refererade attribut
  * Deltaimport för ändringsspårning strategi tar bort objekt när värdet tas bort från Flervärde tabell
  * OverflowException i GSQL connector med DB2 på AS / 400

Lotus:
  * Tillagda möjlighet att enable\disable söker organisationsenheter innan du öppnar GlobalParameters sidan

## <a name="114430"></a>1.1.443.0

Utgiven: 2017 mars

### <a name="enhancements"></a>Förbättringar

* Allmän SQL:</br>
  **Scenariot Symptom:** det är en välkänd begränsning med SQL-anslutningen där vi endast tillåta en referens till en objekttyp och kräver korsreferens med medlemmar. </br>
  **Lösningsbeskrivning av:** bearbetningssteg för referenser fanns ”*” har valts, alla kombinationer av objekt av typen returneras till Synkroniseringsmotorn.

>[!Important]
- Detta skapar många platshållare
- Det krävs för att försäkra att namnge är unika mellan objekttyper.


* Allmän LDAP:</br>
 **Scenario:** när endast några behållare har markerats i en specifik partition, sedan sökningen fortfarande görs i hela partition. Specifika filtreras som synkroniseringstjänsten, men inte av MA vilket kan leda till försämrade prestanda. </br>

 **Lösningsbeskrivning av:** ändras GLDAP connector koden och gör det möjligt gå igenom alla behållare som du kan söka efter objekt i var och en av dem, i stället för att söka i hela partitionen.


* Lotus Domino:

  **Scenario:** Domino mail borttagning stöd för en person tas bort vid en export. </br>
  **Lösning:** konfigurerbara e borttagning stöd för en person tas bort vid en export.

### <a name="fixed-issues"></a>Fast problem:
* Allmän Web Services:
 * När du ändrar tjänstens Webbadress i SAP wsconfig projekt via webbtjänsten konfigurationsverktyget sedan händer följande fel: Det gick inte att hitta en del av sökvägen

      ``'C:\Users\cstpopovaz\AppData\Local\Temp\2\e2c9d9b0-0d8a-4409-b059-dceeb900a2b3\b9bedcc0-88ac-454c-8c69-7d6ea1c41d17\cfg.config\cloneconfig.xml'. ``

* Allmän LDAP:
 * GLDAP Connector kan inte se alla attribut i AD LDS
 * Guiden radbrytningar när inga UPN-attribut identifieras från LDAP-directory-schemat
 * Delta importen misslyckas med identifiering av fel som inte finns vid fullständig import när attributet ”objectclass” inte är markerat
 * En ”konfigurera partitioner och hierarkier” konfigurationssidan visar inte alla objekt som är vilken typ som motsvarar partitionen för nya servrar i generiska  
LDAP-MA. De visade endast objekt från RootDSE partition.


* Allmän SQL:
 * Korrigering för allmän SQL vattenstämpel Deltaimport flervärdesattribut inte importera programfel
 * När du exporterar deleted\added värdena för flervärdesattribut, men de är inte deleted\added i datakällan.  


* Lotus Notes:
 * Ett visst fält ”fullständigt namn” visas i metaversum korrekt men när du exporterar till anteckningar värdet för attributet är Null eller tom.
 * Korrigering för Certifier dubblettfel
 * När objekt utan data är markerat för Lotus Domino kopplingen med andra objekt sedan får vi identifiering felmeddelandet när du utför en fullständig Import.
 * När Deltaimport körs på Lotus Domino-anslutningen i slutet av den kör Microsoft.IdentityManagement.MA.LotusDomino.Service.exe tjänsten ibland returnerar ett programfel.
 * Gruppen medlemskap övergripande fungerar bra och underhålls, förutom när du kör export att ta bort en användare från medlemskap det visas som slutförd med en uppdatering, men användaren inte komma bort från medlemskap i Lotus Notes.
 * Möjlighet att välja läge för export som ”Lägg till objektet längst ned” har lagts till i konfigurationen GUI för Lotus MA att lägga till nya objekt längst ned under exporten för attribut med flera värden.
 * Anslutningen läggs nödvändiga logik för att ta bort filen i mappen för e-post och ID-valvet.
 * Ta bort medlemskap som inte fungerar för mellan NAB medlem.
 * Värden ska vara har tagits bort från flervärdesattribut

## <a name="111170"></a>1.1.117.0
Utgiven: 2016 mars

**Ny koppling**  
Inledande versionen av den [allmänna SQL Connector](active-directory-aadconnectsync-connector-genericsql.md).

**Nya funktioner:**

* Allmän LDAP Connector:
  * Stöd för Deltaimport med Isode har lagts till.
* Web Services Connector:
  * Uppdatera den csEntryChangeResult och setImportErrorCode aktiviteten så att objekt nivån fel som returneras till Synkroniseringsmotorn.
  * Uppdatera SAP6 och SAP6User mallar om du vill använda de nya funktionerna i objektet nivån fel.
* Lotus Domino-anslutningen:
  * Du måste en certifier per adressbok för exporten. Du kan nu använda samma lösenord för alla certifiers för att underlätta hanteringen.

**Fast problem:**

* Allmän LDAP Connector:
  * För IBM Tivoli DS identifierades vissa referensattribut på rätt sätt.
  * Mellanslag i början och slutet av strängar för öppna LDAP under en Deltaimport har trunkerats.
  * För Novell och NetIQ export som flytta ett objekt mellan organisationsenheter-behållare och samtidigt byta namn på objekt misslyckades.
* Web Services Connector:
  * Om webbtjänsten har flera slutpunkter för samma bindning, sedan anslutningen inte rätt att identifiera dessa slutpunkter.
* Lotus Domino-anslutningen:
  * Export av attributet fullName till en e-post i databasen fungerar inte.
  * En export som både läggas till och ta bort medlemmen från en grupp exporteras endast tillagda medlemmar.
  * Om ett Notes-dokument är ogiltig (det attributet isValid inställd på false) och sedan anslutningen misslyckas.

## <a name="older-releases"></a>Äldre versioner
Kopplingarna publicerades före mars 2016 som supportfrågor.

**Allmän LDAP**

* [KB3078617](https://support.microsoft.com/kb/3078617) -1.0.0597 September 2015
* [KB3044896](https://support.microsoft.com/kb/3044896) -1.0.0549, 2015 mars
* [KB3031009](https://support.microsoft.com/kb/3031009) -1.0.0534 januari 2015
* [KB3008177](https://support.microsoft.com/kb/3008177) -1.0.0419 September 2014
* [KB2936070](https://support.microsoft.com/kb/2936070) -4.3.1082, 2014 mars

**WebServices**

* [KB3008178](https://support.microsoft.com/kb/3008178) -1.0.0419 September 2014

**PowerShell**

* [KB3008179](https://support.microsoft.com/kb/3008179) -1.0.0419 September 2014

**Lotus Domino**

* [KB3096533](https://support.microsoft.com/kb/3096533) -1.0.0597 September 2015
* [KB3044895](https://support.microsoft.com/kb/3044895) -1.0.0549, 2015 mars
* [KB2977286](https://support.microsoft.com/kb/2977286) -5.3.0712 2014 augusti
* [KB2932635](https://support.microsoft.com/kb/2932635) -5.3.1003 februari 2014  
* [KB2899874](https://support.microsoft.com/kb/2899874) -5.3.0721, oktober 2013
* [KB2875551](https://support.microsoft.com/kb/2875551) -5.3.0534 2013 augusti

## <a name="troubleshooting"></a>Felsökning 

> [!NOTE]
> När du uppdaterar Microsoft Identity Manager eller AADConnect med användningen av ECMA2 kopplingar. 

Du måste uppdatera connector-definitionen om du uppgraderar till matchar eller du får följande fel i händelseloggen programstart att rapportera varning 6947-ID: ”Sammansättningsversionen i AAD-kopplingskonfiguration (” X.X.XXX. X ”) är tidigare än den faktiska versionen (” X.X.XXX. X ”) för” C:\Program Files\Microsoft Azure AD Sync\Extensions\Microsoft.IAM.Connector.GenericLdap.dll ”.

Uppdatera definitionen:
* Öppna Egenskaper för kopplingsinstansen
* Klicka på anslutningen / ansluta till fliken
  * Ange lösenordet för kontot Connector
* Klicka på var och en av flikarna egenskapen i sin tur
  * Om den här typen av koppling har en flik för partitioner, med en uppdateringsknappen klickar du på uppdateringsknappen på fliken
* När alla egendom flikar har använts, klicka på OK om du vill spara ändringarna.


## <a name="next-steps"></a>Nästa steg
Lär dig mer om den [Azure AD Connect-synkronisering](active-directory-aadconnectsync-whatis.md) konfiguration.

Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md).
