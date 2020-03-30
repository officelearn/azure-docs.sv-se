---
title: Distribuera diagnostikverktyget för Windows Virtual Desktop – Azure
description: Distribuera UX-verktyget för diagnostik för Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 4eb63fe4bd8f8a8b0961aa6a7fccb8de9b7c2f16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80123414"
---
# <a name="deploy-the-diagnostics-tool"></a>Distribuera diagnostikverktyget

>[!IMPORTANT]
>Från och med den 16 mars 2020 har vi tillfälligt inaktiverat diagnostiska frågor som påverkade användarupplevelsen på grund av ökad efterfrågan på tjänsten. Detta gör att verktyget slutar fungera eftersom det är beroende av att dessa frågor fungerar. Vi uppdaterar den här artikeln när diagnostikfrågor är tillgängliga igen.
>
>Fram till dess rekommenderar vi starkt att du [använder Log Analytics](diagnostics-log-analytics.md) för fortsatt övervakning.

Det här är vad diagnostikverktyget för Windows Virtual Desktop kan göra för dig:

- Slå upp diagnostiska aktiviteter (hantering, anslutning eller feed) för en enskild användare under en vecka.
- Samla in sessionsvärdinformation för anslutningsaktiviteter från logganalysarbetsytan.
- Granska prestandainformation för virtuella datorer (VM) för en viss värd.
- Se vilka användare som är inloggade på sessionsvärden.
- Skicka meddelande till aktiva användare på en viss sessionsvärd.
- Logga ut användare från en sessionsvärd.

## <a name="prerequisites"></a>Krav

Du måste skapa en Azure Active Directory-appregistrering och en Log Analytics-arbetsyta innan du kan distribuera Azure Resource Manager-mallen för verktyget. Du eller administratören behöver dessa behörigheter för att göra det:

- Ägare av Azure-prenumerationen
- Behörighet att skapa resurser i din Azure-prenumeration
- Behörighet att skapa en Azure AD-app
- RDS-ägare eller deltagarrättigheter

Du måste också installera dessa två PowerShell-moduler innan du börjar:

- [Azure PowerShell-modul](/powershell/azure/install-az-ps?view=azps-2.4.0/)
- [Azure AD-modul](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0/)

Se till att ditt prenumerations-ID är redo när du loggar in.

När du har allt i ordning kan du skapa Azure AD-appregistreringen.

## <a name="create-an-azure-active-directory-app-registration"></a>Skapa en registrering av Active Directory-program i Azure

I det här avsnittet visas hur du använder PowerShell för att skapa Azure Active Directory-appen med ett tjänsthuvudnamn och få API-behörigheter för den.

>[!NOTE]
>API-behörigheterna är behörigheterna Windows Virtual Desktop, Log Analytics och Microsoft Graph API läggs till i Azure Active Directory Application.

1. Öppna PowerShell som administratör.
2. Logga in på Azure med ett konto som har behörigheter för ägare eller deltagare för den Azure-prenumeration som du vill använda för diagnostikverktyget:
   ```powershell
   Login-AzAccount
   ```
3. Logga in på Azure AD med samma konto:
   ```powershell
   Connect-AzureAD
   ```
4. Gå till [RDS-mallarna GitHub repo](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy/scripts) och kör **CreateADAppRegistrationforDiagnostics.ps1** skriptet i PowerShell.
5.  När skriptet ber dig namnge appen anger du ett unikt appnamn.


När skriptet har körts bör det visa följande saker i utdata:

-  Ett meddelande som bekräftar att din app nu har en tjänsthuvudrolltilldelning.
-  Klient-ID: n och klienthemlighetsnyckeln som du behöver när du distribuerar diagnostikverktyget.

Nu när du har registrerat din app är det dags att konfigurera logganalysarbetsytan.

## <a name="configure-your-log-analytics-workspace"></a>Konfigurera arbetsytan Log Analytics

För bästa möjliga upplevelse rekommenderar vi att du konfigurerar din Log Analytics-arbetsyta med följande prestandaräknare som gör att du kan härleda satser från användarupplevelsen i en fjärrsession. En lista över rekommenderade räknare med föreslagna tröskelvärden finns i [tröskelvärden för prestandaräknare i Windows](deploy-diagnostics.md#windows-performance-counter-thresholds).

### <a name="create-an-azure-log-analytics-workspace-using-powershell"></a>Skapa en Azure Log Analytics-arbetsyta med PowerShell

Du kan köra ett PowerShell-skript för att skapa en Log Analytics-arbetsyta och konfigurera de rekommenderade Prestandaräknarna för Windows för att övervaka användarupplevelsen och appprestanda.

>[!NOTE]
>Om du redan har en befintlig Log Analytics-arbetsyta som du har gjort utan det PowerShell-skript som du vill använda, går du vidare för att [validera skriptresultaten i Azure-portalen](#validate-the-script-results-in-the-azure-portal).

Så här kör du PowerShell-skriptet:

1.  Öppna PowerShell som administratör.
2.  Gå till [RDS-mallarna GitHub repo](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy/scripts) och kör **CreateLogAnalyticsWorkspaceforDiagnostics.ps1** skriptet i PowerShell.
3. Ange följande värden för parametrarna:

    - För **ResourceGroupName**anger du namnet på resursgruppen.
    - För **LogAnalyticsWorkspaceName**anger du ett unikt namn för log analytics-arbetsytan.
    - För **Plats**anger du den Azure-region som du använder.
    - Ange **Azure-prenumerations-ID**, som du hittar i Azure-portalen under **Prenumerationer**.

4. Ange autentiseringsuppgifterna för en användare med delegerad administratörsåtkomst.
5. Logga in på Azure-portalen med samma användares autentiseringsuppgifter.
6. Skriv ned eller memorera LogAnalyticsWorkspace-ID för senare.
7. Om du konfigurerar Log Analytics-arbetsytan med PowerShell-skriptet bör prestandaräknarna redan konfigureras och du kan hoppa över [skriptresultaten i Azure-portalen](#validate-the-script-results-in-the-azure-portal). Annars fortsätter du till nästa avsnitt.

### <a name="configure-windows-performance-counters-in-your-existing-log-analytics-workspace"></a>Konfigurera Windows-prestandaräknare på den befintliga Log Analytics-arbetsytan

Det här avsnittet är för användare som vill använda en befintlig Azure Log Analytics-arbetsyta som skapats utan PowerShell-skriptet i föregående avsnitt. Om du inte har använt skriptet måste du konfigurera de rekommenderade Prestandaräknarna för Windows manuellt.

Så här konfigurerar du manuellt de rekommenderade prestandaräknarna:

1. Öppna din webbläsare och logga in på [Azure-portalen](https://portal.azure.com/) med ditt administrativa konto.
2. Gå sedan till **Log Analytics-arbetsytor** för att granska de konfigurerade Prestandaräknare för Windows.
3. Välj **Avancerade inställningar**i avsnittet **Inställningar** .
4. Därefter navigerar du till **Prestandaräknare** > för Data**Windows** och lägger till följande räknare:

    -   LogicalDisk(\*\\) %ledigt utrymme
    -   LogicalDisk(C:)\\Genomsnittlig diskkölängd
    -   Minne(\*\\) Tillgängligt Mbytes
    -   Processorinformation(\*\\) Processortid
    -   Fördröjning för användarindata per session(\*)\\Max indatafördröjning

Läs mer om prestandaräknarna på [Prestandadatakällor](/azure/azure-monitor/platform/data-sources-performance-counters)för Windows och Linux i Azure Monitor .

>[!NOTE]
>Ytterligare räknare som du konfigurerar visas inte i själva diagnostikverktyget. För att få det att visas i diagnostikverktyget måste du konfigurera verktygets konfigurationsfil. Instruktioner för hur du gör detta med avancerad administration kommer att finnas tillgängliga i GitHub vid ett senare tillfälle.

## <a name="validate-the-script-results-in-the-azure-portal"></a>Verifiera skriptresultaten i Azure-portalen

Innan du fortsätter att distribuera diagnostikverktyget rekommenderar vi att du kontrollerar att ditt Azure Active Directory-program har API-behörigheter och att arbetsytan Log Analytics har de förkonfigurerade Windows-prestandaräknarna.

### <a name="review-your-app-registration"></a>Granska din appregistrering

Så här kontrollerar du att din appregistrering har API-behörigheter:

1. Öppna en webbläsare och anslut till [Azure-portalen](https://portal.azure.com/) med ditt administrativa konto.
2. Gå till **Azure Active Directory**.
3. Gå till **Appregistreringar** och välj **Alla program**.
4. Leta efter din Azure AD-appregistrering med samma appnamn som du angav i steg 5 i [Skapa en Azure Active Directory-appregistrering](deploy-diagnostics.md#create-an-azure-active-directory-app-registration).

### <a name="review-your-log-analytics-workspace"></a>Granska arbetsytan Log Analytics

Så här kontrollerar du att arbetsytan Log Analytics har de förkonfigurerade Prestandaräknarna för Windows:

1. Gå till **Log Analytics-arbetsytor** i [Azure-portalen](https://portal.azure.com/)och granska de konfigurerade Prestandaräknarna för Windows.
2. Under **Inställningar**väljer du **Avancerade inställningar**.
3. Därefter går du till**Prestandaräknare för** **Data** > Windows .
4. Kontrollera att följande räknare är förkonfigurerade:

   - LogicalDisk(\*\\) %Ledigt utrymme: Visar mängden ledigt utrymme för det totala användbara utrymmet på disken i procent.
   - LogicalDisk(C:)\\Genomsnittlig diskkölängd: Längden på begäran om disköverföring för C-enheten. Värdet bör inte överstiga 2 under mer än en kort tidsperiod.
   - Memory(\*\\) Tillgängligt Mbytes: Det tillgängliga minnet för systemet i megabyte.
   - Processorinformation(\*\\) Processortid: den procentandel av förfluten tid som processorn spenderar för att köra en icke-inaktiv tråd.
   - Fördröjning för användarindata per session(\*)\\Max indatafördröjning

### <a name="connect-to-vms-in-your-log-analytics-workspace"></a>Ansluta till virtuella datorer på arbetsytan Log Analytics

För att kunna visa hälsotillståndet för virtuella datorer måste du aktivera Log Analytics-anslutningen. Följ dessa steg för att ansluta dina virtuella datorer:

1. Öppna en webbläsare och logga in på [Azure-portalen](https://portal.azure.com/) med ditt administrativa konto.
2. Gå till arbetsytan Logganalys.
3. Välj **virtuella datorer**under Datakällor på arbetsytan på den vänstra panelen .
4. Välj namnet på den virtuella dator som du vill ansluta till.
5. Välj **Anslut**.

## <a name="deploy-the-diagnostics-tool"></a>Distribuera diagnostikverktyget

Så här distribuerar du Azure Resource Management-mallen för diagnostikverktyget:

1.  Gå till [sidan GitHub Azure RDS-mallar](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy).
2.  Distribuera mallen till Azure och följ instruktionerna i mallen. Kontrollera att du har följande information tillgänglig:

    -   Klient-Id
    -   Klient-Hemlighet
    -   Log Analytics-arbetsytans ID

3.  När indataparametrarna har angetts godkänner du villkoren och väljer sedan **Köp**.

Utplaceringen tar 2–3 minuter. När du har lyckats distribuera går du till resursgruppen och kontrollerar att webbappen och appserviceplanresurserna finns där.

Därefter måste du ställa in Redirect URI.

### <a name="set-the-redirect-uri"></a>Ange omdirigerings-URI

Så här ställer du in Redirect URI:

1.  Gå till **App Services** och leta reda på programmet som du skapade i [Azure-portalen.](https://portal.azure.com/)
2.  Gå till översiktssidan och kopiera webbadressen du hittar där.
3.  Navigera till **appregistreringar** och välj den app som du vill distribuera.
4.  Välj **Autentisering**under Avsnittet Hantera på den vänstra panelen.
5.  Ange önskad Redirect URI i textrutan **Omdirigera URI** och välj sedan **Spara** i det övre vänstra hörnet på menyn.
6. Välj **Webben** i den nedrullningsbara menyn under Typ.
7. Ange WEBBADRESS:en från sidan för appöversikt och lägg till **/security/signin-callback** i slutet av den. Till exempel: `https://<yourappname>.azurewebsites.net/security/signin-callback`.

   ![Omdirigerings-URI-sidan](media/redirect-uri-page.png)

8. Gå nu till dina Azure-resurser, välj Azure App Services-resursen med namnet du angav i mallen och navigera till url:en som är associerad med den. (Om till exempel appnamnet som du `contosoapp45`använde i mallen <https://contosoapp45.azurewebsites.net>var , så är den associerade webbadressen ).
9. Logga in med lämpligt Azure Active Directory-användarkonto.
10.   Välj **Acceptera**.

## <a name="distribute-the-diagnostics-tool"></a>Distribuera diagnostikverktyget

Innan du gör diagnostikverktyget tillgängligt för användarna kontrollerar du att de har följande behörigheter:

- Användare behöver läsbehörighet för logganalys. Mer information finns i [Komma igång med roller, behörigheter och säkerhet med Azure Monitor](/azure/azure-monitor/platform/roles-permissions-security).
-  Användarna behöver också läsbehörighet för windows virtual desktop-klienten (rds reader-rollen). Mer information finns [i Delegerad åtkomst i Windows Virtual Desktop](delegated-access-virtual-desktop.md).

Du måste också ge användarna följande information:

- Appens webbadress
- Namnen på den enskilda klientorganisationen för klientgruppen som de kan komma åt.

## <a name="use-the-diagnostics-tool"></a>Använda diagnostikverktyget

När du har loggat in på ditt konto med hjälp av den information du har fått från din organisation ska UPN vara redo för den användare som du vill fråga aktiviteter för. En sökning ger dig alla aktiviteter under den angivna aktivitetstypen som inträffade under den senaste veckan.

### <a name="how-to-read-activity-search-results"></a>Så här läser du aktivitetssökresultat

Aktiviteter sorteras efter tidsstämpel, med den senaste aktiviteten först. Om resultatet returnerar ett fel kontrollerar du först om det är ett servicefel. Skapa en supportbiljett med aktivitetsinformationen för att hjälpa oss att felsöka problemet för servicefel. Alla andra feltyper kan vanligtvis lösas av användaren eller administratören. En lista över de vanligaste felscenarierna och hur du löser dem finns i [Identifiera och diagnostisera problem](diagnostics-role-service.md#common-error-scenarios).

>[!NOTE]
>Servicefel kallas "externa fel" i den länkade dokumentationen. Detta ändras när vi uppdaterar PowerShell-referensen.

Anslutningsaktiviteter kan ha mer än ett fel. Du kan expandera aktivitetstypen för att se andra fel som användaren har stött på. Välj namnet på felkoden för att öppna en dialogruta för att se mer information om den.

### <a name="investigate-the-session-host"></a>Undersök sessionsvärden 

Leta reda på och välj den sessionsvärd som du vill ha information om i sökresultaten.

Du kan analysera sessionsvärdens hälsa:

- Baserat på ett fördefinierat tröskelvärde kan du hämta hälsoinformationen för sessionsvärden som loggar analytics-frågor.
- När det inte finns någon aktivitet eller om sessionsvärden inte är ansluten till Log Analytics är informationen inte tillgänglig.

Du kan också interagera med användare på sessionsvärden:

- Du kan antingen logga ut eller skicka ett meddelande till inloggade användare.
- Användaren som du ursprungligen sökte efter är markerad som standard, men du kan också välja ytterligare användare för att skicka meddelanden eller logga ut flera användare samtidigt.

### <a name="windows-performance-counter-thresholds"></a>Tröskelvärden för prestandaräknare i Windows

- LogicalDisk(\*\\) %ledigt utrymme:

    - Visar procentandelen av det totala användbara utrymmet på den logiska disken som är ledig.
    - Tröskelvärde: Mindre än 20 % markeras som felfritt.

- LogicalDisk(C:)\\Genomsnittlig diskkölängd:

    - Representerar lagringssystemsförhållanden.
    - Tröskelvärde: Högre än 5 markeras som felfritt.

- Minne(\*\\) Tillgängliga Mbytes:

    - Det tillgängliga minnet för systemet.
    - Tröskelvärde: Mindre än 500 megabyte markerade som ohälsosamma.

- Processorinformation(\*\\) Processortid:

    - Tröskelvärde: Högre än 80% markeras som ohälsosamt.

- [Fördröjning för\*användarindata\\per session( ) Max indatafördröjning:](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/)

    - Tröskelvärde: Högre än 2000 ms markeras som felfritt.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du övervakar aktivitetsloggar vid [Användning av diagnostik med Log Analytics](diagnostics-log-analytics.md).
- Läs om vanliga felscenarier och hur du åtgärdar dem på [Identifiera och diagnostisera problem](diagnostics-role-service.md).
