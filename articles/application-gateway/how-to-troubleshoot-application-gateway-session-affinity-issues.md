---
title: Felsöka Azure Application Gateway problem med Sessionstillhörighet
description: Den här artikeln innehåller information om hur du felsöker problem med sessionstillhörighet i Azure Application Gateway
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/22/2019
ms.author: absha
ms.openlocfilehash: bcfb50a2d7d5221abaa15a5b286163cf63b7a875
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58446679"
---
# <a name="troubleshoot-azure-application-gateway-session-affinity-issues"></a>Felsöka Azure Application Gateway problem med Sessionstillhörighet

Lär dig att diagnostisera och lösa problem med sessionstillhörighet med Azure Application Gateway.

## <a name="overview"></a>Översikt

Den cookie-baserade sessionstillhörighetsfunktionen är användbar när du vill behålla en användarsession på samma server. Genom att använda gatewayhanterade cookies kan Application Gateway dirigera efterföljande trafik från en användarsession till samma serverdel för bearbetning. Det här är viktigt i de fall där sessionstillstånd har sparats lokalt på servern för en användarsession.

## <a name="possible-problem-causes"></a>Möjligt problem orsaker

Problemet i att upprätthålla cookie-baserad sessionstillhörighet kan inträffa på grund av följande huvudsakliga skäl:

- ”Cookie-baserad tillhörighet” inställningen är inte aktiverad
- Programmet kan inte hantera cookie-baserad tillhörighet
- Programmet använder cookie-baserad tillhörighet men förfrågningar som fortfarande lyser mellan backend-servrar

### <a name="check-whether-the-cookie-based-affinity-setting-is-enabled"></a>Kontrollera om ”Cookie-baserad tillhörighet”-inställningen har aktiverats

De problem med sessionstillhörighet kan ibland uppstå när du glömmer att aktivera inställningen för ”Cookiebaserad tillhörighet”. Följ anvisningarna för att avgöra om du har aktiverat inställningen ”Cookiebaserad tillhörighet” på fliken HTTP-inställningar i Azure portal:

1. Logga in på [Azure Portal](https://portal.azure.com/).

2. I den **vänster navigeringsfält** fönstret klickar du på **alla resurser**. Klicka på application gateway-namnet på bladet alla resurser. Om den prenumeration du valde redan har flera resurser kan du ange namnet på programmet gateway i den **filtrera efter namn...** för att enkelt få åtkomst till Application Gateway.

3. Välj **HTTP-inställningar** fliken **inställningar**.

   ![Felsöka-session-tillhörighet-problem med-1](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-1.png)

4. Klicka på **appGatewayBackendHttpSettings** till höger för att kontrollera om du har valt **aktiverad** för Cookie-baserad tillhörighet.

   ![Felsöka-session-tillhörighet-problem – 2](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-2.jpg)



Du kan också kontrollera värdet för den ”**CookieBasedAffinity**” är inställt på *aktiverad*under ”**backendHttpSettingsCollection**” med någon av följande metoder:

- Kör [Get-AzureRmApplicationGatewayBackendHttpSettings](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermapplicationgatewaybackendhttpsettings) i PowerShell
- Titta igenom JSON-fil med hjälp av Azure Resource Manager-mall

```
"cookieBasedAffinity": "Enabled", 
```

### <a name="the-application-cannot-handle-cookie-based-affinity"></a>Programmet kan inte hantera cookie-baserad tillhörighet

#### <a name="cause"></a>Orsak

Application gateway kan bara utföra session-baserad tillhörighet genom att använda en cookie.

#### <a name="workaround"></a>Lösning

Om programmet inte kan hantera cookie-baserad tillhörighet, måste du använda en extern eller intern azure belastningsutjämnare eller en annan tredjepartslösning.

### <a name="application-is-using-cookie-based-affinity-but-requests-still-bouncing-between-back-end-servers"></a>Programmet använder cookie-baserad tillhörighet men förfrågningar som fortfarande lyser mellan backend-servrar

#### <a name="symptom"></a>Symtom

Du har aktiverat inställningen Cookie-baserad tillhörighet när du har åtkomst till Application Gateway med hjälp av ett kort namn-URL i Internet Explorer, till exempel: [ http://website ](http://website/) , begäran fortfarande lyser mellan backend-servrar.

Följ anvisningarna för att identifiera det här problemet:

1. Koppla en felsökare web-spårning på ”klienten” som ansluter till programmet bakom programmet Gateway(We are using Fiddler in this example).
    **Tips** om du inte vet hur du använder Fiddler, markerar du alternativet ”**jag vill samla in nätverkstrafik och analysera den med hjälp av web felsökare**” längst ned på sidan.

2. Kontrollera och analysera sessionsloggarna, för att avgöra om de cookies som tillhandahålls av klienten har ARRAffinity information. Om du inte hittar ARRAffinity mer information, till exempel ”**ARRAffinity =** *ARRAffinityValue*” i uppsättningen cookie, som innebär att klienten inte svara med form-cookie som tillhandahålls av den Application Gateway.
    Exempel:

    ![Felsöka-session-tillhörighet-problem-3](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-3.png)

        ![troubleshoot-session-affinity-issues-4](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-4.png)

Programmet fortsätter att försök att ange cookien för varje begäran tills den får svar.

#### <a name="cause"></a>Orsak

Det här problemet uppstår eftersom Internet Explorer och andra webbläsare inte kan lagra eller använda cookien med en URL för kort namn.

#### <a name="resolution"></a>Lösning

För att åtgärda problemet, bör du komma åt Application Gateway med hjälp av ett fullständigt domännamn. Till exempel använda [ http://website.com ](http://website.com/) eller [ http://appgw.website.com ](http://appgw.website.com/) .

## <a name="additional-logs-to-troubleshoot"></a>Ytterligare loggar att felsöka

Du kan samla in ytterligare loggar och analysera dem för att felsöka problem med relaterade Cookiebaserad-tillhörighet

### <a name="analyze-application-gateway-logs"></a>Analysera Application Gateway-loggar

Följ anvisningarna för att samla in Application Gateway-loggar:

aktivera loggning via Azure Portal

1. I den [Azure-portalen](https://portal.azure.com/), hitta resursen och klicka sedan på **diagnostikloggar**.

   Tre loggar är tillgängliga för Application Gateway: Åtkomstlogg, prestandaloggen, brandväggslogg

2. Om du vill börja samla in data, klickar du på **slå på diagnostik**.

   ![Felsöka-session-tillhörighet-problem – 5](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-5.png)

3. Den **diagnostikinställningar** bladet innehåller inställningarna för diagnostiska loggar. I det här exemplet lagras loggarna i Log Analytics. Klicka på **konfigurera** under **Log Analytics** att ställa in din arbetsyta. Du kan också använda händelsehubbar och ett lagringskonto till att spara dina diagnostiska loggar.

   ![Felsöka-session-tillhörighet-problem – 6](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-6.png)

4. Bekräfta inställningarna och klicka sedan på **spara**.

   ![Felsöka-session-tillhörighet-problem – 7](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-7.png)

#### <a name="view-and-analyze-the-application-gateway-access-logs"></a>Visa och analysera loggar för Application Gateway-åtkomst

1. I Azure-portalen under resursvy Application Gateway, väljer **diagnostikloggar** i den **övervakning** avsnittet.

   ![Felsöka-session-tillhörighet-problem – 8](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-8.png)

2. Till höger, Välj ”**ApplicationGatewayAccessLog**” i listrutan under **logga kategorier.**  

   ![Felsöka-session-tillhörighet-problem – 9](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-9.png)

3. Klicka på den logg som du vill analysera och exportera i listan Åtkomstlogg för Application Gateway och sedan exportera JSON-filen.

4. Konvertera den JSON-fil som du exporterade i steg 3 till CSV-fil och visa dem i Excel, Power BI eller något annat verktyg för visualisering av data.

5. Kontrollera följande data:

- **ClientIP**– detta är klientens IP-adress från den anslutande klienten.
- **ClientPort** – detta är källport från den anslutande klienten för begäran.
- **RequestQuery** – detta anger att en begäran tas emot målservern.
- **Server-dirigerat**: Backend-serverpoolinstans att en begäran tas emot.
- **X-AzureApplicationGateway-LOG-ID**: Korrelations-ID som användes för begäran. Det kan användas för att felsöka problem med trafik på backend servrarna. Exempel: X-AzureApplicationGateway-CACHE-HIT=0&SERVER-ROUTED=10.0.2.4.

  - **SERVER-STATUS**: HTTP-svarskoden som Application Gateway har fått från backend-servern.

  ![Felsöka-session-tillhörighet-problem – 11](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-11.png)

Om du ser två objekt kommer från samma ClientIP och klientport och de skickas till samma backend-server, det innebär att Application-Gateway som konfigurerats korrekt.

Om du ser två objekt kommer från samma ClientIP och klientport och de skickas till olika backend-servrar, som innebär att förfrågan lyser mellan backend-servrar, Välj ”**program använder cookie-baserad tillhörighet men begäranden fortfarande lyser mellan backend-servrarna**”längst ned i samband med det.

### <a name="use-web-debugger-to-capture-and-analyze-the-http-or-https-traffics"></a>Använda web felsökare för att samla in och analysera HTTP eller HTTPS så

Webb-felsökning verktyg som Fiddler, kan hjälpa dig att felsöka webbprogram genom att samla in nätverkstrafik mellan Internet- och test. Dessa verktyg kan du granska inkommande och utgående data när webbläsaren tar emot/skickar dem. Fiddler, har i det här exemplet repetitionsattacker HTTP-alternativet som kan hjälpa dig att felsöka problem på klientsidan med webbprogram, särskilt för typ av autentisering av problemet.

Använda felsökaren web valfri. I det här exemplet använder vi Fiddler för att samla in och analysera http eller https så, följ instruktionerna:

1. Hämta verktyget Fiddler på <https://www.telerik.com/download/fiddler>.

    > [!NOTE]
    > Välj Fiddler4 om in datorn har .NET 4 vara installerat. Annars väljer du Fiddler2.

2. Högerklicka på den körbara Installationsprogramfilen och kör som administratör för att installera.

            ![troubleshoot-session-affinity-issues-12](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-12.png)

3. När du öppnar Fiddler bör automatiskt börja samla in trafik (Observera hämtar i nedre vänstra hörnet). Tryck på F12 för att starta eller stoppa trafik avbildning.

        ![troubleshoot-session-affinity-issues-13](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-13.png)

4. Mest sannolikt du är intresserad av dekrypterade HTTPS-trafik, och du kan aktivera HTTPS-dekryptering genom att välja **verktyg** > **Fiddler alternativ**, och markera kryssrutan ” **dekryptera HTTPS-trafik**”.

        ![troubleshoot-session-affinity-issues-14](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-14.png)

5. Du kan ta bort tidigare orelaterade sessioner innan återge problemet genom att klicka på **X** (ikon) > **ta bort alla** följer skärmbild: 

        ![troubleshoot-session-affinity-issues-15](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-15.png)

6. När du har återskapat problemet, spara filen för granskning genom att välja **filen** > **spara** > **alla sessioner...** . 

        ![troubleshoot-session-affinity-issues-16](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-16.png)

7. Kontrollera och analysera sessionsloggar för att fastställa vad problemet är.

    Exempel:

- **Exempel A:** Du hittar en sessionsloggen som begäran skickades från klienten och den går till offentliga IP-adressen för Application Gateway, klickar du på den här loggen om du vill visa information.  Till höger är data i den nedre rutan Application Gateway returneras till klienten. Välj fliken ”RAW” och avgör om klienten tar emot en ”**Set-Cookie: ARRAffinity =** *ARRAffinityValue*”. Om det finns ingen cookie, sessionstillhörighet har inte angetts eller Application Gateway är inte tillämpa cookie tillbaka till klienten.

   > [!NOTE]
   > Den här ARRAffinity-värdet är cookie-id som Application Gateway anger för att klienten ska skickas till en viss backend-server.

    ![Felsöka-session-tillhörighet-problem – 17](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-17.png)

- **Exempel B:** Nästa sessionsloggen följt av den tidigare en är klienten svarar tillbaka till Application Gateway, som har angetts i ARRAAFFINITY. Om ARRAffinity cookie-id matchar ska paketet skickas till samma backend-server som har använts tidigare. Kontrollera nästa flera rader med http-kommunikation för att se om klientens ARRAffinity cookie ändras.

    ![Felsöka-session-tillhörighet-problem – 18](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-18.png)

> [!NOTE]
> För kommunikationen samma session bör cookien inte om du vill ändra. Kontrollera den översta rutan till höger och välj ”Cookies” fliken för att se om klienten är med hjälp av cookien och skickas tillbaka till Application Gateway. Om inte, klientens webbläsare inte är att behålla och använda cookien för konversationer. Klienten kan ibland finns.

 

## <a name="next-steps"></a>Nästa steg

Om föregående steg inte löser problemet kan du öppna en [supportärende](https://azure.microsoft.com/support/options/).