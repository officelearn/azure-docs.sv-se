---
title: Hur man ringer ett telefonsamtal från Twilio (.NET) | Microsoft-dokument
description: Läs om hur du ringer ett telefonsamtal och skickar ett SMS-meddelande med Twilio API-tjänsten på Azure. Kodexempel skrivna i .NET.
services: ''
documentationcenter: .net
author: mimckitt
editor: ''
ms.assetid: 789185ad-69dc-4e9e-a936-42e0a25315c8
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/04/2016
ms.author: mimckitt
ms.openlocfilehash: df1f5e1c21c28fa8c1fcdef6b2278fb92014a3b1
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272567"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-web-role-on-azure"></a>Så här ringer du ett telefonsamtal med Twilio i en webbroll på Azure
Den här guiden visar hur du använder Twilio för att ringa ett samtal från en webbsida som finns i Azure. Det resulterande programmet uppmanar användaren att ringa ett samtal med det angivna numret och meddelandet, som visas i följande skärmdump.

![Azure-samtalsformulär med Twilio och ASP.NET][twilio_dotnet_basic_form]

## <a name="prerequisites"></a><a name="twilio-prereqs"></a>Krav
Du måste göra följande för att använda koden i det här avsnittet:

1. Hämta ett Twilio-konto och en autentiseringstoken från [Twilio-konsolen][twilio_console]. För att komma igång med Twilio, registrera dig på [https://www.twilio.com/try-twilio][try_twilio]. Du kan utvärdera [https://www.twilio.com/pricing][twilio_pricing]priser på . Information om API:et som tillhandahålls [https://www.twilio.com/voice/api][twilio_api]av Twilio finns i .
2. Lägg till *Twilio .NET-biblioteket* i webbrollen. Se **Så här lägger du till Twilio-biblioteken i webbrollprojektet**senare i det här avsnittet.

Du bör känna till att skapa en grundläggande [webbroll på Azure][azure_webroles_get_started].

## <a name="how-to-create-a-web-form-for-making-a-call"></a><a name="howtocreateform"></a>Så här skapar du ett webbformulär för att ringa ett samtal
<a id="use_nuget"></a>Så här lägger du till Twilio-biblioteken i webbrollprojektet:

1. Öppna din lösning i Visual Studio.
2. Högerklicka på **Referenser**.
3. Klicka på **Hantera NuGet-paket**.
4. Klicka på **Online**.
5. Skriv *twilio*i sökrutan online .
6. Klicka på **Installera** på Twilio-paketet.

Följande kod visar hur du skapar ett webbformulär för att hämta användardata för att ringa ett samtal. I det här exemplet skapas en ASP.NET webbroll med namnet **TwilioCloud.**

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

## <a name="how-to-create-the-code-to-make-the-call"></a><a id="howtocreatecode"></a>Så här skapar du koden för att ringa samtalet
Följande kod, som anropas när användaren fyller i formuläret, skapar samtalsmeddelandet och genererar samtalet. I det här exemplet körs koden i onclick-händelsehanteraren för knappen i formuläret . (Använd ditt Twilio-konto och autentiseringstoken `accountSID` i `authToken` stället för de platshållarvärden som tilldelats och i koden nedan.)

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
            // Call processing happens here.

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
                var url = $"https://twimlets.com/message?Message%5B0%5D={myMessage.Replace(" ", "%20")}";
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

Anropet görs och Twilio-slutpunkten, API-versionen och samtalsstatusen visas. Följande skärmbild visar utdata från en exempelkörning.

![Azure-anropssvar med Twilio och ASP.NET][twilio_dotnet_basic_form_output]

Mer information om TwiML [https://www.twilio.com/docs/api/twiml][twiml]finns på . Mer information &lt;&gt; om Say och andra Twilio-verb finns på [https://www.twilio.com/docs/api/twiml/say][twilio_say].

## <a name="next-steps"></a><a id="nextsteps"></a>Nästa steg
Den här koden tillhandahölls för att visa dig grundläggande funktioner med Twilio i en ASP.NET webbroll på Azure. Innan du distribuerar till Azure i produktion kanske du vill lägga till fler felhantering eller andra funktioner. Ett exempel:

* I stället för att använda ett webbformulär kan du använda Azure Blob storage eller en Azure SQL Database-instans för att lagra telefonnummer och samtalstext. Information om hur du använder Blobbar i Azure finns i [Så här använder du Azure Blob storage-tjänsten i .NET][howto_blob_storage_dotnet]. Information om hur du använder SQL Database finns i [Så här använder du Azure SQL Database i .NET-program][howto_sql_azure_dotnet].
* Du kan `RoleEnvironment.getConfigurationSettings` använda för att hämta Twilio-konto-ID: t och autentiseringstoken från distributionens konfigurationsinställningar, i stället för att hårdkoda värdena i formuläret. Information om `RoleEnvironment` klassen finns i [Microsoft.WindowsAzure.ServiceRuntime Namespace][azure_runtime_ref_dotnet].
* Läs Twilios säkerhetsriktlinjer på [https://www.twilio.com/docs/security][twilio_docs_security].
* Läs mer om Twilio på [https://www.twilio.com/docs][twilio_docs].

## <a name="see-also"></a><a name="seealso"></a>Se även
* [Så här använder du Twilio för röst- och SMS-funktioner från Azure](twilio-dotnet-how-to-use-for-voice-sms.md)

[twilio_console]: https://www.twilio.com/console
[twilio_pricing]: https://www.twilio.com/pricing
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_api]: https://www.twilio.com/voice/api
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified

[twilio_dotnet_basic_form]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form.png
[twilio_dotnet_basic_form_output]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form_output.png

[twiml]: https://www.twilio.com/docs/api/twiml



[howto_twilio_voice_sms_dotnet]: /develop/net/how-to-guides/twilio/

[howto_blob_storage_dotnet]: https://www.windowsazure.com/develop/net/how-to-guides/blob-storage/

[howto_sql_azure_dotnet]: https://www.windowsazure.com/develop/net/how-to-guides/sql-database/


[twilio_docs_security]: https://www.twilio.com/docs/security
[twilio_docs]: https://www.twilio.com/docs
[twilio_say]: https://www.twilio.com/docs/api/twiml/say


[azure_runtime_ref_dotnet]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.serviceruntime.aspx
[azure_webroles_get_started]: https://docs.microsoft.com/azure/cloud-services/cloud-services-dotnet-get-started
