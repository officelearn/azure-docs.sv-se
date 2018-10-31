---
title: Versionshistorik för anslutningsappen | Microsoft Docs
description: Det här avsnittet listar alla versioner av kopplingar för Forefront Identity Manager (FIM) och Microsoft Identity Manager (MIM)
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 6a0c66ab-55df-4669-a0c7-1fe1a091a7f9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/22/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: cbafe7fcdf2ccd515dfec5554c93bec9c7f3537a
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50243171"
---
# <a name="connector-version-release-history"></a>Versionshistorik för anslutningsappen
Anslutningsappar för Forefront Identity Manager (FIM) och Microsoft Identity Manager (MIM) uppdateras kontinuerligt.

> [!NOTE]
> Det här avsnittet är bara på FIM och MIM. Dessa anslutningar stöds inte för installation i Azure AD Connect. Utgiven Anslutningsappar är förinstallerade på AADConnect när du uppgraderar till angivna Build.


Det här avsnittet listas alla versioner av kopplingar som har frisläppts.

Relaterade länkar:

* [Hämta senaste anslutningar](http://go.microsoft.com/fwlink/?LinkId=717495)
* [Allmän LDAP Connector](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericldap) referensdokumentation
* [Allmän SQL-Anslutningsapp](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericsql) referensdokumentation
* [Web Services Connector](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-ma-ws) referensdokumentation
* [PowerShell-Anslutningsapp](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-powershell) referensdokumentation
* [Lotus Domino-Anslutningsapp](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-domino) referensdokumentation


## <a name="118300"></a>1.1.830.0

### <a name="fixed-issues"></a>Åtgärdade problem:
* Löst ConnectorsLog System.Diagnostics.EventLogInternal.InternalWriteEvent(Message: A device attached to the system is not functioning)
* I den här versionen av kopplingar behöver du uppdatera bindningen omdirigering från 3.3.0.0-4.1.3.0 till 4.1.4.0 i miiserver.exe.config
* Allmän webbtjänster:
    * Löst giltig JSON-svar kunde inte sparas i verktyget serverkonfiguration
* Allmän SQL:
    * Export genererar alltid bara uppdateringsfråga för driften av tas bort. Har lagts till för att generera en fråga
    * SQL-frågan som hämtar objekt för driften av Deltaimport, om Delta-strategi är Change Tracking har åtgärdats. I den här implementeringen känd begränsning: Deltaimport med Change Tracking läge inte spåra ändringar i attribut med flera värden
    * Har lagts till möjligheten att generera en fråga för fall när det är nödvändigt att ta bort det sista värdet för flervärdesattribut och den här raden innehåller inte andra data förutom värdet som det är nödvändigt att ta bort.
    * Hantering av när System.ArgumentException implementeras utdataparametrar av SP 
    * Felaktig fråga för att göra driften av export till fält som har typen för varbinary (max)
    * Problem med parameterList variabeln initierades två gånger (i funktionerna ExportAttributes och GetQueryForMultiValue)


## <a name="116490-aadconnect-116490"></a>1.1.649.0 (AADConnect 1.1.649.0)

### <a name="fixed-issues"></a>Åtgärdade problem:

* Lotus Notes:
  * Anpassade certifiers filtreringsalternativ
  * Import av klassen ImportOperations fasta definitionen av vilka åtgärder kan köras i läget för ”Views” och som i ”Sök”-läge.
* Allmän LDAP:
  * OpenLDAP Directory använder DN som ankare i stället för entryUUI. Ny möjlighet att GLDAP anslutning som gör för att ändra ankare
* Allmän SQL:
  * Fast export till fält som har typen för varbinary (max).
  * När du lägger till binära data från en datakälla till CSEntry objekt The DataTypeConversion funktionen misslyckades på noll byte. Fast DataTypeConversion funktion i klassen CSEntryOperationBase.




### <a name="enhancements"></a>Förbättringar av:

* Allmän SQL:
  * Möjligheten att konfigurera läge för att utföra åtgärden lagrade proceduren med namngivna parametrar eller som inte heter läggs till i en konfigurationsfönstret för allmän SQL-hanteringsagenten på sidan ”globala Parameters”. På sidan ”globala Parameters” det är kryssrutan med etiketten ”Använd namngivna parametrarna att köra en lagrad procedur, som ansvarar för läge för Kör lagrad procedur med namngivna parametrarna eller inte.
    * Möjligheten att köra den lagrade proceduren med namngivna parametrar fungerar för närvarande endast för IBM DB2 och MSSQL-databaser. Den här metoden fungerar inte för Oracle och MySQL-databaser: 
      * SQL-syntax av MySQL stöder inte namngivna parametrar i lagrade procedurer.
      * ODBC-drivrutin för Oracle stöder inte namngivna parametrar för namngivna parametrar i lagrade procedurer)

## <a name="116040-aadconnect-116140"></a>1.1.604.0 (AADConnect 1.1.614.0)


### <a name="fixed-issues"></a>Åtgärdade problem:

* Allmän webbtjänster:
  * Ett problem som förhindrar att ett SOAP-projekt som skapas när det fanns två eller flera slutpunkter har åtgärdats.
* Allmän SQL:
  * I driften av import GSQL inte konvertera tid korrekt när den sparas till anslutningsplatsen. Standardformatet för datum och tid för anslutarplatsen för GSQL har ändrats från ”åååå-MM-dd ssZ' till 'yyyy-MM-dd ssZ'.

## <a name="115510-aadconnect-115530"></a>1.1.551.0 (AADConnect 1.1.553.0)

### <a name="fixed-issues"></a>Åtgärdade problem:

* Allmän webbtjänster:
  * Verktyget Wsconfig inte att konvertera korrekt Json-matris från ”exempelförfrågan” för metoden REST-tjänsten. Det bero på problem med serialisering det här Json-matris för REST-begäran.
  * Konfigurationsverktyget för Web Service Connector stöder inte användning av utrymme symboler i JSON-attributnamn 
    * Arbetsprofilen ersättningen kan läggas till manuellt till filen WSConfigTool.exe.config, t.ex. ```<appSettings> <add key="JSONSpaceNamePattern" value="__" /> </appSettings>```
> [!NOTE]
> JSONSpaceNamePattern nyckeln är obligatorisk för export kommer du får följande fel: meddelande: tomt namn är inte giltigt. 

* Lotus Notes:
  * När alternativet **Tillåt anpassade certifiers för organisationen/organisations enheter** inaktiveras misslyckas anslutningen under export (uppdatera) efter exporten flödet alla attribut som exporteras till Domino men vid tidpunkten för exporten en KeyNotFoundException returneras till synkronisering. 
    * Detta inträffar eftersom Byt namn på åtgärden misslyckas vid försök att ändra DN (användarnamn-attribut) genom att ändra en av attribut som nedan:  
      - LastName
      - FirstName
      - MiddleInitial
      - AltFullName
      - AltFullNameLanguage
      - organisationsenhet
      - altcommonname

  * När **Tillåt anpassade certifiers för organisationen/organisations enheter** alternativ är aktiverat, men nödvändiga certifiers är fortfarande tomma och sedan KeyNotFoundException inträffar.

### <a name="enhancements"></a>Förbättringar av:

* Allmän SQL:
  * **Scenario: ny utformning implementerat:** ”*” funktionen
  * **Lösningsbeskrivning:** ändrats metod för [med flera värden referens attribut hantering](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericsql).


### <a name="fixed-issues"></a>Åtgärdade problem:

* Allmän webbtjänster:
  * Det går inte att importera serverkonfigurationen om webbtjänsten Connector finns
  * Webbtjänsten anslutningen fungerar inte med flera webbtjänster

* Allmän SQL:
  * Inga objekt av typen listas för enstaka värde refererade attributet
  * Deltaimport på ändringsspårning strategi tar bort objekt när värdet tas bort från flera värdetabell
  * OverflowException i GSQL connector med DB2 på AS / 400

Lotus:
  * Har lagts till alternativet för att söka organisationsenheter innan du öppnar sidan GlobalParameters enable\disable

## <a name="114430"></a>1.1.443.0

Utgiven: 2017 mars

### <a name="enhancements"></a>Förbättringar

* Allmän SQL:</br>
  **Scenariot Symptom:**   det är en välkänd begränsning med SQL-anslutningen där vi bara tillåta en referens till en objekttyp och kräver korsreferens med medlemmar. </br>
  **Lösningsbeskrivning:** i bearbetningssteg för referenser var ”*” har valts, alla kombinationer av objekt av typen returneras till Synkroniseringsmotorn.

>[!Important]
- Detta skapar många platshållare
- Det krävs för att se till att namnge är unikt för olika plattformar objekttyper.


* Allmän LDAP:</br>
 **Scenario:** när endast några behållare är markerade i specifik partition, sedan sökningen fortfarande görs i hela partition. Specifika filtreras som synkroniseringstjänsten, men inte av MA som kan leda till försämrade prestanda. </br>

 **Lösningsbeskrivning:** ändrats GLDAP kopplingens koden och gör det möjligt gå igenom alla behållare som du kan söka efter objekt i var och en av dem, i stället för att söka i hela partition.


* Lotus Domino:

  **Scenario:** Domino e-post tas bort stöd för en person tas bort vid en export. </br>
  **Lösning:** konfigurerbara e-post tas bort stöd för en person tas bort vid en export.

### <a name="fixed-issues"></a>Åtgärdade problem:
* Allmän webbtjänster:
 * När du ändrar tjänstens URL i SAP wsconfig projekt via webbtjänsten konfigurationsverktyget och sedan händer följande fel: Det gick inte att hitta en del av sökvägen

      ``'C:\Users\cstpopovaz\AppData\Local\Temp\2\e2c9d9b0-0d8a-4409-b059-dceeb900a2b3\b9bedcc0-88ac-454c-8c69-7d6ea1c41d17\cfg.config\cloneconfig.xml'. ``

* Allmän LDAP:
 * GLDAP Connector kan inte se alla attribut i AD LDS
 * Guiden radbrytningar när inga UPN-attribut identifieras från LDAP-directory-schemat
 * Delta import misslyckas med identifiering av fel finns inte under fullständig import, när ”objectclass”-attributet inte är markerat
 * En sida för konfiguration av ”Konfigurera partitioner och hierarkier”, visar inte alla objekt som är vilken typ är lika med partitionen för nya servrar i ett allmänt  
LDAP-MA. De visade endast objekt från RootDSE partition.


* Allmän SQL:
 * Åtgärda för allmän SQL vattenstämpel Deltaimport flervärdesattribut inte importera fel
 * När du exporterar deleted\added värdena för flervärdesattribut, men de är inte deleted\added i datakällan.  


* Lotus Notes:
 * Ett visst fält ”efternamn” visas i metaversum korrekt men när du exporterar till anteckningar värdet för attributet är Null eller tom.
 * Korrigering för Certifier dubblettfel
 * När objekt utan data är markerat på Lotus Domino-Anslutningsapp med andra objekt sedan får vi identifiering felmeddelandet när du utför en fullständig Import.
 * När Deltaimport har körts på Lotus Domino-Anslutningsapp i slutet av som kör Microsoft.IdentityManagement.MA.LotusDomino.Service.exe tjänsten ibland returnerar ett programfel.
 * Gruppen medlemskap övergripande fungerar bra och upprätthålls, förutom när du kör export att ta bort en användare från medlemskapet visas som slutförd med en uppdatering, men användaren inte faktiskt få bort från medlemskapet i Lotus Notes.
 * En möjlighet att välja läge för export som ”Lägg till objektet längst ned på” har lagts till i konfigurationen GUI för Lotus MA att lägga till nya objekt längst ned under exporten för attribut med flera värden.
 * Anslutningen läggs nödvändiga logik för att ta bort filen från e-postmapp och ID-valvet.
 * Ta bort medlemskap fungerar inte för mellan NAB medlem.
 * Värdena bör vara har tagits bort från flervärdesattribut

## <a name="111170"></a>1.1.117.0
Utgiven: 2016 mars

**Ny anslutning**  
Första versionen av den [allmän SQL-Anslutningsapp](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericsql).

**Nya funktioner:**

* Allmän LDAP Connector:
  * Stöd har lagts till för Deltaimport med Isode.
* Anslutningsappen för webbtjänster:
  * Uppdatera csEntryChangeResult aktivitet och setImportErrorCode aktivitet så att objekt fel på som ska returneras till Synkroniseringsmotorn.
  * Uppdatera SAP6 och SAP6User mallar om du vill använda de nya funktionerna för objektet på toppnivå.
* Lotus Domino-Anslutningsapp:
  * För export behöver du en certifier per adressboken. Du kan nu använda samma lösenord för alla certifiers för att underlätta hanteringen.

**Åtgärdade problem:**

* Allmän LDAP Connector:
  * För IBM Tivoli DS kunde vissa referensattribut inte identifieras korrekt.
  * Blanksteg i början och slutet av strängar för öppna LDAP under en Deltaimport har trunkerats.
  * Byta namn objekt misslyckades för Novell och NetIQ en export som flyttas ett objekt mellan organisationsenheter/behållare och på samma gång.
* Anslutningsappen för webbtjänster:
  * Om webbtjänsten har haft flera slutpunkter för samma bindning, sedan anslutningen inte korrekt identifiera dessa slutpunkter.
* Lotus Domino-Anslutningsapp:
  * En export av fullName-attribut som en e-post i databasen fungerar inte.
  * En export som både läggas till och ta bort medlem från en grupp exporteras endast de tillagda medlemmarna.
  * Om ett Notes-dokument är ogiltigt (det attributet isValid, FALSKT) och sedan anslutningen misslyckas.

## <a name="older-releases"></a>Äldre versioner
Före mars 2016 släpptes kopplingar som supportfrågor.

**Allmän LDAP**

* [KB3078617](https://support.microsoft.com/kb/3078617) -1.0.0597, September 2015
* [KB3044896](https://support.microsoft.com/kb/3044896) -1.0.0549, 2015 mars
* [KB3031009](https://support.microsoft.com/kb/3031009) -1.0.0534, januari 2015
* [KB3008177](https://support.microsoft.com/kb/3008177) -1.0.0419, September 2014
* [KB2936070](https://support.microsoft.com/kb/2936070) -4.3.1082, 2014 mars

**WebServices**

* [KB3008178](https://support.microsoft.com/kb/3008178) -1.0.0419, September 2014

**PowerShell**

* [KB3008179](https://support.microsoft.com/kb/3008179) -1.0.0419, September 2014

**Lotus Domino**

* [KB3096533](https://support.microsoft.com/kb/3096533) -1.0.0597, September 2015
* [KB3044895](https://support.microsoft.com/kb/3044895) -1.0.0549, 2015 mars
* [KB2977286](https://support.microsoft.com/kb/2977286) -5.3.0712, augusti 2014
* [KB2932635](https://support.microsoft.com/kb/2932635) -5.3.1003, februari 2014  
* [KB2899874](https://support.microsoft.com/kb/2899874) -5.3.0721, oktober 2013
* [KB2875551](https://support.microsoft.com/kb/2875551) -5.3.0534, augusti 2013

## <a name="troubleshooting"></a>Felsökning 

> [!NOTE]
> När du uppdaterar eller AADConnect för Microsoft Identity Manager med användning av någon av ECMA2 kopplingar. 

Du måste uppdatera definitionen för anslutningsappen om du uppgraderar till matcha eller du får följande fel i händelseloggen programstart att rapportera varning ID 6947: ”Sammansättningsversionen i AAD-kopplingskonfiguration (” X.X.XXX. ”X”) är tidigare än den faktiska versionen (”X.X.XXX. ”X”) av ”C:\Program Files\Microsoft Azure AD Sync\Extensions\Microsoft.IAM.Connector.GenericLdap.dll”.

Uppdatera definitionen:
* Öppna egenskaperna för anslutningsinstansen
* Klicka på anslutningen / ansluta till fliken
  * Ange lösenordet för kontot Connector
* Klicka på var och en av flikarna med egenskaper i sin tur
  * Om den här typen av anslutning har en flik för partitioner med en Uppdateringsknapp klickar du på Uppdatera-knappen på fliken
* När alla flikarna med egenskaper har använts, klickar du på knappen OK för att spara ändringarna.


## <a name="next-steps"></a>Nästa steg
Läs mer om den [Azure AD Connect-synkronisering](how-to-connect-sync-whatis.md) konfiguration.

Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
