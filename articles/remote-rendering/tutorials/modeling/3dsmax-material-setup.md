---
title: Konfigurera fysiskt baserat åter givnings material i 3DSMax
description: Förklarar hur du konfigurerar fysiskt baserat åter givnings material i 3DSMax och exporterar dem till FBX-format.
author: muxanickms
ms.author: misams
ms.date: 06/16/2020
ms.topic: tutorial
ms.openlocfilehash: df4be8963c93199f9fad23ab3f709f691e1da768
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85880148"
---
# <a name="tutorial-set-up-physically-based-rendering-materials-in-3d-studio-max"></a>Självstudie: Konfigurera fysiskt baserat åter givnings material i 3D Studio Max

## <a name="overview"></a>Översikt
I den här självstudien får du lära dig att:

>[!div class="checklist"]
>
> * Tilldela material med avancerad belysning till objekt i scenen.
> * Hantera instanser av objekt och material.
> * Exportera en scen till FBX-format och välj viktiga alternativ.

Att skapa ett [PBR-material (fysiskt baserat åter givning)](../../overview/features/pbr-materials.md) i 3D Studio Max (3DSMax) är en relativt enkel uppgift. Det liknar på många sätt att installera PBR i andra appar för innehålls skapande som Maya. Den här självstudien är en guide till grundläggande PBR Shader-installation och FBX export för Azure-fjärrrendering-projekt.

Exempel scenen i den här självstudien innehåller ett antal polygon-Box-objekt. De har tilldelats olika material, till exempel trä, metall, målad metall, plast och gummi. Varje material innehåller i stort sett alla eller de flesta av följande texturer:

* **Albedo**, som är materialets färg karta och kallas även för **diffusion** eller **BaseColor**.
* **Metall**, som avgör om ett material är metallisk och vilka delar som är metalliska. 
* **Grovhet**, som avgör hur grov eller utjämna en yta är.
Det påverkar också skärpan eller blurriness av reflekterande och högdagrar på en yta.
* **Normal**, som lägger till information på en yta utan att behöva lägga till fler polygoner. Exempel på Detaljer kan vara pitting och indrag på en metall yta eller kornig het i trä.
* **Omgivande ocklusion**, som används för att lägga till mjuka skuggor och kontakt skuggor i en modell. Det är en grå Skale karta som visar vilka områden i en modell som får fullständig belysning (vit) eller en fullständig nyans (svart).

## <a name="prepare-the-scene"></a>Förbered scenen
I **3D Studio Max**är processen för att ställa in ett PBR-material som följer.

För att börja med, som du ser i exempel scenen har vi skapat ett antal Box-objekt, som representerar en annan typ av material:

>[!TIP]
>Det är värt att notera innan du börjar skapa till gångar för ARR som använder **mätare** för mått.  
>Därför är det lämpligt att ställa in dina **enhets system enheter** på **mätare**. Dessutom är det tillrådligt att exportera för att ange enheter till mätare i FBX export inställningar.

Följande bild illustrerar stegen för att ställa in system enheter på mätare i högsta antal i 3D Studio. I huvud menyn går du till **Anpassa**  >  **enheter installations**  >  **system enheter** och i list rutan **system enheter skalning** väljer du **mätare**. 
![system enheter](media/3dsmax/system-units.jpg)

Med system enheter inställda på mätare kan vi börja skapa våra modeller. I vår exempel scen skapar vi flera Box-objekt, var och en som representerar en annan material typ, till exempel metall, gummi, plast osv. 

>[!TIP]
>Det är en bra idé när du skapar till gångar för att ge namn på rätt sätt. Detta gör dem lättare att hitta senare om scenen har många objekt

![Byt namn på objekt](media/3dsmax/rename-objects.jpg)

## <a name="assign-materials"></a>Tilldela material

Med vissa objekt som skapats i vår scen – i det här fallet ett antal kuber kan vi starta PBR-installationen:

* I huvud verktygsfältet klickar du på ikonen **material redigerare** , som du ser i följande bild. Du kan också trycka på **M** på tangent bordet för att öppna redigeraren. Material redigeraren har två lägen som kan väljas i list rutan **lägen** – **kompakt material redigerings** läge och läget för bakgrunds **material** . Eftersom den här scenen är relativt enkel, kommer vi att använda **kompakt läge**.

* Inuti material redigeraren visas ett antal sfärer – dessa sfärer är vårt material. Vi kommer att tilldela ett av dessa material till varje objekt-Box – i vår scen. Om du vill utföra den här tilldelningen väljer du först ett av objekten i huvud visnings området. När du har valt det här alternativet klickar du på den första sfären i fönstret material redigerare. När du har tilldelat ett objekt markeras det valda materialet som du ser i nästa bild.

* Klicka på knappen **Tilldela material till markering** som visas. Det valda materialet har nu tilldelats det valda objektet.
![Tilldela material](media/3dsmax/assign-material.jpg)

I material redigeraren kan du välja typer av material från ett brett urval, beroende på ditt användnings fall. Normalt är material typen **standard** inställd som standard. Det här materialet är ett grundläggande material som inte är lämpligt för PBR-installationen, så vi måste ändra material typen till ett PBR-material. Det **fysiska materialet är det fysiska materialet**som föredra **3DSMax** material för Azure-fjärrrendering-projekt.

* I material redigeraren klickar du på fliken **standard** och i den material-och kart läsare som öppnar Välj **fysiskt material**. Den här åtgärden kommer att konvertera det tilldelade **standard** materialet till ett fysiskt PBR- **material**.
![fysiskt material](media/3dsmax/physical-material.jpg)

* I material redigeraren visas nu egenskaperna för det fysiska materialet (se nedan) och vi kan börja tilldela till gång till strukturer.
![texturer – lista](media/3dsmax/textures-list.jpg)

Som du kan se på bilden ovan finns det en mängd olika kartor och texturer som kan läggas till i materialet. I vårt syfte kommer vi bara att använda fem textur fack i materialet.

>[!TIP]
>Det är en bra idé att namnge ditt material på lämpligt sätt, som visas i bilden ovan.

Nu kan vi börja överväga att tilldela texturer till vårt material. Hur du genererar strukturer kan variera beroende på preferenser eller till och med efter användning. Du kan till exempel vara glad att använda bild strukturer som kan användas för alla till gångar, eller så kan du behöva specifika delar av ett projekt/till gång för att få en egen anpassad uppsättning texturer. Du kanske vill använda generiska indelnings texturer som erhållits online eller skapa dem själv i appar som **Photoshop**, **Quixel Suite**, **substans Suite** osv. 

Innan vi börjar tilldela våra strukturer, kommer vi att behöva ta hänsyn till våra till gångars textur koordinater (UVW). Även om det är bäst att använda en textur i en modell för att se till att modellen har blivit omsluten (texturer inte visas korrekt utan rätt UV-unwrap), är det viktigt för våra syfte om vi tänker använda en **ocklusion** karta i vår modell. Till skillnad från **Stingray Shader** i **Maya**har det **fysiska materialet** i **3DSMax** inte någon dedikerad **omgivande ocklusion** textur plats. Därför kommer vi att använda AO-kartan på en annan plats och för att tillåta att den används separat från andra texturer (till exempel), vi tilldelar den en egen UVW-kart kanal. 

Vi börjar med att tilldela en **UVW-modifierare** till vår modell enligt följande:

* I redigeraren för valda objekt egenskaper klickar du på listan modifierare och i den nedrullningsbara List rutan öppnas rullnings listen nedåt och väljer unwrap UVW. Den här åtgärden kommer att använda en UVW-modifierare för vår till gång.
![unwrap-modifierare](media/3dsmax/unwrap-modifier.jpg)

* Kart kanalen är inställd på en. Den är i kart kanal en som du kommer att göra i huvud listan. I vårt fall har objektet inte omslutits utan några överlappande textur koordinater (UV).
![unwrap-UVW](media/3dsmax/unwrapped-uvw.jpg)

Nästa steg är att skapa en andra UV-kart kanal.

* Stäng UV-redigeraren om den är öppen och i avsnittet kanal på menyn **Redigera UV** -menyn ändra kanal numret till två. Kart kanal 2 är den förväntade kanalen för omgivande ocklusion Maps. 

* I dialog rutan för **kanal ändrings varningar** som öppnas kan du välja att antingen **Flytta** befintliga UV-objekt i kanal 1 till den nya kanalen 2 eller **överge** de befintliga UV: s som skapar en ny UV- **unwrap** automatiskt. Välj endast **överge** om du vill skapa en ny **UV** -omslutning för den omgivande ocklusion-kartan som skiljer sig från UV: s i kart kanal 1 (till exempel om du vill använda strukturerade texturer i kanal 1). I vårt syfte kommer vi att **Flytta** UV: s från kanal ett till kanal 2 eftersom vi inte behöver redigera den nya UV-kanalen.

>[!NOTE]
>Även om du har kopierat – **flyttat** – UV-unwrap från kart kanal 1 till kart kanal 2 kan du göra nödvändiga ändringar i den nya kanalens UV, utan att påverka den ursprungliga kart kanalen.

![kanal-ändra](media/3dsmax/channel-change.jpg)

Med den nya kart kanalen skapad kan vi återgå till det fysiska materialet i material redigeraren och börja lägga till våra texturer till den. Vi lägger först till den omgivande ocklusion (**Ao**)-kartan eftersom det finns ett ytterligare steg att vidta för att tillåta att den fungerar korrekt. När AO-kartan är ansluten till vårt material måste vi instruera den att använda kart kanal 2.

* Som tidigare nämnts finns det ingen dedikerad plats för AO Maps i det **3DSMax fysiska materialet**. Vi tillämpar i stället den AO kartan på den **diffusa tuffa** platsen.

* I det fysiska materialets **allmänna Maps** -lista klickar du på den **diffusa ojämnheten** **ingen kart** plats och läser in din Ao-karta.

* I egenskaperna för AO texturer visas kart kanalen inställd på **1** som standard. Ändra värdet till **2**. Den här åtgärden slutför de steg som krävs för att lägga till din omgivande ocklusion-karta.

>[!IMPORTANT]
>Det här är ett viktigt steg, särskilt om UV: er i kanal 2 skiljer sig från dem i kanal 1, eftersom AO inte kommer att mappa korrekt med fel kanal vald.

![tilldela-Ao – mappa](media/3dsmax/assign-ao-map.jpg)

Vi kommer nu att ta itu med att tilldela vår normal-karta till vårt PBR-material. Den här åtgärden skiljer sig något från **Maya** i att normal-kartan inte appliceras direkt på ojämnhets kartans plats (det finns ingen normal kart plats i det **3DSMax fysiska materialet** som sådan), men läggs i stället till i en normal-map-modifierare som är kopplad till den **normala** platsen.

* I avsnittet **särskilda kartor** i egenskaper för fysiskt material (i material redigeraren) klickar du på diagrammets **ojämnhets**plats **.** 

* Leta upp och klicka på **Normal ojämnhet**i webbläsaren material/karta. Med den här åtgärden läggs en **Normal ojämnhets** modifierare till på vårt material.

* I den **normala ojämnhets** modifieraren klickar du på **Normal**, **ingen karta** och letar upp och läser in din normala-karta.

* Kontrol lera att metoden är inställd på **tangens** (den bör vara som standard) och växla till **vänd grönt (Y)** om det behövs.

![normal-ojämnhets ](media/3dsmax/normal-bump.jpg)
 ![ belastning – normal-karta](media/3dsmax/load-normal-map.jpg)

Med vår normala karta tilldelad kan vi fortsätta att tilldela de återstående texturerna för att slutföra installationen av det fysiska materialet. Den här processen är en enkel process utan särskilda inställningar att tänka på. Följande bild visar en fullständig uppsättning texturer som tilldelats vårt material: ![ alla – texturer](media/3dsmax/all-textures.jpg)

När ditt PBR-material har skapats och kon figurer ATS är det värt att tänka på instans objekt i din scen. Informerar liknande objekt i din scen – till exempel nötter, bultar, skruv spolar, i stort sett alla objekt som är desamma kan ge betydande besparingar i fil storlek. Instanser av ett huvud objekt kan ha sin egen skalning, rotation och transformering så att de kan placeras efter behov i din scen. I **3D Studio Max**är **indelnings** processen enkel.

* I huvud visnings området väljer du de objekt/objekt som du vill exportera.

* Håll **ned SKIFT** och dra till gångarna uppåt med verktyget Omforma (flytta) 

* I dialog rutan **klonings alternativ** som öppnas anger du **objekt** till **instans** och klickar på **OK**. 
![instans – objekt](media/3dsmax/instance-object.jpg)

Den här åtgärden skapar en instans av objektet som kan flyttas roterat eller skalas oberoende av dess överordnade och andra instanser av den överordnade.

>[!IMPORTANT]
>Men eventuella ändringar som du gör i ett under objekts läge överförs till alla instanser av ditt objekt, så om du arbetar med ett instans objekt komponenter – hörn, är polygoner osv så att du vill att alla de här instanserna ska påverkas. Kom ihåg att alla instanser av objekt kan göras till ett unikt objekt när som helst. 

>[!TIP]
>Bästa praxis när det gäller indelningen i din scen är att skapa dem när du går vidare, eftersom det är mycket svårt att ersätta **kopior** med instans objekt senare. 

En slutlig sak att ta hänsyn till innan vi går vidare till export processen är hur du kanske vill paketera din scen/till gång för delning. Vi rekommenderar att du skickar till gången till en klient eller en grupp medlem om du vill att de ska kunna öppna och Visa till gången eftersom den bör ses med en minimal mängd du behöver. Det är därför viktigt att du behåller dina till gångs textur Sök vägar i förhållande till scen filen. Om textur Sök vägarna för din till gång pekar på en lokal enhet eller absolut sökväg/plats, kommer de inte att läsas in i scenen om de öppnas på en annan dator, även om **. Max** -filen sitter i samma mapp som texturerna. Att göra textur Sök vägarna relativa i 3D Studio Max löser problemet och är ganska enkelt.

* I huvud verktygsfältet går du till **fil**  >  **referens**för  >  **till gångs spårning växla**. 

* I webbläsaren till gångs spårning som öppnas visas alla eller de flesta texturer som du har tillämpat på ditt PBR-material som anges under kolumnen **Maps/shaders** .

* Bredvid dem i kolumnen **fullständig sökväg** ser du fil Sök vägen till platsen för dina strukturer, vilket förmodligen är deras plats på den lokala datorn.

* Slutligen visas en kolumn med namnet **status**. Den här kolumnen visar om en specifik struktur har hittats och använts på din scen eller inte, och flaggar den här texturen med något av följande villkor **OK**, **found**eller **File saknas**. De två första visar att filen har hittats och lästs in, medan det sista, innebär att spåraren inte kunde hitta någon fil.
![textur – sökvägar](media/3dsmax/texture-paths.jpg)

Du kanske märker att inte alla dina texturer visas i till gångs Spårare när du öppnar det första gången. Det finns inget som är bekymrat över, som att köras genom Sök vägs processen en gång eller två gånger hittar alla scen strukturer. Processen för att söka efter Sök vägar är följande: 

* I fönstret till gångs Spårare **flyttar** + **du till** den översta texturen i listan **kartor/skuggningar** och fortsätter att hålla ned SKIFT-tangenten genom att klicka på den sista texturen i listan. Den här åtgärden markerar alla texturer i listan. De valda texturerna är nu markerade i blått (se bilden ovan).

* Högerklicka på markeringen och i popup-menyn som öppnas, Välj **Ange sökväg**.

* I rutan **Ange till gångs Sök väg** som öppnas, väljer du den lokala sökvägen till dina texturer och ersätter den med följande `.\` och klickar sedan på **OK**. 

* Efter en tids period (som varierar beroende på hur många texturer som finns i din scen och på hur stor din scen är) bör till gångs spåraren lösa sig på följande sätt (se bilden).
![Lös – texturer](media/3dsmax/resolve-textures.jpg)

Observera att kolumnen **fullständig sökväg** nu är tom. Det innebär att scenen inte längre söker efter relevanta texturer på en angiven (absolut) plats, men kommer alltid att hitta dem så länge den maximala filen eller den relaterade FBX-filen placeras i samma mapp som texturerna. 

>[!NOTE]
>Ibland kan det hända att du måste upprepa den här processen några gånger för att hitta och lösa alla texturer och sökvägar. Detta är inget som är bekymrat till, bara upprepa tills alla relevanta till gångar redovisas. Det kan också vara fallet att vissa filer inte längre hittas. I det här fallet markerar du bara alla till gångar i listan och klickar på **ta bort saknade sökvägar** (se bilden ovan)

## <a name="fbx-export"></a>FBX-export

När du har slutfört till gångs spårningen kan vi nu gå vidare till FBX-exporten. Processen är återigen enkel och kan göras på ett par olika sätt. 

>[!TIP]
>Det är en bra idé att om du inte vill exportera hela scenen väljer du endast för att exportera de till gångar som behövs. I synnerhet resurs intensiva scener kan exporten ta lång tid, så det är klokt att bara exportera det du behöver
>
>Om du har använt modifierare som t. ex. **Turbosmooth** eller **Öppna SubDiv** osv som du döljer dem innan du exporterar som de kan orsaka problem under exporten. Spara alltid din scen innan du gör detta! 

* I scenen väljer du de till gångar som du vill exportera och i huvud verktygsfältet går du till **fil**  >  **export**  >  **export vald**

* I dialog rutan **Välj fil som ska exporteras** skriver eller väljer du namn på utdatafil och i alternativet för att **Spara som typ** väljer du **Autodesk (*. FBX)**. Den här åtgärden öppnar FBX-export-menyn. 

* Kom ihåg att om du har skapat instanser i din scen är det viktigt att **bevara instanser** är aktiverade i FBX export inställningar. 
![FBX – exportera](media/3dsmax/fbx-export.jpg)

Kom ihåg att tidigare nämnde att det fanns ett par olika sätt att exportera filen. Om avsikten med exporten är att FBX ska delas tillsammans med dess texturer-filer i en mapp/katalog, bör inställningarna som visas i bilden nedan gälla och fungerar bra. När du har valt inställningarna klickar du på **OK**.
![FBX – inställningar](media/3dsmax/fbx-settings.jpg)

Men om du föredrar att inte dela stora mappar/kataloger med texturer tillsammans med FBX kan du välja att **bädda** in texturerna i FBX. Det innebär att hela till gångs texturer ingår – kommer att läggas till i en enda FBX. Tänk på att om du kombinerar din export till en enda till gång så blir FBX-filen betydligt större.

>[!IMPORTANT]
>Om din resultat FBX-fil är större än 2,4 GB, ska den lägsta versionen av FBX-export inställningarna (se ovan) vara 2016 eller senare. Eftersom nyare versioner har 64-bitars stöd och därmed stöder större filer.

* I export inställningarna för FBX växlar du till * * Inbäddnings medium och klickar sedan på **OK** för att exportera med texturer som ingår. 

När du exporterar till FBX när du använder det fysiska materialet visas förmodligen följande varnings fönster när du klickar på OK i dialog rutan exportera: ![ Exportera – varningar](media/3dsmax/export-warnings.jpg)

Den här varningen informerar bara användaren om att de exporterade materialen kanske inte är kompatibla med andra program varu paket. Eftersom det fysiska materialet är kompatibelt med Azure fjärrrendering är det inget att bekymra dig om. Klicka bara på **OK** för att slutföra processen och Stäng fönstret.

## <a name="conclusion"></a>Slutsats

I allmänhet ser den här typen av material mer realistisk eftersom den baseras på den verkliga fysiken av ljus. Den skapar en extra intensiv påverkan så att scenen verkar finnas i verkligheten.

## <a name="next-steps"></a>Nästa steg

Nu vet du hur du konfigurerar material med avancerad belysning för objekt i en scen. Du vet också hur du exporterar objekten till FBX-format som stöds av Azure Remote rendering. Nästa steg är att konvertera FBX-filen och visualisera den i Azure Remote rendering.

>[!div class="nextstepaction"]
>[Snabb start: konvertera en modell för åter givning](../../quickstarts\convert-model.md)