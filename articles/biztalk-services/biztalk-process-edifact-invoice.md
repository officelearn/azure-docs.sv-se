---
title: "Självstudier: Bearbeta EDIFACT fakturor med Azure BizTalk-tjänst | Microsoft Docs"
description: "Hur du skapar och konfigurerar rutan kopplingen eller API-app och använda den i en logikapp i Azure App Service"
services: biztalk-services
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
ms.assetid: 7e0b93fa-3e2b-4a9c-89ef-abf1d3aa8fa9
ms.service: biztalk-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 05/31/2016
ms.author: deonhe
ms.openlocfilehash: 4597ee28e4c3b797c0ab050b21a126a95d9e8191
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-process-edifact-invoices-using-azure-biztalk-services"></a>Självstudier: Processen EDIFACT fakturor med hjälp av Azure BizTalk-tjänst

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Du kan använda BizTalk-Services-portalen för att konfigurera och distribuera X12 och EDIFACT-avtal. I den här självstudiekursen kommer titta vi på hur du skapar ett EDIFACT-avtal för att utbyta fakturor mellan affärspartner. Den här självstudiekursen skrivs runt en slutpunkt till slutpunkt affärslösning som inbegriper två handelspartners Northwind och Contoso att utbyta EDIFACT-meddelanden.  

## <a name="sample-based-on-this-tutorial"></a>Exempel baserat på den här självstudiekursen
Den här självstudiekursen skrivs runt ett prov, **skicka EDIFACT fakturor med BizTalk-tjänst**, som är tillgänglig för nedladdning från den [MSDN Code Gallery](http://go.microsoft.com/fwlink/?LinkId=401005). Du kan Använd exemplet och gå igenom självstudierna för att förstå hur exemplet skapades. Eller, du kan använda den här självstudiekursen för att skapa din egen lösning grunden. Den här kursen är riktad mot den andra metoden så att du förstår hur den här lösningen har skapats. Dessutom så mycket som möjligt kursen är konsekvent med exemplet och använder samma namn för artefakter (till exempel scheman, transformeringar) som används i exemplet.  

> [!NOTE]
> Eftersom den här lösningen skickar ett meddelande från en EAI bridge till EDI-brygga, återanvänds det [BizTalk Services Bridge länkning exempel](http://code.msdn.microsoft.com/BizTalk-Bridge-chaining-2246b104) exempel.  
> 
> 

## <a name="what-does-the-solution-do"></a>Vad är lösningen?
I den här lösningen får Northwind EDIFACT fakturor från Contoso. Dessa fakturor är inte i ett standard EDI-format. Så, innan du skickar fakturan till Northwind, den måste omvandlas till ett dokument för EDIFACT-faktura (kallas även INVOIC). Erhåller, måste Northwind bearbeta EDIFACT-faktura och returnera ett kontrollmeddelande (kallas även CONTRL) till Contoso.

![][1]  

Contoso använder funktioner som tillhandahålls med Microsoft Azure BizTalk-tjänst för att uppnå det här scenariot för företag.

* Contoso använder EAI bryggor för att omvandla den ursprungliga fakturan till EDIFACT INVOIC.
* EAI bryggan sedan skickar meddelandet till en EDI skicka brygga som distribueras som en del av ett avtal som konfigurerats i BizTalk-Services-portalen.
* EDI skicka bridge bearbetar EDIFACT INVOIC och skickar den till Northwind.
* När du har fått fakturan får Northwind returnerar en CONTRL meddelandet till EDI brygga som distribueras som en del av avtalet.  

> [!NOTE]
> Du kan också visar den här lösningen även hur du använder batchbearbetning för att skicka fakturor i batchar, i stället för att skicka varje faktura separat.  
> 
> 

Vi använder Service Bus-köer för att skicka faktura från Contoso till Northwind eller ta emot bekräftelse från exempeldatabasen för att slutföra Scenarionamn. Dessa köer kan skapas med ett klientprogram som är tillgänglig för hämtning och ingår i exempel-paket som är tillgängliga som en del av den här kursen.  

## <a name="prerequisites"></a>Krav
* Du måste ha en Service Bus-namnrymd. Anvisningar om hur du skapar ett namnområde finns [hur man: skapa eller ändra en Service Bus-tjänsten Namespace](https://msdn.microsoft.com/library/azure/hh674478.aspx). Låt oss anta att du redan har ett namnområde för Service Bus etableras, kallas **edifactbts**.
* Du måste ha en prenumeration BizTalk-tjänst. Instruktioner finns i [skapa en BizTalk Service med hjälp av Azure klassiska portal](http://go.microsoft.com/fwlink/?LinkID=302280). För den här självstudiekursen kommer vi förutsätter att du har en BizTalk-tjänst-prenumeration som kallas **contosowabs**.
* Registrera prenumerationen BizTalk-tjänst för BizTalk-Services-portalen. Instruktioner finns i [registrerar en BizTalk Tjänstdistribution på BizTalk-Services-portalen](https://msdn.microsoft.com/library/hh689837.aspx)
* Du måste ha Visual Studio installerat.
* Du måste ha BizTalk Services SDK är installerat. Du kan hämta SDK från [http://go.microsoft.com/fwlink/?LinkId=235057](http://go.microsoft.com/fwlink/?LinkId=235057)  

## <a name="step-1-create-the-service-bus-queues"></a>Steg 1: Skapa Service Bus-köer
Den här lösningen använder Service Bus-köer för att utbyta meddelanden mellan affärspartner. Contoso och Northwind skicka meddelanden till köerna från där EAI-och/eller EDI-bryggor använda dem. Du behöver tre Service Bus-köer för den här lösningen:

* **northwindreceive** – Northwind tar emot fakturan från Contoso via den här kön.
* **contosoreceive** – Contoso tar emot bekräftelsen från exempeldatabasen via den här kön.
* **avbruten** – alla avbrutna meddelanden vidarebefordras till den här kön. Meddelanden gör uppehåll om de inte under bearbetning.

Du kan skapa dessa Service Bus-köer med hjälp av ett klientprogram i exempel-paketet.  

1. Den plats där du sparade exemplet, öppna **självstudiekursen skickar fakturor med BizTalk Services EDI Bridges.sln**.
2. Tryck på **F5** att skapa och starta den **kursen klienten** program.
3. Ange ACS för Service Bus-namnområde, Utfärdarens namn och nyckeln för utfärdaren på skärmen.
   
   ![][2]  
4. En meddelanderuta uppmanas att tre köer kommer att skapas i Service Bus-namnrymd. Klicka på **OK**.
5. Lämna kursen klienten körs. Öppna klickar du på **Service Bus** > ***Service Bus-namnrymd*** > **köer**, och kontrollera att de tre köerna skapas.  

## <a name="step-2-create-and-deploy-trading-partner-agreement"></a>Steg 2: Skapa och distribuera handelspartneravtal
Skapa ett handelspartneravtal mellan Contoso och Northwind. Ett handelspartneravtal definierar ett handel avtal mellan två affärspartner, till exempel vilka meddelandet schemat ska användas, vilket protokoll som meddelanden ska användas osv. Ett handelspartneravtal innehåller två EDI-bryggor, en för att skicka meddelanden till handelspartner (kallas den **EDI skicka bridge**) och en för att ta emot meddelanden från handelspartner (kallas den **EDI får bridge**).

I samband med den här lösningen EDI skicka bridge motsvarar sändningssidan i avtalet och används för att skicka EDIFACT-faktura från Contoso till exempeldatabasen. På liknande sätt EDI receive bridge motsvarar mottagarsidan i avtalet och används för att ta emot bekräftelser från exempeldatabasen.  

### <a name="create-the-trading-partners"></a>Skapa handelspartner
Börja med, skapa handelspartner för Contoso och Northwind.  

1. BizTalk-Services-portalen på den **Partners** klickar du på **Lägg till**.
2. På sidan partner, ange **Contoso** som Partnernamn och klicka sedan på **spara**.
3. Upprepa steg för att skapa den andra partnern **Northwind**.  

### <a name="create-the-agreement"></a>Skapa avtal
Handelspartneravtal skapas mellan företag-profiler för handelspartner. Den här lösningen använder standard partner profiler som skapas automatiskt när vi skapade partners.  

1. I BizTalk-Services-portalen klickar du på **avtal** > **Lägg till**.
2. I det nya avtalet **allmänna inställningar** sidan, anger du värdena som visas i bilden nedan och klickar sedan på **Fortsätt**.
   
   ![][3]  
   
   När du klickar på **Fortsätt**, två flikar **tar emot inställningar** och **skicka inställningar** blir tillgängliga.
3. Skapa skicka avtalet mellan Contoso och Northwind. Det här avtalet styr hur Contoso skickar EDIFACT-fakturan till exempeldatabasen.
   
   1. Klicka på **skicka inställningar**.
   2. Behåll standardinställningarna på den **inkommande URL**, **transformera**, och **Batching** flikar.
   3. På den **protokollet** fliken, under den **scheman** avsnittet laddar du upp den **EFACT_D93A_INVOIC.xsd** schema. Det här schemat är tillgänglig med exempel paketet.
      
      ![][4]  
   4. På den **Transport** anger information för Service Bus-köer. För sändningssidan avtalet, vi använder den **northwindreceive** kön att skicka EDIFACT-fakturan till Northwind, och **avbruten** kön att vidarebefordra meddelanden som misslyckas under bearbetningen och har avbrutits. Du har skapat dessa köer i **steg 1: skapa Service Bus-köer** (i det här avsnittet).
      
      ![][5]  
      
      Under **Transport Inställningar > transporttypen** och **upphävande Meddelandeinställningar > transporttypen**väljer Azure Service Bus och ange värden som visas i bilden.
4. Skapa receive-avtal mellan Contoso och Northwind. Det här avtalet styr hur Contoso får bekräftelsen från exempeldatabasen.
   
   1. Klicka på **ta emot inställningarna**.
   2. Behåll standardinställningarna på den **Transport** och **transformera** flikar.
   3. På den **protokollet** fliken, under den **scheman** avsnittet laddar du upp den **EFACT_4.1_CONTRL.xsd** schema. Det här schemat är tillgänglig med exempel paketet.
   4. På den **väg** fliken, skapa ett filter för att säkerställa att endast bekräftelser från exempeldatabasen dirigeras till Contoso. Under **vägar**, klickar du på **Lägg till** att skapa routning filtret.
      
      ![][6]  
      
      1. Ange värden för **Regelnamn**, **väg regeln**, och **väg mål** som visas i bilden.
      2. Klicka på **Spara**.
   5. På den **väg** igen och ange var pausat bekräftelser (bekräftelser som inte under bearbetning) dirigeras till. Ange vilket transport till Azure Service Bus, vidarebefordra måltypen till **kön**, autentiseringstypen som **signatur för delad åtkomst** (SAS), ange en SAS-anslutningssträng för Service Bus-namnrymd och ange sedan namnet på kön som **avbruten**.
5. Klicka slutligen på **distribuera** att distribuera avtalet. Observera slutpunkterna där skicka och ta emot avtal distribueras.
   
   * På den **skicka inställningar** fliken, under **inkommande URL**, Lägg märke till slutpunkten. Om du vill skicka ett meddelande från Contoso till Northwind med EDI skicka bridge, måste du skicka ett meddelande till den här slutpunkten.
   * På den **tar emot inställningar** fliken, under **Transport**, Lägg märke till slutpunkten. Skicka ett meddelande från exempeldatabasen till Contoso med hjälp av EDI får bridge, måste du skicka ett meddelande till den här slutpunkten.  

## <a name="step-3-create-and-deploy-the-biztalk-services-project"></a>Steg 3: Skapa och distribuera projektet BizTalk-tjänst
I det föregående steget, distribueras EDI-skicka och ta emot avtal att bearbeta EDIFACT fakturor och bekräftelser. Dessa avtal kan endast bearbeta meddelanden som följer standard EDIFACT meddelandet schemat. Dock per scenario för den här lösningen skickar Contoso en faktura till Northwind i ett egna interna schema. Så innan meddelandet skickas till EDI skicka bridge, måste den konverteras från interna schema standard EDIFACT faktura schemat. BizTalk-tjänster EAI-projektet har som.

BizTalk-tjänst-projektet **InvoiceProcessingBridge**, att transformeringar meddelandet är också ingår i exemplet som du hämtat. Projektet innehåller följande artefakter:

* **INHOUSEINVOICE. XSD** – schemat för den interna faktura som skickas till exempeldatabasen.
* **EFACT_D93A_INVOIC. XSD** – schemat för standard EDIFACT fakturan.
* **EFACT_4.1_CONTRL. XSD** – schemat för EDIFACT-bekräftelse som Northwind skickar till Contoso.
* **INHOUSEINVOICE_to_D93AINVOIC. TRFM** -transformeringen som mappar interna faktura schemat till standard EDIFACT faktura schemat.  

### <a name="create-the-biztalk-services-project"></a>Skapa projektet BizTalk-tjänst
1. Expandera InvoiceProcessingBridge projektet i Visual Studio-lösningen och öppna den **MessageFlowItinerary.bcs** fil.
2. Klicka någonstans på arbetsytan och ange den **BizTalk-tjänst-URL** i egenskapsrutan för att ange din prenumerationsnamn BizTalk-tjänst. Till exempel `https://contosowabs.biztalk.windows.net`.
   
   ![][7]  
3. Dra från verktygslådan en **Xml One-Way Bridge** till arbetsytan. Ange den **entitetsnamnet** och **relativ adress** egenskaper för bryggan till **ProcessInvoiceBridge**. Dubbelklicka på **ProcessInvoiceBridge** att öppna bridge configuration ytan.
4. I den **meddelandetyper** klickar du på plustecknet (**+**) för att ange schemat för det inkommande meddelandet. Eftersom det inkommande meddelandet för EAI bridge alltid är interna fakturan väljer **INHOUSEINVOICE**.
   
   ![][8]  
5. Klicka på den **XML-transformeringen** form, och i egenskapsrutan för den **Maps** egenskap, klicka på knappen (**...** ) knappen. I den **Maps markeringen** dialogrutan markerar du den **INHOUSEINVOICE_to_D93AINVOIC** transformeringsfilen och klicka sedan på **OK**.
   
   ![][9]  
6. Gå tillbaka till **MessageFlowItinerary.bcs**, och dra från verktygslådan en **tvåvägs externa tjänstslutpunkten** till höger om den **ProcessInvoiceBridge**. Ange dess **entitetsnamnet** egenskapen **EDIBridge**.
7. I Solution Explorer expanderar den **MessageFlowItinerary.bcs** och dubbelklicka på den **EDIBridge.config** fil. Ersätta innehållet i den **EDIBridge.config** med följande.
   
   > [!NOTE]
   > Varför måste redigera .config-filen? Den externa tjänstslutpunkten som vi har lagts till på designerytan bridge representerar EDI-bryggor som vi tidigare har distribuerats. EDI bryggor är dubbelriktad bryggor med en skicka och ta emot sida. EAI-brygga som vi har lagt till bridge Designer är dock en enkelriktad brygga. Så om du vill hantera olika meddelandet exchange mönster av två platslänkbryggor, använder vi en anpassad bridge beteende genom att inkludera dess konfiguration i .config-filen. Dessutom hanterar anpassade beteendet också autentiseringen till EDI skicka bridge slutpunkten. Detta beteende är tillgänglig som en separat exempelprogram på [BizTalk Services Bridge exempel - EAI till EDI-länkning](http://code.msdn.microsoft.com/BizTalk-Bridge-chaining-2246b104). Den här lösningen återanvänder exemplet.  
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
8. Uppdatera EDIBridge.config-filen om du vill inkludera konfigurationsinformation
   
   * Under  *<behaviors>* , ange ACS-namnområde och nyckeln som associeras med prenumerationen BizTalk-tjänst.
   * Under  *<client>* , ange slutpunkten där EDI skicka avtalet har distribuerats.
   
   Spara ändringarna och Stäng konfigurationsfilen.
9. Från verktygslådan klickar du på den **Connector** och ansluta den **ProcessInvoiceBridge** och **EDIBridge** komponenter. Välj kopplingen och egenskaper i rutan Ange **filtervillkor** till **matcha alla**. Detta säkerställer att alla meddelanden som bearbetas av EAI bryggan dirigeras till EDI-bridge.
   
   ![][10]  
10. Spara ändringar i lösningen.  

### <a name="deploy-the-project"></a>Distribuera projektet
1. Hämta och installera SSL-certifikatet för prenumerationen BizTalk-tjänst på datorn där du skapade BizTalk-Services-projekt. Från, under BizTalk-tjänst klickar du på **instrumentpanelen**, och klicka sedan på **hämta SSL-certifikat**. Dubbelklicka på certifikatet och följ anvisningarna för att slutföra installationen. Kontrollera att du installerar certifikatet under **betrodda rotcertifikatutfärdare** certifikatarkiv.
2. I Visual Studio Solution Explorer högerklickar du på den **InvoiceProcessingBridge** projektet och klicka sedan på **distribuera**.
3. Ange värden som visas i bilden och klickar sedan på **distribuera**. Du kan hämta ACS-autentiseringsuppgifter för BizTalk-tjänst genom att klicka på **anslutningsinformationen** från instrumentpanelen BizTalk-tjänst.
   
   ![][11]  
   
   Kopiera från fönstret utdata slutpunkten där EAI bryggan distribueras, till exempel `https://contosowabs.biztalk.windows.net/default/ProcessInvoiceBridge`. Du behöver den här slutpunkts-URL senare.  

## <a name="step-4-test-the-solution"></a>Steg 4: Testa lösningen
I det här avsnittet vi titta på hur du testar lösningen med hjälp av den **kursen klienten** program som ingår i exemplet.  

1. I Visual Studio trycker du på F5 för att starta den **kursen klienten**.
2. Skärmen måste ha värden förinställd från steg där vi skapade Service Bus-köer. Klicka på **Nästa**.
3. I fönstret nästa ange ACS-autentiseringsuppgifter för abonnemang BizTalk- och slutpunkterna där EAI- och EDI (får) bryggor distribueras.
   
   Du har kopierat EAI bridge slutpunkten i föregående steg. För EDI får bridge slutpunkt i BizTalk-Services-portalen, går du till avtalet > få Inställningar > Transport > slutpunkt.
   
   ![][12]  
4. I fönstret nästa under Contoso, klickar du på den **skicka interna faktura** knappen. Öppna dialogrutan, öppna filen INHOUSEINVOICE.txt i filen. Granska innehållet i filen och klicka sedan på **OK** att skicka fakturan.
   
   ![][13]  
5. Fakturan tas emot på Northwind inom några sekunder. Klicka på den **Visa meddelande** länk för att visa fakturan som tagits emot av Northwind. Lägg märke till att fakturan som tagits emot av Northwind är i standard EDIFACT-schemat som skickades av Contoso var ett interna schema.
   
   ![][14]  
6. Välj faktura och klicka sedan på **skicka bekräftelse**. Observera att interchange-ID är samma i mottagen faktura och bekräftelse skickas i dialogrutan som öppnas. Klicka på OK i den **skicka bekräftelse** dialogrutan.
   
   ![][15]  
7. Bekräftelsen har tagits emot på Contoso inom några sekunder.
   
   ![][16]  

## <a name="step-5-optional-send-edifact-invoice-in-batches"></a>Steg 5 (valfritt): skicka EDIFACT faktura i batchar
BizTalk-tjänster EDI bryggor stöder också massbearbetning av utgående meddelanden. Den här funktionen är användbart för att ta emot partners som vill få en grupp med meddelanden (uppfyller vissa villkor) i stället för enskilda meddelanden.

De viktigaste aspekterna när du arbetar med batchar är den faktiska versionen av batchen, kallas även kriterier för versionen. Kriterierna som versionen kan baseras på hur mottagaren vill få meddelanden. Om batchbearbetning aktiveras skickar EDI-bridge inte det utgående meddelandet till mottagaren tills versionen villkor är uppfyllt. Till exempel batching villkor baserat på meddelandet storlek levererar en batch endast när n meddelanden grupperas. Ett batch-villkor kan också vara tidsbaserade, så att en batch skickas med en viss tid varje dag. I den här lösningen försök vi meddelandestorlek baserade villkor.

1. Klicka på det avtal som du skapade tidigare i BizTalk-Services-portalen. Klicka på Skicka Inställningar > batchbearbetning > Lägg till Batch.
2. Batch-namn, ange **InvoiceBatch**, ange en beskrivning och klicka sedan på **nästa**.
3. Ange ett batch-villkor som definierar vilka meddelanden måste vara batchar. I den här lösningen batch vi alla meddelanden. Så markerar du kryssrutan Använd avancerade definitioner alternativet och ange **1 = 1**. Detta är ett villkor som alltid är true och därför alla meddelanden som ska grupperas. Klicka på **Nästa**.
   
   ![][17]  
4. Ange ett villkor för batch-versionen. Välj i listrutan **MessageCountBased**, och för **antal**, ange **3**. Det innebär att en grupp med tre meddelanden ska skickas till exempeldatabasen. Klicka på **Nästa**.
   
   ![][18]  
5. Granska sammanfattningen och klicka sedan på **spara**. Klicka på **distribuera** distribuera avtalet.
6. Gå tillbaka till den **kursen klienten**, klickar du på **skicka interna faktura**, och följ anvisningarna för att skicka fakturan. Du ser att någon faktura tas emot på Northwind eftersom batchstorleken inte är uppfyllt. Upprepa detta två gånger, så att du har tre faktura meddelanden som skickas till exempeldatabasen. Det här uppfyller kriterierna för batch-versionen av 3 meddelanden och du bör nu se en faktura på Northwind.

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

