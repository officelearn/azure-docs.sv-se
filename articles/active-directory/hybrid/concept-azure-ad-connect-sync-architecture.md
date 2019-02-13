---
title: 'Azure AD Connect-synkronisering: Förstå arkitekturen | Microsoft Docs'
description: Det här avsnittet beskrivs arkitekturen för Azure AD Connect-synkronisering och de termer som används.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 465bcbe9-3bdd-4769-a8ca-f8905abf426d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60a648b24ad16c1b5ea404b5ad9f1f18fd5041da
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56193857"
---
# <a name="azure-ad-connect-sync-understanding-the-architecture"></a>Azure AD Connect-synkronisering: Förstå arkitekturen
Det här avsnittet innehåller grundläggande arkitektur för Azure AD Connect-synkronisering. Det är till stor del liknar föregående MIIS 2003 ILM 2007 och FIM 2010. Azure AD Connect-synkronisering är en utveckling av våra tekniker. Om du är bekant med någon av dessa tidigare tekniker kommer innehållet i det här avsnittet att du är van att också. Om du är nybörjare på synkronisering, är det här avsnittet för dig. Det är dock inte ett krav att känna till det här avsnittet för att utföra gör anpassningar i Azure AD Connect-synkronisering (kallas Synkroniseringsmotorn i det här avsnittet).

## <a name="architecture"></a>Arkitektur
Synkroniseringsmotorn skapar en integrerad vy av objekt som lagras i flera anslutna datakällor och hanterar identitetsinformation i dessa datakällor. Den här integrerad vy bestäms av identitetsinformationen hämtades från anslutna datakällor och en uppsättning regler som bestämmer hur du bearbetar den här informationen.

### <a name="connected-data-sources-and-connectors"></a>Anslutna datakällor och Anslutningsappar
Synkroniseringsmotorn bearbetar identitetsinformation från olika datalager, till exempel Active Directory eller en SQL Server-databas. Varje datalager som ordnar data i ett format som databasliknande och som ger standard dataåtkomst metoder är en potentiell data källan kandidat för Synkroniseringsmotorn. Datalager som synkroniseras av Synkroniseringsmotorn kallas **anslutna datakällor** eller **anslutna kataloger** (CD).

Synkroniseringsmotorn kapslar in interaktion med en ansluten datakälla i en modul som kallas en **Connector**. Varje typ av ansluten datakälla har en viss koppling. Anslutningen omvandlar en obligatorisk åtgärd i det format som den anslutna datakällan förstår.

Kopplingar gör API-anrop till exchange-ID-information (både läsning och skrivning) med en ansluten datakälla. Det är också möjligt att lägga till en anpassad Anslutningsapp med extensible connectivity-ramverket. Följande bild visar hur en anslutning ansluter en ansluten datakälla till Synkroniseringsmotorn.

![Ark1](./media/concept-azure-ad-connect-sync-architecture/arch1.png)

Data kan flöda i båda riktningarna, men det går inte att flöda i båda riktningarna samtidigt. Med andra ord en anslutning kan konfigureras så att data kan flöda från den anslutna datakällan till Synkroniseringsmotorn eller Synkroniseringsmotorn till den anslutna datakällan, men endast en av dessa åtgärder kan inträffa när som helst en för ett objekt och attribut. Riktningen kan vara olika för olika objekt och för olika attribut.

Om du vill konfigurera en anslutning, kan du ange de objekttyper som du vill synkronisera. Anger objekttyperna definierar vilka objekt som ingår i synkroniseringsprocessen. Nästa steg är att välja attribut som ska synkroniseras, som kallas en inkludering attributlistan. De här inställningarna kan ändras som helst som svar på ändringar till din affärsregler. När du använder installationsguiden för Azure AD Connect konfigureras de här inställningarna åt dig.

Om du vill exportera objekt till en ansluten datakälla, måste inkludering attributlistan innehålla minst de minsta attribut som krävs för att skapa en viss objekttyp i en ansluten datakälla. Till exempel den **sAMAccountName** attributet måste inkluderas i attributlistan för inkludering att exportera ett användarobjekt till Active Directory eftersom alla användarobjekt i Active Directory måste ha en **sAMAccountName** attribut som har definierats. Igen, gör installationsguiden av den här konfigurationen åt dig.

Om den anslutna datakällan använder strukturella komponenter, till exempel partitioner eller behållare för att organisera objekt, kan du begränsa de områden i den anslutna datakällan som används för den aktuella lösningen.

### <a name="internal-structure-of-the-sync-engine-namespace"></a>Interna strukturen hos motorn synkroniseringsnamnområde
Namnområdet hela sync-motorn består av två namnområdena som lagrar ID-information. De två namnområdena är:

* Anslutningsplatsen (CS)
* Metaversum (MV)

Den **anslutarplatsen** är ett mellanlagringsområde som innehåller framställningar av avsedda objekt från en ansluten datakälla och attribut som anges i attributlistan för inkludering. Synkroniseringsmotorn använder anslutningsplatsen att avgöra vad som har ändrats i den anslutna datakällan och att mellanlagra inkommande ändringar. Synkroniseringsmotorn använder också anslutarplatsen för att mellanlagra utgående ändringar för export till den anslutna datakällan. Synkroniseringsmotorn har en distinkt anslutarplats som ett mellanlagringsområde för varje anslutning.

Med hjälp av ett mellanlagringsområde Synkroniseringsmotorn förblir oberoende av anslutna datakällor och påverkas inte av tillgänglighet och hjälpmedel. Därför kan du bearbeta identitetsinformation när som helst med hjälp av data i mellanlagringsområdet. Synkroniseringsmotorn kan begära endast ändringar som görs i den anslutna datakällan sedan den senaste kommunikationssessionen avslutas eller push ut bara ändringarna i identitetsinformation som den anslutna datakällan inte har har tagits emot, vilket minskar nätverket trafik mellan Synkroniseringsmotorn och den anslutna datakällan.

Dessutom lagras Synkroniseringsmotorn statusinformation om alla objekt som den skapar etapper i anslutningsplatsen. När nya data tas emot, utvärderar Synkroniseringsmotorn alltid om data redan har synkroniserats.

Den **metaversum** är en lagringsplats som innehåller aggregerad ID-information från flera anslutna datakällor, vilket ger en enda global vy över alla kombinerade objekt. Metaversum-objekt skapas baserat på ID-information som hämtas från de anslutna datakällorna och en uppsättning regler som kan du anpassa synkroniseringsprocessen.

Följande bild visar connector utrymme namnområde och metaversum-namnområde i Synkroniseringsmotorn.

![Arch2](./media/concept-azure-ad-connect-sync-architecture/arch2.png)

## <a name="sync-engine-identity-objects"></a>Synkronisera motorn identitetsobjekt
Objekten i Synkroniseringsmotorn är representationer av antingen objekt i den anslutna datakällan eller integrerad vy som synkroniserar motor har med objekten. Varje motor synkroniseringsobjektet måste ha en globalt unik identifierare (GUID). GUID ger dataintegritet och uttryckliga relationer mellan objekt.

### <a name="connector-space-objects"></a>Anslutningsobjekt som utrymme
När Synkroniseringsmotorn kommunicerar med en ansluten datakälla, läser identitetsinformation i den anslutna datakällan och använder denna information för att skapa en representation av identitetsobjekt i anslutarplatsen. Du kan inte skapa eller ta bort de här objekten individuellt. Du kan manuellt ta bort alla objekt i en anslutarplats.

Alla objekt i anslutarplatsen har två attribut:

* En globalt unik identifierare (GUID)
* Ett unikt namn (DN)

Om den anslutna datakällan tilldelar ett unikt attribut till objektet, kan objekt i anslutarplatsen också att ha fästpunktsattribut. Fästpunktsattributet identifierar ett objekt i den anslutna datakällan. Synkroniseringsmotorn använder till ankaret för att hitta en motsvarande representation av det här objektet i den anslutna datakällan. Synkroniseringsmotorn förutsätter att förtroendeankare för ett objekt aldrig ändras under livslängden för objektet.

Många av kopplingarna kan använda en unik identifierare som är kända för att generera en fästpunkt automatiskt för varje objekt när den importeras. Till exempel Active Directory-kopplingen använder den **objectGUID** attribut för en fästpunkt. Du kan ange ankare generation som en del av Anslutningstjänstens konfiguration av anslutna datakällor som inte uppger ett tydligt definierade unika identifierare.

På så sätt att fallet till ankaret skapas från en eller fler unika attribut för ett objekt Skriv, varken i vilken ändringar och som unikt identifierar objekt i anslutarplatsen (till exempel en anställd tal eller ett användar-ID).

En anslutarplatsen som kan vara något av följande:

* Ett mellanlagrings-objekt
* En platshållare

### <a name="staging-objects"></a>Mellanlagring objekt
En mellanlagrings-objektet representerar en instans av de angivna objekt av typerna från den anslutna datakällan. Förutom GUID och det unika namnet har ett fristående objekt alltid ett värde som anger typen av objekt.

Mellanlagrings-objekt som har importerats alltid har ett värde för attributet ankare. Mellanlagrings-objekt som nyligen har etablerats med Synkroniseringsmotorn och håller på att skapas på den anslutna datakällan har inte ett värde för attributet ankare.

Mellanlagring objekt har också aktuella värden för företag-attribut och teknisk information som krävs av Synkroniseringsmotorn för att utföra synkroniseringen. Funktionsinformation innehåller flaggor som anger vilken typ av uppdateringar som mellanlagras mellanlagrings-objektet. Om en mellanlagrings-objektet har fått nya ID-information från den anslutna datakällan som ännu inte har behandlats, objektet flaggas som **väntande import**. Om ett mellanlagrings-objekt har nya identitetsinformation som ännu inte har exporterats till den anslutna datakällan, den flaggas som **väntande export**.

Ett mellanlagrings-objekt kan vara en import eller export objekt. Synkroniseringsmotorn skapar ett importobjekt med hjälp av Objektinformation togs emot från den anslutna datakällan. När Synkroniseringsmotorn får information om förekomsten av ett nytt objekt som matchar en av de objekttyper som valts i anslutningen, skapar den ett importobjekt i anslutarplatsen som en representation av objektet i den anslutna datakällan.

Följande bild visar ett importobjekt som representerar ett objekt i den anslutna datakällan.

![Arch3](./media/concept-azure-ad-connect-sync-architecture/arch3.png)

Synkroniseringsmotorn skapar en export-objekt med hjälp av objektinformation i metaversum. Exportera objekt exporteras till den anslutna datakällan under nästa kommunikation. Ur Synkroniseringsmotorn finns exportera objekt inte i den anslutna datakällan ännu. Därför är fästpunktsattributet för en export-objektet inte tillgänglig. När den tar emot objektet från Synkroniseringsmotorn skapar ett unikt värde för attributet ankare för objektet i den anslutna datakällan.

Följande bild visar hur en export-objekt skapas med hjälp av identitetsinformation i metaversum.

![Arch4](./media/concept-azure-ad-connect-sync-architecture/arch4.png)

Synkroniseringsmotorn bekräftar export av objektet av hur du importerar objekt från den anslutna datakällan igen. Exportera objekt bli importera objekt när Synkroniseringsmotorn tar emot dem vid nästa import från den anslutna datakällan.

### <a name="placeholders"></a>Platshållare
Synkroniseringsmotorn använder en ostrukturerad namnrymd för att lagra objekt. Men vissa anslutna datakällor, till exempel Active Directory och använda ett hierarkiskt namnområde. Om du vill omvandla information från ett hierarkiskt namnområde till en ostrukturerad namnrymd, använder Synkroniseringsmotorn platshållare för att behålla hierarkin.

Varje är en komponent (till exempel en organisationsenhet) i hierarkiskt för ett objekt som inte har importerats till Synkroniseringsmotorn men krävs för att konstruera hierarkiskt. De fyller luckor som skapats av referenser i den anslutna datakällan till objekt som inte mellanlagring objekt i anslutarplatsen.

Synkroniseringsmotorn använder också platshållare för att lagra refererade objekt som inte har importerats ännu. Exempel: om synkronisering har konfigurerats för att inkludera chefsattribut för den *Abbie Spencer* objektet och det mottagna värdet är ett objekt som inte har importerats ännu, till exempel *CN = Lee Sperry, CN = Users, DC = fabrikam, DC = com* , manager informationen lagras som platshållare i anslutarplatsen. Om objektet manager senare importeras skrivs objektet platshållaren över av det mellanlagrade objekt som representerar chefen.

### <a name="metaverse-objects"></a>Metaversum-objekt
En metaversumobjekt innehåller den sammanställda vy som Synkroniseringsmotorn har mellanlagrade objekt i anslutarplatsen. Synkroniseringsmotorn skapar metaversum-objekt med hjälp av informationen i Importera objekt. Flera anslutningsobjekt som utrymme kan länkas till en enda metaversumobjekt, men en anslutarplatsen kan inte länkas till mer än en metaversumobjekt.

Metaversum-objekt kan inte skapas eller tas bort manuellt. Synkroniseringsmotorn tar automatiskt bort metaversum-objekt som inte har en länk till alla anslutarplatsen i anslutarplatsen.

Om du vill mappa objekt i en ansluten datakälla till en motsvarande objekt i metaversum innehåller Synkroniseringsmotorn ett Utökningsbart schema med en fördefinierad uppsättning objekttyper och tillhörande attribut. Du kan skapa nya objekttyper och attribut för metaversum-objekt. Attribut kan ha enstaka eller flera värden och attributtyperna kan vara strängar, referenser, siffror och booleska värden.

### <a name="relationships-between-staging-objects-and-metaverse-objects"></a>Relationer mellan mellanlagring och metaversum-objekt
Dataflödet är aktiverat som länk relationen mellan mellanlagring och metaversum-objekt i motorn synkroniseringsnamnområde. Ett mellanlagrings-objekt som är länkad till ett metaversumobjekt kallas en **kopplade objekt** (eller **kopplingsobjektet**). Ett mellanlagrings-objekt som inte är länkad till ett metaversumobjekt kallas en **uppdelad objektet** (eller **disconnector objektet**). Villkoren ansluten och uppdelad rekommenderas inte blandas ihop med kopplingarna som ansvarar för att importera och exportera data från en ansluten katalog.

Platshållare är aldrig länkade till ett metaversumobjekt

En domänansluten objekt består av ett mellanlagrings-objekt och dess länkade relation till en enda metaversumobjekt. Domänansluten objekt används för att synkronisera attributvärden mellan en anslutarplatsen och ett metaversumobjekt.

När en mellanlagrings-objektet blir en domänansluten objektet under synkroniseringen, kan attribut flöda mellan mellanlagring objektet och metaversumobjekt. Attributflöde är dubbelriktad och konfigureras med hjälp av regler för import av attribut och attributet exportrestriktioner.

En enda anslutarplatsen kan länkas till endast en metaversumobjekt. Varje metaversumobjekt får vara kopplade till flera connector utrymme objekt i samma eller olika kopplingens utrymmen som visas i följande bild.

![Arch5](./media/concept-azure-ad-connect-sync-architecture/arch5.png)

Länkade relationen mellan mellanlagrings-objekt och en metaversumobjekt är permanent och kan tas bort bara av regler som du anger.

Ett skilda objekt är ett mellanlagrings-objekt som inte är länkad till någon metaversumobjekt. Attributet värden i en frånkopplad objektet inte är bearbeta några ytterligare i metaversum. Attributvärden för motsvarande objekt i den anslutna datakällan uppdateras inte med Synkroniseringsmotorn.

Genom att använda skilda objekt kan du lagra identitetsinformation i Synkroniseringsmotorn och bearbeta dem senare. Att hålla en mellanlagrings-objektet som en frånkopplad objekt i anslutarplatsen har många fördelar. Eftersom systemet har redan förberetts nödvändig information om det här objektet, behöver du inte skapa en representation av det här objektet igen vid nästa import från den anslutna datakällan. På så sätt kan Synkroniseringsmotorn har alltid en fullständig ögonblicksbild av den anslutna datakällan, även om det finns någon anslutning till den anslutna datakällan. Skilda objekt kan konverteras till kopplade objekt och vice versa, beroende på de regler som du anger.

Ett importobjekt skapas som en frånkopplad objekt. En export-objektet måste vara en domänansluten objekt. Logiken som system tillämpar den här regeln och tar bort alla export-objekt som inte är en domänansluten objekt.

## <a name="sync-engine-identity-management-process"></a>Synkronisera motorprocessen identity management
Process för hantering av identitet styr hur identitetsinformation uppdateras mellan olika anslutna datakällor. Identitetshantering som sker i tre processer:

* Importera
* Synkronisering
* Exportera

Under importen utvärderar Synkroniseringsmotorn inkommande identitetsinformation från en ansluten datakälla. När ändringar identifieras det antingen skapar nya mellanlagrade objekt eller uppdaterar befintliga mellanlagrade objekt i anslutarplatsen för synkronisering.

Under synkroniseringsprocessen Synkroniseringsmotorn uppdateras metaversum för att återspegla ändringarna som skett i anslutarplatsen samt anslutarplatsen för att återspegla ändringar som har inträffat i metaversum.

Under exporten skickar Synkroniseringsmotorn ändringar som mellanlagras på mellanlagring objekt och som är flaggade som väntande export.

I följande illustration visas där var och en av processerna som uppstår som identitet information flöden från en ansluten datakälla till en annan.

![Arch6](./media/concept-azure-ad-connect-sync-architecture/arch6.png)

### <a name="import-process"></a>Importen
Under importen utvärderar Synkroniseringsmotorn uppdateringar till ID-information. Synkroniseringsmotorn jämför identitetsinformation som togs emot från den anslutna datakällan med ID-information om ett mellanlagrings-objekt och avgör om det mellanlagrade objektet kräver uppdateringar. Om det är nödvändigt att uppdatera det mellanlagrade objektet med nya data, mellanlagrings-objektet har flaggats som väntar på import.

Av mellanlagring objekt i anslutarplatsen innan synkronisering, kan Synkroniseringsmotorn bearbeta endast ID-information som har ändrats. Den här processen ger följande fördelar:

* **Effektiv synkronisering**. Mängden data som bearbetas under synkroniseringen minimeras.
* **Effektiv omsynkroniseringen**. Du kan ändra hur Synkroniseringsmotorn bearbetar identitetsinformation utan återansluter Synkroniseringsmotorn till datakällan.
* **Möjlighet att förhandsgranska synkronisering**. Du kan förhandsgranska synkronisering för att kontrollera att dina antaganden om hanteringen identitet är korrekta.

För varje objekt som angetts i kopplingen försöker Synkroniseringsmotorn hitta en representation av objektet i anslutarplatsen för anslutningen. Synkroniseringsmotorn undersöker alla mellanlagrade objekt i anslutarplatsen och försöker hitta en motsvarande mellanlagrade objekt som har en matchande fästpunktsattributet. Om inga befintliga mellanlagrade objekt har en matchande fästpunktsattributet, försöker Synkroniseringsmotorn hitta motsvarande mellanlagrings-objekt med samma unika namn.

När Synkroniseringsmotorn hittar ett mellanlagrings-objekt som matchar av unika namn, men inte av förtroendeankare, händer särskilt följande:

* Om objektet finns i anslutarplatsen har inga fästpunkt, så Synkroniseringsmotorn tar bort det här objektet från anslutningsplatsen och markerar metaversumobjekt som den är länkad till som **försök etablering vid nästa synkronisering kör**. Sedan skapar den nya importera objekt.
* Om objektet finns i anslutarplatsen har en fästpunkt, förutsätter Synkroniseringsmotorn att det här objektet har antingen ändrats eller tagits bort i den anslutna katalogen. Det tilldelar ett tillfälligt, nytt unikt namn för anslutarplatsen så att den kan mellanlagra inkommande objektet. Det gamla objektet blir **tillfälliga**, redo för koppling för import av Byt namn på eller ta bort för att lösa problemet.

Om Synkroniseringsmotorn hittar ett mellanlagrings-objekt som motsvarar det angivna objektet i anslutningen, avgör vilka ändringar som ska tillämpas. Till exempel Synkroniseringsmotorn kan byta namn på eller ta bort objekt i den anslutna datakällan, eller den kan bara uppdatera objektets attributvärden.

Mellanlagring objekt med uppdaterade data är markerade som väntande import. Det finns olika typer av väntande import. Beroende på resultatet av importprocessen har ett fristående objekt i anslutarplatsen något av följande väntande import typer:

* **Ingen**. Det finns inga ändringar i alla attribut för mellanlagrings-objektet. Synkroniseringsmotorn flaggas inte den här typen som väntande import.
* **Lägg till**. Mellanlagringsplatser objektet är ett nytt importera objekt i anslutarplatsen. Synkroniseringsmotorn flaggar den här typen som väntande import för ytterligare bearbetning i metaversum.
* **Uppdatera**. Synkroniseringsmotorn söker efter ett motsvarande mellanlagrade objekt i anslutarplatsen och vilket den här typen som väntar på import så att uppdateringar till attributen kan bearbetas i metaversum. Uppdateringar med byta namn på objektet.
* **Ta bort**. Synkroniseringsmotorn söker efter ett motsvarande mellanlagrade objekt i anslutarplatsen och vilket den här typen som väntar på import så att det kopplade objektet kan tas bort.
* **Ta bort/Lägg till**. Synkroniseringsmotorn söker efter ett motsvarande mellanlagrade objekt i anslutarplatsen, men objekttyperna stämmer inte överens. I det här fallet en delete-Lägg till ändring av mellanlagras. En delete-Lägg till ändring av anger att Synkroniseringsmotorn att en fullständig omsynkronisering i det här objektet måste inträffa eftersom olika uppsättningar av regler som gäller för det här objektet när du objekttyp ändringar.

Det är möjligt att avsevärt minska mängden data som bearbetas under synkroniseringen eftersom gör det blir systemet för att bearbeta endast de objekt som har uppdaterat data genom att ställa in pågående importstatus för ett fristående objekt.

### <a name="synchronization-process"></a>Processen för synkronisering
Synkronisering består av två relaterade processer:

* Inkommande synkronisering när innehållet i metaversum uppdateras med hjälp av data i anslutningsplatsen.
* Utgående synkronisering, när innehållet i anslutarplatsen uppdateras med hjälp av data i metaversum.

Med hjälp av informationen i anslutarplatsen, skapas inkommande synkronisering i metaversum integrerad vy av de data som lagras i de anslutna datakällorna. Alla mellanlagrade objekt eller endast de som har en väntande importera information räknas samman, beroende på hur reglerna har konfigurerats.

Utgående synkronisering processen uppdateringar exportera objekt när metaversum objekt ändras.

Inkommande synkronisering skapar integrerad vy i metaversum för ID-information som tas emot från de anslutna datakällorna. Synkroniseringsmotorn kan bearbeta identitetsinformation när som helst genom att använda den senaste identitetsinformation som den har från den anslutna datakällan.

**Inkommande synkronisering**

Inkommande synkronisering innehåller följande processer:

* **Etablera** (kallas även **projektion** om det är viktigt att skilja mellan den här processen från utgående synkronisering etablering). Synkroniseringsmotorn skapar ett nytt metaversumobjekt baserat på ett fristående objekt och länkar dem. Etablera är en åtgärd på objektnivå.
* **Ansluta till**. Synkroniseringsmotorn länkar ett mellanlagrings-objekt till ett befintligt metaversumobjekt. En koppling är en åtgärd på objektnivå.
* **Importera attributflöde**. Synkroniseringsmotorn uppdaterar de attributvärden som kallas attributflöde för objektet i metaversum. Importera attributflöde är en åtgärd för attributet på servernivå som kräver en länk mellan ett fristående objekt och en metaversumobjekt.

Etablera är den enda process som skapar objekt i metaversum. Etablera påverkar endast importera objekt som är skilda objekt. Under etablera skapar Synkroniseringsmotorn ett metaversumobjekt som motsvarar objekttypen för objektet import och upprättar en länk mellan båda-objekten, vilket skapar en domänansluten objekt.

Join-processen upprättar även en länk mellan importera objekt och en metaversumobjekt. Skillnaden mellan anslutnings- och etablera är att processen kräver att importera objekt är kopplade till ett befintligt metaversumobjekt, där etablera processen skapar ett nytt metaversumobjekt.

Synkroniseringsmotorn försöker ansluta ett importobjekt till ett metaversumobjekt med hjälp av villkor som har angetts i konfigurationen för Synkroniseringsregeln.

Under etablera och join länkar Synkroniseringsmotorn en frånkopplad objekt till ett metaversumobjekt, vilket gör dem ansluten. När dessa objektnivå åtgärder har slutförts, kan Synkroniseringsmotorn uppdatera attributvärden för den associerade metaversumobjekt. Den här processen kallas attributflöde för import.

Importera attributflöde uppstår på alla importera objekt som innehåller nya data och har kopplats till ett metaversumobjekt.

**Utgående synkronisering**

Utgående synkronisering uppdateringar exportera objekt när en metaversumobjekt ändra men tas inte bort. Målet med utgående synkronisering är att utvärdera om ändringar i metaversum objekt kräver uppdateringar till mellanlagrade objekt i kopplingens utrymmen. I vissa fall kan ändringarna kan kräva att mellanlagring objekt i alla kopplingens utrymmen uppdateras. Mellanlagrings-objekt som ändras är flaggade som väntande export, vilket gör dem exportera objekt. Dessa exportalternativ objekt är senare push-installeras på den anslutna datakällan under exporten.

Utgående synkronisering har tre processer:

* **Etablering**
* **Avetablering**
* **Exportera attributflöde**

Etablering och borttagning är båda på objektnivå-åtgärder. Avetablering beror på etablering eftersom endast etablering kan upprätta den. Avetablering utlöses när etableringen tar bort länken mellan ett metaversumobjekt och en export-objekt.

Etablering utlöses alltid när ändringar tillämpas på objekt i metaversum. När ändringar görs till metaversum-objekt, utför Synkroniseringsmotorn någon av följande uppgifter som en del av etableringen:

* Skapa kopplade objekt, där en metaversumobjekt är länkad till en nyskapad export-objektet.
* Byt namn på en domänansluten objekt.
* Sedan en frånkoppling av länkar mellan en metaversumobjekt, organiserar objekt, skapa en frånkopplad objekt.

Om etablering kräver Synkroniseringsmotorn att skapa ett nytt anslutningsobjekt, mellanlagring objektet som metaversumobjekt är länkad är alltid en export-objekt, eftersom objektet inte finns ännu i den anslutna datakällan.

Om etablering av kräver Synkroniseringsmotorn till frånkoppling en domänansluten objekt som skapar ett objekt för skilda utlöses avetablering. Avetableringsprocessen tar bort objektet.

Under avetableringen bort tar bort ett objekt för export inte fysiskt objektet. Objektet flaggas som **bort**, vilket innebär att borttagningen mellanlagras på objektet.

Det inträffar också exportattributflödet under utgående synkroniseringsprocessen liknande sätt att importera attributflöde sker under inkommande synkronisering. Exportattributflödet sker bara mellan metaversum och export av objekt som är anslutna.

### <a name="export-process"></a>Exportprocess
Under exportprocessen undersöker Synkroniseringsmotorn alla export-objekt som är flaggade som väntande export i anslutningsplatsen och skickar sedan uppdateringar till den anslutna datakällan.

Synkroniseringsmotorn kan avgöra en export men tillräckligt kan bestämma inte att identity management-processen är klar. Objekt i den anslutna datakällan kan alltid ändras av andra processer. Synkroniseringsmotorn har inte en beständig anslutning till den anslutna datakällan, räcker det inte att göra antaganden om egenskaperna för ett objekt i den anslutna datakällan som endast baseras på ett meddelande för lyckade exporten.

En process i den anslutna datakällan kan till exempel ändra objektets attribut till deras ursprungliga värden (det vill säga den anslutna datakällan kan skriva över värdena omedelbart efter det att data skickas av Synkroniseringsmotorn och tillämpats i den anslutna datakällan).

Synkronisera motorn butiker exportera och importera statusinformation om varje mellanlagrade objekt. Om värdena för attribut som anges i attributlistan för inkludering har ändrats sedan den senaste exporten, lagring av importera och exportera status kan Synkroniseringsmotorn för att reagera på lämpligt sätt. Synkroniseringsmotorn använder importen för att bekräfta attributvärden som har exporterats till den anslutna datakällan. En jämförelse mellan den importerade och exporterade informationen kan som visas i följande bild, Synkroniseringsmotorn att fastställa om exporten lyckades eller om den behöver upprepas.

![Arch7](./media/concept-azure-ad-connect-sync-architecture/arch7.png)

Till exempel om Synkroniseringsmotorn exporterar attributet C, som har värdet 5, till en ansluten datakälla, lagrar C = 5 i minnet export status. Varje ytterligare export på det här objektet resulterar i ett försök att exportera C = 5 till den anslutna datakällan igen eftersom Synkroniseringsmotorn förutsätter att det här värdet inte har ett beständigt sätt tillämpats till objektet (det vill säga om inte ett annat värde har nyligen har importerats från den anslutna datakällan). Export-minne rensas när C = 5 tas emot under en import för objektet.

## <a name="next-steps"></a>Nästa steg
Läs mer om den [Azure AD Connect-synkronisering](how-to-connect-sync-whatis.md) konfiguration.

Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).

