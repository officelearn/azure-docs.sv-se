---
title: Ansluta till 3270 appar på IBM-stordatorer
description: Integrera och automatisera 3270 skärmdrivna appar med Azure med hjälp av Azure Logic Apps och IBM 3270-anslutning
services: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: estfan, valthom
ms.topic: article
ms.date: 03/06/2019
tags: connectors
ms.openlocfilehash: 808eef5424d678559ae94ffd04e41eacd0f16aee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371107"
---
# <a name="integrate-3270-screen-driven-apps-on-ibm-mainframes-with-azure-by-using-azure-logic-apps-and-ibm-3270-connector"></a>Integrera 3270 skärmdrivna appar på IBM-stordatorer med Azure med Hjälp av Azure Logic Apps och IBM 3270-anslutning

> [!NOTE]
> Den här kopplingen är i [*offentlig förhandsversion*](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

Med Azure Logic Apps och IBM 3270-anslutningen kan du komma åt och köra IBM-stordatorappar som du vanligtvis kör genom att navigera genom 3270 emulatorskärmar. På så sätt kan du integrera dina IBM-stordatorappar med Azure, Microsoft och andra appar, tjänster och system genom att skapa automatiserade arbetsflöden med Azure Logic Apps. Anslutningsappen kommunicerar med IBM-stordatorer med hjälp av TN3270-protokollet och är tillgänglig i alla Azure Logic Apps-regioner utom Azure Government och Azure China 21Vianet. Om du inte har tidigare i logikappar läser du [Vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

I den här artikeln beskrivs dessa aspekter för att använda 3270-kopplingen: 

* Varför använda IBM 3270-anslutningen i Azure Logic Apps och hur anslutningen kör 3270 skärmdrivna appar

* Förutsättningarna och inställningarna för att använda 3270-kontakten

* Stegen för att lägga till 3270-anslutningsåtgärder i logikappen

## <a name="why-use-this-connector"></a>Varför använda den här kontakten?

För att komma åt appar på IBM stordatorer använder du vanligtvis en 3270 terminal emulator, ofta kallad en "grön skärm". Denna metod är ett beprövade sätt men har begränsningar. Även om Host Integration Server (HIS) hjälper dig att arbeta direkt med dessa appar, ibland, separera skärmen och affärslogik kanske inte är möjligt. Eller kanske du inte längre har information om hur värdprogrammen fungerar.

För att utöka dessa scenarier fungerar IBM 3270-kopplingen i Azure Logic Apps med 3270 Design Tool, som du använder för att spela in, eller "fånga", värdskärmarna som används för en viss uppgift, definiera navigeringsflödet för den uppgiften via stordatorappen och definiera metoder med in- och utdataparametrar för den aktiviteten. Designverktyget konverterar den informationen till metadata som 3270-kopplingen använder när du anropar en åtgärd som representerar aktiviteten från logikappen.

När du har genererat metadatafilen från designverktyget lägger du till filen i ett integrationskonto i Azure. På så sätt kan logikappen komma åt appens metadata när du lägger till en 3270-anslutningsåtgärd. Kopplingen läser metadatafilen från ditt integrationskonto, hanterar navigering via 3270-skärmarna och visar parametrarna för 3270-anslutningsåtgärden dynamiskt. Du kan sedan tillhandahålla data till värdprogrammet och kopplingen returnerar resultaten till logikappen. På så sätt kan du integrera dina äldre appar med Azure, Microsoft och andra appar, tjänster och system som Azure Logic Apps stöder.

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Rekommenderas: En [integrationstjänstmiljö (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) 

  Du kan välja den här miljön som plats för att skapa och köra logikappen. En ISE ger åtkomst från din logikapp till resurser som är skyddade i virtuella Azure-nätverk.

* Logikappen som ska användas för att automatisera och köra din 3270-skärmdrivna app

  IBM 3270-anslutningen har inga utlösare, så använd en annan utlösare för att starta logikappen, till exempel **återkomstutlösaren.** Du kan sedan lägga till 3270-anslutningsåtgärder. Kom igång [genom att skapa en tom logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). 
  Om du använder en ISE väljer du ISE som logikappens plats.

* [Ladda ner och installera 3270 Design Tool](https://aka.ms/3270-design-tool-download).
Den enda förutsättningen är [Microsoft .NET Framework 4.6.1](https://aka.ms/net-framework-download).

  Det här verktyget hjälper dig att spela in skärmar, navigeringssökvägar, metoder och parametrar för de uppgifter i appen som du lägger till och kör som 3270-anslutningsåtgärder. Verktyget genererar en HIDX-fil (Host Integration Designer) som tillhandahåller de metadata som krävs för att anslutasen ska användas för att köra stordatorappen.
  
  När du har hämtat och installerat det här verktyget följer du dessa steg för att ansluta till värden:

  1. Öppna designverktyget 3270. Välj **Värdsessioner**på **Session-menyn** .
  
  1. Ange information om TN3270-värdservern.

* Ett [integrationskonto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), som är den plats där du lagrar HIDX-filen som en karta så att logikappen kan komma åt metadata och metoddefinitioner i filen. 

  Kontrollera att ditt integrationskonto är länkat till logikappen du använder. Om du använder en ISE kontrollerar du också att integrationskontots plats är samma ISE som logikappen använder.

* Tillgång till TN3270-servern som är värd för stordatorappen

<a name="define-app-metadata"></a>

## <a name="create-metadata-overview"></a>Översikt över skapa metadata

I en 3270 skärmdriven app är skärmarna och datafälten unika för dina scenarier, så 3270-anslutningen behöver den här informationen om din app, som du kan tillhandahålla som metadata. I de här metadata beskrivs information som hjälper logikappen att identifiera och känna igen skärmar, hur du navigerar mellan skärmar, var du ska mata in data och var du kan förvänta dig resultat. Om du vill ange och generera dessa metadata använder du designverktyget 3270, som hjälper dig genom dessa specifika lägen eller *faser,* enligt beskrivningen senare i mer information:

* **Capture**: I det här läget registrerar du de skärmar som krävs för att slutföra en viss uppgift med din stordatorapp, till exempel att få ett banksaldo.

* **Navigering**: I det här läget anger du planen eller sökvägen för hur du navigerar genom stordatorappens skärmar för den specifika uppgiften.

* **Metoder**: I det här läget definierar `GetBalance`du metoden, till exempel , som beskriver skärmens navigeringssökväg. Du väljer också de fält på varje skärm som blir metodens in- och utdataparametrar.

### <a name="unsupported-elements"></a>Element som inte stöds

Designverktyget stöder inte följande element:

* BMS-kartor (Partial IBM Basic Mapping Support) : Om du importerar en BMS-karta ignorerar designverktyget partiella skärmdefinitioner.
* In/ut-parametrar: Du kan inte definiera in-/ut-parametrar.
* Menybearbetning: Stöds inte under förhandsgranskningen
* Matrisbearbetning: Stöds inte under förhandsgranskningen

<a name="capture-screens"></a>

## <a name="capture-screens"></a>Spela in skärmar

I det här läget markerar du ett objekt på varje 3270-skärm som unikt identifierar den skärmen. Du kan till exempel ange en textrad eller en mer komplex uppsättning villkor, till exempel specifik text och ett icke-tomt fält. Du kan antingen spela in dessa skärmar via en liveanslutning till värdservern eller importera den här informationen från en BMS-karta (IBM Basic Mapping Support). Live-anslutningen använder en TN3270-emulator för anslutning till värden. Varje kopplingsåtgärd måste mappas till en enskild uppgift som börjar med att ansluta till sessionen och slutar med att du kopplar från sessionen.

1. Om du inte redan har gjort det öppnar du designverktyget för 3270. Välj **Fånga** i verktygsfältet så att du går in i inspelningsläge.

1. Om du vill starta inspelningen trycker du på F5 eller på **inspelningsmenyn** väljer du **Starta inspelning**. 

1. Välj **Anslut**på **Session-menyn** .

1. Stega igenom appen i **fönstret Fånga,** från den första skärmen i appen, för den specifika uppgift som du spelar in.

1. När du är klar med uppgiften loggar du ut från appen som vanligt.

1. Välj Koppla från **på** **Session-menyn** .

1. Om du vill stoppa inspelningen trycker du på Skift + F5-tangenterna eller på **inspelningsmenyn** väljer du **Stoppa inspelning**.

   När du har spelat in skärmarna för en uppgift visar designerverktyget miniatyrer som representerar skärmarna. Några anteckningar om dessa miniatyrer:

   * Medföljande med dina intagna skärmar har du en skärm med namnet "Tom".

     När du först ansluter till [CICS](https://www.ibm.com/it-infrastructure/z/cics)måste du skicka "Rensa" innan du kan ange namnet på den transaktion som du vill köra. Skärmen där du skickar "Rensa"-tangenten har inga *igenkänningsattribut,* till exempel en skärmtitel, som du kan lägga till med hjälp av skärmigenkänningsredigeraren. För att representera den här skärmen innehåller miniatyrbilderna en skärm med namnet "Tom". Du kan senare använda den här skärmen för att representera skärmen där du anger transaktionsnamnet.

   * Som standard använder namnet på en tagen skärm det första ordet på skärmen. Om det namnet redan finns lägger designverktyget till namnet med ett understreck och ett tal, till exempel "WBGB" och "WBGB_1".

1. Så här ger du ett mer meningsfullt namn till en tagen skärm:

   1. Markera den skärm som du vill byta namn på i fönstret **Värdskärmar.**

   1. Leta reda på egenskapen **Skärmnamn** i samma fönsterruta, längst ned i samma fönsterruta.

   1. Ändra det aktuella skärmnamnet till ett mer beskrivande namn.

1. Ange nu fälten för att identifiera varje skärm.

   Med dataströmmen 3270 har skärmarna inte standardidentifierare, så du måste välja unik text på varje skärm. För komplexa scenarier kan du ange flera villkor, till exempel unik text och ett fält med ett visst villkor.

När du har markerat igenkänningsfälten går du vidare till nästa läge.

### <a name="conditions-for-identifying-repeated-screens"></a>Villkor för att identifiera upprepade skärmar

För att kopplingen ska kunna navigera och skilja mellan skärmar hittar du vanligtvis unik text på en skärm som du kan använda som identifierare bland de infångade skärmarna. För upprepade skärmar kan du behöva fler identifieringsmetoder. Anta till exempel att du har två skärmar som ser likadana ut förutom att en skärm returnerar ett giltigt värde, medan den andra skärmen returnerar ett felmeddelande.

I designverktyget kan du lägga till *igenkänningsattribut,* till exempel en skärmrubrik som "Hämta kontosaldo", med hjälp av skärmigenkänningsredigeraren. Om du har en klupig bana och båda grenarna returnerar samma skärm men med olika resultat behöver du andra igenkänningsattribut. Vid körning använder kopplingen dessa attribut för att bestämma den aktuella grenen och gaffeln. Här är de villkor du kan använda:

* Specifikt värde: Det här värdet matchar den angivna strängen på den angivna platsen.
* INTE ett specifikt värde: Det här värdet matchar inte den angivna strängen på den angivna platsen.
* Tom: Det här fältet är tomt.
* INTE tomt: Det här fältet är inte tomt.

Mer information finns i [exempelnas navigeringsplan](#example-plan) senare i det här avsnittet.

<a name="define-navigation"></a>

## <a name="define-navigation-plans"></a>Definiera navigeringsplaner

I det här läget definierar du flödet eller stegen för att navigera genom stordatorappens skärmar för din specifika uppgift. Ibland kan du till exempel ha mer än en sökväg som appen kan ta där en sökväg ger rätt resultat, medan den andra sökvägen skapar ett fel. För varje skärm anger du de tangenttryckningar som krävs `CICSPROD <enter>`för att gå till nästa skärm, till exempel .

> [!TIP]
> Om du automatiserar flera uppgifter som använder samma kopplings- och frånkopplingsskärmar innehåller designverktyget särskilda abonnemangstyper för anslutning och frånkoppling. När du definierar dessa planer kan du lägga till dem i navigeringsplanens början och.

### <a name="guidelines-for-plan-definitions"></a>Riktlinjer för plandefinitioner

* Inkludera alla skärmar, som börjar med anslutning och slutar med frånkoppling.

* Du kan skapa en fristående plan eller använda abonnemangen Anslut och koppla från, där du kan återanvända en serie skärmar som är gemensamma för alla dina transaktioner.

  * Den sista skärmen i Connect-abonnemanget måste vara samma skärm som den första skärmen i navigeringsplanen.

  * Den första skärmen i frånkopplingsplanen måste vara samma skärm som den sista skärmen i navigeringsplanen.

* Dina infångade skärmar kan innehålla många upprepade skärmar, så välj och använd bara en instans av upprepade skärmar i planen. Här är några exempel på upprepade skärmar:

  * Inloggningsskärmen, till exempel **SKÄRMEN MSG-10**
  * Välkomstskärmen för CICS
  * Skärmen "Rensa" eller **Tom**

<a name="create-plans"></a>

### <a name="create-plans"></a>Skapa planer

1. I verktygsfältet 3270-designverktyget väljer du **Navigering** så att du går in i navigeringsläge.

1. Starta abonnemanget i **navigeringsfönstret** och välj **Nytt abonnemang**.

1. Ange ett namn på planen under **Välj Nytt plannamn.** Välj plantyp i listan **Typ:**

   | Typ av plan | Beskrivning |
   |-----------|-------------|
   | **Process** | För fristående eller kombinerade planer |
   | **Anslut** | För Connect-abonnemang |
   | **Koppla från** | För frånkopplingsplaner |
   |||

1. Dra de tagna miniatyrerna till navigeringsplansytan i **navigeringsfönstret** i fönstret **Värdskärmar.**

   Om du vill representera den tomma skärmen där du anger transaktionsnamnet använder du skärmen "Tom".

1. Ordna skärmarna i den ordning som beskriver den uppgift som du definierar.

1. Om du vill definiera flödesbanan mellan skärmar, inklusive gafflar och kopplingar, väljer du **Flöde**i designverktygets verktygsfält .

1. Välj den första skärmen i flödet. Dra och rita en anslutning till nästa skärm i flödet.

1. Ange värdena för egenskapen **AID Key** (Attention Identifier) och för egenskapen Fixed **Text** för varje skärm, som flyttar flödet till nästa skärm.

   Du kanske har bara AID-tangenten, eller både AID-tangenten och fast text.

När du är klar med navigeringsplanen kan du [definiera metoder i nästa läge](#define-method).

<a name="example-plan"></a>

### <a name="example"></a>Exempel

Anta i det här exemplet att du kör en CICS-transaktion med namnet "WBGB" med följande steg: 

* På den första skärmen anger du ett namn och ett konto.
* På den andra skärmen får du kontosaldot.
* Du går ut till skärmen "Tom".
* Du loggar ut från CICS till skärmen "MSG-10".

Anta också att du upprepar dessa steg, men du anger felaktiga data så att du kan fånga skärmen som visar felet. Här är skärmarna du fångar:

* MSG-10
* CICS Välkommen
* Tom
* WBGB_1 (ingång)
* WBGB_2 (fel)
* Empty_1
* MSG-10_1

Även om många skärmar här får unika namn, vissa skärmar är samma skärm, till exempel "MSG-10" och "Empty". För en upprepad skärm använder du bara en instans för den skärmen i planen. Här är några exempel som visar hur en fristående plan, Connect-abonnemang, frånkopplingsplan och en kombinerad plan kan se ut:

* Fristående plan

  ![Fristående navigeringsplan](./media/connectors-create-api-3270/standalone-plan.png)

* Anslut plan

  ![Anslut plan](./media/connectors-create-api-3270/connect-plan.png)

* Frånkopplingsplan

  ![Frånkopplingsplan](./media/connectors-create-api-3270/disconnect-plan.png)

* Kombinerad plan

  ![Kombinerad plan](./media/connectors-create-api-3270/combined-plan.png)

#### <a name="example-identify-repeated-screens"></a>Exempel: Identifiera upprepade skärmar

För att kopplingen ska kunna navigera och differentiera skärmar hittar du vanligtvis unik text på en skärm som du kan använda som identifierare på de infångade skärmarna. För upprepade skärmar kan du behöva fler identifieringsmetoder. Exempelplanen har en gaffel där du kan få skärmar som ser likadana ut. En skärm returnerar ett kontosaldo medan den andra skärmen returnerar ett felmeddelande.

Med designverktyget kan du lägga till igenkänningsattribut, till exempel en skärmtitel med namnet "Hämta kontosaldo", med hjälp av skärmigenkänningsredigeraren. När det gäller liknande skärmar behöver du andra attribut. Vid körning använder kopplingen dessa attribut för att bestämma grenen och gaffeln.

* I grenen som returnerar giltig indata, som är skärmen med kontosaldot, kan du lägga till ett fält som har ett "inte tomt" villkor.

* I grenen som returneras med ett fel kan du lägga till ett fält som har ett "tomt" villkor.

<a name="define-method"></a>

## <a name="define-methods"></a>Definiera metoder

I det här läget definierar du en metod som är associerad med navigeringsplanen. För varje metodparameter anger du datatypen, till exempel en sträng, heltal, datum eller tid och så vidare. När du är klar kan du testa din metod på den aktiva värden och bekräfta att metoden fungerar som förväntat. Du kan sedan generera filen metadatafil, eller HIDX-filen (Host Integration Designer), som nu har de metoddefinitioner som ska användas för att skapa och köra en åtgärd för IBM 3270-kopplingen.

1. I verktygsfältet 3270 designverktyg väljer du **Metoder** så att du går in i metodläge. 

1. Markera den skärm som har de inmatningsfält du vill använda i **navigeringsfönstret.**

1. Så här lägger du till den första indataparametern för metoden:

   1. I **fönstret Fånga,** på skärmen 3270 emulator, väljer du hela fältet, inte bara text inuti fältet, som du vill ha som första indata.

      > [!TIP]
      > Om du vill visa alla fält och se till att du markerar det fullständiga fältet väljer du **Alla fält på** **Visa-menyn** .

   1. Välj **Inmatningsfält**i designverktygets verktygsfält . 

   Om du vill lägga till fler indataparametrar upprepar du föregående steg för varje parameter.

1. Så här lägger du till den första utdataparametern för metoden:

   1. I **fönstret Fånga,** på skärmen 3270 emulator, väljer du hela fältet, inte bara text inuti fältet, som du vill ha som första utdata.

      > [!TIP]
      > Om du vill visa alla fält och se till att du markerar det fullständiga fältet väljer du **Alla fält på** **Visa-menyn** .

   1. Välj **Utdatafält**i designverktygets verktygsfält .

   Om du vill lägga till fler utdataparametrar upprepar du föregående steg för varje parameter.

1. När du har lagt till alla parametrar för metoden definierar du dessa egenskaper för varje parameter:

   | Egenskapsnamn | Möjliga värden | 
   |---------------|-----------------|
   | **Datatyp** | Byte, datumtid, decimal, int, lång, kort, sträng |
   | **Fältfyllningsteknik** | Parametrar stöder dessa fyllningstyper och fyll med ämnen om det behövs: <p><p>- **Typ**: Ange tecken sekventiellt i fältet . <p>- **Fyllning**: Ersätt fältets innehåll med tecken, fyll med tomma ämnen om det behövs. <p>- **EraseEofType**: Rensa fältet och ange sedan tecken sekventiellt i fältet . |
   | **Formatera sträng** | Vissa parameterdatatyper använder en formatsträng som informerar 3270-kopplingen om hur du konverterar text från skärmen till en .NET-datatyp: <p><p>- **DateTime**: Formatsträngen DateTime följer [.NET:s anpassade datum- och tidsformatsträngar](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Datumet `06/30/2019` använder till exempel formatsträngen `MM/dd/yyyy`. <p>- **Decimal**: Decimalformatsträngen använder [COBOL-bildsatsen](https://www.ibm.com/support/knowledgecenter/SS6SG3_5.2.0/com.ibm.cobol52.ent.doc/PGandLR/ref/rlddepic.html). Talet `100.35` använder till exempel formatsträngen `999V99`. |
   |||

## <a name="save-and-view-metadata"></a>Spara och visa metadata

När du har definierat din metod, men innan du testar metoden, spara all information som du har definierat hittills till en RAP-fil (.rap).
Du kan spara till den här RAP-filen när som helst under vilket läge som helst. Designverktyget innehåller också ett exempel RAP-fil som du kan öppna och granska genom att bläddra till designverktygets installationsmapp på denna plats och öppna filen "WoodgroveBank.rap":

`..\Program Files\Microsoft Host Integration Server - 3270 Design Tool\SDK\WoodgroveBank.rap`

Om du däremot försöker spara ändringar i exempelhÃ¤gg-filen RAP eller Ã¶dga en HIDX-fil frÃ¶r exempelfilen frÃ¶rs NÃ¤r filen finns i installationsmappen fÃ¶r designverktyget kan du få ett felmeddelande om att du har nekats.If you try saving changes to the sample RAP file or generating HIDX file from the sample RAP file while the the file stays in the design tool's installation folder, you might get an "accessdenied" error. Som standard installeras designverktyget i mappen Programfiler utan förhöjda behörigheter. Om du får ett felmeddelande kan du prova någon av följande lösningar:

* Kopiera exempelfilen till en annan plats.
* Kör designverktyget som administratör.
* Gör dig själv till ägare för SDK-mappen.

## <a name="test-your-method"></a>Testa din metod

1. Om du vill köra din metod mot den aktiva värden, medan du fortfarande är i metodläge, trycker du på F5-tangenten eller i designverktygets verktygsfält väljer **du Kör**.

   > [!TIP]
   > Du kan ändra lägen när som helst. Välj **Läge**på **Arkiv-menyn** och välj sedan önskat läge.

1. Ange parametrarnas värden och välj **OK**.

1. Om du vill fortsätta till nästa skärm väljer du **Nästa**.

1. När du är klar väljer du **Klar**, som visar dina utdataparametervärden.

<a name="add-metadata-integration-account"></a>

## <a name="generate-and-upload-hidx-file"></a>Generera och ladda upp HIDX-fil

När du är klar skapar du HIDX-filen så att du kan ladda upp till ditt integrationskonto. Designverktyget 3270 skapar HIDX-filen i en ny undermapp där du sparade RAP-filen.

1. Välj **Generera kod**i verktygsfältet 3270 designverktyget .

1. Gå till mappen som innehåller RAP-filen och öppna undermappen som verktyget skapade när du har genererat HIDX-filen. Bekräfta att verktyget har skapat HIDX-filen.

1. Logga in på [Azure-portalen](https://portal.azure.com)och hitta ditt integrationskonto.

1. Lägg till din HIDX-fil som en karta i ditt integrationskonto genom att [följa dessa liknande steg för att lägga till kartor](../logic-apps/logic-apps-enterprise-integration-liquid-transform.md), men när du väljer karttyp väljer du **HIDX**.

Senare i det här avsnittet, när du lägger till en IBM 3270-åtgärd i logikappen för första gången, uppmanas du att skapa en anslutning mellan logikappen och värdservern genom att tillhandahålla anslutningsinformation, till exempel namnen för ditt integrationskonto och värdserver . När du har skapat anslutningen kan du välja den tidigare tillagda HIDX-filen, metoden som ska köras och vilka parametrar som ska användas.

När du är klar med alla dessa steg kan du använda den åtgärd som du skapar i logikappen för att ansluta till IBM:s stordator, enhetsskärmar för din app, ange data, returnera resultat och så vidare. Du kan också fortsätta att lägga till andra åtgärder i logikappen för integrering med andra appar, tjänster och system.

<a name="run-action"></a>

## <a name="run-ibm-3270-actions"></a>Kör IBM 3270-åtgärder

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på [Azure-portalen](https://portal.azure.com)och öppna logikappen i Logic App Designer, om den inte redan är öppen.

1. Under det sista steget där du vill lägga till en åtgärd väljer du **Nytt steg**och väljer Lägg till **en åtgärd**. 

1. Välj **Enterprise**under sökrutan . Skriv "3270" som filter i sökrutan. Välj den här åtgärden i åtgärdslistan: **Kör ett stordatorprogram via en TN3270-anslutning**

   ![Välj 3270-åtgärd](./media/connectors-create-api-3270/select-3270-action.png)

   Om du vill lägga till en åtgärd mellan stegen flyttar du pekaren över pilen mellan stegen. 
   Välj plustecknet**+**( ) som visas och välj sedan **Lägg till en åtgärd**.

1. Om det ännu inte finns någon anslutning anger du nödvändig information för anslutningen och väljer **Skapa**.

   | Egenskap | Krävs | Värde | Beskrivning |
   |----------|----------|-------|-------------|
   | **Anslutningsnamn** | Ja | <*anslutningsnamn*> | Namnet på din anslutning |
   | **Id för integrationskonto** | Ja | <*integration-konto-namn*> | Namn på ditt integrationskonto |
   | **SAS-url för integrationskonto** | Ja | <*integration-konto-SAS-URL*> | Sas-URLn (Shared Access Signature) för ditt integrationskonto (Shared Access Signature), som du kan generera från inställningarna för integrationskontot i Azure-portalen. <p>1. På menyn för integrationskonto väljer du **Motringnings-URL**under **Inställningar**. <br>2. Kopiera url-värdet **genererad motringning** i den högra rutan. |
   | **Server** | Ja | <*TN3270-server-namn*> | Servernamnet för tjänsten TN3270 |
   | **Port** | Inga | <*TN3270-server-port*> | Porten som används av TN3270-servern. Om den lämnas `23` tom används kopplingen som standardvärde. |
   | **Enhetstyp** | Inga | <*IBM-terminal-modell*> | Modellnamnet eller numret för IBM-terminalen att emulera. Om den lämnas tom använder kopplingen standardvärden. |
   | **Kodsida** | Inga | <*kod-sidnummer*> | Teckentabellsnumret för värden. Om den lämnas `37` tom används kopplingen som standardvärde. |
   | **Namn på logisk enhet** | Inga | <*logiskt enhetsnamn*> | Det specifika logiska enhetsnamnet som ska begäras från värden |
   | **Aktivera SSL?** | Inga | På eller av | Aktivera eller inaktivera TLS-kryptering. |
   | **Validera värdorganisations ssl-certifikat?** | Inga | På eller av | Aktivera eller inaktivera validering för serverns certifikat. |
   ||||

   Ett exempel:

   ![Anslutningsegenskaper](./media/connectors-create-api-3270/connection-properties.png)

1. Lämna nödvändig information för åtgärden:

   | Egenskap | Krävs | Värde | Beskrivning |
   |----------|----------|-------|-------------|
   | **Hidx Namn** | Ja | <*HIDX-filnamn*> | Välj den 3270 HIDX-fil som du vill använda. |
   | **Metodnamn** | Ja | <*metodnamn*> | Välj den metod i HIDX-filen som du vill använda. När du har valt en metod visas listan **Lägg till ny parameter** så att du kan välja parametrar som ska användas med den metoden. |
   ||||

   Ett exempel:

   **Markera HIDX-filen**

   ![Välj HIDX-fil](./media/connectors-create-api-3270/select-hidx-file.png)

   **Välj metod**

   ![Välj metod](./media/connectors-create-api-3270/select-method.png)

   **Välj parametrar**

   ![Välj parametrar](./media/connectors-create-api-3270/add-parameters.png)

1. När du är klar sparar och kör du logikappen.

   När logikappen har körts visas stegen från körningen. 
   Lyckade steg visar bockmarkeringar, medan misslyckade steg visar bokstaven "X".

1. Om du vill granska indata och utdata för varje steg expanderar du det steget.

1. Om du vill granska utdata väljer du **Se råutdata**.

## <a name="connector-reference"></a>Referens för anslutningsapp

Mer teknisk information om den här kopplingen, till exempel utlösare, åtgärder och begränsningar enligt beskrivningen i kopplingens Swagger-fil, finns på [kopplingens referenssida](https://docs.microsoft.com/connectors/si3270/).

> [!NOTE]
> För logikappar i en [integrationstjänstmiljö (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)använder den här anslutningens ISE-märkta version [ISE-meddelandegränserna](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) i stället.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps-kopplingar](../connectors/apis-list.md)
