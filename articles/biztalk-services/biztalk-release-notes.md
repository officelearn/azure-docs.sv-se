---
title: "Viktig information för Azure BizTalk-tjänst | Microsoft Docs"
description: "Visar en lista över kända problem för Azure BizTalk-tjänst"
services: biztalk-services
documentationcenter: 
author: msftman
manager: erikre
editor: 
ms.assetid: f4906fdc-4cd9-4a57-a007-a88c2e51a18f
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: deonhe
ms.openlocfilehash: 18ed891a9bba2b4011d3492722a2366d96fb3c01
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="release-notes-for-azure-biztalk-services"></a>Viktig information om Azure BizTalk-tjänst

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Viktig information för Microsoft Azure BizTalk Services innehåller kända problem i den här versionen.

## <a name="whats-new-in-the-november-update-of-biztalk-services"></a>Vad är nytt i November uppdateringen av BizTalk-tjänst
* Du kan aktivera kryptering i vila i BizTalk-Services-portalen. Se [Aktivera kryptering i vila i BizTalk-Services-portalen](https://msdn.microsoft.com/library/azure/dn874052.aspx).

## <a name="update-history"></a>Update-historiken
### <a name="october-update"></a>Uppdatering oktober
* Organisationskonton stöds:  
  * **Scenariot**: du har registrerat en BizTalk Service-distribution med ett Microsoft-konto (som user@live.com). I det här scenariot kan endast Account användare hantera den BizTalk Service med hjälp av BizTalk-tjänst-portalen. Ett organisationskonto kan inte användas.  
  * **Scenariot**: du har registrerat en BizTalk Service-distribution med ett organisationskonto i ett Azure Active Directory (t.ex. user@fabrikam.com eller user@contoso.com). I det här scenariot kan endast Azure Active Directory-användare inom samma organisation hantera den BizTalk Service med hjälp av BizTalk-tjänst-portalen. Ett Microsoft-konto kan inte användas.  
* Du registreras automatiskt i BizTalk-Services-portalen när du skapar en BizTalk Service i den klassiska Azure-portalen.
  * **Scenariot**: du logga in på den klassiska Azure portalen, skapa en BizTalk Service och välj sedan **hantera** för första gången. När BizTalk-Services-portalen öppnas registrerar BizTalk Service automatiskt och är redo för din distribution.  
    Se [registrera och uppdatera en BizTalk-tjänst-distribution på BizTalk-Services-portalen](https://msdn.microsoft.com/library/azure/hh689837.aspx).  

### <a name="august-14-update"></a>14 augusti uppdatering
* BizTalk-Services-portalen frikopplas nu avtalet och bridge Frikoppling – handel partner avtal och bryggor. Du nu skapa avtal och bryggor separat och vid körning bryggor matcha till ett avtal baserat på värdena i EDI-meddelandet. Se [skapa avtal i Azure BizTalk-tjänst](https://msdn.microsoft.com/library/azure/hh689908.aspx), [skapa en EDI-brygga med BizTalk-Services-portalen](https://msdn.microsoft.com/library/azure/dn793986.aspx), [skapa ett AS2-brygga med BizTalk-Services-portalen](https://msdn.microsoft.com/library/azure/dn793993.aspx), och [hur löser bryggor avtal vid körning?](https://msdn.microsoft.com/library/azure/dn794001.aspx)  
* Alternativet för att skapa mallar för avtal upphör.  
* För sändningssidan avtalet, kan du nu ange en avgränsare för olika uppsättningar för varje schema. Den här konfigurationen har angetts under protokollinställningar för för Skicka sida avtal. Mer information finns i [skapa en X12 avtalet i Azure BizTalk-tjänst](https://msdn.microsoft.com/library/azure/hh689847.aspx) och [skapa ett EDIFACT-avtal i Azure BizTalk Services](https://msdn.microsoft.com/library/azure/dn606267.aspx). Två nya entiteter läggs också till TPM OM API för samma ändamål. Se [X12DelimiterOverrides](https://msdn.microsoft.com/library/azure/dn798749.aspx) och [EDIFACTDelimiterOverride](https://msdn.microsoft.com/library/azure/dn798748.aspx).  
* Standard XSD-konstruktioner, inklusive härledda typer stöds nu. Se [Använd standard XSD konstruktioner i din maps](https://msdn.microsoft.com/library/azure/dn793987.aspx) och [använda härledda typer i mappning av scenarier och exempel](https://msdn.microsoft.com/library/azure/dn793997.aspx).  
* AS2 stöder nya MIC algoritmer för Meddelandesignering av och nya krypteringsalgoritmer. Se [skapar ett AS2-avtal i Azure BizTalk-tjänst](https://msdn.microsoft.com/library/azure/hh689890.aspx).  
  ## <a name="know-issues"></a>Kända problem

### <a name="connectivity-issues-after-biztalk-services-portal-update"></a>Problem med nätverksanslutningen när BizTalk Services-uppdateringen av Företagsportalen
  Om du har BizTalk-Services-portalen öppnar medan BizTalk-tjänst har uppgraderats för att kunna återställa ändringar till tjänsten kan du står inför anslutningsproblem med BizTalk-Services-portalen.  
  Som en tillfällig lösning kan du starta om webbläsaren, ta bort webbläsarens cache eller starta portalen i privat läge.  

### <a name="visual-studio-ide-cannot-locate-the-artifact-if-you-click-an-error-or-warning-in-a-biztalk-services-project"></a>Visual Studio IDE går inte att hitta artefakten om du klickar på ett fel eller en varning i ett projekt BizTalk-tjänst
Installera Visual Studio 2012 uppdatering 3 RC 1 för att åtgärda problemet.  

### <a name="custom-binding-project-reference"></a>Anpassade bindningen projektreferens
Överväg följande situationer med ett BizTalk-tjänst-projekt i Visual Studio-lösning:  

* I samma Visual Studio-lösning finns en BizTalk-tjänst-projektet och en anpassade bindningen. BizTalk Service-projekt har en referens till den här anpassade bindningen projektfilen.
* BizTalk Service-projekt har en referens till en anpassad/bindningsegenskaper DLL-filen.

Du Build'-lösningen i Visual Studio har. Sedan 'Återskapa' eller rensa lösningen. När du återskapa eller rensa igen efter det inträffar följande fel:  
  Det gick inte att kopiera filen <Path to DLL> till ”bin\Debug\FileName.dll”. Det går inte att komma åt filen 'bin\Debug\FileName.dll' eftersom den används av en annan process.  

#### <a name="workaround"></a>Lösning
* Om [Visual Studio 2012 uppdatering 3](https://www.microsoft.com/download/details.aspx?id=39305) är installerat, du har följande två alternativ:
  
  * Starta om Visual Studio eller
  * Starta om lösningen. Därefter utför endast en version i lösningen.  
* Om [Visual Studio 2012 uppdatering 3](https://www.microsoft.com/download/details.aspx?id=39305) är inte installerat, öppna Aktivitetshanteraren, klicka på processer fliken på MSBuild.exe process och klickar sedan på knappen Avsluta processen.  

### <a name="routing-to-basichttprelay-endpoints-is-not-supported-from-bridges-and-biztalk-services-portal-if-non-printable-characters-are-promoted-as-http-headers"></a>Routning till BasicHttpRelay slutpunkter stöds inte från bryggor och BizTalk-Services-portalen om icke utskrivbara tecken befordras som HTTP-huvuden
Om du använder icke utskrivbara tecken som en del av upphöjda egenskaper för meddelanden, kan dessa meddelanden skickas till relay mål som använder BasicHttpRelay bindningen. Dessutom upphöjda egenskaper som är tillgängliga som en del av spårning är URL-kodade för BLOB och icke kodad för mål.  

### <a name="mdn-is-sent-asynchronously-even-if-the-send-asynchronous-mdn-option-is-unchecked"></a>MDN skickas asynkront även om skicka asynkront MDN alternativet är markerat
Överväg att det här scenariot – om du väljer den **skicka asynkront MDN** kryssrutan och ange en URL för att skicka asynkrona MDN till, och avmarkerar sedan den **skicka asynkront MDN** kryssrutan igen, MDN fortfarande skickas till den angivna URL: en även om alternativet att skicka asynkrona MDNs inte är markerat.  
Som en lösning, måste du ta bort den angivna URL: en innan avmarkera den **skicka asynkront MDN** kryssrutan och sedan distribuera AS2-avtal.  

### <a name="whitespace-characters-beyond-a-valid-interchange-cause-an-empty-message-to-be-sent-to-the-suspend-endpoint"></a>Blanksteg efter en giltigt interchange orsaka ett tomt meddelande skickas till slutpunkten pausa
Om det finns blanksteg utanför ett IEA segment, behandlar det som slutet av aktuella interchange disassembler och tittar på en uppsättning mellanslag som ett nästa meddelande. Eftersom det inte är giltigt interchange, kan du se att en lyckad meddelande skickas till väg mål och ett tomt meddelande skickas suspend-slutpunkten.  

### <a name="tracking-in-biztalk-services-portal"></a>Spårning i BizTalk-Services-portalen
Spårning av händelser fångas upp till meddelandebehandling EDI och eventuella samband. Om ett meddelande inte utanför fasen protokoll, visas spårning som slutförd. I det här fallet finns i avsnittet LOGGEN under den **information** kolumn i **spårning** information om felet.
X12 ta emot och skicka inställningarna ([skapa en X12 avtalet i Azure BizTalk Services](https://msdn.microsoft.com/library/azure/hh689847.aspx)) innehåller information om protokoll-steget.  

### <a name="update-agreement"></a>Uppdatera avtal
BizTalk-Services-portalen kan du ändra kvalificerare av en identitet när ett avtal konfigureras. Detta kan resultera i egenskaperna för stämmer inte överens. Det finns till exempel ett avtal med ZZ:1234567 och ZZ:7654321 kvalificeraren. Ändra ZZ:1234567 ska 01:ChangedValue i profilinställningarna BizTalk-Services-portalen. Du öppnar avtalet och 01:ChangedValue visas i stället för ZZ:1234567.
Att ändra kvalificerare av en identitet, ta bort avtalet, uppdatera **identiteter** i profilen för partner och sedan återskapa avtalet.  

> AZURE. VARNING för det här problemet påverkar X12 och AS2.  
> 
> 

### <a name="as2-attachments"></a>AS2 bifogade filer
Bifogade filer för AS2 meddelanden inte stöds i skicka eller ta emot. Mer specifikt bifogade filer ignoreras tyst och meddelandetexten behandlas som ett vanligt AS2-meddelande.  

### <a name="resources-remembering-path"></a>Resurser: Komma ihåg sökväg
När du lägger till **resurser**, dialogrutan kan inte spara sökvägen som användes för att lägga till en resurs. Försök att lägga till webbplatsen BizTalk-Services-portalen för att spara sökvägen tidigare använt **tillförlitliga platser** i Internet Explorer.  

### <a name="if-you-rename-the-entity-name-of-a-bridge-and-close-the-project-without-saving-changes-opening-the-entity-again-results-in-an-error"></a>Om du byter namn på entitetsnamnet i en brygga och stänger du projektet utan att spara ändringarna, resulterar Öppna entiteten igen i ett fel
Överväg ett scenario i följande ordning:  

* Lägga till en brygga (till exempel en XML-One-Way brygga) till ett BizTalk Service-projekt  
* Byt namn på bryggan genom att ange ett värde för egenskapen entitetsnamnet. Detta byter namn på associerad .bridgeconfig filen med det namn du angav.  
* Stäng filen .bcs (genom att stänga fliken i Visual Studio) utan att spara ändringarna.  
* Öppna filen .bcs igen från Solution Explorer.  
  Du ser att medan den associera .bridgeconfig-filen har det nya namnet du angav, entitetsnamnet i designvyn fortfarande är det gamla namnet. Om du försöker öppna konfiguration av brygga genom att dubbelklicka på komponenten bridge ger följande fel:  
  `‘<old name>’ Entity’s associated file ‘<old name>.bridgeconfig’ does not exist`Om du vill undvika det här scenariot, kontrollera att du sparar ändringar när du byter namn på entiteter i BizTalk Service-projekt.  
  
### <a name="biztalk-service-project-builds-successfully-even-if-an-artifact-has-been-excluded-from-a-visual-studio-project"></a>BizTalk-Service-projekt skapar har även om en artefakt har undantagits från ett Visual Studio-projekt
Överväg ett scenario där du lägger till en artefakt (till exempel en XSD-fil) till ett BizTalk Service-projekt, inkludera den artefakten i konfiguration av brygga (till exempel genom att ange den som en begäran meddelandetyp) och utesluter den från Visual Studio-projekt. I sådana fall får bygga projektet inte något fel så länge den borttagna artefakten finns på disken på samma plats från där den ingår i Visual Studio-projekt.
  
### <a name="the-biztalk-service-project-does-not-check-for-schema-availability-while-configuring-the-bridges"></a>BizTalk-Service-projekt kontrollerar inte om schemat tillgänglighet vid konfiguration av bryggor
I ett projekt BizTalk Service om ett schema som har lagts till i projektet importerar ett annat schema kontrollerar BizTalk Service-projekt inte om det importerade schemat ska läggas till i projektet. Om du försöker skapa ett sådant projekt får du inte några build-fel.
  
### <a name="the-response-message-for-a-xml-request-reply-bridge-is-always-of-charset-utf-8"></a>Svarsmeddelandet för en XML-Request-Reply-brygga är alltid av Teckenuppsättning UTF-8
Den här versionen inställt teckenuppsättning av svarsmeddelandet från en XML-Request-Reply Bridge alltid på UTF-8.
  
### <a name="user-defined-datatypes"></a>Användardefinierade datatyper
BizTalk-kort Pack-kort i funktionen BizTalk-tjänst för nätverkskortet kan använda användardefinierade datatyper för nätverkskortet.
När du använder användardefinierade datatyper kan kopiera filer (.dll) till enhet: \Program\Microsoft BizTalk Adapter Service\BAServiceRuntime\bin\ eller till den globala sammansättningscachen (GAC) på den server där BizTalk Adapter-tjänsten. Följande fel kan annars uppstå på klienten:  
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
> Det rekommenderas att använda GACUtil.exe för att installera en fil i den globala sammansättningscachen. GACUtil.exe beskrivs hur du använder det här verktyget och kommandoradsalternativ för Visual Studio.  
> 
> 

### <a name="restarting-the-biztalk-adapter-service-web-site"></a>Starta om webbplatsen BizTalk Adapter-tjänsten
Installera den **BizTalk Adapter körtid*** skapar den **BizTalk Adapter Service** webbplats i IIS som innehåller den **BAService** program. **BAService** programmet internt relä bindningen använder för att utöka räckvidden för lokala tjänstslutpunkten till molnet. För en tjänst som finns lokalt registreras motsvarande relay-slutpunkten på Service Bus endast när den lokala tjänsten startar.  

Om du stoppar och startar ett program är konfigurationen för automatisk start av ett program inte Inlöst. Så när **BAService** har stoppats, du måste alltid starta om den **BizTalk Adapter-tjänst** -webbplats i stället. Inte starta eller stoppa den **BAService** program.

### <a name="special-characters-should-not-be-used-for-address-and-entity-names-of-lob-components"></a>Specialtecken ska inte användas för adress och entiteten namnen på LOB-komponenter
Du bör inte använda specialtecken för adress och entiteten namnen på LOB-komponenter. Om du gör det visas ett fel vid distribution av BizTalk Service-projekt. För vissa tecken som '%' webbplatsen BizTalk Adapter-tjänst kan tänkas placeras i ett stoppat tillstånd och måste du starta den manuellt.

### <a name="test-map-with-get-context-property"></a>Testa karta med Get-kontextegenskap
Om en transformering innehåller en **hämta kontextegenskap** kartan åtgärden **Test kartan** misslyckas. Som en tillfällig lösning kan ersätta den **hämta kontextegenskap** kartan igen med en sträng sammanfoga kartan åtgärd som innehåller falska data. Kommer att fylla i målschemat och kan du testa andra Transform-funktioner.

### <a name="test-map-property-does-not-display"></a>Testa egenskap visas inte
Den **Test kartan** egenskaper visas inte i Visual Studio. Detta kan inträffa om den **egenskaper** fönster och **Solution Explorer** fönstret inte dockad samtidigt. För att lösa problemet docka i **egenskaper** och **Solution Explorer** windows.  

### <a name="datetime-reformat-drop-down-is-grayed-out"></a>DateTime formatera om listrutan är nedtonad
När en DateTime formatera om kartan åtgärd har lagts till designytan och konfigurerat är listrutan Format nedtonade. Detta kan hända om datorn visa **medel – 125%** eller **större – 150%**. Lös problemet genom att ställa in bildskärmens **mindre – 100% (standard)** enligt instruktionerna nedan:  

1. Öppna den **Kontrollpanelen** och på **utseende och anpassning**.
2. Klicka på **visa**.
3. Klicka på **mindre – 100% (standard)** och på **tillämpa**.

Den **Format** listrutan ska nu fungera som förväntat.

### <a name="duplicate-agreements-in-the-biztalk-services-portal"></a>Duplicera avtal i BizTalk-Services-portalen
Studera följande scenario:

1. Skapa ett avtal med handel Partner hantering OM API.
2. Öppna avtalet i BizTalk-Services-portalen på två olika flikar.
3. Distribuera avtal från båda flikarna.
4. Därför distribueras båda avtalen ledde till dubblettposter i BizTalk-Services-portalen

**Lösning**. Öppna någon av de duplicera avtalen i BizTalk-Services-portalen och avinstallera.  

### <a name="bridges-do-not-use-updated-certificate-even-after-a-certificate-has-been-updated-in-the-artifact-store"></a>Bryggor Använd inte uppdaterade certifikatet även efter att ett certifikat har uppdaterats i arkivet artefakt
Tänk på följande scenarier:  

**Scenario 1: Använda tumavtrycket-baserade certifikat för att säkra meddelandet skickades från en brygga till en tjänstslutpunkt**  
Överväg ett scenario där du använder tumavtrycket-baserade certifikat i projektet BizTalk Service. Du uppdatera certifikatet i BizTalk-Services-portalen med samma namn men med ett annat tumavtryck, men uppdaterar inte BizTalk Service-projekt i enlighet med detta. I ett sådant scenario kan bryggan fortsätta att bearbeta meddelanden eftersom äldre certifikatdata kan det fortfarande finnas i cachen kanal. Efter det behandlingen av meddelandet misslyckas.  

**Lösning**: uppdatera certifikat i BizTalk Service-projektet och distribuera projektet.  

**Scenario 2: Använda namnbaserat beteenden för att identifiera certifikat för att skydda meddelandet skickades från en brygga till en tjänstslutpunkt**

Överväg ett scenario där du använder-namnbaserat beteenden för att identifiera certifikat i projektet BizTalk Service. Du uppdatera certifikatet i BizTalk-Services-portalen, men uppdaterar inte BizTalk Service-projekt i enlighet med detta. I ett sådant scenario kan bryggan fortsätta att bearbeta meddelanden eftersom äldre certifikatdata kan det fortfarande finnas i cachen kanal. Efter det behandlingen av meddelandet misslyckas.  

**Lösning**: uppdatera certifikat i BizTalk Service-projektet och distribuera projektet.  

### <a name="bridges-continue-to-process-messages-even-when-the-sql-database-is-offline"></a>Bryggor fortsätta att bearbeta meddelanden även om SQL-databasen är offline
BizTalk-tjänst bryggor fortsätta att bearbeta meddelanden under en period, även om Microsoft Azure SQL Database (som lagrar informationen körs som distribuerade artefakter och pipelines) är offline. Det beror på att BizTalk-tjänst som använder den cachelagrade artefakter och konfiguration av brygga.
Du kan använda BizTalk Services PowerShell-cmdlets för att stoppa eller inaktivera BizTalk Service om du inte vill att bryggor att bearbeta meddelanden när SQL-databasen är offline. Se [Azure BizTalk Service Management Sample](http://go.microsoft.com/fwlink/p/?LinkID=329019) för Windows PowerShell-cmdletar för att hantera åtgärder.  

### <a name="reading-the-xml-message-within-a-bridges-custom-code-component-includes-an-extra-bom-character"></a>Läsning av XML-meddelande i en brygga anpassad kodkomponenten innehåller ett extra BOM tecken
Överväg ett scenario där du vill läsa XML-meddelanden i en brygga anpassad kod. Om du använder .NET API-System.Text.Encoding.UTF8.GetString(bytes) ett extra BOM tecken ingår i utdata i början av meddelandet. Så om du inte vill att utdata till innehåller extra BOM-tecken, måste du använda ```System.IO.StreamReader().ReadToEnd()```.

### <a name="sending-messages-to-a-bridge-using-wcf-does-not-scale"></a>Skicka meddelanden till en brygga som använder WCF skalas inte
Meddelanden som skickas till en brygga som använder WCF inte skalas. I stället bör du använda HttpWebRequest om du vill att en skalbar klient.

### <a name="upgrade-token-provider-error-after-upgrading-from-biztalk-services-preview-to-general-availability-ga"></a>UPPGRADERING: Token Provider-fel när du har uppgraderat från BizTalk-Services-förhandsgranskning för allmän tillgänglighet (GA)
Det finns en EDI eller AS2-avtal med aktiva batchar. Vid BizTalk Service uppgraderas från Preview till GA, händer följande:

* Fel: Token providern kunde inte tillhandahålla en säkerhetstoken. Tokenleverantören returnerade meddelandet: fjärrnamnet kunde inte matchas.
* Batchaktiviteter har avbrutits.

**Lösning**: efter BizTalk-tjänst har uppdaterats till allmän tillgänglighet (GA), omdistribuera avtalet.  

### <a name="upgrade-toolbox-shows-the-old-bridge-icons-after-upgrading-the-biztalk-services-sdk"></a>UPPGRADERING: Verktygslådan visar de gamla bridge ikonerna när du har uppgraderat BizTalk Services SDK
När du har uppgraderat en tidigare version av BizTalk Services SDK, som har gamla ikoner som representerar bryggor fortsätter verktygslådan att visa de gamla ikonerna för bryggor. Om du lägger till en brygga BizTalk Service-projekt designytan visar ytan ikonen Ny.  

**Lösning**. Du kan undvika det här problemet genom att ta bort filer på .tbd <system drive>: \Users\<användare > \AppData\Local\Microsoft\VisualStudio\11.0.  

### <a name="upgrade-biztalk-portal-update-from-preview-to-ga-might-show-an-error-indicating-that-the-edi-capability-is-not-available"></a>UPPGRADERING: BizTalk Portal uppdatering från förhandsversionen GA kan visa ett fel som anger att EDI-funktionen inte är tillgänglig
Om du är inloggad i BizTalk-Services-portalen medan BizTalk-tjänst har uppgraderats från Preview till GA, kan du få följande fel i portal:  

Den här funktionen är inte tillgänglig som en del av den här versionen av Microsoft Azure BizTalk-tjänst. Om du vill använda växla dessa funktioner till en lämplig version.  

**Lösning**: Logga ut från den portalen, Stäng och öppna webbläsaren och sedan logga in på portalen.  

### <a name="upgrade-new-tracking-data-does-not-show-up-after-biztalk-services-is-upgraded-to-ga"></a>UPPGRADERING: Nya spårningsdata visas inte när BizTalk-tjänst har uppgraderats till GA
Anta att ett scenario där du har en XML-brygga som distribuerats på BizTalk-Services-förhandsgranskning prenumeration. Du kan skicka meddelanden till bryggan och motsvarande spårningsdata är tillgängligt på BizTalk-Services-portalen. Nu om du skickar ett meddelande till samma bridge slutpunkten distribuerade tidigare runtime-bits BizTalk-Services-portalen och BizTalk-tjänst har uppgraderats till GA, visas spårningsdata inte för meddelanden som skickas efter uppgraderingen.  

### <a name="pipelines-versus-bridges"></a>Pipelines och bryggor
I det här dokumentet används termen ”pipelines' och 'bryggor” synonymt. Både i stort sett betyda samma sak, vilket är en meddelande processing unit som distribuerats på BizTalk-tjänst.  

### <a name="concepts"></a>Koncept
[BizTalk-tjänst](https://msdn.microsoft.com/library/azure/hh689864.aspx)   

