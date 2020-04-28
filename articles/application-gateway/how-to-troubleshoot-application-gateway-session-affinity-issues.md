---
title: Felsöka problem med sessionstillhörighet
titleSuffix: Azure Application Gateway
description: Den här artikeln innehåller information om hur du felsöker problem med sessionstillhörighet i Azure Application Gateway
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: aa3617b30fe1ef9b4d4a6c5fe5aac51bff95bb92
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81866679"
---
# <a name="troubleshoot-azure-application-gateway-session-affinity-issues"></a>Felsöka problem med tillhörighet för Azure Application Gateway-session

Lär dig hur du diagnostiserar och löser problem med sessionstillhörighet med Azure Application Gateway.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Översikt

Den cookie-baserade sessionstillhörighetsfunktionen är användbar när du vill behålla en användarsession på samma server. Genom att använda gatewayhanterade cookies kan Application Gateway dirigera efterföljande trafik från en användarsession till samma serverdel för bearbetning. Det här är viktigt i de fall där sessionstillstånd har sparats lokalt på servern för en användarsession.

## <a name="possible-problem-causes"></a>Möjliga problemorsakare

Problemet med att upprätthålla cookie-baserad sessionstillhörighet kan uppstå på grund av följande huvudorsaker:

- Inställningen "Cookie-baserad tillhörighet" är inte aktiverad
- Din applikation kan inte hantera cookie-baserad tillhörighet
- Programmet använder cookie-baserad tillhörighet men begär fortfarande studsar mellan backend-servrar

### <a name="check-whether-the-cookie-based-affinity-setting-is-enabled"></a>Kontrollera om inställningen "Cookie-baserad tillhörighet" är aktiverad

Ibland kan sessionstillhörighetsproblem uppstå när du glömmer att aktivera inställningen "Cookie-baserad tillhörighet". Så här tar du reda på om du har aktiverat inställningen "Cookie-baserad tillhörighet" på fliken HTTP-inställningar i Azure-portalen:

1. Logga in på [Azure portal](https://portal.azure.com/).

2. Klicka på **Alla resurser**i det **vänstra navigeringsfönstret** . Klicka på programgatewaynamnet i bladet Alla resurser. Om prenumerationen som du har valt redan har flera resurser i den kan du ange programmets gatewaynamn i **filtret efter namn...** för att enkelt få åtkomst till Application Gateway.

3. Välj **fliken HTTP-inställningar** under **INSTÄLLNINGAR**.

   ![felsöka-session-tillhörighet-frågor-1](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-1.png)

4. Klicka på **appGatewayBackendHttpSettings** till höger för att kontrollera om du har valt **Aktiverad** för Cookie-baserad tillhörighet.

   ![felsöka-session-tillhörighet-frågor-2](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-2.jpg)



Du kan också kontrollera att värdet för "**CookieBasedAffinity**" är inställt *på Aktiverad*under "**backendHttpSettingsCollection**" med någon av följande metoder:

- Kör [Get-AzApplicationGatewayBackendHttpSetting](https://docs.microsoft.com/powershell/module/az.network/get-azapplicationgatewaybackendhttpsetting) i PowerShell
- Titta igenom JSON-filen med hjälp av Azure Resource Manager-mallen

```
"cookieBasedAffinity": "Enabled", 
```

### <a name="the-application-cannot-handle-cookie-based-affinity"></a>Programmet kan inte hantera cookie-baserad tillhörighet

#### <a name="cause"></a>Orsak

Programgatewayen kan bara utföra sessionsbaserad tillhörighet med hjälp av en cookie.

#### <a name="workaround"></a>Lösning

Om programmet inte kan hantera cookie-baserad tillhörighet måste du använda en extern eller intern azure load balancer eller en annan tredjepartslösning.

### <a name="application-is-using-cookie-based-affinity-but-requests-still-bouncing-between-back-end-servers"></a>Programmet använder cookie-baserad tillhörighet men begär fortfarande studsar mellan backend-servrar

#### <a name="symptom"></a>Symptom

Du har aktiverat inställningen Cookie-baserad tillhörighet när du öppnar Application Gateway med hjälp av en [http://website](http://website/) kort namn-URL i Internet Explorer, till exempel: , begäran studsar fortfarande mellan backend-servrar.

Så här identifierar du problemet:

1. Ta en webbfelsökare spårning på "Klient" som ansluter till programmet bakom Application Gateway (Vi använder Fiddler i det här exemplet).
    **Tips** Om du inte vet hur du använder Spelmannen kontrollerar du alternativet "**Jag vill samla in nätverkstrafik och analysera den med hjälp av webbfelsökare**" längst ned.

2. Kontrollera och analysera sessionsloggarna för att avgöra om cookies som tillhandahålls av klienten har ARRAffinity-informationen. Om du inte hittar ARRAffinity-detaljerna, till exempel "**ARRAffinity =** *ARRAffinityValue*" i cookie-uppsättningen, betyder det att klienten inte svarar med ARRA-cookien, som tillhandahålls av Application Gateway.
    Ett exempel:

    ![felsöka-session-tillhörighet-frågor-3](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-3.png)

    ![felsöka-session-tillhörighet-frågor-4](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-4.png)

Programmet fortsätter att försöka ställa in cookien på varje begäran tills den får svar.

#### <a name="cause"></a>Orsak

Det här problemet beror på att Internet Explorer och andra webbläsare kanske inte lagrar eller använder cookien med en url med kort namn.

#### <a name="resolution"></a>Lösning

Åtgärda problemet genom att ansluta till Application Gateway med hjälp av ett fullständigt domännamn. Använd [http://website.com](https://website.com/) eller [http://appgw.website.com](http://website.com/) .

## <a name="additional-logs-to-troubleshoot"></a>Ytterligare loggar att felsöka

Du kan samla in ytterligare loggar och analysera dem för att felsöka problem relaterade cookie-baserade sessionstillhörighet

### <a name="analyze-application-gateway-logs"></a>Analysera application gateway-loggar

Så här samlar du in application gateway-loggarna:

aktivera loggning via Azure Portal

1. Leta reda på din resurs i [Azure-portalen](https://portal.azure.com/)och klicka sedan på **Diagnostikloggar**.

   För Application Gateway finns tre loggar tillgängliga: Åtkomstlogg, Prestandalogg, Brandväggslogg

2. Om du vill börja samla in data klickar du **på Aktivera diagnostik**.

   ![felsöka-session-tillhörighet-frågor-5](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-5.png)

3. Bladet **Diagnostikinställningar** innehåller inställningarna för diagnostikloggarna. I det här exemplet lagrar Log Analytics loggarna. Klicka på **Konfigurera** under **Logganalys** för att ange arbetsytan. Du kan också använda händelsehubbar och ett lagringskonto till att spara dina diagnostiska loggar.

   ![felsöka-session-tillhörighet-frågor-6](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-6.png)

4. Bekräfta inställningarna och klicka sedan på **Spara**.

   ![felsöka-session-tillhörighet-frågor-7](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-7.png)

#### <a name="view-and-analyze-the-application-gateway-access-logs"></a>Visa och analysera åtkomstloggarna för Application Gateway

1. I Azure-portalen under resursvyn Application Gateway väljer du **Diagnostikloggar** i avsnittet **ÖVERVAKNING** .

   ![felsöka-session-tillhörighet-frågor-8](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-8.png)

2. På höger sida väljer du "**ApplicationGatewayAccessLog**" i listrutan under **Loggkategorier.**  

   ![felsöka-session-tillhörighet-frågor-9](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-9.png)

3. Klicka på den logg som du vill analysera och exportera i listan Application Gateway Access Log och exportera sedan JSON-filen.

4. Konvertera JSON-filen som du exporterade i steg 3 till CSV-filen och visa dem i Excel, Power BI eller något annat datavisualiseringsverktyg.

5. Kontrollera följande data:

- **ClientIP**– Det här är klientens IP-adress från den anslutande klienten.
- **ClientPort** - Det här är källporten från anslutningsklienten för begäran.
- **RequestQuery** – Detta anger målservern att begäran tas emot.
- **Server-dirigerad**: Backend poolinstans som begäran tas emot.
- **X-AzureApplicationGateway-LOG-ID**: Korrelations-ID som används för begäran. Den kan användas för att felsöka trafikproblem på backend-servrarna. Till exempel: X-AzureApplicationGateway-CACHE-HIT=0&SERVER-ROUTED=10.0.2.4.

  - **SERVER-STATUS**: HTTP-svarskod som Application Gateway har tagits emot från serverdelen.

  ![felsöka-session-tillhörighet-frågor-11](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-11.png)

Om två objekt kommer från samma ClientIP och Klientport och de skickas till samma server för serverdelsserver, betyder det att Application Gateway har konfigurerats korrekt.

Om två objekt kommer från samma ClientIP och Klientport, och de skickas till de olika backend-servrarna, betyder det att begäran studsar mellan serverdelsservrar, väljer "**Programmet använder cookie-baserad tillhörighet men begäranden studsar fortfarande mellan backend-servrar**" längst ned för att felsöka den.

### <a name="use-web-debugger-to-capture-and-analyze-the-http-or-https-traffics"></a>Använda webbfelsökare för att samla in och analysera HTTP- eller HTTPS-trafik

Webbfelsökningsverktyg som Fiddler kan hjälpa dig att felsöka webbprogram genom att samla in nätverkstrafik mellan Internet och testdatorer. Med dessa verktyg kan du granska inkommande och utgående data när webbläsaren tar emot/skickar dem. Fiddler har i det här exemplet http-uppspelningsalternativet som kan hjälpa dig att felsöka problem på klientsidan med webbprogram, särskilt för autentiseringsslag.

Använd den webbfelsökare du väljer. I det här exemplet kommer vi att använda Fiddler för att fånga och analysera http eller https-trafik, följ instruktionerna:

1. Ladda ner Fiddler <https://www.telerik.com/download/fiddler>verktyget på .

    > [!NOTE]
    > Välj Fiddler4 om den fånga datorn har .NET 4 installerat. Annars väljer du Fiddler2.

2. Högerklicka på den körbara inställningen och kör som administratör för att installera.

    ![felsöka-session-tillhörighet-frågor-12](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-12.png)

3. När du öppnar Fiddler bör den automatiskt börja fånga trafik (lägg märke till att fånga i nedre vänstra hörnet). Tryck på F12 för att starta eller stoppa trafikinsamling.

    ![felsöka-session-tillhörighet-frågor-13](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-13.png)

4. Troligtvis kommer du att vara intresserad av dekrypterad HTTPS-trafik, och du kan aktivera HTTPS dekryptering genom att välja **Verktyg** > **Fiddler Alternativ**och markera rutan " **Dekryptera HTTPS-trafik**".

    ![felsöka-session-tillhörighet-frågor-14](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-14.png)

5. Du kan ta bort tidigare orelaterade sessioner innan du återskapar problemet genom att klicka på **X** (ikon) > **Ta bort alla** enligt skärmbild: 

    ![felsöka-session-tillhörighet-frågor-15](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-15.png)

6. När du har återgett problemet sparar du filen för granskning genom att välja **Spara** > **Save** > **alla sessioner..**. 

    ![felsöka-session-tillhörighet-frågor-16](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-16.png)

7. Kontrollera och analysera sessionsloggarna för att avgöra vad problemet är.

    För exempel:

- **Exempel A:** Du hittar en sessionslogg som begäran skickas från klienten, och den går till den offentliga IP-adressen för Application Gateway, klicka på den här loggen för att visa informationen.  Till höger är data i den nedre rutan vad Application Gateway returnerar till klienten. Välj fliken "RAW" och bestäm om klienten får en "**Set-Cookie: ARRAffinity=** *ARRAffinityValue*." Om det inte finns någon cookie har sessionstillhörigheten inte angetts eller så tillämpas inte cookien tillbaka till klienten.

   > [!NOTE]
   > Det här ARRAffinity-värdet är cookie-id, som Application Gateway anger för att klienten ska skickas till en viss backend-server.

   ![felsöka-session-tillhörighet-frågor-17](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-17.png)

- **Exempel B:** Nästa sessionslogg följt av den föregående är klienten som svarar tillbaka på Application Gateway, som har angett ARRAAFFINITY. Om cookie-id-ID:t ARRAffinity matchar ska paketet skickas till samma server för server för server för server som användes tidigare. Kontrollera nästa flera rader i http-kommunikation för att se om klientens ARRAffinity cookie förändras.

   ![felsöka-session-tillhörighet-frågor-18](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-18.png)

> [!NOTE]
> För samma kommunikationssession ska cookien inte ändras. Markera den övre rutan till höger, välj fliken "Cookies" för att se om klienten använder cookien och skickar tillbaka den till Application Gateway. Om inte, är klienten webbläsaren inte hålla och använda cookie för konversationer. Ibland kan klienten ljuga.

 

## <a name="next-steps"></a>Nästa steg

Om föregående steg inte löser problemet öppnar du en [supportbiljett](https://azure.microsoft.com/support/options/).
