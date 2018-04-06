---
title: Konfigurera HTTPS på en anpassad domän i Azure Content Delivery Network | Microsoft Docs
description: Lär dig hur du aktiverar eller inaktiverar HTTPS för Azure CDN-slutpunkten med en anpassad domän.
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
ms.topic: article
ms.date: 03/22/2018
ms.author: rli; v-deasim
ms.openlocfilehash: 554ae4c19d1a3d35075ad174549a62a20329e5fa
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="configure-https-on-an-azure-content-delivery-network-custom-domain"></a>Konfigurera HTTPS på en anpassad domän för innehållsleveransnätverk i Azure

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Microsoft stöder HTTPS-protokollet för anpassade domäner på Azure Content Delivery Network (CDN). Med stöd för HTTPS-domänen, kan du leverera skyddat innehåll via SSL genom att använda ditt eget domännamn för att förbättra säkerheten för data under överföringen. Arbetsflöde för att aktivera HTTPS för den anpassade domänen är förenklad via en enda klickning aktivering och fullständig certifikathantering alla med utan extra kostnad.

Det är viktigt att säkerställa sekretess och dataintegriteten för ditt webbprogram känsliga data när den är i överföringen. Genom att använda HTTPS-protokollet kan du se till att dina känsliga data krypteras när de skickas över internet. Det ger litar på autentisering och skyddar ditt webbprogram från attacker. Som standard stöder Azure CDN HTTPS på en CDN-slutpunkt. Till exempel om du skapar en CDN-slutpunkt från Azure CDN (till exempel https:\//contoso.azureedge.net), aktiveras automatiskt för HTTPS. Dessutom med stöd för anpassade domäner HTTPS, du kan också aktivera säker leverans för en anpassad domän (till exempel https:\//www.contoso.com). 

Några viktiga attribut för HTTPS-funktionen är:

- Utan extra kostnad: det finns inga kostnader för certifikatanskaffningen eller förnyelse och utan extra kostnad för HTTPS-trafik. Du betalar bara för GB utgående trafik från CDN.

- Enkel aktivering: enkelklickning etablering är tillgänglig från den [Azure-portalen](https://portal.azure.com). Du kan också använda REST API eller andra verktyg för utvecklare för att aktivera funktionen.

- Slutföra certifikathantering: alla certifikat inköp och hanteras åt dig. Certifikaten etableras automatiskt och förnyas innan upphör att gälla, vilket tar bort riskerna med avbrott i tjänsten på grund av ett certifikat upphör att gälla.

>[!NOTE] 
>Innan du aktiverar stöd för HTTPS, du måste har upprättat en [Azure CDN domänen](./cdn-map-content-to-custom-domain.md).

## <a name="enabling-https"></a>Aktivera HTTPS

Följ dessa steg om du vill aktivera HTTPS på en anpassad domän:

### <a name="step-1-enable-the-feature"></a>Steg 1: Aktivera funktionen 

1. I den [Azure-portalen](https://portal.azure.com), bläddra till din **Azure CDN Standard från Verizon** eller **Azure CDN Premium från Verizon** CDN-profilen.

2. Klicka på den slutpunkt som innehåller din anpassade domän i listan över slutpunkter.

3. Klicka på den anpassade domänen som du vill aktivera HTTPS.

    ![Lista över anpassade domäner](./media/cdn-custom-ssl/cdn-custom-domain.png)

4. Klicka på **på** om du vill aktivera HTTPS, klicka på **tillämpa**.

    ![Anpassad domän för HTTPS-status](./media/cdn-custom-ssl/cdn-enable-custom-ssl.png)


### <a name="step-2-validate-domain"></a>Steg 2: Validera domän

>[!NOTE]
>Om du har en certifikatet myndigheten auktorisering (CAA)-post hos din DNS-leverantör, måste den innehålla DigiCert som en giltig Certifikatutfärdare. En post för CAA kan domän ägare att ange med respektive DNS-leverantör som certifikatutfärdare har behörighet att utfärda certifikat för sin domän. Om en Certifikatutfärdare tar emot en order för ett certifikat för en domän som har en CAA-post och Certifikatutfärdaren har inte listats som en auktoriserad utfärdare, är det förbjudet från utfärda certifikatet till domänen eller underdomänen. Information om hur du hanterar CAA poster finns [hantera CAA poster](https://support.dnsimple.com/articles/manage-caa-record/). En post verktyget CAA finns [CAA post Helper](https://sslmate.com/caa/).

#### <a name="custom-domain-is-mapped-to-cdn-endpoint"></a>Domänen är mappad till CDN-slutpunkten

När du har lagt till en anpassad domän i din slutpunkt, skapa en CNAME-post i DNS-tabellen för domänregistrator för att mappa till CDN-slutpunktens värdnamn. Om posten CNAME fortfarande finns och inte innehåller underdomänen cdnverify används DigiCert-certifikatutfärdare (CA) för att verifiera ägarskapet för den anpassade domänen. 

CNAME-post måste vara i följande format, där *namn* är ditt domännamn och *värdet* är CDN-slutpunktens värdnamn:

| namn            | Typ  | Värde                 |
|-----------------|-------|-----------------------|
| www.contoso.com | CNAME | contoso.azureedge.net |

Mer information om CNAME-poster finns [skapa CNAME DNS-post](https://docs.microsoft.com/en-us/azure/cdn/cdn-map-content-to-custom-domain#step-2-create-the-cname-dns-records).

Om CNAME-post är i rätt format, DigiCert verifierar ditt domännamn och automatiskt läggs till alternativa namn på CERTIFIKATMOTTAGARE certifikatet. DigitCert Skicka inte ett e-postmeddelandet och du behöver inte godkänna din förfrågan. Certifikatet är giltigt i ett år och automatisk-förnyas innan den upphör. Gå vidare till [steg3: vänta tills spridningen](#step-3-wait-for-propagation). 

#### <a name="cname-record-is-not-mapped-to-cdn-endpoint"></a>CNAME-post är inte mappad till CDN-slutpunkten

Om posten CNAME-post för slutpunkten finns inte eller innehåller underdomänen cdnverify, följer du instruktionerna i det här steget.

När du aktiverar HTTPS på en anpassad domän, DigiCert-certifikatutfärdare (CA) verifierar ägarskap för domänen genom att kontakta dess registrant enligt domänens [WHOIS](http://whois.domaintools.com/) registrant information. Kontakta görs via den e-postadressen (som standard) eller telefonnummer som anges i WHOIS-registrering. Du måste slutföra verifiering av domän innan HTTPS kommer att vara aktiv på den anpassade domänen. Du har sex arbetsdagar att godkänna domänen. Begäranden som inte är godkända inom sex arbetsdagar avbryts automatiskt. 

![WHOIS-post](./media/cdn-custom-ssl/whois-record.png)

DigiCert skickar också ett e-postmeddelandet till ytterligare e-postadresser. Om WHOIS registrant information är privat, kontrollerar du att du kan godkänna direkt från en av följande adresser:

admin@&lt;your-domain-name.com&gt;  
administrator@&lt;your-domain-name.com&gt;  
webmaster@&lt;your-domain-name.com&gt;  
hostmaster@&lt;your-domain-name.com&gt;  
postmaster@&lt;your-domain-name.com&gt;  

Du bör få ett e-postmeddelande om några minuter som liknar följande exempel visas där du uppmanas att godkänna begäran. Om du använder ett skräppostfilter, lägga till admin@digicert.com till dess godkända. Om du inte får ett e-postmeddelande inom 24 timmar, kontaktar du Microsoft support.
    
![E-post för domänen-validering](./media/cdn-custom-ssl/domain-validation-email.png)

När du klickar på länken godkännande, dirigeras till formatet online godkännande: 
    
![Domän valideringsformulär](./media/cdn-custom-ssl/domain-validation-form.png)

Följ instruktionerna i formuläret. du har två alternativ för verifiering:

- Du kan godkänna alla framtida beställningar via samma konto för samma rotdomän; till exempel contoso.com. Den här metoden rekommenderas om du vill lägga till ytterligare anpassade domäner i samma rot-domänen.

- Du kan godkänna bara specifika värdnamnet som används i denna begäran. Ytterligare godkännande krävs för efterföljande förfrågningar.

Lägger till ditt domännamn till SAN-certifikat efter godkännande och DigiCert. Certifikatet är giltigt i ett år och automatisk-förnyas innan den har upphört att gälla.

### <a name="step-3-wait-for-propagation"></a>Steg 3: Vänta tills spridning

Det kan ta upp till 6 – 8 timmar för anpassade domäner HTTPS-funktionen ska aktiveras när domännamnet har verifierats. När processen är klar, den anpassade HTTPS i Azure-portalen är status **aktiverad** och fyra åtgärden stegen i dialogrutan för anpassade domäner har markerats som slutförd. Den anpassade domänen är nu klar att använda HTTPS.

![Aktivera HTTPS dialogrutan](./media/cdn-custom-ssl/cdn-enable-custom-ssl-complete.png)

### <a name="operation-progress"></a>Åtgärden pågår

I följande tabell visas förloppet igen som händer när du aktiverar HTTPS. När du har aktiverat HTTPS visas fyra åtgärden steg i dialogrutan för anpassade domäner. Eftersom varje steg blir aktiv ytterligare substep informationen som visas under steget när den. Inte alla de här stegen utförs. När ett steg har slutförts visas en grön bockmarkering bredvid den. 

| Åtgärden steg | Information om substep | 
| --- | --- |
| 1 skicka begäran | Skickar begäran |
| | HTTPS-förfrågan skickas. |
| | Din HTTPS-förfrågan har skickats. |
| 2 verifiering av domän | Vi har skickat ett e-postmeddelande som ber dig att validera domän ägare. Väntar på att bekräfta. ** |
| | Domänägarskapet har verifierats och godkänts. |
| | Begäran om domänen ägarskap upphört att gälla (kunden förmodligen inte svarar inom 6 dagar). HTTPS aktiveras inte på din domän. * |
| | Domän ägarskap begäran avvisades av kunden. HTTPS aktiveras inte på din domän. * |
| 3 tilldelning av certifikat | Certifikatutfärdaren håller på att utfärda det certifikat som krävs för att aktivera HTTPS på din domän. |
| | Certifikatet har utfärdats och distribueras just nu till CDN-nätverk. Detta kan ta upp till sex timmar. |
| | Certifikatet har distribuerats till CDN-nätverket. |
| 4 Slutför | HTTPS har aktiverats på domänen. |

\* Det här meddelandet visas inte om ett fel har uppstått. 

\** Det här meddelandet visas inte om du har en CNAME-post för den anpassade domänen som pekar direkt till CDN-slutpunktens värdnamn.

Om ett fel inträffar innan begäran har skickats, visas följande felmeddelande:

<code>
We encountered an unexpected error while processing your HTTPS request. Please try again and contact support if the issue persists.
</code>

## <a name="disabling-https"></a>Inaktivera HTTPS

När du har aktiverat HTTPS på en anpassad domän, kan du inaktivera det senare. Följ dessa steg om du vill inaktivera HTTPS:

### <a name="step-1-disable-the-feature"></a>Steg 1: Inaktivera funktionen 

1. I den [Azure-portalen](https://portal.azure.com), bläddra till din **Azure CDN Standard från Verizon** eller **Azure CDN Premium från Verizon** CDN-profilen.

2. Klicka på den slutpunkt som innehåller din anpassade domän i listan över slutpunkter.

3. Klicka på den anpassade domänen som du vill inaktivera HTTPS.

    ![Lista över anpassade domäner](./media/cdn-custom-ssl/cdn-custom-domain-HTTPS-enabled.png)

4. Klicka på **av** för att inaktivera HTTPS, klicka på **tillämpa**.

    ![Dialogrutan för anpassade HTTPS](./media/cdn-custom-ssl/cdn-disable-custom-ssl.png)

### <a name="step-2-wait-for-propagation"></a>Steg 2: Vänta tills spridning

Det kan ta upp till 6 – 8 timmar innan den börjar gälla när den anpassade domänen HTTPS-funktionen är inaktiverad. När processen är klar, den anpassade HTTPS i Azure-portalen är status **inaktiverad** och tre åtgärden stegen i dialogrutan för anpassade domäner har markerats som slutförd. Den anpassade domänen kan inte längre använda HTTPS.

![Inaktivera HTTPS dialogrutan](./media/cdn-custom-ssl/cdn-disable-custom-ssl-complete.png)

### <a name="operation-progress"></a>Åtgärden pågår

I följande tabell visas förloppet igen som händer när du inaktiverar HTTPS. När du inaktiverar HTTPS visas tre åtgärden steg i dialogrutan för anpassad domän. Eftersom varje steg blir aktiv visas ytterligare information under steget. När ett steg har slutförts visas en grön bockmarkering bredvid den. 

| Åtgärden pågår | Åtgärdsinformation | 
| --- | --- |
| 1 skicka begäran | Din förfrågan skickas |
| 2 borttagning av certifikat | Tar bort certifikat |
| 3 slutfört | Certifikatet har tagits bort |

## <a name="frequently-asked-questions"></a>Vanliga frågor

1. *Vem är certifikat-providern och vilken typ av certifikat används?*

    Microsoft använder ett alternativt namn på CERTIFIKATMOTTAGARE certifikat som tillhandahålls av DigiCert. Ett SAN-certifikat kan skydda flera fullständigt kvalificerade domännamn med ett certifikat.

2. *Kan jag använda mitt dedikerade certifikat?*
    
    För närvarande inte, men det är på Översikt.

3. *Vad händer om jag får inte domänen e-postmeddelandet från DigiCert?*

    Kontakta Microsoft support om du inte får ett e-postmeddelande inom 24 timmar. Om du har en CNAME-post för den anpassade domänen som pekar direkt på slutpunktens värdnamn (och du inte använder underdomännamnet cdnverify), får du inte en domän e-postmeddelandet. Validering sker automatiskt.

4. *Använder ett SAN-certifikat som är mindre säker än ett dedikerat certifikat?*
    
    Ett SAN-certifikat följer samma kryptering och säkerhet standarder som ett dedikerat certifikat. Alla utfärdade SSL-certifikat använder SHA-256 för förbättrad säkerhet.

5. *Kan jag använda en anpassad domän HTTPS med Azure CDN från Akamai?*

    Den här funktionen är för närvarande bara tillgänglig med Azure CDN från Verizon. Microsoft arbetar med stöd för den här funktionen med Azure CDN från Akamai under de kommande månaderna.

6. *Behöver jag en certifikatet myndigheten auktorisering post med DNS-leverantör?*

    Nej, en post för certifikatet myndigheten auktorisering krävs inte för närvarande. Om du har en, måste den innehålla DigiCert som en giltig Certifikatutfärdare.


## <a name="next-steps"></a>Nästa steg

- Lär dig hur du ställer in en [domänen på Azure CDN-slutpunkten](./cdn-map-content-to-custom-domain.md)


