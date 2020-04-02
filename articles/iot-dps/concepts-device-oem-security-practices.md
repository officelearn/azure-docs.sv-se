---
title: Säkerhetspraxis för tillverkare – Azure IoT-enhetsetableringstjänst
description: Översikter vanliga säkerhetsmetoder för OEM-tillverkare och enhetstillverkare som förbereder enheter för att registrera sig i DPS (Azure IoT Device Provisioning Service).
author: timlt
ms.author: timlt
ms.date: 3/02/2020
ms.topic: conceptual
ms.service: iot-dps
ms.custom: iot-p0-scenario, iot-devices-deviceOEM
ms.reviewer: nberdy
ms.openlocfilehash: 3854f353e4ea0b78c0162681e0b89d37419105d8
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529524"
---
# <a name="security-practices-for-azure-iot-device-manufacturers"></a>Säkerhetsrutiner för Azure IoT-enhetstillverkare
När fler tillverkare släpper IoT-enheter är det bra att identifiera vägledning kring vanliga metoder. Den här artikeln sammanfattar rekommenderade säkerhetsmetoder att tänka på när du tillverkar enheter för användning med DPS (Azure IoT Device Provisioning Service).  

> [!div class="checklist"]
> * Välja alternativ för enhetsautentisering
> * Installera certifikat på IoT-enheter
> * Integrera en TPM (Trusted Platform Module) i tillverkningsprocessen

## <a name="selecting-device-authentication-options"></a>Välja alternativ för enhetsautentisering
Det yttersta syftet med alla IoT-enhetssäkerhetsåtgärd är att skapa en säker IoT-lösning. Men frågor som maskinvarubegränsningar, kostnader och säkerhetsexpertis påverkar alla vilka alternativ du väljer. Dessutom påverkar din inställning till säkerhet hur dina IoT-enheter ansluter till molnet. Det finns [flera element av IoT-säkerhet](https://www.microsoft.com/research/publication/seven-properties-highly-secure-devices/) att tänka på, men ett nyckelelement som varje kund stöter på är vilken autentiseringstyp som ska användas. 

Tre allmänt använda autentiseringstyper är X.509-certifikat, TPM (Trusted Platform Modules) och symmetriska nycklar. Andra autentiseringstyper finns, men de flesta kunder som bygger lösningar på Azure IoT använder en av dessa tre typer. Resten av den här artikeln undersöker för- och nackdelar med att använda varje autentiseringstyp.

### <a name="x509-certificate"></a>X.509 certifikat
X.509-certifikat är en typ av digital identitet som du kan använda för autentisering. Certifikatstandarden X.509 är dokumenterad i [IETF RFC 5280](https://tools.ietf.org/html/rfc5280). I Azure IoT finns det två sätt att autentisera certifikat:
- Stämpel. En tumavtrycksalgoritm körs på ett certifikat för att generera en hexadecimal sträng. Den genererade strängen är en unik identifer för certifikatet. 
- CA-autentisering baserat på en fullständig kedja. En certifikatkedja är en hierarkisk lista över alla certifikat som behövs för att autentisera ett EE-certifikat (end-entity). För att autentisera ett EE-certifikat är det nödvändigt att autentisera varje certifikat i kedjan, inklusive en betrodd rotcertifikatutfärdaren. 

Fördelar för X.509:
- X.509 är den säkraste autentiseringstypen som stöds i Azure IoT.
- X.509 tillåter en hög kontrollnivå för certifikathantering.
- Många leverantörer är tillgängliga för att tillhandahålla X.509-baserade autentiseringslösningar.

Nackdelar för X.509:
- Många kunder kan behöva förlita sig på externa leverantörer för sina certifikat.
- Certifikathantering kan vara kostsamt och ökar den totala lösningskostnaden.
- Certifikat livscykelhantering kan vara svårt om logistiken inte är väl genomtänkt. 

### <a name="trusted-platform-module-tpm"></a>Trusted Platform Module (TPM)
TPM, även känd som [ISO/IEC 11889](https://www.iso.org/standard/66510.html), är en standard för att säkert generera och lagra kryptografiska nycklar. TPM refererar också till en virtuell eller fysisk I/O-enhet som interagerar med moduler som implementerar standarden. En TPM-enhet kan finnas som diskret maskinvara, integrerad maskinvara, en firmwarebaserad modul eller en programvarubaserad modul. 

Det finns två viktiga skillnader mellan TPM:er och symmetriska nycklar: 
- TPM-chips kan också lagra X.509-certifikat.
- TPM-attestation i DPS använder TPM-godkännandenyckeln (EK), en form av asymmetrisk autentisering. Med asymmetrisk autentisering används en offentlig nyckel för kryptering och en separat privat nyckel används för dekryptering. Symmetriska nycklar använder däremot symmetrisk autentisering, där den privata nyckeln används för både kryptering och dekryptering. 

Fördelar för TPM:
- TPM ingår som standardmaskinvara på många Windows-enheter, med inbyggt stöd för operativsystemet. 
- TPM attestation är lättare att skydda än delad åtkomst signatur (SAS) token-baserade symmetrisk nyckel attestation.
- Du kan enkelt gå ut och förnya, eller rulla, enhetsautentiseringsuppgifter. DPS rullar automatiskt IoT Hub-autentiseringsuppgifterna när en TPM-enhet ska återetableras.

Nackdelar för TPM: 
- TPM:er är komplexa och kan vara svåra att använda. 
- Programutveckling med TPM är svårt om du inte har en fysisk TPM eller en kvalitetsemulator.
- Du kanske måste omforma enhetens anslagstavla så att den innehåller en TPM i maskinvaran. 
- Om du rullar EK på en TPM förstör den TPM:ens identitet och skapar en ny. Även om det fysiska chippet förblir detsamma har det en ny identitet i din IoT-lösning.

### <a name="symmetric-key"></a>Symmetrisk nyckel
Med symmetriska tangenter används samma nyckel för att kryptera och dekryptera meddelanden. Därför är samma nyckel känd för både enheten och tjänsten som autentiserar den. Azure IoT stöder SAS tokenbaserade symmetriska nyckelanslutningar. Symmetrisk nyckelautentisering kräver ett betydande ägaransvar för att skydda nycklarna och uppnå en lika hög säkerhetsnivå med X.509-autentisering. Om du använder symmetriska nycklar är den rekommenderade metoden att skydda nycklarna med hjälp av en maskinvarusäkerhetsmodul (HSM).

Fördelar för symmetrisk nyckel:
- Att använda symmetriska nycklar är det enklaste och lägsta kostnadssättet för att komma igång med autentisering.
- Med hjälp av symmetriska nycklar effektiviserar din process eftersom det inte finns något extra att generera. 

Nackdelar för symmetrisk nyckel: 
- Symmetriska nycklar tar en betydande grad av ansträngning för att säkra nycklarna. Samma nyckel delas mellan enhet och moln, vilket innebär att nyckeln måste skyddas på två ställen. Utmaningen med TPM- och X.509-certifikat bevisar däremot att den offentliga nyckeln har haft utan att avslöja den privata nyckeln.
- Symmetriska nycklar gör det enkelt att följa dålig säkerhet. En vanlig tendens med symmetriska nycklar är att hårt koda okrypterade nycklar på enheter. Även om denna praxis är bekvämt, lämnar det nycklarna sårbara. Du kan minska vissa risker genom att säkert lagra den symmetriska nyckeln på enheten. Men om din prioritet i slutändan är säkerhet snarare än bekvämlighet, använd X.509 certifikat eller TPM för autentisering. 

### <a name="shared-symmetric-key"></a>Delad symmetrisk nyckel
Det finns en variant av symmetrisk nyckelautentisering som kallas delad symmetrisk nyckel. Den här metoden innebär att du använder samma symmetriska nyckel i alla enheter. Rekommendationen är att undvika att använda delade symmetriska nycklar på dina enheter. 

Pro för delad symmetrisk nyckel:
- Enkelt att implementera och billigt att producera i stor skala. 

Nackdelar för delad symmetrisk nyckel: 
- Mycket sårbar för angrepp. Fördelen med ett enkelt genomförande uppvägs långt av risken. 
- Vem som helst kan personifiera dina enheter om de får den delade nyckeln.
- Om du förlitar dig på en delad symmetrisk nyckel som äventyras, kommer du sannolikt att förlora kontrollen över enheterna. 

### <a name="making-the-right-choice-for-your-devices"></a>Göra rätt val för dina enheter
Om du vill välja en autentiseringsmetod måste du tänka på fördelarna och kostnaderna för varje metod för din unika tillverkningsprocess.  För enhetsautentisering finns det vanligtvis en omvänd relation mellan hur säker en viss metod är och hur bekvämt det är.  

## <a name="installing-certificates-on-iot-devices"></a>Installera certifikat på IoT-enheter
Om du använder X.509-certifikat för att autentisera dina IoT-enheter innehåller det här avsnittet vägledning om hur du integrerar certifikat i tillverkningsprocessen. Du måste fatta flera beslut.  Dessa inkluderar beslut om vanliga certifikatvariabler, när certifikat ska genereras och när de ska installeras. 

Om du är van vid att använda lösenord kan du fråga varför du inte kan använda samma certifikat på alla dina enheter, på samma sätt som du skulle kunna använda samma lösenord på alla dina enheter. För det första är det farligt att använda samma lösenord överallt. Praxis har utsatt företag för stora DDoS-attacker, inklusive den som tog ner DNS på den amerikanska östkusten för flera år sedan. Använd aldrig samma lösenord överallt, även med personliga konton. För det andra är ett certifikat inte ett lösenord, det är en unik identitet. Ett lösenord är som en hemlig kod som alla kan använda för att öppna en dörr på en säker byggnad.  Det är något du vet, och du kan ge lösenordet till vem som helst att få entré.  Ett certifikat är som ett körkort med ditt foto och andra detaljer, som du kan visa för en vakt för att komma in i en säker byggnad. Den är knuten till vem du är.  Förutsatt att vakten korrekt matchar personer med körkort, bara du kan använda ditt körkort (identitet) för att få entré. 

### <a name="variables-involved-in-certificate-decisions"></a>Variabler som deltar i certifikatbeslut
Tänk på följande variabler och hur var och en påverkar den övergripande tillverkningsprocessen. 

#### <a name="where-the-certificate-root-of-trust-comes-from"></a>Var certifikatroten av förtroende kommer ifrån
Det kan vara kostsamt och komplicerat att hantera en infrastruktur för offentliga nycklar (PKI).  Speciellt om ditt företag inte har någon erfarenhet av att hantera en PKI. Alternativen är:
- Använd en PKI från tredje part. Du kan köpa mellanliggande signeringscertifikat från en tredjepartscertifikatleverantör. Du kan också använda en privat certifikatutfärdarmyndighet. 
- Använd en självhanterad PKI. Du kan underhålla ditt eget PKI-system och generera dina egna certifikat.
- Använd säkerhetstjänsten [Azure Sphere.](https://azure.microsoft.com/services/azure-sphere/) Det här alternativet gäller endast Azure Sphere-enheter. 

#### <a name="where-certificates-are-stored"></a>Var certifikat lagras
Det finns några faktorer som påverkar beslutet om var certifikat lagras. Dessa faktorer inkluderar typ av enhet, förväntade vinstmarginaler (om du har råd med säker lagring), enhetsfunktioner och befintlig säkerhetsteknik på den enhet som du kanske kan använda. Tänk på följande alternativ:
- I en maskinvarusäkerhetsmodul (HSM). Användning av en HSM rekommenderas starkt. Kontrollera om enhetens kontrollkort redan har en HSM installerad. Om du vet att du inte har en HSM kan du arbeta med maskinvarutillverkaren för att identifiera en HSM som uppfyller dina behov.
- På en säker plats på disken, till exempel en betrodd körningsmiljö (TEE).
- I det lokala filsystemet eller i ett certifikatarkiv. Till exempel Windows-certifikatarkivet. 

#### <a name="connectivity-at-the-factory"></a>Anslutning på fabriken
Anslutningen på fabriken avgör hur och när du får certifikaten att installera på enheterna. Anslutningsalternativen är följande:
- Anslutning. Att ha anslutning är optimalt, det effektiviserar processen att generera certifikat lokalt. 
- Ingen anslutning. I det här fallet använder du ett signerat certifikat från en certifikatutfärdare för att generera enhetscertifikat lokalt och offline. 
- Ingen anslutning. I det här fallet kan du hämta certifikat som genererades i förväg. Du kan också använda en offline-PKI för att generera certifikat lokalt.

#### <a name="audit-requirement"></a>Revisionskrav
Beroende på vilken typ av enheter du producerar kan du ha ett regelkrav för att skapa en granskningsspårning över hur enhetsidentiteter installeras på dina enheter. Granskning tillför betydande produktionskostnad. Så i de flesta fall, bara göra det om det behövs. Om du är osäker på om en revision krävs kontaktar du företagets juridiska avdelning. Granskningsalternativ är: 
- Inte en känslig bransch. Ingen granskning krävs.
- Känslig industri. Certifikat bör installeras i ett säkert rum enligt kraven för efterlevnadscertifiering. Om du behöver ett säkert rum för att installera certifikat är du förmodligen redan medveten om hur certifikat installeras på dina enheter. Och du har förmodligen redan ett revisionssystem på plats. 

#### <a name="length-of-certificate-validity"></a>Intygets giltighetstid
Precis som ett körkort har certifikaten ett utgångsdatum som anges när de skapas. Här är alternativen för certifikatets giltighetstid:
- Förnyelse krävs inte.  Den här metoden använder en lång förnyelseperiod, så du behöver aldrig förnya certifikatet under enhetens livstid. Även om ett sådant tillvägagångssätt är bekvämt, det är också riskabelt.  Du kan minska risken genom att använda säker lagring som en HSM på dina enheter. Den rekommenderade metoden är dock att undvika att använda långlivade certifikat.
- Förnyelse krävs.  Du måste förnya certifikatet under enhetens livstid. Certifikatets giltighetstid beror på sammanhanget och du behöver en strategi för förnyelse.  Strategin bör omfatta var du får certifikat och vilken typ av over-the-air-funktioner dina enheter har att använda i förnyelseprocessen. 

### <a name="when-to-generate-certificates"></a>När ska certifikat genereras
Internetanslutningsfunktionerna på din fabrik påverkar din process för att generera certifikat. Du har flera alternativ för när du ska generera certifikat: 

- Förinstallerade certifikat.  Vissa HSM-leverantörer erbjuder en premiumtjänst där HSM-leverantören installerar certifikat för kunden. Först ger kunderna HSM-leverantören åtkomst till ett signeringscertifikat. Sedan hsm-leverantören installerar certifikat som undertecknats av signeringscertifikatet på varje HSM som kunden köper. Allt kunden behöver göra är att installera HSM på enheten. Även om den här tjänsten tillför kostnader, hjälper den till att effektivisera din tillverkningsprocess.  Och det löser frågan om när certifikat ska installeras.
- Enhetsgenererade certifikat.  Om dina enheter genererar certifikat internt måste du extrahera det offentliga X.509-certifikatet från enheten för att registrera det i DPS. 
- Ansluten fabrik.  Om din fabrik har anslutning kan du generera enhetscertifikat när du behöver dem.
- Offline fabrik med din egen PKI. Om din fabrik inte har någon anslutning och du använder din egen PKI med offlinesupport kan du generera certifikaten när du behöver dem.
- Offline fabrik med tredje part PKI. Om din fabrik inte har någon anslutning och du använder en PKI från tredje part måste du generera certifikaten i förväg. Och det kommer att bli nödvändigt att generera certifikat från en plats som har anslutning. 

### <a name="when-to-install-certificates"></a>När du ska installera certifikat
När du har genererat certifikat för dina IoT-enheter kan du installera dem på enheterna. 

Om du använder förinlästa certifikat med en HSM förenklas processen. När HSM har installerats i enheten kan enhetskoden komma åt den. Sedan ringer du HSM API:er för att komma åt certifikatet som lagras i HSM. Detta tillvägagångssätt är det mest praktiska för din tillverkningsprocess. 

Om du inte använder ett förinläst certifikat måste du installera certifikatet som en del av produktionsprocessen. Den enklaste metoden är att installera certifikatet i HSM samtidigt som du blinkar den ursprungliga firmware bilden. Processen måste lägga till ett steg för att installera avbildningen på varje enhet. Efter det här steget kan du köra slutliga kvalitetskontroller och andra steg innan du paketerar och skickar enheten. 

Det finns programvaruverktyg som gör att du kan köra installationsprocessen och den slutliga kvalitetskontrollen i ett enda steg. Du kan ändra dessa verktyg för att generera ett certifikat eller hämta ett certifikat från ett förgenererat certifikatarkiv. Då programvaran kan installera certifikatet där du behöver installera den. Med programvaruverktyg av den här typen kan du köra tillverkning av produktionskvalitet i stor skala. 

När du har installerat certifikat på dina enheter är nästa steg att lära dig hur du registrerar enheterna med [DPS](about-iot-dps.md). 

## <a name="integrating-a-tpm-into-the-manufacturing-process"></a>Integrera en TPM i tillverkningsprocessen
Om du använder en TPM för att autentisera dina IoT-enheter innehåller det här avsnittet vägledning. Vägledningen omfattar de ofta använda TPM 2.0-enheter som har HMAC-nyckelstöd (Hash-baserad meddelandeautentiseringskod). TPM-specifikationen för TPM-chips är en ISO-standard som underhålls av Trusted Computing Group. Mer information om TPM finns i specifikationerna för [TPM 2.0](https://trustedcomputinggroup.org/tpm-library-specification/) och [ISO/IEC 11889](https://www.iso.org/standard/66510.html). 

### <a name="taking-ownership-of-the-tpm"></a>Att ta ansvar för TPM
A critical step in manufacturing a device with a TPM chip is to take ownership of the TPM. Det här steget krävs så att du kan ange en nyckel till enhetens ägare. Det första steget är att extrahera bekräftelsenyckeln (EK) från enheten. Nästa steg är att faktiskt göra anspråk på äganderätt. Hur du åstadkommer detta beror på vilket TPM och operativsystem du använder. Om det behövs kontaktar du TPM-tillverkaren eller utvecklaren av enhetens operativsystem för att avgöra hur du gör anspråk på ägarskap. 

I din tillverkningsprocess kan du extrahera EK och göra anspråk på ägarskap vid olika tidpunkter, vilket ger flexibilitet. Många tillverkare utnyttjar den här flexibiliteten genom att lägga till en maskinvarusäkerhetsmodul (HSM) för att öka säkerheten för sina enheter. Det här avsnittet innehåller vägledning om när EK ska extraheras, när du ska göra anspråk på äganderätten till TPM:en, och överväganden för att integrera dessa steg i en tillverkningstidslinje. 

> [!IMPORTANT]
> Följande vägledning förutsätter att du använder en diskret, firmware eller integrerad TPM. På platser där det är tillämpligt lägger vägledningen till anteckningar om hur du använder en icke-diskret eller programvara TPM. Om du använder en TPM för programvara kan det finnas ytterligare steg som den här vägledningen inte innehåller. Program-TPM:er har en mängd olika implementeringar som ligger utanför den här artikelns räckvidd.  I allmänhet är det möjligt att integrera en programvara TPM i följande allmänna tillverkning tidslinje. Men medan en programvara emulerade TPM är lämplig för prototyper och testning, kan det inte ge samma säkerhetsnivå som en diskret, firmware eller integrerad TPM. Som en allmän praxis, undvika att använda en programvara TPM i produktion.

### <a name="general-manufacturing-timeline"></a>Allmän tillverkningstidslinje
Följande tidslinje visar hur en TPM går igenom en produktionsprocess och hamnar i en enhet. Varje tillverkningsprocess är unik och den här tidslinjen visar de vanligaste mönstren. Tidslinjen ger vägledning om när vissa åtgärder ska vidtas med tangenterna. 

#### <a name="step-1-tpm-is-manufactured"></a>Steg 1: TPM tillverkas
- Om du köper TPM:er från en tillverkare för användning på dina enheter kan du se om de extraherar offentliga godkännandenycklar (EK_pubs) åt dig. Det är användbart om tillverkaren tillhandahåller en lista över EK_pubs med de levererade enheterna. 
    > [!NOTE]
    > Du kan ge TPM-tillverkaren skrivbehörighet till registreringslistan med hjälp av principer för delad åtkomst i etableringstjänsten.  Med den här metoden kan de lägga till TPM:er i registreringslistan åt dig.  Men det är tidigt i tillverkningsprocessen, och det kräver förtroende för TPM tillverkaren. Gör det på egen risk. 

- Om du tillverkar TPM:er att sälja till enhetstillverkare kan du överväga att ge dina kunder en lista över EK_pubs tillsammans med deras fysiska TPM:er.  Att ge kunderna EK_pubs sparar ett steg i deras process. 
- Om du tillverkar TPM:er att använda med dina egna enheter identifierar du vilken punkt i processen som är mest praktiskt att extrahera EK_pub. Du kan extrahera EK_pub vid någon av de återstående punkterna i tidslinjen. 

#### <a name="step-2-tpm-is-installed-into-a-device"></a>Steg 2: TPM installeras i en enhet
Vid denna punkt i produktionsprocessen bör du veta vilken DPS-instans enheten kommer att användas med. Därför kan du lägga till enheter i registreringslistan för automatisk etablering. Mer information om automatisk enhetsetablering finns i [DPS-dokumentationen](about-iot-dps.md).
- Om du inte har extraherat EK_pub, nu är en bra tid att göra det. 
- Beroende på installationsprocessen för TPM, är detta steg också ett bra tillfälle att bli ägare till TPM. 

#### <a name="step-3-device-has-firmware-and-software-installed"></a>Steg 3: Enheten har inbyggd programvara och programvara installerad
Installera dps-klienten tillsammans med ID-scopet och den globala URL:en för etablering.
- Nu är sista chansen att utvinna EK_pub. Om en tredje part installerar programvaran på enheten är det en bra idé att extrahera EK_pub först.
- Denna punkt i tillverkningsprocessen är idealisk för att ta ansvar för TPM.  
    > [!NOTE]
    > Om du använder en TPM-programvara kan du installera den nu.  Extrahera EK_pub samtidigt.

#### <a name="step-4-device-is-packaged-and-sent-to-the-warehouse"></a>Steg 4: Enheten paketerar och skickas till lagret
En enhet kan sitta i ett lager i 6-12 månader innan den distribueras. 

#### <a name="step-5-device-is-installed-into-the-location"></a>Steg 5: Enheten är installerad på platsen
När enheten anländer till sin slutliga plats, går den igenom automatisk etablering med DPS.

Mer information finns i [Begrepp för automatisk etablering](concepts-auto-provisioning.md) och [TPM-intyg](concepts-tpm-attestation.md). 

## <a name="resources"></a>Resurser

Utöver de rekommenderade säkerhetsrutinerna i den här artikeln tillhandahåller Azure IoT resurser för att hjälpa dig att välja säker maskinvara och skapa säkra IoT-distributioner: 
- Azure [IoT-säkerhetsrekommendationer](../iot-fundamentals/security-recommendations.md) för att styra distributionsprocessen. 
- [Azure Security Center](https://azure.microsoft.com/services/security-center/) erbjuder en tjänst som hjälper till att skapa säkra IoT-distributioner. 
- Om du vill ha hjälp med att utvärdera maskinvarumiljön finns i vitboken [Utvärdera din IoT-säkerhet](https://download.microsoft.com/download/D/3/9/D3948E3C-D5DC-474E-B22F-81BA8ED7A446/Evaluating_Your_IOT_Security_whitepaper_EN_US.pdf). 
- Mer information om hur du väljer säker maskinvara finns i [Rätt säker maskinvara för din IoT-distribution](https://download.microsoft.com/download/C/0/5/C05276D6-E602-4BB1-98A4-C29C88E57566/The_right_secure_hardware_for_your_IoT_deployment_EN_US.pdf). 