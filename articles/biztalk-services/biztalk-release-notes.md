---
title: Viktig information för Azure BizTalk Services | Microsoft Docs
description: Visar en lista över kända problem för Azure BizTalk Services
services: biztalk-services
documentationcenter: ''
author: msftman
manager: erikre
editor: ''
ms.assetid: f4906fdc-4cd9-4a57-a007-a88c2e51a18f
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: deonhe
ms.openlocfilehash: f990aa086997d51e59df4285aabeccd31dcce822
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51253439"
---
# <a name="release-notes-for-azure-biztalk-services"></a>Viktig information om Azure BizTalk Services

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

Viktig information om Microsoft Azure BizTalk Services innehåller kända problem i den här versionen.

## <a name="whats-new-in-the-november-update-of-biztalk-services"></a>Vad är nytt i novemberuppdatering för BizTalk Services
* Du kan aktivera kryptering i vila i BizTalk Services-portalen. Se [Aktivera kryptering i vila i BizTalk Services-portalen](https://msdn.microsoft.com/library/azure/dn874052.aspx).

## <a name="update-history"></a>Uppdateringshistorik
### <a name="october-update"></a>Uppdatering oktober
* Organisationskonton stöds:  
  * **Scenariot**: du har registrerat en BizTalk Service-distribution med ett Microsoft-konto (t.ex. user@live.com). I det här scenariot kan endast Account användare hantera den BizTalk Service med hjälp av BizTalk Services-portalen. Ett organisationskonto kan inte användas.  
  * **Scenariot**: du har registrerat en BizTalk Service-distribution med ett organisationskonto i en Azure Active Directory (t.ex. user@fabrikam.com eller user@contoso.com). I det här scenariot kan endast Azure Active Directory-användare inom samma organisation hantera den BizTalk Service med hjälp av BizTalk Services-portalen. Ett Microsoft-konto kan inte användas.  
* När du skapar en BizTalk Service kan registrerade du automatiskt i BizTalk Services-portalen.
  * **Scenariot**: du loggar in på Azure, skapa en BizTalk Service och välj sedan **hantera** för första gången. När BizTalk Services-portalen öppnas registreras automatiskt i BizTalk Service och är redo för dina distributioner.  
    Se [registrera och uppdatera en BizTalk-tjänst-distribution på BizTalk Services-portalen](https://msdn.microsoft.com/library/azure/hh689837.aspx).  

### <a name="august-14-update"></a>14 augusti Update
* Avtalet och brygga Frikoppling – partner avtal och bryggor är nu fristående i BizTalk Services-portalen. Du nu skapa avtal och bryggor separat och vid körning bryggor lösa Agreement baserat på värdena i EDI-meddelande. Se [skapa avtal i Azure BizTalk Services](https://msdn.microsoft.com/library/azure/hh689908.aspx), [skapa ett EDI-brygga med hjälp av BizTalk Services-portalen](https://msdn.microsoft.com/library/azure/dn793986.aspx), [skapa ett AS2-brygga med hjälp av BizTalk Services-portalen](https://msdn.microsoft.com/library/azure/dn793993.aspx), och [ Hur löser bryggor avtal vid körning?](https://msdn.microsoft.com/library/azure/dn794001.aspx)  
* Alternativet för att skapa mallar för avtal kommer att upphöra.  
* För sändningssidan avtalet, kan du nu ange olika avgränsare uppsättningar för varje schema. Den här konfigurationen har angetts under protokollinställningarna för Skicka sida avtal. Mer information finns i [skapa en X12 avtalet i Azure BizTalk Services](https://msdn.microsoft.com/library/azure/hh689847.aspx) och [skapar ett EDIFACT-avtal i Azure BizTalk Services](https://msdn.microsoft.com/library/azure/dn606267.aspx). Två nya entiteter har också lagts till OM TPM-API för samma ändamål. Se [X12DelimiterOverrides](https://msdn.microsoft.com/library/azure/dn798749.aspx) och [EDIFACTDelimiterOverride](https://msdn.microsoft.com/library/azure/dn798748.aspx).  
* Standard XSD-konstruktioner, inklusive härledda typer stöds nu. Se [Använd standard XSD konstruktioner i dina kartor](https://msdn.microsoft.com/library/azure/dn793987.aspx) och [använda härledda typer i mappning av scenarier och exempel](https://msdn.microsoft.com/library/azure/dn793997.aspx).  
* AS2 har stöd för nya MIC-algoritmer för att signera meddelandet och nya krypteringsalgoritmer. Se [skapar ett AS2-avtal i Azure BizTalk Services](https://msdn.microsoft.com/library/azure/hh689890.aspx).  
  ## <a name="know-issues"></a>Kända problem

### <a name="connectivity-issues-after-biztalk-services-portal-update"></a>Problem med nätverksanslutningen när uppdateringen för BizTalk Services
  Om du har BizTalk Services-portalen öppnar medan BizTalk Services uppgraderas för att kunna återställa ändringar till tjänsten kan du står inför anslutningsproblem med BizTalk Services-portalen.  
  Som en tillfällig lösning kan du starta om webbläsaren, ta bort webbläsarens cache eller starta portalen i privat läge.  

### <a name="visual-studio-ide-cannot-locate-the-artifact-if-you-click-an-error-or-warning-in-a-biztalk-services-project"></a>Visual Studio IDE går inte att hitta artefakten om du klickar på ett fel eller varning i BizTalk Services-projekt
Installera Visual Studio 2012 uppdatering 3 RC 1 för att åtgärda problemet.  

### <a name="custom-binding-project-reference"></a>Anpassade bindningen project-referens
Överväg följande situationer med ett BizTalk Services-projekt i Visual Studio-lösningen:  

* I samma Visual Studio-lösning finns ett BizTalk Services-projekt och en bindning för anpassade projekt. BizTalk Service-projekt har en referens till den här anpassade bindningen projektfilen.
* BizTalk Service-projekt har en referens till en bindning/beteende DLL-filen.

Du Build ”” lösningen i Visual Studio har. Sedan ”återskapa' eller rensa lösningen. När du återskapa eller rensa igen, efter det uppstår följande fel:  
  Det går inte att kopiera filen <Path to DLL> till ”bin\Debug\FileName.dll”. Det går inte att komma åt filen 'bin\Debug\FileName.dll' eftersom den används av en annan process.  

#### <a name="workaround"></a>Lösning
* Om [Visual Studio 2012 Update 3](https://docs.microsoft.com/visualstudio/releasenotes/vs2012-update3-vs) är installerat, du har följande två alternativ:
  
  * Starta om Visual Studio eller
  * Starta om lösningen. Utför sedan endast en version i lösningen.  
* Om [Visual Studio 2012 Update 3](https://docs.microsoft.com/visualstudio/releasenotes/vs2012-update3-vs) är inte installerat, öppna Aktivitetshanteraren, klicka på processerna som fliken, klickar du på MSBuild.exe processen och klicka sedan på knappen Avsluta Process.  

### <a name="routing-to-basichttprelay-endpoints-is-not-supported-from-bridges-and-biztalk-services-portal-if-non-printable-characters-are-promoted-as-http-headers"></a>Routning till BasicHttpRelay slutpunkter stöds inte från bryggor och BizTalk Services-portalen om icke utskrivbara tecken har befordras som HTTP-huvuden
Om du använder icke utskrivbara tecken som en del av upphöjda egenskaper för meddelanden kan inte meddelandena skickas till relay-mål som använder BasicHttpRelay bindningen. Dessutom upphöjda egenskaper som är tillgängliga som en del av spårning är URL-kodade för blobbar och icke kodade för mål.  

### <a name="mdn-is-sent-asynchronously-even-if-the-send-asynchronous-mdn-option-is-unchecked"></a>MDN skickas asynkront även om skicka asynkron MDN alternativet är avmarkerat
Överväg det här scenariot – om du väljer den **skicka asynkron MDN** kryssrutan och ange en URL för att skicka asynkron MDN till, och sedan avmarkera den **skicka asynkron MDN** kryssrutan igen, MDN fortfarande skickas till den Ange URL: en även om alternativet att skicka asynkron mdn måste specificeras inte är markerat.  
Som en lösning måste du rensa den angivna URL: en innan du avmarkera den **skicka asynkron MDN** kryssrutan och sedan distribuera AS2-avtal.  

### <a name="whitespace-characters-beyond-a-valid-interchange-cause-an-empty-message-to-be-sent-to-the-suspend-endpoint"></a>Tecken som blanksteg utöver en giltig utbytet orsaka något tomt meddelande skickas till suspend-slutpunkt
Om det finns blanksteg utöver ett IEA segment, disassembler behandlar det som slutet av aktuell utbytet och tittar på nästa uppsättning blanksteg som ett nästa meddelande. Eftersom detta inte är giltigt utbytet, kan du se att en lyckad meddelande skickas till målet vägen och ett tomt meddelande skickas suspend-slutpunkten.  

### <a name="tracking-in-biztalk-services-portal"></a>Spåra i BizTalk Services-portalen
Spåra händelser fångas upp till EDI-meddelandebehandling och alla samband. Om ett meddelande inte utanför scenen protokoll, visas spårning som slutförd. I så fall kan referera till avsnittet LOG under den **information** kolumn i **spåra** för felinformation.
X12 ta emot och skicka inställningar ([skapa en X12 avtalet i Azure BizTalk Services](https://msdn.microsoft.com/library/azure/hh689847.aspx)) innehåller information om protokoll-steget.  

### <a name="update-agreement"></a>Uppdatera avtal
BizTalk Services-portalen kan du ändra kvalificeraren för en identitet när ett avtal har konfigurerats. Detta kan resultera i egenskaperna för stämmer inte överens. Det finns till exempel ett avtal med ZZ:1234567 och ZZ:7654321 kvalificeraren. Du kan ändra ZZ:1234567 för att vara 01:ChangedValue i Profilinställningar BizTalk Services-portalen. Du öppnar avtalet och 01:ChangedValue visas i stället för ZZ:1234567.
Att ändra kvalificeraren för en identitet, ta bort avtalet, uppdatera **identiteter** i partnerprofil och sedan återskapa avtalet.  

> AZURE. VARNING detta påverkar X12 och AS2.  
> 
> 

### <a name="as2-attachments"></a>AS2 bifogade filer
Bifogade filer för AS2-meddelanden inte stöds i skicka eller ta emot. Mer specifikt bifogade filer ignoreras tyst och meddelandetexten behandlas som en vanlig AS2-meddelandet.  

### <a name="resources-remembering-path"></a>Resurser: Komma ihåg sökväg
När du lägger till **resurser**, dialogrutan kanske inte kommer ihåg den sökväg som användes för att lägga till en resurs. Försök att lägga till webbplatsen BizTalk Services-portalen för att spara den tidigare använda sökvägen **tillförlitliga platser** i Internet Explorer.  

### <a name="if-you-rename-the-entity-name-of-a-bridge-and-close-the-project-without-saving-changes-opening-the-entity-again-results-in-an-error"></a>Om du byta namn på enhetens namn i en brygga och Stäng projektet utan att spara ändringarna genom resulterar att öppna entiteten igen i ett fel
Tänk dig ett scenario i följande ordning:  

* Lägg till en brygga (till exempel en XML-One-Way brygga) till ett BizTalk Service-projekt  
* Byt namn på bryggan genom att ange ett värde för egenskapen entitetsnamn. Detta byter namn på tillhörande .bridgeconfig-fil med det namn du angav.  
* Stäng filen .bcs (genom att stänga fliken i Visual Studio) utan att spara ändringarna.  
* Öppna filen .bcs igen från Solution Explorer.  
  Du ser att medan den associera .bridgeconfig-filen har det nya namnet som du angav, enhetens namn på designytan som är fortfarande det gamla namnet. Om du försöker öppna Bryggkonfigurationen genom att dubbelklicka på komponenten brygga får du följande fel:  
  `‘<old name>’ Entity’s associated file ‘<old name>.bridgeconfig’ does not exist` Om du vill undvika det här scenariot, se till att spara ändringarna när du byter namn på entiteter i en BizTalk Service-projekt.  
  
### <a name="biztalk-service-project-builds-successfully-even-if-an-artifact-has-been-excluded-from-a-visual-studio-project"></a>BizTalk Service projektversioner har även om en artefakt har undantagits från ett Visual Studio-projekt
Tänk dig ett scenario där du lägger till en artefakt (till exempel en XSD-fil) till ett BizTalk Service-projekt, inkludera den artefakten i Bryggkonfigurationen (till exempel genom att ange den som en begäran meddelandetyp) och utesluter den från Visual Studio-projektet. I detta fall är får att skapa projektet inte något fel så länge den borttagna artefakten finns på disken på samma plats från där den ingår i Visual Studio-projektet.
  
### <a name="the-biztalk-service-project-does-not-check-for-schema-availability-while-configuring-the-bridges"></a>BizTalk Service-projekt kontrollerar inte om schemat tillgänglighet när du konfigurerar bryggor
I BizTalk Service-projekt om ett schema som har lagts till i projektet importerar ytterligare ett schema, kontrollerar BizTalk Service-projekt inte om det importerade schemat ska läggas till i projektet. Om du försöker skapa sådana ett projekt, får du inte några build-fel.
  
### <a name="the-response-message-for-a-xml-request-reply-bridge-is-always-of-charset-utf-8"></a>Svarsmeddelandet för en XML-begäran / svar-brygga är alltid av teckenuppsättningen UTF-8
Den här versionen är har teckenuppsättningen i svarsmeddelandet från en XML-begäran / svar-brygga alltid värdet UTF-8.
  
### <a name="user-defined-datatypes"></a>Användardefinierade datatyper
BizTalk Adapter Pack-korten i BizTalk Adapter Service-funktionen kan använda användardefinierade datatyper för nätverkskortet.
När du använder användardefinierade datatyper, kopiera filer (.dll) till enhet: \Program\Microsoft BizTalk Adapter Service\BAServiceRuntime\bin\ eller till Global Assembly Cache (GAC) på den server där tjänsten BizTalk Adapter-tjänsten. I annat fall kan följande fel uppstå på klienten:  
```
<s:Fault xmlns:s="http://schemas.xmlsoap.org/soap/envelope/">
<faultcode>s:Client</faultcode>
<faultstring xml:lang="en-US">The UDT with FullName "File, FileUDT, Version=Value, Culture=Value, PublicKeyToken=Value" could not be loaded. Try placing the assembly containing the UDT definition in the Global Assembly Cache.</faultstring>
<detail>
  <AFConnectRuntimeFault xmlns="http://Microsoft.ApplicationServer.Integration.AFConnect/2011" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
    <ExceptionCode>ERROR_IN_SENDING_MESSAGE</ExceptionCode>
  </AFConnectRuntimeFault>
</detail>
</s:Fault>
```  
  
> [!IMPORTANT]
> Det rekommenderas att använda GACUtil.exe för att installera en fil i den globala sammansättningscachen. GACUtil.exe beskrivs hur du använder det här verktyget och Visual Studio-kommandoradsalternativ.  
> 
> 

### <a name="restarting-the-biztalk-adapter-service-web-site"></a>Starta om webbplatsen BizTalk Adapter Service
Installera den **BizTalk Adapter Service Runtime*** skapar den **BizTalk Adapter Service** webbplats i IIS som innehåller den **BAService** program. **BAService** programmet internt relä bindningen använder för att utöka räckvidden för lokala tjänstslutpunkt till molnet. För en tjänst som finns lokalt registreras den motsvarande relay-slutpunkten på Service Bus endast när den lokala tjänsten startas.  

Om du stoppar och startar ett program, konfigurationen för automatisk start av ett program är inte lösts in. Så när **BAService** har stoppats, du måste alltid starta om den **BizTalk Adapter Service** webbplats i stället. Inte starta eller stoppa den **BAService** program.

### <a name="special-characters-should-not-be-used-for-address-and-entity-names-of-lob-components"></a>Specialtecken ska inte användas för adressen och entiteten namnen på LOB-komponenter
Du bör inte använda specialtecken för adressen och entiteten namnen på LOB-komponenter. Om du gör det, får du ett fel när du distribuerar BizTalk Service-projekt. För vissa tecken som '%' webbplatsen BizTalk Adapter Service försätts i ett stoppat tillstånd och du måste starta den manuellt.

### <a name="test-map-with-get-context-property"></a>Testa karta med Get-kontextegenskap
Om en transformering innehåller en **hämta Kontextegenskapen** kartan åtgärden **Test kartan** misslyckas. Som en tillfällig lösning kan ersätta den **hämta kontextegenskap** kartan igen med en sträng sammanfoga kartan åtgärd med dummy-data. På så sätt fylla målschemat och Tillåt du testa andra Transform-funktioner.

### <a name="test-map-property-does-not-display"></a>Testa kartan egenskapen visas inte
Den **Test kartan** egenskaper visas inte i Visual Studio. Detta kan inträffa om den **egenskaper** fönster och **Solution Explorer** fönstret inte dockad samtidigt. Om du vill lösa det här docka den **egenskaper** och **Solution Explorer** windows.  

### <a name="datetime-reformat-drop-down-is-grayed-out"></a>DateTime omformatera listrutan är nedtonat
När en DateTime formatera om kartan åtgärd har lagts till designytan och konfigurerats, är listrutan Format nedtonade. Detta kan inträffa om datorn visa **medel – 125%** eller **större – 150%**. Ange visningen att lösa, **mindre – 100% (standard)** med hjälp av stegen nedan:  

1. Öppna den **Kontrollpanelen** och klicka på **utseende och anpassning**.
2. Klicka på **visa**.
3. Klicka på **mindre – 100% (standard)** och klicka på **tillämpa**.

Den **Format** listrutan ska nu fungera som förväntat.

### <a name="duplicate-agreements-in-the-biztalk-services-portal"></a>Duplicera avtal i BizTalk Services-portalen
Föreställ dig följande:

1. Skapa ett avtal med hjälp av Trading Partner Management OM API.
2. Öppna i avtal i BizTalk Services-portalen på två olika flikar.
3. Distribuera avtal från båda flikarna.
4. Därför kan distribueras både avtalen vilket resulterar i dubblettposter i BizTalk Services-portalen

**Lösning**. Öppna någon av de duplicera avtal i BizTalk Services-portalen och ta bort.  

### <a name="bridges-do-not-use-updated-certificate-even-after-a-certificate-has-been-updated-in-the-artifact-store"></a>Bryggor använder inte uppdaterade certifikatet även efter att ett certifikat har uppdaterats i arkivet artefakt
Tänk på följande scenarier:  

**Scenario 1: Använda tumavtryck-baserade certifikat för att skydda meddelandet skickades från en brygga till en tjänstslutpunkt**  
Tänk dig ett scenario där du använder tumavtryck-baserade certifikat i BizTalk Service-projektet. Du uppdaterar certifikatet i BizTalk Services-portalen med samma namn men ett annat tumavtryck, men uppdaterar inte BizTalk Service-projekt i enlighet med detta. I ett sådant scenario kan bryggan fortsätta att bearbeta meddelanden eftersom äldre certifikatdata kan det fortfarande finnas i cacheminnet för kanalen. Efter det misslyckas meddelandebehandling.  

**Lösning**: uppdatera certifikatet i BizTalk Service-projekt och distribuera om projektet.  

**Scenario 2: Använda namnbaserat beteenden för att identifiera certifikat för att skydda meddelandet skickades från en brygga till en tjänstslutpunkt**

Tänk dig ett scenario där du använder-namnbaserat beteenden för att identifiera certifikat i din BizTalk Service-projekt. Du uppdaterar certifikatet i BizTalk Services-portalen men uppdaterar inte BizTalk Service-projekt i enlighet med detta. I ett sådant scenario kan bryggan fortsätta att bearbeta meddelanden eftersom äldre certifikatdata kan det fortfarande finnas i cacheminnet för kanalen. Efter det misslyckas meddelandebehandling.  

**Lösning**: uppdatera certifikatet i BizTalk Service-projekt och distribuera om projektet.  

### <a name="bridges-continue-to-process-messages-even-when-the-sql-database-is-offline"></a>Bryggor fortsätta att bearbeta meddelanden även om SQL-databasen är offline
BizTalk Services-bryggor fortsätta att bearbeta meddelanden för ett tag, även om Microsoft Azure SQL Database (som lagras som körs som distribuerade artefakter och pipelines) är offline. Det beror på att BizTalk Services använder den cachelagrade artefakter och bryggkonfigurationen.
Du kan använda BizTalk Services PowerShell-cmdlets för att stoppa eller inaktivera BizTalk Service om du inte vill att bryggor bearbeta några meddelanden när SQL-databasen är offline. Se [Azure BizTalk Service Management-exempel](https://go.microsoft.com/fwlink/p/?LinkID=329019) för Windows PowerShell-cmdletar för att hantera åtgärder.  

### <a name="reading-the-xml-message-within-a-bridges-custom-code-component-includes-an-extra-bom-character"></a>Läsa XML-meddelandet i en brygga anpassad kodkomponenten innehåller ett extra BOM tecken
Tänk dig ett scenario där du vill läsa ett XML-meddelande i en brygga anpassad kod. Om du använder .NET API-System.Text.Encoding.UTF8.GetString(bytes) ett extra BOM tecken ingår i utdata i början av meddelandet. Så om du inte vill att utdata till innehåller något extra BOM tecken, måste du använda ```System.IO.StreamReader().ReadToEnd()```.

### <a name="sending-messages-to-a-bridge-using-wcf-does-not-scale"></a>Skicka meddelanden till en brygga med WCF skalas inte
Meddelanden som skickas till en brygga med WCF skalas inte. I stället bör du använda i HttpWebRequest om du vill att en skalbar klient.

### <a name="upgrade-token-provider-error-after-upgrading-from-biztalk-services-preview-to-general-availability-ga"></a>UPPGRADERING: Token Provider-fel när du har uppgraderat från förhandsversionen av BizTalk Services till allmän tillgänglighet (GA)
Det finns ett EDI- eller AS2-avtal med active batchar. När BizTalk Service uppgraderas från förhandsversion till allmänt tillgänglig, kan följande inträffa:

* Fel: Tokenleverantören kunde inte tillhandahålla en säkerhetstoken. Leverantör av säkerhetstoken returnerade meddelandet: fjärrnamnet kunde inte matchas.
* Batch-aktiviteterna har avbrutits.

**Lösning**: efter BizTalk-tjänsten uppdateras till allmän tillgänglighet (GA), distribuera om avtalet.  

### <a name="upgrade-toolbox-shows-the-old-bridge-icons-after-upgrading-the-biztalk-services-sdk"></a>UPPGRADERING: Verktygslådan visar de gamla brygga ikonerna när du har uppgraderat BizTalk Services SDK
När du har uppgraderat en tidigare version av BizTalk Services SDK, som hade gamla ikoner som representerar bryggor fortsätter verktygslådan att visa de gamla ikonerna för bryggor. Om du lägger till en brygga designytan för BizTalk Service-projekt, visas på ytan på ikonen Ny.  

**Lösning**. Du kan undvika det här problemet genom att ta bort filer under .tbd <system drive>: \Users\<användare > \AppData\Local\Microsoft\VisualStudio\11.0.  

### <a name="upgrade-biztalk-portal-update-from-preview-to-ga-might-show-an-error-indicating-that-the-edi-capability-is-not-available"></a>UPPGRADERING: BizTalk uppdatering från förhandsversionen av Allmänt kan visa ett felmeddelande om att EDI-funktionen inte är tillgänglig
Om du är inloggad i BizTalk Services-portalen medan BizTalk Services uppgraderas från förhandsversion till allmänt tillgänglig, kan du få följande fel i portal:  

Den här funktionen är inte tillgänglig som en del av den här versionen av Microsoft Azure BizTalk Services. Om du vill använda växla dessa funktioner till en lämplig version.  

**Lösning**: Logga ut från portalen, Stäng och öppna webbläsaren och sedan logga in på portalen.  

### <a name="upgrade-new-tracking-data-does-not-show-up-after-biztalk-services-is-upgraded-to-ga"></a>UPPGRADERING: Nya spårningsdata visas inte när BizTalk Services har uppgraderats till allmän tillgänglighet
Anta att ett scenario där du har en XML-brygga som distribuerats på förhandsversionen av BizTalk Services-prenumeration. Du skickar meddelanden till bryggan och motsvarande spårningsdata finns på BizTalk Services-portalen. Nu, om bits för BizTalk Services-portalen och BizTalk Services runtime uppgraderas till allmän tillgänglighet och du skickar ett meddelande till samma bridge-slutpunkt som distribuerats tidigare, spårning av data visas inte för meddelanden som skickas efter uppgraderingen.  

### <a name="pipelines-versus-bridges"></a>Pipelines och bryggor
I det här dokumentet används termen ”pipelines' och 'bryggor” synonymt. Båda innebär i stort sett samma sak, vilket är en bearbetningsenhet för meddelandet som distribuerats på BizTalk Services.  

### <a name="concepts"></a>Begrepp
[BizTalk Services](https://msdn.microsoft.com/library/azure/hh689864.aspx)   

