---
title: Koncept för Azure IoT Hub X.509-säkerhet | Microsoft-dokument
description: Koncept - förstå värdet X.509 certifikatutfärdare certifikat i IoT-enhet tillverkning och autentisering .
author: eustacea
manager: arjmands
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/18/2017
ms.author: eustacea
ms.openlocfilehash: 3c7e1167b3326620863d35cb2d4b07235cbd5517
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "61320489"
---
# <a name="conceptual-understanding-of-x509-ca-certificates-in-the-iot-industry"></a>Begreppsmässig förståelse av X.509 CA-certifikat i IoT-branschen

I den här artikeln beskrivs värdet av att använda Certifikatutfärdarecertifikat för X.509 i IoT-enhetens tillverkning och autentisering till IoT Hub. Den innehåller information om supply chain setup och markera fördelar.

I den här artikeln beskrivs:

* Vad X.509 CA-certifikat är och hur du får dem

* Så här registrerar du ditt X.509 CA-certifikat till IoT Hub

* Konfigurera en tillverkningsförsörjningskedja för X.509 CA-baserad autentisering

* Så här ansluter enheter signerade med X.509 CA till IoT Hub

## <a name="overview"></a>Översikt

X.509 Certifikatutfärdaresautentisering är en metod för att autentisera enheter till IoT Hub med en metod som dramatiskt förenklar skapandet av enhetsidentitet och livscykelhantering i leveranskedjan.

Ett särskiljande attribut för X.509 CA-autentisering är en 1:1-relation som ett CA-certifikat har med sina underordnade enheter. Den här relationen möjliggör registrering av valfritt antal enheter i IoT Hub genom att registrera ett X.509 CA-certifikat en gång, annars måste enhetsunika certifikat vara förregistrerade för varje enhet innan en enhet kan ansluta. Den här 1:1-relationen förenklar också livscykelhanteringsåtgärder för enhetscertifikat.

Ett annat viktigt attribut för X.509 CA-autentiseringen är förenkling av logistiken i leveranskedjan. Säker autentisering av enheter kräver att varje enhet har en unik hemlighet som en nyckel som grund för förtroende. I certifikatbaserad autentisering är den här hemligheten en privat nyckel. Ett typiskt enhetstillverkningsflöde innebär flera steg och vårdnadshavare. Det är svårt och dyrt att hantera enhetsprivata nycklar på ett säkert sätt över flera vårdnadshavare och att upprätthålla förtroendet. Använda certifikatutfärdare löser detta problem genom att signera varje vårdnadshavare i en kryptografisk förtroendekedja i stället för att anförtro dem med enhetsprivatnycklar. Varje vårdnadshavare i sin tur undertecknar enheter vid sina respektive processsteg i tillverkningsflödet. Det övergripande resultatet är en optimal leveranskedja med inbyggd ansvarsskyldighet genom användning av den kryptografiska förtroendekedjan. Det är värt att notera att denna process ger mest säkerhet när enheter skyddar sina unika privata nycklar. För detta ändamål uppmanar vi användning av Hardware Secure Modules (HSM) som kan internt generera privata nycklar som aldrig kommer att se dagens ljus.

Den här artikeln innehåller en heltäckande vy för att använda X.509 CA-autentisering, från leveranskedjans inställning till enhetsanslutning, samtidigt som du använder ett verkligt exempel för att befästa förståelsen.

## <a name="introduction"></a>Introduktion

X.509 CA-certifikatet är ett digitalt certifikat vars innehavare kan signera andra certifikat. Det här digitala certifikatet är X.509 eftersom det överensstämmer med en certifikatformateringsstandard som föreskrivs i IETF:s RFC 5280-standard och är certifikatutfärdare eftersom innehavaren kan signera andra certifikat.

Användningen av X.509 CA förstås bäst i förhållande till ett konkret exempel. Tänk på Company-X, en tillverkare av Smart-X-Widgets som utformats för professionell installation. Företag-X lägger ut både tillverkning och installation. Företaget har kontrakt med tillverkaren Factory-Y för att tillverka Smart-X-Widgets och serviceleverantören Technician-Z att installera. Företaget-X önskar att Smart-X-Widget direkt levereras från Factory-Y till Technician-Z för installation och att det ansluter direkt till Company-X: s instans av IoT Hub efter installationen utan ytterligare ingripande från Company-X. För att detta ska ske måste Company-X slutföra några engångsinstallationsåtgärder för att förbereda Smart-X-Widget för automatisk anslutning. Med scenariot från slutna till är resten av den här artikeln strukturerad på följande sätt:

* Skaffa X.509-ca-certifikatet

* Registrera X.509 CA-certifikat till IoT Hub

* Logga in enheter i en certifikatkedja med förtroende

* Enhetsanslutning

## <a name="acquire-the-x509-ca-certificate"></a>Skaffa X.509-ca-certifikatet

Företag-X har möjlighet att köpa ett X.509 CA-certifikat från en offentlig rotcertifikatutfärdande myndighet eller skapa ett genom en självsignerad process. Ett alternativ skulle vara optimalt jämfört med det andra beroende på programscenariot. Oavsett alternativet innebär processen två grundläggande steg, vilket genererar ett offentligt/privat nyckelpar och signerar den offentliga nyckeln till ett certifikat.

![Flöde för att generera ett X509CA-certifikat](./media/iot-hub-x509ca-concept/csr-flow.png)

Information om hur du utför dessa steg skiljer sig åt med olika tjänsteleverantörer.

### <a name="purchasing-an-x509-ca-certificate"></a>Köpa ett X.509 CA-certifikat

Att köpa ett CA-certifikat har fördelen av att ha en välkänd rotcertifikatutfärdar fungera som en betrodd tredje part för att gå i god för legitimiteten för IoT-enheter när enheterna ansluter. Företag-X skulle välja det här alternativet om de avser Smart-X-Widget att interagera med produkter eller tjänster från tredje part efter första anslutningen till IoT Hub.

Om du vill köpa ett X.509 CA-certifikat väljer Company-X en rotcertifikattjänstleverantör. En sökning på Internet efter frasen "Root CA" kommer att ge bra leads. Rotcertifikatutfärdaren guidar Företag-X om hur du skapar det offentliga/privata nyckelparet och hur du genererar en begäran om certifikatsignering (CSR) för sina tjänster. En kundtjänstrepresentant är den formella processen att ansöka om ett certifikat från en certifikatutfärdare. Resultatet av det här inköpet är ett certifikat som kan användas som ett myndighetscertifikat. Med tanke på allestädes närvarande X.509 certifikat, är certifikatet sannolikt har formaterats korrekt till IETF: s RFC 5280 standard.

### <a name="creating-a-self-signed-x509-ca-certificate"></a>Skapa ett självsignerat X.509-certifikat

Processen för att skapa ett självsignerat X.509 CA-certifikat liknar inköp med undantag för att involvera en tredjeparts undertecknare som rotcertifikatutfärdaren. I vårt exempel signerar Company-X sitt myndighetscertifikat i stället för en rotcertifikatutfärdande. Company-X kan välja det här alternativet för testning tills de är redo att köpa ett myndighetscertifikat. Company-X kan också använda ett självsignerat X.509 CA-certifikat i produktion, om Smart-X-Widget inte är avsedd att ansluta till tjänster från tredje part utanför IoT Hub.

## <a name="register-the-x509-certificate-to-iot-hub"></a>Registrera X.509-certifikatet till IoT Hub

Company-X måste registrera X.509 CA till IoT Hub där det kommer att tjäna till att autentisera Smart-X-Widgets när de ansluter. Detta är en engångsprocess som gör det möjligt att autentisera och hantera valfritt antal Smart-X-Widget-enheter. Den här processen är en gång på grund av en 1:1-relation mellan myndighetscertifikat och enheter och utgör också en av de främsta fördelarna med att använda X.509 CA-autentiseringsmetoden. Alternativet är att ladda upp enskilda certifikat tumavtryck för varje Smart-X-Widget enhet och därmed lägga till driftskostnader.

Att registrera X.509 CA-certifikatet är en tvåstegsprocess, certifikatöverföringen och certifikatet som bevis på innehav.

![Registrera ett X509CA-certifikat](./media/iot-hub-x509ca-concept/pop-flow.png)

### <a name="x509-ca-certificate-upload"></a>Uppladdning av X.509 CA-certifikat

X.509 CA-certifikatuppladdningsprocessen är just det, ladda upp CA-certifikatet till IoT Hub. IoT Hub förväntar sig att certifikatet i en fil. Company-X laddar helt enkelt upp certifikatfilen. Certifikatfilen får inte under några omständigheter innehålla några privata nycklar. Bästa praxis från standarder som styr PKI (Public Key Infrastructure) ger mandat att kunskap om Company-X:s privata i detta fall uteslutande finns inom Company-X.

### <a name="proof-of-possession-of-the-certificate"></a>Bevis på innehav av intyget

X.509 CA-certifikatet är, precis som alla digitala certifikat, offentlig information som kan avlyssnas. Som sådan kan en tjuvlyssnare fånga upp ett certifikat och försöka ladda upp det som sin egen. I vårt exempel vill IoT Hub se till att ca-certifikatet Company-X laddar upp verkligen tillhör Company-X. Det gör det genom att utmana Company-X att bevisa att de faktiskt har certifikatet genom ett [bevis på innehav (PoP) flöde](https://tools.ietf.org/html/rfc5280#section-3.1). Proof-of-possession-flödet innebär att IoT Hub genererar ett slumptal som ska signeras av Company-X med hjälp av sin privata nyckel. Om Company-X följde PKI:s bästa praxis och skyddade sin privata nyckel skulle endast de kunna svara korrekt på beviskravsutmaningen. IoT Hub fortsätter att registrera X.509 CA-certifikatet på ett framgångsrikt svar på bevis-of-possession utmaning.

Ett framgångsrikt svar på proof-of-possession utmaning från IoT Hub kompletterar X.509 CA registrering.

## <a name="sign-devices-into-a-certificate-chain-of-trust"></a>Logga in enheter i en förtroendekedja

IoT kräver att varje enhet har en unik identitet. Dessa identiteter finns i formulärcertifikaten för certifikatbaserade autentiseringsscheman. I vårt exempel innebär detta att varje Smart-X-Widget måste ha ett unikt enhetscertifikat. Hur företag-X setup för detta i sin leveranskedja?

Ett sätt att gå tillväga är att iföra certifikat för Smart-X-Widgets och anförtro kunskap om motsvarande unika enhetsprivatnycklar med supply chain-partners. För Company-X innebär detta att man anförtror Factory-Y och Tekniker-Z. Även om detta är en giltig metod, det kommer med utmaningar som måste övervinnas för att säkerställa förtroende enligt följande:

1. Att behöva dela enhetsprivata nycklar med supply chain-partner, förutom att ignorera PKI:s bästa praxis att aldrig dela privata nycklar, gör det dyrt att bygga förtroende i leveranskedjan. Det innebär kapitalsystem som säkra rum för att hysa enhet privata nycklar, och processer som regelbundna säkerhetsgranskningar måste installeras. Båda lägger till kostnader i leveranskedjan.

2. Säkert redovisning av enheter i leveranskedjan och senare hantera dem i distributionen blir en en-till-en uppgift för varje nyckel-till-enhet par från den punkt av enhetens unika certifikat (därav privat nyckel) generation till enhetspensionering. Detta utesluter grupphantering av enheter om inte begreppet grupper uttryckligen är inbyggt i processen på något sätt. Säker redovisning och enhetens livscykelhantering blir därför en tung driftbörda. I vårt exempel skulle Company-X bära denna börda.

X.509 CA-certifikatautentisering erbjuder eleganta lösningar på tidigare listade utmaningar med hjälp av certifikatkedjor. En certifikatkedja är resultatet av en certifikatutfärdare som signerar en mellanliggande certifikatutfärdare som i sin tur signerar en annan mellanliggande certifikatutfärdare och fortsätter tills en slutlig mellanliggande certifikatutfärdare signerar en enhet. I vårt exempel tecknar Company-X Factory-Y, som i sin tur signerar Technician-Z som slutligen signerar Smart-X-Widget.

![Hierarki för certifikatkedja](./media/iot-hub-x509ca-concept/cert-chain-hierarchy.png)

Ovanför kaskad av certifikat i kedjan presenterar den logiska hand-off av myndigheten. Många leveranskedjor följer denna logiska hand-off där varje mellanliggande CA blir inloggad i kedjan samtidigt som de tar emot alla uppströms CA-certifikat, och den sista mellanliggande CA slutligen tecken varje enhet och injicera alla certifikat myndighet från kedjan in i enheten. Detta är vanligt när kontraktet tillverkande företag med en hierarki av fabriker provisioner en viss fabrik för att göra tillverkningen. Hierarkin kan vara flera nivåer djupa (till exempel efter geografi/produkttyp/tillverkningslinje), men endast fabriken i slutet får interagera med enheten men kedjan underhålls från toppen av hierarkin.

Alternativa kedjor kan ha olika mellanliggande ca interagera med enheten i vilket fall certifikatutfärdaren interagerar med enheten injicerar certifikatkedja innehåll vid den tidpunkten. Hybridmodeller är också möjliga om endast en del av certifikatutfärdaren har fysisk interaktion med enheten.

I vårt exempel interagerar både Factory-Y och Technician-Z med Smart-X-Widget. Medan Company-X äger Smart-X-Widget, interagerar det faktiskt inte fysiskt med den i hela leveranskedjan. Certifikatkedjan för förtroende för Smart-X-Widget består därför av Company-X-signering Factory-Y som i sin tur undertecknar Tekniker-Z som sedan kommer att ge slutlig signatur till Smart-X-Widget. Tillverkningen och installationen av Smart-X-Widget omfattar Factory-Y och Technician-Z med hjälp av sina respektive mellanliggande CA-certifikat för att signera varje Smart-X-Widgets. Slutresultatet av hela den här processen är Smart-X-Widgets med unika enhetscertifikat och certifikatkedja som går upp till Company-X CA-certifikat.

![Förtroendekedjan från ett företags cert till ett annat företags certs](./media/iot-hub-x509ca-concept/cert-mfr-chain.png)

Detta är en bra punkt att granska värdet av X.509 CA-metoden. Istället för att förgenerera och lämna ut certifikat för varje Smart-X-Widget i leveranskedjan, var Company-X bara tvungen att underteckna Factory-Y en gång. Istället för att behöva spåra alla enheter under enhetens hela livscykel kan Company-X nu spåra och hantera enheter genom grupper som naturligt kommer ut ur leveranskedjan, till exempel enheter som installerats av Tekniker-Z efter juli något år.

Sist men inte minst ingjuter ca-metoden för autentisering säker ansvarsskyldighet i leveranskedjan för tillverkning av enhet. På grund av certifikatkedjans process registreras och kanseraråtgärderna för varje medlem i kedjan kryptografiskt registreras och kan verifieras.

Denna process bygger på vissa antaganden som måste tas upp för fullständighet. Det kräver oberoende skapande av enhetens unika offentliga/privata nyckelpar och att den privata nyckeln skyddas i enheten. Lyckligtvis finns säkra kiselchips i form av Hardware Secure Modules (HSM) som kan generera nycklar internt och skydda privata nycklar. Företag-X behöver bara lägga till en av sådana marker i Smart-X-Widgets komponent räkning av material.

## <a name="device-connection"></a>Enhetsanslutning

Tidigare avsnitt ovan har byggt upp till enhetsanslutning. Genom att helt enkelt registrera ett X.509 CA-certifikat till IoT Hub en gång, hur kan miljontals enheter ansluta och autentiseras från första gången?  Enkelt; genom samma certifikatuppladdning och bevis på innehavsflöde som vi tidigare stötte på när du registrerade X.509 CA-certifikatet.

Enheter som tillverkas för X.509 CA-autentisering är utrustade med enhetsunika certifikat och en certifikatkedja från deras respektive tillverkningsförsörjningskedja. Enhetsanslutning, även för första gången, sker i en tvåstegsprocess: uppladdning av certifikatkedja och bevis på innehav.

Under uppladdningen av certifikatkedjan laddar enheten upp sitt enhets unika certifikat tillsammans med certifikatkedjan som är installerad i den till IoT Hub. Med hjälp av det förregistrerade X.509 CA-certifikatet kan IoT Hub kryptografiskt validera ett par saker, att den uppladdade certifikatkedjan är internt konsekvent och att kedjan har sitt ursprung i den giltiga ägaren av X.509 CA-certifikatet. Bara var med X.509 CA registreringsprocessen, IoT Hub skulle inleda ett bevis på innehav utmaning-svar process för att fastställa att kedjan och därmed enhetscertifikat faktiskt tillhör enheten ladda upp den. Det gör det genom att generera en slumpmässig utmaning som ska signeras av enheten med hjälp av dess privata nyckel för validering av IoT Hub. Ett lyckat svar utlöser IoT Hub för att acceptera enheten som autentisk och bevilja den anslutning.

I vårt exempel skulle varje Smart-X-Widget ladda upp sitt enhets unika certifikat tillsammans med Factory-Y- och Technician-Z X.509 CA-certifikat och sedan svara på proof-of-possession-utmaningen från IoT Hub.

![Flöda från ett certifikat till ett annat, poputmaning från nav](./media/iot-hub-x509ca-concept/device-pop-flow.png)

Observera att grunden för förtroende vilar på att skydda privata nycklar, inklusive privata enhetsnycklar. Vi kan därför inte nog betona vikten av säkra kiselchips i form av Hardware Secure Modules (HSM) för att skydda privata nycklar för enheten, och den övergripande bästa praxisen att aldrig dela några privata nycklar, som en fabrik som anförtror en annan med sina privat nyckel.