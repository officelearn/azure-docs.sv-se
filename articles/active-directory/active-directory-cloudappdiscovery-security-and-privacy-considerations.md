---
title: "Cloud App Discovery säkerhets- och överväganden för sekretess | Microsoft Docs"
description: "Det här avsnittet beskriver säkerhet och sekretess överväganden som rör Cloud App Discovery."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: 2fce5c82-d3de-4097-808f-40214768df9e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 7775bad1503e62e1887e6d85f67c3107f48866bf
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2018
---
# <a name="cloud-app-discovery-security-and-privacy-considerations"></a>Cloud App Discovery säkerhets- och överväganden för sekretess
Det här avsnittet beskriver hur data som samlas in, bearbetas och skyddas i Azure Active Directory Cloud App Discovery. Microsoft strävar efter att skydda din integritet och skydda dina data. Microsoft följer för att skydda programvaruutveckling livscykel metoder för att driva en tjänst. Säkra och skydda data är högsta prioritet på Microsoft.

> [!TIP] 
> Kolla in nya utan Agent Cloud App Discovery i Azure Active Directory (Azure AD), som har förbättrats genom [integrering med Microsoft Cloud App Security](https://portal.cloudappsecurity.com). 

## <a name="overview"></a>Översikt
Cloud App Discovery ingår i Azure AD och finns i Microsoft Azure.  
Cloud App Discovery endpoint agent används för att samla in identifieringsdata för programmet från IT hanterade datorer. Insamlade data skickas på ett säkert sätt via en krypterad kanal till Azure AD Cloud App Discovery-tjänsten. Cloud App Discovery-data för en organisation visas sedan i Azure-portalen. 

![Så här fungerar Cloud App Discovery](./media/active-directory-cloudappdiscovery-security-and-privacy-considerations/cad01.png) 

Nedan följer säkert flödet av information från din organisation till Cloud App Discovery-tjänsten och i slutändan till Cloud App Discovery-portalen.

## <a name="collecting-data-from-your-organization"></a>Samla in data från din organisation
Du måste först distribuera Azure AD Cloud App Discovery endpoint agent till datorerna i din organisation för att kunna använda funktionen för identifiering av Azure Active Directory Molnappen få insikter om de program som används av anställda i din organisation.

Administratörer av Azure Active Directory-klient (eller ombud) kan hämta installationspaketet för agenten från Azure-portalen. Agenten kan vara manuellt installerats eller installeras på flera datorer i organisationen med hjälp av SCCM eller en Grupprincip.

Mer information om distributionsalternativ finns [Cloud App Discovery grupp princip Deployment Guide](http://social.technet.microsoft.com/wiki/contents/articles/30965.cloud-app-discovery-group-policy-deployment-guide.aspx).


### <a name="data-collected-by-the-agent"></a>Data som samlas in av agenten
Informationen som beskrivs i följande lista samlas in av agenten när upprättas en anslutning till ett webbprogram. Informationen samlas endast för program som administratören har konfigurerat för identifiering. Du kan redigera listan över molnappar som agenten övervakar via Cloud App Discovery i Azure AD i Microsoft [Azure-portalen](https://portal.azure.com/)under **inställningar**->**datainsamling**  -> **App Samlingslista**. 

**Information kategori**: användarinformation  
**Beskrivning**: Windows-användarnamn för processen som gjorde en begäran till mål-webbprogram (till exempel DOMÄN\användarnamn) samt de Windows säkerhetsidentifierare (SID) för användaren.

**Information kategori**: processinformation  
**Beskrivning**: namnet på processen som gjorde begäran till mål-webbprogram (till exempel iexplore.exe)

**Information kategori**: datorn information  
**Beskrivning**: datorn NetBIOS-namn på agenten är installerad.

**Information kategori**: App trafikinformation  
**Beskrivning**: följande anslutningsinformation:

* Källa (lokal dator) och mål-IP-adresser och portnummer
* Offentliga IP-adressen för den organisation som begäran går ut.
* Tid för begäran
* Mängden trafik som skickas och tas emot
* IP-version (4 eller 6)
* För TLS-anslutningar: målvärddator från Servernamnsindikation tillägget eller servercertifikatet.

Följande HTTP-information:

* Metod (GET, POST, etc.)
* Protokoll (HTTP/1.1, etc.)
* Användaragentsträng
* Värdnamn
* Mål-URI (exklusive frågesträngen)
* Information om innehållstyp
* Referent URL-information (exklusive frågesträngen)

> [!NOTE]
> HTTP-informationen ovan samlas in för alla icke-krypterade anslutningar.
> För TLS-anslutningar fångas endast den här informationen när inställningen 'Djupinspektion' är aktiverat i portalen. Inställningen är ”ON” som standard.
> Mer information finns nedan, och [komma igång med Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)
> 
> 

Förutom de data som agenten samlar in om nätverksaktivitet den samlar även in anonymiserade data om
* Konfiguration av programvara och maskinvara
* Felrapporter
* Data på hur agenten används.


### <a name="how-the-agent-works"></a>Så här fungerar agenten
Agentinstallationen innehåller två komponenter:

* En komponent i användarläge
* En komponent som drivrutinen kernel-läge (Windows Filtering Platform drivrutin)

När agenten installeras lagrar ett betrott certifikat datorspecifik på datorn som sedan används för att upprätta en säker anslutning med Cloud App Discovery-tjänsten. Agenten hämtar regelbundet principkonfigurationen från Cloud App Discovery-tjänsten via den här säker anslutning. Principen innehåller information om vilka molnprogram att övervaka och om automatisk uppdatering måste vara aktiverad, bland annat.

Eftersom Internet-trafik skickas och tas emot på datorn från Internet Explorer och Chrome, Cloud App Discovery-agenten analyserar trafiken och extraherar relevanta metadata (finns i **Data som samlas in av agenten** ovan).  
Varje minut överför agenten insamlade metadata till Cloud App Discovery-tjänsten via en krypterad kanal.

Komponenten drivrutinen fångar upp krypterad trafik och infogas i krypterade dataströmmen. Mer information finns i den **fånga upp data från krypterade anslutningar (djupinspektion)** nedan.

### <a name="respecting-user-privacy"></a>Respektera användarnas integritet
Vårt mål är att ge administratörer verktyg för att ange balansen mellan detaljerad optik till programmet användnings- och sekretess för organisationen. Som tillhandahåller vi följande rattar på inställningssidan i portalen:

* **Datainsamling**: Administratörer kan välja att ange vilka program eller programkategorier som de vill ha identifieringsdata på.
* **Djupinspektion**: Administratörer kan välja att ange om agenten samlar in HTTP-trafik för SSL/TLS-anslutningar (aka **'Djupinspektion'**). Mer om detta i nästa avsnitt.
* **Medgivande alternativ**: Administratörer kan använda Cloud App Discovery-portal för att välja om du vill meddela användare för datainsamling av agenten och om du vill kräva att användare godkännande innan agenten startar att samla in användardata.

Cloud App Discovery endpoint agent samlar endast in de uppgifter som beskrivs i den **Data som samlas in av agenten** ovan.

### <a name="intercepting-data-from-encrypted-connections-deep-inspection"></a>Fånga upp data från krypterade anslutningar (djupinspektion)
Administratörer kan konfigurera agenten för övervakning av data från krypterade anslutningar (djup kontroll) som vi nämnt tidigare. TLS ([Transport Layer Security](https://msdn.microsoft.com/library/windows/desktop/aa380516%28v=vs.85%29.aspx)) är en av de vanligaste protokollen används på Internet idag. Genom att kryptera kommunikationen med TLS, kan en klient upprättar en säker och privat kommunikationskanal med en webbserver. TLS innehåller grundläggande skydd för att skicka autentiseringsuppgifter och förhindra avslöjande av känslig information.

Den slutpunkt till slutpunkt säker krypterad kanal som tillhandahålls av TLS kan viktiga säkerhet och sekretess, missbrukat protokollet ofta för skadliga eller nefarious ändamål. Så mycket så faktum är kallas att TLS ofta ”universal-kringgående för brandväggar protokoll”. Roten till problemet är att de flesta brandväggar det går inte att inspektera TLS kommunikation eftersom programnivå data krypteras med SSL. Den här kunskapen kan använda angripare ofta TLS för att leverera skadliga nyttolaster till en användare som är säker på att även de mest intelligent programnivå brandväggarna är helt syn i TLS och måste bara vidarebefordra TLS kommunikation mellan värdar. Slutanvändare utnyttja ofta TLS för att kringgå åtkomstkontroller tvingas av deras företag brandväggar och proxyservrar, använder den för att ansluta till offentliga proxyservrar och för icke-TLS-tunnelprotokoll genom brandväggen som annars kan blockeras av en princip.

Djupinspektion tillåter agenten att fungera som en betrodd man-in-the-middle Cloud App Discovery. När en klientbegäran görs att få åtkomst till en resurs för HTTPS skyddade drivrutinens Endpoint Agent spärras anslutningen och upprättar en ny anslutning till målservern för att hämta dess SSL-certifikat för klientens räkning. Agenten kontrollerar sedan att certifikatet kan vara betrott (genom att kontrollera att den inte har återkallats och utföra andra kontroller för certifikat), och om dessa pass Endpoint Agent sedan kopieras informationen från servercertifikatet och skapar ett eget servercertifikat--kallas en avlyssning certifikat--med hjälp av informationen. Avlyssning certifikatet är signerade på direkt av agenten slutpunkt med ett rotcertifikat som är installerat i det betrodda certifikatarkivet för Windows. Den här självsignerade rotcertifikat markeras inte kan exporteras och ACL hade till administratörer. Avsikten är att aldrig lämna den dator som har skapats. När den slutanvändarens klientprogrammet får avlyssning certifikatet, den litar på den eftersom det har kan validera certifikatkedjan ända till rotcertifikatet. Den här processen är främst transparent från en slutanvändares synsätt med några varningar som beskrivs nedan.

Genom att aktivera djupinspektion Cloud App Discovery Endpoint Agent dekryptera och inspektera TLS krypterad kommunikation, vilket gör att tjänsten för att minska bruset och få insikter om användningen av de krypterade molnapparna.

#### <a name="a-word-of-caution"></a>En liten varning
Innan du aktiverar på djupinspektion, vi rekommenderar att du kommunicera din avsikt att din juridiska och HR avdelningar och få sitt samtycke. Kontrollera slutanvändarens privata krypterad kommunikation kan vara känslig ämne, uppenbara. Innan en produktion lansering av djupinspektion, kontrollera att din företagets säkerhetsprinciper och principer för godkänd användning har uppdaterats för att ange att krypterad kommunikation ska kontrolleras. Användarmeddelande och undantag för webbplatser som anses vara känsliga (t.ex. banker och medicinska webbplatser) kan också vara nödvändigt om du konfigurerar Cloud App Discovery för att övervaka dem. Som nämnts ovan är kan administratörer använda Cloud App Discovery-portal för att välja om du vill meddela användare för datainsamling av agenten och om du vill kräva användarens medgivande innan agenten börjar samla in användardata.

### <a name="known-issues-and-drawbacks"></a>Kända problem och nackdelar
Det finns några fall där TLS avlyssning kan påverka slutanvändarens upplevelse:

* Certifikat för utökad (Validation) återge adressfältet i webbläsaren grön ska fungera som en ledtråd att du besöker en webbplats som betrodd. TLS-kontroll kan inte duplicera EV i certifikat som utfärdas till klienten, så att webbplatser som använder EV-certifikat fungerar normalt men adressfältet visas inte grönt.  
* Offentlig nyckel fästning (även kallat certifikat fästning) är utformade för att skydda användare från man-in-the-middle-attacker och obehöriga certifikatutfärdare. När rotcertifikatet för en fast plats inte matchar något känt fungerande CA: ns, avvisar anslutning med ett fel i webbläsaren. Eftersom TLS avlyssning är faktiskt en man-in-the-middle, dessa anslutningar kommer att misslyckas.
* Om användare klickar på låsikonen i webbläsaren adress fältet webbläsaren att inspektera platsinformationen, visas inte en kedja som slutar på den certifikatutfärdare som används för att signera webbplatscertifikat, men i stället en certifikatkedja som slutar med Windows betrodda certifikatarkiv.

Om du vill minska antalet förekomster av de här problemen, vi håller reda på molntjänster och klientprogram använder utökad validering eller offentliga nyckel fästning och instruera Endpoint Agent känna till att undvika att avlyssna berörda anslutningar. Även i dessa fall kan dock får du ändå rapporter om användningen av dessa molnappar och mängden data som överförs men eftersom de inte djupgående kontrolleras ingen information om hur apparna användes blir tillgängliga.

## <a name="sending-data-to-cloud-app-discovery"></a>Skicka data till Cloud App Discovery
När metadata har samlats in av agenten, cachelagras på datorn för upp till en minut eller tills cachelagrade data når en storlek på 5MB. Den sedan komprimeras och skickas via en säker anslutning till Cloud App Discovery-tjänsten.

Om agenten inte kan kommunicera med Cloud App Discovery-tjänsten av någon anledning, lagras den insamlade metadata i en lokal fil-cache som bara kan användas av Privilegierade användare på datorn (till exempel gruppen Administratörer).  
Agenten försöker automatiskt att skicka cachelagrade metadata förrän den har tagits emot av Cloud App Discovery-tjänsten.

## <a name="receiving-the-data-at-the-service-end"></a>Mottagning av data i tjänsten slutet
Agenterna autentisera till Cloud App Discovery-tjänsten använder datorn specifika certifikatet för klientautentisering anges ovan och vidarebefordrar data via en krypterad kanal.  
Pipeline för realtidsanalyser av Cloud App Discovery-tjänsten bearbetar metadata för varje kund separat med logiskt resurspartitionering den alla led i pipeline för realtidsanalyser.
Analyserade metadata styr olika rapporter på portalen.

Obearbetat metadata och analyserade metadata lagras i upp till 180 dagar. Kunder kan också välja att hämta analyserade metadata i ett Azure blob storage-konto de önskar.
Detta är användbart för offlineanalys av metadata samt längre kvarhållning av data.

## <a name="accessing-the-data-using-the-azure-portal"></a>Åtkomst till data med Azure-portalen
I syfte att skydda de metadata som samlas in, som standard har endast globala administratörer för klienten åtkomst till Cloud App Discovery-funktionen i Azure-portalen.  
Administratörer kan du delegera åtkomst till andra användare eller grupper.

> [!NOTE]
> Mer information finns i [komma igång med Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)
> 
> 


Alla användare åtkomst till data i portalen måste licensieras med en Azure AD Premium-licens.

## <a name="additional-resources"></a>Ytterligare resurser
* [Hur kan identifiera ej sanktionerade molnappar som används inom organisationen](active-directory-cloudappdiscovery-whatis.md)
* [Artikelindex för programhantering i Azure Active Directory](active-directory-apps-index.md)

