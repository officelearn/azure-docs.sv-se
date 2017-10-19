---
title: "Säkerhetsaviseringar per typ i Azure Security Center | Microsoft Docs"
description: "I den här artikeln beskrivs de olika typerna av säkerhetsaviseringar som är tillgängliga i Azure Security Center."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: b3e7b4bc-5ee0-4280-ad78-f49998675af1
ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2017
ms.author: yurid
ms.openlocfilehash: 274c50dad9b8a1d79a71a29b04cb8e44ad91893c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="understanding-security-alerts-in-azure-security-center"></a>Förstå säkerhetsaviseringar i Azure Security Center
Den här artikeln visar de olika typerna av säkerhetsaviseringar och meddelanden som är tillgängliga i Azure Security Center. Mer information om att hantera aviseringar och händelser finns i [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md).

För avancerad identifiering rekommenderar vi att du uppgraderar till Azure Security Center Standard. En kostnadsfri 60-dagars utvärderingsversion är tillgänglig. Om du vill uppgradera väljer du **Prisnivå** i avsnittet om [säkerhetsprinciper](security-center-policies.md). Mer information finns på [prissidan](https://azure.microsoft.com/pricing/details/security-center/).

> [!NOTE]
> Security Center har för den begränsade förhandsversionen publicerat en ny uppsättning identifieringar som utnyttjar auditd-poster, ett gemensamt granskningsramverk för att upptäcka skadligt beteende på Linux-datorer. Skicka ett e-postmeddelande med dina prenumerations-id:n till [oss](mailto:ASC_linuxdetections@microsoft.com) för att ta del av förhandsversionen.

## <a name="what-type-of-alerts-are-available"></a>Vilka typer av aviseringar finns?
Azure Security Center använder olika [identifieringsfunktioner](security-center-detection-capabilities.md) för att uppmärksamma kunder om eventuella hot mot deras miljöer. Dessa aviseringar innehåller värdefull information om vad som utlöste aviseringen, vilka resurser som berörs och attackens källa. Informationen som ingår i en avisering varierar beroende på vilken typ av analys som används för att identifiera hot. Händelser kan också innehålla ytterligare sammanhangsinformation som kan vara användbart när du utreder ett hot.  Den här artikeln innehåller information om följande aviseringstyper:

* Virtual Machine Behavioral Analysis (VMBA)
* Nätverksanalys
* Resursanalys
* Sammanhangsbaserad information

## <a name="virtual-machine-behavioral-analysis"></a>Beteendeanalys av virtuell dator
Azure Security Center kan använda beteendeanalyser för att identifiera resurser som har komprometterats, baserat på analysen av Event Logs i virtuella datorer. Till exempel processgenereringshändelser och inloggningshändelser. Dessutom korreleras data med andra tecken för att hitta bevis på utbredda attacker.

> [!NOTE]
> Mer information om hur identifieringsfunktionerna i Security Center fungerar finns i [Identifieringsfunktioner i Azure Security Center](security-center-detection-capabilities.md).
>

### <a name="crash-analysis"></a>Kraschanalys
Analysen där kraschen dumpar minnet är en metod som används för att identifiera avancerad skadlig kod som kan undvika traditionella säkerhetslösningar. Olika former av skadlig kod försöker undvika att identifieras av antivirusprodukter genom att aldrig skriva till disk, eller genom att kryptera programvarukomponenter som skrivs till disk. Den här metoden gör det svårt att identifiera den skadliga koden med traditionella antivirusprogram. Sådan skadlig kod kan dock identifieras med minnesanalyser eftersom den skadliga koden måste lämna spår i minnet för att kunna fungera.

När programvara kraschar fångar en kraschdumpfil en del av minnet vid tidpunkten för kraschen. Kraschen kan ha orsakats av skadlig kod, allmänna program- eller systemproblem. Genom att analysera minnet i kraschdumpen kan Azure Security Center identifiera tekniker som utnyttjar sårbarheter i programvara, som kommer åt känsliga data och som ligger gömda på en komprometterad dator. Detta åstadkoms med minsta möjliga prestandapåverkan på de värdar där analysen utförs av Security Centers serverdel.

Följande fält är gemensamma för alla exempel på aviseringar som är relaterade till kraschdumpar som dyker upp senare i den här artikeln:

* DUMPFILE: Namnet på kraschdumpfilen.
* PROCESSNAME: Namnet på processen som kraschat.
* PROCESSVERSION: Versionen för processen som kraschat.

### <a name="shellcode-discovered"></a>Identifierad Shellcode
Shellcode är nyttolasten som körs när skadlig kod har utnyttjat en sårbarhet i ett program. Den här aviseringen anger att en analys av kraschdumpfiler har identifierat körbar kod som uppvisar beteenden som vanligen utförs av skadliga nyttolaster. Icke-skadlig programvara kan uppvisa detta beteende, men det är inte typiskt i normala programutvecklingsrutiner.

Det här Shellcode-aviseringsexemplet tillhandahåller följande ytterligare fält:

* ADDRESS: Shellcode-kodens plats i minnet.

Här är ett exempel på den här typen av varning:

![Shellcode-varning](./media/security-center-alerts-type/security-center-alerts-type-fig2.png)

### <a name="module-hijacking-discovered"></a>Identifierad modulkapning
Windows använder DLL:er (Dynamic Link Library) för att program ska kunna använda vanliga systemfunktioner i Windows. DLL-kapning inträffar när skadlig kod ändrar inläsningsordningen för DLL-filer för att läsa in skadliga nyttolaster i minnet, där godtycklig kod kan köras. Den här aviseringen anger att en analys av kraschdumpfiler har upptäckt en modul med ett liknande namn som har lästs in från två olika sökvägar. En av de inlästa sökvägarna kommer från en vanlig plats för binära Windows-systemfiler.

Legitima programutvecklare ändrar ibland inläsningsordningen för DLL-filer av icke-skadliga skäl, till exempel för instrumentering eller för att utöka Windows OS- eller Windows-program. För att skilja mellan skadlig och potentiellt oskadliga ändringar av DLL-filernas inläsningsordning kontrollerar Azure Security Center om en inläst modul överensstämmer med en misstänkt profil eller inte. Resultatet av den här kontrollen anges i ”SIGNATURE”-fältet i varningen och avspeglas i varningens allvarlighetsgrad, varningsbeskrivningen och i stegen för att åtgärda varningen. En analys av en kopia av den maskerande modulen på disk kan ge mer information för att avgöra om modulen är skadlig eller inte. Du kan till exempel kontrollera filens digitala signatur eller köra en virusgenomsökning.

Förutom fälten som beskrivs i det tidigare avsnittet ”Shellcode-identifiering” ovan, visar den här aviseringen följande fält:

* SIGNATURE: Anger om kapningsmodulen överensstämmer med en profil för misstänkt beteende.
* HIJACKEDMODULE: Namnet på den kapade Windows-systemmodulen.
* HIJACKEDMODULEPATH: Den kapade Windows-systemmodulens sökväg.
* HIJACKINGMODULEPATH: Kapningsmodulens sökväg.

Här är ett exempel på den här typen av varning:

![Varning vid modulkapning](./media/security-center-alerts-type/security-center-alerts-type-fig3.png)

### <a name="masquerading-windows-module-detected"></a>Identifierad maskering av Windows-modul
Skadlig kod kan använda vanliga namn på binära Windows-systemfiler (t.ex. SVCHOST.EXE) eller moduler (t.ex. NTDLL.DLL) för att *smälta in* och dölja den skadliga programvarans egentliga natur för administratören. Den här aviseringen anger att en analys av kraschdumpfiler har upptäckt att en kraschdumpfil innehåller moduler som använder namn på Windows-systemmoduler, men inte uppfyller andra kriterier som är typiska för Windows-moduler. En analys av en kopia av den maskerande modulen på disk kan ge mer information för att avgöra om modulen är skadlig eller inte. Analysen kan inbegripa följande åtgärder:

* Bekräfta att den aktuella filen levereras som en del av ett legitimt programpaket.
* Kontrollera filens digitala signatur.
* Kör en virusgenomsökning av filen.

Förutom fälten som beskrivs i avsnittet ”Shellcode-identifiering” ovan, visar den här aviseringen följande fält:

* INFORMATION: Beskriver om modulernas metadata är giltiga och om modulen lästes in från en systemsökväg.
* NAME: Namnet på den maskerande Windows-modulen.
* PATH: Sökvägen till den maskerande Windows-modulen.

Den här aviseringen extraherar och visar även vissa fält från modulens PE-huvud, till exempel ”CHECKSUM” och ”TIMESTAMP”. De här fälten visas bara om fälten finns i modulen. Mer information om dessa fält finns i [Microsoft PE- och COFF-specifikationen](https://msdn.microsoft.com/windows/hardware/gg463119.aspx).

Här är ett exempel på den här typen av varning:

![Avisering om Windows-maskering](./media/security-center-alerts-type/security-center-alerts-type-fig4.png)

### <a name="modified-system-binary-discovered"></a>Identifierad ändring av binär systemfil
Skadlig kod kan ändra grundläggande systembinärfiler för att komma åt data eller ligga kvar i ett komprometterat system utan att bli upptäckt. Den här aviseringen anger att en analys av kraschdumpfiler har upptäckt att grundläggande Windows OS-binärfiler har ändrats i minnet eller på disk.

Legitima programutvecklare kan ibland ändra systemmoduler i minnet för icke-skadliga ändamål, till exempel Detours eller för programkompatibilitet. För att skilja mellan skadliga och potentiellt oskadliga moduler kontrollerar Azure Security Center om modulen överensstämmer med en misstänkt profil eller inte. Resultatet av den här kontrollen avspeglas i varningens allvarlighetsgrad, varningsbeskrivningen och stegen för att åtgärda problemet.

Förutom fälten som beskrivs i avsnittet ”Shellcode-identifiering” ovan, visar den här aviseringen följande fält:

* MODULNAME: Namnet på den modifierade systembinärfilen.
* MODULEVERSION: Den modifierade systembinärfilens version.

Här är ett exempel på den här typen av varning:

![Varning om binär systemfil](./media/security-center-alerts-type/security-center-alerts-type-fig5.png)

### <a name="suspicious-process-executed"></a>Misstänkt process kördes
Security Center identifierar misstänkta processer som körs i den virtuella måldatorn och utlöser sedan en avisering. Identifieringen letar inte efter det specifika namnet, utan efter den körbara filens parameter. Det innebär att även om angriparen byter namn på den körbara filen kan Security Center fortfarande identifiera den misstänkta processen.

Här är ett exempel på den här typen av varning:

![Varning om misstänkt process](./media/security-center-alerts-type/security-center-alerts-type-fig6-new.png)

### <a name="multiple-domains-accounts-queried"></a>Flera domänkonton efterfrågas
Security Center kan identifiera flera försök att fråga Active Directory-domänkonton, vilket vanligtvis utförs av angripare under spaning i nätverket. Angripare kan använda den här metoden för att fråga domänen och identifiera användarna, identifiera domänadministratörskontona, identifiera datorerna som är domänkontrollanter och även potentiella domänförtroenderelationer med andra domäner.

Här är ett exempel på den här typen av varning:

![Varning om flera domänkonton](./media/security-center-alerts-type/security-center-alerts-type-fig7-new.png)

### <a name="local-administrators-group-members-were-enumerated"></a>Medlemmarna i gruppen lokala administratörer har räknats upp

Security Center utlöser en avisering när säkerhetshändelsen 4798 utlöses i Windows Server 2016 och Windows 10. Detta händer när grupper med lokala administratörer räknas upp, något som vanligtvis utförs av angripare under spaning i nätverket. Angripare kan dra fördel av den här tekniken för att fråga om identiteten för användare med administratörsbehörighet.

Här är ett exempel på den här typen av varning:

![Lokal administratör](./media/security-center-alerts-type/security-center-alerts-type-fig14-new.png)

### <a name="anomalous-mix-of-upper-and-lower-case-characters"></a>Avvikande blandning av versaler och gemener

Security Center utlöser en avisering när den identifierar användning av en blandning av versaler och gemener på kommandoraden. Vissa angripare kan använda den här tekniken för att kringgå skiftlägeskänsliga eller hashbaserade datorregler.

Här är ett exempel på den här typen av varning:

![Avvikande blandning](./media/security-center-alerts-type/security-center-alerts-type-fig15-new.png)

### <a name="suspected-kerberos-golden-ticket-attack"></a>Misstänkt attack av med gyllene Kerberos-biljett

En komprometterad [krbtgt](https://technet.microsoft.com/library/dn745899.aspx)-nyckel kan användas av en angripare för att skapa ”gyllene Kerberos-biljetter” som gör det möjligt för angripare att utge sig för att vara vilken användare de vill. Security Center utlöser en avisering när det identifierar den här typen av aktivitet.

> [!NOTE] 
> Mer information om gyllene Kerberos-biljetter finns i [Åtgärder mot stöld av autentiseringsuppgifter i Windows 10](http://download.microsoft.com/download/C/1/4/C14579CA-E564-4743-8B51-61C0882662AC/Windows%2010%20credential%20theft%20mitigation%20guide.docx).

Här är ett exempel på den här typen av varning:

![Gyllene biljett](./media/security-center-alerts-type/security-center-alerts-type-fig16-new.png)

### <a name="suspicious-account-created"></a>Ett misstänkt konto har skapats

Security Center utlöser en avisering när ett konto skapas som liknar ett befintligt konto med integrerade administratörsprivilegier. Den här tekniken kan användas av angripare för att skapa ett falskt konto för att undvika att upptäckas via mänsklig verifiering.
 
Här är ett exempel på den här typen av varning:

![Misstänkt konto](./media/security-center-alerts-type/security-center-alerts-type-fig17-new.png)

### <a name="suspicious-firewall-rule-created"></a>Misstänkt brandväggsregel skapad

Angripare kan försöka kringgå värdsäkerheten genom att skapa anpassade brandväggsregler för att tillåta skadliga program att kommunicera med kommando och kontroll eller starta attacker i nätverket via den komprometterade värden. Security Center utlöser en avisering när den upptäcker att en ny brandväggsregel har skapats från en körbar fil på en misstänkt plats.
 
Här är ett exempel på den här typen av varning:

![Brandväggsregel](./media/security-center-alerts-type/security-center-alerts-type-fig18-new.png)

### <a name="suspicious-combination-of-hta-and-powershell"></a>Misstänkt kombination av HTA och PowerShell

Security Center utlöser en avisering när den identifierar att en Microsoft HTML-programvärd (HTA) startar PowerShell-kommandon. Det här är en teknik som används av angripare för att starta skadliga PowerShell-skript.
 
Här är ett exempel på den här typen av varning:

![HTA och PS](./media/security-center-alerts-type/security-center-alerts-type-fig19-new.png)


## <a name="network-analysis"></a>Nätverksanalys
Hotidentifieringen i nätverk av Security Center sker genom automatisk insamling av säkerhetsinformation från din Azure IPFIX-trafik (Internet Protocol Flow Information Export). Tjänsten analyserar den här informationen, och korrelerar ofta information från flera källor för att identifiera hot.

### <a name="suspicious-outgoing-traffic-detected"></a>Identifierad misstänkt utgående trafik
Nätverksenheter kan identifieras och profileras på i stort sett samma sätt som andra typer av system. Angripare börjar vanligtvis med att skanna eller söka av portar. I nästa exempel har du SSH-trafik (Secure Shell) från en virtuell dator. I det här scenariot är en SSH-råstyrkeattack eller en avsökningsattack möjlig.

![Varning om misstänkt utgående trafik](./media/security-center-alerts-type/security-center-alerts-type-fig8.png)

Den här aviseringen ger information som du kan använda för att identifiera resursen som användes för att initiera den här attacken. Den här aviseringen tillhandahåller information för att identifiera den komprometterade datorn, tidpunkten för identifieringen samt det protokoll och den port som användes. Den här sidan visar också en lista över de steg som du kan följa för att åtgärda problemet.

### <a name="network-communication-with-a-malicious-machine"></a>Nätverkskommunikation med en obehörig dator
Med hjälp av Microsofts insamling av hotinformation kan Azure Security Center identifiera komprometterade datorer som kommunicerar med skadliga IP-adresser. I många fall är den skadliga adressen ett kommando- eller kontrollcenter. I detta fall upptäckte Security Center att kommunikationen gjordes med programmet Pony Loader (även kallat [Fareit](https://www.microsoft.com/security/portal/threat/encyclopedia/entry.aspx?Name=PWS:Win32/Fareit.AF)).

![varning om nätverkskommunikation](./media/security-center-alerts-type/security-center-alerts-type-fig9.png)

Den här aviseringen tillhandahåller information som hjälper dig att identifiera den resurs som användes för att initiera attacken, den angripna resursen, den drabbade IP-adressen, angriparens IP-adress och tidpunkten för identifieringen.

> [!NOTE]
> Live-IP-adresserna har tagits bort i den här skärmbilden i sekretessyfte.
>
>

### <a name="possible-outgoing-denial-of-service-attack-detected"></a>Möjlig utgående Denial of Service-attack upptäcktes
Avvikande nätverkstrafik som kommer från en virtuell dator kan leda till att Security Center utlöser en potentiell Denial of Service-typ av angrepp.

Här är ett exempel på den här typen av varning:

![Utgående DOS](./media/security-center-alerts-type/security-center-alerts-type-fig10-new.png)

## <a name="resource-analysis"></a>Resursanalys
Resursanalysen i Security Center fokuserar på PaaS-tjänster (platform as a service), till exempel integration med funktionen [Hotidentifiering i Azure SQL Database](../sql-database/sql-database-threat-detection.md). Baserat på analysresultatet från dessa områden utlöser Security Center en relaterad resursavisering.

### <a name="potential-sql-injection"></a>Potentiell SQL-inmatning
SQL-inmatning är en attack där skadlig kod matas in i strängar som senare skickas till en instans av SQL Server för parsning och körning. Sårbarhet för den här typen av inmatning bör granskas för alla procedurer som konstruerar SQL-instruktioner eftersom SQL Server kör alla syntaktiskt giltiga frågor som den tar emot. SQL-hotidentifiering använder Machine Learning, beteendeanalys och avvikelseidentifiering för att hitta misstänkta händelser i Azure SQL-databaserna. Exempel:

* Försök till databasåtkomst av en tidigare anställd
* SQL-inmatningsattacker
* Onormal åtkomst till en produktionsdatabas från en användare hemifrån

![Varning om potentiell SQL-inmatning](./media/security-center-alerts-type/security-center-alerts-type-fig11.png)

Informationen i den här aviseringen kan användas för att identifiera den angripna resursen, tidpunkten för identifieringen och attackens tillstånd. Den innehåller även en länk till steg för vidare utredning.

### <a name="vulnerability-to-sql-injection"></a>Sårbarhet för SQL-inmatning
Den här aviseringen utlöses när ett programfel hittas på en databas. Aviseringen kan tyda på en eventuell sårbarhet för SQL-inmatningsattacker.

![Varning om potentiell SQL-inmatning](./media/security-center-alerts-type/security-center-alerts-type-fig12-new.png)

### <a name="unusual-access-from-unfamiliar-location"></a>Onormal åtkomst från en okänd plats
Den här aviseringen utlöses när en åtkomsthändelse från en okänd IP-adress har identifierats på servern, och som inte har synts den senaste perioden.

![Varning om onormal åtkomst](./media/security-center-alerts-type/security-center-alerts-type-fig13-new.png)

## <a name="contextual-information"></a>Sammanhangsbaserad information
Vid en undersökning måste analytiker tillhandahålla information om sammanhanget så att en bedömning av risken och lämpliga åtgärder kan vidtas.  Till exempel, om en nätverksavvikelse upptäcks är det svårt att bestämma vilka åtgärder som ska vidtas utan att förstå vad som sker på nätverket som helhet eller vilken resurs som är målet för attacken. För att hjälpa med som kan en säkerhetsincident omfatta artefakter, relaterade händelser och information som kan hjälpa utredaren. Tillgängligheten för ytterligare information kan variera beroende på vilken typ av hot som har upptäckts och konfigurationen av din miljö. För vissa säkerhetsincidenter kan sådan information vara otillgänglig.

Om ytterligare information är tillgänglig visas den i säkerhetsincidenten under listan över aviseringar. Detta kan innehålla information som:

- Logga raderingshändelser
- PNP-enhet ansluten från okänd källa
- Aviseringar som inte medför åtgärd 

![Varning om onormal åtkomst](./media/security-center-alerts-type/security-center-alerts-type-fig20.png) 


## <a name="see-also"></a>Se även
I den här artikeln lär du dig om de olika typerna av säkerhetsaviseringar i Security Center. I följande avsnitt kan du lära dig mer om Security Center:

* [Hantera säkerhetsincidenter i Azure Security Center](security-center-incident.md)
* [Identifieringsfunktioner i Azure Security Center](security-center-detection-capabilities.md)
* [Planerings- och bruksanvisning för Azure Security Center](security-center-planning-and-operations-guide.md)
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md): Här hittar du vanliga frågor och svar om tjänsten.
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/): Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.
