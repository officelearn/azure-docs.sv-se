---
title: Självstudiekurs – Konfigurera HTTPS på en anpassad Azure CDN-domän | Microsoft Docs
description: I den här kursen får du lära dig hur du aktiverar och inaktiverar HTTPS på en anpassad domän på en Azure CDN-slutpunkt.
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.assetid: 10337468-7015-4598-9586-0b66591d939b
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/12/2018
ms.author: rli
ms.custom: mvc
ms.openlocfilehash: a8f2da5a68552c35a55a7bbb764afc7b36af6962
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
---
# <a name="tutorial-configure-https-on-an-azure-cdn-custom-domain"></a>Självstudiekurs: Konfigurera HTTPS på en anpassad Azure CDN-domän

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Den här kursen visar hur du aktiverar HTTP-protokollet för en anpassad domän som är associerad med en Azure CDN-slutpunkt (Content Delivery Network). Med HTTPS-protokollet på din anpassade domän (till exempel https:\//www.contoso.com) ser du till att dina känsliga data levereras på ett säkert sätt via SSL-kryptering när de skickas över Internet. HTTPS ger förtroende, autentisering och skyddar dina webbprogram mot attacker. Arbetsflödet för att aktivera HTTPS har förenklats med enklicksaktivering och fullständig certifikathantering, utan extra kostnad.

Azure CDN stöder HTTPS i CDN-slutpunktens värdnamn som standard. Om du skapar en CDN-slutpunkt som till exempel https:\//contoso.azureedge.net aktiveras HTTPS automatiskt.  

Några viktiga punkter för HTTPS-funktionen:

- Ingen extra kostnad: det kostar ingenting att förvärva eller förnya certifikat och det tillkommer ingen extra kostnad för HTTPS-trafik. Du betalar bara för utgående trafik (GB) från CDN.

- Enkel aktivering: enklicksetablering är tillgänglig från [Azure Portal](https://portal.azure.com). Du kan också aktivera funktionen med REST API eller andra utvecklingsverktyg.

- Komplett certifikathantering: all anskaffning och hantering av certifikat hanteras åt dig. Certifikaten etableras automatiskt och förnyas innan de upphör att gälla, vilket tar bort risken för avbrott i tjänsten på grund av ett certifikat upphör att gälla.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> - Aktivera HTTPS-protokollet på en anpassad domän
> - Verifiera domänen
> - Inaktivera HTTPS-protokollet på en anpassad domän

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du kan slutföra stegen i den här kursen måste du först skapa en CDN-profil och minst en CDN-slutpunkt. Mer information finns i [Snabbstart: Skapa en Azure CDN-profil och CDN-slutpunkt](cdn-create-new-endpoint.md).

Dessutom måste du associera en anpassad Azure CDN-domän på CDN-slutpunkten. Mer information finns i [Självstudiekurs: Lägga till en anpassad domän i Azure CDN-slutpunkten](cdn-map-content-to-custom-domain.md)

## <a name="enable-the-https-feature"></a>Aktivera HTTPS-funktionen

Följ dessa steg om du vill aktivera HTTPS på en anpassad domän:

1. I [Azure Portal](https://portal.azure.com) navigerar du till CDN-profilen **Azure CDN Standard från Verizon** eller **Azure CDN Premium från Verizon**.

2. I listan med CDN-slutpunkter väljer du slutpunkten som innehåller din anpassade domän.

    ![Lista med slutpunkter](./media/cdn-custom-ssl/cdn-select-custom-domain-endpoint.png)

    Sidan **Slutpunkt** visas.

3. I listan med anpassade domäner väljer du den anpassade domänen som du vill aktivera HTTPS för.

    ![Lista med anpassade domäner](./media/cdn-custom-ssl/cdn-custom-domain.png)

    Sidan **Anpassad domän** visas.

4. Välj **På** om du vill aktivera HTTPS och välj sedan **Använd**.

    ![HTTPS-status för anpassad domän](./media/cdn-custom-ssl/cdn-enable-custom-ssl.png)


## <a name="validate-the-domain"></a>Verifiera domänen

Om du redan har en anpassad domän i bruk som är mappad till din anpassade slutpunkt med en CNAME-post, fortsätter du till  
[Den anpassade domänen har mappats till CDN-slutpunkten](#custom-domain-is-mapped-to-your-cdn-endpoint-by-a-cname-record). Om CNAME-posten för slutpunkten inte längre finns eller om den innehåller cdnverify-underdomänen fortsätter du i stället till [Den anpassade domänen har inte mappats till CDN-slutpunkten](#custom-domain-is-not-mapped-to-your-cdn-endpoint).

### <a name="custom-domain-is-mapped-to-your-cdn-endpoint-by-a-cname-record"></a>Den anpassade domänen har mappats till CDN-slutpunkten med en CNAME-post

När du lade till en anpassad domän till din slutpunkt skapade du en CNAME-post i din domänregistrators DNS-tabell för att mappa den till CDN-slutpunktens värdnamn. Om CNAME-posten fortfarande finns kvar och inte innehåller cdnverify-underdomänen använder DigiCert-certifikatutfärdaren den för att verifiera ägarskapet för din anpassade domän. 

CNAME-posten ska ha följande format, där *Namn* är namnet på din anpassade domän och *Värde* är CDN-slutpunktens värdnamn:

| Namn            | Typ  | Värde                 |
|-----------------|-------|-----------------------|
| www.contoso.com | CNAME | contoso.azureedge.net |

Mer information om CNAME-poster finns i [Skapa CNAME DNS-posten](https://docs.microsoft.com/en-us/azure/cdn/cdn-map-content-to-custom-domain#create-the-cname-dns-records).

Om din CNAME-post har rätt format verifierar DigiCert automatiskt det anpassade domännamnet och lägger till det i SAN-certifikatet (alternativt namn för certifikatmottagare). DigitCert skickar ingen bekräftelse via e-post och du behöver inte godkänna din begäran. Certifikatet är giltigt i ett år och förnyas automatiskt innan det upphör. Gå vidare till [Vänta på spridning](#wait-for-propagation). 

Den automatiska verifieringen tar vanligtvis några minuter. Öppna ett supportärende om domänen inte har verifierats inom en timme.

>[!NOTE]
>Om du har en CAA-post (Certificate Authority Authorization) hos DNS-providern måste den innehålla DigiCert som giltig certifikatutfärdare. En CAA-post gör att domänägare kan ange med sina DNS-provider vilka certifikatutfärdare som är auktoriserade att utfärda certifikat för deras domän. Om en certifikatutfärdare mottar en order för ett certifikat för en domän som har en CAA-post och den certifikatutfärdaren inte finns med i listan över auktoriserade utfärdare hindras certifikatufärdaren att utfärda certifikatet till den domänen eller underdomänen. Information om hur du hanterar CAA poster finns i [Hantera CAA-poster](https://support.dnsimple.com/articles/manage-caa-record/). Hjälpverktyg för CAA-poster finns i [CAA Record Helper](https://sslmate.com/caa/).

### <a name="custom-domain-is-not-mapped-to-your-cdn-endpoint"></a>Den anpassade domänen har inte mappats till CDN-slutpunkten

Om CNAME-posten för slutpunkten inte finns längre eller om den innehåller cdnverify-underdomänen följer du instruktionerna i det här steget.

När du har aktiverat HTTPS på din anpassade domän verifierar DigiCert-certifikatutfärdaren ägarskapet för din domän genom att kontakta registranten enligt domänens [WHOIS](http://whois.domaintools.com/)-registrantinformation. Kontakten sker via e-postadressen (som standard) eller telefonnumret som står i WHOIS-registreringen. Du måste slutföra domänverifieringen för att HTTPS ska aktiveras på din anpassade domän. Du har sex arbetsdagar på dig att godkänna domänen. Begäranden som inte godkänns inom sex arbetsdagar avbryts automatiskt. 

![WHOIS-post](./media/cdn-custom-ssl/whois-record.png)

DigiCert skickar också ett bekräftelsemeddelande till ytterligare e-postadresser. Om WHOIS-registrantinformationen är privat, kontrollerar du att du kan godkänna direkt från en av följande adresser:

admin@&lt;dittdomännamn.com&gt;  
administrator@&lt;dittdomännamn.com&gt;  
webmaster@&lt;dittdomännamn.com&gt;  
hostmaster@&lt;dittdomännamn.com&gt;  
postmaster@&lt;dittdomännamn.com&gt;  

Inom ett par minuter får du ett e-postmeddelande som ser ut ungefär som i följande exempel och som ber dig godkänna begäran. Om du använder ett skräppostfilter lägger du till admin@digicert.com i listan med tillåtna adresser. Kontakta Microsoft-supporten om du inte får ett e-postmeddelande inom 24 timmar.
    
![Domänverifieringsmeddelande](./media/cdn-custom-ssl/domain-validation-email.png)

När du klickar på godkännandelänken dirigeras du till följande formulär för godkännande online: 
    
![Domänverifieringsformulär](./media/cdn-custom-ssl/domain-validation-form.png)

Följ instruktionerna i formuläret. Du har två verifieringsalternativ:

- Du kan godkänna alla framtida order som placerats via samma konto för samma rotdomän, till exempel contoso.com. Den här metoden rekommenderas om du tänker lägga till ytterligare anpassade domäner i samma rotdomän.

- Du kan bara godkänna det specifika värdnamn som används i den här begäran. Ytterligare godkännande krävs för efterföljande begäranden.

Efter godkännandet lägger DigiCert till ditt anpassade domännamn i SAN-certifikatet. Certifikatet är giltigt i ett år och förnyas automatiskt innan det upphör.

## <a name="wait-for-propagation"></a>Vänta på spridning

När domännamnet har verifierats kan det ta upp till 6–8 timmar innan HTTPS-funktionen för den anpassade domänen aktiveras. När processen är klar visas HTTPS-status **Aktiverad** i Azure Portal och de fyra åtgärdsstegen i dialogrutan för anpassad domän markeras som slutförda. Den anpassade domänen är nu klar att använda HTTPS.

![Dialogrutan Aktivera HTTPS](./media/cdn-custom-ssl/cdn-enable-custom-ssl-complete.png)

### <a name="operation-progress"></a>Åtgärdsförlopp

I följande tabell visas åtgärdsförloppet när du aktiverar HTTPS. När du har aktiverat HTTPS visas de fyra åtgärdsstegen i dialogrutan Anpassad domän. När nästa steg i förloppet aktiveras visas ytterligare information om dess understeg. Alla understeg utförs inte. När ett steg har slutförts visas en grön bock bredvid steget. 

| Åtgärdssteg | Information om åtgärdsundersteg | 
| --- | --- |
| 1 Skicka begäran | Begäran skickas |
| | Din HTTPS-begäran skickas. |
| | Din HTTPS-begäran har skickats. |
| 2 Domänverifiering | Domänen verifieras automatiskt om CNAME har mappats till CDN-slutpunkten. I annat fall skickas en verifieringsbegäran till den e-postadress som står angiven i domänens registreringspost (WHOIS-registrant). Verifiera domänen så snart som möjligt. |
| | Domänägarskapet har verifierats och godkänts. |
| | Begäran om verifiering av domänägarskap har gått ut (kunden svarade troligtvis inte inom 6 dagar). HTTPS aktiveras inte på din domän. * |
| | Begäran om verifiering av domänägarskap avvisades av kunden. HTTPS aktiveras inte på din domän. * |
| 3 Etablering av certifikat | Certifikatutfärdaren håller på att utfärda det certifikat som krävs för att aktivera HTTPS på din domän. |
| | Certifikatet har utfärdats och håller på att distribueras till CDN-nätverket. Det kan ta upp till 6 timmar. |
| | Certifikatet har distribuerats till CDN-nätverket. |
| 4 Slutfört | HTTPS har aktiverats på din domän. |

\* Det här meddelandet visas bara om ett fel har uppstått. 

Om det uppstår ett fel innan begäran har skickats visas följande felmeddelande:

<code>
We encountered an unexpected error while processing your HTTPS request. Please try again and contact support if the issue persists.
</code>

## <a name="clean-up-resources---disable-https"></a>Rensa resurser – inaktivera HTTPS

I föregående steg aktiverade du HTTPS-protokollet på en anpassad domän. Om du inte längre vill använda HTTPS för din anpassade domän kan du inaktivera HTTPS på följande sätt:

### <a name="disable-the-https-feature"></a>Inaktivera HTTPS-funktionen 

1. I [Azure Portal](https://portal.azure.com) navigerar du till CDN-profilen **Azure CDN Standard från Verizon** eller **Azure CDN Premium från Verizon**.

2. I listan med slutpunkter klickar du på slutpunkten som innehåller din anpassade domän.

3. Klicka på den anpassade domänen som du vill inaktivera HTTPS för.

    ![Lista med anpassade domäner](./media/cdn-custom-ssl/cdn-custom-domain-HTTPS-enabled.png)

4. Klicka på **Av** för att inaktivera HTTPS och klicka sedan på **Använd**.

    ![Dialogrutan Anpassad HTTPS](./media/cdn-custom-ssl/cdn-disable-custom-ssl.png)

### <a name="wait-for-propagation"></a>Vänta på spridning

När HTTPS-funktionen för den anpassade domänen har inaktiverats kan det ta upp till 6–8 timmar innan ändringen börjar gälla. När processen är klar visas HTTPS-status **Inaktiverad** i Azure Portal och de tre åtgärdsstegen i dialogrutan Anpassad domän är markerade som slutförda. Den anpassade domänen använder inte längre HTTPS.

![Dialogrutan Inaktivera HTTPS](./media/cdn-custom-ssl/cdn-disable-custom-ssl-complete.png)

#### <a name="operation-progress"></a>Åtgärdsförlopp

I följande tabell visas åtgärdsförloppet när du inaktiverar HTTPS. När du har inaktiverat HTTPS visas de tre åtgärdsstegen i dialogrutan Anpassad domän. När nästa steg i förloppet aktiveras visas ytterligare information under steget. När ett steg har slutförts visas en grön bock bredvid steget. 

| Åtgärdsförlopp | Åtgärdsinformation | 
| --- | --- |
| 1 Skicka begäran | Begäran skickas |
| 2 Avetablering av certifikat | Tar bort certifikat |
| 3 Slutfört | Certifikatet har tagits bort |

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

1. *Vem är certifikatleverantör och vilken typ av certifikat används?*

    Microsoft använder ett SAN-certifikat (alternativt namn på certifikatmottagare) som tillhandahålls av DigiCert. Ett SAN-certifikat kan skydda flera fullständigt kvalificerade domännamn med ett certifikat.

2. *Kan jag använda mitt dedikerade certifikat?*
    
    Inte för närvarande, men det ingår i vår plan.

3. *Vad händer om jag inte får domänverifieringsmeddelandet från DigiCert?*

    Om du har en CNAME-post för din anpassade domän som pekar direkt på slutpunktens värdnamn (och du inte använder cdnverify-underdomännamnet) får du inget domänverifieringsmeddelande. Verifieringen sker i så fall automatiskt. Om du inte har en CNAME-post och inte har fått något e-postmeddelande inom 24 timmar kontaktar du Microsoft-supporten.

4. *Är det mindre säkert att använda ett SAN-certifikat än att använda ett dedikerat certifikat?*
    
    Ett SAN-certifikat följer samma standarder för kryptering och säkerhet som ett dedikerat certifikat. Alla utfärdade SSL-certifikat använder SHA-256 för förbättrad serversäkerhet.

5. *Kan jag använda HTTPS för en anpassad domän med Azure CDN från Akamai?*

    Den här funktionen är för närvarande bara tillgänglig med Azure CDN från Verizon. Microsoft arbetar med att lägga till stöd för den här funktionen med Azure CDN från Akamai inom de närmsta månaderna.

6. *Behöver jag en CAA-post (Certificate Authority Authorization) med DNS-leverantören?*

    Nej, en CAA-post krävs inte för närvarande. Men om du har en sådan måste den innehålla DigiCert som en giltig certifikatutfärdare.


## <a name="next-steps"></a>Nästa steg

Vad du lärt dig:

> [!div class="checklist"]
> - Aktivera HTTPS-protokollet på en anpassad domän
> - Verifiera domänen
> - Inaktivera HTTPS-protokollet på en anpassad domän

Gå vidare till nästa kurs om du vill lära dig hur du konfigurerar cachelagring på en CDN-slutpunkt.

> [!div class="nextstepaction"]
> [Kontrollera funktionssättet för Azure CDN-cachelagring med cachelagringsregler](cdn-caching-rules.md)

