---
title: Vad är Dedikerad HSM? – Dedikerad HSM i Azure| Microsoft Docs
description: Översikten över dedikerad HSM i Azure tillhandahåller funktioner för lagring av nycklar i Azure som uppfyller FIPS 140-2 Level 3-certifiering
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc, seodec18
ms.date: 12/07/2018
ms.author: mbaldwin
ms.openlocfilehash: ad3c9d702384b8a32a9d4f0c8aebe44de4bb526e
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80337178"
---
# <a name="troubleshooting"></a>Felsökning

Azure Dedikerad HSM-tjänsten har två distinkta aspekter. För det första registrering och distribution i Azure av HSM-enheter med sina underliggande nätverkskomponenter. För det andra, konfigurationen av HSM-enheter som förberedelse för användning / integration med en viss arbetsbelastning eller program. Även om Thales Luna Network HSM-enheter är desamma i Azure som du skulle köpa direkt från Thales, skapar det faktum att de är en resurs i Azure några unika överväganden. Dessa överväganden och eventuella felsökningsinsikter eller metodtips dokumenteras här för att säkerställa hög synlighet och åtkomst till viktig information. När tjänsten används är slutgiltig information tillgänglig via supportbegäranden direkt till Antingen Microsoft eller Thales. 

> [!NOTE]
> Det bör noteras att innan någon konfiguration utförs på en nyligen distribuerad HSM-enhet, bör den uppdateras med alla relevanta korrigeringar. En specifik obligatorisk patch är [KB0019789](https://supportportal.gemalto.com/csm?id=kb_article_view&sys_kb_id=19a81c8bdb9a1fc8d298728dae96197d&sysparm_article=KB0019789) i Thales supportportal som åtgärdar en omstart hänga problem.

## <a name="hsm-registration"></a>HSM-registrering

Dedikerad HSM är inte fritt tillgänglig för användning eftersom den levererar maskinvaruresurser i molnet och därför är en värdefull resurs som behöver skyddas. Vi använder därför en vitlistningsprocess via e-post med HSMrequest@microsoft.com. 

### <a name="getting-access-to-dedicated-hsm"></a>Få tillgång till dedikerad HSM

Om du tror att dedikerad HSM passar HSMrequest@microsoft.com dina viktigaste lagringskrav, sedan e-post för att begära åtkomst. Beskriv ditt program, de regioner som du vill ha HSM:er och volymen på HSM:er som du letar efter. Om du till exempel arbetar med en Microsoft-representant, till exempel en Account Executive eller Cloud Solution Architect, inkluderar du dem i alla förfrågningar.

## <a name="hsm-provisioning"></a>HSM-etablering

Etablering av en HSM-enhet i Azure kan göras via antingen CLI eller PowerShell. När du registrerar dig för tjänsten kommer en exempel-ARM-mall att tillhandahållas och hjälp ges för inledande anpassning. 

### <a name="hsm-deployment-failure-information"></a>Information om fel i HSM-distribution

Dedikerad HSM stöder CLI och PowerShell för distribution så portalbaserad felinformation är begränsad och inte utförlig. Bättre information kan hittas med hjälp av Resource Explorer. Portalens hemsida har en ikon för detta och mer detaljerad felinformation finns tillgänglig. Den här informationen hjälper mycket om du klistrar in när du skapar en supportbegäran som är relaterad till distributionsproblem.

![Information om fel](./media/troubleshoot/failure-information.png)

### <a name="hsm-subnet-delegation"></a>HSM-delegering av undernät
Den främsta orsaken till distributionsfel är att glömma att ange lämplig delegering för det kunddefinierade undernätet där HSM:erna ska etableras. Ange att delegering är en del av VNet och undernät förutsättningar för distribution och mer information finns i självstudierna.

![Delegering av undernät](./media/troubleshoot/subnet-delegation.png)

### <a name="hsm-deployment-race-condition"></a>Tillstånd för HSM-driftsättningsrace

Standard-ARM-mallen som tillhandahålls för distribution har HSM- och ExpressRoute-gatewayrelaterade resurser. Nätverksresurser är ett beroende för en lyckad HSM-distribution och tidpunkten kan vara avgörande.  Ibland har vi sett distributionsfel relaterade till beroendeproblem och att köra om distributionen löser ofta problemet. Om inte, är det ofta framgångsrikt att ta bort resurser och sedan distribuera om. När du har försökt med det här och fortfarande har hittat problemet kan du ta upp en supportbegäran i Azure-portalen och välja problemtypen "Problem med att konfigurera Azure-installationen".

### <a name="hsm-deployment-using-terraform"></a>HSM-distribution med Terraform

Några kunder har använt Terraform som en automationsmiljö i stället för ARM-mallar som levereras vid registrering för den här tjänsten. HSM:erna kan inte distribueras på det här sättet, men de beroende nätverksresurserna kan. Terraform har en modul att ropa till en minimal ARM-mall som skjuter har HSM-distributionen.  I det här fallet bör man se till att nätverksresurser som den nödvändiga ExpressRoute Gateway distribueras fullt ut innan du distribuerar HSM.In this situation, care to ensure networking resources such as the required ExpressRoute Gateway are fully deployed before deploying HSM. Följande CLI-kommando kan användas för att testa för slutförd distribution och integreras efter behov. Byt ut vinkelfästets platshållare för just ditt namn. Du bör leta efter ett resultat av "provisioningState lyckades"

```azurecli
az resource show --ids /subscriptions/<subid>/resourceGroups/<myresourcegroup>/providers/Microsoft.Network/virtualNetworkGateways/<myergateway>
```

### <a name="deployment-failure-based-on-quota"></a>Distributionsfel baserat på kvot
Distributioner kan misslyckas om du överskrider 2 HSM per stämpel och 4 HSM per region. Undvik den här situationen genom att se till att du har tagit bort resurser från tidigare misslyckade distributioner innan du distribuerar igen. Se objektet "Hur ser jag HSM"nedan för att kontrollera resurser. Om du tror att du behöver överskrida denna kvot, HSMrequest@microsoft.com som främst finns där som en garanti, vänligen maila med detaljer.

### <a name="deployment-failure-based-on-capacity"></a>Distributionsfel baserat på kapacitet
När en viss stämpel eller region blir full, det vill än alla kostnadsfria HSM:er, kan detta leda till distributionsfel. Varje stämpel har 11 HSMs tillgängliga för kunder, vilket innebär 22 per region. Det finns också 3 reservdelar och 1 testenhet i varje stämpel. Om du tror att du kan HSMrequest@microsoft.com ha nått en gräns, sedan e-post för information om påfyllningsnivå av specifika frimärken.

###  <a name="how-do-i-see-hsms-when-provisioned"></a>Hur ser jag HSM:er när de etableras?
På grund av att dedikerad HSM är en vitlistad tjänst anses den vara en "dold typ" i Azure-portalen. Om du vill se HSM-resurserna måste du markera kryssrutan "Visa dolda typer" som visas nedan. Nätverkskortets resurs följer alltid HSM och är ett bra ställe att ta reda på IP-adressen för HSM innan du använder SSH för att ansluta.

![Delegering av undernät](./media/troubleshoot/hsm-provisioned.png)

## <a name="networking-resources"></a>Nätverksresurser

Distribution av dedikerad HSM har ett beroende av nätverksresurser och vissa följdbegränsningar att vara medveten om.

### <a name="provisioning-expressroute"></a>Etablera ExpressRoute

Dedikerad HSM använder ExpressRoute Gateway som en "tunnel" för kommunikation mellan kundens privata IP-adressutrymme och den fysiska HSM i ett Azure-datacenter.  Med tanke på att det finns en begränsning av en gateway per Vnet måste kunder som behöver anslutning till sina lokala resurser via ExpressRoute använda ett annat virtuella nätverk för den anslutningen.  

### <a name="hsm-private-ip-address"></a>HSM privat IP-adress

Exempelmallarna som tillhandahålls för dedikerad HSM förutsätter att HSM IP automatiskt tas från ett visst undernätsområde. Du kan ange en explicit IP-adress för HSM via attributet "NetworkInterfaces" i ARM-mallen. 

![Delegering av undernät](./media/troubleshoot/private-ip-address.png)

## <a name="hsm-initialization"></a>HSM-initiering

Initiering förbereder en ny HSM för användning, eller en befintlig HSM för återanvändning. Initieringen av HSM måste slutföras innan du kan generera eller lagra objekt, tillåta klienter att ansluta eller utföra kryptografiska åtgärder.

### <a name="lost-credentials"></a>Förlorade autentiseringsuppgifter

Förlust av Shell-administratörslösenordet kommer att resultera i förlust av HSM-nyckelmaterial. En supportbegäran bör göras för att återställa HSM.
När du initierar HSM, säkert lagra autentiseringsuppgifter. Shell- och HSM-referenser bör behållas i enlighet med företagets policyer.

### <a name="failed-logins"></a>Misslyckade inloggningar

Att ge felaktiga autentiseringsuppgifter till HSM:er kan få destruktiva konsekvenser. Följande är standardbeteenden för HSM-roller.

| Roll | Tröskel (antal försök) | Resultat av för många dåliga inloggningsförsök | Återställning |
|--|--|--|--|
| HSM SÅ | 3 |  HSM är nollställd (alla HSM-objektidentiteter och alla partitioner är borta)  |  HSM måste initieras på nytt. Innehållet kan återställas från säkerhetskopiering(er). | 
| Partition SO | 10 |  Partitionen är nollställd. |  Partitionen måste initieras på nytt. Innehållet kan återställas från säkerhetskopian. |  
| Granska | 10 | Lockout | Låses upp automatiskt efter 10 minuter. |  
| Kryptoofficer | 10 (kan minskas) | Om HSM-princip 15: Aktivera SO-återställning av partitions-PIN är inställd på 1 (aktiverat) är CO- och CU-rollerna utelåsta.<br>Om HSM-princip 15: Aktivera SO-återställning av partitions-PIN är inställd på 0 (inaktiverad), är CO- och CU-rollerna permanent utelåsta och partitionsinnehållet är inte längre tillgängligt. Det här är standardinställningen. | CO-rollen måste låsas upp och autentiseringsuppgifterna återställas av Partition SO med hjälp av `role resetpw -name co`.<br>Partitionen måste initieras om och nyckelmaterial återställas från en säkerhetskopieringsenhet. |  

## <a name="hsm-configuration"></a>HSM-konfiguration 

Följande objekt är situationer där konfigurationsfel antingen är vanliga eller har en effekt som är värd att ropa ut:

### <a name="hsm-documentation-and-software"></a>HSM Dokumentation och programvara
Programvara och dokumentation för Thales SafeNet Luna 7 HSM-enheter är inte tillgängliga från Microsoft och måste laddas ner direkt från Thales. Registrering krävs med hjälp av Thales kund-ID som tas emot under registreringsprocessen. Enheterna, enligt Microsoft, har programvaruversion 7.2 och firmware version 7.0.3. I början av 2020 Thales gjort dokumentation offentligt och det finns [här](https://thalesdocs.com/gphsm/luna/7.2/docs/network/Content/Home_network.htm).  

### <a name="hsm-networking-configuration"></a>HSM-nätverkskonfiguration

Var försiktig när du konfigurerar nätverken i HSM.  HSM har en anslutning via ExpressRoute Gateway från en privat IP-adress för kunder direkt till HSM.  Den här kommunikationskanalen är endast för kundkommunikation och Microsoft har ingen åtkomst. Om HSM är konfigurerat på ett sådant sätt att den här nätverkssökvägen påverkas, betyder det att all kommunikation med HSM tas bort.  I det här fallet är det enda alternativet att höja en Microsoft-supportbegäran via Azure-portalen för att få enheten återställd. Den här återställningsproceduren ställer HSM tillbaka till sitt ursprungliga tillstånd och allt konfigurations- och nyckelmaterial går förlorat.  Konfigurationen måste återskapas och när enheten ansluter till HA-gruppen kommer nyckelmaterialet att replikeras.  

### <a name="hsm-device-reboot"></a>HSM-enhet omstart

Vissa konfigurationsändringar kräver att HSM ska strömcykel eller startas om. Microsoft-testning av HSM i Azure fastställt att vid vissa tillfällen omstarten kunde hänga. Innebörden är att en supportbegäran måste skapas i Azure-portalen som begär hård omstart och det kan ta upp till 48 timmar att slutföra med tanke på att det är en manuell process i ett Azure-datacenter.  För att undvika den här situationen, se till att du har distribuerat omstart patch tillgänglig från Thales direkt. Se [KB0019789](https://supportportal.gemalto.com/csm?sys_kb_id=d66911e2db4ffbc0d298728dae9619b0&id=kb_article_view&sysparm_rank=1&sysparm_tsqueryId=d568c35bdb9a4850d6b31f3b4b96199e&sysparm_article=KB0019789) i Thales Luna Network HSM 7.2 Nedladdningar för en rekommenderad patch för en omstart hänga fråga (Obs: du måste ha registrerat sig i Thales supportportal för att ladda ner).

### <a name="ntls-certificates-out-of-sync"></a>NTLS-certifikat är inte synkroniserade
En klient kan förlora anslutningen till en HSM när ett certifikat upphör att gälla eller har skrivits över via konfigurationsuppdateringar. Certifikatutväxlingsklientkonfigurationen bör tillämpas på nytt med varje HSM.
Exempel på NTLS-loggning med ogiltigt certifikat:

> NTLS[8508]: info : 0 : Inkommande anslutningsbegäran... : 192.168.50.2/59415 NTLS[8508]: Felmeddelande från SSLAccept är : error:14094418:SSL-rutiner:ssl3_read_bytes:tlsv1 alert unknown ca NTLS[8508]: Fel under SSL accept (RC_SSL_ERROR ) NTLS[8508]: info : 0xc0000711 : Misslyckas med att upprätta en säker kanal med klient : 192.168.50.2/59415 : RC_SSL_FAILED_HANDSHAKE NTLS[8508]: info : 0 : NTLS-klienten "Okänt värdnamn" Anslutningsinstansen borttagen : 192.168.50.2/59415

### <a name="failed-tcp-communication"></a>Misslyckad TCP-kommunikation

Kommunikation från Luna Client-installationen till HSM kräver minst TCP-port 1792. Tänk på detta som alla nätverkskonfigurationer ändras i miljön.

### <a name="failed-ha-group-member-doesnt-recover"></a>Misslyckad HA-gruppmedlem återställs inte

Om en misslyckad HA-gruppmedlem inte återställs måste den återställas manuellt från Luna-klienten med kommandot hagroup recover.
Det är nödvändigt att konfigurera ett antal nya försök för en HA-grupp för att aktivera automatisk återställning. Som standard försöker en HA-grupp inte återställa en HA-medlem i gruppen när den återställs.

### <a name="ha-group-doesnt-sync"></a>HA-gruppen synkroniseras inte

Om medlemspartitioner inte har samma kloningsdomän visas följande kommandot ha synchronize: Varning: Synkronisering kan misslyckas.  Medlemmarna i fack 0 och plats 1 har motstridiga inställningar för privat nyckelkloning.
En ny partition med rätt kloningsdomän bör läggas till i HA-gruppen, följt av att den felaktigt konfigurerade partitionen tas bort.

## <a name="hsm-deprovisioning"></a>HSM avetablering 

Endast när helt klar med en HSM kan det avetableras och sedan Microsoft kommer att återställa den och returnera den till en gratis pool. 

### <a name="how-to-delete-an-hsm-resource"></a>Så här tar du bort en HSM-resurs

Azure-resursen för en HSM kan inte tas bort om inte HSM är i ett "nollat" tillstånd.  Därför måste allt nyckelmaterial ha tagits bort innan du försöker ta bort det som en resurs. Det snabbaste sättet att nollställa är att få HSM admin lösenord fel 3 gånger (obs: detta hänvisar till HSM admin och inte apparaten nivå admin). Luna-skalet har `hsm -factoryreset` ett kommando som nollställer men det kan bara utföras via konsol på serieporten och kunderna har inte tillgång till detta.

## <a name="next-steps"></a>Nästa steg

Den här artikeln har gett insikt i områden i HSM-distributionens livscykel som kan ha problem eller kräva felsökning eller noggrant övervägande. Förhoppningsvis hjälper den här artikeln dig att undvika onödiga förseningar och frustration, och om du har relevanta tillägg eller ändringar, sedan ta upp en supportbegäran med Microsoft och låt oss veta. 
