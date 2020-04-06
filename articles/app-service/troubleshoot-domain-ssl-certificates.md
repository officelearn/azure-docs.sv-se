---
title: Felsöka domän- och TLS/SSL-certifikat
description: Hitta lösningar på vanliga problem som kan uppstå när du konfigurerar en domän eller TLS/SSL-certifikat i Azure App Service.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.topic: article
ms.date: 03/01/2019
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: d61b95c7136a4cbce11789a58d27cc1a164ae374
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668027"
---
# <a name="troubleshoot-domain-and-tlsssl-certificate-problems-in-azure-app-service"></a>Felsöka problem med domän- och TLS/SSL-certifikat i Azure App Service

I den här artikeln visas vanliga problem som kan uppstå när du konfigurerar en domän eller ett TLS/SSL-certifikat för dina webbappar i Azure App Service. Den beskriver också möjliga orsaker och lösningar på dessa problem.

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experterna på [MSDN- och Stack Overflow-forumen](https://azure.microsoft.com/support/forums/). Du kan också arkivera en Azure-supportincident. Gå till [Azure Support-webbplatsen](https://azure.microsoft.com/support/options/) och välj **Hämta support**.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="certificate-problems"></a>Problem med certifikat

### <a name="you-cant-add-a-tlsssl-certificate-binding-to-an-app"></a>Du kan inte lägga till en TLS/SSL-certifikatbindning i en app 

#### <a name="symptom"></a>Symptom

NÃ¤tt sÃ¤tt visas fÃ¶ndesÃ¶ttningsbindning visas fÃ¶nde:

"Det gick inte att lägga till SSL-bindning. Det går inte att ange certifikat för befintlig VIP eftersom en annan VIP redan använder certifikatet."

#### <a name="cause"></a>Orsak

Det här problemet kan uppstå om du har flera IP-baserade SSL-bindningar för samma IP-adress över flera appar. App A har till exempel ett IP-baserat SSL med ett gammalt certifikat. App B har en IP-baserad SSL med ett nytt certifikat för samma IP-adress. När du uppdaterar appen TLS-bindning med det nya certifikatet misslyckas den med det här felet eftersom samma IP-adress används för en annan app. 

#### <a name="solution"></a>Lösning 

Lös problemet genom att använda någon av följande metoder:

- Ta bort IP-baserad SSL-bindning på appen som använder det gamla certifikatet. 
- Skapa en ny IP-baserad SSL-bindning som använder det nya certifikatet.

### <a name="you-cant-delete-a-certificate"></a>Du kan inte ta bort ett certifikat 

#### <a name="symptom"></a>Symptom

NÃ¤r du fÃ¶rsÃ¶k fÃ¶rsÃ¶k att ta bort ett certifikat visas fÃ¶1sã¥re:

"Det gick inte att ta bort certifikatet eftersom det för närvarande används i en TLS/SSL-bindning. TLS-bindningen måste tas bort innan du kan ta bort certifikatet."

#### <a name="cause"></a>Orsak

Det här problemet kan uppstå om en annan app använder certifikatet.

#### <a name="solution"></a>Lösning

Ta bort TLS-bindningen för certifikatet från apparna. Försök sedan ta bort certifikatet. Om du fortfarande inte kan ta bort certifikatet rensar du webbläsarens cacheminne och öppnar Azure-portalen igen i ett nytt webbläsarfönster. Försök sedan ta bort certifikatet.

### <a name="you-cant-purchase-an-app-service-certificate"></a>Du kan inte köpa ett App Service-certifikat 

#### <a name="symptom"></a>Symptom
Du kan inte köpa ett [Azure App Service-certifikat](./configure-ssl-certificate.md#import-an-app-service-certificate) från Azure-portalen.

#### <a name="cause-and-solution"></a>Orsak och lösning
Det här problemet kan uppstå av något av följande skäl:

- App serviceplanen är kostnadsfri eller delad. Dessa prisnivåer stöder inte TLS. 

    **Lösning**: Uppgradera apptjänstplanen för appen till Standard.

- Prenumerationen har inget giltigt kreditkort.

    **Lösning**: Lägg till ett giltigt kreditkort i din prenumeration. 

- Prenumerationserbjudandet stöder inte att köpa ett App Service-certifikat som Microsoft Student.  

    **Lösning**: Uppgradera din prenumeration. 

- Prenumerationen nådde gränsen för köp som är tillåtna på en prenumeration.

    **Lösning**: Apptjänstcertifikat har en gräns på 10 certifikatköp för prenumerationstyperna Betala per användning och EA. För andra prenumerationstyper är gränsen 3. Om du vill öka gränsen kontaktar du [Azure-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Apptjänstcertifikatet har markerats som bedrägeri. Du fick följande felmeddelande: "Ditt certifikat har flaggats för eventuellt bedrägeri. Begäran är för närvarande under granskning. Om certifikatet inte blir användbart inom 24 timmar kontaktar du Azure Support."

    **Lösning**: Om certifikatet är markerat som bedrägeri och inte har lösts efter 24 timmar gör du så här:

    1. Logga in på [Azure-portalen](https://portal.azure.com).
    2. Gå till **App Service-certifikat**och välj certifikatet.
    3. Välj **certifikatkonfiguration** > **steg 2: Verifiera** > **domänverifiering**. Det här steget skickar ett e-postmeddelande till Azure-certifikatleverantören för att lösa problemet.

## <a name="custom-domain-problems"></a>Anpassade domänproblem

### <a name="a-custom-domain-returns-a-404-error"></a>En anpassad domän returnerar ett 404-fel 

#### <a name="symptom"></a>Symptom

När du bläddrar till webbplatsen med det anpassade domännamnet visas följande felmeddelande:

"Det gick inte att hitta en webbapp för 404 webb.

#### <a name="cause-and-solution"></a>Orsak och lösning

**Orsak 1** 

Den anpassade domän som du har konfigurerat saknar en CNAME- eller A-post. 

**Lösning för orsak 1**

- Om du har lagt till en A-post kontrollerar du att även en TXT-post läggs till. Mer information finns i [Skapa A-post](./app-service-web-tutorial-custom-domain.md#create-the-a-record).
- Om du inte behöver använda rotdomänen för din app rekommenderar vi att du använder en CNAME-post i stället för en A-post.
- Använd inte både en CNAME-post och en A-post för samma domän. Det här problemet kan orsaka en konflikt och förhindra att domänen löses. 

**Orsak 2** 

Webbläsaren kan fortfarande cachela den gamla IP-adressen för din domän. 

**Lösning för orsak 2**

Rensa webbläsaren. För Windows-enheter kan du `ipconfig /flushdns`köra kommandot . Använd [WhatsmyDNS.net](https://www.whatsmydns.net/) för att verifiera att domänen pekar på appens IP-adress. 

### <a name="you-cant-add-a-subdomain"></a>Du kan inte lägga till en underdomän 

#### <a name="symptom"></a>Symptom

Du kan inte lägga till ett nytt värdnamn i en app för att tilldela en underdomän.

#### <a name="solution"></a>Lösning

- Kontrollera med prenumerationsadministratören att du har behörighet att lägga till ett värdnamn i appen.
- Om du behöver fler underdomäner rekommenderar vi att du ändrar domänhostingen till DNS (Azure Domain Name Service). Genom att använda Azure DNS kan du lägga till 500 värdnamn i din app. Mer information finns i [Lägga till en underdomän](https://blogs.msdn.microsoft.com/waws/2014/10/01/mapping-a-custom-subdomain-to-an-azure-website/).

### <a name="dns-cant-be-resolved"></a>DNS kan inte lösas

#### <a name="symptom"></a>Symptom

Följande felmeddelande har tagits emot:

"DNS-posten kunde inte lokaliseras."

#### <a name="cause"></a>Orsak
Det här problemet uppstår av något av följande skäl:

- Tiden att leva (TTL) har inte gått ut. Kontrollera DNS-konfigurationen för din domän för att fastställa TTL-värdet och vänta sedan tills perioden har gått ut.
- DNS-konfigurationen är felaktig.

#### <a name="solution"></a>Lösning
- Vänta i 48 timmar på att problemet ska lösa sig själv.
- Om du kan ändra TTL-inställningen i DNS-konfigurationen ändrar du värdet till 5 minuter för att se om det löser problemet.
- Använd [WhatsmyDNS.net](https://www.whatsmydns.net/) för att verifiera att domänen pekar på appens IP-adress. Om den inte gör det konfigurerar du A-posten till rätt IP-adress för appen.

### <a name="you-need-to-restore-a-deleted-domain"></a>Du måste återställa en borttagen domän 

#### <a name="symptom"></a>Symptom
Din domän visas inte längre i Azure-portalen.

#### <a name="cause"></a>Orsak 
Ägaren till prenumerationen kan ha tagit bort domänen av misstag.

#### <a name="solution"></a>Lösning
Om domänen togs bort för mindre än sju dagar sedan har domänen ännu inte startat borttagningsprocessen. I det här fallet kan du köpa samma domän igen på Azure-portalen under samma prenumeration. (Var noga med att skriva det exakta domännamnet i sökrutan.) Du debiteras inte igen för den här domänen. Om domänen togs bort för mer än sju dagar sedan kontaktar du [Azure-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) för att få hjälp med att återställa domänen.

## <a name="domain-problems"></a>Domänproblem

### <a name="you-purchased-a-tlsssl-certificate-for-the-wrong-domain"></a>Du har köpt ett TLS/SSL-certifikat för fel domän

#### <a name="symptom"></a>Symptom

Du har köpt ett App Service-certifikat för fel domän. Du kan inte uppdatera certifikatet för att använda rätt domän.

#### <a name="solution"></a>Lösning

Ta bort certifikatet och köp sedan ett nytt certifikat.

Om det aktuella certifikatet som använder fel domän är i tillståndet "Utfärdat" debiteras du också för det certifikatet. AppTjänstcertifikat återbetalas inte, men du kan kontakta [Azure-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) för att se om det finns andra alternativ. 

### <a name="an-app-service-certificate-was-renewed-but-the-app-shows-the-old-certificate"></a>Ett App Service-certifikat har förnyats, men appen visar det gamla certifikatet 

#### <a name="symptom"></a>Symptom

Apptjänstcertifikatet förnyades, men appen som använder App Service-certifikatet använder fortfarande det gamla certifikatet. Du fick också en varning om att HTTPS-protokollet krävs.

#### <a name="cause"></a>Orsak 
App Service synkroniserar automatiskt ditt certifikat inom 48 timmar. När du roterar eller uppdaterar ett certifikat hämtar programmet ibland fortfarande det gamla certifikatet och inte det nyligen uppdaterade certifikatet. Anledningen är att jobbet för att synkronisera certifikatresursen inte har körts ännu. Klicka på Synkronisera. Synkroniseringsåtgärden uppdaterar automatiskt värdnamnsbindningarna för certifikatet i App Service utan att orsaka några driftstopp för dina appar.
 
#### <a name="solution"></a>Lösning

Du kan tvinga fram en synkronisering av certifikatet:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **App Service-certifikat**och välj sedan certifikatet.
2. Välj **Nycklar och synkronisera**och välj sedan **Synkronisera**. Synkroniseringen tar lite tid att slutföra. 
3. När synkroniseringen är klar visas följande meddelande: "Framgångsrikt uppdaterat alla resurser med det senaste certifikatet."

### <a name="domain-verification-is-not-working"></a>Domänverifieringen fungerar inte 

#### <a name="symptom"></a>Symptom 
Apptjänstcertifikatet kräver domänverifiering innan certifikatet är klart att användas. När du väljer **Verifiera**misslyckas processen.

#### <a name="solution"></a>Lösning
Verifiera din domän manuellt genom att lägga till en TXT-post:
 
1.  Gå till DNS-providern (Domain Name Service) som är värd för ditt domännamn.
2.  Lägg till en TXT-post för din domän som använder värdet för domäntoken som visas i Azure-portalen. 

Vänta några minuter innan DNS-spridning körs och välj sedan knappen **Uppdatera** för att utlösa verifieringen. 

Alternativt kan du använda HTML-webbsidan för att manuellt verifiera domänen. Med den här metoden kan certifikatutfärdaren bekräfta domänägarskapet för den domän som certifikatet har utfärdats för.

1.  Skapa en HTML-fil med namnet {domain verification token}.html. Innehållet i den här filen ska vara värdet av domänverifieringstoken.
3.  Ladda upp den här filen i roten på webbservern som är värd för din domän.
4.  Välj **Uppdatera** om du vill kontrollera certifikatstatusen. Det kan ta några minuter innan verifieringen är klar.

Om du till exempel köper ett standardcertifikat för azure.com med domänverifieringstoken https://azure.com/1234abcd.html 1234abcd, ska en webbbegäran returnera 1234abcd. 

> [!IMPORTANT]
> En certifikatorder har bara 15 dagar på sig att slutföra domänverifieringen. Efter 15 dagar nekar certifikatutfärdaren certifikatet och du debiteras inte för certifikatet. I det här fallet tar du bort det här certifikatet och försöker igen.
>
> 

### <a name="you-cant-purchase-a-domain"></a>Du kan inte köpa en domän

#### <a name="symptom"></a>Symptom
Du kan inte köpa en App Service-domän i Azure-portalen.

#### <a name="cause-and-solution"></a>Orsak och lösning

Det här problemet uppstår av något av följande skäl:

- Det finns inget kreditkort på Azure-prenumerationen, eller så är kreditkortet ogiltigt.

    **Lösning**: Lägg till ett giltigt kreditkort i din prenumeration.

- Du är inte prenumerationsägare, så du har inte behörighet att köpa en domän.

    **Lösning**: [Tilldela ägarrollen](../role-based-access-control/role-assignments-portal.md) till ditt konto. Eller kontakta prenumerationsadministratören för att få behörighet att köpa en domän.
- Du har nått gränsen för att köpa domäner i din prenumeration. Den nuvarande gränsen är 20.

    **Lösning:** Om du vill begära en ökning till gränsen kontaktar du [Azure-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Din Azure-prenumerationstyp stöder inte köpet av en App Service-domän.

    **Lösning:** Uppgradera din Azure-prenumeration till en annan prenumerationstyp, till exempel en prenumeration på användningsbaserad betalning.

### <a name="you-cant-add-a-host-name-to-an-app"></a>Du kan inte lägga till ett värdnamn i en app 

#### <a name="symptom"></a>Symptom

När du lägger till ett värdnamn misslyckas processen med att validera och verifiera domänen.

#### <a name="cause"></a>Orsak 

Det här problemet uppstår av något av följande skäl:

- Du har inte behörighet att lägga till ett värdnamn.

    **Lösning**: Be prenumerationsadministratören att ge dig behörighet att lägga till ett värdnamn.
- Det gick inte att verifiera domänägarskapet.

    **Lösning**: Kontrollera att CNAME- eller A-posten är korrekt konfigurerad. Om du vill mappa en anpassad domän till en app skapar du antingen en CNAME-post eller en A-post. Om du vill använda en rotdomän måste du använda A- och TXT-poster:

    |Posttyp|Värd|Peka på|
    |------|------|-----|
    |A|@|IP-adress för en app|
    |TXT|@|`<app-name>.azurewebsites.net`|
    |CNAME|www|`<app-name>.azurewebsites.net`|

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

**Måste jag konfigurera min anpassade domän för min webbplats när jag köper den?**

När du köper en domän från Azure-portalen konfigureras App Service-programmet automatiskt för att använda den anpassade domänen. Du behöver inte vidta några ytterligare åtgärder. Mer information finns i [Självhjälp för Azure App Service: Lägg till ett anpassat domännamn](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name) på Channel9.

**Kan jag använda en domän som köpts i Azure-portalen för att peka på en Virtuell Azure i stället?**

Ja, du kan peka domänen på en virtuell dator. Mer information finns i [Använda Azure DNS för att skapa inställningar för anpassad domän för en Azure-tjänst](../dns/dns-custom-domain.md).

**Är min domän värd GoDaddy eller Azure DNS?**

App Service Domains använder GoDaddy för domänregistrering och Azure DNS för att vara värd för domänerna. 

**Jag har automatiskt förnya aktiverat men ändå fått en förnyelse meddelande för min domän via e-post. Vad ska jag göra?**

Om du har aktiverat automatisk förnyelse behöver du inte vidta några åtgärder. E-postmeddelandet med e-post tillhandahålls för att informera dig om att domänen är nära att löpa ut och för att förnya manuellt om automatisk förnyelse inte är aktiverad.

**Debiteras jag för Azure DNS som är värd för min domän?**

Den ursprungliga kostnaden för domänköp gäller endast domänregistrering. Utöver registreringskostnaden medför du avgifter för Azure DNS baserat på din användning. Mer information finns i [Azure DNS-priser](https://azure.microsoft.com/pricing/details/dns/) för mer information.

**Jag köpte min domän tidigare från Azure-portalen och vill flytta från GoDaddy-värd till Azure DNS-värd. Hur kan jag göra det här?**

Det är inte obligatoriskt att migrera till Azure DNS-värd. Om du vill migrera till Azure DNS innehåller domänhanteringsupplevelsen i Azure-portalen om steg som krävs för att flytta till Azure DNS. Om domänen köptes via App Service är migrering från GoDaddy-värd till Azure DNS relativt sömlös procedur.

**Jag skulle vilja köpa min domän från App Service Domain men kan jag vara värd för min domän på GoDaddy i stället för Azure DNS?**

Från och med den 24 juli 2017 finns App Service-domäner som köpts i portalen på Azure DNS. Om du föredrar att använda en annan webbhotell, måste du gå till deras hemsida för att få en domän hosting lösning.

**Måste jag betala för sekretessskydd för min domän?**

När du köper en domän via Azure-portalen kan du välja att lägga till sekretess utan extra kostnad. Detta är en av fördelarna med att köpa din domän via Azure App Service.

**Kan jag få tillbaka mina pengar om jag inte längre vill ha min domän?**

När du köper en domän debiteras du inte under en period av fem dagar, under vilken tid du kan bestämma att du inte vill ha domänen. Om du bestämmer dig för att du inte vill ha domänen inom den femdagarsperioden debiteras du inte. (.uk domäner är ett undantag från detta. Om du köper en .uk-domän debiteras du omedelbart och du kan inte återbetalas.)

**Kan jag använda domänen i en annan Azure App Service-app i min prenumeration?**

Ja. När du öppnar bladet Anpassade domäner och TLS i Azure-portalen visas de domäner som du har köpt. Du kan konfigurera appen så att den använder någon av dessa domäner.

**Kan jag överföra en domän från en prenumeration till en annan prenumeration?**

Du kan flytta en domän till en annan prenumerations-/resursgrupp med hjälp av [Cmdleten Flytta-AzResource](https://docs.microsoft.com/powershell/module/az.Resources/Move-azResource) PowerShell.

**Hur kan jag hantera min anpassade domän om jag för närvarande inte har en Azure App Service-app?**

Du kan hantera din domän även om du inte har en App Service Web App. Domän kan användas för Azure-tjänster som virtuell dator, lagring etc. Om du tänker använda domänen för App Service Web Apps måste du inkludera en webbapp som inte finns i abonnemanget för kostnadsfri apptjänst för att binda domänen till din webbapp.

**Kan jag flytta en webbapp med en anpassad domän till en annan prenumeration eller från App Service Environment v1 till V2?**

Ja, du kan flytta webbappen mellan prenumerationer. Följ vägledningen i [Hur du flyttar resurser i Azure](../azure-resource-manager/management/move-resource-group-and-subscription.md). Det finns några begränsningar när du flyttar webbappen. Mer information finns i [Begränsningar för att flytta App Service-resurser](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md).

När du har flyttat webbappen bör värdnamnsbindningarna för domänerna i inställningen för anpassade domäner förbli desamma. Inga ytterligare steg krävs för att konfigurera värdnamnsbindningar.
