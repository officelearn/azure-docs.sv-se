---
title: "Säkerhetsaviseringar per typ i Azure Security Center | Microsoft Docs"
description: "Det här dokumentet visar vilken typ av säkerhetsaviseringar som är tillgängliga i Azure Security Center."
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
ms.date: 02/06/2017
ms.author: yurid
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: bdd7d3d6e532efe7c3ed8225dd29a895725f2ff9


---
# <a name="security-alerts-by-type-in-azure-security-center"></a>Säkerhetsaviseringar per typ i Azure Security Center
Det här dokumentet visar de olika typerna av säkerhetsaviseringar som är tillgängliga i Azure Security Center. Mer information om att hantera dessa aviseringar finns i [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md).

> [!NOTE]
> För avancerad identifiering rekommenderar vi att du uppgraderar till Azure Security Center. En kostnadsfri 90-dagars utvärderingsversion är tillgänglig. Om du vill uppgradera väljer du Prisnivå i avsnittet om [säkerhetsprinciper](security-center-policies.md). Mer information finns på [prissidan](https://azure.microsoft.com/pricing/details/security-center/).
>
>

## <a name="what-type-of-alerts-are-available"></a>Vilka typer av aviseringar finns?
Azure Security Center innehåller olika aviseringar för de olika stegen i händelsekedjan. I bilden nedan visas några exempel på olika typer av aviseringar som är relaterade till vissa av dessa steg.

![Händelsekedja](./media/security-center-alerts-type/security-center-alerts-type-fig1.png)

**Mål och attack**

* Inkommande RDP/SSH-attacker
* Program- och DDoS-attacker (WAF-partners)
* Intrångsidentifiering (NG-brandväggspartners)

**Installera och använda**

* Signaturer för känd skadlig kod (AM-partners)
* Skadlig InMemory-kod och trojanförsök
* Körning av misstänkt process
* Kringgående manövrar för att undvika identifiering
* Sidorörelse
* Intern spaning
* Misstänkt PowerShell-aktivitet

**Efter intrång**  

* Kommunikation till känd skadlig IP-adress (dataexfiltrering eller kommando- och kontroll)
* Använder komprometterade resurser för att skapa fler attacker (den utgående porten söker i RDP/SSH efter råstyrkeattacker och skräppost)

Olika typer av attacker är associerade med varje steg och de hanterar olika delsystem. För att hantera attacker under dessa faser har Security Center tre kategorier av aviseringar:

* Virtual Machine Behavioral Analysis (VMBA)
* Nätverksanalys
* Resursanalys

## <a name="virtual-machine-behavioral-analysis"></a>Beteendeanalys av virtuell dator
Azure Security Center kan använda beteendeanalyser för att identifiera resurser som har komprometterats, baserat på analysen av händelseloggar i virtuella datorer, t.ex. processgenereringshändelser, inloggningshändelser etc. Dessutom korreleras data med andra tecken för att hitta bevis på utbredda attacker.

> [!NOTE]
> Mer information om hur identifieringsfunktionerna i Security Center fungerar finns i [Identifieringsfunktioner i Azure Security Center](security-center-detection-capabilities.md).
>
>

### <a name="crash-analysis"></a>Kraschanalys
Analysen där kraschen dumpar minnet är en metod som används för att identifiera avancerad skadlig kod som kan undvika traditionella säkerhetslösningar. Olika former av skadlig kod försöker undvika att identifieras av antivirusprodukter genom att aldrig skriva till disk, eller genom att kryptera programvarukomponenter som skrivs till disk. Detta gör det svårt att identifiera den skadliga koden med traditionella antivirusprogram. Sådan skadlig kod kan dock identifieras med minnesanalyser eftersom den skadliga koden måste lämna spår i minnet för att kunna fungera.

När programvara kraschar fångar en kraschdumpfil en del av minnet vid tidpunkten för kraschen. Kraschen kan ha orsakats av skadlig kod, allmänna program- eller systemproblem. Genom att analysera minnet i kraschdumpen kan Azure Security Center identifiera tekniker som utnyttjar sårbarheter i programvara, som kommer åt känsliga data och som ligger gömda på en komprometterad dator. Detta åstadkoms med minsta möjliga prestandapåverkan på de värdar där analysen utförs av Security Centers backend.

De fält som är gemensamma för alla analysaviseringar vid kraschdumpar visas nedan:

* DUMPFILE: Namnet på kraschdumpfilen.
* PROCESS: Namnet på processen som kraschat.
* PROCESSVERSION: Versionen för processen som kraschat.

### <a name="shellcode-discovered"></a>Identifierad Shellcode
Shellcode är nyttolasten som körs när skadlig kod har utnyttjat en sårbarhet i ett program. Den här varningen anger att en analys av kraschdumpfiler har identifierat körbar kod som uppvisar beteenden som vanligen utförs av skadliga nyttolaster. Icke-skadlig programvara kan uppvisa detta beteende, men det är inte typiskt i normala programutvecklingsrutiner.

Den här varningen tillhandahåller följande ytterligare fält:

* ADDRESS: Shellcode-kodens plats i minnet

Det här är ett exempel på den här typen av varning:

![Shellcode-varning](./media/security-center-alerts-type/security-center-alerts-type-fig2.png)

### <a name="module-hijacking-discovered"></a>Identifierad modulkapning
Windows är beroende av DLL:er (Dynamic Link Library) för att program ska kunna använda vanliga systemfunktioner i Windows. DLL-kapning inträffar när skadlig kod ändrar inläsningsordningen för DLL-filer för att läsa in skadliga nyttolaster i minnet, där godtycklig kod kan köras. Den här varningen anger att en analys av kraschdumpfiler har upptäckt att en modul med liknande namn har lästs in från två olika sökvägar, där en av de inlästa sökvägarna kommer från en vanlig plats för binära Windows-systemfiler.

Legitima programutvecklare ändrar ibland inläsningsordningen för DLL-filer av icke-skadliga skäl, till exempel för instrumentering eller för att utöka Windows OS- eller Windows-program. För att skilja mellan skadlig och potentiellt oskadliga ändringar av DLL-filernas inläsningsordning kontrollerar Azure Security Center om en inläst modul överensstämmer med en misstänkt profil eller inte. Resultatet av den här kontrollen anges i ”SIGNATURE”-fältet i varningen och avspeglas i varningens allvarlighetsgrad, varningsbeskrivningen och i stegen för att åtgärda varningen. En analys av en kopia av den kapade modulen på disk, t.ex. genom att verifiera filernas digitala signatur eller genom att köra en virusgenomsökning, kan ge mer information för att avgöra om kapningsmodulen är skadlig eller inte.

Förutom fälten som beskrivs i avsnittet ”Shellcode-identifiering” ovan, visar den här varningen följande fält:

* SIGNATURE: Anger om kapningsmodulen överensstämmer med en profil för misstänkt beteende.
* HIJACKEDMODULE: Namnet på den kapade Windows-systemmodulen.
* HIJACKEDMODULEPATH: Den kapade Windows-systemmodulens sökväg.
* HIJACKINGMODULEPATH: Kapningsmodulens sökväg.

Det här är ett exempel på den här typen av varning:

![Varning vid modulkapning](./media/security-center-alerts-type/security-center-alerts-type-fig3.png)

### <a name="masquerading-windows-module-detected"></a>Identifierad maskering av Windows-modul
Skadlig kod kan använda vanliga namn på binära Windows-systemfiler (t.ex. SVCHOST.EXE) eller moduler (t.ex. NTDLL.DLL) för att ”smälta in” och dölja den skadliga programvarans egentliga natur för administratören. Den här varningen anger att en analys av kraschdumpfiler har upptäckt att en kraschdumpfil innehåller moduler som använder namn på Windows-systemmoduler, men inte uppfyller andra kriterier som är typiska för Windows-moduler. En analys av en kopia av den maskerande modulen på disk kan ge mer information för att avgöra om modulen är skadlig eller inte. Analysen kan inbegripa följande åtgärder:

* Bekräfta att den aktuella filen levereras som en del av ett legitimt programpaket.
* Kontrollera filens digitala signatur.
* Kör en virusgenomsökning av filen.

Förutom fälten som beskrivs i avsnittet ”Shellcode-identifiering” ovan, visar den här varningen följande fält:

* INFORMATION: Beskriver om modulernas metadata är giltiga och om modulen lästes in från en systemsökväg.
* NAME: Namnet på den maskerande Windows-modulen.
* PATH: Sökvägen till den maskerande Windows-modulen.

Den här varningen extraherar och visar även vissa fält från modulens PE-huvud, till exempel ”CHECKSUM” och ”TIMESTAMP”. De här fälten visas bara om fälten finns i modulen. Mer information om dessa fält finns i [Microsoft PE- och COFF-specifikationen](https://msdn.microsoft.com/windows/hardware/gg463119.aspx).

Det här är ett exempel på den här typen av varning:

![Avisering om Windows-maskering](./media/security-center-alerts-type/security-center-alerts-type-fig4.png)

### <a name="modified-system-binary-discovered"></a>Identifierad ändring av binär systemfil
Skadlig kod kan ändra grundläggande systembinärfiler för att komma åt data eller ligga kvar i ett komprometterat system utan att bli upptäckt. Den här varningen anger att en analys av kraschdumpfiler har upptäckt att grundläggande Windows OS-binärfiler har ändrats i minnet eller på disk.
Legitima programutvecklare kan ibland ändra systemmoduler i minnet för icke-skadliga ändamål, till exempel Detours eller för programkompatibilitet. För att skilja mellan skadliga och potentiellt oskadliga moduler kontrollerar Azure Security Center om modulen överensstämmer med en misstänkt profil eller inte. Resultatet av den här kontrollen avspeglas i varningens allvarlighetsgrad, varningsbeskrivningen och stegen för att åtgärda problemet.

Förutom fälten som beskrivs i avsnittet ”Shellcode-identifiering” ovan, visar den här varningen följande fält:

* MODULNAME: Namnet på den modifierade systembinärfilen.
* MODULEVERSION: Den modifierade systembinärfilens version.

Det här är ett exempel på den här typen av varning:

![Varning om binär systemfil](./media/security-center-alerts-type/security-center-alerts-type-fig5.png)

### <a name="suspicious-process-executed"></a>Misstänkt process kördes
Security Center identifierar misstänkta processer som körs i den virtuella måldatorn och utlöser en avisering. Identifieringen letar inte efter det specifika namnet, utan efter dess parameter. Det innebär att även om angriparen byter namn på den körbara filen kan Security Center fortfarande identifiera den.

Det här är ett exempel på den här typen av varning:

![Varning om misstänkt process](./media/security-center-alerts-type/security-center-alerts-type-fig6-new.png)

### <a name="multiple-domain-accounts-queried"></a>Flera domänkonton efterfrågas
Security Center kan identifiera flera försök att fråga efter domänkonton, vilket vanligtvis utförs av angripare under spaning i nätverket. Angripare kan använda den här metoden för att fråga domänen och identifiera vilka som är användare, vilka konton som är domänadministratörskonton, vilka datorer som är domänkontrollanter och även potentiella domänförtroenderelationer med andra domäner.

Det här är ett exempel på den här typen av varning:

![Varning om flera domänkonton](./media/security-center-alerts-type/security-center-alerts-type-fig7-new.png)

## <a name="network-analysis"></a>Nätverksanalys
Hotidentifieringen i nätverk av Security Center sker genom automatisk insamling av säkerhetsinformation från din Azure IPFIX-trafik (Internet Protocol Flow Information Export). Tjänsten analyserar den här informationen, och korrelerar ofta information från flera källor för att identifiera hot.

### <a name="suspicious-outgoing-traffic-detected"></a>Identifierad misstänkt utgående trafik
Nätverksenheter kan identifieras och profileras på i stort sett samma sätt som andra typer av system. Angripare börjar vanligtvis med att skanna och söka av portar. Exemplet nedan visar misstänkt SSH-trafik från en virtuell dator som kan vara i färd med att utföra en SSH-råstyrkeattack eller avsökningsattack mot en extern resurs.

![Varning om misstänkt utgående trafik](./media/security-center-alerts-type/security-center-alerts-type-fig8.png)

Den här varningen tillhandahåller information som hjälper dig att identifiera resursen som användes för att initiera attacken, den komprometterade datorn, tidpunkten för identifieringen, samt det protokoll och den port som användes. Det här bladet visar också en lista över de steg som du kan följa för att åtgärda problemet.

### <a name="network-communication-with-a-malicious-machine"></a>Nätverkskommunikation med en obehörig dator
Med hjälp av Microsofts insamling av hotinformation kan Azure Security Center identifiera komprometterade datorer som kommunicerar med skadliga IP-adresser, ofta ett kommando- eller kontrollcenter. I detta fall upptäckte Azure Security Center att kommunikationen gjordes med programmet Pony Loader (även kallat [Fareit](https://www.microsoft.com/security/portal/threat/encyclopedia/entry.aspx?Name=PWS:Win32/Fareit.AF)).

![varning om nätverkskommunikation](./media/security-center-alerts-type/security-center-alerts-type-fig9.png)

Den här varningen tillhandahåller information som hjälper dig att identifiera den resurs som användes för att initiera attacken, den angripna resursen, den drabbade IP-adressen, angriparens IP-adress och tidpunkten för identifieringen.

> [!NOTE]
> Live-IP-adresserna har tagits bort i den här skärmbilden i sekretessyfte.
>
>

### <a name="possible-outgoing-denial-of-service-attack-detected"></a>Möjlig utgående Denial of Service-attack upptäcktes
Avvikande nätverkstrafik som kommer från en virtuell dator kan leda till att Security Center utlöser en potentiell Denial of Service-typ av angrepp.

Det här är ett exempel på den här typen av varning:

![Utgående DOS](./media/security-center-alerts-type/security-center-alerts-type-fig10-new.png)

## <a name="resource-analysis"></a>Resursanalys
Resursanalysen i Security Center fokuserar på PaaS-tjänster, till exempel integration med funktionen [Hotidentifiering i Azure SQL DB](../sql-database/sql-database-threat-detection-get-started.md). Baserat på analysresultatet från dessa områden utlöser Security Center en relaterad resursavisering.

### <a name="potential-sql-injection"></a>Potentiell SQL-inmatning
SQL-inmatning är en attack där skadlig kod matas in i strängar som senare skickas till en instans av SQL Server för parsning och körning. Sårbarhet för den här typen av inmatning bör granskas för alla procedurer som konstruerar SQL-instruktioner eftersom SQL Server kör alla syntaktiskt giltiga frågor som den tar emot. SQL-hotidentifiering använder Machine Learning, beteendeanalys och avvikelseidentifiering för att hitta misstänkta händelser i Azure SQL-databaserna. Några exempel:

* Försök till databasåtkomst av en tidigare anställd
* SQL-inmatningsattacker
* Onormal åtkomst till produktionsdatabasen från en användare hemifrån

![Varning om potentiell SQL-inmatning](./media/security-center-alerts-type/security-center-alerts-type-fig11.png)

Den här varningen tillhandahåller information som hjälper dig att identifiera den angripna resursen, tidpunkten för identifieringen, attackens tillstånd, samt innehåller även en länk till steg för vidare utredning.

### <a name="vulnerability-to-sql-injection"></a>Sårbarhet för SQL-inmatning
Den här aviseringen utlöses när ett programfel har identifierats i en databas, vilket kan tyda på en eventuell sårbarhet för SQL-inmatningsattacker.

![Varning om potentiell SQL-inmatning](./media/security-center-alerts-type/security-center-alerts-type-fig12-new.png)

### <a name="unusual-access-from-unfamiliar-location"></a>Onormal åtkomst från en okänd plats
Den här aviseringen utlöses när en åtkomst från en okänd IP-adress har identifierats på servern, och som inte har synts den senaste perioden.

![Varning om onormal åtkomst](./media/security-center-alerts-type/security-center-alerts-type-fig13-new.png)

## <a name="see-also"></a>Se även
I det här dokumentet lär du dig om de olika typerna av säkerhetsvarningar i Security Center. I följande avsnitt kan du lära dig mer om Security Center:

* [Hantera säkerhetsincidenter i Azure Security Center](security-center-incident.md)
* [Identifieringsfunktioner i Azure Security Center](security-center-detection-capabilities.md)
* [Planerings- och bruksanvisning för Azure Security Center](security-center-planning-and-operations-guide.md)
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/) – Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.



<!--HONumber=Dec16_HO1-->


