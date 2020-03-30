---
title: 'Azure AD Connect-synkronisering: Förstå arkitekturen - Azure'
description: I det här avsnittet beskrivs arkitekturen för Azure AD Connect-synkronisering och de termer som används.
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
ms.openlocfilehash: fac0f9143918d3f273812e53abfb88d6a56f7a71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261624"
---
# <a name="azure-ad-connect-sync-understanding-the-architecture"></a>Synkronisering av Azure AD Connect: Förstå arkitekturen
Det här avsnittet beskriver den grundläggande arkitekturen för Azure AD Connect-synkronisering. I många avseenden liknar det sina föregångare MIIS 2003, ILM 2007 och FIM 2010. Azure AD Connect-synkronisering är utvecklingen av dessa tekniker. Om du är bekant med någon av dessa tidigare tekniker, innehållet i detta ämne kommer att vara bekant för dig också. Om du är ny i synkronisering, då det här avsnittet är för dig. Det är dock inte ett krav att känna till detaljerna i det här avsnittet för att lyckas med att göra anpassningar till Azure AD Connect-synkronisering (kallas synkroniseringsmotor i det här avsnittet).

## <a name="architecture"></a>Arkitektur
Synkroniseringsmotorn skapar en integrerad vy över objekt som lagras i flera anslutna datakällor och hanterar identitetsinformation i dessa datakällor. Den här integrerade vyn bestäms av den identitetsinformation som hämtas från anslutna datakällor och en uppsättning regler som avgör hur den här informationen ska bearbetas.

### <a name="connected-data-sources-and-connectors"></a>Anslutna datakällor och kopplingar
Synkroniseringsmotorn bearbetar identitetsinformation från olika datadatabaser, till exempel Active Directory eller en SQL Server-databas. Varje datadatabas som organiserar sina data i ett databasliknande format och som tillhandahåller standardmetoder för dataåtkomst är en potentiell datakällkandidat för synkroniseringsmotorn. De datadatabaser som synkroniseras av synkroniseringsmotorn kallas **anslutna datakällor** eller **anslutna kataloger** (CD).

Synkroniseringsmotorn kapslar in interaktion med en ansluten datakälla i en modul som kallas **en Connector**. Varje typ av ansluten datakälla har en specifik koppling. Kopplingen översätter en obligatorisk åtgärd till det format som den anslutna datakällan förstår.

Kopplingar gör API-anrop för att utbyta identitetsinformation (både läsa och skriva) med en ansluten datakälla. Det är också möjligt att lägga till en anpassad anslutningsapp med hjälp av det utökningsbara anslutningsramverket. Följande bild visar hur en anslutning ansluter en ansluten datakälla till synkroniseringsmotorn.

![Båge1](./media/concept-azure-ad-connect-sync-architecture/arch1.png)

Data kan flöda i båda riktningarna, men de kan inte flöda i båda riktningarna samtidigt. Med andra ord kan en anslutning konfigureras så att data kan flöda från den anslutna datakällan för att synkronisera motorn eller från synkroniseringsmotorn till den anslutna datakällan, men endast en av dessa åtgärder kan utföras samtidigt för ett objekt och attribut. Riktningen kan vara olika för olika objekt och för olika attribut.

Om du vill konfigurera en koppling anger du vilka objekttyper som du vill synkronisera. Om du anger objekttyperna definieras omfattningen av de objekt som ingår i synkroniseringsprocessen. Nästa steg är att välja de attribut som ska synkroniseras, som kallas en lista över inkludering av attribut. Dessa inställningar kan ändras när som helst som svar på ändringar i dina affärsregler. När du använder installationsguiden för Azure AD Connect konfigureras dessa inställningar åt dig.

Om du vill exportera objekt till en ansluten datakälla måste listan med attributinräknande innehålla minst de minsta attribut som krävs för att skapa en viss objekttyp i en ansluten datakälla. Attributet **sAMAccountName** måste till exempel inkluderas i attributinkluderingslistan för att exportera ett användarobjekt till Active Directory eftersom alla användarobjekt i Active Directory måste ha ett **sAMAccountName-attribut** definierat. Återigen gör installationsguiden den här konfigurationen åt dig.

Om den anslutna datakällan använder strukturella komponenter, till exempel partitioner eller behållare för att ordna objekt, kan du begränsa de områden i den anslutna datakällan som används för en viss lösning.

### <a name="internal-structure-of-the-sync-engine-namespace"></a>Den interna strukturen för namnområdet för synkroniseringsmotorn
Hela namnområdet för synkroniseringsmotorn består av två namnområden som lagrar identitetsinformationen. De två namnområdena är:

* Anslutningsutrymmet (CS)
* Metaverse (MV)

**Kopplingsutrymmet** är ett mellanlagringsområde som innehåller representationer av de angivna objekten från en ansluten datakälla och de attribut som anges i listan över inkludering av attribut. Synkroniseringsmotorn använder anslutningsutrymmet för att avgöra vad som har ändrats i den anslutna datakällan och för att arrangera inkommande ändringar. Synkroniseringsmotorn använder också anslutningsutrymmet för att arrangera utgående ändringar för export till den anslutna datakällan. Synkroniseringsmotorn har ett distinkt anslutningsutrymme som mellanlagringsområde för varje koppling.

Genom att använda ett mellanlagringsområde förblir synkroniseringsmotorn oberoende av de anslutna datakällorna och påverkas inte av deras tillgänglighet och tillgänglighet. Därför kan du när som helst bearbeta identitetsinformation med hjälp av data i mellanlagringsområdet. Synkroniseringsmotorn kan endast begära de ändringar som gjorts i den anslutna datakällan sedan den senaste kommunikationssessionen avslutades eller endast har utelämnat ändringar i identitetsinformation som den anslutna datakällan ännu har tagit emot, vilket minskar nätverket mellan synkroniseringsmotorn och den anslutna datakällan.

Dessutom lagrar synkroniseringsmotorn statusinformation om alla objekt som den faser i anslutningsutrymmet. När nya data tas emot utvärderar synkroniseringsmotorn alltid om data redan har synkroniserats.

**Metaversumet** är ett lagringsutrymme som innehåller den aggregerade identitetsinformationen från flera anslutna datakällor, vilket ger en enda global, integrerad vy över alla kombinerade objekt. Metaversumobjekt skapas baserat på den identitetsinformation som hämtas från de anslutna datakällorna och en uppsättning regler som gör att du kan anpassa synkroniseringsprocessen.

Följande bild visar namnområdet för anslutningsutrymme och det metaversumnamnområde som finns i synkroniseringsmotorn.

![Båge2](./media/concept-azure-ad-connect-sync-architecture/arch2.png)

## <a name="sync-engine-identity-objects"></a>Synkronisera motoridentitetsobjekt
Objekten i synkroniseringsmotorn är representationer av antingen objekt i den anslutna datakällan eller den integrerade vy som synkroniseringsmotorn har av dessa objekt. Varje synkroniseringsmotorobjekt måste ha en globalt unik identifierare (GUID). GUID ger dataintegritet och expressrelationer mellan objekt.

### <a name="connector-space-objects"></a>Objekt för kopplingsutrymme
När synkroniseringsmotorn kommunicerar med en ansluten datakälla läser den identitetsinformationen i den anslutna datakällan och använder den informationen för att skapa en representation av identitetsobjektet i anslutningsutrymmet. Du kan inte skapa eller ta bort dessa objekt individuellt. Du kan dock ta bort alla objekt manuellt i ett anslutningsutrymme.

Alla objekt i kopplingsutrymmet har två attribut:

* En globalt unik identifierare (GUID)
* Ett unikt namn (även känt som DN)

Om den anslutna datakällan tilldelar objektet ett unikt attribut kan objekt i kopplingsutrymmet också ha ett ankarattribut. Ankarattributet identifierar unikt ett objekt i den anslutna datakällan. Synkroniseringsmotorn använder ankaret för att hitta motsvarande representation av det här objektet i den anslutna datakällan. Synkroniseringsmotorn förutsätter att ett objekts ankare aldrig ändras under objektets livstid.

Många av kopplingarna använder en känd unik identifierare för att generera ett ankare automatiskt för varje objekt när det importeras. Active Directory Connector använder till exempel **attributet objectGUID** för ett ankare. För anslutna datakällor som inte tillhandahåller en tydligt definierad unik identifierare kan du ange ankargenerering som en del av anslutningskonfigurationen.

I så fall byggs ankaret från ett eller flera unika attribut av en objekttyp, varav ingen ändras och som unikt identifierar objektet i anslutningsutrymmet (till exempel ett medarbetarnummer eller ett användar-ID).

Ett anslutningsutrymmesobjekt kan vara något av följande:

* Ett mellanlagringsobjekt
* En platshållare

### <a name="staging-objects"></a>Mellanlagringsobjekt
Ett mellanlagringsobjekt representerar en instans av de angivna objekttyperna från den anslutna datakällan. Förutom GUID och det unika namnet har ett mellanlagringsobjekt alltid ett värde som anger objekttypen.

Mellanlagringsobjekt som har importerats har alltid ett värde för ankarattributet. Mellanlagringsobjekt som nyligen har etablerats av synkroniseringsmotorn och håller på att skapas i den anslutna datakällan har inget värde för ankarattributet.

Mellanlagringsobjekt har också aktuella värden för affärsattribut och driftsinformation som krävs av synkroniseringsmotorn för att utföra synkroniseringsprocessen. Operativ information innehåller flaggor som anger vilken typ av uppdateringar som är iscensatta på mellanlagringsobjektet. Om ett mellanlagringsobjekt har fått ny identitetsinformation från den anslutna datakällan som ännu inte har bearbetats, flaggas objektet som **väntande import**. Om ett mellanlagringsobjekt har ny identitetsinformation som ännu inte har exporterats till den anslutna datakällan flaggas det som **väntande export**.

Ett mellanlagringsobjekt kan vara ett importobjekt eller ett exportobjekt. Synkroniseringsmotorn skapar ett importobjekt med hjälp av objektinformation som tas emot från den anslutna datakällan. När synkroniseringsmotorn tar emot information om förekomsten av ett nytt objekt som matchar en av de objekttyper som valts i kopplingen, skapas ett importobjekt i anslutningsutrymmet som en representation av objektet i den anslutna datakällan.

Följande bild visar ett importobjekt som representerar ett objekt i den anslutna datakällan.

![Båge3](./media/concept-azure-ad-connect-sync-architecture/arch3.png)

Synkroniseringsmotorn skapar ett exportobjekt med hjälp av objektinformation i metaversumet. Exportera objekt exporteras till den anslutna datakällan under nästa kommunikationssession. Ur synkroniseringsmotorns perspektiv finns det inte exportobjekt i den anslutna datakällan ännu. Därför är ankarattributet för ett exportobjekt inte tillgängligt. När objektet har tagit emot objektet från synkroniseringsmotorn skapar den anslutna datakällan ett unikt värde för objektets ankarattribut.

Följande bild visar hur ett exportobjekt skapas med hjälp av identitetsinformation i metaversumet.

![Båge4](./media/concept-azure-ad-connect-sync-architecture/arch4.png)

Synkroniseringsmotorn bekräftar exporten av objektet genom att importera objektet från den anslutna datakällan. Exportera objekt blir importobjekt när synkroniseringsmotorn tar emot dem under nästa import från den anslutna datakällan.

### <a name="placeholders"></a>Platshållare
Synkroniseringsmotorn använder ett platt namnområde för att lagra objekt. Vissa anslutna datakällor, till exempel Active Directory, använder dock ett hierarkiskt namnområde. Om du vill omvandla information från ett hierarkiskt namnområde till ett platt namnområde använder synkroniseringsmotorn platshållare för att bevara hierarkin.

Varje platshållare representerar en komponent (till exempel en organisationsenhet) av ett objekts hierarkiska namn som inte har importerats till synkroniseringsmotor men som krävs för att konstruera det hierarkiska namnet. De fyller luckor som skapas av referenser i den anslutna datakällan till objekt som inte mellanstationerar objekt i kopplingsutrymmet.

Synkroniseringsmotorn använder också platshållare för att lagra refererade objekt som ännu inte har importerats. Om synkronisering till exempel är konfigurerat för att inkludera managerattributet för *Abbie Spencer-objektet* och det mottagna värdet är ett objekt som inte har importerats ännu, till exempel *CN=Lee Sperry,CN=Users,DC=fabrikam,DC=com*, lagras chefinformationen som platshållare i anslutningsutrymmet. Om förvaltarobjektet senare importeras skrivs platshållarobjektet över av mellanlagringsobjektet som representerar chefen.

### <a name="metaverse-objects"></a>Metaversumobjekt
Ett metaversumobjekt innehåller den aggregerade vy som synkroniseringsmotorn har för mellanlagringsobjekten i kopplingsutrymmet. Synkroniseringsmotorn skapar metaversumobjekt med hjälp av informationen i importobjekt. Flera kopplingsutrymmesobjekt kan länkas till ett enda metaversumobjekt, men ett kopplingsutrymmesobjekt kan inte länkas till mer än ett metaversumobjekt.

Metaversumobjekt kan inte skapas eller tas bort manuellt. Synkroniseringsmotorn tar automatiskt bort metaversumobjekt som inte har någon länk till något anslutningsutrymme i kopplingsutrymmet.

Om du vill mappa objekt i en ansluten datakälla till en motsvarande objekttyp i metaversumet tillhandahåller synkroniseringsmotorn ett utökningsbart schema med en fördefinierad uppsättning objekttyper och associerade attribut. Du kan skapa nya objekttyper och attribut för metaversumobjekt. Attribut kan värderas med ett värde eller flera värden och attributtyperna kan vara strängar, referenser, tal och booleska värden.

### <a name="relationships-between-staging-objects-and-metaverse-objects"></a>Relationer mellan mellanlagringsobjekt och metaversumobjekt
Inom namnområdet för synkroniseringsmotorn aktiveras dataflödet av länkrelationen mellan mellanlagringsobjekt och metaversumobjekt. Ett mellanlagringsobjekt som är länkat till ett metaversumobjekt kallas ett **sammanfogat objekt** (eller **kopplingsobjekt**). Ett mellanlagringsobjekt som inte är länkat till ett metaversumobjekt kallas ett **osammanhängande objekt** (eller **frånkopplingsobjekt).** De termer som sammanfogas och delas upp är att föredra att inte förväxla med de kopplingar som ansvarar för att importera och exportera data från en ansluten katalog.

Platshållare är aldrig länkade till ett metaversumobjekt

Ett sammanfogat objekt består av ett mellanlagringsobjekt och dess länkade relation till ett enda metaversumobjekt. Sammanfogade objekt används för att synkronisera attributvärden mellan ett kopplingsutrymmesobjekt och ett metaversumobjekt.

När ett mellanlagringsobjekt blir ett sammanfogat objekt under synkroniseringen kan attribut flöda mellan mellanlagringsobjektet och metaversumobjektet. Attributflödet är dubbelriktat och konfigureras med hjälp av importattributregler och exportattributregler.

Ett enda kopplingsutrymmesobjekt kan bara länkas till ett metaversumobjekt. Varje metaversumobjekt kan dock länkas till flera kopplingsutrymmesobjekt i samma eller i olika kopplingsutrymmen, vilket visas i följande bild.

![Båge5](./media/concept-azure-ad-connect-sync-architecture/arch5.png)

Den länkade relationen mellan mellanlagringsobjektet och ett metaversumobjekt är beständig och kan bara tas bort av regler som du anger.

Ett disjoined objekt är ett mellanlagringsobjekt som inte är länkat till något metaversumobjekt. Attributvärdena för ett osammanhängande objekt bearbetas inte längre inom metaversumet. Attributvärdena för motsvarande objekt i den anslutna datakällan uppdateras inte av synkroniseringsmotorn.

Genom att använda osammanhängande objekt kan du lagra identitetsinformation i synkroniseringsmotorn och bearbeta den senare. Att behålla ett mellanlagringsobjekt som ett osammanhängande objekt i anslutningsutrymmet har många fördelar. Eftersom systemet redan har mellanlagrade den information som krävs om det här objektet är det inte nödvändigt att skapa en representation av objektet igen under nästa import från den anslutna datakällan. På så sätt har synkroniseringsmotorn alltid en fullständig ögonblicksbild av den anslutna datakällan, även om det inte finns någon aktuell anslutning till den anslutna datakällan. Osammanhängande objekt kan konverteras till sammanfogade objekt och vice versa, beroende på vilka regler du anger.

Ett importobjekt skapas som ett osammanhängande objekt. Ett exportobjekt måste vara ett sammanfogat objekt. Systemlogiken tillämpar den här regeln och tar bort alla exportobjekt som inte är ett sammanfogat objekt.

## <a name="sync-engine-identity-management-process"></a>Hantering av synkronisering av motorns identitetshantering
Identitetshanteringsprocessen styr hur identitetsinformation uppdateras mellan olika anslutna datakällor. Identitetshantering sker i tre processer:

* Importera
* Synkronisering
* Exportera

Under importen utvärderar synkroniseringsmotorn inkommande identitetsinformation från en ansluten datakälla. När ändringar upptäcks skapas nya mellanlagringsobjekt eller befintliga mellanlagringsobjekt i anslutningsutrymmet för synkronisering.

Under synkroniseringsprocessen uppdaterar synkroniseringsmotorn metaversumet för att återspegla ändringar som har inträffat i anslutningsutrymmet och uppdaterar anslutningsutrymmet för att återspegla ändringar som har inträffat i metaversumet.

Under exportprocessen skickar synkroniseringsmotorn ut ändringar som mellanlagras på mellanlagringsobjekt och som flaggas som väntande export.

Följande bild visar var var och en av processerna inträffar när identitetsinformation flödar från en ansluten datakälla till en annan.

![Båge6](./media/concept-azure-ad-connect-sync-architecture/arch6.png)

### <a name="import-process"></a>Importprocess
Under importprocessen utvärderar synkroniseringsmotorn uppdateringar av identitetsinformation. Synkroniseringsmotorn jämför identitetsinformationen som tas emot från den anslutna datakällan med identitetsinformationen om ett mellanlagringsobjekt och avgör om mellanlagringsobjektet kräver uppdateringar. Om det är nödvändigt att uppdatera mellanlagringsobjektet med nya data flaggas mellanlagringsobjektet som väntande import.

Genom att mellanlagring av objekt i anslutningsutrymmet före synkroniseringen kan synkroniseringsmotorn endast bearbeta identitetsinformationen som har ändrats. Den här processen ger följande fördelar:

* **Effektiv synkronisering**. Mängden data som bearbetas under synkroniseringen minimeras.
* **Effektiv omsynkronisering**. Du kan ändra hur synkroniseringsmotorn bearbetar identitetsinformation utan att återansluta synkroniseringsmotorn till datakällan.
* **Möjlighet att förhandsgranska synkronisering**. Du kan förhandsgranska synkronisering för att kontrollera att dina antaganden om identitetshanteringsprocessen är korrekta.

För varje objekt som anges i kopplingen försöker synkroniseringsmotorn först hitta en representation av objektet i kopplingens anslutningsutrymme. Synkroniseringsmotorn undersöker alla mellanlagringsobjekt i kopplingsutrymmet och försöker hitta ett motsvarande mellanlagringsobjekt som har ett matchande ankarattribut. Om inget befintligt mellanlagringsobjekt har ett matchande ankarattribut försöker synkroniseringsmotorn hitta ett motsvarande mellanlagringsobjekt med samma unika namn.

När synkroniseringsmotorn hittar ett mellanlagringsobjekt som matchar med ett unikt namn men inte med ankare uppstår följande speciella beteende:

* Om objektet som finns i kopplingsutrymmet inte har något ankare, tar synkroniseringsmotorn bort objektet från kopplingsutrymmet och markerar det metaversumobjekt som det är länkat till som **återförsöksetablering vid nästa synkroniseringskörning**. Sedan skapas det nya importobjektet.
* Om objektet som finns i anslutningsutrymmet har ett ankare förutsätter synkroniseringsmotorn att objektet antingen har bytt namn eller tagits bort i den anslutna katalogen. Det tilldelar ett tillfälligt, nytt unikt namn för kopplingsutrymmesobjektet så att det inkommande objektet kan arrangeras. Det gamla objektet blir sedan **tillfälligt**och väntar på att kopplingen ska importera byten eller borttagningen för att lösa situationen.

Om synkroniseringsmotorn hittar ett mellanlagringsobjekt som motsvarar det objekt som anges i kopplingen avgör den vilken typ av ändringar som ska tillämpas. Synkroniseringsmotorn kan till exempel byta namn på eller ta bort objektet i den anslutna datakällan, eller så kanske det bara uppdaterar objektets attributvärden.

Mellanlagringsobjekt med uppdaterade data markeras som väntande import. Det finns olika typer av pågående import. Beroende på resultatet av importprocessen har ett mellanlagringsobjekt i anslutningsutrymmet en av följande väntande importtyper:

* **Ingen**. Det finns inga ändringar i något av attributen för mellanlagringsobjektet. Synkroniseringsmotor flaggar inte den här typen som väntande import.
* **Lägg till**. Mellanlagringsobjektet är ett nytt importobjekt i kopplingsutrymmet. Synkroniseringsmotorn flaggar den här typen som väntande import för ytterligare bearbetning i metaversumet.
* **Uppdatera**. Synkroniseringsmotorn hittar ett motsvarande mellanlagringsobjekt i kopplingsutrymmet och flaggar den här typen som väntande import så att uppdateringar av attributen kan bearbetas i metaversumet. Uppdateringar inkluderar objektbyte.
* **Ta bort**. Synkroniseringsmotorn hittar ett motsvarande mellanlagringsobjekt i kopplingsutrymmet och flaggar den här typen som väntande import så att det kopplade objektet kan tas bort.
* **Ta bort/lägg till**. Synkroniseringsmotorn hittar ett motsvarande mellanlagringsobjekt i kopplingsutrymmet, men objekttyperna matchar inte. I det här fallet är en ändring mellan borttagningstillägg stegvis. En ändring av borttagningstillägg anger för synkroniseringsmotorn att en fullständig omsynkronisering av objektet måste ske eftersom olika uppsättningar regler gäller för det här objektet när objekttypen ändras.

Genom att ange väntande importstatus för ett mellanlagringsobjekt är det möjligt att avsevärt minska mängden data som bearbetas under synkroniseringen, eftersom det gör att systemet endast kan bearbeta de objekt som har uppdaterade data.

### <a name="synchronization-process"></a>Synkroniseringsprocess
Synkroniseringen består av två relaterade processer:

* Inkommande synkronisering, när innehållet i metaversumet uppdateras med hjälp av data i anslutningsutrymmet.
* Utgående synkronisering, när innehållet i anslutningsutrymmet uppdateras med hjälp av data i metaversumet.

Genom att använda informationen som mellanlagras i anslutningsutrymmet skapar den inkommande synkroniseringsprocessen i metaversum den integrerade vyn av data som lagras i de anslutna datakällorna. Antingen sammanställs alla mellanlagringsobjekt eller endast de med väntande importinformation, beroende på hur reglerna konfigureras.

Den utgående synkroniseringsprocessen uppdaterar exportobjekt när metaversumobjekt ändras.

Inkommande synkronisering skapar den integrerade vyn i metaversumet för identitetsinformationen som tas emot från de anslutna datakällorna. Synkroniseringsmotorn kan när som helst bearbeta identitetsinformation med hjälp av den senaste identitetsinformationen som den har från den anslutna datakällan.

**Inkommande synkronisering**

Inkommande synkronisering omfattar följande processer:

* **Etablera** (kallas även **Projektion** om det är viktigt att skilja den här processen från utgående synkroniseringsetablering). Synkroniseringsmotorn skapar ett nytt metaversumobjekt baserat på ett mellanlagringsobjekt och länkar dem. Etablering är en åtgärd på objektnivå.
* **Gå med**. Synkroniseringsmotorn länkar ett mellanlagringsobjekt till ett befintligt metaversumobjekt. En koppling är en åtgärd på objektnivå.
* **Importera attributflöde**. Synkroniseringsmotorn uppdaterar attributvärdena, som kallas attributflöde, för objektet i metaversumet. Importattributflödet är en åtgärd på attributnivå som kräver en länk mellan ett mellanlagringsobjekt och ett metaversumobjekt.

Bestämmelse är den enda process som skapar objekt i metaversumet. Bestämmelsen påverkar bara importobjekt som är osammanhängande objekt. Under etableringen skapar synkroniseringsmotorn ett metaversumobjekt som motsvarar objekttypen för importobjektet och upprättar en länk mellan båda objekten, vilket skapar ett sammanfogat objekt.

Kopplingsprocessen upprättar också en länk mellan importobjekt och ett metaversumobjekt. Skillnaden mellan koppling och bestämmelse är att kopplingsprocessen kräver att importobjektet är länkat till ett befintligt metaversumobjekt, där etableringsprocessen skapar ett nytt metaversumobjekt.

Synkroniseringsmotorn försöker koppla ett importobjekt till ett metaversumobjekt med hjälp av villkor som anges i konfigurationen för synkroniseringsregel.

Under etablerings- och kopplingsprocesserna länkar synkroniseringsmotorn ett osammanhängande objekt till ett metaversumobjekt, vilket gör att de sammanfogas. När dessa åtgärder på objektnivå har slutförts kan synkroniseringsmotorn uppdatera attributvärdena för det associerade metaversumobjektet. Den här processen kallas importattributflöde.

Importattributflödet sker på alla importobjekt som innehåller nya data och som är länkade till ett metaversumobjekt.

**Utgående synkronisering**

Utgående synkronisering uppdaterar exportobjekt när ett metaversumobjekt ändras men inte tas bort. Syftet med utgående synkronisering är att utvärdera om ändringar i metaversumobjekt kräver uppdateringar av mellanlagringsobjekt i kopplingsutrymmena. I vissa fall kan ändringarna kräva att mellanlagringsobjekt i alla kopplingsutrymmen uppdateras. Mellanlagringsobjekt som ändras flaggas som väntande export, vilket gör att de exporterar objekt. Dessa exportobjekt överförs senare till den anslutna datakällan under exportprocessen.

Utgående synkronisering har tre processer:

* **Etablering**
* **Avetablering**
* **Exportera attributflöde**

Etablering och avetablering är båda åtgärder på objektnivå. Avetablering beror på etablering eftersom endast etablering kan initiera den. Avetablering utlöses när etableringen tar bort länken mellan ett metaversumobjekt och ett exportobjekt.

Etablering utlöses alltid när ändringar tillämpas på objekt i metaversumet. När ändringar görs i metaversumobjekt kan synkroniseringsmotorn utföra någon av följande uppgifter som en del av etableringsprocessen:

* Skapa sammanfogade objekt, där ett metaversumobjekt är länkat till ett nyskapade exportobjekt.
* Byt namn på ett sammanfogat objekt.
* Koppla bort länkar mellan ett metaversumobjekt och mellanlagringsobjekt, vilket skapar ett osammanhängande objekt.

Om etablering kräver synkroniseringsmotor för att skapa ett nytt kopplingsobjekt, är mellanlagringsobjektet som metaversumobjektet är länkat till alltid ett exportobjekt, eftersom objektet ännu inte finns i den anslutna datakällan.

Om etablering kräver synkroniseringsmotor för att skilja ett sammanfogat objekt, vilket skapar ett disjoined objekt, utlöses avetablering. Avetableringsprocessen tar bort objektet.

När du avetableras tas objektet inte fysiskt bort om du tar bort ett exportobjekt. Objektet flaggas som **borttaget,** vilket innebär att borttagningsåtgärden mellanlagras på objektet.

Exportattributflödet sker också under den utgående synkroniseringsprocessen, ungefär som det sätt på vilket importattributflödet sker under inkommande synkronisering. Exportattributflödet sker endast mellan metaversum och exportobjekt som är kopplade.

### <a name="export-process"></a>Exportprocess
Under exportprocessen undersöker synkroniseringsmotorn alla exportobjekt som flaggas som väntande export i anslutningsutrymmet och skickar sedan uppdateringar till den anslutna datakällan.

Synkroniseringsmotorn kan avgöra hur framgångsrik en export lyckas, men den kan inte tillräckligt fastställa att identitetshanteringsprocessen är klar. Objekt i den anslutna datakällan kan alltid ändras av andra processer. Eftersom synkroniseringsmotorn inte har en beständig anslutning till den anslutna datakällan är det inte tillräckligt att göra antaganden om egenskaperna för ett objekt i den anslutna datakällan baserat endast på ett lyckat exportmeddelande.

En process i den anslutna datakällan kan till exempel ändra tillbaka objektets attribut till sina ursprungliga värden (det vill säga den anslutna datakällan kan skriva över värdena direkt efter att data har skickats ut av synkroniseringsmotorn och tillämpats i den anslutna datakällan).

Synkroniseringsmotorn lagrar export- och importstatusinformation om varje mellanlagringsobjekt. Om värdena för de attribut som anges i listan över inkludering av attribut har ändrats sedan den senaste exporten, gör lagringen av import- och exportstatus att synkroniseringsmotorn kan reagera på lämpligt sätt. Synkroniseringsmotorn använder importprocessen för att bekräfta attributvärden som har exporterats till den anslutna datakällan. En jämförelse mellan importerad och exporterad information, som visas i följande bild, gör det möjligt för synkroniseringsmotorn att avgöra om exporten lyckades eller om den behöver upprepas.

![Båge7](./media/concept-azure-ad-connect-sync-architecture/arch7.png)

Om till exempel synkroniseringsmotorexporterar attribut C, som har värdet 5, till en ansluten datakälla lagras C=5 i sitt exportstatusminne. Varje ytterligare export på det här objektet resulterar i ett försök att exportera C=5 till den anslutna datakällan igen eftersom synkroniseringsmotorn förutsätter att det här värdet inte har tillämpats permanent på objektet (det vill än ett annat värde importerades nyligen från ansluten datakälla). Exportminnet rensas när C=5 tas emot under en importåtgärd på objektet.

## <a name="next-steps"></a>Nästa steg
Läs mer om synkroniseringskonfigurationen för [Azure AD Connect.](how-to-connect-sync-whatis.md)

Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).

