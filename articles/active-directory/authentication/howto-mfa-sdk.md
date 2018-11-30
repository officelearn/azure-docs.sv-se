---
title: Azure MFA SDK för anpassade appar
description: Den här artikeln visar hur du hämtar och använder Azure MFA SDK för att aktivera tvåstegsverifiering för dina appar.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: 565c35b296e2734df264942b9eab0a067626c368
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/27/2018
ms.locfileid: "52427190"
---
# <a name="building-multi-factor-authentication-into-custom-apps-sdk"></a>Att skapa Multi-Factor Authentication i anpassade appar (SDK)

> [!IMPORTANT]
> Utfasningen av Azure Multi-Factor Authentication Software Development Kit (SDK) har meddelats. Den här funktionen kommer inte längre stöd för nya kunder. Kunderna kan fortsätta använda SDK:n till den 14 november 2018. Efter det misslyckas anrop till SDK. 

Azure Multi-Factor Authentication Software Development Kit (SDK) kan du skapa tvåstegsverifiering direkt till processerna som inloggnings- eller transaktion av program i Azure AD-klienten.

Multi-Factor Authentication SDK är tillgänglig för C#, Visual Basic (.NET), Java, Perl, PHP och Ruby. SDK innehåller en tunn adapter för tvåstegsverifiering. Den innehåller allt du behöver för att skriva din kod, inklusive kommenterade källkodsfiler, exempel filer och en detaljerad viktigt-fil. Varje SDK innehåller också ett certifikat och privat nyckel för att kryptera transaktioner som är unika för multi-Factor Authentication-Provider. Så länge som du har en provider, kan du hämta SDK: N i så många språk och format efter behov.

Strukturen för API: er i SDK för multi-Factor Authentication är enkelt. Se en enskild funktion som anropar till ett API med flera faktorer alternativet parametrar (t.ex. verifieringsläge) och användardata (t.ex. att anropa telefonnumret eller PIN-kod för att verifiera). API: erna översätta funktionsanrop i webbtjänstförfrågningar till molnbaserade Azure Multi-Factor Authentication-tjänsten. Alla anrop måste innehålla en referens till det privata certifikat som ingår i varje SDK.

Eftersom API: erna inte har åtkomst till användare som har registrerats i Azure Active Directory, måste du ange användarinformation i en fil eller databas. API: erna erbjuder dessutom inte funktioner för registrering eller användare, så du behöver skapa dessa processer i ditt program.

> [!IMPORTANT]
> För att hämta SDK:n måste du skapa en Azure Multi-Factor Auth-provider, även om du har Azure MFA-, AAD Premium- eller EMS-licenser. Om du skapar en Azure Multi-Factor Authentication-Provider för detta ändamål och redan har licenser, se till att skapa providern med den **Per aktiverad användare** modellen. Koppla sedan providern till katalogen som innehåller Azure MFA, Azure AD Premium eller EMS-licenser. Den här konfigurationen garanterar att du bara faktureras om du har fler unika användare som använder SDK: N än antalet licenser du äger.


## <a name="download-the-sdk"></a>Ladda ned SDK
Ladda ned Azure Multi-Factor Authentication SDK kräver en [Azure Multi-Factor Authentication-Provider](concept-mfa-authprovider.md).  Detta kräver en fullständig Azure-prenumeration, även om Azure MFA, Azure AD Premium eller Enterprise Mobility Suite-licenser ägs. De offentliga metoderna för att hämta SDK: N har inaktiverats eftersom SDK: N är inaktuell. Du måste öppna ett supportärende med Microsoft om du vill ladda ned SDK. SDK: N tillhandahålls endast för kunder som redan använder SDK: N. Nya kunder kan inte publiceras.

## <a name="whats-in-the-sdk"></a>Vad ingår i SDK: N
SDK innehåller följande objekt:

* **VIKTIGT**. Beskriver hur du använder multi-Factor Authentication-API: er i ett nytt eller befintligt program.
* **Källfiler** för Multifaktorautentisering
* **Klientcertifikatet** som används för att kommunicera med Multi-Factor Authentication-tjänsten
* **Privat nyckel** för certifikatet
* **Anropa resultat.** En lista över resultatkoder för webbtjänstanrop. Öppna den här filen genom att använda ett program med textformatering, till exempel WordPad. Använd resultatkoder för anrop för att testa och felsöka implementeringen av Multi-Factor Authentication i ditt program. De är inte statuskoder för autentisering.
* **Exempel.** Exempelkod för en grundläggande fungerande implementeringen av Multi-Factor Authentication.

> [!WARNING]
> Klientcertifikatet är ett unikt privata certifikat som har skapats speciellt för dig. Dela inte eller förlorar den här filen. Det är din nyckel till att säkerställa säkerheten för din kommunikation med Multi-Factor Authentication-tjänsten.

## <a name="code-sample"></a>Kodexempel
Detta kodexempel visar hur du använder API: erna i Azure Multi-Factor Authentication SDK att lägga till standardläge röstverifiering anrop i ditt program. Standardläge är ett telefonsamtal som användaren svarar genom att trycka på fyrkant.

Det här exemplet används C# .NET 2.0 Multi-Factor Authentication SDK i en grundläggande ASP.NET-program med C# serversidan logik, men processen påminner om på andra språk. Eftersom SDK innehåller källfiler, inte körbara filer du skapa filerna och referera till dem eller lägga till dem direkt i ditt program.

> [!NOTE]
> När du implementerar Multi-Factor Authentication använder du ytterligare metoder (telefonsamtal eller SMS) som sekundär eller tertiär verifiering för att komplettera primära autentiseringsmetod (användarnamn och lösenord). Dessa metoder är inte utformade som primära autentiseringsmetoder.

### <a name="code-sample-overview"></a>Översikt över exemplet för kod
Den här exempelkoden för en enkel webbapp för demo använder ett telefonsamtal med svar # för att verifiera användarautentiseringen. Den här telefonsamtal faktor kallas i Multi-Factor Authentication standardläge.

Koden för klientsidan inkluderar inte de Multi-Factor Authentication-specifika element. Eftersom ytterligare autentiseringsfaktorer är oberoende av den primära autentiseringen, kan du lägga till dem utan att ändra befintliga inloggnings-gränssnittet. API: er i Multi-Factor Authentication SDK kan du anpassa användarupplevelsen, men du kanske inte behöver ändra något alls.

Serversidan koden lägger till standard-läge-autentisering i steg 2. Skapar den en PfAuthParams-objekt med parametrar som krävs för standardläge verifiering: användarnamn, telefon tal, och läge och sökvägen till klientcertifikatet (CertFilePath) som krävs i varje anrop. En demonstration av alla parametrar i PfAuthParams finns i SDK-exempelfilen.

Koden skickar sedan objektet PfAuthParams till funktionen pf_authenticate(). Det returnera värdet anger lyckas eller misslyckas autentiseringen. De parametrar, callStatus och samtalsstatus, innehåller information om ytterligare anrop resultatet. Resultatkoder för webbtjänstanrop dokumenteras i resultatfilen anrop i SDK.

Den här minimal implementeringen kan skrivas i några rader. I produktionskoden ska du dock inkludera Förbättrad felhantering, extradatabasen kod och en förbättrad användarupplevelse.

### <a name="web-client-code"></a>Web klientkod
Följande är web klientkod till en Demonstrationssida.

    <%@ Page Language="C#" AutoEventWireup="true" CodeFile="Default.aspx.cs" Inherits="\_Default" %>

    <!DOCTYPE html>

    <html xmlns="https://www.w3.org/1999/xhtml">
    <head runat="server">
    <title>Multi-Factor Authentication Demo</title>
    </head>
    <body>
    <h1>Azure Multi-Factor Authentication Demo</h1>
    <form id="form1" runat="server">

    <div style="width:auto; float:left">
    Username:&nbsp;<br />
    Password:&nbsp;<br />
    </div>

    <div">
    <asp:TextBox id="username" runat="server" width="100px"/><br />
    <asp:Textbox id="password" runat="server" width="100px" TextMode="password" /><br />
    </div>

    <asp:Button id="btnSubmit" runat="server" Text="Log in" onClick="btnSubmit_Click"/>

    <p><asp:Label ID="lblResult" runat="server"></asp:Label></p>

    </form>
    </body>
    </html>


### <a name="server-side-code"></a>Kod på serversidan
I följande kod för serversidan, har Multi-Factor Authentication konfigurerats och kör i steg 2. Standardläge (MODE_STANDARD) är ett telefonsamtal som användaren svarar genom att trycka på fyrkant.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.Web.UI;
    using System.Web.UI.WebControls;

    public partial class \_Default : System.Web.UI.Page
    {
        protected void Page_Load(object sender, EventArgs e)
        {
        }

        protected void btnSubmit_Click(object sender, EventArgs e)
        {
            // Step 1: Validate the username and password
            if (username.Text != "Contoso" || password.Text != "password")
            {
                lblResult.ForeColor = System.Drawing.Color.Red;
                lblResult.Text = "Username or password incorrect.";
            }
            else
            {
                // Step 2: Perform multi-factor authentication

                // Add call details from the user database.
                PfAuthParams pfAuthParams = new PfAuthParams();
                pfAuthParams.Username = username.Text;
                pfAuthParams.Phone = "5555555555";
                pfAuthParams.Mode = pf_auth.MODE_STANDARD;

                // Specify a client certificate
                // NOTE: This file contains the private key for the client
                // certificate. It must be stored with appropriate file
                // permissions.
                pfAuthParams.CertFilePath = "c:\\cert_key.p12";

                // Perform phone-based authentication
                int callStatus;
                int errorId;

                if(pf_auth.pf_authenticate(pfAuthParams, out callStatus, out errorId))
                {
                    lblResult.ForeColor = System.Drawing.Color.Green;
                    lblResult.Text = "Multi-Factor Authentication succeeded.";
                }
                else
                {
                    lblResult.ForeColor = System.Drawing.Color.Red;
                    lblResult.Text = "Multi-Factor Authentication failed.";
                }
            }

        }
    }
