---
title: Felsöka domän och SSL - certifikat i Azure App Service | Microsoft Docs
description: Felsöka domän och SSL-certifikat problem i Azure App Service
services: app-service\web
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/01/2019
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: d007f688483366f2f714a78b5bf9b56a67c55490
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2019
ms.locfileid: "57730111"
---
# <a name="troubleshoot-domain-and-ssl-certificate-problems-in-azure-app-service"></a>Felsöka domän och SSL-certifikat problem i Azure App Service

Den här artikeln innehåller vanliga problem som kan uppstå när du konfigurerar en domän eller SSL-certifikat för web apps i Azure App Service. Här beskrivs också möjliga orsaker och lösningar för dessa problem.

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experter på [MSDN och Stack Overflow-forum](https://azure.microsoft.com/support/forums/). Alternativt kan du arkivera en Azure-support-incident. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och välj **få Support**.

## <a name="certificate-problems"></a>Problem med säkerhetscertifikat

### <a name="you-cant-add-an-ssl-certificate-binding-to-an-app"></a>Du kan inte lägga till en SSL-certifikat-bindning till en app 

#### <a name="symptom"></a>Symtom

När du lägger till en SSL-bindning, visas följande felmeddelande visas:

”Det gick inte att lägga till SSL-bindning. Det går inte att ange certifikatet för befintliga VIP eftersom en annan VIP redan använder certifikatet ”.

#### <a name="cause"></a>Orsak

Det här problemet kan inträffa om du har flera IP-baserad SSL-bindningar för samma IP-adress över flera appar. Till exempel har app A en IP-baserad SSL med ett gammalt certifikat. App B har en IP-baserad SSL med ett nytt certifikat för samma IP-adress. När du uppdaterar appen SSL-bindning med det nya certifikatet, misslyckas med felet eftersom samma IP-adress som används för en annan app. 

#### <a name="solution"></a>Lösning 

Använd någon av följande metoder för att åtgärda problemet:

- Ta bort IP-baserad SSL-bindning på den app som använder det äldre certifikatet. 
- Skapa en ny IP-baserad SSL-bindning som använder det nya certifikatet.

### <a name="you-cant-delete-a-certificate"></a>Du kan inte ta bort ett certifikat 

#### <a name="symptom"></a>Symtom

När du försöker ta bort ett certifikat, visas följande felmeddelande visas:

”Det gick inte att ta bort certifikatet eftersom den används för närvarande i en SSL-bindning. SSL-bindning måste tas bort innan du kan ta bort certifikatet ”.

#### <a name="cause"></a>Orsak

Det här problemet kan inträffa om en annan app använder certifikatet.

#### <a name="solution"></a>Lösning

Ta bort SSL-bindning för det certifikatet från apparna. Försök att ta bort certifikatet. Om du fortfarande inte kan ta bort certifikatet Rensa webbläsarcachen för internet och öppna Azure-portalen i ett nytt webbläsarfönster. Försök att ta bort certifikatet.

### <a name="you-cant-purchase-an-app-service-certificate"></a>Du kan inte köpa ett certifikat för App Service 

#### <a name="symptom"></a>Symtom
Du kan inte köpa ett [Azure App Service certificate](./web-sites-purchase-ssl-web-site.md) från Azure-portalen.

#### <a name="cause-and-solution"></a>Orsak och lösning
Det här problemet kan inträffa för någon av följande orsaker:

- App Service-planen är kostnadsfri eller delad. De här prisnivåerna stöder inte SSL. 

    **Lösningen**: Uppgradera App Service-plan för appen till Standard.

- Prenumerationen har inte ett giltigt kreditkort.

    **Lösningen**: Lägga till ett giltigt kreditkort i prenumerationen. 

- Prenumerationserbjudande stöder inte köpa ett App Service-certifikat, till exempel Microsoft Student.  

    **Lösningen**: Uppgradera din prenumeration. 

- Prenumerationen nått gränsen för köp som tillåts för en prenumeration.

    **Lösningen**: App Service-certifikat har en gräns på 10 inköp av certifikat för prenumerationstyper betala per användning och EA. För andra prenumerationstyper av är gränsen 3. Om du vill öka gränsen kontaktar [Azure-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- App Service certificate har markerats som bedrägeri. Du har fått följande felmeddelande visas: ”Ditt certifikat har flaggats för potentiellt bedrägeri. Begäran är för närvarande under granskning. Om certifikatet inte blir kan användas inom 24 timmar kontaktar du supporten för Azure ”.

    **Lösningen**: Om certifikatet har markerats som bedrägerier och inte är löst efter 24 timmar, följer du dessa steg:

    1. Logga in på [Azure Portal](https://portal.azure.com).
    2. Gå till **App Service-certifikat**, och välj certifikatet.
    3. Välj **certifikatet har konfigurerats** > **steg 2: Kontrollera** > **domänverifiering**. Det här steget skickar ett e-postmeddelande till Azure certifikatleverantör att lösa problemet.

## <a name="custom-domain-problems"></a>Problem med anpassad domän

### <a name="a-custom-domain-returns-a-404-error"></a>En anpassad domän returnerar ett 404-fel 

#### <a name="symptom"></a>Symtom

När du bläddrar till platsen med hjälp av det anpassade domännamnet får följande felmeddelande visas:

”Fel 404-webbapp hittades inte”.

#### <a name="cause-and-solution"></a>Orsak och lösning

**Orsak 1** 

Den anpassade domänen som du har konfigurerat saknar en CNAME-post eller en post. 

**Lösning för orsak 1**

- Om du lagt till en A-post, se till att läggs också en TXT-post. Mer information finns i [skapa A-posten](./app-service-web-tutorial-custom-domain.md#create-the-a-record).
- Om du inte behöver använda rotdomänen för din app, rekommenderar vi att du använder en CNAME-post i stället för en A-post.
- Använd inte både en CNAME-post och en A-post för samma domän. Det här problemet kan orsaka en konflikt och förhindra att domänen som matchas. 

**Orsak 2** 

Webbläsaren kan fortfarande cachelagring den gamla IP-adressen för din domän. 

**Lösning för orsak 2**

Rensa webbläsaren. För Windows-enheter, kan du köra kommandot `ipconfig /flushdns`. Använd [WhatsmyDNS.net](https://www.whatsmydns.net/) att verifiera att din domän pekar till appens IP-adress. 

### <a name="you-cant-add-a-subdomain"></a>Du kan inte lägga till en underdomän 

#### <a name="symptom"></a>Symtom

Du kan inte lägga till ett nytt värdnamn till en app att tilldela en underdomän.

#### <a name="solution"></a>Lösning

- Kontakta prenumerationsadministratören för att se till att du har behörighet att lägga till ett värdnamn till appen.
- Om du behöver mer underdomäner, rekommenderar vi att du ändrar domänvärdar till Azure Service DNS (Domain Name). Genom att använda Azure DNS kan du lägga till 500 värdnamn till din app. Mer information finns i [Lägg till en underdomän](https://blogs.msdn.microsoft.com/waws/2014/10/01/mapping-a-custom-subdomain-to-an-azure-website/).

### <a name="dns-cant-be-resolved"></a>DNS kan inte lösas

#### <a name="symptom"></a>Symtom

Du har fått följande felmeddelande visas:

”DNS-posten hittades inte”.

#### <a name="cause"></a>Orsak
Det här problemet beror på något av följande orsaker:

- Time to live (TTL)-perioden har inte gått ut. Kontrollera DNS-konfigurationen för din domän att avgöra TTL-värdet och sedan vänta tills perioden att upphöra att gälla.
- DNS-konfigurationen är felaktig.

#### <a name="solution"></a>Lösning
- Vänta tills 48 timmar innan det här problemet löser sig.
- Om du kan ändra TTL-inställningen i din DNS-konfiguration, ändrar du värdet 5 minuter för att se om det löser problemet.
- Använd [WhatsmyDNS.net](https://www.whatsmydns.net/) att verifiera att din domän pekar till appens IP-adress. Om inte kan konfigurera A-post till rätt IP-adressen för appen.

### <a name="you-need-to-restore-a-deleted-domain"></a>Du behöver återställa en borttagen domän 

#### <a name="symptom"></a>Symtom
Din domän inte längre visas i Azure-portalen.

#### <a name="cause"></a>Orsak 
Prenumerationens ägare kan misstag har tagit bort domänen.

#### <a name="solution"></a>Lösning
Om din domän har tagits bort färre än sju dagar sedan, startats domänen inte har borttagningsprocessen. I det här fallet kan du köpa samma domän igen på Azure-portalen under samma prenumeration. (Vara noga med att skriva det exakta namnet i sökrutan.) Du debiteras inte igen för den här domänen. Om domänen har tagits bort mer än sju dagar sedan, kontakta [Azure-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) hjälp med att återställa domänen.

## <a name="domain-problems"></a>Domän-problem

### <a name="you-purchased-an-ssl-certificate-for-the-wrong-domain"></a>Du har köpt ett SSL-certifikat för fel domän

#### <a name="symptom"></a>Symtom

Du har köpt ett App Service-certifikat för fel domän. Du kan inte uppdatera certifikatet för att använda rätt domän.

#### <a name="solution"></a>Lösning

Ta bort certifikatet och sedan köpa ett nytt certifikat.

Om det aktuella certifikatet som använder fel domän är i tillståndet ”utfärdat”, kommer du även att faktureras för det certifikatet. App Service-certifikat är inte återbetalningsbar, men du kan kontakta [Azure-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att se om det finns andra alternativ. 

### <a name="an-app-service-certificate-was-renewed-but-the-app-shows-the-old-certificate"></a>En App Service-certifikat har förnyats, men appen visar det gamla certifikatet 

#### <a name="symptom"></a>Symtom

App Service certificate har förnyats, men den app som använder App Service certificate fortfarande använder det äldre certifikatet. Dessutom kan du fått en varning att HTTPS-protokollet krävs.

#### <a name="cause"></a>Orsak 
Azure App Service och kör ett bakgrundsjobb var åttonde timme och synkroniserar certifikat-resurs om det finns några ändringar. När du roterar eller uppdaterar ett certifikat, hämtas ibland programmet fortfarande det gamla certifikatet och inte det uppdaterade certifikatet. Det beror på att jobbet att synkronisera certifikatresursen inte har körts ännu. 
 
#### <a name="solution"></a>Lösning

Du kan tvinga en synkronisering för certifikatet:

1. Logga in på [Azure Portal](https://portal.azure.com). Välj **App Service-certifikat**, och välj sedan certifikatet.
2. Välj **uppdatera nyckel och synkronisera**, och välj sedan **synkronisering**. Synkroniseringen tar lite tid att slutföra. 
3. När synkroniseringen är klar visas följande meddelande: ”Har uppdaterats alla resurser med det senaste certifikatet”.

### <a name="domain-verification-is-not-working"></a>Domänverifiering fungerar inte 

#### <a name="symptom"></a>Symtom 
App Service certificate kräver domänkontroll innan certifikatet är klart att användas. När du väljer **Kontrollera**, utförs inte processen.

#### <a name="solution"></a>Lösning
Verifiera din domän manuellt genom att lägga till en TXT-post:
 
1.  Gå till den tjänsten DNS (Domain Name)-provider som är värd för ditt domännamn.
2.  Lägg till en TXT-post för din domän som använder värdet för den domän-token som visas i Azure-portalen. 

Vänta några minuter för DNS-spridningen att köra och välj sedan den **uppdatera** knappen för att utlösa verifieringen. 

Alternativt kan använda du metoden HTML webbsidan manuellt verifiera din domän. Den här metoden kan certifikatutfärdaren kontrollera vem som äger den domän som certifikatet har utfärdats för.

1.  Skapa en HTML-fil som heter {domänverifieringstoken} .html. Innehållet i den här filen ska vara värdet för domänverifieringstoken.
3.  Ladda upp den här filen i roten på webbservern som är värd för din domän.
4.  Välj **uppdatera** att kontrollera certifikatets status. Det kan ta några minuter för att bekräfta att slutföra.

Till exempel om du köper ett standardcertifikat för azure.com med domain verification token 1234abcd, en webbegäran gjorts https://azure.com/1234abcd.html ska returnera 1234abcd. 

> [!IMPORTANT]
> En certifikatbeställning måste endast 15 dagar så att den domänregistreringen utföras. Certifikatutfärdaren nekar certifikatet efter 15 dagar, och du debiteras inte för certifikatet. I så fall kan du ta bort certifikatet och försök igen.
>
> 

### <a name="you-cant-purchase-a-domain"></a>Du kan inte köpa en domän

#### <a name="symptom"></a>Symtom
Du kan köpa en App Service-domän i Azure-portalen.

#### <a name="cause-and-solution"></a>Orsak och lösning

Det här problemet beror på något av följande orsaker:

- Det finns inget kreditkort på Azure-prenumeration eller kreditkortet är ogiltig.

    **Lösningen**: Lägga till ett giltigt kreditkort i prenumerationen.

- Du är inte prenumerationsägare, så att du inte har behörighet att köpa en domän.

    **Lösningen**: [Tilldela rollen ägare](../role-based-access-control/role-assignments-portal.md) till ditt konto. Eller kontakta prenumerationsadministratören för att få behörighet att köpa en domän.
- Du har nått gränsen för att köpa domäner i din prenumeration. Den aktuella gränsen är 20.

    **Lösningen**: Om du vill begära en ökning av gränsen, kontakta [Azure-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Prenumerationstyp Azure stöder inte köp av en App Service-domän.

    **Lösningen**: Uppgradera din Azure-prenumeration till en annan prenumerationstyp, till exempel en betala per användning-prenumeration.

### <a name="you-cant-add-a-host-name-to-an-app"></a>Du kan inte lägga till ett värdnamn i en app 

#### <a name="symptom"></a>Symtom

När du lägger till ett värdnamn, misslyckas processen att validera och verifiera domänen.

#### <a name="cause"></a>Orsak 

Det här problemet beror på något av följande orsaker:

- Du har inte behörighet att lägga till ett värdnamn.

    **Lösningen**: Kontakta prenumerationsadministratören för att ge dig behörighet att lägga till ett värdnamn.
- Det gick inte att verifiera domänägarskapet.

    **Lösningen**: Kontrollera att din CNAME-post eller en post har konfigurerats korrekt. Skapa en CNAME-post eller en A-post för att mappa en anpassad domän till en app. If you want to use a root domain, you must use A and TXT records:

    |Posttyp|Värd|Peka på|
    |------|------|-----|
    |A|@|IP-adress för en app|
    |TXT|@|<app-name>.azurewebsites.net|
    |CNAME|www|<app-name>.azurewebsites.net|

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

**Måste jag konfigurera min anpassad domän för Min webbplats när jag köper det?**

När du köper en domän från Azure-portalen, konfigureras automatiskt App Service-programmet för att använda den anpassa domänen. Du behöver inte vidta ytterligare åtgärder. Mer information finns [Self-hjälp i Azure App Service: Lägg till ett anpassat domännamn](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name) på Channel 9.

**Kan jag använda en domän som har köpt i Azure portal så att den pekar till en Azure virtuell dator i stället?**

Ja, kan du peka domänen på en virtuell dator, lagring osv. Mer information finns i [skapa en anpassad fullständigt domännamn i Azure portal för en virtuell Windows-dator](../virtual-machines/windows/portal-create-fqdn.md).

**Min domän finns på GoDaddy och Azure DNS?**

App Service-domäner kan du använda GoDaddy för domänregistrering och Azure DNS som värd för domäner. 

**Jag har automatisk förnyelse aktiverad men fortfarande tog emot ett meddelande om förnyelse för min domän via e-post. Vad ska jag göra?**

Om du har den automatiska förnyelsen aktiverad, du behöver inte vidta några åtgärder. E-postmeddelandet meddelande tillhandahålls för att informera dig om att domänen är nära upphör att gälla och förnya manuellt om den automatiska förnyelsen inte är aktiverad.

**Kommer jag att debiteras för Azure DNS vara värd för min domän?**

Inledande kostnaden för domän-köp gäller endast domänregistrering. Förutom registrering-kostnaden finns dra på dig kostnader för Azure DNS baserat på din användning. Mer information finns i [priser för Azure DNS](https://azure.microsoft.com/pricing/details/dns/) för mer information.

**Jag har köpt min domän tidigare från Azure-portalen och vill flytta från GoDaddy värd till värd för Azure DNS. Hur gör jag?**

Det är inte obligatoriskt att migrera till Azure DNS-värd. Om du vill migrera till Azure DNS kan hanteringsupplevelse domän i Azure portal om du ger information om stegen för att flytta till Azure DNS. Om domänen har köpts via App Service, är migrering från GoDaddy som är värd för till Azure DNS relativt sömlös proceduren.

**Jag skulle vilja köpa min domän från App Service-domänen men kan jag använda min domän på GoDaddy i stället för Azure DNS?**

Från och med den 24 juli 2017 finns App Service-domäner som har köpt i portalen på Azure DNS. Om du föredrar att använda en annan värdleverantör måste du gå till sin webbplats för att hämta en domän som värd-lösning.

**Måste jag betala för sekretesskydd för min domän?**

När du köper en domän via Azure-portalen, kan du lägga till sekretess utan extra kostnad. Detta är en av fördelarna med att köpa din domän med Azure App Service.

**Om jag väljer jag vill inte längre min domän, kan jag få min pengar tillbaka?**

Du debiteras inte för en period om fem dagar, under tiden kan du bestämma att du inte vill att domänen när du köper en domän. Om du bestämmer dig för du inte vill att domänen inom denna period på fem dagar, debiteras du inte. (.uk domäner är ett undantag till detta. Om du köper en .uk domän, debiteras du omedelbart och du kan inte återbetalas.)

**Kan jag använda domänen i en annan Azure App Service-app i min prenumeration?**

Ja. När du använder anpassade domäner och SSL-bladet i Azure-portalen kan se du de domäner som du har köpt. Du kan konfigurera din app för att använda någon av dessa domäner.

**Kan jag överföra en domän från en prenumeration till en annan prenumeration?**

Du kan flytta en domän till en annan prenumeration/resurs med det [Move-AzureRmResource](https://docs.microsoft.com/powershell/module/AzureRM.Resources/Move-AzureRmResource?view=azurermps-6.13.0) PowerShell-cmdlet.

**Hur hanterar jag mina anpassade domäner om jag inte för närvarande har en Azure App Service-app?**

Du kan hantera din domän, även om du inte har en App Service Web App. Domänen kan användas för Azure-tjänster som virtuell dator, lagring osv. Om du planerar att använda domänen för App Service Web Apps, måste du ta med en Webbapp som inte är på den kostnadsfria App Service-planen för att binda till domänen till din webbapp.

**Kan jag flytta en webbapp med en anpassad domän till en annan prenumeration eller från App Service Environment v1 till V2?**

Ja, kan du flytta din webbapp mellan prenumerationer. Följ riktlinjerna i [hur du flyttar resurser i Azure](../azure-resource-manager/resource-group-move-resources.md). Det finns några begränsningar när du flyttar webbappen. Mer information finns i [begränsningar för att flytta App Service-resurser](../azure-resource-manager/resource-group-move-resources.md#app-service-limitations
).

När du har flyttat webbappen förblir värden namnet bindningar för domäner i de anpassade domäner inställningen desamma. Inga ytterligare åtgärder krävs för att konfigurera värden namnet bindningar.
