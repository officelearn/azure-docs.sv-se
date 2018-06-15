---
title: Azure MFA SDK för anpassade appar
description: Den här artikeln visar hur du hämtar och använder Azure MFA SDK för att aktivera tvåstegsverifiering för dina egna appar.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 11/29/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: 28b48df27bf9b2f7176b886ef684f9281b3c4f37
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
ms.locfileid: "33866051"
---
# <a name="building-multi-factor-authentication-into-custom-apps-sdk"></a>Skapa Multifaktorautentisering i anpassade appar (SDK)

> [!IMPORTANT]
> Utfasningen av Azure Multi-Factor Authentication Software Development Kit (SDK) har meddelats. Den här funktionen kommer inte längre stöd för nya kunder. Kunderna kan fortsätta använda SDK:n till den 14 november 2018. Efter det misslyckas anrop till SDK. 

Azure Multi-Factor Authentication Software Development Kit (SDK) kan du skapa tvåstegsverifiering direkt i inloggning eller transaktionen processer för program i Azure AD-klienten.

Multi-Factor Authentication SDK är tillgängligt för C#, Visual Basic (.NET), Java, Perl, PHP och Ruby. SDK innehåller en tunn wrapper runt tvåstegsverifiering. Den innehåller allt du behöver skriva koden, inklusive kommenterad källkodsfiler, exempel filer och en detaljerad viktigt-fil. Varje SDK innehåller också ett certifikat och privat nyckel för att kryptera transaktioner som är unika för multi-Factor Authentication Provider. Så länge som du har en provider, kan du hämta SDK i så många språk och format som du behöver.

Strukturen för API: er i Multi-Factor Authentication SDK är enkelt. Gör en enskild funktion anrop till en API med Multi-Factor alternativet parametrar (t.ex. verifieringen läge) och användardata (till exempel den telefonnummer eller PIN-kod för att verifiera). API: erna översätta funktionsanropet i tjänster webbegäranden till molnbaserad Azure Multi-Factor Authentication-tjänsten. Alla anrop måste innehålla en referens till det privata certifikat som ingår i varje SDK.

Eftersom API: erna inte har åtkomst till användare som har registrerats i Azure Active Directory, måste du ange användarinformation i en fil eller databas. Dessutom tillhandahåller API: er funktioner för registrering eller användare, så du måste skapa de här processerna i ditt program.

> [!IMPORTANT]
> För att hämta SDK:n måste du skapa en Azure Multi-Factor Auth-provider, även om du har Azure MFA-, AAD Premium- eller EMS-licenser. Om du skapar ett Azure leverantör av Multifaktorautent för detta ändamål och redan har licenser, se till att skapa Provider med den **Per aktiverad användare** modell. Koppla sedan providern till katalogen som innehåller Azure MFA, Azure AD Premium eller EMS-licenser. Den här konfigurationen garanterar att du endast debiteras om du har flera unika användare med hjälp av SDK än antalet licenser du äger.


## <a name="download-the-sdk"></a>Ladda ned SDK
Hämta SDK för Azure Multi-Factor kräver en [Azure leverantör av Multifaktorautent](concept-mfa-authprovider.md).  Detta kräver en fullständig Azure-prenumeration, även om Azure MFA, Azure AD Premium eller Enterprise Mobility Suite licenser äger. De offentliga metoderna för att hämta SDK har inaktiverats eftersom SDK är föråldrad. Du måste öppna ett supportärende med Microsoft om du behöver hämta SDK. SDK tillhandahålls endast för kunder som redan använder SDK. Nya kunder kommer inte att publicera.

## <a name="whats-in-the-sdk"></a>Vad är i SDK
SDK innehåller följande:

* **VIKTIGT**. Förklarar hur du använder multi-Factor Authentication-API: er i ett nytt eller befintligt program.
* **Källfiler** för Multifaktorautentisering
* **Klientcertifikatet** som används för att kommunicera med Multi-Factor Authentication-tjänsten
* **Privat nyckel** för certifikatet
* **Anropa resultat.** En lista över resultatkoder för webbtjänstanrop. För att öppna den här filen genom att använda ett program med textformatering, till exempel WordPad. Använd resultatkoder för anrop för att testa och felsöka implementeringen av Multi-Factor Authentication i ditt program. De är inte statuskoder för autentisering.
* **exempel.** Exempelkod för en grundläggande fungerande implementeringen av Multi-Factor Authentication.

> [!WARNING]
> Klientcertifikatet är en unik privat certifikat som har genererats särskilt för dig. Dela eller inte förlorar den här filen. Det är din nyckel för att säkerställa säkerheten för din kommunikation med Multi-Factor Authentication-tjänsten.

## <a name="code-sample"></a>Kodexempel
Det här kodexemplet visar hur du använder API: erna i Azure Multi-Factor Authentication SDK för att lägga till standardläge röst anropet verifiering i ditt program. Standardläge är ett telefonsamtal som användaren svarar genom att trycka på #.

Det här exemplet använder C# .NET 2.0 Multi-Factor Authentication SDK i ett grundläggande ASP.NET-program med C# serversidan logik, men processen påminner om på andra språk. Eftersom SDK innehåller källfiler, inte körbara filer kan skapa filer och referera till dem eller lägga till dem direkt i ditt program.

> [!NOTE]
> När du implementerar Multifaktorautentisering använda metoderna ytterligare (telefonsamtal eller SMS) som sekundär eller tertiär verifiering för att komplettera primära autentiseringsmetod (användarnamn och lösenord). Dessa metoder är inte avsedda som primära autentiseringsmetoder.

### <a name="code-sample-overview"></a>Översikt över kod-exempel
Den här exempelkoden för en enkel demo-webbapp använder ett telefonsamtal med svaret # för att verifiera användarautentiseringen. Denna telefonsamtal faktor kallas i Multi-Factor Authentication standardläge.

Koden för klientsidan innehåller inte några Multi-Factor Authentication-specifika element. Eftersom ytterligare autentiseringsfaktorer är oberoende av den primära autentiseringen, du kan lägga till dem utan att ändra befintlig inloggning gränssnittet. API: er i Multi-Factor SDK kan du anpassa användarupplevelsen, men du kanske inte behöver ändra något alls.

Serverkoden som lägger till standard-autentisering i steg 2. Den skapar ett PfAuthParams-objekt med de parametrar som krävs för verifiering i standard-läge: användarnamn, telefon siffra, och läge och sökvägen till klientcertifikatet (CertFilePath) som krävs i varje anrop. En demonstration av alla parametrar i PfAuthParams finns i SDK-exempelfil.

Koden skickar sedan PfAuthParams objektet till funktionen pf_authenticate(). Det returnera värdet anger lyckad eller misslyckad verifiering. Den out-parametrar, callStatus och samtalsstatus kan innehålla ytterligare information om resultatet. Resultatkoder för webbtjänstanrop dokumenteras i resultatfilen anrop i SDK.

Den här minimal implementeringen kan skrivas i några rader. I produktionskod, skulle du dock inkludera mer felhantering, ytterligare databaskod och en förbättrad användarupplevelse.

### <a name="web-client-code"></a>Web klientkod
Följande är web klientkod för en Demonstrationssida.

    <%@ Page Language="C#" AutoEventWireup="true" CodeFile="Default.aspx.cs" Inherits="\_Default" %>

    <!DOCTYPE html>

    <html xmlns="http://www.w3.org/1999/xhtml">
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
I följande kod för servern, har Multi-Factor Authentication konfigurerats och kör i steg 2. Standardläge (MODE_STANDARD) är ett telefonsamtal som användaren svarar genom att trycka på #.

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
