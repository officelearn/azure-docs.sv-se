---
title: Hur man gör ett telefonsamtal från Twilio (Java) | Microsoft-dokument
description: Läs om hur du ringer ett telefonsamtal från en webbsida med Twilio i ett Java-program på Azure.
services: ''
documentationcenter: java
author: georgewallace
ms.assetid: 0381789e-e775-41a0-a784-294275192b1d
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: 168ec65cfd0ff4e87c33324daa353b554111c8aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73838560"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-java-application-on-azure"></a>Så här ringer du ett telefonsamtal med Twilio i ett Java-program på Azure
I följande exempel visas hur du kan använda Twilio för att ringa ett samtal från en webbsida som finns i Azure. Det resulterande programmet kommer att uppmana användaren för telefonsamtal värden, som visas i följande skärmdump.

![Azure-samtalsformulär med Twilio och Java][twilio_java]

Du måste göra följande för att kunna använda koden i det här avsnittet:

1. Skaffa ett Twilio-konto och en autentiseringstoken. För att komma igång med Twilio, utvärdera prissättning på [https://www.twilio.com/pricing][twilio_pricing]. Du kan registrera [https://www.twilio.com/try-twilio][try_twilio]dig på . Information om API:et som tillhandahålls [https://www.twilio.com/api][twilio_api]av Twilio finns i .
2. Skaffa Twilio JAR. På [https://github.com/twilio/twilio-java][twilio_java_github]kan du hämta GitHub-källorna och skapa din egen JAR, eller ladda ner en färdig JAR (med eller utan beroenden).
   Koden i det här avsnittet skrevs med hjälp av den färdiga TwilioJava-3.3.8-med-beroenden JAR.
3. Lägg till JAR i din Java bygga sökväg.
4. Om du använder Eclipse för att skapa det här Java-programmet inkluderar du Twilio-JAR:en i programdistributionsfilen (WAR) med Eclipses distributionsmonteringsfunktion. Om du inte använder Eclipse för att skapa det här Java-programmet kontrollerar du att Twilio-JAR:en ingår i samma Azure-roll som ditt Java-program och läggs till i klasssökvägen till ditt program.
5. Kontrollera att din cacerts keystore innehåller Equifax Secure Certificate Authority certifikat med MD5 fingeravtryck 67:CB:9D:C0:13:24:8A:82:9B:B2:017:1E:D1:1B:EG:D4 (serienumret är 35:DE:F4:CF och SHA1-fingeravtrycket är D2:32:09:AD:23:D 3:14:23:21:74:E4:0D:7F:9D:62:13:97:86:63:3A). Det här är certifikatutfärdarcertifikatet [https://api.twilio.com][twilio_api_service] för tjänsten, som anropas när du använder Twilio API:er. Information om hur du lägger till det här certifikatutfärdarcertifikatet i JDK:s cacert-arkiv finns i [Lägga till ett certifikat i Java CA Certificate Store][add_ca_cert].

Dessutom rekommenderas starkt information om hur du [skapar ett Hello World-program][azure_java_eclipse_hello_world]med hjälp av Azure Toolkit for Eclipse eller med andra tekniker för att vara värd för Java-program i Azure om du inte använder Eclipse.

## <a name="create-a-web-form-for-making-a-call"></a>Skapa ett webbformulär för att ringa ett samtal
Följande kod visar hur du skapar ett webbformulär för att hämta användardata för att ringa ett samtal. I det här exemplet skapades ett nytt dynamiskt webbprojekt med namnet **TwilioCloud**och **callform.jsp** lades till som en JSP-fil.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "https://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Automated call form</title>
    </head>
    <body>
     <p>Fill in all fields and click <b>Make this call</b>.</p>
     <br/>
      <form action="makecall.jsp" method="post">
       <table>
         <tr>
           <td>To:</td>
           <td><input type="text" size=50 name="callTo" value="" />
           </td>
         </tr>
         <tr>
           <td>From:</td>
           <td><input type="text" size=50 name="callFrom" value="" />
           </td>
         </tr>
         <tr>
           <td>Call message:</td>
           <td><input type="text" size=400 name="callText" value="Hello. This is the call text. Good bye." />
           </td>
         </tr>
         <tr>
           <td colspan=2><input type="submit" value="Make this call" />
           </td>
         </tr>
       </table>
     </form>
     <br/>
    </body>
    </html>

## <a name="create-the-code-to-make-the-call"></a>Skapa koden för att ringa samtalet
Följande kod, som anropas när användaren fyller i formuläret som visas av callform.jsp, skapar samtalsmeddelandet och genererar samtalet. I det här exemplet heter JSP-filen **makecall.jsp** och lades till i **TwilioCloud-projektet.** (Använd ditt Twilio-konto och autentiseringstoken i stället för de platshållarvärden som tilldelats **accountSID** och **authToken** i koden nedan.)

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
    import="java.util.*"
    import="com.twilio.*"
    import="com.twilio.sdk.*"
    import="com.twilio.sdk.resource.factory.*"
    import="com.twilio.sdk.resource.instance.*"
    pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "https://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Call processing happens here</title>
    </head>
    <body>
        <b>This is my make call page.</b><p/>
     <%
    try 
    {
         // Use your account SID and authentication token instead
         // of the placeholders shown here.
         String accountSID = "your_twilio_account";
         String authToken = "your_twilio_authentication_token";

         // Instantiate an instance of the Twilio client.     
         TwilioRestClient client;
         client = new TwilioRestClient(accountSID, authToken);

         // Retrieve the account, used later to retrieve the CallFactory.
         Account account = client.getAccount();

         // Display the client endpoint. 
         out.println("<p>Using Twilio endpoint " + client.getEndpoint() + ".</p>");

         // Display the API version.
         String APIVERSION = TwilioRestClient.DEFAULT_VERSION;
         out.println("<p>Twilio client API version is " + APIVERSION + ".</p>");

         // Retrieve the values entered by the user.
         String callTo = request.getParameter("callTo");  
         // The Outgoing Caller ID, used for the From parameter,
         // must have previously been verified with Twilio.
         String callFrom = request.getParameter("callFrom");
         String userText = request.getParameter("callText");

         // Replace spaces in the user's text with '%20', 
         // to make the text suitable for a URL.
         userText = userText.replace(" ", "%20");

         // Create a URL using the Twilio message and the user-entered text.
         String Url="https://twimlets.com/message";
         Url = Url + "?Message%5B0%5D=" + userText;

         // Display the message URL.
         out.println("<p>");
         out.println("The URL is " + Url);
         out.println("</p>");

         // Place the call From, To and URL values into a hash map. 
         HashMap<String, String> params = new HashMap<String, String>();
         params.put("From", callFrom);
         params.put("To", callTo);
         params.put("Url", Url);

         CallFactory callFactory = account.getCallFactory();
         Call call = callFactory.create(params);
         out.println("<p>Call status: " + call.getStatus()  + "</p>"); 
    } 
    catch (TwilioRestException e) 
    {
        out.println("<p>TwilioRestException encountered: " + e.getMessage() + "</p>");
        out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
    }
    catch (Exception e) 
    {
        out.println("<p>Exception encountered: " + e.getMessage() + "");
        out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
    }
    %>
    </body>
    </html>

Förutom att ringa samtalet visar makecall.jsp slutpunkten Twilio, API-versionen och anropsstatusen. Ett exempel är följande skärmdump:

![Azure-samtalssvar med Twilio och Java][twilio_java_response]

## <a name="run-the-application"></a>Köra appen
Följande är de steg på hög nivå för att köra ditt program; information för dessa steg finns på [Skapa ett Hello World-program med hjälp av Azure Toolkit för Eclipse][azure_java_eclipse_hello_world].

1. Exportera din TwilioCloud WAR till **Azure-approt-mappen.** 
2. Ändra **startup.cmd** för att packa upp ditt TwilioCloud WAR.
3. Kompilera ditt program för beräkningsemulatorn.
4. Starta distributionen i beräkningsemulatorn.
5. Öppna en webbläsare `http://localhost:8080/TwilioCloud/callform.jsp`och kör .
6. Ange värden i formuläret, klicka på **Ring det här samtalet**och se sedan resultaten i makecall.jsp.

När du är redo att distribuera till Azure, kompilera om för distribution till molnet, distribuera till Azure och köra http://*your_hosted_name*.cloudapp.net/TwilioCloud/callform.jsp i webbläsaren (ersätta ditt värde med *your_hosted_name*).

## <a name="next-steps"></a>Nästa steg
Den här koden tillhandahölls för att visa dig grundläggande funktioner med Twilio i Java på Azure. Innan du distribuerar till Azure i produktion kanske du vill lägga till fler felhantering eller andra funktioner. Ett exempel:

* I stället för att använda ett webbformulär kan du använda Azure storage blobbar eller SQL Database för att lagra telefonnummer och samtalstext. Information om hur du använder Azure storage blobbar i Java finns i [Så här använder du Blob Storage Service från Java][howto_blob_storage_java]. 
* Du kan använda **RoleEnvironment.getConfigurationSettings** för att hämta Twilio-konto-ID och autentiseringstoken från distributionens konfigurationsinställningar, i stället för att hårdkoda värdena i makecall.jsp. Information om klassen **RoleEnvironment** finns i [Använda Azure Service Runtime Library i JSP][azure_runtime_jsp].
* Makecall.jsp-koden tilldelar en URL som [https://twimlets.com/message][twimlet_message_url]tillhandahålls av Twilio till **variabeln Url.** Den här webbadressen innehåller ett Twilio Markup Language (TwiML) som informerar Twilio om hur du går vidare med samtalet. TwiML som returneras kan till exempel innehålla ett ** &lt;Say-verb&gt; ** som resulterar i att texten blir uppläst till samtalsmottagaren. I stället för att använda webbadressen som tillhandahålls av Twilio kan du skapa din egen tjänst för att svara på Twilios begäran. Mer information finns i [Så här använder du Twilio för röst- och SMS-funktioner i Java][howto_twilio_voice_sms_java]. Mer information om TwiML [https://www.twilio.com/docs/api/twiml][twiml]finns på , och mer information om ** &lt;Say&gt; ** och [https://www.twilio.com/docs/api/twiml/say][twilio_say]andra Twilio verb finns på .
* Läs Twilio säkerhetsriktlinjer på [https://www.twilio.com/docs/security][twilio_docs_security].

Mer information om Twilio [https://www.twilio.com/docs][twilio_docs]finns i .

## <a name="see-also"></a>Se även
* [Hur man använder Twilio för röst- och SMS-funktioner i Java][howto_twilio_voice_sms_java]
* [Lägga till ett certifikat i Java CA-certifikatarkivet][add_ca_cert]

[twilio_pricing]: https://www.twilio.com/pricing
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_api]: https://www.twilio.com/api
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_java_github]: https://github.com/twilio/twilio-java
[twimlet_message_url]: https://twimlets.com/message
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api_service]: https://api.twilio.com
[add_ca_cert]: java-add-certificate-ca-store.md
[azure_java_eclipse_hello_world]: https://docs.microsoft.com/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app 
[howto_twilio_voice_sms_java]: partner-twilio-java-how-to-use-voice-sms.md
[howto_blob_storage_java]: https://www.windowsazure.com/develop/java/how-to-guides/blob-storage/
[howto_sql_azure_java]: https://msdn.microsoft.com/library/windowsazure/hh749029.aspx
[azure_runtime_jsp]: https://msdn.microsoft.com/library/windowsazure/hh690948.aspx
[twilio_docs_security]: https://www.twilio.com/docs/security
[twilio_docs]: https://www.twilio.com/docs
[twilio_say]: https://www.twilio.com/docs/api/twiml/say
[twilio_java]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaCallForm.jpg
[twilio_java_response]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaMakeCall.jpg
