---
title: Felsöka domän- och SSL-certifikatfel i Azure web apps | Microsoft Docs
description: Felsöka domän- och SSL-certifikatfel i Azure webbappar
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
ms.date: 04/18/2018
ms.author: genli
ms.openlocfilehash: 59a9011edef49494288716ab16f30e28e440293b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34195190"
---
# <a name="troubleshoot-domain-and-ssl-certificate-problems-in-azure-web-apps"></a>Felsöka domän- och SSL-certifikatfel i Azure webbappar

Den här artikeln innehåller vanliga problem som kan uppstå när du konfigurerar en domän eller SSL-certifikat för din Azure-webbappar. Här beskrivs också möjliga orsaker och lösningar på problemen.

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experter på [MSDN och Stack Overflow-forum](https://azure.microsoft.com/support/forums/). Alternativt kan du lagra en incident i Azure-supporten. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och välj **Get Support**.

## <a name="certificate-problems"></a>Problem med säkerhetscertifikat

### <a name="you-cant-add-an-ssl-certificate-binding-to-a-web-app"></a>Du kan inte lägga till en SSL-certifikat-bindning till en webbapp 

#### <a name="symptom"></a>Symtom

När du lägger till en SSL-bindning visas följande felmeddelande:

”Det gick inte att lägga till SSL-bindning. Kan inte ange certifikatet för befintliga VIP eftersom en annan VIP redan använder certifikatet ”.

#### <a name="cause"></a>Orsak

Det här problemet kan inträffa om du har flera IP-baserade SSL-bindningar för samma IP-adress i flera webbprogram. Webbprogrammet A har till exempel en IP-baserade SSL med ett gammalt certifikat. Webbprogrammet B har en IP-baserade SSL med ett nytt certifikat för samma IP-adress. När du uppdaterar web app SSL-bindning med det nya certifikatet, misslyckas med felet eftersom samma IP-adress som används för en annan app. 

#### <a name="solution"></a>Lösning 

Använd någon av följande metoder för att åtgärda problemet:

- Ta bort IP-baserade SSL-bindning på webbprogram som använder det gamla certifikatet. 
- Skapa en ny IP-baserade SSL-bindning som använder det nya certifikatet.

### <a name="you-cant-delete-a-certificate"></a>Du kan inte ta bort ett certifikat 

#### <a name="symptom"></a>Symtom

När du försöker ta bort ett certifikat visas följande felmeddelande:

”Det gick inte att ta bort certifikatet eftersom det för närvarande används i en SSL-bindning. SSL-bindning måste tas bort innan du kan ta bort certifikatet ”.

#### <a name="cause"></a>Orsak

Det här problemet kan inträffa om en annan webbapp använder certifikatet.

#### <a name="solution"></a>Lösning

Ta bort SSL-bindning för certifikatet från web apps. Försök att ta bort certifikatet. Om du fortfarande inte kan ta bort certifikatet Rensa cacheminne för internet-webbläsaren och öppna den Azure-portalen i ett nytt webbläsarfönster. Försök att ta bort certifikatet.

### <a name="you-cant-purchase-an-app-service-certificate"></a>Du kan inte köpa ett certifikat för App Service 

#### <a name="symptom"></a>Symtom
Du kan inte köpa en [Azure App Service certifikat](./web-sites-purchase-ssl-web-site.md) från Azure-portalen.

#### <a name="cause-and-solution"></a>Orsak och lösning
Det här problemet kan uppstå om någon av följande skäl:

- Programtjänstplanen är kostnadsfri eller delad. Dessa prisnivåer stöder inte SSL. 

    **Lösningen**: uppgradera App Service-plan för webbprogrammet till Standard.

- Prenumerationen saknar ett giltigt kreditkort.

    **Lösningen**: lägga till ett giltigt kreditkort till prenumerationen. 

- Prenumerationserbjudande stöder inte Köp ett certifikat för App Service, till exempel Microsoft Student.  

    **Lösningen**: uppgradera din prenumeration. 

- Prenumerationen nått gränsen för inköp som tillåts för en prenumeration.

    **Lösningen**: apptjänstcertifikat har en begränsning på 10 certifikat inköp för betala per användning och EA prenumerationstyper. För andra prenumerationstyper av är gränsen 3. Om du vill öka gränsvärdet Kontakta [Azure-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Apptjänst-certifikatet har markerats som bedrägeri. Du har fått följande felmeddelande: ”ditt certifikat har flaggats för eventuellt bedrägeriförsök. Begäran är för närvarande granskas. Om certifikatet inte är användbar inom 24 timmar, kontakta Azure Support ”.

    **Lösningen**: om certifikatet har markerats som bedrägeri och inte är löst efter 24 timmar, följer du dessa steg:

    1. Logga in på [Azure Portal](https://portal.azure.com).
    2. Gå till **Apptjänstcertifikat**, och välj certifikatet.
    3. Välj **certifikat Configuration** > **steg 2: Kontrollera** > **domänverifiering**. Det här steget skickar ett e-postmeddelande till Azure certifikat-leverantör för att lösa problemet.

## <a name="domain-problems"></a>Domän-problem

### <a name="you-purchased-an-ssl-certificate-for-the-wrong-domain"></a>Du har köpt ett SSL-certifikat för fel domän

#### <a name="symptom"></a>Symtom

Du har köpt ett certifikat för App Service för fel domän. Du kan inte uppdatera certifikatet för att använda rätt domän.

#### <a name="solution"></a>Lösning

Ta bort certifikatet och köp sedan ett nytt certifikat.

Om det aktuella certifikatet som använder fel domän är i tillståndet ”utfärdat”, måste du också att debiteras för certifikatet. Apptjänstcertifikat inte återbetalas, men du kan kontakta [Azure-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om det finns andra alternativ. 

### <a name="an-app-service-certificate-was-renewed-but-the-web-app-shows-the-old-certificate"></a>Ett certifikat för App Service förnyades, men visar det gamla certifikatet för webbprogrammet 

#### <a name="symptom"></a>Symtom

Apptjänst certifikatet förnyades, men den webbapp som använder certifikat för Apptjänst fortfarande använder det gamla certifikatet. Dessutom tog emot en varning att HTTPS-protokollet krävs.

#### <a name="cause"></a>Orsak 
Funktionen Web Apps i Azure App Service kör bakgrunden var åttonde timme och synkroniserar resursen certifikatet om det finns några ändringar. När du roterar eller uppdaterar ett certifikat, hämtas ibland programmet fortfarande det gamla certifikatet och inte det uppdaterade certifikatet. Det beror på att jobbet ska synkroniseras resursen certifikat inte har körts ännu. 
 
#### <a name="solution"></a>Lösning

Du kan tvinga en synkronisering för certifikatet:

1. Logga in på [Azure Portal](https://portal.azure.com). Välj **Apptjänstcertifikat**, och välj sedan certifikatet.
2. Välj **genererar nya nycklar och synkronisera**, och välj sedan **Sync**. Synkroniseringen tar en stund. 
3. När synkroniseringen är klar visas följande meddelande: ”har uppdaterats alla resurser med det senaste certifikatet”.

### <a name="domain-verification-is-not-working"></a>Verifiering av domän fungerar inte 

#### <a name="symptom"></a>Symtom 
Apptjänst certifikat kräver verifiering av domän innan certifikatet är redo att användas. När du väljer **Kontrollera**, misslyckas processen.

#### <a name="solution"></a>Lösning
Verifiera din domän manuellt genom att lägga till en TXT-post:
 
1.  Gå till Domain Name Service (DNS)-providern som är värd för ditt domännamn.
2.  Lägg till en TXT-post för domänen som använder värdet för den domän-token som visas i Azure-portalen. 

Vänta några minuter för DNS-spridningen att köra och välj sedan den **uppdatera** knappen utlöser verifiering. 

Alternativt kan använda du metoden HTML-webbsidan för att manuellt verifiera din domän. Den här metoden kan bekräfta domän ägarskap för domänen som certifikatet har utfärdats för certifikatutfärdaren.

1.  Skapa en HTML-fil med namnet {domän verifieringstoken} .html. Innehållet i den här filen ska värdet för domänen verifieringstoken.
3.  Överför den här filen i roten på webbservern som är värd för din domän.
4.  Välj **uppdatera** att kontrollera certifikatstatus. Det kan ta några minuter för verifiering ska slutföras.

Till exempel om du köper ett standard-certifikat för azure.com med domänen verifiering token 1234abcd, en webbegäran gjort http://azure.com/1234abcd.html ska returnera 1234abcd. 

> [!IMPORTANT]
> En certifikat-ordning har endast 15 dagar att slutföra åtgärden för domän-verifiering. Efter 15 dagar certifikatutfärdaren nekar certifikatet och du debiteras inte för certifikatet. I så fall kan du ta bort certifikatet och försök igen.
>
> 

### <a name="you-cant-purchase-a-domain"></a>Du kan inte köpa en domän

#### <a name="symptom"></a>Symtom
Du kan köpa en domän från domänen Web Apps eller Apptjänst i Azure-portalen.

#### <a name="cause-and-solution"></a>Orsak och lösning

Det här problemet uppstår på något av följande skäl:

- Det finns inget kreditkort på Azure-prenumeration eller kreditkortet är ogiltig.

    **Lösningen**: lägga till ett giltigt kreditkort till prenumerationen.

- Du är inte prenumerationsägaren, så att du inte har behörighet att köpa en domän.

    **Lösningen**: [lägger du till rollen ägare](../billing/billing-add-change-azure-subscription-administrator.md) till ditt konto. Eller kontakta en administratör för prenumerationen för att få behörighet att köpa en domän.
- Du har nått gränsen för att köpa domäner i din prenumeration. Den aktuella gränsen är 20.

    **Lösningen**: Om du vill begära en ökning av gränsen Kontakta [Azure-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Prenumerationstyp Azure stöder inte köp av en Apptjänst-domän.

    **Lösningen**: uppgradera din Azure-prenumeration till en annan prenumerationstyp, till exempel en prenumeration betala per användning.

### <a name="you-cant-add-a-host-name-to-a-web-app"></a>Du kan inte lägga till ett värdnamn till en webbapp 

#### <a name="symptom"></a>Symtom

När du lägger till ett värdnamn, misslyckas processen att validera och verifiera domänen.

#### <a name="cause"></a>Orsak 

Det här problemet uppstår på något av följande skäl:

- Du har inte behörighet att lägga till ett värdnamn.

    **Lösningen**: Be administratören att ge dig behörighet att lägga till ett värdnamn prenumeration.
- Det gick inte att verifiera ditt ägarskap för domänen.

    **Lösningen**: Verifiera att din CNAME-post eller en post har konfigurerats korrekt. Om du vill mappa en anpassad domän till webb-app, skapa en CNAME-post eller en A-post. Om du vill använda en rotdomän måste du använda en och TXT-poster:

    |Posttyp|Värd|Peka på|
    |------|------|-----|
    |A|@|IP-adressen för en webbapp|
    |TXT|@|< programnamn >. azurewebsites.net|
    |CNAME|www|< programnamn >. azurewebsites.net|

### <a name="dns-cant-be-resolved"></a>DNS kan inte lösas

#### <a name="symptom"></a>Symtom

Du har fått följande felmeddelande visas:

”DNS-posten hittades inte”.

#### <a name="cause"></a>Orsak
Det här problemet uppstår på något av följande skäl:

- Time to live (TTL)-perioden inte har gått ut. Kontrollera DNS-konfiguration för domänen för att fastställa TTL-värdet och vänta tills perioden ska upphöra att gälla.
- DNS-konfigurationen är felaktig.

#### <a name="solution"></a>Lösning
- Vänta tills 48 timmar innan det här problemet ska lösas av sig självt.
- Om du kan ändra TTL-inställningen i DNS-konfiguration, ändrar du värdet 5 minuter för att se om det löser problemet.
- Använd [WhatsmyDNS.net](https://www.whatsmydns.net/) att kontrollera att din domän pekar till webbappens IP-adress. Om det inte du konfigurera A-posten till rätt IP-adressen för webbappen.

### <a name="you-need-to-restore-a-deleted-domain"></a>Du måste återställa en borttagen domän 

#### <a name="symptom"></a>Symtom
Din domän visas inte längre i Azure-portalen.

#### <a name="cause"></a>Orsak 
Prenumerationens ägare kan av misstag ha bort domänen.

#### <a name="solution"></a>Lösning
Om din domän har tagits bort färre än sju dagar sedan, startat domänen inte har borttagningsprocessen. I det här fallet kan du köpa samma domän igen på Azure portal under samma prenumeration. (Måste du ange det exakta domännamnet i sökrutan.) Du kommer inte att debiteras igen för den här domänen. Om domänen har tagits bort mer än sju dagar sedan, kontakta [Azure-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) för att få hjälp att återställa domänen.

### <a name="a-custom-domain-returns-a-404-error"></a>En anpassad domän returnerar ett 404-fel 

#### <a name="symptom"></a>Symtom

När du bläddrar till platsen med hjälp av det anpassade domännamnet får följande felmeddelande:

”Fel 404-webbapp gick inte att hitta”.


#### <a name="cause-and-solution"></a>Orsak och lösning

**Orsak 1** 

En CNAME-post eller en post saknas i den anpassade domänen som du har konfigurerat. 

**Lösning för orsak 1**

- Om du har lagt till en A-post, kontrollerar du att en TXT-post läggs också till. Mer information finns i [skapa sedan A-posten](./app-service-web-tutorial-custom-domain.md#create-the-a-record).
- Om du inte behöver använda rotdomänen för ditt webbprogram, rekommenderar vi att du använder en CNAME-post i stället för en A-post.
- Använd inte både en CNAME-post och en A-post för samma domän. Detta kan orsaka en konflikt och förhindrar att domänen som matchas. 

**Orsak 2** 

Webbläsaren kan fortfarande cachelagring den gamla IP-adressen för din domän. 

**Lösning för orsak 2**

Avmarkera webbläsaren. För Windows-enheter kan du köra kommandot `ipconfig /flushdns`. Använd [WhatsmyDNS.net](https://www.whatsmydns.net/) att kontrollera att din domän pekar till webbappens IP-adress. 

### <a name="you-cant-add-a-subdomain"></a>Du kan inte lägga till en underdomän 

#### <a name="symptom"></a>Symtom

Du kan inte lägga till ett nytt värdnamn till ett webbprogram för att tilldela en underdomän.

#### <a name="solution"></a>Lösning

- Kontrollera prenumerationen administratör att se till att du har behörighet att lägga till ett värdnamn i webbprogrammet.
- Om du behöver mer underdomäner, rekommenderar vi att du ändrar den domän som värd till Azure DNS. Du kan lägga till 500 värdnamn till ditt webbprogram med hjälp av Azure DNS. Mer information finns i [lägga till en underdomän](https://blogs.msdn.microsoft.com/waws/2014/10/01/mapping-a-custom-subdomain-to-an-azure-website/).











 


















