---
title: "Kopplingen för Lotus Domino | Microsoft Docs"
description: "Den här artikeln beskriver hur du konfigurerar Microsofts Lotus Domino-anslutaren."
services: active-directory
documentationcenter: 
author: AndKjell
manager: femila
editor: 
ms.assetid: e07fd469-d862-470f-a3c6-3ed2a8d745bf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/119/2017
ms.author: barclayn
ms.openlocfilehash: 15155fd9e1ab2dd6d58bcaf85a465c0585d3bc41
ms.sourcegitcommit: b723436807176e17e54f226fe00e7e977aba36d5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2017
---
# <a name="lotus-domino-connector-technical-reference"></a>Teknisk referens för Lotus Domino-koppling
Den här artikeln beskriver kopplingen för Lotus Domino. Artikeln gäller för följande produkter:

* Microsoft Identity Manager 2016 (MIM2016)
* Forefront Identity Manager 2010 R2 (FIM2010R2)
  * Måste använda snabbkorrigering 4.1.3671.0 eller senare [KB3092178](https://support.microsoft.com/kb/3092178).

För MIM2016 och FIM2010R2 kopplingen är tillgänglig för hämtning från den [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=717495).

## <a name="overview-of-the-lotus-domino-connector"></a>Översikt över kopplingen för Lotus Domino
Lotus Domino-anslutningen kan du integrera synkroniseringstjänsten med IBM: s Lotus Domino-server.

Följande funktioner stöds av den aktuella versionen av kopplingen ur på hög nivå:

| Funktion | Support |
| --- | --- |
| Anslutna datakällan |Server: <li>Lotus Domino 8.5.x</li><li>Lotus Domino 9.x</li>Klient:<li>Lotus Domino 8.5.x</li><li>Lotus Notes-9.x</li> |
| Scenarier |<li>Livscykelhantering för objektet</li><li>Grupphantering</li><li>Lösenordshantering</li> |
| Åtgärder |<li>Fullständig och Deltaimport</li><li>Exportera</li><li>Ange och ändra lösenord på http-lösenord</li> |
| Schemat |<li>Person (centrala användare, kontakta (personer med inget certifikat))</li><li>Grupp</li><li>Resursen (resurs, plats, möte)</li><li>E-post i databasen</li><li>Dynamisk identifiering av attribut för objekt som stöds</li><li>Stöd för upp till 250 anpassade certifiers med en organisation och organisationsenheter (OU)</li> |

Lotus Domino-kopplingen använder Lotus Notes-klienten för att kommunicera med Lotus Domino-Server. Till följd av detta beroende måste en stöds Lotus Notes-klienten installeras på synkroniseringsservern. Kommunikationen mellan klienten och servern implementeras via gränssnittet Lotus Notes .NET Interop (Interop.domino.dll). Det här gränssnittet underlättar kommunikationen mellan Microsoft.NET plattform och Lotus Notes-klienten och stöder åtkomst till dokument för Lotus Domino och vyer. För Deltaimport är det också möjligt att det interna gränssnittet C++ används (beroende på markerade delta importmetoden).

### <a name="prerequisites"></a>Krav
Innan du använder anslutningen Kontrollera att du har följande krav på synkroniseringsserver:

* 4.5.2 för Microsoft .NET Framework eller senare
* Lotus Notes-klienten måste installeras på din synkroniseringsserver
* Kopplingen för Lotus Domino kräver Lotus Domino LDAP schemat standarddatabasen (schema.nsf) måste finnas på Domino katalogservern. Om det inte finns, kan du installera det genom att köra eller starta om tjänsten LDAP på Domino-server.

### <a name="connected-data-source-permissions"></a>Den anslutna datakällan behörigheter
För att genomföra någon av uppgifterna som stöds i Lotus Domino-koppling, måste du vara medlem i följande grupper:

* Administratörer med fullständig åtkomst
* Administratörer
* Databasadministratörer

I följande tabell visas de behörigheter som krävs för varje åtgärd:

| Åtgärd | Behörighet som krävs |
| --- | --- |
| Importera |<li>Läsa offentliga dokument</li><li> Fullständig åtkomst administratör (när du är medlem i administratörsgruppen för fullständig åtkomst kan du automatiskt ha effektiv åtkomst till i ACL.)</li> |
| Export och ange ett lösenord |Effektiv åtkomst: <li>Skapa dokument</li><li>Ta bort dokument</li><li>Läsa offentliga dokument</li><li>Skriva offentliga dokument</li><li>Replikera eller kopiera dokument</li>För export måste du också följande roller: <li>CreateResource</li><li>GroupCreator</li><li>GroupModifier</li><li>UserCreator</li><li>UserModifier</li> |

### <a name="direct-operations-and-adminp"></a>Direkta åtgärder och AdminP
Åtgärder som antingen gå direkt till Domino katalogen eller via AdminP-processen. Följande tabeller listan över alla objekt, åtgärder och, om tillämpligt, metoden relaterade implementering:

**Primär adressboken**

| Objekt | Skapa | Uppdatering | Ta bort |
| --- | --- | --- | --- |
| Person |AdminP |Direkt |AdminP |
| Grupp |AdminP |Direkt |AdminP |
| MailInDB |Direkt |Direkt |Direkt |
| Resurs |AdminP |Direkt |AdminP |

**Sekundär adressboken**

| Objekt | Skapa | Uppdatering | Ta bort |
| --- | --- | --- | --- |
| Person |Saknas |Direkt |Direkt |
| Grupp |Direkt |Direkt |Direkt |
| MailInDB |Direkt |Direkt |Direkt |
| Resurs |Saknas |Saknas |Saknas |

När en resurs skapas, skapas ett Notes-dokument. När en resurs tas bort tas bort på samma sätt Notes-dokumentet.

### <a name="ports-and-protocols"></a>Portar och protokoll
IBM Lotus Notes-klienten och Domino-servrar kommunicerar med anteckningar Remote Procedure anropa (NRPC) där NRPC ska använda TCP/IP. Standardportnumret är 1352, men kan ändras av administratören Domino.

### <a name="not-supported"></a>Stöds inte
Följande åtgärder stöds inte av den aktuella versionen av Lotus Domino-anslutningen:

* Flytta postlådan mellan servrar.

## <a name="create-a-new-connector"></a>Skapa en ny koppling
### <a name="client-software-installation-and-configuration"></a>Installera klientprogramvaran och konfiguration
Lotus Notes måste vara installerat på servern **innan** anslutningstjänsten har installerats.

När du installerar gör du en **enskild användare installera**. Standard **flera användare installera** fungerar inte.  
![Notes1](./media/active-directory-aadconnectsync-connector-domino/notes1.png)

Installera endast nödvändiga funktioner för Lotus Notes på sidan funktioner och **enda klientinloggning**. Enkel inloggning krävs för kopplingen för att kunna logga in på Domino-server.  
![Notes2](./media/active-directory-aadconnectsync-connector-domino/notes2.png)

**Obs:** Start Lotus Notes när med en användare som finns på samma server som kontot du använder som kopplingens tjänstkonto. Kontrollera också att avsluta Lotus Notes-klienten på servern. Den kan inte köras samtidigt som anslutningen försöker ansluta till Domino-server.

### <a name="create-connector"></a>Skapa koppling
Skapa en Lotus Domino-anslutning i **synkroniseringstjänsten** Välj **hanteringsagenten** och **skapa**. Välj den **Lotus Domino (Microsoft)** koppling.  
![CreateConnector](./media/active-directory-aadconnectsync-connector-domino/createconnector.png)

Om din version av synkroniseringstjänsten ger dig möjlighet att konfigurera **arkitektur**, kontrollera att anslutningen har angetts till standardvärdet att köras i **processen**.

### <a name="connectivity"></a>Anslutning
På sidan anslutningar måste du ange servernamnet för Lotus Domino och ange autentiseringsuppgifterna för inloggning.  
![Anslutning](./media/active-directory-aadconnectsync-connector-domino/connectivity.png)

Egenskapen Domino Server stöder två format för namnet på servern:

* Servernamn
* ServerName/DirectoryName

Den **ServerName/DirectoryName** format är formatet för det här attributet eftersom det ger snabbare svar när kopplingen kontaktar Domino-Server.

Filen som tillhandahållits UserID lagras i konfigurationsdatabasen för synkroniseringstjänsten.

För **Deltaimport** finns följande alternativ:

* **Ingen**. Kopplingen inte någon delta-import.
* **Lägg till/uppdatera**. Deltaimport för Connector har lägga till och uppdatera åtgärder. Att ta bort, en **fullständig Import** åtgärd krävs. Den här åtgärden använder .net-interop.
* **Lägg till/uppdatera och ta bort**. Deltaimport för Connector har lägga till, uppdatera och ta bort. Den här åtgärden används de interna C++-gränssnitt.

I **Schemaalternativ** har du följande alternativ:

* **Standard schemat**. Anslutningen identifierar schemat från Domino-server. Det här alternativet är standardalternativet.
* **DSML-Schema**. Används endast om servern Domino inte exponerar schemat. Du kan sedan skapa en DSML-fil med schemat och importera det i stället. Mer information om DSML finns [OASIS](https://www.oasis-open.org/committees/tc_home.php?wg_abbrev=dsml).

När du klickar på nästa verifieras konfigurationsparametrar användar-ID och lösenord.

### <a name="global-parameters"></a>Globala parametrar
På sidan globala parametrar Konfigurera tidszonen och importera och exportera åtgärdsalternativ.  
![Globala parametrar](./media/active-directory-aadconnectsync-connector-domino/globalparameters.png)

Den **Domino serverns tidszon** parametern definierar platsen för Domino-Server.

Det här konfigurationsalternativet krävs för att stödja **Deltaimport** operations eftersom det låter synkroniseringstjänsten avgöra ändringar mellan de två sista import.

>[!Note]
Från och med mars 2017 uppdateringen skärmen globala parametrar innehåller alternativ för att ta bort användarens e-databasen när användarens togs bort.

![Ta bort användarens postlåda](./media/active-directory-aadconnectsync-connector-domino/AdminP.png)

#### <a name="import-settings-method"></a>Importera inställningar, metod
Den **utföra fullständig Import av** finns följande alternativ:

* Söka
* Visa (rekommenderas)

**Sök** är med hjälp av indexering i Domino men det är vanligt att indexen uppdateras inte i realtid och de data som returneras från servern är inte alltid korrekt. För ett system med många ändringar av det här alternativet vanligtvis fungerar inte väl och ger false tas bort i vissa situationer. Dock **Sök** är snabbare än **visa**.

**Visa** är det rekommenderade alternativet eftersom det innehåller ett korrekt tillstånd för data. Det är något långsammare än sökning.

#### <a name="creation-of-virtual-contact-objects"></a>Skapa virtuella kontaktobjekt
Den **skapa \_kontaktobjekt** finns följande alternativ:

* Ingen
* Värden för icke-referens
* Referens-och icke-referens

I Domino, kan referensattribut innehålla olika format för att referera till andra objekt. För att kunna representera olika variationer, Connector implementerar \_kontakta objekt, även kallat **virtuella kontakter** (VC). Dessa objekt skapas så att de kan ansluta till befintliga MV-objekt eller projiceras som nya objekt. På så sätt kan attributet referenser bevaras.

Genom att aktivera den här inställningen och om innehållet i ett referensattribut inte är ett DN-format, en \_kontakta objektet har skapats. Medlemsattributet i en grupp kan till exempel innehålla SMTP-adresser. Det är också möjligt att ha kort filnamn och andra attribut i referensattribut. Det här scenariot väljer **icke-referensvärden**. Den här konfigurationen är den vanligaste inställningen för Domino-implementeringar.

När Lotus Domino är konfigurerad för att ha separata adressböcker med olika unika namn som representerar samma objekt, är det möjligt att också skapa \_kontakta objekt för alla referensvärden som finns i en adressbok som. Det här scenariot, Välj den **och icke-referensvärden** alternativet.

Om du har flera värden i attributet **FullName** Domino, sedan du också vill aktivera skapandet av virtuella kontakter så referenser kan lösas. Det här attributet kan ha flera värden efter ett annullering eller äktenskapsskillnad. Markera kryssrutan **aktivera... FullName har flera värden** för det här scenariot.

Genom att anslutas på rätt attribut i \_kontaktobjekt skulle anslutas till MV-objekt.

Dessa objekt har VC =\_kontakta lagts till deras unika namn.

#### <a name="import-settings-conflict-object"></a>Importera inställningar, objekt i konflikt
**Uteslut objekt i konflikt**

Det är möjligt att flera objekt har samma DN på grund av problem med replikering i en stor Domino-implementering. I dessa fall visas kopplingen två objekt med olika UniversalIDs men samma unika namn. Den här konflikten innebär att ett tillfälligt objekt som skapas i anslutningsplatsen. Kopplingen kan ignorera de objekt som har markerats i Domino som offer för replikering. Det rekommenderas att behålla den här kryssrutan markerad.

#### <a name="export-settings"></a>Exportera inställningar
Om alternativet **använder AdminP för uppdatering av referenser** är avmarkerat, export av referensattribut, till exempel medlem, är ett direkt anrop och använder inte AdminP-processen. Använd endast det här alternativet om AdminP inte har konfigurerats för att upprätthålla referensintegritet.

#### <a name="routing-information"></a>Information om routning
I Domino är det möjligt att ett referensattribut har routningsinformationen inbäddade som ett suffix för att det unika namnet. Till exempel Medlemsattributet i en grupp kan innehålla **CN =example/organization@ABC**. Suffixet @ABC är routningsinformation. Denna information används av Domino för att skicka e-postmeddelanden till rätt Domino-systemet, vilket kan vara ett system i en annan organisation. Du kan ange routning suffix som används inom organisationen i omfattningen för anslutningen i fältet Routing Information. Om ett av dessa värden finns som suffix i ett referensattribut, tas routningsinformation bort från referensen. Om routning suffix på ett referensvärde inte kan matchas med ett av dessa värden anges en \_kontakta objektet har skapats. Dessa \_kontaktobjekt skapas med **RO = @<RoutingSuffix>**  infogas i det unika namnet. För dessa \_kontakta objekt läggs följande attribut för att ansluta till en verkliga objektet om det behövs: \_routingName, \_kontaktperson, \_displayName, och UniversalID.

#### <a name="additional-address-books"></a>Ytterligare adressböcker
Om du inte har **directory hjälp** installeras, som innehåller namnet på sekundär adressböcker och du kan ange dessa adressböcker manuellt.

#### <a name="multivalued-transformation"></a>Omvandling av flera värden
Många är i Lotus Domino flera värden. Motsvarande metaversum-attribut är vanligtvis samma värden. Genom att konfigurera Import och Export åtgärden alternativet kan aktivera du kopplingen för att hjälpa dig med krävs vid översättning av de berörda attribut.

**Exportera**  
Åtgärden Exportalternativet stöder två lägen:

* Lägg till objekt
* Ersätta objektet

**Ersätta objektet** – när du väljer det här alternativet kopplingen alltid ta bort de aktuella värdena för attributet i Domino och ersätta dem med de tillhandahållna värdena. Den angivna värden kan vara antingen enstaka eller flera värden.

Exempel: Attributet assistent för en person-objektet har följande värden:

* CN = Greg Winston/OU=Contoso/O=Americas,NAB=names.nsf
* CN = John Smith/OU=Contoso/O=Americas,NAB=names.nsf

Om en ny assistent med namnet **David Alexander** tilldelas till den här personen objekt resultatet är:

* CN = David Alexander/OU=Contoso/O=Americas,NAB=names.nsf

**Lägga till objektet** – när du väljer det här alternativet kopplingen behåller befintliga värden för attributet i Domino och infoga nya värden överst i listan.

Exempel: Attributet assistent för en person-objektet har följande värden:

* CN = Greg Winston/OU=Contoso/O=Americas,NAB=names.nsf
* CN = John Smith/OU=Contoso/O=Americas,NAB=names.nsf

Om en ny assistent med namnet **David Alexander** tilldelas till den här personen objekt resultatet är:

* CN = David Alexander/OU=Contoso/O=Americas,NAB=names.nsf
* CN = Greg Winston/OU=Contoso/O=Americas,NAB=names.nsf
* CN = John Smith/OU=Contoso/O=Americas,NAB=names.nsf

**Importera**  
Alternativet Importera åtgärden stöder två lägen:

* Standard
* Flervärdesattribut enskilt värde

**Som standard** – när du väljer standardalternativet alla värden för alla attribut har importerats.

**Flervärdesattribut enskilt värde** – när du väljer det här alternativet ett flervärdesattribut konverteras till en enkelvärdesattribut. Om det finns fler än ett värde, används värdet överst (det här värdet är vanligtvis även senast).

Exempel: Attributet assistent för en person-objektet har följande värden:

* CN = David Alexander/OU=Contoso/O=Americas,NAB=names.nsf
* CN = Greg Winston/OU=Contoso/O=Americas,NAB=names.nsf
* CN = John Smith/OU=Contoso/O=Americas,NAB=names.nsf

Den senaste uppdateringen till detta attribut är **David Alexander**. Alternativet Importera åtgärden är inställd på Multivalued enskilt värde och importerar connector endast **David Alexander** till anslutningsplatsen.

Logik för att konvertera flera värden attribut till enkelvärdesattribut anges gäller inte attributet grupp medlem och attributet person fullname.

Det också möjligt att konfigurera importera och exportera regler för anspråksomvandling för flervärdesattribut per attribut, som undantag till den globala regeln. Ange [objecttype] om du vill konfigurera det här alternativet. [attributename] i den **importera undantagslistan för attributet** och **exportera undantagslistan för attributet** textrutor. Om du anger Person.Assistant och den globala flaggan anges för att importera alla värden, importeras till exempel bara det första värdet för assistenten.

#### <a name="certifiers"></a>Certifiers
Alla enheter i organisationen/organiserad visas av anslutningen. Om du vill kunna exportera person objekt till den primära adressboken krävs en certifier med dess lösenord.

Om alla certifiers har ett och samma lösenord de **lösenordet för alla Certifers** kan användas. Du kan ange lösenord här och endast ange certifier-filen.

Om du bara importera behöver du inte ange några certifiers.

### <a name="configure-provisioning-hierarchy"></a>Konfigurera etablering hierarki
Hoppa över den här dialogrutan sidan när du konfigurerar Lotus Domino-anslutningen. Lotus Domino-anslutningen har inte stöd för hierarkin etablering.  
![Etablering hierarki](./media/active-directory-aadconnectsync-connector-domino/provisioninghierarchy.png)

### <a name="configure-partitions-and-hierarchies"></a>Konfigurera partitioner och hierarkier
När du konfigurerar partitioner och hierarkier, väljer du den primära adressboken kallas NAB=names.nsf. Du kan välja sekundära adressböcker om de finns förutom den primära adressboken.  
![Partitioner](./media/active-directory-aadconnectsync-connector-domino/partitions.png)

### <a name="select-attributes"></a>Välj attribut
När du konfigurerar din attribut, måste du välja alla attribut som föregås  **\_MMS\_**. Dessa attribut krävs när du etablera nya objekt för Lotus Domino

![Attribut](./media/active-directory-aadconnectsync-connector-domino/attributes.png)

## <a name="object-lifecycle-management"></a>Livscykelhantering för objektet
Det här avsnittet innehåller en översikt över de olika objekt i Domino.

### <a name="person-objects"></a>Person objekt
Person-objektet representerar användare i organisationen och organisationsenheter. Utöver standardattribut Domino-administratör kan lägga till anpassade attribut som en Person-objektet. Minst måste en Person objekt innehålla alla obligatoriska attribut. En fullständig lista över obligatoriska attribut finns [Lotus Notes egenskaper](#lotus-notes-properties). Följande krav måste uppfyllas för att registrera en person objektet:

* Adressboken (names.nsf) måste ha definierats och bör vara den primära adressboken.
* Du måste ha O/OU certifier Id och lösenord för att registrera en viss användare i organisationen / organisationsenhet.
* Du måste ange en specifik uppsättning Lotus Notes-egenskaperna för en person-objektet. De här egenskaperna används för att etablera objektet person. Mer information finns i avsnittet [Lotus Notes egenskaper](#lotus-notes-properties) senare i det här dokumentet.
* Första http-lösenordet för en person är ett attribut och ange under etableringen.
* Person objektet måste vara något av följande tre stöds:
  1. Normal användare som har en e-fil och en fil för användar-id
  2. Centrala användare (en Normal användare som innehåller alla centrala databasfiler)
  3. Kontakter (användare med någon id-fil)

Personer (utom kontakter) kan ytterligare grupperas till oss och internationella användare som definierats av värdet för den \_MMS\_IDRegType egenskapen. Dessa personer använder Anteckningar-klienten att komma åt Lotus Domino-servrar är ett anteckningar-Id och ett Person-dokument. Om de använder Anteckningar mail sedan ha de också en e-fil. Användaren måste vara registrerad för att aktiveras. Mer information finns i:

* [Konfigurera anteckningar användare](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_SETTING_UP_NOTES_USERS.html)
* [Användarregistrering](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_REGISTERING_USERS.html)
* [Hantera användare](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_MANAGING_USERS_5151.html)
* [Byta namn på användare](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_RENAMING_A_USER_AUTOMATICALLY.html)

Dessa åtgärder utförs i Lotus Domino och sedan importeras till synkroniseringstjänsten.

### <a name="resources-and-rooms"></a>Resurser och lokaler
En resurs är en annan typ av en databas i Lotus Domino. Resurser kan vara konferensrum med olika typer av utrustning, till exempel projektorer. Det finns underordnade typer av resurser som stöds av Lotus Domino-anslutningen som definieras av attributet resurstyp:

| Typ av resurs | Resurstyp-attribut |
| --- | --- |
| Plats |1 |
| Resursen (Other) |2 |
| Online-möte |3 |

För objektet resurstypen ska fungera, krävs följande:

* Reservation resursdatabasen ska redan finnas på den anslutna servern Domino
* Platsen har redan definierats för resursen

Databasen Resource Reservation innehåller tre typer av dokument:

* Plats-profil
* Resurs
* Reservation

Mer information om hur du konfigurerar av databasen Resource Reservation finns [installerar databasen Resource reservationer](https://www-01.ibm.com/support/knowledgecenter/SSKTMJ_8.0.1/com.ibm.help.domino.admin.doc/DOC/H_SETTING_UP_THE_RESOURCE_RESERVATIONS_DATABASE.html).

**Skapa, uppdatera, och ta bort resurser**  
Skapa, uppdatera och ta bort åtgärder utförs av Lotus Domino-anslutningen i databasen Resource Reservation. Resurser skapas som dokument i Names.nsf (det vill säga den primära adressboken). Mer information om redigera och ta bort resurser finns [redigera och ta bort resursen dokument](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_EDITING_AND_DELETING_RESOURCE_DOCUMENTS.html).

**Importera och exportera åtgärden för resurser**  
Resurserna som kan importeras till och exporteras från synkroniseringstjänsten som någon annan objekttyp. Välj typ av objekt som resurs under konfigurationen. Du bör ha information för resurstypen, konferens-databasen och platsnamn för lyckade exporten.

### <a name="mail-in-databases"></a>E-post i databaser
En e-post i databasen är en databas som är utformade för att ta emot e-post. Det är en Lotus Domino-postlåda som inte är associerad med ett specifikt användarkonto för Lotus Domino (det vill säga den inte har sin egen ID-filen och lösenord). En e-post i databasen har en unik användar-ID (”kort namn”) som är kopplade till den och en egen e-postadress.

Om det behövs en separat postlåda med sin egen e-postadress som kan delas mellan olika användare (till exempel group@contoso.com), en e-databas skapas. Åtkomst till den här postlådan styrs via dess åtkomstkontrollista (ACL), som innehåller namnen på de anteckningar-användare som tillåts att öppna postlådan.

En lista över attribut som krävs finns i avsnittet [obligatoriska attribut](#mandatory-attributes) senare i den här artikeln.

När en databas är utformat för att få ett e-post, skapas ett e-post i databasen dokument i Lotus Domino. Det här dokumentet måste finnas i Domino-katalogen på alla servrar som lagrar en kopia av databasen. Mer information om hur du skapar ett dokument för e-post i databasen, se [skapa e-post i databasen dokument](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_CREATING_A_MAILIN_DATABASE_DOCUMENT_FOR_A_NEW_DATABASE_OVERVIEW.html).

Innan du skapar en databas för e-post i databasen ska redan finnas (skulle ha skapats av Lotus Admin) på Domino-servern.

### <a name="group-management"></a>Grupphantering
Du kan få en detaljerad översikt över grupphantering för Lotus Domino från följande resurser:

* [Med hjälp av grupper](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_USING_GROUPS_OVER.html)
* [Skapa en grupp](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_CREATING_AND_MODIFYING_GROUPS_STEPS_MIDTOPIC_55038956829238418.html)
* [Skapa och ändra grupper](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_CREATING_AND_MODIFYING_GROUPS_STEPS.html)
* [Hantera grupper](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_MANAGING_GROUPS_1804.html)
* [Byta namn på en grupp](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_RENAMING_A_GROUP_STEPS.html)

### <a name="password-management"></a>Lösenordshantering
Det finns två typer av lösenord för en registrerad Lotus Domino-användare:

1. Användarlösenord (lagrat i User.id-fil)
2. Internet / http-lösenord

Kopplingen för Lotus Domino stöder endast åtgärder med HTTP-lösenord.

Om du vill utföra lösenordshantering, bör du aktivera lösenordshantering för Management Agent Designer-anslutningen. Välj för att aktivera lösenordshantering **aktivera lösenordshantering** på den **konfigurera tillägg** dialogrutan sidan.  
![Konfigurera tillägg](./media/active-directory-aadconnectsync-connector-domino/configureextensions.png)

Lotus Domino connector stöd följande åtgärder på Internet-lösenord:

* Ange ett lösenord: Ange ett lösenord anger ett nytt HTTP-Internet-lösenord för användaren i Domino. Som standard är också kontot upplåst. Flaggan unlock exponeras WMI-gränssnitt för Synkroniseringsmotorn.
* Ändra lösenord: I det här scenariot kan en användare kanske vill ändra lösenordet eller uppmanas att ändra lösenord efter en angiven tid. Den här åtgärden ska kunna placera, både (gammalt och det nya lösenordet) är obligatoriska. När ändras uppdateras det nya lösenordet i Lotus Domino.

Mer information finns i:

* [Med hjälp av funktionen för kontoutelåsning Internet](http://www.ibm.com/developerworks/lotus/library/domino8-lockout/)
* [Hantera Internet-lösenord](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_NOTES_AND_INTERNET_PASSWORD_SYNCHRONIZATION_7570_OVER.html)

## <a name="reference-information"></a>Referensinformation
Det här avsnittet innehåller till exempel Attributbeskrivningar och attributet krav för Lotus Domino-anslutning.

### <a name="lotus-notes-properties"></a>Lotus Notes-egenskaper
När du etablerar Person objekt till din katalog för Lotus Domino måste din objekt ha en specifik uppsättning egenskaper med specifika värden som fylls i. Dessa värden krävs endast för skapar operationer.

Följande tabell listar de här egenskaperna och ger en beskrivning av dem.

| Egenskap | Beskrivning |
| --- | --- |
| \_MMS_AltFullName |Det alternativa användarens fullständiga namnet. |
| \_MMS_AltFullNameLanguage |Språket som ska användas för att ange användarens alternativa fullständiga namn. |
| \_MMS_CertDaysToExpire |Antal dagar från det aktuella datumet innan certifikatet upphör att gälla. Om den inte anges är standard två år från dagens datum. |
| \_MMS_Certifier |Egenskapen som innehåller Hierarkinamnet på certifier organisationens. Exempel: OU = OrganizationUnit, O = Org, C = land. |
| \_MMS_IDPath |Om egenskapen är tom, skapas ingen användare identifiering filen lokalt på synkroniseringsservern. Om egenskapen innehåller ett filnamn, skapas en användar-ID-fil i mappen madata. Egenskapen kan även innehålla en fullständig sökväg. |
| \_MMS_IDRegType |Personer kan klassificeras som kontakter, oss användare och internationella användare. I följande tabell visas möjliga värden: <li>0 - kontakt</li><li>1 - USA användare</li><li>2 - internationella användare</li> |
| \_MMS_IDStoreType |Obligatorisk egenskap för USA och internationellt användare. Egenskapen innehåller ett heltalsvärde som anger om användar-ID lagras som en bifogad fil i anteckningar adressboken eller i personens e-fil. Om filen användar-ID är bifogad i adressboken, kan du skapa som en fil med \_MMS_IDPath. <li>Tom - fil-ID för lagring i ID valvet, ingen identifiering-fil (används för kontakter).</li><li> 1 - bifogad fil i anteckningar adressboken. Den \_MMS_Password-egenskapen måste anges för användaren identifiering filer som är bifogade filer</li><li>2 - lagra ID i personens e-fil. Den \_MMS_UseAdminP måste anges till false så att e-postfilen skapas under registreringen Person. Den \_MMS_Password-egenskapen måste anges för identifiering av användarfiler.</li> |
| \_MMS_MailQuotaSizeLimit |Antal megabyte som tillåts för e-filen databasen. |
| \_MMS_MailQuotaWarningThreshold |Antal megabyte som tillåts för e-filen databasen innan en varning. |
| \_MMS_MailTemplateName |E-mallfilen som används för att skapa användarens e-fil. Om du anger en mall för skapas e-postfilen med hjälp av den angivna mallen. Om ingen mall har angetts används standardfilen för mallen för att skapa filen. |
| \_MMS_OU |Egenskapen som är OU-namn under certifier. Den här egenskapen ska vara tom för kontakter. |
| \_MMS_Password |Obligatorisk egenskap för användare. Egenskapen innehåller lösenordet för filen identifiering för objektet. |
| \_MMS_UseAdminP |Egenskapen ska anges till true om e-postfilen ska skapas av AdminP process på Domino-server (asynkront till exportprocessen). Om egenskapen är inställd på false, skapas e-postfilen med Domino användaren (synkron i exporten). |

För en användare med en tillhörande ID-fil, den \_MMS_Password egenskapen måste innehålla ett värde. E-postserver och MailFile egenskaperna för en användare måste innehålla ett värde för e-poståtkomst via Lotus Notes-klienten.

Följande egenskaper måste innehålla värden för att komma åt e-post via en webbläsare:

* MailFile - obligatorisk egenskap som innehåller sökvägen för Lotus Domino-server där e-postfilen lagras.
* E-postserver - obligatorisk egenskap som innehåller namnet på Lotus Domino-server. Det här värdet är namnet som ska användas när du skapade filen Lotus e-post på Domino-server.
* HTTPPassword - valfri egenskap som innehåller Web access-lösenordet för objektet.

Egenskapen HTTPPassword måste innehålla ett värde för att komma åt Domino-Server utan e-funktionen. Egenskapen MailFile och egenskapen e-postserver kan vara tom.

Med \_MMS_ IDStoreType = 2 (store-id i filen för e-post), egenskapen MailSystem för NotesRegistrationclass anges till REG_MAILSYSTEM_INOTES (3).

### <a name="mandatory-attributes"></a>Obligatoriska attribut
Dessa typer av objekt (dokumenttyper) stöder huvudsakligen i Lotus Domino-anslutningen:

* Grupp
* E-post i databasen
* Person
* Kontaktpersonen med ingen certifier
* Resurs

Det här avsnittet innehåller de attribut som är obligatoriska för varje objekt som stöds att exportera till en Domino-server.

| Objekttyp | Obligatoriska attribut |
| --- | --- |
| Grupp |<li>ListName</li> |
| Main i databasen |<li>Fullständigt namn</li><li>MailFile</li><li>E-postserver</li><li>MailDomain</li> |
| Person |<li>Efternamn</li><li>MailFile</li><li>Kort filnamn</li><li>\_MMS_Password</li><li>\_MMS_IDStoreType</li><li>\_MMS_Certifier</li><li>\_MMS_IDRegType</li><li>\_MMS_UseAdminP</li> |
| Kontaktpersonen med ingen certifier |<li>\_MMS_IDRegType</li> |
| Resurs |<li>Fullständigt namn</li><li>ResourceType</li><li>ConfDB</li><li>Resurskapacitet</li><li>plats</li><li>Visningsnamn</li><li>MailFile</li><li>E-postserver</li><li>MailDomain</li> |

## <a name="common-issues-and-questions"></a>Vanliga problem och frågor
### <a name="schema-detection-does-not-work"></a>Schemat identifiering fungerar inte
Om du vill kunna identifiera schemat är det nödvändigt att filen schema.nsf finns på Domino-servern. Den här filen visas bara om LDAP har installerats på servern. Om schemat inte kan identifieras, kontrollera följande:

* Filen schema.nsf finns i rotmappen på Domino-Server
* Användaren har behörighet att visa filen schema.nsf.
* Tvinga fram omstart av LDAP-servern. Öppna **Lotus Domino konsolen** och använda **berätta för LDAP-ReloadSchema** kommando för att läsa in schemat.

### <a name="not-all-secondary-address-books-are-visible"></a>Inte alla sekundära adressböcker visas
Domino-anslutaren förlitar sig på funktionen **Directory hjälp** ska kunna hitta sekundära adressböcker. Om de sekundära adressböckerna saknas kontrollerar du om [Directory hjälp](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_ABOUT_DIRECTORY_ASSISTANCE.html) har aktiverats och konfigurerats på Domino-servern.

### <a name="custom-attributes-in-domino"></a>Anpassade attribut i Domino
Det finns flera olika sätt i Domino att utöka schemat så att det visas som ett anpassat attribut som kan användas av anslutningen.

**Metod 1: Utöka schemat för Lotus Domino**

1. Skapa en kopia av Domino Directory mall {PUBNAMES. NTF} genom att följa [här](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_CREATING_A_COPY_OF_THE_DEFAULT_PUBIC_ADDRESS_BOOK_TEMPLATE.html) (du bör inte anpassa IBM Lotus Domino standardkatalogen mall):
2. Öppna mallen för kopia av Domino directory {CONTOSO. NTF} mall som skapades i Domino Designer och följ de här stegen:
   * Klicka på delade element och expandera underformulär
   * Dubbelklicka på ${ObjectName} InheritableSchema underformulär (där {ObjectName} är namnet på standardklassen strukturella objekt, till exempel: Person).
   * Namnet på attributet som du vill lägga till i schemat {MyPersonAtrribute} och motsvarande attribut. Skapa ett fält genom att markera den **skapa** -menyn och välj sedan **fältet** -menyn.
   * Ange egenskaper i fältet lagts till genom att välja dess typ, format, storlek, teckensnitt och andra relaterade parametrar på fältet i fönstret.
   * Behåll standardvärdet detsamma som namnet som angetts för attributet attributet (till exempel om attributnamn är MyPersonAttribute, Behåll standardvärdet med samma namn).
   * Spara ${ObjectName} InheritableSchema underformuläret med uppdaterade värdena.
3. Ersätta Domino Directory mallen {PUBNAMES. NTF} med den nya anpassade mallen {CONTOSO. NTF} genom att följa [här](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_ABOUT_RULES_FOR_CUSTOMIZING_THE_PUBLIC_ADDRESS_BOOK.html).
4. Stäng Domino Admin och öppna Domino-konsolen för att starta om tjänsten LDAP och läsa in LDAP-schemat:
   * I konsolen för Domino infoga kommandot under **Domino kommandot** text in för att starta om tjänsten LDAP - [starta om uppgiften LDAP](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_STARTING_AND_STOPPING_THE_LDAP_SERVER_OVER.html).
   * För att läsa in LDAP schemat kommandot berätta för LDAP - berätta för LDAP-ReloadSchema
5. Lägga till personen öppna Domino Admin och väljer fliken för personer och grupper för att se tillagda attributet avspeglas i domino.
6. Öppna Schema.nsf från **filer** fliken och se tillagda attributet avspeglas i dominoPerson LDAP-object-klassen.

**Metod 2: Skapa en auxClass med anpassade attribut och associera med object-klassen**

1. Skapa en kopia av Domino Directory mall {PUBNAMES. NTF} genom att följa [här](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_CREATING_A_COPY_OF_THE_DEFAULT_PUBIC_ADDRESS_BOOK_TEMPLATE.html) (aldrig anpassa IBM Lotus Domino standardkatalogen mall):
2. Öppna mallen för kopia av Domino directory {CONTOSO. NTF} mall som skapades i Domino Designer.
3. Välj delad kod och underformulär i den vänstra rutan.
4. Klicka på ny underformulär
5. Gör följande för att ange egenskaper för ny underformuläret:
   * Med nya underformuläret öppna, och välj Design - underformulär egenskaper
   * Ange ett namn för den extra objektklassen--exempelvis TestSubform bredvid egenskapen Name.
   * Behåll egenskapen alternativ ”inkludera i underformulär Insert... dialogrutan” markerats
   * Avmarkera de alternativ för egenskapen ”Render passerar HTML i anteckningar”.
   * Lämna de andra egenskaperna samma och Stäng dialogrutan Egenskaper för underformuläret.
   * Spara och Stäng nya underformuläret.
6. Gör följande om du vill lägga till ett fält för att definiera auxiliary object-klassen:
   * Öppna underformuläret som du skapade.
   * Välj skapa - fältet.
   * Bredvid namnet på fliken grunderna i dialogrutan fält, anger du ett namn, till exempel: {MyPersonTestAttribute}.
   * Ange dess egenskaper genom att välja dess typ, format, storlek, teckensnitt och relaterade egenskaper i fältet lagts till.
   * Behåll standardvärdet detsamma som namnet som angetts för attributet attributet (till exempel om attributnamn är MyPersonTestAttribute, Behåll standardvärdet med samma namn).
   * Spara underformuläret med uppdaterade värdena och gör följande:
     * I den vänstra rutan, Välj delad kod och underformulär
     * Välj ny underformuläret och välj Design - Design egenskaper.
     * Klicka på fliken tredje från vänster och välj **sprida detta förbud av designändring**.
7. Öppna ${ObjectName} ExtensibleSchema underformulär, (där {ObjectName} är namnet på standardklassen strukturella objekt, till exempel – Person).
8. Infoga resurs och välj underformuläret (som du skapade, till exempel – TestSubform) och spara ${ObjectName} ExtensibleSchema underformuläret.
9. Ersätta Domino Directory mallen {PUBNAMES. NTF} med den nya anpassade mallen {CONTOSO. NTF} genom att följa [här](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_ABOUT_RULES_FOR_CUSTOMIZING_THE_PUBLIC_ADDRESS_BOOK.html).
10. Stäng Domino Admin och öppna Domino-konsolen för att starta om tjänsten LDAP och läsa in LDAP-schemat:
    * I konsolen för Domino infoga kommandot under **Domino kommandot** text in för att starta om tjänsten LDAP - [starta om uppgiften LDAP](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_STARTING_AND_STOPPING_THE_LDAP_SERVER_OVER.html).
    * Att läsa in LDAP schemat kommandot berätta för LDAP- **berätta för LDAP-ReloadSchema**.
11. Öppna Domino Admin och välj personer & grupper fliken för att se tillagda attributet avspeglas i domino Lägg till Person (under andra fliken).
12. Öppna Schema.nsf från **filer** fliken och se tillagda attributet återspeglas under TestSubform LDAP Auxiliary object-klassen.

**Metod 3: Lägga till det anpassade attributet i ExtensibleObject-klass**

1. Öppna {Schema.nsf} filen placeras i rotkatalogen
2. Välj LDAP objektklasser i den vänstra menyn under **alla schemadokument** och på **lägga till Object-klassen** knappen:
3. Ange LDAP-namn i formatet {zzzExtensibleSchema} (där zzz är namnet på standardklassen strukturella objekt, till exempel Person). Till exempel för att utöka schemat för Person object-klassen, ange LDAP-namnet {PersonExtensibleSchema}.
4. Ange klassnamnet för överordnade objekt, som du vill utöka schemat. Ange till exempel överordnad klass objektnamnet {dominoPerson} för att utöka schemat för Person object-klassen:
5. Ange ett giltigt OID motsvarar object-klassen.
6. Välj utökad/anpassade attribut under obligatoriska eller valfria attributtyper fält enligt krav:
7. När du lägger till attribut i ExtensibleObjectClass, klickar du på **spara och Stäng**.
8. En ExtensibleObjectClass skapas för respektive standard object-klassen med utökade attribut.

## <a name="troubleshooting"></a>Felsökning
* Information om hur du aktiverar loggning för att felsöka anslutningen finns i [hur du aktiverar ETW-spårning för kopplingar](http://go.microsoft.com/fwlink/?LinkId=335731).
