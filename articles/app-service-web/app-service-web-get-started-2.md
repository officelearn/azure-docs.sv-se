<properties 
    pageTitle="Lägg in funktioner i din första webbapp" 
    description="Lägg in funktioner i din första webbapp på några minuter" 
    services="app-service\web"
    documentationCenter=""
    authors="cephalin" 
    manager="wpickett" 
    editor="" 
/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="hero-article"
    ms.date="05/12/2016" 
    ms.author="cephalin"
/>

# Lägg in funktioner i din första webbapp

I kursen om att [distribuera en webbapp till Azure på fem minuter](app-service-web-get-started.md) distribuerade du en exempelwebbapp till [Azure Apptjänst](../app-service/app-service-value-prop-what-is.md). I den här artikeln får du lära dig hur du lägger in några bra funktioner i din distribuerade webbapp. På några minuter gör du följande:

- ställer in användarautentisering
- ställer in automatisk skalning av appen
- ställer in att du vill ha aviseringar om appen

Oavsett vilket exempel du distribuerade i föregående artikel kan du följa med i den här kursen.

De tre funktionerna i den här kursen är endast några exempel på de många användbara funktioner som du får tillgång till när du lägger din webbapp i Apptjänst. Många av funktionerna kan fås med den **kostnadsfria nivån** (som din första webbapp körs i), och du kan använda dina utvärderingskrediter för att prova funktioner som det krävs högre prisnivåer för. Du kan vara säker på att din webbapp fortsätter att vara på den **kostnadsfria nivån** så länge du inte uttryckligen ändrar till en högre prisnivå.

>[AZURE.NOTE] Webbappen som du skapade med Azure CLI körs i den **kostnadsfria nivån** där det endast får vara en enda delad VM-instans med resurskvoter. Mer information om vad du får i den **kostnadsfria nivån** finns i avsnittet om [gränser för Apptjänst](../azure-subscription-service-limits.md#app-service-limits).

## Autentisera användarna

Nu ska vi visa hur enkelt det är att lägga in autentisering i appen (i avsnittet om[autentisering/auktorisering i Apptjänst](https://azure.microsoft.com/blog/announcing-app-service-authentication-authorization/) finns mer att läsa).

1. På portalbladet för appen som du just öppnade klickar du på **Inställningar** > **Autentisering/auktorisering**.  
    ![Autentisera – inställningsbladet](./media/app-service-web-get-started/aad-login-settings.png)
    
2. Aktivera autentisering genom att klicka på **På**.  
    
4. Gå till **Autentiseringsprovidrar** och klicka på **Azure Active Directory**.  
    ![Autentisera – välj Azure AD](./media/app-service-web-get-started/aad-login-config.png)

5. I bladet **Inställningar för Azure Active Directory** klickar du på **Express** och sedan på **OK**. Med standardinställningarna skapas ett nytt Azure AD-program i din standardkatalog.  
 ![Autentisera – expresskonfigurering](./media/app-service-web-get-started/aad-login-express.png)

6. Klicka på **Spara**.  
    ![Autentisera – spara konfiguration](./media/app-service-web-get-started/aad-login-save.png)

    När ändringen är genomförd du får ett meddelande om det och informationsklockan blir grön.

7. Gå tillbaka till portalbladet för din app och klicka på länken under **URL** (eller **Bläddra** i menyraden). Länken är en HTTP-adress.  
    ![Autentisera – leta fram till URL](./media/app-service-web-get-started/aad-login-browse-click.png)  
    Men när appen öppnas i en ny flik omdirigeras URL-rutan flera gånger och adressen till appen blir till slut en HTTPS-adress. Det du ser nu är att du redan är inloggad i din Azureprenumeration. Du är därmed automatiskt autentiserad i appen.  
    ![Autentisera – logga in](./media/app-service-web-get-started/aad-login-browse-http-postclick.png)  
    Så om du nu öppnar en oautentiserad session i en annan webbläsare får du upp inloggningsrutan när du går till samma URL.  
    <!-- ![Authenticate - login page](./media/app-service-web-get-started/aad-login-browse.png)  -->
    Om du aldrig har gjort något med Azure Active Directory tidigare finns det kanske inga Azure AD-användare i din standardkatalog. Då är antagligen det enda konto som finns där det Microsoftkonto som Azureprenumerationen är registrerad på. Det var därför som du blev automatiskt inloggad i appen i samma webbläsare tidigare. Du kan logga in med samma Microsoftkontouppgifter på den här inloggningssidan också.

Vad bra! Nu autentiseras all trafik till appen.

Kanske såg du på bladet **Autentisering/auktorisering** att du kan göra mycket mer, till exempel:

- aktivera inloggning via sociala medier
- aktivera flera olika inloggningsalternativ
- ändra vad som händer som standard när besökare kommer till appen första gången

I Apptjänst får du en nyckelfärdig lösning som passar för de flesta vanliga autentiseringsbehov så att du inte behöver skapa autentiseringslogiken själv. Mer information om autentisering/auktorisering i Apptjänst finns [här](https://azure.microsoft.com/blog/announcing-app-service-authentication-authorization/).

## Automatisk skalning av appen utifrån efterfrågan

Nu ska vi ställa in automatisk skalning av appen så att den anpassar sig efter efterfrågan från användarna (du kan läsa mer om skalningsprisnivåer i Azure Apptjänst [här](app-service-scale) och om att skala antalet instanser manuellt eller automatiskt [här](../azure-portal/insights-how-to-scale.md)). 

Webbappar kan i princip skalas på två sätt:

- [Skala upp](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): Du får mer processorkapacitet, minne och diskutrymme och extra funktioner såsom dedikerade virtuella datorer, anpassade domäner och certifikat, mellanlagringsplatser, autoskalning med mera. Du skalar upp genom att ändra prisnivån på apptjänstplanen som appen i fråga tillhör.
- [Skala ut](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): Antalet VM-instanser som appen körs på ökas.
Du kan skala ut till så många som 50 instanser, beroende på vilken prisnivå du har.

Nu kommer vi till saken och ställer in autoskalningen.

1. Först aktiverar vi autoskalning genom att skala upp. Gå till portalbladet för appen i fråga och klicka på **Inställningar** > **Skala upp(apptjänstplan)**.  
    ![Skala upp – inställningsbladet](./media/app-service-web-get-started/scale-up-settings.png)

2. Bläddra ned och välj prisnivån **S1 Standard**, som är den lägsta nivån som autoskalning kan göras på (inringad i skärmbilden), och klicka sedan på **Välj**.  
    ![Skala upp – välj nivå](./media/app-service-web-get-started/scale-up-select.png)

    Så där, nu har du skalat upp.
    
    >[AZURE.IMPORTANT] Med den här nivån förbrukas dina kostnadsfria utvärderingskrediter. Om du har ett konto där du betalar utifrån hur mycket du använder innebär det att kontot debiteras.
    
3. Då ska vi ställa in autoskalning. Gå till portalbladet för appen i fråga och klicka på **Inställningar** > **Skala ut (apptjänstplan)**.  
    ![Skala ut – inställningsbladet](./media/app-service-web-get-started/scale-out-settings.png)

4. Ändra **Skala enligt** till **CPU-procent**. Skjutreglagen under listrutan ändras enligt inställningen. Ange sedan antal **Instanser** mellan **1** och **2** och ett **Målområde** mellan **40** och **80**. Du kan antingen skriva i rutorna eller dra i skjutreglagen.  
 ![Skala ut  – konfigurera autoskalning](./media/app-service-web-get-started/scale-out-configure.png)
    
    Med den här inställningen skalas appen ut automatiskt när processoranvändningen går över 80 procent och skalas in när processoranvändningen går under 40 procent. 
    
5. Klicka på **Spara** i menyraden.

Vad bra! Nu autoskalas appen.

Kanske såg du på bladet **Skalningsinställningar** att det finns mycket mer du kan göra, till exempel:

- skala till ett specifikt antal instanser manuellt
- skala efter andra prestandavärden, till exempel minnesprocent eller diskkö
- anpassa skalningsuppförande när en prestandaregel löser ut
- autoskala enligt schema
- ställa in autoskalningsuppförande för framtida händelser

Mer information om att skala upp appar finns i avsnittet om [skalningsprisnivåer i Azure Apptjänst ](../app-service/app-service-scale.md). Mer information om att skala ut finns i avsnittet om att [skala antalet instanser manuellt eller automatiskt ](../azure-portal/insights-how-to-scale.md).

## Varningar om appen

Nu autoskalas appen. Men vad händer då om det högsta inställda antalet instanser (2) uppnås och processorn går över inställd användningsgrad (80 procent)? Du kan ställa in att du vill ha en varning (du kan läsa mer om varningsaviseringar [här](../azure-portal/insights-receive-alert-notifications.md)) om situationen så att du till exempel kan skala upp eller ut appen ytterligare. Vi ställer snabbt in en varning för en sådan händelse.

1. Gå till portalbladet för appen i fråga och klicka på **Verktyg** > **Varningar**.  
    ![Varningar – inställningsbladet](./media/app-service-web-get-started/alert-settings.png)

2. Klicka på **Lägg till avisering**. Gå sedan till rutan **Resurs** och välj den resurs som slutar med **(serverfarms)**. Det är din apptjänstplan.  
    ![Varningar – lägg till varning för apptjänstplan](./media/app-service-web-get-started/alert-add.png)

3. I rutan **Namn** anger du `CPU Maxed`, i rutan **Mått** anger du **CPU-procent** och i rutan **Tröskelvärde** `90`. Markera sedan alternativet **E-postägare, deltagare och läsare** och klicka sedan på **OK**.   
 ![Varningar – ställ in varning](./media/app-service-web-get-started/alert-configure.png)
    
    När varningen har skapats visas den på bladet **Varningar**.  
    ![Varningar – färdig varning visas](./media/app-service-web-get-started/alert-done.png)

Vad bra, nu får du varningar. 

Med den här varningen kontrolleras processoranvändningen med några minuters mellanrum. Om användningen går över 90 procent får du, liksom de som har rätt behörighet, en varning via e-post. Om du vill se vilka som har behörighet att få varningar går du tillbaka till portalbladet för appen och klickar på **åtkomstknappen**.  
![Se vilka som får varningar](./media/app-service-web-get-started/alert-rbac.png)

Det bör stå att **prenumerationsadministratörer** redan är **ägare** till appen. Där ingår du om du är kontoadministratör för Azure-prenumerationen (det vill säga utvärderingsprenumerationen). Mer information om rollbaserad åtkomstkontroll i Azure finns [här](../active-directory/role-based-access-control-configure.md).

> [AZURE.NOTE] Varningsregler är en funktion i Azure. Mer information om varningsaviseringar finns [här](../azure-portal/insights-receive-alert-notifications.md). 

## Nästa steg

När du var på väg att ställa in varningen märkte du kanske att det fanns många andra verktyg på bladet **Verktyg**. Här kan du felsöka problem, övervaka prestanda, testa säkerhetsrisker, hantera resurser, interagera med VM-konsolen och lägga till användbara tillägg. Klicka gärna på de olika verktygen och se vilka bra verktyg som du har till hands. 

Det finns så mycket mer du kan göra med den distribuerade appen. Här är bara några exempel:

- [Köpa och konfigurera ett eget domännamn](custom-dns-web-site-buydomains-web-app.md) – Du kan köpa en snygg domän för din webbapp att använda istället för domänen *.azurewebsites.net. Eller så kan du använda en domän som du redan har.
- [Skapa mellanlagringsmiljöer](web-sites-staged-publishing.md) – Du kan distribuera appar till en mellanlagrings-URL innan du lägger ut dem i produktion. På så sätt kan du uppdatera livewebbappar utan att behöva oroa dig. Du kan skapa en utförlig DevOps-lösning med flera olika distributionsplatser. 
- [Ställa in kontinuerlig distribution](web-sites-publish-source-control.md) – Du kan integrera appdistribution i ditt källkontrollsystem. Distribution till Azure sker vid varje incheckning.
- [Få tillgång till lokala resurser](web-sites-hybrid-connection-get-started.md) – Du kan nå en befintlig lokal databas eller lokalt CRM-system.
- [Säkerhetskopiera appar](web-sites-backup.md) – Du kan ställa in säkerhetskopiering och återställning av webbappar. På så sätt är du förberedd för oväntade fel och kan återställa vid behov.
- [Aktivera diagnostikloggar](web-sites-enable-diagnostic-log.md) – Du kan läsa IIS-loggar från Azure eller programspår. Du kan läsa dem i ett flöde, ladda ned dem eller portera dem till [Application Insights](../application-insights/app-insights-overview.md) för nyckelfärdig analys.
- [Söka igenom appen efter säkerhetsrisker](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) -
 – Du kan söka igenom dina webbappar och skydda dem mot aktuella hot genom en tjänst från [Tinfoil Security](https://www.tinfoilsecurity.com/).
- [Köra bakgrundsjobb](../azure-functions/functions-overview.md) – Du kan köra jobb för databehandling, rapportering med mera.
- [Lära dig hur Apptjänst fungerar](../app-service/app-service-how-works-readme.md) 


<!--HONumber=Jun16_HO2-->


