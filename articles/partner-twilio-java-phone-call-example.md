---
title: "Hur du gör ett telefonsamtal från Twilio (Java) | Microsoft Docs"
description: "Lär dig att ringa ett samtal från en webbsida med Twilio i ett Java-program på Azure."
services: 
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
ms.openlocfilehash: 04ecb80a2a9e15b549b47138caf71c7e64bda500
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-java-application-on-azure"></a>Hur du gör ett telefonsamtal med Twilio i ett Java-program på Azure
I följande exempel visas hur du kan använda Twilio ringa ett samtal från en webbsida som finns på Azure. Exempelprogrammet uppmanas användaren för telefonsamtal värden som visas i följande skärmbild visar.

![Azure anropet formuläret med hjälp av Twilio och Java][twilio_java]

Du behöver göra följande för att använda koden i det här avsnittet:

1. Skaffa ett Twilio-konto och autentisering token. Kom igång med Twilio, utvärdera priser på [http://www.twilio.com/pricing][twilio_pricing]. Du kan registrera dig på [https://www.twilio.com/try-twilio][try_twilio]. Mer information om API som tillhandahålls av Twilio finns [http://www.twilio.com/api][twilio_api].
2. Hämta Twilio JAR. Vid [https://github.com/twilio/twilio-java][twilio_java_github], du kan hämta GitHub-datakällor och skapa egna JAR eller hämta en förskapad JAR (med eller utan beroenden).
   Koden i det här avsnittet har skrivits med hjälp av fördefinierade TwilioJava 3.3.8 med beroenden JAR.
3. Lägg till JAR din Java byggsökväg.
4. Om du använder Eclipse för att skapa den här Java-program, lägger Twilio JAR i programmet distributionsfilen (WAR) funktionen Eclipses distribution sammansättningen. Om du inte använder Eclipse för att skapa Java-program, kontrollera Twilio JAR är ingår i samma Azure rollen som Java-programmet, och lägga till klassen sökvägen till programmet.
5. Kontrollera din cacerts keystore innehåller Equifax certifikatutfärdare för säkra-certifikat med MD5 fingeravtryck 67:CB:9 D: C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (serienumret är 35:DE:F4:CF och SHA1 fingeravtrycket D2:32:09:AD:23:D 3:14:23:21:74:E4:0 D: 7F:9 D: 62:13:97:86:63:3A). Det här är certifikatet certifikatutfärdarens certifikat för den [https://api.twilio.com] [ twilio_api_service] -tjänsten som anropas när du använder Twilio APIs. Information om att lägga till den här Certifikatutfärdarens certifikat till din JDK cacert store finns i [att lägga till ett certifikat i certifikatarkivet för Certifikatutfärdaren i Java][add_ca_cert].

Dessutom förtrogenhet med informationen på [skapar en Hello World program med hjälp av Azure-verktygen för Eclipse][azure_java_eclipse_hello_world], eller med andra tekniker för om du är värd för Java-program i Azure Vi rekommenderar starkt att du inte använder Eclipse.

## <a name="create-a-web-form-for-making-a-call"></a>Skapa ett webbformulär för ett samtal
Följande kod visar hur du skapar ett webbformulär för att hämta användardata för ett samtal. Det här exemplet, ett nytt dynamiskt webbprojekt heter **TwilioCloud**, har skapats och **callform.jsp** har lagts till som en JSP-fil.

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
Följande kod, som kallas när användaren slutför formuläret som visas av callform.jsp, skapar anropet meddelandet och genererar anropet. För det här exemplet JSP-fil med namnet **makecall.jsp** och lades till i **TwilioCloud** projekt. (Använd ditt Twilio-konto och autentisering token i stället för platshållarvärdena för **accountSID** och **authToken** i koden nedan.)

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

Förutom att göra anropet visar makecall.jsp Twilio-slutpunkten och API-versionen av samtalsstatus. Följande skärmdump är ett exempel:

![Azure anropet svaret med hjälp av Twilio och Java][twilio_java_response]

## <a name="run-the-application"></a>Köra programmet
Följande är de övergripande stegen för att köra programmet; information för de här stegen finns på [skapar en Hello World program med hjälp av Azure-verktygen för Eclipse][azure_java_eclipse_hello_world].

1. Exportera din TwilioCloud WAR till Azure **approot** mapp. 
2. Ändra **startup.cmd** att packa TwilioCloud-WAR.
3. Kompilera programmet för beräkningsemulatorn.
4. Starta distributionen i beräkningsemulatorn.
5. Öppna en webbläsare och kör **http://localhost:8080/TwilioCloud/callform.jsp**.
6. Ange värden i formuläret, klickar du på **gör det här anropet**, och sedan visa resultaten i makecall.jsp.

När du är redo att distribuera till Azure, recompile för distribution till molnet, distribuera till Azure och kör http://*your_hosted_name*.cloudapp.net/TwilioCloud/callform.jsp i webbläsaren (Ersätt värdet för  *your_hosted_name*).

## <a name="next-steps"></a>Nästa steg
Den här koden har angetts för att visa grundläggande funktioner med hjälp av Twilio i Java i Azure. Innan du distribuerar till Azure i produktion, kanske du vill lägga till flera felhantering eller andra funktioner. Exempel:

* Istället för att använda ett webbformulär, kunde du använda Azure storage-blobbar eller SQL-databas för att lagra telefonnummer och anropa text. Information om hur du använder Azure storage-blobbar i Java finns [hur du använder tjänsten Blob Storage från Java][howto_blob_storage_java]. Information om hur du använder SQL-databas i Java finns [med hjälp av SQL-databas i Java][howto_sql_azure_java].
* Du kan använda **RoleEnvironment.getConfigurationSettings** för att hämta Twilio-konto-ID och autentisering token från din distribution konfigurationsinställningar, i stället för att hårdkoda värdena i makecall.jsp. Information om den **RoleEnvironment** klassen, se [med hjälp av Azure Service Runtime Library i JSP] [ azure_runtime_jsp] och dokumentation för körtid för Azure-paketet på [http://dl.windowsazure.com/javadoc][azure_javadoc].
* Koden makecall.jsp tilldelar en URL som anges Twilio [http://twimlets.com/message][twimlet_message_url], i den **Url** variabeln. URL: en innehåller ett Twilio Markup Language (TwiML)-svar som informerar Twilio hur du fortsätter med anropet. Till exempel TwiML som returneras kan innehålla en  **&lt;säg&gt;**  verb som resulterar i text som talas till mottagaren anrop. I stället för med den angivna Twilio-URL, kan du skapa tjänsten för att svara på Twilios begäran. Mer information finns i [så Använd Twilio för röst- och SMS-funktioner i Java][howto_twilio_voice_sms_java]. Mer information om TwiML kan hittas på [http://www.twilio.com/docs/api/twiml][twiml], och mer information om  **&lt;säg&gt;**  och andra Twilio-verb finns på [http://www.twilio.com/docs/api/twiml/say][twilio_say].
* Läs Twilio-riktlinjer för säkerhet på [https://www.twilio.com/docs/security][twilio_docs_security].

Mer information om Twilio finns [https://www.twilio.com/docs][twilio_docs].

## <a name="see-also"></a>Se även
* [Hur du använder Twilio för röst- och SMS-funktioner i Java][howto_twilio_voice_sms_java]
* [Lägga till ett certifikat i certifikatarkivet för Java-Certifikatutfärdare][add_ca_cert]

[twilio_pricing]: http://www.twilio.com/pricing
[try_twilio]: http://www.twilio.com/try-twilio
[twilio_api]: http://www.twilio.com/api
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_java_github]: http://github.com/twilio/twilio-java
[twimlet_message_url]: http://twimlets.com/message
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api_service]: http://api.twilio.com
[add_ca_cert]: java-add-certificate-ca-store.md
[azure_java_eclipse_hello_world]: http://msdn.microsoft.com/library/windowsazure/hh690944.aspx
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
