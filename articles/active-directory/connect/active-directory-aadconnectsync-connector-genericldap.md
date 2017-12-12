---
title: "Allmän LDAP Connector | Microsoft Docs"
description: "Den här artikeln beskriver hur du konfigurerar Microsofts allmän LDAP Connector."
services: active-directory
documentationcenter: 
author: AndKjell
manager: mtillman
editor: 
ms.assetid: 984beeb0-4d91-4908-ad81-c19797c4891b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: fe8db8f2a2412a3dfdf31201678c51e4fa0cee30
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="generic-ldap-connector-technical-reference"></a>Teknisk referens för allmän LDAP Connector
Den här artikeln beskriver allmän LDAP Connector. Artikeln gäller för följande produkter:

* Microsoft Identity Manager 2016 (MIM2016)
* Forefront Identity Manager 2010 R2 (FIM2010R2)
  * Måste använda snabbkorrigering 4.1.3671.0 eller senare [KB3092178](https://support.microsoft.com/kb/3092178).

För MIM2016 och FIM2010R2 kopplingen är tillgänglig för hämtning från den [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=717495).

När du refererar till IETF RFC det här dokumentet använder formatet (RFC [RFC nummer] / [avsnitt i RFC-dokument]), till exempel (RFC 4512/4.3).
Mer information hittar du på http://tools.ietf.org/html/rfc4500 (du måste ersätta 4500 med rätt RFC nummer).

## <a name="overview-of-the-generic-ldap-connector"></a>Översikt över allmän LDAP Connector
Allmän LDAP Connector kan du integrera synkroniseringstjänsten med en LDAP v3-server.

Vissa åtgärder och schemaelement, till exempel de som krävs för att utföra Deltaimport, har inte angetts i IETF RFC. För dessa åtgärder stöds endast LDAP-kataloger som uttryckligen anges.

Följande funktioner stöds av den aktuella versionen av kopplingen ur på hög nivå:

| Funktion | Support |
| --- | --- |
| Anslutna datakällan |Anslutningen stöds för alla LDAP v3-servrar (RFC 4510-kompatibel). Den har testats med följande: <li>Microsoft Active Directory Lightweight Directory Services (AD LDS)</li><li>Microsoft Active Directory-katalogen (AD GC)</li><li>389 katalogserver</li><li>Apache katalogserver</li><li>IBM Tivoli DS</li><li>Isode Directory</li><li>NetIQ eDirectory</li><li>Novell eDirectory</li><li>Öppna DJ</li><li>Öppna DS</li><li>Öppna LDAP (openldap.org)</li><li>Oracle (tidigare Sun) Directory Server Enterprise Edition</li><li>RadiantOne virtuell katalog-Server (VDS)</li><li>Sun en katalogserver</li>**Viktiga kataloger som inte stöds:** <li>Microsoft Active Directory Domain Services (AD DS) [Använd den inbyggda Active Directory-koppling i stället]</li><li>Oracle Internet-katalog (OID)</li> |
| Scenarier |<li>Livscykelhantering för objektet</li><li>Grupphantering</li><li>Lösenordshantering</li> |
| Åtgärder |Följande åtgärder stöds på alla LDAP-kataloger: <li>Fullständig Import</li><li>Exportera</li>Följande åtgärder stöds bara på angivna kataloger:<li>Deltaimport</li><li>Ange lösenord, ändra lösenord</li> |
| Schema |<li>Schemat har upptäckts från LDAP-schema (RFC3673 och RFC4512/4.2)</li><li>Stöder strukturella klasser, aux klasser och extensibleObject object-klassen (RFC4512/4.3)</li> |

### <a name="delta-import-and-password-management-support"></a>Delta import- och stöd för hantering av
Kataloger som stöds för Deltaimport och lösenordshantering:

* Microsoft Active Directory Lightweight Directory Services (AD LDS)
  * Har stöd för alla åtgärder för Deltaimport
  * Stöder ange ett lösenord
* Microsoft Active Directory-katalogen (AD GC)
  * Har stöd för alla åtgärder för Deltaimport
  * Stöder ange ett lösenord
* 389 katalogserver
  * Har stöd för alla åtgärder för Deltaimport
  * Stöder ange lösenordet och ändra lösenord
* Apache katalogserver
  * Stöder inte Deltaimport eftersom den här katalogen inte har en beständig Ändringslogg
  * Stöder ange ett lösenord
* IBM Tivoli DS
  * Har stöd för alla åtgärder för Deltaimport
  * Stöder ange lösenordet och ändra lösenord
* Isode Directory
  * Har stöd för alla åtgärder för Deltaimport
  * Stöder ange lösenordet och ändra lösenord
* Novell eDirectory och NetIQ eDirectory
  * Har stöd för Lägg till, uppdatera och Byt namn på åtgärder för Deltaimport
  * Har inte stöd för Delete-åtgärder för Deltaimport
  * Stöder ange lösenordet och ändra lösenord
* Öppna DJ
  * Har stöd för alla åtgärder för Deltaimport
  * Stöder ange lösenordet och ändra lösenord
* Öppna DS
  * Har stöd för alla åtgärder för Deltaimport
  * Stöder ange lösenordet och ändra lösenord
* Öppna LDAP (openldap.org)
  * Har stöd för alla åtgärder för Deltaimport
  * Stöder ange ett lösenord
  * Stöder inte ändra lösenord
* Oracle (tidigare Sun) Directory Server Enterprise Edition
  * Har stöd för alla åtgärder för Deltaimport
  * Stöder ange lösenordet och ändra lösenord
* RadiantOne virtuell katalog-Server (VDS)
  * Måste använda version 7.1.1 eller högre
  * Har stöd för alla åtgärder för Deltaimport
  * Stöder ange lösenordet och ändra lösenord
* Sun en katalogserver
  * Har stöd för alla åtgärder för Deltaimport
  * Stöder ange lösenordet och ändra lösenord

### <a name="prerequisites"></a>Krav
Innan du använder anslutningen kan du kontrollera att du har följande på synkroniseringsservern:

* 4.5.2 för Microsoft .NET Framework eller senare

### <a name="detecting-the-ldap-server"></a>Identifiering av LDAP-servern
Kopplingen använder olika metoder för att upptäcka och identifiera LDAP-servern. Anslutningen används Root DSE, leverantör namn/version och den kontrollerar schemat för att hitta unika objekt och attribut som är kända i vissa LDAP-servrar. Dessa data om finns, används för att fylla konfigurationsalternativ för kopplingen.

### <a name="connected-data-source-permissions"></a>Den anslutna datakällan behörigheter
Om du vill importera och exportera objekt i katalogen anslutna måste connector-kontot ha tillräckliga behörigheter. Anslutningen måste skrivbehörighet för att kunna exportera och läsbehörighet för att kunna importera. Behörighet konfigurationen utförs inom hanteringsupplevelser av målkatalogen sig själv.

### <a name="ports-and-protocols"></a>Portar och protokoll
Det portnummer som angavs i konfigurationen, vilket som standard är 389 för LDAP och 636 för LDAPS används för anslutningen.

LDAPS, måste du använda SSL 3.0 och TLS. SSL 2.0 stöds inte och kan inte aktiveras.

### <a name="required-controls-and-features"></a>Nödvändiga kontroller och funktioner
Följande kontroller och funktioner i LDAP-måste vara tillgängligt på LDAP-servern för anslutningstjänsten ska fungera korrekt:  
`1.3.6.1.4.1.4203.1.5.3`SANT/FALSKT filter

True/False-filtret har ofta inte rapporterats som stöds av LDAP-kataloger och kan visas på den **globala sidan** under **obligatoriska funktioner inte att hitta**. Används för att skapa **eller** filter i LDAP-frågor, till exempel när du importerar flera objekttyper. Om du kan importera mer än en objekttyp, stöder den här funktionen med LDAP-servern.

Om du använder en katalog där en unik identifierare är ankaret följande också måste vara tillgänglig (Mer information finns i [konfigurera ankare](#configure-anchors) avsnitt):  
`1.3.6.1.4.1.4203.1.5.1`Alla operativa attribut

Om katalogen har fler objekt än vad som ryms i ett anrop till katalogen, rekommenderas det att använda sidindelning. För sidindelning ska fungera måste ett av följande alternativ:

**Alternativ 1:**  
`1.2.840.113556.1.4.319`pagedResultsControl

**Alternativ 2:**  
`2.16.840.1.113730.3.4.9`VLVControl  
`1.2.840.113556.1.4.473`SortControl

Om båda alternativen är aktiverade i anslutningstjänstens konfiguration används pagedResultsControl.

`1.2.840.113556.1.4.417`ShowDeletedControl

ShowDeletedControl används bara med metoden USNChanged delta-import för att kunna se borttagna objekt.

Kopplingen försöker identifiera alternativ som finns på servern. Om alternativen inte kan identifieras, finns en varning på sidan globala i egenskaperna för anslutningen. Inte alla LDAP-servrar finns alla kontroller och funktioner de stöder och även om den här varningen finns anslutningstjänsten kanske fungerar utan problem.

### <a name="delta-import"></a>Deltaimport
Deltaimport är endast tillgänglig när en katalog för stöd för har upptäckts. Följande metoder används:

* LDAP-Accesslog. Se [http://www.openldap.org/doc/admin24/overlays.html#Access loggning](http://www.openldap.org/doc/admin24/overlays.html#Access Logging)
* LDAP-Changelog. Se [http://tools.ietf.org/html/draft-good-ldap-changelog-04](http://tools.ietf.org/html/draft-good-ldap-changelog-04)
* Tidsstämpel. Anslutningen används för Novell/NetIQ eDirectory senaste datum/tid att hämta skapade och uppdaterade objekt. Novell/NetIQ eDirectory ger inte någon motsvarande innebär att hämta borttagna objekt. Det här alternativet kan också användas om ingen annan delta-import-metod är aktiv i LDAP-servern. Det här alternativet kan inte importera borttagna objekt.
* USNChanged. Se: [https://msdn.microsoft.com/library/ms677627.aspx](https://msdn.microsoft.com/library/ms677627.aspx)

### <a name="not-supported"></a>Stöds inte
Följande LDAP-funktioner stöds inte:

* LDAP-referenser mellan servrar (RFC 4511/4.1.10)

## <a name="create-a-new-connector"></a>Skapa en ny koppling
Skapa en allmän LDAP connector i **synkroniseringstjänsten** Välj **hanteringsagenten** och **skapa**. Välj den **allmän LDAP (Microsoft)** koppling.

![CreateConnector](./media/active-directory-aadconnectsync-connector-genericldap/createconnector.png)

### <a name="connectivity"></a>Anslutning
Du måste ange en värd, Port och bindningen information på sidan anslutningar. Beroende på vilket bindning är markerad, ytterligare kan information anges i följande avsnitt.

![Anslutning](./media/active-directory-aadconnectsync-connector-genericldap/connectivity.png)

* Inställningen för Timeout för anslutningen används bara för den första anslutningen till servern när identifiering av schemat.
* Om bindningen är anonym, sedan varken användarnamn / lösenord eller certifikat används.
* För andra bindningar ange information antingen i användarnamn / lösenord eller välj ett certifikat.
* Om du använder Kerberos för autentisering kan du även ange sfär/domänen för användaren.

Den **attributet alias** textruta används för attribut som angetts i schemat med RFC4522 syntax. Dessa attribut kan inte identifieras vid identifiering av schemat och anslutningen måste du ange de attribut. Till exempel måste följande anges i attributet alias om du vill identifiera userCertificate attributet som ett binärt attribut:

`userCertificate;binary`

Följande är ett exempel på hur den här konfigurationen kan se ut:

![Anslutning](./media/active-directory-aadconnectsync-connector-genericldap/connectivityattributes.png)

Välj den **innehåller attributen i schemat** kryssrutan kan också lägga till attribut som skapats av servern. Dessa inkluderar attribut, till exempel när objektet har skapats och senaste uppdatering.

Välj **med extensible attribut i schemat** om extensible objekt (RFC4512/4.3) används och aktivera det här alternativet tillåter alla attribut som ska användas för alla objekt. Det här alternativet gör schemat mycket stora så att om inte ansluten katalog använder den här funktionen rekommendationen är att behålla alternativet vara omarkerat.

### <a name="global-parameters"></a>Globala parametrar
På sidan globala parametrar kan du konfigurera DN delta ändringsloggen och ytterligare LDAP-funktioner. Sidan fylls med den information som tillhandahålls av LDAP-servern.

![Anslutning](./media/active-directory-aadconnectsync-connector-genericldap/globalparameters.png)

Överst visas information som tillhandahålls av servern, till exempel namnet på servern. Kopplingen kontrollerar också att de obligatoriska kontrollerna finns i Root DSE. Om inte de här kontrollerna visas visas en varning. Vissa LDAP-kataloger visar inte alla funktioner i Root DSE och det är möjligt att kopplingen fungerar utan problem, även om det finns en varning.

Den **stöds kontroller** kryssrutorna styr beteendet för vissa åtgärder:

* Med trädet ta bort det markerade, en hierarki tas bort med ett LDAP-anrop. Med trädet ta bort omarkerade, stöder anslutningen en rekursiv borttagning om det behövs.
* Med växlingsbara resultaten har kopplingen en växlingsbar import med den storlek som anges på steg som kör.
* VLVControl och SortControl är ett alternativ till pagedResultsControl att läsa data från LDAP-katalogen.
* Om alla tre alternativ (pagedResultsControl VLVControl och SortControl) är omarkerat importeras alla objekt i en åtgärd som kan misslyckas om det är en stor katalog.
* ShowDeletedControl används bara när importmetoden Delta är USNChanged.

Ändringsloggen DN är namngivningskontext som används av ändringsloggen delta, till exempel **cn = changelog**. Det här värdet måste anges för att kunna göra Deltaimport.

Följande är en lista över standard ändringsloggen DNs:

| Katalog | Delta Ändringslogg |
| --- | --- |
| Microsoft AD LDS och AD GC |Identifieras automatiskt. USNChanged. |
| Apache katalogserver |Inte tillgängligt. |
| Directory 389 |Ändra logg. Värde som ska användas som standard: **cn = changelog** |
| IBM Tivoli DS |Ändra logg. Värde som ska användas som standard: **cn = changelog** |
| Isode Directory |Ändra logg. Värde som ska användas som standard: **cn = changelog** |
| Novell/NetIQ eDirectory |Inte tillgängligt. Tidsstämpel. Anslutningen använder senast uppdaterad datum/tid för att hämta läggas till och uppdateras poster. |
| Öppna DJ/DS |Ändra logg.  Värde som ska användas som standard: **cn = changelog** |
| Öppna LDAP |Åtkomstlogg. Värde som ska användas som standard: **cn = accesslog** |
| Oracle DSEE |Ändra logg. Värde som ska användas som standard: **cn = changelog** |
| RadiantOne VDS |Virtuell katalog. Beror på den katalog som är anslutna till VDS. |
| Sun en katalogserver |Ändra logg. Värde som ska användas som standard: **cn = changelog** |

Attributet lösenord är namnet på det attribut som anslutningen ska använda för att ange lösenordet i lösenordsändring och åtgärder för att ange lösenord.
Det här värdet är som standard till **userPassword** men kan ändras när de behövs för ett visst LDAP-system.

I listan ytterligare partitioner är det möjligt att lägga till ytterligare namnområden identifieras inte automatiskt. Den här inställningen kan till exempel användas om flera servrar som utgör ett logiskt kluster, vilket bör alla importeras på samma gång. Precis som Active Directory kan ha flera domäner i en skog, men alla domäner som delar ett schema, kan samma simuleras genom att ange ytterligare namnområden i den här rutan. Varje namnområde kan importera från olika servrar och ytterligare är konfigurerat på sidan Konfigurera partitioner och hierarkier. Du kan använda Ctrl + Retur för att få en ny rad.

### <a name="configure-provisioning-hierarchy"></a>Konfigurera etablering hierarki
Den här sidan används för att mappa DN komponenten, till exempel OU för den objekttyp som ska etableras, till exempel organizationalUnit.

![Etablering hierarki](./media/active-directory-aadconnectsync-connector-genericldap/provisioninghierarchy.png)

Du kan konfigurera anslutningen för att automatiskt skapa en struktur vid behov genom att konfigurera etablering hierarki. Till exempel om det finns ett namnområde dc = contoso, dc = com och en ny objektet cn = Johan, ou = Seattle, c = US, dc = contoso, dc = com etableras därefter kopplingen kan skapa ett objekt av typen land för USA och en organizationalUnit för Seattle om de inte redan finns i katalogen.

### <a name="configure-partitions-and-hierarchies"></a>Konfigurera partitioner och hierarkier
Markera alla namnområden med objekt som du tänker importera och exportera på sidan partitioner och hierarkier.

![Partitioner](./media/active-directory-aadconnectsync-connector-genericldap/partitions.png)

För varje namnområde är det också möjligt att konfigurera inställningar som ska åsidosätta de värden som anges på skärmen för anslutningen. Om värdena lämnas tomt standardvärdena, används information från skärmen för anslutningen.

Det är också möjligt att välja vilka standardbehållare och organisationsenheter kopplingen ska importera från och exportera till.

När du utför en sökning görs detta över alla behållare i partitionen. I fall där det finns många behållare detta leder till försämrade prestanda.

>[!NOTE]
Startar i mars 2017 uppdatering för allmän LDAP kan connector sökningar begränsa omfånget till bara valda behållare. Detta kan göras genom att markera kryssrutan ”Sök endast i vald behållare” som visas i bilden nedan.

![Sök endast valda behållare](./media/active-directory-aadconnectsync-connector-genericldap/partitions-only-selected-containers.png)

### <a name="configure-anchors"></a>Konfigurera ankare
Den här sidan har alltid förkonfigurerade värde och kan inte ändras. Om server-leverantör har identifierats kan ankaret fyllas med attributet ändras till exempel GUID för ett objekt. Om den inte identifieras eller inte har attributet ändras, använder kopplingen dn (unikt namn) som ankaret.

![ankare](./media/active-directory-aadconnectsync-connector-genericldap/anchors.png)


Här följer en lista över LDAP-servrar och ankaret används:

| Katalog | Fästpunktsattributet |
| --- | --- |
| Microsoft AD LDS och AD GC |objectGUID |
| 389 katalogserver |DN |
| Apache Directory |DN |
| IBM Tivoli DS |DN |
| Isode Directory |DN |
| Novell/NetIQ eDirectory |GUID |
| Öppna DJ/DS |DN |
| Öppna LDAP |DN |
| Oracle ODSEE |DN |
| RadiantOne VDS |DN |
| Sun en katalogserver |DN |

## <a name="other-notes"></a>Anmärkningar
Det här avsnittet innehåller information om aspekter som är specifika för den här anslutningen eller av andra orsaker är viktigt att känna till.

### <a name="delta-import"></a>Deltaimport
Vattenmärket delta i öppna LDAP är UTC-datum/tid. Därför måste du synkroniserade klockor mellan FIM-synkroniseringstjänsten och öppna LDAP. Om inte, vissa poster i ändringsloggen delta kan utelämnas.

För Novell eDirectory identifieras inte delta importera alla objekt tas bort. Därför är det nödvändigt att köra en fullständig import regelbundet för att söka efter alla borttagna objekt.

För kataloger med en delta-Ändringslogg som baseras på datum/tid, rekommenderas att köra en fullständig import vid regelbundna tidpunkter. Den här processen kan Synkroniseringsmotorn att hitta och skillnader mellan LDAP-servern och vad som är för närvarande i anslutningsplatsen.

## <a name="troubleshooting"></a>Felsökning
* Information om hur du aktiverar loggning för att felsöka anslutningen finns i [hur du aktiverar ETW-spårning för kopplingar](http://go.microsoft.com/fwlink/?LinkId=335731).
