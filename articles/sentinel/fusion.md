---
title: Avancerad identifiering av flerstegsattacker i Azure Sentinel
description: Använd Fusion-teknik i Azure Sentinel för att minska aviseringströtthet och skapa användbara incidenter som baseras på avancerad identifiering av angrepp i flera steg.
services: sentinel
documentationcenter: na
author: yelevin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2020
ms.author: yelevin
ms.openlocfilehash: 87ca322cbdfdd8a53a3ecefcb120a961ea1bb936
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587931"
---
# <a name="advanced-multistage-attack-detection-in-azure-sentinel"></a>Avancerad identifiering av flerstegsattacker i Azure Sentinel


> [!IMPORTANT]
> Vissa Fusion-funktioner i Azure Sentinel är för närvarande i offentlig förhandsversion.
> Dessa funktioner tillhandahålls utan ett servicenivåavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



Genom att använda Fusion-teknik som baseras på maskininlärning kan Azure Sentinel automatiskt identifiera flerstegsattacker genom att kombinera avvikande beteenden och misstänkta aktiviteter som observeras i olika skeden av dödskedjan. Azure Sentinel genererar sedan incidenter som annars skulle vara mycket svåra att fånga. Dessa incidenter innesluter två eller flera aviseringar eller aktiviteter. Avsiktligt är dessa incidenter låg volym, hög trohet och hög allvarlighetsgrad.

Anpassad för din miljö, den här identifieringen minskar inte bara falska positiva priser utan kan också identifiera attacker med begränsad eller saknad information.

## <a name="configuration-for-advanced-multistage-attack-detection"></a>Konfiguration för avancerad attackidentifiering i flera faser

Den här identifieringen är aktiverad som standard i Azure Sentinel. Om du vill kontrollera status eller inaktivera den kanske för att du använder en alternativ lösning för att skapa incidenter baserat på flera aviseringar använder du följande instruktioner:

1. Om du inte redan gjort det loggar du in på [Azure Portal](https://portal.azure.com).

2. Navigera till **Azure Sentinel** > **Configuration** > **Analytics**

3. Välj **Aktiva regler** och leta reda på avancerad **flerstegsattackidentifiering** i kolumnen **NAMN.** Kontrollera kolumnen **STATUS** för att bekräfta om identifieringen är aktiverad eller inaktiverad.

4. Om du vill ändra status markerar du den här posten och på bladet **Avancerad flerstegsattackidentifiering** väljer du **Redigera**.

5. I **guidebladet För att skapa regler** väljs statusändringen automatiskt åt dig, så välj **Nästa: Granska**och sedan **Spara**. 

Regelmallar är inte tillämpliga för avancerad attackidentifiering i flera steg.

> [!NOTE]
> Azure Sentinel använder för närvarande 30 dagar med historiska data för att träna maskininlärningssystemen. Dessa data krypteras alltid med Microsofts nycklar när de passerar genom pipelinen för maskininlärning. Utbildningsdata krypteras dock inte med [customer managed keys (CMK)](customer-managed-keys.md) om du har aktiverat CMK på din Azure Sentinel-arbetsyta. Om du vill välja bort Fusion navigerar du till **Azure Sentinel** \> **Configuration** \> ** \> Analytics Active rules \> Advanced Multistage Attack Detection** och väljer Inaktivera i kolumnen **Status.** **Disable.**

## <a name="fusion-using-palo-alto-networks-and-microsoft-defender-atp"></a>Fusion med Palo Alto Networks och Microsoft Defender ATP

Dessa scenarier kombinerar två av de grundläggande loggar som används av säkerhetsanalytiker: Brandväggsloggar från Palo Alto-nätverk och slutpunktsidentifieringsloggar från Microsoft Defender ATP. I alla scenarier som anges nedan identifieras en misstänkt aktivitet i slutpunkten som involverar en extern IP-adress, och detta följs av avvikande trafik från den externa IP-adressen tillbaka till brandväggen. I Palo Alto-loggar fokuserar Azure Sentinel på [hotloggar](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)och trafik anses misstänkt när hot tillåts (misstänkta data, filer, översvämningar, paket, skanningar, spionprogram, webbadresser, virus, sårbarheter, löpeldvirus, skogsbränder).

### <a name="network-request-to-tor-anonymization-service-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Nätverksbegäran till TOR-anonymiseringstjänsten följt av avvikande trafik som flaggats av Palo Alto Networks-brandväggen.

I det här fallet upptäcker Azure Sentinel först en avisering om att Microsoft Defender Advanced Threat Protection har upptäckt en nätverksbegäran till en TOR-anonymiseringstjänst som leder till avvikande aktivitet. Detta initierades under konto {kontonamn} med SID-ID {sid} vid {time}. Den utgående IP-adressen till anslutningen var {IndividualIp}.
Sedan upptäcktes ovanlig aktivitet av Palo Alto Networks-brandväggen vid {TimeGenerated}. Detta indikerar skadlig trafik som har angetts i nätverket Mål-IP-adressen för nätverkstrafiken är {DestinationIP}.

Det här scenariot är för närvarande i offentlig förhandsversion.


### <a name="powershell-made-a-suspicious-network-connection-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>PowerShell gjorde en misstänkt nätverksanslutning följt av avvikande trafik som flaggats av Palo Alto Networks-brandväggen.

I det här fallet upptäcker Azure Sentinel först en varning om att Microsoft Defender Advanced Threat Protection upptäckt att PowerShell har gjort en misstänkt nätverksanslutning som ledde till avvikande aktivitet som upptäcktes av en Palo Alto-nätverksbrandvägg. Detta initierades av kontot {account name} med SID ID {sid} vid {time}. Den utgående IP-adressen till anslutningen var {IndividualIp}. Sedan upptäcktes ovanlig aktivitet av Palo Alto Networks-brandväggen vid {TimeGenerated}. Detta indikerar att skadlig trafik har kommit in i nätverket. Mål-IP-adressen för nätverkstrafiken är {DestinationIP}.

Det här scenariot är för närvarande i offentlig förhandsversion.

### <a name="outbound-connection-to-ip-with-a-history-of-unauthorized-access-attempts-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Utgående anslutning till IP med en historik över obehöriga åtkomstförsök följt av avvikande trafik som flaggats av Palo Alto Networks-brandväggen

I det här fallet upptäcker Azure Sentinel en avisering om att Microsoft Defender Advanced Threat Protection har upptäckt en utgående anslutning till en IP-adress med en historik över obehöriga åtkomstförsök som leder till avvikande aktivitet som upptäcks av Palo Alto Brandvägg för nätverk. Detta initierades av kontot {account name} med SID ID {sid} vid {time}. Den utgående IP-adressen till anslutningen var {IndividualIp}. Därefter upptäcktes ovanlig aktivitet av Palo Alto Networks-brandväggen vid {TimeGenerated}. Detta indikerar att skadlig trafik har kommit in i nätverket. Mål-IP-adressen för nätverkstrafiken är {DestinationIP}.

Det här scenariot är för närvarande i offentlig förhandsversion.



## <a name="fusion-using-identity-protection-and-microsoft-cloud-app-security"></a>Fusion med identitetsskydd och Microsoft Cloud App Security

Med hjälp av avancerad identifiering av flerstegsattacker stöder Azure Sentinel följande scenarier som kombinerar avvikelsehändelser från Azure Active Directory Identity Protection och Microsoft Cloud App Security:

- [Omöjlig resa till atypisk plats följt av avvikande Office 365-aktivitet](#impossible-travel-to-atypical-location-followed-by-anomalous-office-365-activity)
- [Inloggningsaktivitet för okänd plats följt av avvikande Office 365-aktivitet](#sign-in-activity-for-unfamiliar-location-followed-by-anomalous-office-365-activity)
- [Inloggningsaktivitet från infekterad enhet följt av avvikande Office 365-aktivitet](#sign-in-activity-from-infected-device-followed-by-anomalous-office-365-activity)
- [Inloggningsaktivitet från anonym IP-adress följt av avvikande Office 365-aktivitet](#sign-in-activity-from-anonymous-ip-address-followed-by-anomalous-office-365-activity)
- [Inloggningsaktivitet från användare med läckta autentiseringsuppgifter följt av avvikande Office 365-aktivitet](#sign-in-activity-from-user-with-leaked-credentials-followed-by-anomalous-office-365-activity)

Du måste ha [Azure AD Identity Protection-dataanslutningen](connect-azure-ad-identity-protection.md) och Cloud App Security-anslutningsapparna konfigurerade. [Cloud App Security](connect-cloud-app-security.md)

I de beskrivningar som följer visar Azure Sentinel det faktiska värdet från dina data som representeras på den här sidan som variabler inom parentes. Det faktiska visningsnamnet för ett \<konto i stället för *kontonamn*> \<och det faktiska numret i stället för *tal*>.

### <a name="impossible-travel-to-atypical-location-followed-by-anomalous-office-365-activity"></a>Omöjlig resa till atypisk plats följt av avvikande Office 365-aktivitet

Det finns sju möjliga Azure Sentinel-incidenter som kombinerar omöjliga resor till atypiska platsaviseringar från Azure AD Identity Protection och avvikande Office 365-aviseringar som genereras av Microsoft Cloud App Security:

- **Omöjlig resa till atypiska platser som leder till Office 365-postlåda exfiltration**
    
    Den här varningen är en indikation \<på en inloggningshändelse med *kontonamn*> från en omöjlig resa till \< *plats*>, en atypisk plats, följt av en misstänkt inkorgsbefordringsregel som angavs i en användares inkorg.
    
    Detta kan tyda på att kontot har komprometterats och att postlådan används för att frigöra information från din organisation. \< *Användarkontots namn*> skapat eller uppdaterat en vidarebefordringsregel för inkorgen som vidarebefordrar all inkommande e-post till den externa adressens \< *e-postadress*>.

- **Omöjlig resa till atypiska platser som leder till misstänkt molnapp administrativ aktivitet**
    
    Den här varningen är en indikation \<på en inloggningshändelse efter *kontonamn*> från en omöjlig resa till \< *plats*>, en atypisk plats.
    
    Därefter> \< *kontokontonamnet* utförts över \< *antal*> administrativa aktiviteter i en enda session.

- **Omöjlig resa till atypiska platser som leder till massfil radering**
    
    Den här aviseringen är en indikation \<på en inloggningshändelse efter *kontonamn*> till \< *plats*>, en atypisk plats. 
    
    Därefter> \< *kontokontonamnet* \< *borttaget antal*> unika filer i en enda session.

- **Omöjligt att resa till atypiska platser som leder till massfil nedladdning**
    
    Den här varningen är en indikation \<på en inloggningshändelse efter *kontonamn*> från en omöjlig resa till \< *plats*>, en atypisk plats. 
    
    Därefter> \< *kontokontonamnet* hämtats över \< *antalet*> unika filer i en enda session.

- **Omöjlig resa till atypiska platser som leder till Office 365-personifiering**
    
    Den här varningen är en indikation \<på en inloggningshändelse efter *kontonamn*> från en omöjlig resa till \< *plats*>, en atypisk plats. 
    
    Därefter utförde \< *kontokontonamnet*> ett ovanligt belopp\<*(antal aktiviteter*>) av personifieringsaktiviteter i en enda session.

- **Omöjlig resa till atypiska platser som leder till massfildelning**
    
    Den här varningen är en indikation \<på en inloggningshändelse efter *kontonamn*> från en omöjlig resa till \< *plats*>, en atypisk plats. 
    
    Därefter> \< *kontokontonamnet* delats över \< *antalet*> unika filer i en enda session.

- **Omöjlig resa till atypiska platser som leder till ransomware i molnapp**
    
    Den här varningen är en indikation \<på en inloggningshändelse efter *kontonamn*> från en omöjlig resa till \< *plats*>, en atypisk plats. 
    
    Därefter> \< *kontokontonamnet* uppladdat \< *antal*> filer och raderade totalt \< *antal*> filer. 
    
    Denna aktivitet mönster är ett tecken på en potentiell ransomware attack.


### <a name="sign-in-activity-for-unfamiliar-location-followed-by-anomalous-office-365-activity"></a>Inloggningsaktivitet för okänd plats följt av avvikande Office 365-aktivitet

Det finns sju möjliga Azure Sentinel-incidenter som kombinerar inloggningsaktivitet för okända platsaviseringar från Azure AD Identity Protection och avvikande Office 365-aviseringar som genereras av Microsoft Cloud App Security.

- **Inloggningshändelse från en okänd plats som leder till Exfiltration för Exchange Online-postlåda**
    
    Den här varningen är en indikation \<på en inloggningshändelse med *kontonamn*> från \< *plats*>, en okänd plats, följt av en misstänkt inkorgsbefordringsregel, angavs i en användares inkorg.
    
    Detta kan tyda på att kontot har komprometterats och att postlådan används för att frigöra information från din organisation. \< *Användarkontots namn*> skapat eller uppdaterat en vidarebefordringsregel för inkorgen som vidarebefordrar all inkommande e-post till den externa adressens \< *e-postadress*>. 

- **Inloggningshändelse från en okänd plats som leder till misstänkt molnapp administrativ aktivitet**
    
    Den här aviseringen är en indikation \<på en inloggningshändelse efter *kontonamn*> från \< *plats*>, en okänd plats. 
    
    Därefter> \< *kontokontonamnet* utförts under \< *antalet*> administrativa aktiviteter i en enda session.

- **Inloggningshändelse från en okänd plats som leder till massfilradering**
    
    Den här aviseringen är en indikation \<på en inloggningshändelse efter *kontonamn*> från \< *plats*>, en okänd plats. 
    
    Därefter> \< *kontokontonamnet* \< *borttaget antal*> unika filer i en enda session.

- **Inloggningshändelse från en okänd plats som leder till massfilhämtning**
    
    Den här aviseringen är en indikation \<på en inloggningshändelse efter *kontonamn*> från \< *plats*>, en okänd plats. 
    
    Därefter> \< *kontokontonamnet* hämtats över \< *antalet*> unika filer i en enda session.

- **Inloggningshändelse från en okänd plats som leder till personifiering av Office 365**
    
    Den här aviseringen är en indikation \<på en inloggningshändelse efter *kontonamn*> från \< *plats*>, en okänd plats.
    
    Därefter> \< *kontokontonamnet* personifieras \<över *antalet*> olika konton i en enda session.

- **Inloggningshändelse från en okänd plats som leder till massfildelning**
    
    Den här aviseringen är en indikation \<på en inloggningshändelse efter *kontonamn*> från \< *plats*>, en okänd plats. 
    
    Därefter> \< *kontokontonamnet* delats över \< *antalet*> unika filer i en enda session.

- **Inloggningshändelse från en okänd plats som leder till ransomware i molnapp**
    
    Den här aviseringen är en indikation \<på en inloggningshändelse efter *kontonamn*> från \< *plats*>, en okänd plats. 
    
    Därefter> \< *kontokontonamnet* uppladdat \< *antal*> filer och raderade totalt \< *antal*> filer. 
    
    Denna aktivitet mönster är ett tecken på en potentiell ransomware attack.

### <a name="sign-in-activity-from-infected-device-followed-by-anomalous-office-365-activity"></a>Inloggningsaktivitet från infekterad enhet följt av avvikande Office 365-aktivitet

Det finns sju möjliga Azure Sentinel-incidenter som kombinerar inloggningsaktivitet från infekterade enhetsaviseringar från Azure AD Identity Protection och avvikande Office 365-aviseringar som genereras av Microsoft Cloud App Security:

- **Inloggningshändelse från en infekterad enhet som leder till exfiltrering för Office 365-postlåda**
    
    Den här varningen är en indikation \<på en inloggningshändelse med *kontonamn*> från en enhet som kan vara infekterad med skadlig kod, följt av en misstänkt inkorgsbefordringsregel som angetts i en användares inkorg.
    
    Detta kan tyda på att kontot har komprometterats och att postlådan används för att frigöra information från din organisation. \< *Användarkontots namn*> skapat eller uppdaterat en vidarebefordringsregel för inkorgen som vidarebefordrar all inkommande e-post till den externa adressens \< *e-postadress*>. 

- **Inloggningshändelse från en infekterad enhet som leder till misstänkt molnapp administrativ aktivitet**
    
    Den här varningen är en indikation \<på en inloggningshändelse efter *kontonamn*> från en enhet som kan vara infekterad med skadlig kod.
    
    Därefter> \< *kontokontonamnet* utförts under \< *antalet*> administrativa aktiviteter i en enda session.

- **Inloggningshändelse från en infekterad enhet som leder till massfilradering**
    
    Den här varningen är en indikation \<på en inloggningshändelse efter *kontonamn*> från en enhet som kan vara infekterad med skadlig kod. 
    
    Därefter> \< *kontokontonamnet* \< *borttaget antal*> unika filer i en enda session.

- **Inloggningshändelse från en infekterad enhet som leder till massfilhämtning**
    
    Den här varningen är en indikation \<på en inloggningshändelse efter *kontonamn*> från en enhet som kan vara infekterad med skadlig kod. 
    
    Därefter> \< *kontokontonamnet* hämtats över \< *antalet*> unika filer i en enda session.

- **Inloggningshändelse från en infekterad enhet som leder till personifiering av Office 365**
    
    Den här varningen är en indikation \<på en inloggningshändelse efter *kontonamn*> från en enhet som kan vara infekterad med skadlig kod. 
    
    Därefter> \< *kontokontonamnet* personifieras \<över *antalet*> olika konton i en enda session.

- **Inloggningshändelse från en infekterad enhet som leder till massfildelning**
    
    Den här varningen är en indikation \<på en inloggningshändelse efter *kontonamn*> från en enhet som kan vara infekterad med skadlig kod. 
    
    Därefter> \< *kontokontonamnet* delats över \< *antalet*> unika filer i en enda session.

- **Inloggningshändelse från en infekterad enhet som leder till ransomware i molnapp**
    
    Den här varningen är en indikation \<på en inloggningshändelse efter *kontonamn*> från en enhet som kan vara infekterad med skadlig kod. 
    
    Därefter> \< *kontokontonamnet* uppladdat \< *antal*> filer och raderade totalt \< *antal*> filer. 
    
    Denna aktivitet mönster är ett tecken på en potentiell ransomware attack.

### <a name="sign-in-activity-from-anonymous-ip-address-followed-by-anomalous-office-365-activity"></a>Inloggningsaktivitet från anonym IP-adress följt av avvikande Office 365-aktivitet

Det finns sju möjliga Azure Sentinel-incidenter som kombinerar inloggningsaktivitet från anonyma IP-adressaviseringar från Azure AD Identity Protection och avvikande Office 365-aviseringar som genereras av Microsoft Cloud App Security:

- **Inloggningshändelse från en anonym IP-adress som leder till exfiltrering för Office 365-postlåda**
    
    Den här aviseringen är en indikation \<på en inloggningshändelse med *kontonamn*> från en anonym IP-adress för \<proxy-IP-adress>, följt av en regel för misstänkt inkorgsinriktning angavs i en användares inkorg. *IP address*
    
    Detta kan tyda på att kontot har komprometterats och att postlådan används för att frigöra information från din organisation. \< *Användarkontots namn*> skapat eller uppdaterat en vidarebefordringsregel för inkorgen som vidarebefordrar all inkommande e-post till den externa adressens \< *e-postadress*>. 

- **Inloggningshändelse från en anonym IP-adress som leder till misstänkt molnapp administrativ aktivitet**
    
    Den här aviseringen är en indikation \<på en inloggningshändelse efter *kontonamn*> från en anonym IP-adress för \< *proxy-IP-adress*>. 
    
    Därefter> \< *kontokontonamnet* utförts under \< *antalet*> administrativa aktiviteter i en enda session.

- **Inloggningshändelse från en anonym IP-adress som leder till massfilradering**
    
    Den här aviseringen är en indikation \<på en inloggningshändelse efter *kontonamn*> från en anonym IP-adress för \< *proxy-IP-adress*>. 
    
    Därefter> \< *kontokontonamnet* \< *borttaget antal*> unika filer i en enda session.

- **Inloggningshändelse från en anonym IP-adress som leder till massfilhämtning**
    
    Den här aviseringen är en indikation \<på en inloggningshändelse efter *kontonamn*> från en anonym IP-adress för \< *proxy-IP-adress*>. 
    
    Därefter> \< *kontokontonamnet* hämtats över \< *antalet*> unika filer i en enda session.

- **Inloggningshändelse från en anonym IP-adress som leder till personifiering av Office 365**
    
    Den här aviseringen är en indikation \<på en inloggningshändelse efter *kontonamn*> från en anonym IP-adress för \< *proxy-IP-adress*>. 
    
    Därefter> \< *kontokontonamnet* personifieras \<över *antalet*> olika konton i en enda session.

- **Inloggningshändelse från en anonym IP-adress som leder till massfildelning**
    
    Den här aviseringen är en indikation \<på en inloggningshändelse efter *kontonamn*> från en anonym IP-adress för \< *proxy-IP-adress*>. 
    
    Därefter> \< *kontokontonamnet* delats över \< *antalet*> unika filer i en enda session.

- **Inloggningshändelse från en anonym IP-adress till ransomware i molnapp**
    
    Den här aviseringen är en indikation \<på en inloggningshändelse efter *kontonamn*> från en anonym IP-adress för \< *proxy-IP-adress*>. 
    
    Därefter> \< *kontokontonamnet* uppladdat \< *antal*> filer och raderade totalt \< *antal*> filer. 
    
    Denna aktivitet mönster är ett tecken på en potentiell ransomware attack.

### <a name="sign-in-activity-from-user-with-leaked-credentials-followed-by-anomalous-office-365-activity"></a>Inloggningsaktivitet från användare med läckta autentiseringsuppgifter följt av avvikande Office 365-aktivitet

Det finns sju möjliga Azure Sentinel-incidenter som kombinerar inloggningsaktivitet från användare med läckta autentiseringsuppgifter från Azure AD Identity Protection och avvikande Office 365-aviseringar som genereras av Microsoft Cloud App Security:

- **Inloggningshändelse från användare med läckta autentiseringsuppgifter som leder till exfiltrering för Office 365-postlåda**
    
    Den här aviseringen är en indikation \<på att inloggningshändelsen efter *kontonamn*> använde läckta autentiseringsuppgifter, följt av en regel för misstänkt vidarebefordran av inkorgen angavs i en användares inkorg. 
    
    Detta kan tyda på att kontot har komprometterats och att postlådan används för att frigöra information från din organisation. \< *Användarkontots namn*> skapat eller uppdaterat en vidarebefordringsregel för inkorgen som vidarebefordrar all inkommande e-post till den externa adressens \< *e-postadress*>. 

- **Inloggningshändelse från användare med läckta autentiseringsuppgifter som leder till misstänkt administrativ aktivitet i molnappen**
    
    Den här aviseringen är en indikation \<på att inloggningshändelsen efter *kontonamn*> använt läckta autentiseringsuppgifter.
    
    Därefter> \< *kontokontonamnet* utförts under \< *antalet*> administrativa aktiviteter i en enda session.

- **Inloggningshändelse från användare med läckta autentiseringsuppgifter som leder till massfilradering**
    
    Den här aviseringen är en indikation \<på att inloggningshändelsen efter *kontonamn*> använt läckta autentiseringsuppgifter.
    
    Därefter> \< *kontokontonamnet* \< *borttaget antal*> unika filer i en enda session.

- **Inloggningshändelse från användare med läckta autentiseringsuppgifter som leder till massfilhämtning**
    
    Den här aviseringen är en indikation \<på att inloggningshändelsen efter *kontonamn*> använt läckta autentiseringsuppgifter.
    
    Därefter> \< *kontokontonamnet* hämtats över \< *antalet*> unika filer i en enda session.

- **Inloggningshändelse från användare med läckta autentiseringsuppgifter som leder till personifiering av Office 365**
    
    Den här aviseringen är en indikation \<på att inloggningshändelsen efter *kontonamn*> använt läckta autentiseringsuppgifter. 
    
    Därefter> \< *kontokontonamnet* personifieras \<över *antalet*> olika konton i en enda session.

- **Inloggningshändelse från användare med läckta autentiseringsuppgifter som leder till massfildelning**
    
    Den här aviseringen är en indikation \<på att inloggningshändelsen efter *kontonamn*> använt läckta autentiseringsuppgifter.
    
    Därefter> \< *kontokontonamnet* delats över \< *antalet*> unika filer i en enda session.

- **Inloggningshändelse från användare med läckta autentiseringsuppgifter till ransomware i molnapp**
    
    Den här aviseringen är en indikation \<på att inloggningshändelsen efter *kontonamn*> använt läckta autentiseringsuppgifter. 
    
    Därefter> \< *kontokontonamnet* uppladdat \< *antal*> filer och raderade totalt \< *antal*> filer. 
    
    Denna aktivitet mönster är ett tecken på en potentiell ransomware attack.

## <a name="next-steps"></a>Nästa steg

Nu har du lärt dig mer om avancerad flerstegsattackidentifiering, du kanske är intresserad av följande snabbstart för att lära dig hur du får insyn i dina data och potentiella hot: [Kom igång med Azure Sentinel](quickstart-get-visibility.md).

Om du är redo att undersöka de incidenter som har skapats för dig läser du följande självstudie: [Undersök incidenter med Azure Sentinel](tutorial-investigate-cases.md).

