---
title: Felsöka domän och SSL - certifikat i Azure App Service | Microsoft Docs
description: Felsöka domän och SSL-certifikat problem i Azure web apps
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
ms.date: 10/31/2018
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: 726bc78532cfe621eb3f3787aa05a7a54571a8c3
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2018
ms.locfileid: "53251614"
---
# <a name="troubleshoot-domain-and-ssl-certificate-problems-in-azure-web-apps"></a>Felsöka domän och SSL-certifikat problem i Azure web apps

Den här artikeln innehåller vanliga problem som kan uppstå när du konfigurerar en domän eller SSL-certifikat för Azure web apps. Här beskrivs också möjliga orsaker och lösningar för dessa problem.

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experter på [MSDN och Stack Overflow-forum](https://azure.microsoft.com/support/forums/). Alternativt kan du arkivera en Azure-support-incident. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och välj **få Support**.

## <a name="certificate-problems"></a>Problem med säkerhetscertifikat

### <a name="you-cant-add-an-ssl-certificate-binding-to-a-web-app"></a>Du kan inte lägga till en SSL-certifikat-bindning till en webbapp 

#### <a name="symptom"></a>Symtom

När du lägger till en SSL-bindning, visas följande felmeddelande visas:

”Det gick inte att lägga till SSL-bindning. Det går inte att ange certifikatet för befintliga VIP eftersom en annan VIP redan använder certifikatet ”.

#### <a name="cause"></a>Orsak

Det här problemet kan inträffa om du har flera IP-baserad SSL-bindningar för samma IP-adress på flera web apps. Webbapp A har exempelvis en IP-baserad SSL med ett gammalt certifikat. Webbapp B har en IP-baserad SSL med ett nytt certifikat för samma IP-adress. När du uppdaterar SSL-bindning web app med det nya certifikatet, misslyckas med felet eftersom samma IP-adress som används för en annan app. 

#### <a name="solution"></a>Lösning 

Använd någon av följande metoder för att åtgärda problemet:

- Ta bort IP-baserad SSL-bindning på webbappen som använder det äldre certifikatet. 
- Skapa en ny IP-baserad SSL-bindning som använder det nya certifikatet.

### <a name="you-cant-delete-a-certificate"></a>Du kan inte ta bort ett certifikat 

#### <a name="symptom"></a>Symtom

När du försöker ta bort ett certifikat, visas följande felmeddelande visas:

”Det gick inte att ta bort certifikatet eftersom den används för närvarande i en SSL-bindning. SSL-bindning måste tas bort innan du kan ta bort certifikatet ”.

#### <a name="cause"></a>Orsak

Det här problemet kan inträffa om en annan webbapp använder certifikatet.

#### <a name="solution"></a>Lösning

Ta bort SSL-bindning för det certifikatet från web apps. Försök att ta bort certifikatet. Om du fortfarande inte kan ta bort certifikatet Rensa webbläsarcachen för internet och öppna Azure-portalen i ett nytt webbläsarfönster. Försök att ta bort certifikatet.

### <a name="you-cant-purchase-an-app-service-certificate"></a>Du kan inte köpa ett certifikat för App Service 

#### <a name="symptom"></a>Symtom
Du kan inte köpa ett [Azure App Service certificate](./web-sites-purchase-ssl-web-site.md) från Azure-portalen.

#### <a name="cause-and-solution"></a>Orsak och lösning
Det här problemet kan inträffa för någon av följande orsaker:

- App Service-planen är kostnadsfri eller delad. De här prisnivåerna stöder inte SSL. 

    **Lösningen**: Uppgradera App Service-plan för webbappen till Standard.

- Prenumerationen har inte ett giltigt kreditkort.

    **Lösningen**: Lägga till ett giltigt kreditkort i prenumerationen. 

- Prenumerationserbjudande stöder inte köpa ett App Service-certifikat, till exempel Microsoft Student.  

    **Lösningen**: Uppgradera din prenumeration. 

- Prenumerationen nått gränsen för köp som tillåts för en prenumeration.

    **Lösningen**: App Service-certifikat har en gräns på 10 inköp av certifikat för prenumerationstyper betala per användning och EA. För andra prenumerationstyper av är gränsen 3. Om du vill öka gränsen kontaktar [Azure-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- App Service certificate har markerats som bedrägeri. Du har fått följande felmeddelande visas: ”Ditt certifikat har flaggats för potentiellt bedrägeri. Begäran är för närvarande under granskning. Om certifikatet inte är användbar inom 24 timmar, kontakta supporten för Azure ”.

    **Lösningen**: Om certifikatet har markerats som bedrägerier och inte är löst efter 24 timmar, följer du dessa steg:

    1. Logga in på [Azure Portal](https://portal.azure.com).
    2. Gå till **App Service-certifikat**, och välj certifikatet.
    3. Välj **certifikatet har konfigurerats** > **steg 2: Kontrollera** > **domänverifiering**. Det här steget skickar ett e-postmeddelande till Azure certifikatleverantör att lösa problemet.

## <a name="domain-problems"></a>Domän-problem

### <a name="you-purchased-an-ssl-certificate-for-the-wrong-domain"></a>Du har köpt ett SSL-certifikat för fel domän

#### <a name="symptom"></a>Symtom

Du har köpt ett App Service-certifikat för fel domän. Du kan inte uppdatera certifikatet för att använda rätt domän.

#### <a name="solution"></a>Lösning

Ta bort certifikatet och sedan köpa ett nytt certifikat.

Om det aktuella certifikatet som använder fel domän är i tillståndet ”utfärdat”, kommer du även att faktureras för det certifikatet. App Service-certifikat är inte återbetalningsbar, men du kan kontakta [Azure-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att se om det finns andra alternativ. 

### <a name="an-app-service-certificate-was-renewed-but-the-web-app-shows-the-old-certificate"></a>En App Service-certifikat har förnyats, men appen visar det gamla certifikatet 

#### <a name="symptom"></a>Symtom

App Service certificate har förnyats, men den webbapp som använder App Service certificate fortfarande använder det äldre certifikatet. Dessutom kan du fått en varning att HTTPS-protokollet krävs.

#### <a name="cause"></a>Orsak 
Funktionen Web Apps i Azure App Service och kör ett bakgrundsjobb var åttonde timme och synkroniserar certifikat-resurs om det finns några ändringar. När du roterar eller uppdaterar ett certifikat, hämtas ibland programmet fortfarande det gamla certifikatet och inte det uppdaterade certifikatet. Det beror på att jobbet att synkronisera certifikatresursen inte har körts ännu. 
 
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
Du kan köpa en domän från Web Apps och App Service-domänen i Azure-portalen.

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

### <a name="you-cant-add-a-host-name-to-a-web-app"></a>Du kan inte lägga till ett värdnamn till en webbapp 

#### <a name="symptom"></a>Symtom

När du lägger till ett värdnamn, misslyckas processen att validera och verifiera domänen.

#### <a name="cause"></a>Orsak 

Det här problemet beror på något av följande orsaker:

- Du har inte behörighet att lägga till ett värdnamn.

    **Lösningen**: Kontakta prenumerationsadministratören för att ge dig behörighet att lägga till ett värdnamn.
- Det gick inte att verifiera domänägarskapet.

    **Lösningen**: Kontrollera att din CNAME-post eller en post har konfigurerats korrekt. Skapa en CNAME-post eller en A-post för att mappa en anpassad domän till webbapp. Om du vill använda en rotdomän måste du använda A- och TXT-poster:

    |Posttyp|Värd|Peka på|
    |------|------|-----|
    |A|@|IP-adressen för en webbapp|
    |TXT|@|< appnamn >. azurewebsites.net|
    |CNAME|www|< appnamn >. azurewebsites.net|

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
- Använd [WhatsmyDNS.net](https://www.whatsmydns.net/) att verifiera att din domän pekar på webbappens IP-adress. Om inte kan konfigurera A-post till rätt IP-adressen för webbappen.

### <a name="you-need-to-restore-a-deleted-domain"></a>Du behöver återställa en borttagen domän 

#### <a name="symptom"></a>Symtom
Din domän inte längre visas i Azure-portalen.

#### <a name="cause"></a>Orsak 
Prenumerationens ägare kan misstag har tagit bort domänen.

#### <a name="solution"></a>Lösning
Om din domän har tagits bort färre än sju dagar sedan, startats domänen inte har borttagningsprocessen. I det här fallet kan du köpa samma domän igen på Azure-portalen under samma prenumeration. (Vara noga med att skriva det exakta namnet i sökrutan.) Du debiteras inte igen för den här domänen. Om domänen har tagits bort mer än sju dagar sedan, kontakta [Azure-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) för att få hjälp att återställa domänen.

### <a name="a-custom-domain-returns-a-404-error"></a>En anpassad domän returnerar ett 404-fel 

#### <a name="symptom"></a>Symtom

När du bläddrar till platsen med hjälp av det anpassade domännamnet får följande felmeddelande visas:

”Fel 404-webbapp hittades inte”.


#### <a name="cause-and-solution"></a>Orsak och lösning

**Orsak 1** 

Den anpassade domänen som du har konfigurerat saknar en CNAME-post eller en post. 

**Lösning för orsak 1**

- Om du lagt till en A-post, se till att läggs också en TXT-post. Mer information finns i [skapa A-posten](./app-service-web-tutorial-custom-domain.md#create-the-a-record).
- Om du inte behöver använda rotdomänen för din webbapp, rekommenderar vi att du använder en CNAME-post i stället för en A-post.
- Använd inte både en CNAME-post och en A-post för samma domän. Detta kan orsaka en konflikt och förhindra att domänen som matchas. 

**Orsak 2** 

Webbläsaren kan fortfarande cachelagring den gamla IP-adressen för din domän. 

**Lösning för orsak 2**

Rensa webbläsaren. För Windows-enheter, kan du köra kommandot `ipconfig /flushdns`. Använd [WhatsmyDNS.net](https://www.whatsmydns.net/) att verifiera att din domän pekar på webbappens IP-adress. 

### <a name="you-cant-add-a-subdomain"></a>Du kan inte lägga till en underdomän 

#### <a name="symptom"></a>Symtom

Du kan inte lägga till ett nytt värdnamn till en webbapp för att tilldela en underdomän.

#### <a name="solution"></a>Lösning

- Kontakta prenumerationsadministratören för att se till att du har behörighet att lägga till ett värdnamn till webbappen.
- Om du behöver mer underdomäner, rekommenderar vi att du ändrar domänvärdar till Azure DNS. Genom att använda Azure DNS kan du lägga till 500 värdnamn till din webbapp. Mer information finns i [Lägg till en underdomän](https://blogs.msdn.microsoft.com/waws/2014/10/01/mapping-a-custom-subdomain-to-an-azure-website/).











 


















