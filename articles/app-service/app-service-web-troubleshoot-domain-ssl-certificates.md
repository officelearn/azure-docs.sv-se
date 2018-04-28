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
ms.openlocfilehash: ba21475b771f1688c0a3f2f34c8d961fba5cd182
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="troubleshoot-domain-and-ssl-certificate-problems-in-azure-web-apps"></a>Felsöka domän- och SSL-certifikatfel i Azure webbappar

Den här artikeln innehåller vanliga problem som kan uppstå när du konfigurerar domän- eller SSL-certifikat för din Azure-webbappar. Här beskrivs också möjliga orsaker och lösningar på problemen.

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experter på [MSDN Azure och Stack Overflow-forum](https://azure.microsoft.com/support/forums/). Alternativt kan du även filen en incident i Azure-supporten. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och klicka på **Get Support**.

## <a name="certificate-problems"></a>Problem med säkerhetscertifikat

### <a name="unable-to-add-bind-ssl-certificate-to-a-web-app"></a>Det gick inte att Lägg till bindning SSL-certifikatet till en Webbapp 

### <a name="symptom"></a>Symtom

När du lägger till en SSL-bindning visas följande felmeddelande:

**Det gick inte att lägga till SSL-bindning. Kan inte ange certifikatet för befintliga VIP eftersom en annan VIP redan använder de certifikat.**

### <a name="cause"></a>Orsak

Det här problemet kan inträffa om du har flera IP-baserade SSL-bindningar för samma IP-adress i flera webbprogram. Webbprogrammet A har till exempel IP-baserade SSL med gammalt certifikat. Webbprogram B med IP-baserade SSL med nytt certifikat för samma IP-adress. När du uppdaterar web app SSL-bindning med det nya certifikatet, misslyckas den med felet eftersom samma IP-adress som används för en annan app. 

### <a name="solution"></a>Lösning 

Använd någon av följande metoder för att åtgärda problemet:

- Ta bort IP-baserade SSL-bindning på webbprogram som använder det gamla certifikatet. 
- Skapa en ny IP-baserade SSL-bindning som använder det nya certifikatet.

### <a name="unable-to-delete-a-certificate"></a>Det går inte att ta bort ett certifikat 

### <a name="symptom"></a>Symtom

När du försöker ta bort ett certifikat visas följande felmeddelande:

**Det går inte att ta bort certifikatet eftersom det för närvarande används i en SSL-bindning. SSL-bindning måste tas bort innan du kan ta bort certifikatet.**

### <a name="cause"></a>Orsak

Det här problemet kan inträffa om certifikatet som används av ett annat webbprogram.

### <a name="solution"></a>Lösning

Ta bort SSL-bindning för certifikatet från web apps. Försök att ta bort certifikatet. Om du fortfarande inte kan ta bort certifikatet Rensa Internet webbläsarens cacheminne, öppna Azure-portalen i ett nytt webbläsarfönster. Och försök sedan ta bort certifikatet.

### <a name="unable-to-purchase-an-app-service-certificate"></a>Det gick inte att köpa ett certifikat för App Service 

### <a name="symptom"></a>Symtom
Du kan inte köpa en [Apptjänst certifikat](./web-sites-purchase-ssl-web-site.md) från Azure-portalen.

### <a name="cause-and-solution"></a>Orsak och lösning
Det här problemet kan uppstå om någon av följande skäl:

- Programtjänstplanen är ”lediga” eller ”delade”. Vi stöder inte SSL för de här prisnivåerna. 

    **Lösningen**: uppgradera App Service-plan för webbprogrammet ”standard”.

- Prenumerationen har inte ett giltigt kreditkort.

    **Lösningen**: lägga till ett giltigt kreditkort till prenumerationen. 

- Erbjudande för prenumerationen har inte stöd för att köpa ett certifikat för App Service, till exempel Microsoft Student.  

    **Lösningen**: uppgradera din prenumeration. 

- Prenumerationen har nått maxgränsen för inköp som tillåts för en prenumeration.

    **Lösningen**: apptjänstcertifikat har en begränsning på 10 certifikat inköp för Pay som Go och EA prenumerationer. För andra prenumerationstyper av är gränsen 3. Om du vill öka gränsvärdet Kontakta [Azure-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Apptjänst-certifikatet har markerats som bedrägeri. Du får följande felmeddelande: ”ditt certifikat har flaggats för eventuellt bedrägeriförsök. Begäran är för närvarande granskas. Om certifikatet inte är användbar inom 24 timmar ”.

    **Lösningen**: om certifikatet har markerats som bedrägeri och kvarstår efter 24 timmar, följer du dessa steg:

    1. Logga in på [Azure-portalen](https://portal.azure.com).
    2. Gå till **Apptjänstcertifikat**, Välj certifikatet.
    3. Välj **certifikat Configuration** > **steg 2: Kontrollera** > **domänverifiering**. Detta skickar ett e-postmeddelande till Azure certifikat-leverantör för att lösa problemet.

## <a name="domain-problems"></a>Domän-problem

### <a name="purchased-ssl-certificate-for-wrong-domain"></a>Köpt SSL-certifikat för fel domän

### <a name="symptom"></a>Symtom

Du har köpt ett certifikat för App Service för fel domän och du kan inte uppdatera certifikatet för att använda rätt domän.

### <a name="solution"></a>Lösning

- Ta bort certifikatet och köp sedan ett nytt certifikat.
- Om det aktuella certifikatet som använder fel domän är i tillståndet ”utfärdat”, kommer sedan du också att debiteras för certifikatet. Apptjänstcertifikat inte återbetalas, men du kan kontakta [Azure-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om det finns andra alternativ. 

### <a name="app-service-certificate-was-renewed-but-still-shows-the-old-certificate"></a>Apptjänst certifikatet förnyades men visas fortfarande gammalt certifikat 

### <a name="symptom"></a>Symtom

Apptjänst certifikatet förnyades men webbprogram som använder App Service-certifikatet är fortfarande använder det gamla certifikatet. Dessutom tog emot en varning att HTTPS-protokollet krävs.

### <a name="cause"></a>Orsak 
Tjänsten Web app körs bakgrunden var åttonde timme och synkroniserar resursen certifikatet om det finns några ändringar. Därför när du roterar eller uppdaterar ett certifikat, hämtas ibland programmet fortfarande det gamla certifikatet och inte det uppdaterade certifikatet. Det beror på att jobbet ska synkroniseras resursen certifikat inte har körts ännu. 
 
### <a name="solution"></a>Lösning

Du kan tvinga en synkronisering för certifikatet:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj den **Apptjänstcertifikat**, och välj sedan certifikatet.
2. Klicka på **genererar nya nycklar och synkronisera**, och klicka sedan på **Sync**. Detta tar en stund. 
3. När synkroniseringen är klar visas följande meddelande: ”har uppdaterats alla resurser med det senaste certifikatet”.

### <a name="domain-verification-is-not-working"></a>Verifiering av domän fungerar inte 

### <a name="symptom"></a>Symtom 
Apptjänst certifikat kräver verifiering av domän innan certifikatet är redo att användas. När du klickar på **Kontrollera**, misslyckas processen.

### <a name="solution"></a>Lösning
Verifiera din domän manuellt genom att lägga till en TXT-post:
 
1.  Gå till Domain Name Service (DNS)-providern som är värd för ditt domännamn.
2.  Lägg till en TXT-post för domänen som använder värdet för den domän-token som visas i Azure-portalen. 

Vänta några minuter för DNS-spridningen att köra och klicka sedan **uppdatera** knappen utlöser verifiering. 

Alternativ metod för att verifiera manuellt är ”HTML-webbsida metod” som kan användas för att tillåta att bekräfta domän ägarskap för domänen certifikatet har utfärdats för certifikatutfärdaren.

1.  Skapa en HTML-fil med namnet {domän verifiering Token} .html. 
2.  Innehållet i den här filen ska värdet för domänen verifiering Token.
3.  Överför den här filen i roten på webbservern som är värd för din domän
4.  Klicka på **uppdatera** att kontrollera status för certifikatet. Det kan ta några minuter för verifiering ska slutföras.

Till exempel om du köper ett standard-certifikat för azure.com med domänen verifiering Token '1234abcd' sedan en webbegäran görs http://azure.com/1234abcd.html ska returnera 1234abcd. 

> [!IMPORTANT]
> En certifikat-ordning har endast 15 dagar att slutföra åtgärden för domän-verifiering. Efter 15 dagar certifikatet nekas av certifikatutfärdaren och du debiteras inte för certifikatet. I så fall kan du ta bort certifikatet och försök igen.
>
> 

### <a name="unable-to-purchase-a-domain"></a>Det gick inte att köpa en domän

### <a name="symptom"></a>Symtom
Du kan köpa en domän från webbapp eller programdomänen för tjänsten i Azure-portalen.

### <a name="cause-and-solution"></a>Orsak och lösning

Det här problemet uppstår på något av följande skäl:

- Inget kreditkort på Azure-prenumeration eller ogiltigt kreditkort.

    **Lösningen**: lägga till ett giltigt kreditkort till prenumerationen om du inte har någon.

- Om du inte är prenumerationsägaren kanske du inte behörighet att köpa domän.

    **Lösningen**: [lägger du till rollen ägare](../billing/billing-add-change-azure-subscription-administrator.md) till ditt konto eller kontakt med administratör för prenumerationen för att få behörighet att köpa en domän.
- Du har nått gränsen för att köpa domäner i din prenumeration. Den aktuella gränsen är 20.

    **Lösningen**: begäran ökning gränsen, kontakta [Azure-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Prenumerationstyp Azure stöder inte köp i App Service-domän.

    **Lösningen**: uppgradera din Azure-prenumeration till andra prenumerationstyper av, till exempel en prenumeration betala per användning.

### <a name="unable-to-add-a-hostname-to-web-app"></a>Det går inte att lägga till ett värdnamn i webbappen 

### <a name="symptom"></a>Symtom

När du lägger till ett värdnamn, misslyckas processen att validera och verifiera domänen.

### <a name="cause"></a>Orsak 

Det här problemet uppstår på något av följande skäl:

- Du har inte behörighet att lägga till ett värdnamn.

    **Lösningen**: Kontrollera prenumerationen administratör att se till att du har behörighet att lägga till ett värdnamn.
- Det gick inte att verifiera ditt ägarskap för domänen.

    **Lösningen**: Kontrollera om din CNAME-post eller en post är korrekt konfigurerade. Anpassade domäner att mappa till webb-app, skapa en CNAME-post eller en A-post. Om du vill använda rotdomänen måste du använda en och TXT-poster:

    |Posttyp|Värd|Peka på|
    |------|------|-----|
    |A|@|IP-adress för webbprogram|
    |TXT|@|< programnamn >. azurewebsites.net|
    |CNAME|www|< programnamn >. azurewebsites.net|

### <a name="dns-cannot-be-resolved"></a>Det går inte att matcha DNS

### <a name="symptom"></a>Symtom

Felmeddelandet ”DNS-posten kunde inte hittas” visas.

### <a name="cause"></a>Orsak
Det här problemet uppstår på något av följande skäl:

- Time to Live (TTL)-perioden inte har gått ut. Kontrollera DNS-konfiguration för domänen för att fastställa TTL-värdet och vänta tills perioden ska upphöra att gälla.
- DNS-konfigurationen är felaktig.

### <a name="solution"></a>Lösning
- Vänta tills 48 timmar innan det här problemet ska lösas av sig självt.
- Om du kan ändra TTL-inställningen i DNS-konfiguration, ändrar du värdet 5 minuter för att se om det löser problemet.
- Använd [WhatsmyDNS.net](https://www.whatsmydns.net/) att kontrollera att domänen som pekar på IP-adressen web app. Om det inte du konfigurera A-posten till rätt IP-adressen för webbappen.

### <a name="restore-a-deleted-domain"></a>Återställa en borttagen domän 

### <a name="symptom"></a>Symtom
Din domän visas inte längre i Azure-portalen.

### <a name="cause"></a>Orsak 
Domänen kan ha raderats av misstag av Prenumerationens ägare.

### <a name="solution"></a>Lösning
Om din domän har tagits bort mindre än sju dagar sedan, startat domänen inte har borttagningsprocessen. I det här fallet kan du köpa samma domän igen på Azure portal under samma prenumeration (du måste ange det exakta domännamnet i sökrutan). Du debiteras inte igen för den här domänen. Om domänen har tagits bort mer än sju dagar sedan, kontakta [Azure-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) för att få hjälp att återställa domänen.

### <a name="custom-domain-returns-404-or-site-inaccessible"></a>Anpassade domäner returnerar 404 eller platsen inte tillgänglig 

### <a name="symptom"></a>Symtom

När du bläddrar till platsen med hjälp av det anpassade domännamnet får följande felmeddelande:

**Fel 404-webbprogram hittades inte.**


### <a name="cause-and-solution"></a>Orsak och lösning

**Orsak 1** 

En CNAME-post eller en post saknas i den anpassade domänen som du har konfigurerat. 

**Lösning för orsak 1**

- Om du har lagt till en A-post, kontrollera att en TXT-post läggs också till. Mer information finns i [skapa det-a-post](./app-service-web-tutorial-custom-domain.md#create-the-a-record).
- Om du inte behöver använda rotdomänen för ditt webbprogram, rekommenderas att använda en CNAME-post i stället för en post.
- Använd inte en CNAME-post och en post för samma domän. Detta kan orsaka konflikter och förhindrar att domänen från att matcha. 

**Orsak 2** 

Webbläsaren kan fortfarande cachelagring den gamla IP-adressen för din domän. 

**Lösning för orsak 2**

Avmarkera webbläsaren. För Windows-enheter kan du köra kommandot `ipconfig /flushdns`. Använd [WhatsmyDNS.net](https://www.whatsmydns.net/) att kontrollera att domänen som pekar på IP-adressen web app. 

### <a name="unable-to-add-subdomain"></a>Det gick inte att lägga till en underdomän 

### <a name="symptom"></a>Symtom

Du kan inte lägga till ett nytt värdnamn ett webbprogram för att tilldela en underordnad domän.

### <a name="solution"></a>Lösning

- Kontrollera prenumerationen administratör att se till att du har behörighet att lägga till ett värdnamn i webbprogrammet.
- Om du behöver fler underordnade domäner, rekommenderar vi att du ändrar den domän som värd till Azure DNS. Du kan lägga till 500 värdnamn till ditt webbprogram med hjälp av Azure DNS. Mer information finns i [lägga till en underdomän](https://blogs.msdn.microsoft.com/waws/2014/10/01/mapping-a-custom-subdomain-to-an-azure-website/).











 


















