---
title: "Hur du gör ett telefonsamtal från Twilio (.NET) | Microsoft Docs"
description: "Lär dig att ringa ett telefonsamtal och skicka ett SMS-meddelande med Twilio-API-tjänsten på Azure. Kodexempel som skrivits i .NET."
services: 
documentationcenter: .net
author: devinrader
manager: timlt
editor: 
ms.assetid: 789185ad-69dc-4e9e-a936-42e0a25315c8
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/04/2016
ms.author: microsofthelp@twilio.com
ms.openlocfilehash: cd9792881182fbe90d9c210130ae8a34b12da363
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2017
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-web-role-on-azure"></a>Hur du gör ett telefonsamtal med Twilio i en webbroll på Azure
Den här guiden visar hur du använder Twilio för att ringa ett samtal från en webbsida som finns på Azure. Exempelprogrammet uppmanar användaren att göra anrop med angivet tal och meddelandet som visas i följande skärmbild.

![Azure anropet formuläret med hjälp av Twilio och ASP.NET][twilio_dotnet_basic_form]

## <a name="twilio-prereqs"></a>Förhandskrav
Du måste göra följande för att använda koden i det här avsnittet:

1. Skaffa ett Twilio-konto och autentisering-token från den [Twilio konsolen][twilio_console]. Registrera dig för att komma igång med Twilio på [https://www.twilio.com/try-twilio][try_twilio]. Du kan utvärdera priser på [http://www.twilio.com/pricing][twilio_pricing]. Mer information om API som tillhandahålls av Twilio finns [http://www.twilio.com/voice/api][twilio_api].
2. Lägg till den *Twilio .NET-bibliotek* till web-roll. Se **att lägga till Twilio-biblioteken i din webbrollsprojektet**senare i det här avsnittet.

Du bör känna till hur du skapar en grundläggande [på Azure-Webbroll][azure_webroles_get_started].

## <a name="howtocreateform"></a>Så här: skapa ett webbformulär för ett samtal
<a id="use_nuget"></a>Lägg till Twilio-bibliotek i webbprojektet för rollen:

1. Öppna din lösning i Visual Studio.
2. Högerklicka på **referenser**.
3. Klicka på **hantera NuGet-paket**.
4. Klicka på **Online**.
5. Skriv i sökrutan online *twilio*.
6. Klicka på **installera** på Twilio-paketet.

Följande kod visar hur du skapar ett webbformulär för att hämta användardata för ett samtal. I det här exemplet, ett ASP.NET Web Role med namnet **TwilioCloud** skapas.

```aspx
<%@ Page Title="Home Page" Language="C#" MasterPageFile="~/Site.master"
    AutoEventWireup="true" CodeBehind="Default.aspx.cs"
    Inherits="WebRole1._Default" %>

<asp:Content ID="HeaderContent" runat="server" ContentPlaceHolderID="HeadContent">
</asp:Content>
<asp:Content ID="BodyContent" runat="server" ContentPlaceHolderID="MainContent">
    <div>
        <asp:BulletedList ID="varDisplay" runat="server" BulletStyle="NotSet">
        </asp:BulletedList>
    </div>
    <div>
        <p>Fill in all fields and click <b>Make this call</b>.</p>
        <div>
            To:<br /><asp:TextBox ID="toNumber" runat="server" /><br /><br />
            Message:<br /><asp:TextBox ID="message" runat="server" /><br /><br />
            <asp:Button ID="callpage" runat="server" Text="Make this call"
                onclick="callpage_Click" />
        </div>
    </div>
</asp:Content>
```

## <a id="howtocreatecode"></a>Så här: skapa koden för att göra anrop
Följande kod, som kallas när användaren slutför formuläret, skapar anropet meddelandet och genererar anropet. I det här exemplet körs koden i händelsehanteraren onclick för knappen i formuläret. (Använd ditt Twilio-konto och autentisering token i stället för platshållarvärdena för `accountSID` och `authToken` i koden nedan.)

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.Web.UI;
using System.Web.UI.WebControls;
using Twilio;
using Twilio.Http;
using Twilio.Types;
using Twilio.Rest.Api.V2010;

namespace WebRole1
{
    public partial class _Default : System.Web.UI.Page
    {
        protected void Page_Load(object sender, EventArgs e)
        {

        }

        protected void callpage_Click(object sender, EventArgs e)
        {
            // Call porcessing happens here.

            // Use your account SID and authentication token instead of
            // the placeholders shown here.
            var accountSID = "ACNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";
            var authToken =  "NNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";

            // Instantiate an instance of the Twilio client.
            TwilioClient.Init(accountSID, authToken);

            // Retrieve the account, used later to retrieve the
            var account = AccountResource.Fetch(accountSID);

            this.varDisplay.Items.Clear();

            if (this.toNumber.Text == "" || this.message.Text == "")
            {
                this.varDisplay.Items.Add(
                        "You must enter a phone number and a message.");
            }
            else
            {
                // Retrieve the values entered by the user.
                var to = PhoneNumber(this.toNumber.Text);
                var from = new PhoneNumber("+14155992671");
                var myMessage = this.message.Text;

                // Create a URL using the Twilio message and the user-entered
                // text. You must replace spaces in the user's text with '%20'
                // to make the text suitable for a URL.
                var url = $"http://twimlets.com/message?Message%5B0%5D={myMessage.Replace(" ", "%20")}";
                var twimlUri = new Uri(url);

                // Display the endpoint, API version, and the URL for the message.
                this.varDisplay.Items.Add($"Using Twilio endpoint {
                }");
                this.varDisplay.Items.Add($"Twilioclient API Version is {apiVersion}");
                this.varDisplay.Items.Add($"The URL is {url}");

                // Place the call.
                var call = CallResource.create(to, from, url: twimlUri);
                this.varDisplay.Items.Add("Call status: " + call.Status);
            }
        }
    }
}
```

Anropet görs och Twilio-slutpunkten och API-versionen av samtalsstatus visas. Följande skärmbild visar utdata från en exempel-körning.

![Azure anropet svaret med hjälp av Twilio och ASP.NET][twilio_dotnet_basic_form_output]

Mer information om TwiML kan hittas på [http://www.twilio.com/docs/api/twiml][twiml]. Mer information om &lt;säg&gt; och andra Twilio-verb finns på [http://www.twilio.com/docs/api/twiml/say][twilio_say].

## <a id="nextsteps"></a>Nästa steg
Den här koden har angetts för att visa grundläggande funktioner med hjälp av Twilio i ett ASP.NET web role på Azure. Innan du distribuerar till Azure i produktion, kanske du vill lägga till flera felhantering eller andra funktioner. Exempel:

* Istället för att använda ett webbformulär, kunde du använda Azure Blob-lagring eller en Azure SQL Database-instans för att lagra telefonnummer och anropa text. Information om hur du använder BLOB i Azure finns [hur du använder tjänsten Azure Blob storage i .NET][howto_blob_storage_dotnet]. Information om hur du använder SQL-databas finns [hur du använder Azure SQL Database i .NET-program][howto_sql_azure_dotnet].
* Du kan använda `RoleEnvironment.getConfigurationSettings` för att hämta Twilio-konto-ID och autentisering token från din distribution konfigurationsinställningar, i stället för att hårdkoda värden i formuläret. Information om den `RoleEnvironment` klassen, se [Microsoft.WindowsAzure.ServiceRuntime Namespace][azure_runtime_ref_dotnet].
* Läs Twilio-riktlinjer för säkerhet på [https://www.twilio.com/docs/security][twilio_docs_security].
* Mer information om Twilio på [https://www.twilio.com/docs][twilio_docs].

## <a name="seealso"></a>Se även
* [Hur du använder Twilio för röst- och SMS-funktioner från Azure](twilio-dotnet-how-to-use-for-voice-sms.md)

[twilio_console]: https://www.twilio.com/console
[twilio_pricing]: http://www.twilio.com/pricing
[try_twilio]: http://www.twilio.com/try-twilio
[twilio_api]: http://www.twilio.com/voice/api
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified

[twilio_dotnet_basic_form]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form.png
[twilio_dotnet_basic_form_output]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form_output.png

[twiml]: http://www.twilio.com/docs/api/twiml



[howto_twilio_voice_sms_dotnet]: /develop/net/how-to-guides/twilio/

[howto_blob_storage_dotnet]: https://www.windowsazure.com/develop/net/how-to-guides/blob-storage/

[howto_sql_azure_dotnet]: https://www.windowsazure.com/develop/net/how-to-guides/sql-database/


[twilio_docs_security]: http://www.twilio.com/docs/security
[twilio_docs]: http://www.twilio.com/docs
[twilio_say]: http://www.twilio.com/docs/api/twiml/say


[azure_runtime_ref_dotnet]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.serviceruntime.aspx
[azure_webroles_get_started]: https://docs.microsoft.com/azure/cloud-services/cloud-services-dotnet-get-started
