---
title: Skapa Azure BizTalk Services i Azure-portalen | Microsoft Docs
description: "Lär dig hur du etablerar eller skapar Azure BizTalk Services i Azure-portalen; MABS, WABS"
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: 3ad18876-a649-40d6-9aa0-1509c1d62c43
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 11/07/2016
ms.author: mandia
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: eca77b4a82eb67e1755717bb4429f8d450a64dc5
ms.contentlocale: sv-se
ms.lasthandoff: 09/18/2017

---
# <a name="create-biztalk-services-using-the-azure-portal"></a>Skapa BizTalk Services med Azure-portalen

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]


> [!TIP]
> Om du vill logga in på Azure-portalen behöver du ett Azure-konto och en Azure-prenumeration. Om du inte har något konto kan du skapa ett kostnadsfritt utvärderingskonto på bara några minuter. Se [Kostnadsfri utvärderingsversion av Azure](http://go.microsoft.com/fwlink/p/?LinkID=239738).


## <a name="CreateService"></a>Skapa en BizTalk-tjänst
Beroende på vilken utgåva du har är det inte säkert att alla BizTalk Services-inställningar finns tillgängliga.

1. Logga in på [Azure-portalen](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. I den nedre navigeringsrutan väljer du **NYTT**:  
   ![Klicka på knappen Nytt][NEWButton]
3. Välj **APPTJÄNSTER** > **BIZTALK-TJÄNST** > **SKAPA ANPASSAD**:  
   ![Välj BizTalk-tjänst och välj Skapa anpassad][NewBizTalkService]
4. Ange BizTalk-tjänstinställningarna:
   
    <table border="1">
    <tr>
    <td><strong>BizTalk-tjänstens namn</strong></td>
    <td>Du kan ange ett namn, men var specifik. Några exempel är:<br/><br/>
    <em>mycompany</em>.biztalk.windows.net<br/>
    <em>mycompanymyapplication</em>.biztalk.windows.net<br/>
    <em>myapplication</em>.biztalk.windows.net<br/><br/>”.biztalk.windows.net” läggs automatiskt till det namn som du anger. Detta skapar en URL som används för att komma åt din BizTalk-tjänst, som t.ex. <strong>https://<em>myapplication</em>.biztalk.windows.net</strong>.
    </td>
    </tr>
    <tr>
    <td><strong>Utgåva</strong></td>
    <td>Om du är i testnings-/utvecklingsfasen väljer du <strong>Developer</strong>. Om du är i produktionsfasen använder du <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302279">BizTalk Services: Diagram över utgåvor</a> för att se om <strong>Premium</strong>, <strong>Standard</strong> eller <strong>Basic</strong> är det rätta valet för ditt affärsscenario.
    </td>
    </tr>
    <tr>
    <td><strong>Region</strong></td>
    <td>Välj ett geografiskt område som värd för din BizTalk-tjänst.</td>
    </tr>
    <tr>
    <td><strong>Domänens URL</strong></td>
    <td><strong>Valfritt</strong>. Domän-URL:en är som standard <em>YourBizTalkServiceName</em>.biztalk.windows.net. Du kan även ange en anpassad domän. Om din domän till exempel är <em>contoso</em> kan du ange: <br/><br/>
    <em>MyCompany</em>.contoso.com<br/>
    <em>MyCompanyMyApplication</em>.contoso.com<br/>
    <em>MyApplication</em>.contoso.com<br/>
    <em>YourBizTalkServiceName</em>.contoso.com<br/>
    </td>
    </tr>
    </table>
Välj pilen NÄSTA.
5. Ange inställningar för lagring och databas:  <table border="1">
    <tr>
    <td><strong>Övervaka/arkivera lagringskontot</strong></td>
    <td>Välj ett befintligt lagringskonto eller skapa ett nytt lagringskonto. <br/><br/>Om du skapar ett nytt lagringskonto anger du <strong>lagringskontonamnet</strong>.</td>
    </tr>
    <tr>
    <td><strong>Spårning av databasen</strong></td>
    <td>Om du använder en befintlig Azure SQL Database kan den inte användas av en annan BizTalk-tjänst. Du måste ha det inloggningsnamn och lösenord som angavs när Azure SQL Database-servern skapades.<br/><br/><strong>Tips</strong> Skapa spårning av databasen och övervakning/arkivering av lagringskontot i samma region som BizTalk-tjänsten.</td>
    </tr>
    </table>
Välj pilen NÄSTA.
6. Ange inställningar för databasen:  <table border="1">
    <tr>
    <td><strong>Namn</strong></td>
    <td>Tillgängligt om <strong>Skapa en ny SQL Database-instans</strong> valdes i den föregående skärmbilden.
    <br/><br/>
Ange ett namn för SQL Database som ska användas av din BizTalk-tjänst.</td>
    </tr>
    <tr>
    <td><strong>Server</strong></td>
    <td>Tillgängligt om <strong>Skapa en ny SQL Database-instans</strong> valdes i den föregående skärmbilden.
    <br/><br/>
Välj en befintlig SQL Database-server eller skapa en ny SQL Database-server.</td>
    </tr>
    <tr>
    <td><strong>Serverns inloggningsnamn</strong></td>
    <td>Ange användarnamnet för inloggningen.</td>
    </tr>
    <tr>
    <td><strong>Lösenord för serverinloggning</strong></td>
    <td>Ange inloggningslösenordet.</td>
    </tr>
    <tr>
    <td><strong>Region</strong></td>
    <td>Tillgängligt om <strong>Skapa en ny SQL Database-instans</strong> har valts. Välj den geografiska region som är värd för din SQL Database.</td>
    </tr>
    </table>

Klicka på bockmarkeringen för att slutföra guiden. Förloppsikonen visas:  
![Förloppsikonen visar när du är klar][ProgressComplete]

När du är klar har Azure BizTalk-tjänsten skapats och är redo för dina program. Standardinställningarna är tillräckliga. Om du vill ändra standardinställningarna väljer du **BIZTALK SERVICES** i den vänstra navigeringsrutan och sedan din BizTalk-tjänst. Ytterligare inställningar visas på [flikarna Instrumentpanel, Övervakare och Skalning](biztalk-dashboard-monitor-scale-tabs.md) överst.

Det finns vissa åtgärder som inte går att slutföra beroende på status för BizTalk-tjänsten. En lista över dessa åtgärder finns i [Statusdiagram för BizTalk Services](biztalk-service-state-chart.md).

## <a name="post-provisioning-steps"></a>Steg efter etableringen
* [Installera certifikatet på en lokal dator](#InstallCert)
* [Lägg till ett produktionsklart certifikat](#AddCert)
* [Hämta namnområdet Access Control](#ACS)

#### <a name="InstallCert"></a>Installera certifikatet på en lokal dator
Som en del av etableringen av BizTalk-tjänsten skapas ett självsignerat certifikat som associeras med prenumerationen på BizTalk-tjänsten. Du måste hämta certifikatet och installera det på de datorer som du antingen distribuerar BizTalk-tjänstprogram eller skickar meddelanden till en BizTalk-tjänstslutpunkt ifrån.

1. Logga in på [Azure-portalen](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Välj **BIZTALK SERVICES** i den vänstra navigeringsrutan och sedan din BizTalk-tjänstprenumeration.
3. Välj fliken **Instrumentpanel**.
4. Välj **Hämta SSL-certifikat**:  
   ![Ändra SSL-certifikat][QuickGlance]
5. Dubbelklicka på certifikatet och använd guiden för att installera det. Kontrollera att du installerar certifikatet under arkivet **Betrodda rotcertifikatutfärdare**.

#### <a name="AddCert"></a>Lägg till ett produktionsklart certifikat
Det självsignerade certifikat som skapas automatiskt när du skapar BizTalk Services är endast avsett att användas i utvecklingsmiljöer. I produktionsscenarier ersätter du det med ett produktionsklart certifikat.

1. På fliken **Instrumentpanel** väljer du **Uppdatera SSL-certifikat**.
2. Bläddra till ditt privata SSL-certifikat (*CertificateName*.pfx) som innehåller namnet på din BizTalk-tjänst, ange lösenordet och klicka sedan på bockmarkeringen.

#### <a name="ACS"></a>Hämta namnområdet Access Control
1. Logga in på [Azure-portalen](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Välj **BIZTALK SERVICES** i den vänstra navigeringsrutan och sedan din BizTalk-tjänst.
3. I aktivitetsfältet väljer du **Anslutningsinformation**:  
   ![Välj Anslutningsinformation][ACSConnectInfo]
4. Kopiera Access Control-värdena.

När du distribuerar ett BizTalk-tjänstprojekt från Visual Studio anger du detta Access Control-namnområde. Access Control-namnområdet skapas automatiskt för din BizTalk-tjänst.

Access Control-värden kan användas med alla program. När Azure BizTalk Services har skapats, kontrollerar Access Control-namnområdet autentiseringen med din distribution av BizTalk-tjänsten. Om du vill ändra prenumerationen eller hantera namnområdet väljer du **ACTIVE DIRECTORY** i den vänstra navigeringsrutan och sedan ditt namnområde. Alternativen visas i aktivitetsfältet.

Klicka på **Hantera** för att öppna Access Control-hanteringsportalen. I Access Control-hanteringsportalen använder BizTalk-tjänsten **Tjänstidentiteter**:  
![ACS-tjänstidentiteter i Access Control-hanteringsportalen][ACSServiceIdentities]

Tjänstidentiteten för Access Control är en uppsättning autentiseringsuppgifter som tillåter att program eller klienter autentiserar direkt med Access Control och tar emot en token.

> [!IMPORTANT]
> BizTalk-tjänsten använder **Ägare** som standardtjänstens identitet och värdet **Lösenord**. Om du använder det symmetriska nyckelvärdet i stället för lösenordet kan följande fel uppstå.<br/><br/>*Det gick inte att ansluta till hanteringstjänstkontot för Access Control med de angivna autentiseringsuppgifterna*
> 
> 

I [Hantera ditt ACS-namnområde](https://msdn.microsoft.com/library/azure/hh674478.aspx) visas några riktlinjer och rekommendationer.

## <a name="requirements-explained"></a>Kraven beskrivs
Dessa krav gäller inte för Free-utgåvan.

<table border="1">
<tr bgcolor="FAF9F9">
        <td><strong>Vad du behöver</strong></td>
        <td><strong>Varför du behöver den</strong></td>
</tr>
<tr>
<td>Azure-prenumeration</td>
<td>Prenumerationen styr vem som kan logga in på Azure-portalen. Kontoinnehavaren skapar prenumerationen i <a HREF="https://account.windowsazure.com/Subscriptions"> Azure-prenumerationer</a>.
<br/><br/>
Azure-kontot kan ha flera prenumerationer och kan hanteras av vem som helst som är behörig. Till exempel kan din Azure-kontoinnehavare skapa en prenumeration med namnet <em>BizTalkServiceSubscription</em> och ge BizTalk-administratörerna i företaget (till exempel ContosoBTSAdmins@live.com) åtkomst till prenumerationen. I det här scenariot loggar BizTalk-administratörerna in på Azure-portalen och har fullständiga administratörsbehörigheter till alla värdtjänster i prenumerationen, inklusive Azure BizTalk Services. BizTalk-administratörerna är inte Azure-kontoinnehavare och har därför inte tillgång till någon faktureringsinformation.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=267577"> Hantera prenumerationer och lagringskonton i Azure-portalen</a> innehåller mer information.
</td>
</tr>
<tr>
<td>Azure SQL Database</td>
<td>Lagrar tabeller, vyer och sparade procedurer som används av BizTalk-tjänsten, inklusive spårning av data.
<br/><br/>
Du kan använda en befintlig Azure SQL Server, Azure SQL Database eller automatiskt skapa en ny server eller databas när du skapar en BizTalk-tjänst.
<br/><br/>
SQL Database-skalan konfigureras automatiskt. Vanligtvis räcker standardskalan för en BizTalk-tjänst. Om du ändrar skalan påverkas prissättningen. Se <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=234930"> Konton och fakturering i Azure SQL Database</a>
<br/><br/>
<strong>Anteckningar</strong>
<br/>
<ul>
<li> När du skapar en ny Azure SQL Server och databas, aktiveras automatiskt Azure-tjänsterna. BizTalk-tjänsten kräver att Azure-tjänster är aktiverade.</li>
<li>Om du skapar en ny Azure SQL Database på en befintlig Azure SQL Server ändras inte brandväggsreglerna för servern. Det kan därför hända att andra Azure-tjänster inte har åtkomst till den serverns databaser.</li>
</ul>
</td>
</tr>
<tr>
<td>Namnområdet Azure Access Control</td>
<td>Autentiserar med Azure BizTalk Services. När du distribuerar ett BizTalk-tjänstprojekt från Visual Studio anger du detta Access Control-namnområde. När du skapar en BizTalk-tjänst skapas automatiskt Access Control-namnområdet.</td>
</tr>

<tr>
<td>Azure-lagringskonto</td>
<td>Ger åtkomst till tabeller, blobbar och köer som används av din BizTalk-tjänst för att spara följande:

<ul>
<li>Loggfiler som övervakar BizTalk-tjänsten. Övervakningens utdata visas även på fliken **Övervakning** i Azure-portalen.</li>
<li>När du skapar ett X12- eller AS2-avtal mellan partners kan du aktivera arkiveringsfunktionen för att lagra meddelandeegenskaper. Informationen sparas i lagringskontot.</li>
</ul>
<br/>
När du skapar en BizTalk-tjänst kan du använda ett befintligt lagringskonto eller automatiskt skapa ett nytt lagringskonto.
<br/><br/>
Vanligtvis är standardinställningarna för lagring tillräckliga för en BizTalk-tjänst.
<br/><br/>
När du skapar ett lagringskonto skapas automatiskt en primär nyckel och en sekundär nyckel. De här nycklarna styr åtkomsten till ditt lagringskonto. BizTalk-tjänsten använder automatiskt den primära nyckeln.
<br/><br/>
Se <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285671"> Lagring</a> för mer information.
</td>
</tr>

<tr>
<td>Privata SSL-certifikat</td>
<td>
När en Azure BizTalk-tjänst skapas, skapas också en HTTPS-URL som innehåller namnet på din BizTalk-tjänst. URL:en konfigureras automatiskt till att använda ett självsignerat certifikat som endast gäller utveckling. För produktion behöver du ett privat SSL-certifikat.
<br/><br/>
<strong>Viktig SSL-certifikatsinformation</strong>

<ul>
<li>Certifikatets förfallodatum måste infalla inom 5 år.</li>
<li>Alla privata certifikat kräver ett lösenord. Kom ihåg lösenordet och dela det med dina administratörer.</li>
<li>Självsignerade certifikat används i test-/utvecklingsmiljöer. När du använder självsignerade certifikat måste du importera certifikatet till det personliga certifikatarkivet och till certifikatarkivet Betrodda rotcertifikatutfärdare.</li>
</ul>
<br/>När du skickar en begäran om produktionscertifikat till certifikatutfärdaren anger du följande egenskaper för certifikatet:
<br/>

<ul>
<li><strong>Förbättrad nyckelanvändning</strong>: Azure BizTalk Services kräver minst serverautentisering.</li>
<li><strong>Nätverksnamn</strong>: Ange det fullständiga domännamnet (FQDN) för Azure BizTalk-tjänstens URL. Se <a HREF="#CreateService">Skapa en BizTalk-tjänst</a> i den här artikeln.</li>
</ul>
<br/>
Ett nytt eller annat certifikat kan läggas till när BizTalk-tjänsten har skapats.
</td>
</tr>
</table>
<!---Loc Comment: Please, check link [Create a BizTalk Service] since it is not redirecting to any location.--->



## <a name="hybrid-connections"></a>Hybridanslutningar
När du skapar en Azure BizTalk-tjänst är fliken **Hybridanslutningar** tillgänglig:

![Fliken Hybridanslutningar][HybridConnectionTab]

Hybridanslutningar används för att ansluta en Azure-webbplats eller mobil Azure-tjänst till alla lokala resurser som använder en statisk TCP-port, ex.vis SQL Server, MySQL, HTTP-webb-API:er, Mobile Services och de flesta anpassade webbtjänster.  Hybridanslutningar och BizTalk Adapter-tjänsten skiljer sig åt. BizTalk Adapter-tjänsten använder Azure BizTalk Services för att ansluta till ett lokalt LOB-system (Line of Business).

 Se [Hybridanslutningar](integration-hybrid-connection-overview.md) för mer information, inklusive att skapa och hantera hybridanslutningar.

## <a name="next-steps"></a>Nästa steg
Nu när en BizTalk-tjänst har skapats kan du bekanta dig med [BizTalk Services: Flikarna Instrumentpanel, Övervakare och Skalning](biztalk-dashboard-monitor-scale-tabs.md). Din BizTalk-tjänst är redo för dina program. Om du vill börja skapa program går du till [Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=235197).

## <a name="see-also"></a>Se även
* [BizTalk Services: Diagram över utgåvor](biztalk-editions-feature-chart.md)<br/>
* [BizTalk Services: Statusdiagram](biztalk-service-state-chart.md)<br/>
* [BizTalk Services: Säkerhetskopiering och återställning](biztalk-backup-restore.md)<br/>
* [BizTalk Services: Begränsning](biztalk-throttling-thresholds.md)<br/>
* [BizTalk Services: Utfärdarens namn och nyckel](biztalk-issuer-name-issuer-key.md)<br/>
* [Hur gör jag för att börja använda Azure BizTalk Services SDK?](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
* [Hybridanslutningar](integration-hybrid-connection-overview.md)

[NewBizTalkService]: ./media/biztalk-provision-services/WABS_NewBizTalkService.png
[NEWButton]: ./media/biztalk-provision-services/WABS_New.png
[ProgressComplete]: ./media/biztalk-provision-services/WABS_ProgressComplete.png
[ACSConnectInfo]: ./media/biztalk-provision-services/WABS_ACSConnectInformation.png
[QuickGlance]: ./media/biztalk-provision-services/WABS_QuickGlance.png
[ACSServiceIdentities]: ./media/biztalk-provision-services/WABS_ACSServiceIdentities.png
[HybridConnectionTab]: ./media/biztalk-provision-services/WABS_HybridConnectionTab.png

