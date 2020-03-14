---
title: Självstudiekurs – Konfigurera HTTPS på en anpassad Azure CDN-domän | Microsoft Docs
description: I den här kursen får du lära dig hur du aktiverar och inaktiverar HTTPS på en anpassad domän på en Azure CDN-slutpunkt.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 10337468-7015-4598-9586-0b66591d939b
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/1/2019
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: f1af388d1f8b9542d196a53cc6c143f9b48e6d5a
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79238722"
---
# <a name="tutorial-configure-https-on-an-azure-cdn-custom-domain"></a>Självstudiekurs: Konfigurera HTTPS på en anpassad Azure CDN-domän

Den här självstudien visar hur du aktiverar HTTPS-protokollet för en anpassad domän som är associerad med en Azure CDN-slutpunkt. Med HTTPS-protokollet på din anpassade domän (till exempel https:\//www.contoso.com) ser du till att dina känsliga data levereras på ett säkert sätt via TLS-/SSL-kryptering när de skickas över Internet. När webbläsaren är ansluten till en webbplats via HTTPS valideras webbplatsens säkerhetscertifikat och verifierar att det är utfärdat av en giltig certifikatutfärdare. Den här processen ger trygghet och skyddar dina webbprogram mot attacker.

Azure CDN stöder HTTPS i CDN-slutpunktens värdnamn som standard. Om du skapar en CDN-slutpunkt som till exempel https:\//contoso.azureedge.net aktiveras HTTPS automatiskt.  

Några viktiga attribut i den anpassade HTTPS-funktionen är:

- Ingen extra kostnad: det kostar ingenting att förvärva eller förnya certifikat och det tillkommer ingen extra kostnad för HTTPS-trafik. Du betalar bara för utgående trafik (GB) från CDN.

- Enkel aktivering: enklicksetablering är tillgänglig från [Azure Portal](https://portal.azure.com). Du kan också aktivera funktionen med REST API eller andra utvecklingsverktyg.

- Komplett certifikathantering finns tillgänglig: All anskaffning och hantering av certifikat hanteras åt dig. Certifikaten etableras automatiskt och förnyas innan de upphör att gälla, vilket tar bort risken för avbrott i tjänsten på grund av ett certifikat upphör att gälla.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> - Aktivera HTTPS-protokollet på din anpassade domän.
> - Använda ett CDN-hanterat certifikat 
> - Använda ditt eget certifikat
> - Verifiera domänen
> - Inaktivera HTTPS-protokollet på din anpassade domän.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)] 

Innan du kan slutföra stegen i den här kursen måste du först skapa en CDN-profil och minst en CDN-slutpunkt. Mer information finns i [Snabbstart: Skapa en Azure CDN-profil och CDN-slutpunkt](cdn-create-new-endpoint.md).

Dessutom måste du associera en anpassad Azure CDN-domän på CDN-slutpunkten. Mer information finns i [Självstudier: Lägg till en anpassad domän i Azure CDN slut punkten](cdn-map-content-to-custom-domain.md).

> [!IMPORTANT]
> CDN-hanterade certifikat är inte tillgängliga för rot-eller Apex-domäner. Om din Azure CDN anpassade domän är en rot-eller Apex-domän måste du använda funktionen ta med din egen certifikat. 
>

---

## <a name="ssl-certificates"></a>SSL-certifikat
Om du vill aktivera protokollet HTTPS för säker leverans av innehåll i en anpassad Azure CDN-domän måste du använda ett SSL-certifikat. Du kan välja att använda ett certifikat som hanteras av Azure CDN eller använda ett eget certifikat.


# <a name="option-1-default-enable-https-with-a-cdn-managed-certificate"></a>[Alternativ 1 (standard): Aktivera HTTPS med ett CDN-hanterat certifikat](#tab/option-1-default-enable-https-with-a-cdn-managed-certificate)

När du använder ett CDN-hanterat certifikat kan du aktivera HTTPS med några få klick. Azure CDN hanterar alla certifikathanteringsuppgifter, till exempel anskaffning och förnyelse. När du har aktiverat funktionen startar processen omedelbart. Om den anpassade domänen redan har mappats till CDN-slutpunkten, krävs ingen ytterligare åtgärd. Azure CDN behandlar stegen och slutför din begäran automatiskt. Men om din anpassade domän mappas någon annanstans, måste du använda e-post för att verifiera att du äger domänen.

Följ dessa steg om du vill aktivera HTTPS på en anpassad domän:

1. Gå till [Azure Portal](https://portal.azure.com) för att hitta ett certifikat som hanteras av din Azure CDN. Sök efter och välj **CDN-profiler**. 

2. Välj din **Azure CDN Standard från Microsoft**, **Azure CDN Standard från Akamai**, **Azure CDN Standard från Verizon**eller **Azure CDN Premium från Verizon** -profilen.

3. I listan med CDN-slutpunkter väljer du slutpunkten som innehåller din anpassade domän.

    ![Lista med slutpunkter](./media/cdn-custom-ssl/cdn-select-custom-domain-endpoint.png)

    Sidan **Slutpunkt** visas.

4. I listan med anpassade domäner väljer du den anpassade domänen som du vill aktivera HTTPS för.

    ![Lista med anpassade domäner](./media/cdn-custom-ssl/cdn-custom-domain.png)

    Sidan **Anpassad domän** visas.

5. Under certifikathanteringstyp väljer du **CDN-hanterat**.

6. Välj **På** för att aktivera HTTPS.

    ![HTTPS-status för anpassad domän](./media/cdn-custom-ssl/cdn-select-cdn-managed-certificate.png)

7. Fortsätt och [verifiera domänen](#validate-the-domain).


# <a name="option-2-enable-https-with-your-own-certificate"></a>[Alternativ 2: Aktivera HTTPS med ditt eget certifikat](#tab/option-2-enable-https-with-your-own-certificate)

> [!IMPORTANT]
> Det här alternativet är endast tillgängligt med **Azure CDN från Microsoft** och **Azure CDN från Verizon** -profiler. 
>
 
Du kan använda ditt eget certifikat för att aktivera HTTPS. Detta görs via en integrering med Azure Key Vault där du kan lagra certifikaten säkert. Azure CDN använder denna säkerhetsmekanism för att hämta certifikatet, och det krävs några ytterligare steg. När du skapar ett SSL-certifikat måste du skapa det med en tillåten certifikatutfärdare (CA). Om du använder en icke-tillåten certifikatutfärdare kan din begäran avvisas. En lista över tillåtna ca: er finns i [tillåtna certifikat utfärdare för att aktivera anpassad https på Azure CDN](cdn-troubleshoot-allowed-ca.md). För **Azure CDN från Verizon**godkänns alla giltiga ca: er. 

### <a name="prepare-your-azure-key-vault-account-and-certificate"></a>Förbered ditt Azure Key Vault-konto och certifikat
 
1. Azure Key Vault: Du måste ha ett aktivt Azure Key Vault-konto under samma prenumeration som Azure CDN-profilen och CDN-slutpunkterna där du vill aktivera anpassad HTTPS. Skapa ett Azure Key Vault-konto om du inte redan har ett.
 
2. Azure Key Vault-certifikat: Om du redan har ett certifikat kan du ladda upp det direkt till ditt Azure Key Vault-konto. Du kan också skapa ett nytt certifikat direkt via Azure Key Vault från en av partnercertifikatutfärdarna som Azure Key Vault integreras med. 

### <a name="register-azure-cdn"></a>Registrera Azure CDN

Registrera Azure CDN som en app i Azure Active Directory via PowerShell.

1. Om det behövs installerar du [Azure PowerShell](/powershell/azure/install-az-ps) på den lokala datorn.

2. Kör följande kommando i PowerShell:

     `New-AzADServicePrincipal -ApplicationId "205478c0-bd83-4e1b-a9d6-db63a3e1e1c8"`

    ![Registrera Azure CDN i PowerShell](./media/cdn-custom-ssl/cdn-register-powershell.png)
              

### <a name="grant-azure-cdn-access-to-your-key-vault"></a>Ge Azure CDN åtkomst till ditt nyckelvalv
 
Ge Azure CDN behörighet att komma åt certifikaten (hemligheterna) på ditt Azure Key Vault-konto.

1. I nyckelvalvskonto under INSTÄLLNINGAR väljer du **Åtkomstprinciper** och sedan **Lägg till nytt** för att skapa en ny princip.

    ![Skapa en ny åtkomstprincip](./media/cdn-custom-ssl/cdn-new-access-policy.png)

2. I **Välj huvudkonto** söker du efter **205478c0-bd83-4e1b-a9d6-db63a3e1e1c8** och väljer **Microsoft.Azure.Cdn**. Klicka på **Välj**.

    ![Åtkomstprincipinställningar](./media/cdn-custom-ssl/cdn-access-policy-settings.png)

3. Välj **certifikat behörigheter**och markera sedan kryss rutorna för **Hämta** och **lista** så att CDN kan utföra dessa behörigheter för att hämta och lista certifikaten.

4. Välj **hemliga behörigheter**och markera sedan kryss rutorna för **Hämta** och **lista** så att CDN kan utföra dessa behörigheter för att hämta och lista hemligheterna.

5. Välj **OK**. 

    Azure CDN har nu åtkomst till nyckelvalvet och certifikaten (hemligheterna) som lagras i nyckelvalvet.
 
### <a name="select-the-certificate-for-azure-cdn-to-deploy"></a>Välj det certifikat som ska distribueras av Azure CDN
 
1. Gå tillbaka till Azure CDN-portalen och välj den profil och CDN-slutpunkt som du vill aktivera anpassad HTTPS för. 

2. I listan med anpassade domäner väljer du den anpassade domänen som du vill aktivera HTTPS för.

    Sidan **Anpassad domän** visas.

3. Under Certifikathanteringstyp väljer du **Använd mitt eget certifikat**. 

    ![Konfigurera ditt certifikat](./media/cdn-custom-ssl/cdn-configure-your-certificate.png)

4. Välj nyckelvalv, certifikat (hemlighet) och certifikatversion.

    Azure CDN visar följande information: 
    - Nyckelvalvskonton för ditt prenumerations-ID. 
    - Certifikaten (hemligheterna) i det valda nyckelvalvet. 
    - Tillgängliga certifikatversioner. 
 
5. Välj **På** för att aktivera HTTPS.
  
6. Domänverifiering krävs inte om du använder ett eget certifikat. Gå vidare till [Vänta på spridning](#wait-for-propagation).

---

## <a name="validate-the-domain"></a>Verifiera domänen

Om du redan har och använder en anpassad domän som är mappad till din anpassade slutpunkt med en CNAME-post eller om du använder ett eget certifikat fortsätter du till  
[Den anpassade domänen har mappats till CDN-slutpunkten](#custom-domain-is-mapped-to-your-cdn-endpoint-by-a-cname-record). Om CNAME-posten för slutpunkten inte längre finns eller om den innehåller cdnverify-underdomänen fortsätter du i stället till [Den anpassade domänen har inte mappats till CDN-slutpunkten](#custom-domain-is-not-mapped-to-your-cdn-endpoint).

### <a name="custom-domain-is-mapped-to-your-cdn-endpoint-by-a-cname-record"></a>Den anpassade domänen har mappats till CDN-slutpunkten med en CNAME-post

När du lade till en anpassad domän till din slutpunkt skapade du en CNAME-post i din domänregistrators DNS-tabell för att mappa den till CDN-slutpunktens värdnamn. Om CNAME-posten fortfarande finns kvar och inte innehåller cdnverify-underdomänen använder DigiCert-certifikatutfärdaren den för att automatiskt verifiera ägarskapet för din anpassade domän. 

Domänverifiering krävs inte om du använder ett eget certifikat.

CNAME-posten ska ha följande format, där *Namn* är namnet på din anpassade domän och *Värde* är CDN-slutpunktens värdnamn:

| Namn            | Typ  | Värde                 |
|-----------------|-------|-----------------------|
| <www.contoso.com> | CNAME | contoso.azureedge.net |

Mer information om CNAME-poster finns i [Skapa CNAME DNS-posten](https://docs.microsoft.com/azure/cdn/cdn-map-content-to-custom-domain).

Om din CNAME-post har rätt format verifierar DigiCert automatiskt det anpassade domännamnet och skapar ett dedikerat certifikat för domännamnet. DigitCert skickar ingen bekräftelse via e-post och du behöver inte godkänna din begäran. Certifikatet är giltigt i ett år och förnyas automatiskt innan det upphör. Gå vidare till [Vänta på spridning](#wait-for-propagation). 

Det tar vanligt vis några timmar att validera automatiskt. Om du inte ser din domän verifierad inom 24 timmar öppnar du ett support ärende.

>[!NOTE]
>Om du har en CAA-post (Certificate Authority Authorization) hos DNS-providern måste den innehålla DigiCert som giltig certifikatutfärdare. En CAA-post gör att domänägare kan ange med sina DNS-provider vilka certifikatutfärdare som är auktoriserade att utfärda certifikat för deras domän. Om en certifikatutfärdare mottar en order för ett certifikat för en domän som har en CAA-post och den certifikatutfärdaren inte finns med i listan över auktoriserade utfärdare hindras certifikatufärdaren att utfärda certifikatet till den domänen eller underdomänen. Information om hur du hanterar CAA poster finns i [Hantera CAA-poster](https://support.dnsimple.com/articles/manage-caa-record/). Hjälpverktyg för CAA-poster finns i [CAA Record Helper](https://sslmate.com/caa/).

### <a name="custom-domain-is-not-mapped-to-your-cdn-endpoint"></a>Den anpassade domänen har inte mappats till CDN-slutpunkten

>[!NOTE]
>Om du använder **Azure CDN från Akamai**bör följande CNAME konfigureras för att aktivera automatisk domän validering. "_acme-Challenge.&lt;anpassat domän-värdnamn&gt;-> CNAME-> &lt;anpassat domän-värdnamn&gt;. ak-acme-challenge.azureedge.net "

Om posten CNAME innehåller cdnverify-underdomänen följer du resten av anvisningarna i det här steget.

DigiCert skickar ett verifierings-e-postmeddelande till följande e-postadresser. Kontrol lera att du kan godkänna direkt från någon av följande adresser:

admin@&lt;dittdomännamn.com&gt;  
administrator@&lt;dittdomännamn.com&gt;  
webmaster@&lt;dittdomännamn.com&gt;  
hostmaster@&lt;dittdomännamn.com&gt;  
postmaster@&lt;dittdomännamn.com&gt;  

Inom ett par minuter får du ett e-postmeddelande som ser ut ungefär som i följande exempel och som ber dig godkänna begäran. Om du använder ett skräp post filter lägger du till verification@digicert.com i listan över tillåtna. Kontakta Microsoft-supporten om du inte får ett e-postmeddelande inom 24 timmar.
    
![Domänverifieringsmeddelande](./media/cdn-custom-ssl/domain-validation-email.png)

När du klickar på godkännandelänken dirigeras du till följande formulär för godkännande online: 
    
![Domänverifieringsformulär](./media/cdn-custom-ssl/domain-validation-form.png)

Följ instruktionerna i formuläret. Du har två verifieringsalternativ:

- Du kan godkänna alla framtida order som placerats via samma konto för samma rotdomän, till exempel contoso.com. Den här metoden rekommenderas om du tänker lägga till ytterligare anpassade domäner i samma rotdomän.

- Du kan bara godkänna det specifika värdnamn som används i den här begäran. Ytterligare godkännande krävs för efterföljande begäranden.

Efter godkännandet slutför DigiCert skapandet av certifikatet för det anpassade domännamnet. Certifikatet är giltigt i ett år och förnyas automatiskt innan det upphör.

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

1. Sök efter och välj **CDN-profiler**i [Azure Portal](https://portal.azure.com). 

2. Välj din **Azure CDN Standard från Microsoft**, **Azure CDN Standard från Verizon**eller **Azure CDN Premium från Verizon** -profilen.

3. I listan över slut punkter väljer du slut punkten som innehåller din anpassade domän.

4. Välj den anpassade domän som du vill inaktivera HTTPS för.

    ![Lista med anpassade domäner](./media/cdn-custom-ssl/cdn-custom-domain-HTTPS-enabled.png)

5. Välj **inaktivera** om du vill inaktivera https och välj sedan **Använd**.

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

    För både **Azure CDN från Verizon** och **Azure CDN från Microsoft** används ett dedikerat/enskilt certifikat från Digicert för den anpassade domänen’. 

2. *Använder du IP- eller SNI-baserad TLS/SSL?*

    Både **Azure CDN från Verizon** och **Azure CDN Standard från Microsoft** använder SNI-baserad TLS/SSL.

3. *Vad händer om jag inte får domänverifieringsmeddelandet från DigiCert?*

    Om du har en CNAME-post för din anpassade domän som pekar direkt på slutpunktens värdnamn (och du inte använder cdnverify-underdomännamnet) får du inget domänverifieringsmeddelande. Verifieringen sker i så fall automatiskt. Om du inte har en CNAME-post och inte har fått något e-postmeddelande inom 24 timmar kontaktar du Microsoft-supporten.

4. *Är det mindre säkert att använda ett SAN-certifikat än att använda ett dedikerat certifikat?*
    
    Ett SAN-certifikat följer samma standarder för kryptering och säkerhet som ett dedikerat certifikat. Alla utfärdade SSL-certifikat använder SHA-256 för förbättrad serversäkerhet.

5. *Behöver jag en CAA-post (Certificate Authority Authorization) med DNS-leverantören?*

    Nej, en CAA-post krävs inte för närvarande. Men om du har en sådan måste den innehålla DigiCert som en giltig certifikatutfärdare.

6. *Den 20 juni 2018 var Azure CDN från Verizon som började använda ett dedikerat certifikat med SNI TLS/SSL som standard. Vad händer med mina befintliga anpassade domäner med hjälp av certifikat för alternativa namn (SAN) och IP-baserad TLS/SSL?*

    Dina befintliga domäner kommer gradvis att migreras till ett enskilt certifikat under de kommande månaderna om Microsoft i sina analyser ser att det bara görs SNI-klientförfrågningar till ditt program. Om Microsoft ser att det görs andra typer av förfrågningar till ditt program fortsätter domänerna att använda SAN-certifikat med IP-baserad TLS/SSL. Det uppstår dock inga avbrott i tjänsten eller stödet för klientförfrågningar oavsett om förfrågningarna är SNI-baserade eller inte.

7. *Hur fungerar certifikat förnyelser med ditt eget certifikat?*

    Om du vill se till att ett nyare certifikat distribueras till PoP-infrastrukturen, laddar du upp det nya certifikatet till Azure-nyckel valvet och väljer sedan den senaste certifikat versionen i SSL-inställningar på Azure CDN och trycker på Spara. Azure CDN kommer sedan att sprida ditt nya uppdaterade certifikat. 

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> - Aktivera HTTPS-protokollet på din anpassade domän.
> - Använda ett CDN-hanterat certifikat 
> - Använda ditt eget certifikat
> - Verifiera domänen.
> - Inaktivera HTTPS-protokollet på din anpassade domän.

Gå vidare till nästa kurs om du vill lära dig hur du konfigurerar cachelagring på en CDN-slutpunkt.

> [!div class="nextstepaction"]
> [Självstudie: Konfigurera Azures CDN-cachelagringsregler](cdn-caching-rules-tutorial.md)

