---
title: Begreppet Azure IoT-hubb X.509 säkerhet | Microsoft Docs
description: Konceptet - förstå värdet X.509-certifikatet certifikat i IoT-enhet tillverkning och autentisering.
author: eustacea
manager: arjmands
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/18/2017
ms.author: eustacea
ms.openlocfilehash: 1f7a02f66a8d87f33d7bac9068628dbd29e5bd7c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34635703"
---
# <a name="conceptual-understanding-of-x509-ca-certificates-in-the-iot-industry"></a>Grundläggande förståelse för X.509-certifikat i branschen IoT

Den här artikeln beskriver värdet för med X.509-certifikat (certifikatutfärdare) certifikat i IoT-enhet tillverkning och autentisering för IoT-hubb.  Det innehåller även information om leverans kedja installationen och markera fördelar.

Den här artikeln beskrivs:

* X.509-certifikat är och hur du hämtar dem.
* Så här registrerar du X.509 Certifikatutfärdarens certifikat till IoT-hubb
* Hur du ställer in en tillverkning leveranskedja för X.509-CA-baserad autentisering
* Hur enheter som har signerats med X.509 CA ansluta till IoT-hubb

## <a name="overview"></a>Översikt

X.509 certifikatutfärdare (CA)-autentisering är en metod för att autentisera enheter till en metod som förenklar kraftigt identitet skapas och livscykel hantering av enheter i leveranskedjan IoT-hubben.

Attributet särskiljande med X.509 CA-autentisering är en en-till-många-relation som ett CA-certifikat har med dess underordnade enheter.  Den här relationen kan registreringen av ett antal enheter i IoT-hubb genom att registrera ett X.509-certifikatutfärdarcertifikat en gång, annars unika enhetscertifikat måste förregistrerade för varje enhet innan en enhet kan ansluta. Den här en-till-många-relationen förenklar också hanteringsåtgärder för enheten certifikat-livscykel.

En annan viktig attribut för X.509-CA-autentisering är förenkling av ange kedjan logistik.  Säker autentisering av enheter kräver att varje enhet har en unik hemlighet som en nyckel som bas för förtroende. I certifikat för autentisering är den här hemligheten en privat nyckel. En typisk enhet tillverkar flödet omfattar flera steg och custodians.  På ett säkert sätt är hantera privata nycklar för enheten över flera custodians och upprätthålla förtroende svåra och dyra.  Med hjälp av certifikatutfärdare löser detta problem genom att logga varje skyddar i en kryptografisk förtroendekedja för i stället för att överlåta dem med enheten privata nycklar.  Varje skyddar loggar i sin tur enheter på deras respektive processen steg i tillverkning flöde.  Övergripande resultatet är en optimal leveranskedja med inbyggda accountability med hjälp av den kryptografiska förtroendekedja för.  Det är värt att nämna att den här processen ger bäst skydd när enheter skyddar sina unika privata nycklar.  Vi rekommenderar användning av maskinvara säkra moduler (HSM) kan genereras internt privata nycklar som visas aldrig ljus på dagen för detta ändamål.

Den här artikeln innehåller en vy för slutpunkt till slutpunkt med X.509 CA-autentisering från ange kedja installationen till enhetens anslutning när gör använda ett verkligt exempel för att förstå.

## <a name="introduction"></a>Introduktion

X.509-CA-certifikatet är ett digitalt certifikat vars innehavaren kan signera andra certifikat.  Den här digitala certifikat är X.509 eftersom den överensstämmer med ett certifikat som standard som föreskrivs av IETF: s RFC 5280 standard formatering och är en certifikatutfärdare (CA) eftersom innehavaren kan signera andra certifikat.

Användning av X.509 CA förstå bäst i förhållande till en konkret exempel.  Överväg att företagets X, en tillverkaren av smartkort-X-widgetar utformade för professionell installation. Företag X outsources både tillverkning och installation.  Det kontrakt tillverkare Factory-Y som tillverkar smartkort-X-widgetar och tjänstleverantören tekniker Z att installera. Företag X önskar att smartkort-X-Widget direkt levereras från fabriken Y till tekniker Z för installation och att den ansluter direkt till företagets-x instans av IoT-hubb efter installationen utan ytterligare åtgärder från företag X. Företag X måste slutföra några enstaka installationsprogrammet åtgärder för att prime smartkort-X-Widget för automatisk anslutning för att göra detta.  Med slutpunkt till slutpunkt-scenario i åtanke resten av den här artikeln med följande struktur:

* Hämta X.509 CA-certifikatet

* Registrera X.509 CA-certifikatet till IoT-hubb

* Logga enheter i en certifikatkedja med förtroenden

* Enhetsanslutning

## <a name="acquire-the-x509-ca-certificate"></a>Hämta X.509 CA-certifikatet

Företag X har alternativet att köpa ett X.509-CA-certifikat från en offentlig rotcertifikatutfärdare eller skapa en via en process som självsignerat.  Ett alternativ är optimala över andra beroende på scenario för programmet.  Oavsett alternativet signeras två grundläggande steg, generera ett offentligt/privat nyckelpar och registrerar den offentliga nyckeln till ett certifikat.

![img csr-flöde](./media/csr-flow.png)

Information om hur du utför de här stegen skiljer sig med olika leverantörer.

### <a name="purchasing-an-x509-ca-certificate"></a>Köp ett X.509-CA-certifikat

Köpa ett CA-certifikat har fördelen att har en välkänd rot-CA-handling som en betrodd tredje part till garanterar äkthet IoT-enheter när enheterna som ansluter. Företag X väljer det här alternativet om de avser Smart X Widget att interagera med tredje partsprodukter eller tjänster efter den första anslutningen till IoT-hubben.

Om du vill köpa ett CA X.509-certifikat, väljer företag X en tjänsteleverantör för rot-certifikat. En internet-Sök efter frasen rot-CA ger bra leads.  Rot-CA hjälper företag X på hur du skapar det offentliga/privata nyckelparet och hur du skapar en begäran om certifikatsignering (CSR) för sina tjänster.  En CSR är formella att använda ett certifikat från en certifikatutfärdare.  Resultatet av det här köpet är ett certifikat för användning som ett certifikat.  Allmän förekomst av X.509-certifikat får sannolikt certifikatet har formaterats korrekt till IETF: s RFC 5280 standard.

### <a name="creating-a-self-signed-x509-ca-certificate"></a>Skapa ett självsignerat X.509 CA-certifikat

Processen för att skapa ett självsignerat X.509 CA-certifikat liknar köp med undantag som rör en tredjeparts-Signerare som rotcertifikatutfärdaren. I vårt exempel signerar företag X dess certifikat i stället för en rotcertifikatutfärdare.  Företag X kan välja det här alternativet för att testa tills de är redo att köpa ett certifikat. Företag X kan också använda ett självsignerat X.509 CA-certifikat i produktionen om smartkort-X-Widget inte är avsedd att ansluta till alla tjänster från tredje part utanför IoT-hubben.

## <a name="register-the-x509-certificate-to-iot-hub"></a>Registrera X.509-certifikatet till IoT-hubb

Företag X måste registrera X.509-CA: N till IoT-hubb som om det ska användas för att autentisera smartkort-X-widgetar som de ansluter.  Detta är en engångsprocess som gör möjligheten att autentisera och hantera ett antal smartkort-X-Widget enheter.  Den här processen är enstaka på grund av en en-till-många-relation mellan certifikat och enheter och även utgör ett av de största fördelarna med att använda autentiseringsmetoden X.509-CA.  Alternativet är att överföra enskilda certifikattumavtryck för varje smartkort-X-Widget enhet så att lägga till driftskostnader.

Registrera X.509 CA-certifikatet är en tvåstegsprocess, ladda upp certifikatet och certifikatet bevis av tillgång.

![img-pop-flöde](./media/pop-flow.png)

### <a name="x509-ca-certificate-upload"></a>Ladda upp X.509 CA-certifikat

X.509-CA-certifikatet uppladdningsprocessen är precis som, ladda upp CA-certifikatet till IoT-hubb.  IoT-hubb förväntar certifikatet i en fil. Företag X Överför bara certifikatfilen. Certifikatfilen får inte under några omständigheter innehålla privata nycklar.  Bästa praxis från standarder för Public Key Infrastructure (PKI) kräver kunskaper om företaget-x privat finns i det här fallet enbart inom företag X.

### <a name="proof-of-possession-of-the-certificate"></a>Bevis tillgång för certifikatet

X.509-CA-certifikatet, precis som alla digitala certifikat är offentlig information som är sårbara för avlyssning.  Därför kan tjuvlyssning snappa upp ett certifikat och försök att överföra som sina egna.  I vårt exempel vill IoT-hubb se till att företagets X laddar upp CA-certifikatet verkligen tillhör företag X. Så företaget-x svårt att bevis som de i själva verket har certifikatet via en [bevis tillgång (PoP) flöde](https://tools.ietf.org/html/rfc5280#section-3.1). Bevis av tillgång flödet innebär att IoT-hubb genererar ett slumptal signeras med företagets X med sin privata nyckel.  Om företag X följt PKI metodtips och skyddade sin privata nyckel skulle sedan bara de göra i positionen till korrekt svarar bevis av tillgång.  IoT-hubb fortsätter att registrera X.509 CA-certifikatet på ett lyckat svar för utmaningen bevis av tillgång.

Ett lyckat svar på bevis av tillgång-anrop från IoT-hubb Slutför registreringen X.509-CA.

## <a name="sign-devices-into-a-certificate-chain-of-trust"></a>Logga enheter i en certifikatkedja med förtroenden

IoT kräver att varje enhet har en unik identitet.  Dessa identiteter finns i formuläret certifikaten för certifikatbaserad autentiseringsscheman.  I vårt exempel innebär detta varje smartkort-X-Widget måste ha ett unikt enhets-certifikat.  Hur företag X in för detta i sin leveranskedja?

Ett sätt att göra detta är att förgenerera certifikat för Smart-X-widgetar och överlåta kunskap om motsvarande unikt enhets privata nycklar med leverans kedjan partners.  För företag X, innebär detta att överlåta Factory-Y och tekniker Z.  När detta är en giltig metod medföljer utmaningar som måste undvikas för att säkerställa förtroende på följande sätt:

1. Behöva dela enheten privata nycklar med leverans kedjan partners, förutom ignorerar PKI och metodtips för att dela aldrig privata nycklar, gör skapa förtroende för leveranskedjan dyra.  Det innebär att kapital system som säkra rum till house enheten privata nycklar och processer som periodiska säkerhetsgranskningar behöver installeras.  Både lägga till kostnaden av leveranskedja.

2. Blir en-till-en aktivitet för varje nyckel till enhet par från enheten unika (därför privata nyckel) Certifikatgenereringen punkt till avyttring redovisning på ett säkert sätt för enheter i leveranskedjan och hantera dem senare i distributionen. Detta förhindrar grupphantering för enheter om begreppet grupper är uttryckligen inbyggd i processen på något sätt. Säker redovisnings- och livscykelhantering, därför blir tungt tunga åtgärder.  I vårt exempel skulle företag X förses med det här problemet.

X.509 CA certifikat-autentisering ger elegant lösningar på ovan visas utmaningar genom att använda certifikatkedjor.  En certifikatkedja resultat från en CA registrerar en mellanliggande Certifikatutfärdare som signerar en annan mellanliggande Certifikatutfärdare i sin tur och så försätts i tills en slutlig mellanliggande CA registrerar en enhet.  I vårt exempel signerar företag X Factory-Y, som i sin tur loggar tekniker Z som signerar slutligen smartkort-X-Widget.

![img cert-kedjan-hierarki](./media/cert-chain-hierarchy.png)

Anger den logiska hand av myndigheten ovan cascade av certifikat i kedjan.  Många ange kedjor följer den här logiska hand av där varje mellanliggande Certifikatutfärdare hämtar inloggad kedjan vid mottagning av alla överordnade CA-certifikat och senaste mellanliggande Certifikatutfärdare signerar slutligen varje enhet och mata in alla utfärdare certifikat från kedjan i enheten. Detta är vanligt när kontraktet tillverkningsföretag med en hierarki med fabriker provisioner en viss fabrik göra tillverkningen.  När hierarkin kan bero på flera nivåer djupt (till exempel efter geografisk plats/produkt typ/produktion rad), bara fabriken slutet hämtar interagera med enheten men kedjan upprätthålls från överst i hierarkin.

Alternativa kedjor kan ha olika mellanliggande Certifikatutfärdare interagera med enheten där fallet CA interagera med enheten lägger in certifikatets kedjan innehåll vid den punkten.  Hybridmodeller är också möjligt för där endast några av CA: N har fysiska interaktion med enheten.

I vårt exempel interagerar både Factory-Y och tekniker Z med smartkort-X-validering.  Medan företag X äger smartkort-X-Widget, interagerar den faktiskt fysiskt inte med den i hela leveranskedja.  Certifikatkedja med förtroenden för smartkort-X-Widget därför omfatta företag X signering Factory-Y som i sin tur loggar tekniker Z som kommer sedan att tillhandahålla slutliga signaturen för smartkort-X-Widget. Tillverkning och installation av smartkort-X-Widget utgör Factory-Y och tekniker Z använder sina respektive mellanliggande CA-certifikat för att signera varje smartkort-X-widgetar. Slutresultatet av hela den här processen är smartkort-X-widgetar med unikt enhets-certifikat och certifikatkedja med förtroenden ska företag X CA-certifikat.

![img--mfr-kedja](./media/cert-mfr-chain.png)

Detta är en bra att granska värdet för X.509-CA-metoden.  I stället för att generera före och lämna in certifikat för varje smartkort-X-Widget i leveranskedjan, lämnar företaget X bara för att signera Factory-Y en gång.  I stället att spåra alla enheter genom hela livscykeln enheter, kan företag X inte spåra och hantera enheter via grupper versionskonflikter naturligt från inköpsprocessen, till exempel enheter som installeras av tekniker Z efter juli vissa året.

Senast men inte minst infuses säker accountability till enheten som tillverkar leveranskedja metoden Certifikatutfärdaren för autentisering. På grund av hur certifikatkedjan åtgärderna för varje medlem i kedjan går att kryptografiskt verifiera.

Den här processen är beroende av vissa antaganden måste exponeras för fullständighetens skull.  Det krävs oberoende skapandet av enheten unika offentliga/privata nyckelpar och att den privata nyckeln skyddas i enheten.  Lyckligtvis finns säker silicon modulerna i form av maskinvara säker moduler (HSM) kan internt nycklar genereras och skydda privata nycklar.  Företag X behöver bara lägga till en av dessa kretsar i smartkort-X-widgeten komponenten strukturer.

## <a name="device-connection"></a>Enhetsanslutning

Föregående avsnitt ovan har bygga upp till enhetens anslutning.  Genom att bara registrera ett X.509-CA-certifikat för IoT-hubb en tid, hur potentiellt miljontals enheter ansluta och hämta autentiserad från första gången?  Enkel; via samma certifikat överföringen och bevis av tillgång flödet tidigare påträffades med registrering X.509 CA-certifikatet.

Enheter som har tillverkats för X.509-CA-autentisering är utrustade med enhetens unika certifikat och en certifikatkedja från deras respektive tillverkar leveranskedja.  Enhetsanslutning även för första gången sker i två steg: kedjan överföringen och bevis av tillgång.

Under överföringen certifikatkedjan filöverföringar enheten sin enhet unikt certifikat tillsammans med certifikatkedjan installeras i den IoT-hubb.  Med det förregistrerade X.509-certifikatutfärdarcertifikatet verifiera IoT-hubb kryptografiskt några saker att överförda certifikatkedjan är internt konsekvent och att kedjan har sitt ursprung av X.509 CA-certifikatet giltigt ägare.  Var bara med X.509 CA-registreringen, IoT-hubb kan initiera en process för bevis av tillgång utmaningssvar bekräftas att kedjan och därför enhetscertifikatet verkligen tillhör enheten överföra den.  Den gör detta genom att generera en slumpmässig utmaning signeras av enheten med dess privata nyckel för verifiering av IoT-hubb.   Ett lyckat svar utlöser IoT-hubb för att godkänna enheten som autentiska och ge den anslutningen.

I vårt exempel skulle varje smartkort-X-Widget överföra sin enhet unikt certifikat tillsammans med Factory-Y och tekniker Z X.509 CA-certifikat och sedan svarar bevis av tillgång från IoT-hubb.

![img enhet-pop-flöde](./media/device-pop-flow.png)

Observera att grunden för förtroende vilar för att skydda privata nycklar inklusive enhet privata nycklar.  Vi kan därför vara påfrestande tillräckligt vikten av säker silicon chips i form av maskinvara säker moduler (HSM) för att skydda privata nycklar för enheten och den totala bästa praxis och aldrig delar privata nycklar, t.ex. en fabrik överlåta en annan med dess privat nyckel.

