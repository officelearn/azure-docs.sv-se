---
title: Principerna för Azure IoT Hub X.509-säkerhet | Microsoft Docs
description: Concept – förstå värdet X.509 certificate certifikat i IoT-enhetens tillverkning och autentisering.
author: eustacea
manager: arjmands
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/18/2017
ms.author: eustacea
ms.openlocfilehash: 3c7e1167b3326620863d35cb2d4b07235cbd5517
ms.sourcegitcommit: e89b9a75e3710559a9d2c705801c306c4e3de16c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2019
ms.locfileid: "59571353"
---
# <a name="conceptual-understanding-of-x509-ca-certificates-in-the-iot-industry"></a>Konceptuell förståelse för X.509 CA-certifikat i IoT-branschen

Den här artikeln beskriver värdet av med X.509 certificate authority (CA)-certifikat i IoT-enhetens tillverkning och autentisering till IoT Hub. Den innehåller information om leverans kedja installationen och markera fördelar.

Den här artikeln beskrivs:

* Vad X.509 CA-certifikat är och hur du hämtar dem

* Så här registrerar du ditt X.509 CA-certifikat till IoT Hub

* Hur du ställer in en tillverkning leveranskedja för autentisering med X.509 CA

* Hur enheter som har signerats med X.509 CA ansluter till IoT Hub

## <a name="overview"></a>Översikt

X.509-autentisering för certifikatutfärdaren (CA) är en metod för att autentisera enheter till IoT Hub med en annan metod som avsevärt förenklar identitet skapande och livscykeln för hantering av enheter i leveranskedjan.

Attributet särskiljande med X.509 CA-autentisering är en en-till-många-relation som ett CA-certifikat som har med dess underordnade enheter. Den här relationen kan registreringen av valfritt antal enheter i IoT Hub genom att registrera ett X.509 CA-certifikat på en gång, annars enheten unika certifikaten måste ha registrerats i förhand för varje enhet innan en enhet kan ansluta. Den här en-till-många-relationen förenklar också åtgärder certifikat livscykeln för enhetshantering.

Ett annat viktigt attribut av X.509 CA-autentisering är förenkling av distribution av strömförsörjning kedja logistik. Säker autentisering av enheter kräver att varje enhet har en unik hemlighet som en nyckel som grund för förtroende. I certifikat-baserad autentisering är den här hemligheten en privat nyckel. En typisk enhet inom tillverkning flow omfattar flera steg och nyckelkomponenter. På ett säkert sätt är hantera privata nycklar för enheten i flera nyckelkomponenter och underhålla förtroende svåra och dyra. Med hjälp av certifikatutfärdare löser detta problem genom att logga varje skyddar i en kryptografisk certifikatkedja i stället för att överlåta dem med privata nycklar för enheten. Varje skyddar loggar i sin tur enheter på sina respektive processen steg i flödet tillverkning. Övergripande resultatet är en optimal leveranskedjan med inbyggda ansvarigheten genom användning av kryptografiska förtroendekedjan. Det är värt att den här processen ger bäst skydd när enheter skydda deras unika privata nycklar. Därför bör användning av maskinvara skydda moduler (HSM) kan utforma internt privata nycklar som kommer aldrig att se ljuset på dagen.

Den här artikeln innehåller en vy över med X.509 CA-autentisering från installationsprogrammet för tillgångs-kedjan att enhetsanslutning, vid att använda ett verkliga exempel för att stärka förståelse.

## <a name="introduction"></a>Introduktion

X.509 CA-certifikatet är ett digitalt certifikat vars innehavaren kan signera andra certifikat. Den här digitala certifikat är X.509 eftersom den överensstämmer med ett certifikat som formatering standard föreskrivs av IETF: s RFC 5280 standard och är en certifikatutfärdare (CA) eftersom innehavaren kan signera andra certifikat.

Användning av X.509 CA är bäst tolkas i förhållande till ett konkreta exempel. Överväg att företag X, skapare av Smart-X-widgetar för professionell installation. Företag X outsources tillverknings- och installationen. Det kontrakt tillverkare Factory Y som tillverkar Smart-X-widgetar och tjänstleverantör tekniker Z att installera. Företag X önskar att Smart-X-Widget direkt levereras från Factory Y till tekniker Z för installation och att den ansluter direkt till företagets-x instans av IoT Hub efter installationen utan ytterligare åtgärder från företag X. Om du vill göra detta måste företag X att slutföra några konfigurationen gång åtgärder för att Preparera Smart-X-widgeten för automatisk anslutning. Med slutpunkt till slutpunkt-scenario i åtanke struktureras resten av den här artikeln på följande sätt:

* Hämta X.509 CA-certifikatet

* Registrera X.509 CA-certifikat till IoT Hub

* Registrera enheter i en certifikatkedja med förtroenden

* Enhetsanslutning

## <a name="acquire-the-x509-ca-certificate"></a>Hämta X.509 CA-certifikatet

Företag X har alternativet att köpa ett X.509 CA-certifikat från en offentlig rotcertifikatutfärdare eller skapa en via en process som är självsignerat. En alternativet skulle vara optimal framför ett annat beroende på program-scenario. Oavsett alternativet signeras två grundläggande steg, generera ett offentligt/privat nyckelpar och registrerar den offentliga nyckeln till ett certifikat.

![Flöde för att generera en X509CA-certifikat](./media/iot-hub-x509ca-concept/csr-flow.png)

Information om hur du utför de här stegen skiljer sig med olika leverantörer.

### <a name="purchasing-an-x509-ca-certificate"></a>Köpa ett X.509 CA-certifikat

Köpa ett CA-certifikat har fördelen med att ha en välkänd rot-CA-act en betrodd tredje part till garanterar giltighet IoT-enheter när enheterna som ansluter. Företag X väljer det här alternativet om de avser Smart-X-widgeten för att interagera med produkter från tredje part eller -tjänster efter den första anslutningen till IoT Hub.

Om du vill köpa ett X.509 CA-certifikat, väljer företag X en rot-certifikat services-provider. En internet-Sök efter frasen rot-CA ger bra leads. Rot-CA hjälper företag X på hur du skapar offentligt/privat nyckelpar och hur du skapar en begäran om certifikatsignering (CSR) för sina tjänster. En CSR är en formell process för tillämpning efter ett certifikat från en certifikatutfärdare. Resultatet av det här köpet är ett certifikat för användning som ett certifikat. Med populära X.509-certifikat kan troligtvis certifikatet har formaterats korrekt till IETF: s RFC 5280 standard.

### <a name="creating-a-self-signed-x509-ca-certificate"></a>Skapa ett självsignerat X.509 CA-certifikat

Processen för att skapa ett självsignerat X.509 CA-certifikat liknar köpa med undantag som inbegriper en tredjeparts-Signerare som rotcertifikatutfärdaren. I vårt exempel signerar företag X dess certifikat i stället för en rotcertifikatutfärdare. Företag X kan välja det här alternativet för att testa tills du är redo att köpa ett certifikat. Företag X kan också använda ett självsignerat X.509 CA-certifikat i produktion, om Smart-X-Widget inte är avsedd att ansluta till valfria tredjepartstjänster utanför IoT-hubben.

## <a name="register-the-x509-certificate-to-iot-hub"></a>Registrera X.509-certifikat till IoT Hub

Företag X måste registrera X.509-CA: N till IoT Hub om det ska användas för att autentisera Smart-X-widgetar som de ansluter. Det här är en engångsprocess som ger möjlighet att autentisera och hantera ett antal enheter för Smart-X-Widget. Den här processen är enstaka på grund av en en-till-många-relation mellan certifikat och enheter och utgör också en av de största fördelarna med att använda X.509 CA-autentiseringsmetod. Alternativet är att ladda upp enskilda certifikattumavtryck för varje Smart-X-Widget enhet så att lägga till i driftskostnader.

Registrera X.509 CA-certifikatet är en tvåstegsprocess, ladda upp certifikatet och certifikatet bevis av tillgång.

![Registrera ett X509CA-certifikat](./media/iot-hub-x509ca-concept/pop-flow.png)

### <a name="x509-ca-certificate-upload"></a>Ladda upp X.509 CA-certifikatet

X.509 CA-certifikatet uppladdningsprocessen är just detta, ladda upp CA-certifikatet till IoT Hub. IoT Hub förväntar sig certifikatet i en fil. Företag X Överför bara certifikatfilen. Certifikatfil måste inte under några omständigheter innehåller privata nycklar. Bästa praxis från standarder för informationssäkerhet Public Key Infrastructure (PKI) mandat denna kunskap av företags-x privat finns i det här fallet exklusivt inom företag X.

### <a name="proof-of-possession-of-the-certificate"></a>Bevis tillgång för certifikatet

X.509 CA-certifikat, precis som alla digitalt certifikat, är offentlig information som är sårbart för avlyssning. Därför kan tjuvlyssning snappa upp ett certifikat och försöker ladda upp dem som egna. I vårt exempel vill IoT Hub Kontrollera att certifikatet företag X överför verkligen tillhör företag X. Så svårt företagets-x till korrektur som de i själva verket har certifikatet via en [bevis tillgång (PoP) flow](https://tools.ietf.org/html/rfc5280#section-3.1). Bevis på tillgång flödet innebär IoT Hub genererar ett slumptal signeras med företag X, med dess privata nyckel. Om företag X följt PKI metodtips och skyddas sin privata nyckel skulle och endast de vara i kunna korrekt svarar bevis av tillgång. IoT Hub fortsätter att registrera X.509 CA-certifikat när ett lyckat svar av utmaningar bevis av tillgång.

Ett lyckat svar på frågan bevis på tillgång från IoT Hub har slutförts X.509 CA-registreringen.

## <a name="sign-devices-into-a-certificate-chain-of-trust"></a>Registrera enheter i en certifikatkedja med förtroenden

IoT kräver att varje enhet har en unik identitet. Dessa identiteter finns i formuläret certifikaten för certifikatbaserad autentiseringsscheman. I vårt exempel, innebär detta att varje Smart-X-Widget måste ha ett unikt enhets-certifikat. Hur företag X klart för detta i sin leveranskedja?

Ett sätt är att om det här är att förväg generera certifikat för Smart-X-widgetar och överlåta kunskaper om motsvarande unikt enhets privata nycklar med partner i leveranskedjan. För företag-X, det innebär att överlåta Factory Y och tekniker-Z. Även om det är en giltig metod medföljer utmaningar som måste undvikas för att säkerställa förtroende på följande sätt:

1. Behöva dela enheten privata nycklar med partner i leveranskedjan, förutom ignorerar PKI praxis bästa att aldrig dela privata nycklar, gör att skapa förtroendet i leveranskedjan dyra. Det innebär stora system som säker rum till house enheten privata nycklar och processer som periodiska säkerhetsgranskningar måste installeras. Både lägga till kostnaden leveranskedjan.

2. På ett säkert sätt redovisning för enheter i leveranskedjan och hantera dem senare i distributionen blir en 1: 1-uppgift för varje par med nyckeln till enheten från det datum då enheten unikt certifikat (därför privata nyckeln) genererades för avyttring. Detta utesluter grupphantering för enheter, såvida inte konceptet med grupper är uttryckligen byggs in i processen på något sätt. Säker redovisning och enheten livscykelhantering, blir därför åtgärder vid hög belastning. I vårt exempel skulle företag X bär det här problemet.

Autentisering med datorcertifikat X.509 CA erbjuder elegant lösningar på ovan visas utmaningar genom att använda certifikatkedjor. En certifikatkedja som resulterar från en Certifikatutfärdare som registrerar en mellanliggande Certifikatutfärdare som i sin tur loggar en annan mellanliggande Certifikatutfärdare och går därför tills en slutlig mellanliggande CA registrerar en enhet. I vårt exempel signerar företag X Factory-Y, vilket i sin tur loggar tekniker Z som slutligen loggar Smart-X-Widget.

![Certifikathierarkin för kedja](./media/iot-hub-x509ca-concept/cert-chain-hierarchy.png)

Anger den logiska hand-off myndighetens ovan cascade av certifikat i kedjan. Många leveranskedjor följer den här logiska hand-off där varje mellanliggande Certifikatutfärdare loggas i kedjan vid mottagning av alla överordnade CA-certifikat och den senaste mellanliggande CA signerar slutligen varje enhet och mata in alla utfärdare av certifikat från kedjan in på enheten. Detta är vanligt när tillverkningsföretag kontrakt med en hierarki för fabriker provisioner en viss factory för att göra tillverkningen. Även om hierarkin är flera nivåer djupt (till exempel efter geografisk plats/produkt typ/tillverkning rad), hämtar endast fabriken i slutet att interagera med enheten men kedjan upprätthålls från överst i hierarkin.

Alternativa kedjor kan ha olika mellanliggande Certifikatutfärdare som interagerar med enheten där fallet CA interagerar med enheten lägger in certifikatkedja innehållet i det här läget. Hybridmodeller är också möjligt för där endast några av CA: N har fysiska interaktion med enheten.

I vårt exempel interagerar både Factory Y och tekniker Z med Smart-X-widgeten. Medan företag X äger Smart-X-Widget kan interagerar det faktiskt fysiskt inte med den i hela leveranskedjan. Certifikatkedja med förtroenden för Smart-X-Widget därför utgör företag X signering Factory Y som i sin tur loggar tekniker Z som sedan ger sista signaturen på smartkort-X-widgeten. Tillverkning och installation av Smart-X-Widget utgör Factory Y och tekniker-Z med sina respektive mellanliggande CA-certifikat för att logga varje Smart-X-widgetar. Slutresultatet av den här processen är Smart-X-widgetar med unika enhetscertifikat och certifikatkedja med förtroenden gå till företag X CA-certifikat.

![Förtroendekedja från certifikat för ett företag att certifikat med ett annat företag](./media/iot-hub-x509ca-concept/cert-mfr-chain.png)

Detta är en bra att granska värdet för X.509 CA-metoden. I stället för att generera förväg och lämna in certifikat för varje Smart-X-Widget i leveranskedjan, behövde företag X bara logga Factory Y en gång. I stället att spåra varje enhet i enhetens livscykel, kan företag X nu spåra och hantera enheter via grupper versionskonflikter naturligt från inköpsprocessen, till exempel enheter som har installerats av tekniker Z efter juli för några år.

Senast men inte minst infuses säker ansvarstagande in på enheten som tillverkar leveranskedja i CA-metoden för autentisering. På grund av hur certifikatkedja åtgärderna för varje medlem i kedjan går att kryptografiskt verifiera.

Den här processen är beroende av vissa antaganden måste exponeras för fullständighetens skull. Det kräver oberoende skapandet av enhetens unika offentligt/privat nyckelpar och att den privata nyckeln skyddas i enheten. Som tur är kan finns säker silicon kretsar i form av maskinvara säker moduler (HSM) kan internt nycklar genereras och skydda privata nycklar. Företag X behöver bara lägga till en av dessa kretsar till Smart-X-widgeten komponenten materialförteckning.

## <a name="device-connection"></a>Enhetsanslutning

Föregående avsnitt ovan har bygga upp till enhetens anslutning. Genom att bara registrera ett X.509 CA-certifikat till IoT Hub en gång, hur potentiellt miljontals enheter ansluta och hämta autentiserade från första gången?  Enkel; via samma certifikatuppladdning och bevis på tillgång flow påträffades tidigare med registrering av X.509 CA-certifikat.

Enheter som har tillverkats för X.509 CA-autentisering är utrustade med unika enhetscertifikat och en certifikatkedja från deras respektive tillverkar leveranskedja. Enhetsanslutning, även för första gången sker i två steg: kedja ladda upp och proof av tillgång.

Under uppladdningen av certifikatkedja överför enheten dess unika certifikat tillsammans med certifikatkedjan har installerats på den till IoT Hub. Använder registrerats i förhand X.509 CA-certifikat, validera IoT Hub kryptografiskt några saker att överförda certifikatkedjan är internt konsekvent och att kedjan kommer ifrån av X.509 CA-certifikatet giltigt ägare. Var bara med X.509 CA-registreringen, IoT Hub skulle initierar en process som bevis på tillgång svar bekräftas att kedjan och kan därför enhetscertifikat faktiskt hör till enheten ladda upp den. Detta sker genom att generera en slumpmässig utmaning signeras med enheten med dess privata nyckel för verifiering av IoT Hub. Ett lyckat svar utlöser IoT-hubb för att acceptera enhet är autentisk och ge den anslutningen.

I vårt exempel skulle varje Smart-X-Widget ladda upp dess unika certifikat tillsammans med Factory Y och tekniker Z X.509 CA-certifikat och sedan svara på bevis på tillgång frågan från IoT Hub.

![Flödar från ett certifikat till en annan, pop utmaning från hub](./media/iot-hub-x509ca-concept/device-pop-flow.png)

Observera att grunden för förtroende vilar skydda privata nycklar, inklusive privata nycklar för enheten. Vi kan därför vara påfrestande tillräckligt vikten av säker silicon chips i form av maskinvara säker moduler (HSM) för att skydda privata nycklar för enheten och den totala bästa praxis och aldrig delar alla privata nycklar som en fabrik överlåta en annan med dess privat nyckel.