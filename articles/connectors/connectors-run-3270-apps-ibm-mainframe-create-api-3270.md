---
title: Ansluta till 3270 appar på IBM-stordatorer med Azure – Azure Logic Apps
description: Integrera och automatisera 3270 skärmen-drivna appar med Azure med Azure Logic Apps och IBM 3270-anslutningen
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: estfan, valthom
ms.topic: article
ms.date: 03/06/2019
tags: connectors
ms.openlocfilehash: fb61935c7e7b45699d4142293f31bbfe39e4cb78
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57457072"
---
# <a name="integrate-3270-screen-driven-apps-on-ibm-mainframes-with-azure-by-using-azure-logic-apps-and-ibm-3270-connector"></a>Integrera 3270 skärmen-drivna appar på IBM-stordatorer med Azure med Azure Logic Apps och IBM 3270-anslutningen

> [!NOTE]
> Den här anslutningsappen är [ *förhandsversion*](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

Du kan använda med Azure Logic Apps och IBM 3270 connector och köra IBM stordatorprogram appar som du vanligtvis få genom att navigera genom 3270 emulatorn skärmar. På så sätt kan du integrera dina appar för IBM-stordatorer med Azure, Microsoft, och andra appar, tjänster och system genom att skapa automatiserade arbetsflöden med Azure Logic Apps. Anslutningen kommunicerar med IBM-stordatorer med hjälp av TN3270-protokollet och är tillgänglig i alla Azure Logic Apps-regioner utom Azure Government och Azure Kina 21Vianet. Om du är nybörjare till logic apps, granska [vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

Den här artikeln beskrivs dessa aspekter för att använda anslutningstjänsten 3270: 

* Varför ska man använda IBM 3270-anslutning i Azure Logic Apps och hur anslutningsprogrammet körs 3270 skärmen-drivna appar

* Krav och installationsprogrammet för att använda anslutningstjänsten 3270

* Stegen för att lägga till 3270 anslutningsappsåtgärder till din logikapp

## <a name="why-use-this-connector"></a>Varför ska jag använda den här anslutningen?

För att komma åt appar på IBM-stordatorer, använder du vanligtvis en 3270 terminalemulator, ofta kallad en ”grön skärm”. Den här metoden är ett beprövade sätt men har begränsningar. Även om Host Integration Server (HIS) hjälper dig att arbeta direkt med dessa appar kan ibland att separera skärmen och affärslogik inte kanske möjligt. Eller så kanske du inte längre behöver information om hur värdprogram fungerar.

Om du vill utöka dessa scenarier, IBM 3270 anslutningen i Azure Logic Apps fungerar med verktyget 3270 Design, som du använder post eller ”fånga”, värd-skärmar som används för en viss uppgift, definiera navigeringsflödet vid filöverföring via appen stordatorprogram och definiera den metoder med indata- och parametrar för aktiviteten. I designverktyget konverterar den informationen till metadata som 3270 anslutningen används när du anropar en åtgärd som representerar aktiviteten från din logikapp.

När du har genererat metadatafilen från designverktyget kan du lägga till filen till ett integrationkonto i Azure. På så sätt kan logikappen kan komma åt din app metadata när du lägger till en 3270 anslutningstjänsten åtgärd. Kopplingen läser metadatafilen från ditt integrationskonto hanterar navigering via 3270 skärmar och dynamiskt anger parametrarna för 3270 anslutningstjänsten åtgärd. Du kan sedan ange data till värdprogrammet och anslutningsappen returnerar resultaten till din logikapp. På så sätt kan du integrera dina äldre appar med Azure, Microsoft, och andra appar, tjänster och system som har stöd för Azure Logic Apps.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du <a href="https://azure.microsoft.com/free/" target="_blank">registrera ett kostnadsfritt Azure-konto</a>.

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Rekommenderat: En [integreringstjänstmiljön (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) 

  Du kan välja den här miljön som plats för att skapa och köra din logikapp. En ISE ger åtkomst från din logikapp till resurser som skyddas i Azure-nätverk.

* Logikappen som ska använda för att automatisera och köra din 3270 skärmen modelldrivna appar

  IBM 3270 anslutningen inte ha utlösare, så Använd ännu en utlösare för att starta logikappen, till exempel den **upprepning** utlösaren. Du kan sedan lägga till 3270 anslutningsappsåtgärder. Du kommer igång [skapa en tom logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). 
  Om du använder en ISE kan du välja den ISE som logikappens plats.

* [Ladda ned och installera designverktyg 3270](https://aka.ms/3270-design-tool-download).
Den enda förutsättningen [Microsoft .NET Framework 4.6.1](https://aka.ms/net-framework-download).

  Det här verktyget kan du registrera skärmar, sökvägarna, metoder och parametrar för uppgifter i din app som du lägger till och kör som-3270 anslutningsappsåtgärder. Verktyget genererar en värd Integration Designer XML (HIDX)-fil som innehåller nödvändiga metadata för anslutningstjänsten att använda för att driva din stordatorprogram-app.
  
  Följ dessa steg för att ansluta till värden när du laddar ned och installera det här verktyget kan:

  1. Öppna verktyget 3270 Design. Från den **Session** menyn och välj **värden sessioner**.
  
  1. Ange värden TN3270 serverinformation.

* En [integrationskontot](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), vilket är den plats där du lagrar din HIDX-fil som en karta så att logikappen kan komma åt metadata och metoden definitioner i filen. 

  Kontrollera att din integrationskontot är länkat till logikappen som du använder. Om du använder en ISE kan se dessutom till att ditt integrationskonto platsen är samma ISE som använder din logikapp.

* Åtkomst till TN3270-servern som är värd för din stordatorprogram-app

<a name="define-app-metadata"></a>

## <a name="create-metadata-overview"></a>Skapa metadata-översikt

I en 3270 skärmen-drivna app är skärmar och datafält som unika för dina scenarier så 3270-kopplingen måste den här informationen om din app, som kan användas som metadata. Dessa metadata beskriver information som hjälper din logikapp identifiera och känna igen skärmar, beskriver hur du navigera mellan skärmar, var du indata och var du kan förvänta dig resultat. För att ange och generera dessa metadata, använder du verktyget 3270 Design som går igenom de här typerna *lägen*, eller faser, som beskrivs senare i mer information:

* **Avbilda**: I det här läget kan registrera du skärmar som krävs för att slutföra en uppgift med stordatorprogram appen, till exempel hämta en bank balans.

* **Navigering**: I det här läget kan ange du den plan eller sökvägen att navigera genom stordatorprogram appens skärmar för en viss aktivitet.

* **metoder**: I det här läget kan du definiera metoden, till exempel `GetBalance`, som beskriver navigeringssökvägen skärmen. Du kan också välja vilka fält på varje skärm som blir den metoden indata- och utdataparametrar.

### <a name="unsupported-elements"></a>Element som inte stöds

I designverktyget stöder inte dessa element:

* Partiell IBM grundläggande mappning Support (BMS) mappar: Om du importerar en BMS karta ignorerar designverktyget partiella skärmen definitioner.
* I/Out-parametrar: Du kan inte definiera In/ut-parametrar.
* Menyn bearbetar: Stöds inte i förhandsversionen
* Matris bearbetar: Stöds inte i förhandsversionen

<a name="capture-screens"></a>

## <a name="capture-screens"></a>Avbilda skärmar

I det här läget kan markera du ett objekt på varje skärm i 3270 som unikt identifierar skärmen. Du kan till exempel ange en rad med text eller en mer komplex uppsättning villkor, till exempel viss text och ett icke-tomma fält. Du kan registrera dessa skärmar över en live-anslutning till värdservern eller importera den här informationen från en IBM grundläggande mappning Support (BMS)-karta. Live-anslutning använder en TN3270-emulatorn för att ansluta till värden. Varje anslutningstjänsten åtgärd måste mappas till en enskild uppgift som startar med att ansluta till din session och slutar med att koppla från sessionen.

1. Om du inte redan gjort öppna 3270 designverktyg. I verktygsfältet, välja **avbilda** så att du anger bildtagningsläge.

1. Börja spela in genom att trycka på tangenten F5 eller från den **spela in** menyn och välj **börja spela in**. 

1. Från den **Session** menyn och välj **Connect**.

1. I den **avbilda** fönstret från och med den första skärmen i din app, gå igenom din app för en viss aktivitet som du spelar in.

1. När du har slutfört uppgiften logga ut från din app på vanligt sätt.

1. Från den **Session** menyn och välj **Disconnect**.

1. Stoppa inspelningen genom att trycka på SKIFT + F5-nycklar, eller från den **spela in** menyn och välj **Stoppa inspelning**.

   När du tagit skärmarna för en aktivitet kan visas i designern verktyget miniatyrer som representerar dessa skärmar. Några anteckningar om de här miniatyrbilderna:

   * Ingår i din hämtade skärmar, har du en skärm som heter ”tom”.

     När du först ansluter till [CICS](https://www.ibm.com/it-infrastructure/z/cics), måste du skicka nyckeln ”Rensa” innan du kan ange namnet på transaktionen som du vill köra. Skärmen där du skickar ”Rensa” nyckeln inte innehåller något *igenkänning av attribut*, till exempel en titel för skärmen som du kan lägga till med hjälp av Redigeraren för igenkänning av skärmen. Miniatyrbilderna innehåller en skärm med namnet ”Töm” som representerar den här skärmen. Du kan senare använda den här skärmen för att representera skärmen där du anger transaktionsnamnet.

   * Som standard använder det första ordet i namnet för en avbildade skärm på skärmen. Om samma namn redan finns lägger designverktyget namnet med ett understreck och ett nummer, till exempel ”WBGB” och ”WBGB_1”.

1. Följ dessa steg för att ge ett mer beskrivande namn till en avbildade skärm:

   1. I den **värden skärmar** fönstret väljer du den skärm som du vill byta namn på.

   1. I fönstret samma längst ned i fönstret samma hitta den **skärmnamn** egenskapen.

   1. Ändra skärmnamnet på aktuella till ett mer beskrivande namn.

1. Ange nu fält för att identifiera varje skärm.

   Med 3270 dataströmmen har skärmar inte standard identifierare, så du måste välja unika texten på varje skärm. Du kan ange flera villkor, till exempel unika texten och ett fält med ett specifikt villkor för avancerade scenarier.

När du har valt igenkänning av fälten, flytta till nästa läge.

### <a name="conditions-for-identifying-repeated-screens"></a>Villkor för att identifiera upprepade skärmar

För anslutningstjänsten att navigera och skilja mellan skärmar, hittar du på en skärm som du kan använda som en identifierare bland de hämtade skärmarna vanligtvis unika texten. Du kanske behöver mer identifieringsmetoder för upprepad skärmar. Anta exempelvis att du har två skärmar som ser likadana ut förutom en skärm returnerar ett giltigt värde, medan den andra skärmen returnerar ett felmeddelande.

Designverktyget och du kan lägga till *igenkänning av attribut*, till exempel en titel för skärmen, till exempel ”hämta saldo”, med hjälp av Redigeraren för igenkänning av skärmen. Om du har en förgrenade sökväg och båda grenar returnera samma skärm, men med olika resultat, behöver du andra igenkänning av attribut. Anslutningsappen använder dessa attribut för att fastställa den aktuella grenen och förgrening vid körning. Här följer de villkor som du kan använda:

* Specifikt värde: Det här värdet matchar den angivna strängen på den angivna platsen.
* Specifikt värde: Det här värdet matchar inte den angivna strängen på den angivna platsen.
* Tom: Det här fältet är tomt.
* INTE tomt: Det här fältet är inte tom.

Mer information finns i den [exempel navigering plan](#example-plan) senare i det här avsnittet.

<a name="define-navigation"></a>

## <a name="define-navigation-plans"></a>Definiera navigering planer

I det här läget kan definiera du flöde eller steg för att navigera genom stordatorprogram appens skärmar för en viss uppgift. Till exempel, ibland kan kan du ha mer än en sökväg som din app kan ta där en sökväg ger rätt resultat, medan andra sökvägen genererar ett fel. Ange tecknen behövs för att flytta till nästa skärm som för varje skärm `CICSPROD <enter>`.

> [!TIP]
> Om du automatiserar flera uppgifter som använder samma Anslut och koppla från skärmar innehåller designverktyget särskilda typer av ansluta och koppla från planer. När du definierar dessa planer kan du lägga till dem till avtalet navigering början och slutet.

### <a name="guidelines-for-plan-definitions"></a>Riktlinjer för plan definitioner

* Inkludera alla skärmar, från och med att ansluta och slutar med kopplar från.

* Du kan skapa ett fristående abonnemang eller använda ansluta och koppla från planerna som låter dig återanvända en serie med skärmar som är gemensamma för alla transaktioner.

  * Den sista skärmen i Connect planen måste vara samma skärm som den första skärmen i din plan för navigering.

  * Den första skärmen i Koppla från planen måste vara samma skärm som den sista skärmen i din plan för navigering.

* Din hämtade skärmar kan innehålla många upprepade skärmar, så Välj och använda endast en instans av alla upprepade skärmar i din plan. Här följer några exempel på upprepade skärmar:

  * Logga in skärmen, till exempel den **MSG – 10** skärmen
  * Välkomstskärmen för CICS
  * ”Rensa” eller **tom** skärmen

<a name="create-plans"></a>

### <a name="create-plans"></a>Skapa planer

1. Välj 3270 designverktyg verktygsfältet **navigering** så att du anger bläddringsläge.

1. Starta din plan i den **navigering** fönstret Välj **ny Plan**.

1. Under **Välj ny Plannamnet**, ange ett namn för din plan. Från den **typ** väljer du typ:

   | Plantyp | Beskrivning |
   |-----------|-------------|
   | **Process** | För fristående eller kombinerade planer |
   | **Anslut** | För Connect-planer |
   | **Koppla från** | För Disconnect-planer |
   |||

1. Från den **värden skärmar** fönstret, dra avbildade miniatyrbilderna till navigering plan lyfta fram i den **navigering** fönstret.

   Använd skärmen ”Töm” för att representera tom skärm där du anger transaktionsnamnet.

1. Ordna skärmar i den ordning som beskriver den aktivitet som du definierar.

1. Om du vill definiera flödet sökvägen mellan skärmar, inklusive förgreningar och kopplingar i designverktyget verktygsfältet och välj **Flow**.

1. Välj den första skärmen i flödet. Dra och rita en anslutning till nästa steg i flödet.

1. För varje skärm anger du värdena för den **stöd nyckeln** egenskapen (uppmärksamhet Identifier) och för den **fast Text** egenskapen, som flyttar flödet till nästa skärm.

   Du kanske bara stöd nyckeln, eller både stöd nyckel och fast text.

När du är klar med din navigering plan kan du [definiera metoder i nästa läge](#define-method).

<a name="example-plan"></a>

### <a name="example"></a>Exempel

Anta att du kör en CICS transaktion med namnet ”WBGB” som har de här stegen i det här exemplet: 

* På den första skärmen anger du ett namn och ett konto.
* På den andra skärmen visas kontosaldot.
* Du avslutar till skärmen ”Töm”.
* Du logga ut från CICS till skärmen ”MSG – 10”.

Anta också att du upprepa stegen, men du anger felaktiga data så att du kan avbilda skärmen som visar felet. Här följer skärmarna du avbildar:

* MSG-10
* CICS Welcome
* Tom
* WBGB_1 (indata)
* WBGB_2 (fel)
* Empty_1
* MSG-10_1

Även om många skärmar här få unika namn, är vissa av skärmarna samma skärm, till exempel ”MSG – 10” och ”tom”. Använda endast en instans för en upprepad skärm för skärmen i din plan. Här följer exempel som visar hur ett fristående abonnemang, Anslut plan, koppla från plan och en kombinerad plan kan se ut:

* Fristående prenumerationsavtal

  ![Fristående navigering plan](./media/connectors-create-api-3270/standalone-plan.png)

* Ansluta plan

  ![Ansluta plan](./media/connectors-create-api-3270/connect-plan.png)

* Koppla bort plan

  ![Koppla bort plan](./media/connectors-create-api-3270/disconnect-plan.png)

* Kombinerade plan

  ![Kombinerade plan](./media/connectors-create-api-3270/combined-plan.png)

#### <a name="example-identify-repeated-screens"></a>Exempel: Identifiera upprepade skärmar

För anslutningstjänsten till att navigera och särskilja skärmar, hittar du på en skärm som du kan använda som en identifierare för de avbildade skärmarna vanligtvis unika texten. Du kanske behöver mer identifieringsmetoder för upprepad skärmar. Exempel-plan har en förgrening där du kan få skärmar som ser ut ungefär. En skärm returnerar ett kontosaldo, medan den andra skärmen returnerar ett felmeddelande.

Designverktyget kan du lägga till igenkänning av attribut, till exempel en titel för skärmen med namnet ”Hämta saldo”, med hjälp av skärmen för redigeraren. I fallet med liknande skärmar behöver du andra attribut. Anslutningsappen använder dessa attribut för att fastställa grenen och förgrening vid körning.

* I grenen som returnerar giltiga indata som är skärmen med kontosaldot, kan du lägga till ett fält som har ett ”tom” villkor.

* I grenen som returnerar ett fel, kan du lägga till ett fält som har ett ”Töm” villkor.

<a name="define-method"></a>

## <a name="define-methods"></a>Definiera metoder

I det här läget kan definiera du en metod som är associerat med din plan för navigering. För varje Metodparametern anger du datatyp, till exempel sträng, heltal, datum och tid, och så vidare. När du är klar kan du testa din metod på live-värden och bekräfta att metoden inte fungerar som förväntat. Du generera sedan metadatafil eller värden Integration Designer XML (HIDX)-fil som har nu definitionerna metod du använder för att skapa och köra en åtgärd för IBM 3270-anslutningen.

1. Välj 3270 designverktyg verktygsfältet **metoder** så att du aktiverar metoder läget. 

1. I den **navigering** fönstret väljer du den skärm som innehåller indata fälten som du vill.

1. Följ dessa steg för att lägga till den första Indataparametern för metoden:

   1. I den **avbilda** fönstret på skärmen 3270 emulatorn väljer hela fältet, inte bara texten i fältet som du vill som första indata.

      > [!TIP]
      > Visa alla fält och se till att du väljer fältet färdigt på den **visa** menyn och välj **alla fält**.

   1. I designverktyget verktygsfältet **indata fältet**. 

   Upprepa föregående steg för att lägga till fler indataparametrar för varje parameter.

1. Följ dessa steg för att lägga till den första Utdataparametern för metoden:

   1. I den **avbilda** fönstret på skärmen 3270 emulatorn väljer hela fältet, inte bara texten i fältet som du vill som första utdata.

      > [!TIP]
      > Visa alla fält och se till att du väljer fältet färdigt på den **visa** menyn och välj **alla fält**.

   1. I designverktyget verktygsfältet **Utdatafältet**.

   Lägg till mer utdataparametrar genom att upprepa föregående steg för varje parameter.

1. Definiera egenskaperna för varje parameter när du har lagt till din metodens parametrar:

   | Egenskapsnamn | Möjliga värden | 
   |---------------|-----------------|
   | **Datatyp** | Byte, datum tid, Decimal, Int, Long, kort sagt: sträng |
   | **Fältet fyllning teknik** | Parametrar stöder typerna fyllning fylla med tomma värden om det behövs: <p><p>- **Typ**: Ange tecknen sekventiellt i fältet. <p>- **Fyll**: Ersätt innehållet i fälten med tecken, fylla med tomma värden om det behövs. <p>- **EraseEofType**: Avmarkera fältet och sedan ange sekventiellt tecknen i fältet. |
   | **Formatsträng** | Vissa typer av data använder en formatsträng som informerar 3270 kopplingen konvertera text från skärmen till en .NET-datatyp: <p><p>- **DateTime**: Formatsträngen som datum/tid följer den [.NET anpassat datum och tid formatsträngar](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Till exempel datum `06/30/2019` använder Formatsträngen `MM/dd/yyyy`. <p>- **Decimal**: Formatsträngen som decimal använder den [COBOL bild-satsen](https://www.ibm.com/support/knowledgecenter/SS6SG3_5.2.0/com.ibm.cobol52.ent.doc/PGandLR/ref/rlddepic.html). Till exempel hur många `100.35` använder Formatsträngen `999V99`. |
   |||

## <a name="save-and-view-metadata"></a>Spara och visa metadata

När du har definierat metoden, men innan du testar din metod, spara den information som du har definierat hittills till en RAP (.rap)-fil.
Du kan spara till den här RAP-filen när som helst under ett annat läge. I designverktyget innehåller också en exempelfil RAP som du kan öppna och visa genom att bläddra till installationsmappen för designverktyget på den här platsen och att öppna filen ”WoodgroveBank.rap”:

`..\Program Files\Microsoft Host Integration Server - 3270 Design Tool\SDK\WoodgroveBank.rap`

Om du sparar ändringar i RAP exempelfilen eller generera en HIDX-fil från RAP exempelfilen samtidigt som filen fortfarande är i installationsmappen för designverktyget, kan du få felmeddelandet ”åtkomst nekad”. I designverktyget installeras som standard i mappen Program utan förhöjd behörighet. Om du får ett fel kan du prova någon av följande lösningar:

* Kopiera exempelfilen till en annan plats.
* Kör designverktyget som administratör.
* Bli ägare till SDK-mappen.

## <a name="test-your-method"></a>Testa din metod

1. Tryck på F5 för att köra metoden mot live värden när du fortfarande i läget för metoder eller i designverktyget verktygsfältet och välj **kör**.

   > [!TIP]
   > Du kan ändra lägen när som helst. På den **filen** menyn och välj **läge**, och välj sedan det läge som du vill.

1. Ange din parametrarnas värden och välj **OK**.

1. Fortsätt till nästa steg att välja **nästa**.

1. När du är klar väljer **klar**, som visar dina utdataparametervärden.

<a name="add-metadata-integration-account"></a>

## <a name="generate-and-upload-hidx-file"></a>Generera och överföra HIDX-fil

När du är klar kan du generera HIDX filen så att du kan överföra till ditt integrationskonto. Designverktyg 3270 skapar HIDX-fil i en ny undermapp där du sparade RAP-filen.

1. Välj 3270 designverktyg verktygsfältet **generera kod**.

1. Gå till den mapp som innehåller din RAP-fil och öppna undermappen som verktyget skapade när du har genererat din HIDX-fil. Kontrollera att verktyget skapats HIDX-filen.

1. Logga in på den [Azure-portalen](https://portal.azure.com), och hitta ditt integrationskonto.

1. Lägg till HIDX filen som en karta till ditt konto för integrering av [följa de här liknande steg för att lägga till kartor](../logic-apps/logic-apps-enterprise-integration-liquid-transform.md), men när du väljer typen karta väljer **HIDX**.

Senare i det här avsnittet när du lägger till en IBM 3270 åtgärd i logikappen för första gången uppmanas du att skapa en anslutning mellan din logikapp och värdservern genom att tillhandahålla anslutningsinformation, till exempel namn för din integration-konto och värd-servern . När du har skapat anslutningen kan välja du din tillagd HIDX-fil, metoden för att köra och parametrar som ska användas.

När du är klar med alla de här stegen du använder åtgärden som du skapar i din logikapp för att ansluta till dina IBM-stordatorer, enhet skärmar för appen, ange data, returnerar resultat och så vidare. Du kan också fortsätta att lägga till andra åtgärder i din logikapp för att integrera med andra appar, tjänster och system.

<a name="run-action"></a>

## <a name="run-ibm-3270-actions"></a>Kör IBM 3270 åtgärder

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på den [Azure-portalen](https://portal.azure.com), och öppna logikappen i Logic App Designer, om inte redan är öppna.

1. Under det sista steget där du vill lägga till en åtgärd, väljer **nytt steg**, och välj **Lägg till en åtgärd**. 

1. Under sökrutan väljer **Enterprise**. I sökrutan anger du ”3270” som filter. Välj den här åtgärden från åtgärdslistan över: **Kör ett stordatorprogram program via en TN3270-anslutning**

   ![Välj 3270 åtgärd](./media/connectors-create-api-3270/select-3270-action.png)

   Om du vill lägga till en åtgärd mellan stegen, flyttar du pekaren över pilen mellan stegen. 
   Välj plustecknet (**+**) som visas och välj sedan **Lägg till en åtgärd**.

1. Om ingen anslutning finns ännu, ange nödvändig information för din anslutning och välj **skapa**.

   | Egenskap  | Krävs | Value | Beskrivning |
   |----------|----------|-------|-------------|
   | **Anslutningsnamn** | Ja | <*connection-name*> | Namn för anslutningen |
   | **Integrationskonto-ID** | Ja | <*integration-account-name*> | Namn på ditt integrationskonto |
   | **SAS-URL för Integrationskontot** | Ja | <*integration-account-SAS-URL*> | Ditt integrationskonto signatur för delad åtkomst (SAS) URL som du kan generera från ditt integrationskonto inställningar i Azure-portalen. <p>1. På din integration account menyn under **inställningar**väljer **Motringnings-URL för**. <br>2. I den högra rutan, kopierar den **genereras Motringnings-URL** värde. |
   | **Server** | Ja | <*TN3270-server-name*> | Servernamnet för din TN3270-tjänst |
   | **Port** | Nej | <*TN3270-server-port*> | Den port som används av TN3270-servern. Om inget anges används anslutningsappen använder `23` som standardvärde. |
   | **Typ av enhet** | Nej | <*IBM-terminal-model*> | Modellnamn eller nummer för IBM-terminalen för att emulera. Om inget anges används använder anslutningsappen standardvärden. |
   | **Teckentabell** | Nej | <*code-page-number*> | Teckentabell för värden. Om inget anges används anslutningsappen använder `37` som standardvärde. |
   | **Namn på logisk lagringsenhet** | Nej | <*logical-unit-name*> | Specifik logisk enhetsnamnet att begära från värden |
   | **Aktivera SSL?** | Nej | Aktivera eller inaktivera | Aktivera eller inaktivera SSL-kryptering. |
   | **Verifiera värden ssl-certifikat?** | Nej | Aktivera eller inaktivera | Aktivera eller inaktivera validering för serverns certifikat. |
   ||||

   Exempel:

   ![Anslutningsegenskaper](./media/connectors-create-api-3270/connection-properties.png)

1. Ange informationen som krävs för åtgärden:

   | Egenskap  | Krävs | Value | Beskrivning |
   |----------|----------|-------|-------------|
   | **Hidx namn** | Ja | <*HIDX-file-name*> | Välj den 3270 HIDX-fil som du vill använda. |
   | **Metodnamn** | Ja | <*method-name*> | Välj metoden i HIDX-filen som du vill använda. När du har valt en metod i **Lägg till ny parameter** lista visas så att du kan välja parametrar som ska användas med metoden. |
   ||||

   Exempel:

   **Välj filen HIDX**

   ![Välj HIDX-fil](./media/connectors-create-api-3270/select-hidx-file.png)

   **Välj metoden**

   ![Välj metod](./media/connectors-create-api-3270/select-method.png)

   **Välj parametrar**

   ![Välj parametrar](./media/connectors-create-api-3270/add-parameters.png)

1. När du är klar, spara och kör din logikapp.

   När du har dina logic app är klar, stegen från kör visas. 
   Lyckad stegen visar bockar, medan misslyckade stegen visar bokstaven ”X”.

1. Expandera det steget och granska indata och utdata för varje steg.

1. Om du vill granska utdata, Välj **finns i råutdata**.

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om utlösare och åtgärder gränsen som beskrivs av anslutningsappens OpenAPI (tidigare Swagger) beskrivning, granska kopplingens [referenssida](/connectors/<*replace-with-api-topic-file-name*>).

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* Läs mer om andra [Logic Apps-anslutningsprogram](../connectors/apis-list.md)
