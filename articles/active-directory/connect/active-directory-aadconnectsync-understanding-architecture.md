---
title: "Azure AD Connect-synkronisering: Förstå arkitekturen | Microsoft Docs"
description: "Det här avsnittet beskrivs arkitekturen i Azure AD Connect-synkronisering och beskrivs de termer som används."
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: 465bcbe9-3bdd-4769-a8ca-f8905abf426d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: 51082ad453d53f56f30f814b78578801c00f4827
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-connect-sync-understanding-the-architecture"></a>Azure AD Connect-synkronisering: Förstå arkitekturen
Det här avsnittet beskriver de grundläggande arkitekturen för Azure AD Connect-synkronisering. Det är många aspekter liknar föregående MIIS 2003 ILM 2007 och FIM 2010. Azure AD Connect-synkronisering är utvecklingen av dessa tekniker. Om du är bekant med någon av dessa tidigare tekniker kommer innehållet i det här avsnittet att bekanta dig även. Om du är nybörjare på synkronisering, är det här avsnittet för dig. Det är emellertid inte ett krav att känna till det här avsnittet ska lyckas i gör anpassningar i Azure AD Connect-synkronisering (kallas Synkroniseringsmotorn i det här avsnittet).

## <a name="architecture"></a>Arkitektur
Synkroniseringsmotorn skapar en integrerad vy av objekt som lagras i flera anslutna datakällor och hanterar identitetsinformation i dessa datakällor. Den här integrerade vyn bestäms av identitetsinformationen hämtades från anslutna datakällor och en uppsättning regler som bestämmer hur att bearbeta den här informationen.

### <a name="connected-data-sources-and-connectors"></a>Anslutna datakällor och kopplingar
Synkroniseringsmotorn bearbetar identitetsinformation från olika databaser, till exempel Active Directory eller en SQL Server-databas. Alla data databaser som organiserar data i en databas som format och som ger standard dataåtkomst metoder är en potentiell datakälla kandidat för Synkroniseringsmotorn. Data-databaser som synkroniseras av Synkroniseringsmotorn kallas **anslutna datakällor** eller **anslutna kataloger** (CD).

Synkroniseringsmotorn kapslar in interaktion med en ansluten datakälla i en modul som kallas en **Connector**. Varje typ av anslutna datakällan har en viss koppling. Kopplingen omvandlar en obligatorisk åtgärd i det format som kan användas med den anslutna datakällan.

Kopplingar gör API-anrop för att utbyta identitetsinformation (både läsning och skrivning) med en ansluten datakälla. Det är också möjligt att lägga till en anpassad koppling med hjälp av extensible connectivity framework. Följande bild visar hur en koppling ansluter en ansluten datakälla till Synkroniseringsmotorn.

![Ark1](./media/active-directory-aadconnectsync-understanding-architecture/arch1.png)

Data kan flöda i båda riktningarna, men det går inte att flöda i båda riktningarna samtidigt. Med andra ord en anslutning kan konfigureras så att data kan flöda från den anslutna datakällan till Synkroniseringsmotorn eller Synkroniseringsmotorn till den anslutna datakällan, men endast en av dessa åtgärder kan ske när som helst för ett objekt och attribut. Riktning kan vara olika för olika objekt och för olika attribut.

Om du vill konfigurera en koppling, kan du ange de objekttyper som du vill synkronisera. Ange typerna av objekt definierar vilka objekt som ingår i synkroniseringsprocessen. Nästa steg är att välja de attribut du vill synkronisera, som kallas en attributet inkluderingslistan. De här inställningarna kan ändras varje gång som svar på ändringar till affärsregler. Dessa inställningar konfigureras för dig när du använder Azure AD Connect-installationsguiden.

Om du vill exportera objekt till en ansluten datakälla måste inkluderingslistan attributet innehålla minst de minsta attribut som krävs för att skapa en viss objekttyp i en ansluten datakälla. Till exempel den **sAMAccountName** attributet måste inkluderas i inkluderingslistan attribut för att exportera ett användarobjekt till Active Directory eftersom alla användarobjekt i Active Directory måste ha en **sAMAccountName** attribut. Installationsguiden har igen och den här konfigurationen åt dig.

Om den anslutna datakällan använder strukturella komponenter, till exempel partitioner eller behållare för att ordna objekt, kan du begränsa de områden i den anslutna datakällan som används för en viss lösning.

### <a name="internal-structure-of-the-sync-engine-namespace"></a>Intern struktur för namnområdet som motorn synkronisering
Synkronisering av hela motorn namnområde består av två namnrymder som lagrar identitetsinformation. De två namnområden är:

* Anslutningsplatsen (CS)
* Metaversum (MV)

Den **anslutarplats** är ett mellanlagringsområde som innehåller framställningar av avsedda objekt från en ansluten datakälla och attribut som anges i attributet inkluderingslistan. Synkroniseringsmotorn använder anslutningsplatsen att avgöra vad som har ändrats i den anslutna datakällan och att mellanlagra inkommande ändringar. Synkroniseringsmotorn använder också anslutningsplatsen för att mellanlagra utgående ändringar för export till den anslutna datakällan. Synkroniseringsmotorn har distinkta anslutningsplatsen som ett mellanlagringsområde för varje koppling.

Med hjälp av ett mellanlagringsområde Synkroniseringsmotorn förblir oberoende av anslutna datakällor och påverkas inte av deras tillgänglighet och tillgänglighet. Du kan därför bearbeta identitetsinformation när som helst med hjälp av data i mellanlagringsområdet. Synkroniseringsmotorn kan begära endast ändringar som görs i den anslutna datakällan sedan den senaste kommunikationssessionen avslutas eller push ut bara ändringarna i identitetsinformation som den anslutna datakällan inte har tagits emot ännu, vilket minskar nätverkets trafik mellan Synkroniseringsmotorn och den anslutna datakällan.

Dessutom lagras Synkroniseringsmotorn statusinformation om alla objekt som den skapar etapper i anslutningsplatsen. När nya data tas emot, utvärderar Synkroniseringsmotorn alltid om data redan har synkroniserats.

Den **metaversum** är en lagringsplats som innehåller aggregerade ID-information från flera anslutna datakällor, vilket ger en enda global vy över alla kombinerade objekt. Metaversum-objekt skapas baserat på ID-information som hämtas från anslutna datakällor och en uppsättning regler som kan du anpassa synkroniseringsprocessen.

Följande bild visar connector utrymme namnområde och metaversum-namnområde inom Synkroniseringsmotorn.

![Arch2](./media/active-directory-aadconnectsync-understanding-architecture/arch2.png)

## <a name="sync-engine-identity-objects"></a>Synkronisera motorn identitetsobjekt
Objekt i Synkroniseringsmotorn framställningar av antingen objekt i den anslutna datakällan eller integrerad vyn som synkroniserar motorn har av dessa objekt. Varje synkronisering motorobjekt måste ha en globalt unik identifierare (GUID). GUID ger dataintegritet och snabb förhållanden mellan objekt.

### <a name="connector-space-objects"></a>Kopplingen utrymme objekt
När Synkroniseringsmotorn kommunicerar med en ansluten datakälla, läser identitetsinformation i den anslutna datakällan och använder informationen för att skapa en representation av objektet identitet i anslutningsplatsen. Du kan inte skapa eller ta bort de här objekten individuellt. Du kan manuellt ta bort alla objekt i en anslutningsplatsen.

Alla objekt i anslutningsplatsen har två attribut:

* En globalt unik identifierare (GUID)
* Ett unikt namn (DN)

Om den anslutna datakällan tilldelar ett unikt attribut till objektet, kan objekt i anslutningsplatsen också att ha fästpunktsattribut. Fästpunktsattributet identifierar ett objekt i den anslutna datakällan. Synkroniseringsmotorn använder ankaret för att leta upp motsvarande representation av det här objektet i den anslutna datakällan. Synkroniseringsmotorn förutsätter att ankarpunkten för ett objekt aldrig ändras under livslängden för objektet.

Många av kopplingarna använder en känd Unik identifierare för att generera ett ankare automatiskt för varje objekt när den har importerats. Till exempel Active Directory-kopplingen använder den **objectGUID** attribut för en fästpunkt. Du kan ange fästpunkt generation som en del av kopplingens konfiguration av anslutna datakällor som inte tillhandahåller ett tydligt definierade unika identifierare.

Då fallet ankaret skapas från en eller flera attribut för ett objekt som unikt skriver, varken vilka ändringar och som unikt identifierar objektet i anslutningsplatsen (till exempel en anställd tal eller ett användar-ID).

Ett utrymme connector-objekt kan vara något av följande:

* Ett fristående objekt
* En platshållare

### <a name="staging-objects"></a>Mellanlagring objekt
Ett fristående objekt representerar en instans av de avsedda objekttyperna från den anslutna datakällan. Förutom GUID och det unika namnet har alltid ett värde som anger typ av objekt i ett fristående objekt.

Mellanlagring objekt som har importerats alltid har ett värde för fästpunktsattributet. Mellanlagring objekt som nyligen har etablerats med Synkroniseringsmotorn och håller på att skapas i den anslutna datakällan har inte ett värde för fästpunktsattributet.

Objekt för Förproduktion utför också aktuella värden för företag-attribut och teknisk information som krävs av Synkroniseringsmotorn för att genomföra synkroniseringen. Funktionsinformation innehåller flaggor som anger vilken typ av uppdateringar som mellanlagras på fristående objekt. Om ett fristående objekt har fått ny ID-information från den anslutna datakällan som ännu inte bearbetats, objektet har flaggats som **väntande import**. Om ett fristående objekt har nya identitetsinformation som ännu inte har exporterats till den anslutna datakällan, den är markerad som **väntande export**.

Ett fristående objekt kan vara en importera eller exportera objekt. Synkroniseringsmotorn skapar ett importobjekt med hjälp av Objektinformation från den anslutna datakällan. När Synkroniseringsmotorn tar emot information om finns ett nytt objekt som matchar ett av de typer av objekt som valts i kopplingen, skapar en importobjekt i anslutningsplatsen som en representation av objektet i den anslutna datakällan.

Följande bild visar ett importobjekt som representerar ett objekt i den anslutna datakällan.

![Arch3](./media/active-directory-aadconnectsync-understanding-architecture/arch3.png)

Synkroniseringsmotorn skapar en export-objekt med hjälp av information om objekt i metaversumet. Exportera objekt exporteras till den anslutna datakällan under nästa kommunikation. Ur ett Synkroniseringsmotorn finns exportera objekt inte i den anslutna datakällan ännu. Därför är fästpunktsattributet för en export-objektet inte tillgänglig. När den tar emot objektet från Synkroniseringsmotorn skapar ett unikt värde för fästpunktsattributet för objektet i den anslutna datakällan.

Följande bild visar hur en export-objektet har skapats med hjälp av identitetsinformation i metaversum.

![Arch4](./media/active-directory-aadconnectsync-understanding-architecture/arch4.png)

Synkroniseringsmotorn bekräftar export av objektet genom att importera objekt från den anslutna datakällan. Exportera objekt blir importera objekt när Synkroniseringsmotorn tar emot dem under nästa import från den anslutna datakällan.

### <a name="placeholders"></a>Platshållare
Synkroniseringsmotorn använder en platt namnrymd för att lagra objekt. Men vissa anslutna datakällor, till exempel Active Directory och använda ett hierarkiskt namnområde. För att omvandla information från ett hierarkiskt namnområde i en platt namnområde, använder Synkroniseringsmotorn platshållare för att behålla hierarkin.

Varje platshållare representerar en komponent (till exempel en organisationsenhet) i en hierarkisk objektnamn som inte har importerats till Synkroniseringsmotorn men krävs för att konstruera det hierarkiska namnet. De fyller luckor som skapats av referenser i den anslutna datakällan till objekt som inte mellanlagring objekt i anslutningsplatsen.

Synkroniseringsmotorn använder också platshållare för att lagra refererade objekt som ännu inte importerats. Om synkronisering är konfigurerad för att inkludera attributet manager för till exempel den *Abbie Spencer* objekt och det mottagna värdet är ett objekt som inte har importerats ännu, t.ex *CN Lee Sperry, CN = = Users, DC = fabrikam, DC = com* , manager informationen lagras som platshållare i anslutningsplatsen. Om objektet manager senare importeras skrivs platshållarobjekt över av det mellanlagrade objekt som representerar hanteraren.

### <a name="metaverse-objects"></a>Metaversum-objekt
En metaversumobjekt innehåller aggregerade vyn att Synkroniseringsmotorn har mellanlagrade objekt i anslutningsplatsen. Synkroniseringsmotorn skapar metaversum-objekt med hjälp av informationen i Importera objekt. Flera connector utrymme objekt kan länkas till en enda metaversumobjekt, men ett utrymme connector-objekt kan inte länkas till fler än ett metaversumobjekt.

Metaversum-objekt kan inte skapas eller tas bort manuellt. Synkroniseringsmotorn tar automatiskt bort metaversum-objekt som inte har en länk till koppling utrymme objekt i anslutningsplatsen.

Om du vill mappa objekt inom en ansluten datakälla till en motsvarande objekttyp i metaversum ger Synkroniseringsmotorn ett Utökningsbart schema med en fördefinierad uppsättning objekttyper och motsvarande attribut. Du kan skapa nya objekttyper och attribut för metaversum-objekt. Attribut kan vara eller flera värden och attributtyperna kan vara strängar, referenser, siffror och booleska värden.

### <a name="relationships-between-staging-objects-and-metaverse-objects"></a>Relationer mellan fristående och metaversum-objekt
Inom namnområdet sync motorn aktiveras dataflödet som länk relationen mellan fristående och metaversum-objekt. Ett fristående objekt som är kopplad till ett metaversumobjekt kallas en **kopplade objekt** (eller **kopplingsobjektet**). Ett fristående objekt som inte är kopplad till ett metaversumobjekt kallas en **degraderingsprocessen objektet** (eller **disconnector objektet**). Villkoren ansluten och degraderingsprocessen är att föredra framför inte förväxlas med kopplingar ansvarar för att importera och exportera data från en ansluten katalog.

Platshållare är aldrig länkade till ett metaversumobjekt

En domänansluten objektet består av ett fristående objekt och dess länkade relation till en enda metaversumobjekt. Kopplade objekt används för att synkronisera attributvärden mellan en koppling utrymme objekt och metaversum-objekt.

När ett fristående objekt blir en domänansluten objektet under synkroniseringen, kan attribut flöda mellan fristående objektet och metaversum-objekt. Attributflöde är dubbelriktad och konfigureras med hjälp av attributet importregler och exportera attributregler.

En enskild koppling utrymme objekt kan länkas till endast en metaversumobjekt. Varje metaversumobjekt kan dock länkas till flera connector utrymme objekt i samma eller olika kopplingens utrymmen som visas i följande bild.

![Arch5](./media/active-directory-aadconnectsync-understanding-architecture/arch5.png)

Länkade relationen mellan mellanlagrade objekt och ett metaversumobjekt är permanent och kan tas bort bara av regler som du anger.

En frånkopplad objektet är ett fristående objekt som inte är länkad till någon metaversumobjekt. Attributet värden i en frånkopplad objektet inte är bearbeta några ytterligare i metaversum. Attributvärden för motsvarande objekt i den anslutna datakällan uppdateras inte av Synkroniseringsmotorn.

Du kan lagra identitetsinformation i Synkroniseringsmotorn och bearbetar den senare genom att använda skilda objekt. Som ett fristående objekt som en frånkopplad objekt i anslutningsplatsen har många fördelar. Eftersom systemet har redan mellanlagrat nödvändig information om det här objektet, behöver du inte skapa en representation av det här objektet igen under nästa importen från den anslutna datakällan. På så sätt kan Synkroniseringsmotorn har alltid en fullständig ögonblicksbild av den anslutna datakällan, även om det inte finns någon anslutning till den anslutna datakällan. Frånkopplad objekt kan konverteras till domänanslutna objekt och omvänt, beroende på de regler som du anger.

Ett importobjekt skapas som en frånkopplad objekt. En export-objektet måste vara en domänansluten objekt. Systemlogik tillämpar regeln och tar bort alla export-objekt som inte är domänansluten objekt.

## <a name="sync-engine-identity-management-process"></a>Motorn identity management-synkroniseringen
Process för hantering av identiteter styr hur identitetsinformation uppdateras mellan olika anslutna datakällor. Identitetshantering sker i tre processer:

* Importera
* Synkronisering
* Exportera

Under importen utvärderar Synkroniseringsmotorn inkommande identitetsinformation från en ansluten datakälla. När ändringar identifieras den antingen skapar nya mellanlagrade objekt eller uppdaterar befintliga mellanlagrade objekt i anslutningsplatsen för synkronisering.

Under synkroniseringen Synkroniseringsmotorn uppdateras för att återspegla ändringarna som skett i anslutningsplatsen metaversum samt anslutningsplatsen för att återspegla ändringarna som skett i metaversum.

Under exporten skickar Synkroniseringsmotorn ändringar som mellanlagras på mellanlagring objekt och som är flaggade som väntande export.

I följande illustration visas där alla processer som används som identitet information flödar från en ansluten datakälla till en annan.

![Arch6](./media/active-directory-aadconnectsync-understanding-architecture/arch6.png)

### <a name="import-process"></a>Importen
Under importen utvärderar Synkroniseringsmotorn uppdateringar till identitetsinformation. Synkroniseringsmotorn jämför identitetsinformation togs emot från den anslutna datakällan med ID-information om ett fristående objekt och anger om uppdateringar krävs för fristående objekt. Om det är nödvändigt att uppdatera objektet mellanlagring med nya data flaggas mellanlagrade objekt som väntande import.

Synkroniseringsmotorn kan bearbeta den identitetsinformation som har ändrats av mellanlagring objekt i anslutningsplatsen innan synkroniseringen. Den här processen ger följande fördelar:

* **Effektiv synkronisering**. Mängden data som bearbetats under synkronisering minimeras.
* **Effektiv omsynkroniseringen**. Du kan ändra hur Synkroniseringsmotorn bearbetar identitetsinformation utan återansluta Synkroniseringsmotorn till datakällan.
* **Möjlighet att förhandsgranska synkronisering**. Du kan förhandsgranska synkronisering för att kontrollera att din antaganden om hanteringen identitet är korrekta.

För varje objekt som angetts för kopplingen försöker Synkroniseringsmotorn hitta en representation av objektet i anslutningsplatsen av anslutningen. Synkroniseringsmotorn undersöker alla mellanlagrade objekt i anslutningsplatsen och försöker hitta ett motsvarande mellanlagrade objekt som har en matchande fästpunktsattributet. Om inga befintliga mellanlagrade objekt har en matchande fästpunktsattributet, försöker Synkroniseringsmotorn hitta ett motsvarande mellanlagrade objekt med samma unika namn.

När Synkroniseringsmotorn hittar ett fristående objekt som matchar med unika namn, men inte genom fästpunkt, händer särskilt följande:

* Om objektet finns i anslutningsplatsen har inga fästpunkt sedan Synkroniseringsmotorn tar bort objektet från anslutningsplatsen och markerar metaversum-objekt som är länkad till som **försök etablering på nästa synkronisering kör**. Sedan skapar den nya importera objekt.
* Om objektet finns i anslutningsplatsen har ankare, förutsätter Synkroniseringsmotorn att det här objektet har antingen ändrats eller tagits bort i ansluten katalog. Tilldelar ett tillfälligt, nya unikt namn för objektet connector utrymme så att den kan mellanlagra inkommande objektet. Det gamla objektet blir **tillfälligt**, väntar på att kopplingen för att importera Byt namn på eller ta bort för att lösa problemet.

Om Synkroniseringsmotorn hittar ett fristående objekt som motsvarar det angivna objektet i kopplingen, anger vilken typ av ändringarna ska tillämpas. Till exempel Synkroniseringsmotorn kan byta namn på eller ta bort objekt i den anslutna datakällan eller det kan bara uppdatera objektets attributvärden.

Mellanlagring objekt med uppdaterade data är markerade som väntande import. Det finns olika typer av väntande import. Beroende på resultatet av importen har ett fristående objekt i anslutningsplatsen något av följande väntande import typer:

* **Ingen**. Det finns inga ändringar i alla attribut för fristående objekt. Synkroniseringsmotorn flaggas inte den här typen som väntande import.
* **Lägg till**. Fristående objektet är ett nytt importera objekt i anslutningsplatsen. Synkroniseringsmotorn flaggor den här typen som väntande import för ytterligare bearbetning i metaversum.
* **Uppdatera**. Synkroniseringsmotorn söker efter ett motsvarande mellanlagrade objekt i anslutningsplatsen och flaggor den här typen som väntande import så att uppdateringar till attributen kan bearbetas i metaversum. Byta namn på objekt ingår.
* **Ta bort**. Synkroniseringsmotorn söker efter ett motsvarande mellanlagrade objekt i anslutningsplatsen och flaggor den här typen som väntande import så att det kopplade objektet kan tas bort.
* **Ta bort/Lägg till**. Synkroniseringsmotorn söker efter ett motsvarande mellanlagrade objekt i anslutningsplatsen, men matchar inte typerna av objekt. I det här fallet en delete-Lägg till ändring mellanlagras. En delete-Lägg till ändring anger att Synkroniseringsmotorn att en fullständig synkronisering av det här objektet måste inträffa eftersom olika uppsättningar med regler som gäller för det här objektet när du objekttyp ändringar.

Det är möjligt att avsevärt minska mängden data som bearbetas under synkroniseringen eftersom gör det blir systemet för att bearbeta endast de objekt som har uppdaterat data genom att ange importstatus väntande av ett fristående objekt.

### <a name="synchronization-process"></a>Processen för synkronisering
Synkronisering består av två relaterade processer:

* Inkommande synkronisering när innehållet i metaversum uppdateras med hjälp av data i anslutningsplatsen.
* Utgående synkronisering när innehållet i anslutningsplatsen uppdateras med hjälp av data i metaversum.

Med hjälp av informationen som mellanlagras i anslutningsplatsen skapas den inkommande synkronisering i metaversum integrerad vy över de data som lagras i de anslutna datakällorna. Alla mellanlagrade objekt eller endast de som en väntande importera information aggregeras, beroende på hur reglerna har konfigurerats.

Utgående synkronisering processen uppdateringar exportera objekt när metaversum objekt ändras.

Inkommande synkronisering skapar vyn integrerad i metaversum identitet information som tas emot från anslutna datakällor. Synkroniseringsmotorn kan bearbeta identitetsinformation när som helst genom att använda den senaste informationen för identitet som den har från den anslutna datakällan.

**Inkommande synkronisering**

Inkommande synkronisering innehåller följande processer:

* **Etablera** (kallas även **projektion** om det är viktigt att skilja den här processen från utgående synkronisering etablering). Synkroniseringsmotorn länkar dem skapas ett nytt metaversumobjekt baserat på ett fristående objekt. Etablera är en åtgärd på objektnivå.
* **Ansluta till**. Synkroniseringsmotorn länkar ett fristående objekt till ett befintligt metaversumobjekt. En koppling är en åtgärd på objektnivå.
* **Importera attributflöde**. Synkroniseringsmotorn uppdaterar attributvärden och kallas attributflöde för objektet i metaversum. Importera attributflöde är ett attribut på objektnivå åtgärd som kräver en länk mellan ett fristående objekt och metaversum-objekt.

Etablera är den enda process som skapar objekt i metaversumet. Etablera påverkar endast importera objekt som är skilda objekt. Under etablera skapar Synkroniseringsmotorn ett metaversumobjekt som motsvarar objekttypen för objektet import och upprättar en länk mellan både objekt, vilket skapar ett objekt som är kopplade.

Join-processen upprättar även en länk mellan importera objekt och metaversum-objekt. Skillnaden mellan koppling och etablera är att ansluta till processen kräver att importera objekt är kopplade till en befintlig metaversumobjekt där etablera process skapar ett nytt metaversumobjekt.

Synkroniseringsmotorn försöker ansluta ett importera objekt till ett metaversumobjekt med hjälp av villkor som angetts i konfigurationen för Synkroniseringsregeln.

Under etablera och koppling länkar Synkroniseringsmotorn skilda objekt till ett metaversumobjekt gör dem ansluten. När dessa åtgärder på objektnivå har slutförts, kan Synkroniseringsmotorn uppdatera attributvärden för den associerade metaversumobjekt. Den här processen kallas attributflöde för import.

Importera attributflöde uppstår på alla importera objekt som innehåller nya data och kopplas till ett metaversumobjekt.

**Utgående synkronisering**

Utgående synkronisering uppdateringar exportera objekt när en metaversumobjekt ändra men tas inte bort. Syftet med utgående synkronisering är att utvärdera om ändringar i metaversum objekt kräver uppdateringar för fristående objekt i kopplingens utrymmen. I vissa fall kan ändringarna kan kräva att mellanlagring objekt i alla kopplingens utrymmen uppdateras. Mellanlagring objekt som har ändrats flaggas som väntande export, gör dem exportera objekt. Dessa exportalternativ objekt senare push-installeras på den anslutna datakällan under exporten.

Utgående synkronisering har tre processer:

* **Etablering**
* **Avetablering**
* **Exportera attributflöde**

Etablering och borttagning finns både på objektnivå-operationer. Avetablering beror på etablering eftersom endast etablering kan starta den. Avetablering utlöses när etablering tar bort länken mellan ett metaversumobjekt och en export-objekt.

Etablering utlöses alltid när ändringar tillämpas på objekt i metaversumet. När ändringar görs metaversum-objekt, utför Synkroniseringsmotorn någon av följande uppgifter som en del av etableringsprocessen:

* Skapa kopplade objekt, där en metaversumobjekt är länkad till en nyligen skapade export-objektet.
* Byt namn på en domänansluten objekt.
* Sedan en frånkoppling av länkar mellan en metaversumobjekt och organisering objekt, skapa en frånkopplad objekt.

Om etablering kräver Synkroniseringsmotorn för att skapa ett nytt connector-objekt, är fristående objektet som är länkad metaversum-objekt alltid ett export-objekt eftersom objektet inte finns ännu i den anslutna datakällan.

Om etablering kräver Synkroniseringsmotorn till sedan en frånkoppling av ett kopplade objekt, skapar en frånkopplad objekt utlöses avetablering. Avetableringsprocessen tar bort objektet.

Under avetablering bort tar bort en export-objektet inte fysiskt objektet. Objektet har flaggats som **bort**, vilket innebär att borttagningen mellanlagras på objektet.

Exportera attributflöde inträffar också under utgående synkroniseringen, liknande sätt att importera attributflöde uppstår under inkommande synkronisering. Exportera attributflöde sker bara mellan metaversum och exportera objekt som är anslutna.

### <a name="export-process"></a>Exportprocess
Under exportprocessen undersöker Synkroniseringsmotorn alla export-objekt som är flaggade som väntande export i anslutningsplatsen och skickar sedan uppdateringarna till den anslutna datakällan.

Synkroniseringsmotorn kan avgöra hur framgångsrik en export men tillräckligt bestämma inte att identity management-processen har slutförts. Objekt i den anslutna datakällan kan alltid ändras av andra processer. Eftersom Synkroniseringsmotorn inte har en beständig anslutning till den anslutna datakällan, räcker det inte att göra antaganden om egenskaperna för ett objekt i den anslutna datakällan endast baseras på ett meddelande om lyckade exporten.

En process i den anslutna datakällan kan till exempel ändra objektets attribut tillbaka till sina ursprungliga värden (det vill säga den anslutna datakällan kan du skriva över värdena omedelbart efter att data skickas av Synkroniseringsmotorn och tillämpats i den anslutna datakällan).

Synkronisera motorn lagrar exportera och importera statusinformation om varje fristående objekt. Om du värdena för attribut som anges i attributet inkluderingslistan har ändrats sedan den senaste exporten, lagring av importera och exportera status aktiverar Synkroniseringsmotorn för att reagera på lämpligt sätt. Synkroniseringsmotorn använder importen för att bekräfta attributvärden som har exporterats till den anslutna datakällan. En jämförelse mellan den importerade och exporterade informationen kan som visas i följande illustration Synkroniseringsmotorn att avgöra om exporten har slutförts eller om den behöver upprepas.

![Arch7](./media/active-directory-aadconnectsync-understanding-architecture/arch7.png)

Till exempel om Synkroniseringsmotorn exporterar attributet C, som har värdet 5, till en ansluten datakälla, lagrar C = 5 i minnet export status. Varje ytterligare export på det här objektet resulterar i ett försök att exportera C = 5 till den anslutna datakällan igen eftersom Synkroniseringsmotorn förutsätter att det här värdet inte har beständigt tillämpats till objekt (det vill säga om ett annat värde har importerats nyligen från den ansluten datakälla). Export-minne rensas när C = 5 tas emot vid en import på objektet.

## <a name="next-steps"></a>Nästa steg
Lär dig mer om den [Azure AD Connect-synkronisering](active-directory-aadconnectsync-whatis.md) konfiguration.

Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md).

