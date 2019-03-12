---
title: 'Självstudier: Bearbeta EDIFACT-fakturor med Azure BizTalk Services | Microsoft Docs'
description: Hur du skapar och konfigurerar Box anslutningen eller API-app och använda den i en logikapp i Azure App Service
services: biztalk-services
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: ''
ms.assetid: 7e0b93fa-3e2b-4a9c-89ef-abf1d3aa8fa9
ms.service: biztalk-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 05/31/2016
ms.author: deonhe
ms.openlocfilehash: 5eb9740bdd0543556265f54a1a37b632f79ac861
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57550130"
---
# <a name="tutorial-process-edifact-invoices-using-azure-biztalk-services"></a>Självstudier: Bearbeta EDIFACT-fakturor med Azure BizTalk Services

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Du kan använda BizTalk Services-portalen för att konfigurera och distribuera X12 och EDIFACT-avtal. I de här självstudierna tittar vi på hur du skapar ett EDIFACT-avtal för utbyte av fakturor mellan affärspartner. Den här självstudien skrivs runt en slutpunkt till slutpunkt affärslösning som inbegriper två handelspartner Northwind och Contoso att utbyta EDIFACT-meddelanden.  

## <a name="sample-based-on-this-tutorial"></a>Exemplet baseras på den här självstudien
Den här självstudien gäller runt ett exempel **skicka EDIFACT fakturor med hjälp av BizTalk Services**, som är tillgänglig för nedladdning från den [MSDN Code Gallery](https://go.microsoft.com/fwlink/?LinkId=401005). Du kan använda exemplet och gå igenom självstudierna för att förstå hur exemplet skapades. Eller du kan använda den här självstudien för att skapa din egen lösning grunden. Den här självstudien är riktade mot den andra metoden så att du förstår hur den här lösningen har skapats. Dessutom så mycket som möjligt i självstudiekursen är konsekvent med exemplet och använder samma namn för artefakter (till exempel scheman, transformeringar) som används i exemplet.  

> [!NOTE]
> Eftersom den här lösningen skickar ett meddelande från en EAI-brygga till ett EDI-brygga, återanvänds det [BizTalk Services-brygga länkning exempel](https://code.msdn.microsoft.com/BizTalk-Bridge-chaining-2246b104) exemplet.  
> 
> 

## <a name="what-does-the-solution-do"></a>Vad är lösningen?
I den här lösningen får Northwind EDIFACT-fakturor från Contoso. Dessa fakturor är inte i ett standardformat för EDI. Därför innan du skickar fakturan till Northwind, måste den omvandlas till ett dokument för EDIFACT-faktura (kallas även INVOIC). Om, måste Northwind bearbeta EDIFACT-faktura och returnera ett kontrollmeddelande (kallas även CONTRL) till Contoso.

![][1]  

För att uppnå det här scenariot för företag, använder Contoso funktioner som tillhandahålls med Microsoft Azure BizTalk Services.

* Contoso använder EAI-bryggor för att omvandla den ursprungliga fakturan till EDIFACT INVOIC.
* EAI-brygga skickar sedan meddelandet till en brygga för EDI-skicka som distribueras som en del av ett avtal som konfigurerats i BizTalk Services-portalen.
* EDI skicka bryggan bearbetar EDIFACT INVOIC och den vidare till exempeldatabasen.
* När du har fått fakturan får Northwind returnerar en CONTRL meddelande till EDI brygga som distribueras som en del av avtalet.  

> [!NOTE]
> Du kan också visas den här lösningen också hur du använder batchbearbetning för att skicka fakturor i batchar i stället för att varje fakturan separat.  
> 
> 

Om du vill slutföra scenariot måste använder vi Service Bus-köer för att skicka faktura från Contoso till Northwind eller få godkännande från exempeldatabasen. Dessa köer kan skapas med ett klientprogram som är tillgänglig för hämtning och ingår i det exemplet-paket som är tillgängliga som en del av den här självstudien.  

## <a name="prerequisites"></a>Förutsättningar
* Du måste ha ett Service Bus-namnområde. Mer information om hur du skapar ett namnområde, finns i [How To: Skapa eller ändra en Service Bus-tjänsten Namespace](https://msdn.microsoft.com/library/azure/hh674478.aspx). Låt oss anta att du redan har ett Service Bus-namnområde som etablerats kallas **edifactbts**.
* Du måste ha en prenumeration för BizTalk Services. I den här självstudien kan vi anta att du har en BizTalk Services-prenumeration, som kallas **contosowabs**.
* Registrera din BizTalk Services-prenumeration på BizTalk Services-portalen. Anvisningar finns i [registrerar en BizTalk Service-distribution på BizTalk Services-portalen](https://msdn.microsoft.com/library/hh689837.aspx)
* Du måste ha Visual Studio installerat.
* Du måste ha BizTalk Services SDK är installerat. Du kan ladda ned SDK: N från [https://go.microsoft.com/fwlink/?LinkId=235057](https://go.microsoft.com/fwlink/?LinkId=235057)  

## <a name="step-1-create-the-service-bus-queues"></a>Steg 1: Skapa Service Bus-köer
Den här lösningen använder Service Bus-köer för att utbyta meddelanden mellan affärspartner. Contoso och Northwind skicka meddelanden till köer från där EAI och/eller EDI-bryggor, använda dem. För den här lösningen behöver du tre Service Bus-köer:

* **northwindreceive** – Northwind får fakturan från Contoso över den här kön.
* **contosoreceive** – Contoso tar emot bekräftelsen från Northwind via den här kön.
* **uppehåll** – alla pausade meddelanden dirigeras till den här kön. Meddelanden gör uppehåll om de misslyckas under bearbetning.

Du kan skapa de här Service Bus-köer med hjälp av ett klientprogram som ingår i exempelpaketet.  

1. Öppna från den plats där du sparade exemplet, **självstudien skicka fakturor med hjälp av BizTalk Services EDI Bridges.sln**.
2. Tryck på **F5** att skapa och starta den **självstudien klienten** program.
3. Ange ACS för Service Bus-namnområde, Utfärdarens namn och nyckel på skärmen.
   
   ![][2]  
4. En meddelanderuta uppmanas att tre köer kommer att skapas i Service Bus-namnområdet. Klicka på **OK**.
5. Lämna självstudien klienten körs. Öppna klickar du på **Service Bus** > ***Service Bus-namnområdet*** > **köer**, och kontrollera att de tre köerna skapas.  

## <a name="step-2-create-and-deploy-trading-partner-agreement"></a>Steg 2: Skapa och distribuera handelspartneravtal
Skapa ett handelspartneravtal mellan Contoso och Northwind. Ett handelspartneravtal definierar ett trade avtal mellan de två affärspartner, till exempel vilka Meddelandeschema som ska användas, vilka meddelandeprotokoll ska användas osv. Ett handelspartneravtal innehåller två EDI-bryggor, en för att skicka meddelanden till handelspartner (kallas den **skicka EDI-brygga**) och en för att ta emot meddelanden från handelspartner (kallas den **EDI får brygga**).

I samband med den här lösningen, EDI skicka bryggan motsvarar sändningssidan i avtalet och används för att skicka EDIFACT-faktura från Contoso till exempeldatabasen. På samma sätt EDI receive bryggan motsvarar mottagarsidan i avtal och används för att ta emot bekräftelser från exempeldatabasen.  

### <a name="create-the-trading-partners"></a>Skapa handelspartner
Börja med skapa handelspartner för Contoso och Northwind.  

1. I BizTalk Services-portalen på den **partner** fliken **Lägg till**.
2. I den nya partner-sidan, anger du **Contoso** som Partnernamn och klicka sedan på **spara**.
3. Upprepa steg för att skapa den andra partnern **Northwind**.  

### <a name="create-the-agreement"></a>Skapa avtalet
Handelspartneravtal skapas mellan företag-profiler för handelspartner. Den här lösningen använder standardprofiler för partner som skapas automatiskt när vi skapade partner.  

1. I BizTalk Services-portalen klickar du på **avtal** > **Lägg till**.
2. I det nya avtalet **allmänna inställningar** anger värden som visas i bilden nedan, och klicka sedan på **Fortsätt**.
   
   ![][3]  
   
   När du klickar på **Fortsätt**, två flikar **ta emot inställningar** och **skicka inställningar** blir tillgängliga.
3. Skapa skicka avtalet mellan Contoso och Northwind. Detta avtal som reglerar hur Contoso skickar EDIFACT-faktura till exempeldatabasen.
   
   1. Klicka på **skicka inställningar**.
   2. Behåll standardvärdena på den **inkommande URL**, **transformera**, och **batchbearbetningen** flikar.
   3. På den **protokollet** fliken, under den **scheman** avsnittet, ladda upp den **EFACT_D93A_INVOIC.xsd** schema. Det här schemat är tillgänglig i exempel-paketet.
      
      ![][4]  
   4. På den **Transport** fliken, anger information om Service Bus-köer. Skicka sida-avtalet, använder vi den **northwindreceive** kö för att skicka EDIFACT-faktura till Northwind, och **har pausats** kö för att dirigera alla meddelanden som misslyckas under bearbetning och pausas. Du har skapat dessa köer i **steg 1: Skapa Service Bus-köer** (i det här avsnittet).
      
      ![][5]  
      
      Under **Transport Inställningar > Transport typ** och **inaktivering Meddelandeinställningar > Transport typ**väljer Azure Service Bus och ange värdena som visas i bilden.
4. Skapa receive avtalet mellan Contoso och Northwind. Detta avtal som reglerar hur Contoso får bekräftelsen från exempeldatabasen.
   
   1. Klicka på **Mottagningsinställningarna**.
   2. Behåll standardvärdena på den **Transport** och **transformera** flikar.
   3. På den **protokollet** fliken, under den **scheman** avsnittet, ladda upp den **EFACT_4.1_CONTRL.xsd** schema. Det här schemat är tillgänglig i exempel-paketet.
   4. På den **väg** fliken, skapa ett filter för att säkerställa att endast bekräftelser från Northwind dirigeras till Contoso. Under **vägar**, klickar du på **Lägg till** att skapa routning filtret.
      
      ![][6]  
      
      1. Ange värden för **Regelnamn**, **väg regeln**, och **väg mål** som visas i bilden.
      2. Klicka på **Spara**.
   5. På den **väg** fliken igen, ange var pausat bekräftelser (bekräftelser som inte under bearbetning) dirigeras till. Ange vilket transport till Azure Service Bus, dirigera måltypen till **kö**, autentiseringstyp för **signatur för delad åtkomst** (SAS), ange anslutningssträngen SAS för Service Bus-namnområde och ange sedan namnet på kön som **pausats**.
5. Klicka slutligen på **distribuera** att distribuera avtalet. Observera slutpunkterna där skicka och ta emot avtal att distribueras.
   
   * På den **skicka inställningar** fliken, under **inkommande URL**, Observera slutpunkten. Om du vill skicka ett meddelande från Contoso till exempeldatabasen med hjälp av EDI skicka bryggan, måste du skicka ett meddelande till den här slutpunkten.
   * På den **ta emot inställningar** fliken, under **Transport**, Observera slutpunkten. Skicka ett meddelande från exempeldatabasen till Contoso genom att använda EDI får brygga måste du skicka ett meddelande till den här slutpunkten.  

## <a name="step-3-create-and-deploy-the-biztalk-services-project"></a>Steg 3: Skapa och distribuera BizTalk Services-projekt
I föregående steg, distribueras EDI-skicka och ta emot avtal att bearbeta EDIFACT-fakturor och bekräftelser. Dessa avtal kan endast bearbeta meddelanden som överensstämmer med schemat för standard EDIFACT-meddelandet. Dock per scenariot för den här lösningen skickar Contoso en faktura till Northwind i ett internt upphovsrättsskyddad schema. Därför innan meddelandet skickas till EDI skicka bryggan, måste den omvandlas från interna schema i standard EDIFACT faktura schemat. BizTalk Services EAI-projektet har som.

BizTalk Services-projekt **InvoiceProcessingBridge**, att transformeringar meddelandet också ingår som en del av exemplet som du laddade ned. Projektet innehåller följande artefakter:

* **INHOUSEINVOICE. XSD** – schemat för den interna faktura som skickas till exempeldatabasen.
* **EFACT_D93A_INVOIC. XSD** – schemat för standard EDIFACT-faktura.
* **EFACT_4.1_CONTRL. XSD** – schemat för EDIFACT-bekräftelsen som Northwind skickar till Contoso.
* **INHOUSEINVOICE_to_D93AINVOIC. TRFM** – transformeringen som mappar det interna faktura schemat till standard EDIFACT faktura schemat.  

### <a name="create-the-biztalk-services-project"></a>Skapa BizTalk Services-projekt
1. Expandera InvoiceProcessingBridge projektet i Visual Studio-lösningen och öppna sedan den **MessageFlowItinerary.bcs** fil.
2. Klicka var som helst på arbetsytan och ange den **BizTalk-tjänstens URL** i egenskapsrutan för att ange ditt prenumerationsnamn för BizTalk Services. Till exempel `https://contosowabs.biztalk.windows.net`.
   
   ![][7]  
3. Dra från verktygslådan en **Xml One-Way brygga** på arbetsytan. Ange den **entitetsnamn** och **relativ adress** egenskaperna för bryggan till **ProcessInvoiceBridge**. Dubbelklicka på **ProcessInvoiceBridge** att öppna brygga configuration ytan.
4. I den **meddelandetyper** klickar du på plustecknet (**+**) för att ange schemat för det inkommande meddelandet. Eftersom det inkommande meddelandet för EAI-brygga är alltid interna fakturan, Ställ in på **INHOUSEINVOICE**.
   
   ![][8]  
5. Klicka på den **Xml-transformering** form, och i egenskapsrutan för den **Maps** egenskapen Klicka på ellipsen (**...** ) knappen. I den **Maps val av** dialogrutan den **INHOUSEINVOICE_to_D93AINVOIC** omvandla-filen och klicka sedan på **OK**.
   
   ![][9]  
6. Gå tillbaka till **MessageFlowItinerary.bcs**, och dra från verktygslådan en **tvåvägs externa tjänstens slutpunkt** till höger om den **ProcessInvoiceBridge**. Ställ in dess **entitetsnamn** egenskap **EDIBridge**.
7. I Solution Explorer expanderar du den **MessageFlowItinerary.bcs** och dubbelklicka på den **EDIBridge.config** fil. Ersätt innehållet i den **EDIBridge.config** med följande.
   
   > [!NOTE]
   > Varför behöver jag redigera .config-filen? Externa tjänsteslutpunkt som vi har lagts till på designerytan brygga representerar EDI-bryggor som vi distribuerade tidigare. EDI-bryggor är dubbelriktad bryggor med att skicka och ta emot sida. EAI-brygga som vi har lagt till brygga Designer är dock en enkelriktad brygga. Så för att hantera olika meddelande exchange mönstren i två bryggor använda vi ett anpassat bridge-beteende genom att inkludera dess konfiguration i .config-filen. Dessutom hanterar anpassade beteendet också autentiseringen till EDI skicka brygga slutpunkten. Det här anpassade beteendet är tillgänglig som en separat exemplet i [BizTalk Services-brygga länkning exempel – EAI till EDI](https://code.msdn.microsoft.com/BizTalk-Bridge-chaining-2246b104). Den här lösningen återanvänder exemplet.  
   > 
   > 
   
   ```
   <?xml version="1.0" encoding="utf-8"?>
   <configuration>
     <system.serviceModel>
       <extensions>
         <behaviorExtensions>
           <add name="BridgeAuthentication" 
                 type="Microsoft.BizTalk.Bridge.Behaviour.BridgeBehaviorElement, Microsoft.BizTalk.Bridge.Behaviour, Version=1.0.0.0, Culture=neutral, PublicKeyToken=ae58f69b69495c05" />
         </behaviorExtensions>
       </extensions>
       <behaviors>
         <endpointBehaviors>
           <behavior name="BridgeAuthenticationConfiguration">
             <!-- Enter the ACS namespace, issuer name and issuer secret of the BizTalk Services deployment -->
             <BridgeAuthentication acsnamespace="[YOUR ACS NAMESPACE]" 
                                   issuername="owner" 
                                   issuersecret="[YOUR ACS SECRET]" />
             <webHttp />
           </behavior>
         </endpointBehaviors>
       </behaviors>
       <bindings>
         <webHttpBinding>
           <binding name="BridgeBindingConfiguration">
             <security mode="Transport" />
           </binding>
         </webHttpBinding>
       </bindings>
       <client>
         <clear />
         <!--
           Go BizTalk Portal > Agreement > Send Settings > Inbound URL
           Copy the Endpoint URL and paste it in the below address field
         -->
         <endpoint name="TwoWayExternalServiceEndpointReference1" 
                   address="[YOUR EDI BRIDGE SEND URI]" 
                   behaviorConfiguration="BridgeAuthenticationConfiguration" 
                   binding="webHttpBinding" 
                   bindingConfiguration="BridgeBindingConfiguration" 
                   contract="System.ServiceModel.Routing.IRequestReplyRouter" />
       </client>
     </system.serviceModel>
   </configuration>
   
   ```
8. Uppdatera filen EDIBridge.config för att inkludera information om
   
   * Under *<behaviors>*, ange ACS-namnområde och nyckeln som associeras med BizTalk Services-prenumeration.
   * Under *<client>*, ge slutpunkten där det skicka EDI-avtalet har distribuerats.
   
   Spara ändringarna och Stäng konfigurationsfilen.
9. I verktygslådan klickar du på den **Connector** och ansluta den **ProcessInvoiceBridge** och **EDIBridge** komponenter. Välj kopplingen och i egenskapsdialogrutan, ange **filtervillkoret** till **matcha alla**. Detta säkerställer att alla meddelanden bearbetas av EAI-brygga dirigeras till EDI-brygga.
   
   ![][10]  
10. Spara ändringar i lösningen.  

### <a name="deploy-the-project"></a>Distribuera projektet
1. På den dator där du skapade BizTalk Services-projekt, hämta och installera SSL-certifikat för din BizTalk Services-prenumeration. Från BizTalk Services, klicka på under **instrumentpanelen**, och klicka sedan på **hämta SSL-certifikat**. Dubbelklicka på certifikatet och följ anvisningarna för att slutföra installationen. Kontrollera att du installerar certifikatet under **betrodda rotcertifikatutfärdare** certifikatarkiv.
2. I Visual Studio Solution Explorer högerklickar du på den **InvoiceProcessingBridge** projektet och klicka sedan på **distribuera**.
3. Ange värdena som visas i bild och klicka sedan på **distribuera**. Du kan hämta ACS-autentiseringsuppgifter för BizTalk Services genom att klicka på **anslutningsinformation** från BizTalk Services-instrumentpanel.
   
   ![][11]  
   
   Kopiera slutpunkten där EAI-brygga distribueras, till exempel från utdatafönstret `https://contosowabs.biztalk.windows.net/default/ProcessInvoiceBridge`. Du behöver den här slutpunkts-URL senare.  

## <a name="step-4-test-the-solution"></a>Steg 4: Testa lösningen
I det här avsnittet tittar vi på hur du testar lösningen med hjälp av den **självstudien klienten** program som tillhandahålls som en del av exemplet.  

1. I Visual Studio trycker du på F5 för att starta den **självstudien klienten**.
2. Skärmen måste ha förifyllda från steg värdena där vi skapade Service Bus-köer. Klicka på **Nästa**.
3. I fönstret nästa ger ACS-autentiseringsuppgifter för BizTalk Services-prenumeration och slutpunkterna där EAI och EDI (ta emot) bryggor distribueras.
   
   Du har kopierat EAI brygga slutpunkten i föregående steg. För EDI får bridge-slutpunkten i BizTalk Services-portalen, gå till avtalet > tar emot Inställningar > Transport > slutpunkt.
   
   ![][12]  
4. I fönstret nästa under Contoso, klickar du på den **skicka interna faktura** knappen. Öppna dialogrutan i filen, öppna filen INHOUSEINVOICE.txt. Granska innehållet i filen och klicka sedan på **OK** att skicka fakturan.
   
   ![][13]  
5. Fakturan tas emot med Northwind på några sekunder. Klicka på den **Visa meddelande** länk för att visa en tas emot av Northwind. Lägg märke till att fakturan tas emot av Northwind är i standard EDIFACT-schema vid det som skickades av Contoso är ett internt schema.
   
   ![][14]  
6. Välj fakturan och klicka sedan på **skicka bekräftelse**. Observera att utbytes-ID: n är samma i mottagen faktura och bekräftelse skickas i dialogrutan som visas. Klicka på OK i den **skicka bekräftelse** dialogrutan.
   
   ![][15]  
7. Bekräftelsen har tagits emot på Contoso på några sekunder.
   
   ![][16]  

## <a name="step-5-optional-send-edifact-invoice-in-batches"></a>Steg 5 (valfritt): Skicka EDIFACT faktura i batchar
BizTalk Services EDI bryggor stöder också massbearbetning av utgående meddelanden. Den här funktionen är användbar för att ta emot partner som vill få en grupp med meddelanden (uppfyller vissa villkor) i stället för enskilda meddelanden.

De viktigaste aspekterna när du arbetar med batchar är den faktiska versionen av batch, även kallat versionskriterierna. Versionsvillkor kan baseras på hur mottagaren vill få meddelanden. Om batchbearbetning är aktiverad, skickar EDI-brygga inte det utgående meddelandet till mottagaren tills versionskriterierna är uppfyllt. Till exempel ett batchbearbetningen villkor baserat på meddelandet storlek levererar en batch endast när n meddelanden grupperas. Ett batch-villkor kan också vara tidsbaserad, så att en batch skickas med en fast tidpunkt varje dag. I den här lösningen försöka vi meddelandestorlek baserat kriterier.

1. I BizTalk Services-portalen klickar du på det avtal som du skapade tidigare. Klicka på Skicka Inställningar > batchbearbetning > Lägg till Batch.
2. Batch-namn, ange **InvoiceBatch**, ange en beskrivning och klicka sedan på **nästa**.
3. Ange ett batch-villkor som definierar vilka meddelanden måste kunna batchbearbetas. I den här lösningen batch vi alla meddelanden. Därför väljer att använda avancerade alternativ för definitioner och ange **1 = 1**. Detta är ett villkor som alltid är SANT och kan därför alla meddelanden ska kunna batchbearbetas. Klicka på **Nästa**.
   
   ![][17]  
4. Ange en batch-släppningskriterierna. I listrutan väljer du **MessageCountBased**, och för **antal**, ange **3**. Det innebär att en grupp med tre meddelanden skickas till exempeldatabasen. Klicka på **Nästa**.
   
   ![][18]  
5. Granska sammanfattningen och klicka sedan på **spara**. Klicka på **distribuera** att distribuera om avtalet.
6. Gå tillbaka till den **självstudien klienten**, klickar du på **skicka interna faktura**, och följ anvisningarna för att skicka fakturan. Du ser att någon faktura tas emot med Northwind eftersom batchstorleken inte uppfylls. Upprepa detta två gånger, så att du har tre faktura-meddelanden som skickas till exempeldatabasen. Det här uppfyller batch-släppningskriterierna 3 meddelanden och du bör nu se en faktura i exempeldatabasen.

<!--Image references-->
[1]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-1.PNG  
[2]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-2.PNG  
[3]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-3.PNG
[4]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-4.PNG  
[5]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-5.PNG  
[6]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-6.PNG  
[7]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-7.PNG  
[8]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-8.PNG
[9]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-9.PNG  
[10]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-10.PNG  
[11]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-11.PNG  
[12]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-12.PNG  
[13]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-13.PNG
[14]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-14.PNG  
[15]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-15.PNG  
[16]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-16.PNG  
[17]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-17.PNG  
[18]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-18.PNG

