---
title: Hur du ringa ett telefonsamtal från Twilio (Java) | Microsoft Docs
description: Lär dig att ringa ett telefonsamtal från en webbsida med Twilio i Java-program på Azure.
services: ''
documentationcenter: java
author: devinrader
manager: twilio
editor: mollybos
ms.assetid: 0381789e-e775-41a0-a784-294275192b1d
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 11/25/2014
ms.author: microsofthelp@twilio.com
ms.openlocfilehash: 9ef754e9952bcbd47d668331e906b19ad582b90c
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/24/2018
ms.locfileid: "42818606"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-java-application-on-azure"></a>Hur du ringa ett telefonsamtal med Twilio i ett Java-program på Azure
I följande exempel visas hur du kan använda Twilio för att göra ett anrop från en webbsida i Azure. Exempelprogrammet uppmanas användaren via telefonsamtal-värden som visas i följande skärmbild.

![Azure anrop formuläret med Twilio och Java][twilio_java]

Du behöver göra följande för att använda koden i det här avsnittet:

1. Hämta en Twilio-konto och autentisering token. Om du vill komma igång med Twilio kan utvärdera prissättning vid [ http://www.twilio.com/pricing ] [ twilio_pricing]. Du kan registrera dig på [ https://www.twilio.com/try-twilio ] [ try_twilio]. Läs om hur API: et som tillhandahålls av Twilio [ http://www.twilio.com/api ] [ twilio_api].
2. Hämta Twilio JAR-filen. Vid [ https://github.com/twilio/twilio-java ] [ twilio_java_github], du kan hämta GitHub-källor och skapa dina egna JAR eller ladda ned en färdiga JAR-filen (med eller utan beroenden).
   Koden i det här avsnittet har skrivits med hjälp av de färdiga TwilioJava 3.3.8 med beroenden JAR-filen.
3. Lägga till den JAR-filen till din Java build path.
4. Om du använder Eclipse för att skapa den här Java-program, lägger Twilio JAR i programmet distributionsfilen (WAR) med hjälp av Eclipses distributionsfunktion sammansättningen. Om du inte använder Eclipse för att skapa den här Java-program, se till att Twilio JAR är ingår i samma Azure-roll som ditt Java-program och har lagts till i klassökvägen i ditt program.
5. Se till att din cacerts keystore innehåller Equifax certifikatutfärdare för säkra-certifikat med MD5 fingeravtryck 67:CB:9 D: C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (serienumret är 35:DE:F4:CF och SHA1-fingeravtryck är D2:32:09:AD:23:D 3:14:23:21:74:E4:0 D: 7F:9 D: 62:13:97:86:63:3A). Det här är certificate authority (CA)-certifikat för den [ https://api.twilio.com ] [ twilio_api_service] -tjänsten, som anropas när du använder Twilio APIs. Läs om hur du lägger till detta CA-certifikat till din JDK cacert store [att lägga till ett certifikat i Java CA Certificate Store][add_ca_cert].

Dessutom är bekant med informationen på [skapar en Hello World-program med Azure Toolkit för Eclipse][azure_java_eclipse_hello_world], eller med andra tekniker som värd för Java-program i Azure om du är Vi rekommenderar starkt att du inte använder Eclipse.

## <a name="create-a-web-form-for-making-a-call"></a>Skapa ett webbformulär för att göra ett anrop
Följande kod visar hur du skapar ett webbformulär om du vill hämta användardata för att göra ett anrop. Det här exemplet, ett nytt dynamiskt webbprojekt heter **TwilioCloud**, har skapats och **callform.jsp** har lagts till som en JSP-fil.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
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

## <a name="create-the-code-to-make-the-call"></a>Skapa koden för att göra anrop
Följande kod, som anropas när användaren slutför formuläret som visas av callform.jsp, skapar anrop meddelandet och genererar anropet. För det här exemplet JSP-fil med namnet **makecall.jsp** och har lagts till i den **TwilioCloud** projekt. (Använd ditt Twilio-konto och autentisering token istället för platshållarvärdena för **accountSID** och **authToken** i koden nedan.)

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
    import="java.util.*"
    import="com.twilio.*"
    import="com.twilio.sdk.*"
    import="com.twilio.sdk.resource.factory.*"
    import="com.twilio.sdk.resource.instance.*"
    pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
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
         String Url="http://twimlets.com/message";
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

Förutom att anropet, visar makecall.jsp Twilio-slutpunkt, API-versionen och status för anropet. Ett exempel är följande skärmbild:

![Azure anrop svar med Twilio och Java][twilio_java_response]

## <a name="run-the-application"></a>Köra programmet
Följande är de övergripande stegen för att köra ditt program; information för de här stegen finns på [skapar en Hello World-program med Azure Toolkit för Eclipse][azure_java_eclipse_hello_world].

1. Exportera dina TwilioCloud WAR till Azure **approot** mapp. 
2. Ändra **startup.cmd** att packa upp din TwilioCloud WAR.
3. Kompilera programmet för compute-emulatorn.
4. Starta din distribution i compute-emulatorn.
5. Öppna en webbläsare och kör **http://localhost:8080/TwilioCloud/callform.jsp**.
6. Ange värden i formuläret, klickar du på **göra följande anrop**, och sedan visar resultatet i makecall.jsp.

När du är redo att distribuera till Azure, recompile för distribution till molnet och distribuera till Azure och kör http://*your_hosted_name*.cloudapp.net/TwilioCloud/callform.jsp i webbläsaren (ersätta din värdet för  *your_hosted_name*).

## <a name="next-steps"></a>Nästa steg
Den här koden har angetts för att visa dig grundläggande funktioner som använder Twilio i Java på Azure. Innan du distribuerar till Azure i produktion ska du lägga till fler felhantering eller andra funktioner. Exempel:

* Istället för att använda ett webbformulär, kunde du använda Azure storage-blobbar eller SQL-databas för att lagra telefonnummer och anropa text. Information om hur du använder Azure storage-blobbar i Java finns i [hur du använder Blob Storage-tjänsten från Java][howto_blob_storage_java]. 
* Du kan använda **RoleEnvironment.getConfigurationSettings** för att hämta Twilio konto-ID och autentiseringsnyckel token från konfigurationsinställningar för din distribution, i stället för att hårdkoda värdena i makecall.jsp. Information om den **RoleEnvironment** klass, se [med hjälp av Azure Service Runtime-biblioteket i JSP] [ azure_runtime_jsp] och dokumentation för Azure Service Runtime-paketet på [ http://dl.windowsazure.com/javadoc][azure_javadoc].
* Makecall.jsp koden tilldelar en Twilio-definierade URL [ http://twimlets.com/message ] [ twimlet_message_url], i den **Url** variabeln. Den här URL: en ger svar Twilio Markup Language (TwiML) som informerar Twilio hur du fortsätter med anropet. Till exempel TwiML som returneras kan innehålla en **&lt;Say&gt;** verb som resulterar i text som sägs till mottagaren. Istället för att använda Twilio-angivna URL: en, kan du bygga din egen tjänst för att svara på Twilios begäran. Mer information finns i [så Använd Twilio för röst- och SMS-funktioner i Java][howto_twilio_voice_sms_java]. Mer information om TwiML finns på [ http://www.twilio.com/docs/api/twiml ] [ twiml], och mer information om **&lt;Say&gt;** och andra Twilio-verb finns på [ http://www.twilio.com/docs/api/twiml/say ] [ twilio_say].
* Läsa Twilio-riktlinjer för säkerhet på [ https://www.twilio.com/docs/security ] [ twilio_docs_security].

Ytterligare information om Twilio finns i [ https://www.twilio.com/docs ] [ twilio_docs].

## <a name="see-also"></a>Se även
* [Använda Twilio för röst- och SMS-funktioner i Java][howto_twilio_voice_sms_java]
* [Att lägga till ett certifikat i CA-certifikat-Store för Java][add_ca_cert]

[twilio_pricing]: http://www.twilio.com/pricing
[try_twilio]: http://www.twilio.com/try-twilio
[twilio_api]: http://www.twilio.com/api
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_java_github]: http://github.com/twilio/twilio-java
[twimlet_message_url]: http://twimlets.com/message
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api_service]: http://api.twilio.com
[add_ca_cert]: java-add-certificate-ca-store.md
[azure_java_eclipse_hello_world]: https://docs.microsoft.com/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app 
[howto_twilio_voice_sms_java]: partner-twilio-java-how-to-use-voice-sms.md
[howto_blob_storage_java]: http://www.windowsazure.com/develop/java/how-to-guides/blob-storage/
[howto_sql_azure_java]: http://msdn.microsoft.com/library/windowsazure/hh749029.aspx
[azure_runtime_jsp]: http://msdn.microsoft.com/library/windowsazure/hh690948.aspx
[azure_javadoc]: http://dl.windowsazure.com/javadoc
[twilio_docs_security]: http://www.twilio.com/docs/security
[twilio_docs]: http://www.twilio.com/docs
[twilio_say]: http://www.twilio.com/docs/api/twiml/say
[twilio_java]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaCallForm.jpg
[twilio_java_response]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaMakeCall.jpg
