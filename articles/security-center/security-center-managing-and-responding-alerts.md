<properties
   pageTitle="Hantera och åtgärda säkerhetsaviseringar i Azure Security Center | Microsoft Azure"
   description="I det här dokumentet beskrivs hur du hanterar och åtgärdar säkerhetsaviseringar med hjälp av funktionerna i Azure Security Center."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.topic="hero-article"
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/26/2016"
   ms.author="yurid"/>

# Hantera och åtgärda säkerhetsaviseringar i Azure Security Center
Det här dokumentet beskriver hur du använder Azure Security Center för att hantera och svara på säkerhetsvarningar.

> [AZURE.NOTE] För avancerad identifiering rekommenderar vi att du uppgraderar till Azure Security Center. En kostnadsfri 90-dagars utvärderingsversion är tillgänglig. Om du vill uppgradera väljer du Prisnivå i avsnittet om [säkerhetsprinciper](security-center-policies.md). Mer information finns på [prissidan](https://azure.microsoft.com/pricing/details/security-center/).


## Vad är säkerhetsaviseringar?
Security Center samlar automatiskt in, analyserar och integrerar loggdata från Azure-resurser, nätverket och anslutna partnerlösningar som brandväggs- och slutpunktsskyddslösningar för att identifiera verkliga hot och minimera antalet falska positiva identifieringar. En lista över prioriterade säkerhetsvarningar visas i Security Center tillsammans med den information som du behöver för att snabbt undersöka problemet, samt rekommendationer för hur du åtgärdar ett angrepp. Azure Security Center visar också varningar om ”kill chain”-mönster i [Incidenter](security-center-incident.md). 

> [AZURE.NOTE] Mer information om hur identifieringsfunktionerna i Security Center fungerar finns i [Identifieringsfunktioner i Azure Security Center](security-center-detection-capabilities.md).


## Hantera säkerhetsaviseringar

Du kan se aktuella aviseringar i rutan **Security alerts (Säkerhetsaviseringar)**. Öppna Azure Portal och följ stegen nedan om du vill ha mer information om varje typ av varning:

1. På instrumentpanelen i Security Center hittar du rutan **Security alerts (Säkerhetsaviseringar)**.

    ![Panelen Säkerhetsvarningar i Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig1-ga.png)

2.  Om du klickar på rutan öppnas bladet **Security alerts (Säkerhetsaviseringar)** med mer information om aviseringarna så som visas på bilden nedan.

    ![Bladet Säkerhetsvarningar i Säkerhetscenter](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig2-ga.png)

Längst ned i bladet visas information om de olika aviseringarna. Du kan sortera listan genom att klicka på den kolumn som du vill sortera efter. Här följer en förklaring av de olika kolumnerna:

- **Alert (Avisering)**: en kort förklaring av aviseringen
- **Count (Antal)**: antalet problem av just den här typen som upptäckts en specifik dag
- **Detected by (Upptäcktes genom)**: den tjänst som utlöste aviseringen
- **Date (Datum)**: det datum då händelsen inträffade
- **State (Status)**: aktuell status för den här aviseringen Det finns två tillstånd:
    - **Active (Aktiv)**: Säkerhetsproblemet är upptäckt.
    - **Dismissed (Avvisad)**: Säkerhetsaviseringen har avvisats av användaren. Den här statusen visas oftast för problem som undersökts men som avhjälpts eller som visat sig inte vara något verkligt angrepp.

- **Severity (Allvarlighetsgrad)**: kan vara hög, medelhög eller låg

### Filtrera varningar

Aviseringarna kan filtreras efter datum, status och allvarlighetsgrad. Att filtrera kan vara bra när du vill begränsa hur många aviseringar du vill se. Kanske vill du till exempel se säkerhetsaviseringar från det senaste dygnet eftersom du undersöker ett potentiellt angrepp i systemet under den här tiden.

1. Klicka på **Filter** på bladet **Security Alerts (Säkerhetsaviseringar)**. Bladet **Filter** öppnas där du kan välja datum, status och vilka allvarlighetsgrader du vill se.

    ![Filtrera varningar i Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig3-ga.png)

2.  När du undersöker säkerhetsaviseringar upptäcker du kanske att det är ett falsklarm eller att aviseringen gäller förväntade händelser för en viss resurs. Oavsett anledning kan du, om du anser att säkerhetsaviseringen inte är relevant, avvisa den och filtrera bort den så att du inte ser den.  Det finns två sätt att avvisa en säkerhetsavisering. Du kan högerklicka på aviseringen och välja **Dismiss (Avvisa)** eller hålla muspekaren över den och klicka på de tre punkterna till höger om den för att sedan klicka på **Dismiss (Avvisa)**. Du kan se stängda säkerhetsaviseringar genom att klicka på **Filter** och välja **Dismissed (Avvisade)**.

    ![Ignorera varningar i Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig4-ga.png)

### Åtgärda säkerhetsaviseringar

Om du klickar på en säkerhetsavisering får du se vad det var som utlöste aviseringen och om det finns något du kan göra för att stoppa ett pågående angrepp. Säkerhetsaviseringarna är indelade i grupper efter typ och datum. När du klickar på en säkerhetsavisering öppnas ett blad med en lista över de gruppindelade aviseringarna.

![Åtgärda säkerhetsaviseringar i Azure Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig5-ga.png)

I det här fallet utlöstes aviseringarna på grund av misstänkt Remote Desktop Protocol-aktivitet. Den första kolumnen visar vilka resurser som attackerades. Den andra kolumnen visar hur många gånger resursen attackerades. Den tredje kolumnen visar tidpunkten för attacken. Den fjärde kolumnen visar varningens tillstånd. Och den femte kolumnen visar attackens allvarlighetsgrad. När du har granskat den här informationen klickar du på den resurs som angreps så öppnas ett nytt blad.

![Förslag på vad som kan göras för att åtgärda säkerhetsaviseringar i Azure Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig6-ga.png)

I fältet **Beskrivning** på det här bladet finns mer information om händelsen. Här kan du se vad det var som utlöste säkerhetsaviseringen, vilken resurs som är angripen, eventuell IP-adress som angreppet kommer ifrån och rekommendationer om hur du kan åtgärda problemet.  I vissa fall finns ingen IP-adress till källan eftersom IP-adresser inte ingår i alla säkerhetshändelseloggar i Windows.

> [AZURE.NOTE] Vilka åtgärder som rekommenderas varierar beroende på typ av säkerhetsavisering. I vissa fall måste du kanske använda andra funktioner i Azure för att utföra de rekommenderade åtgärderna. Den rekommenderade åtgärden för det här angreppet är till exempel att svartlista IP-adressen som angreppet kommer ifrån genom regler för [nätverks-ACL](../virtual-network/virtual-networks-acl.md) eller en [nätverkssäkerhetsgrupp](../virtual-network/virtual-networks-nsg.md).

## Säkerhetsvarningar efter typ
Samma steg som du följer för att komma åt varningar om misstänkt RDP-aktivitet kan användas för att få åtkomst till andra typer av varningar. Här är några andra exempel på varningar som kan visas i Security Center:

### Potentiell SQL-inmatning
SQL-inmatning är en attack där skadlig kod matas in i strängar som senare skickas till en instans av SQL Server för parsning och körning. Sårbarhet för den här typen av inmatning bör granskas för alla procedurer som konstruerar SQL-instruktioner eftersom SQL Server kör alla syntaktiskt giltiga frågor som den tar emot. 

![SQL-inmatningsvarning](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig9.png) 

Den här varningen tillhandahåller information som hjälper dig att identifiera den angripna resursen, tidpunkten för identifieringen, attackens tillstånd, samt innehåller även en länk till steg för vidare utredning.

### Identifierad misstänkt utgående trafik

Nätverksenheter kan identifieras och profileras på i stort sett samma sätt som andra typer av system. Angripare börjar vanligtvis med att skanna och söka av portar. Exemplet nedan visar misstänkt SSH-trafik från en virtuell dator som kan vara i färd med att utföra en SSH-råstyrkeattack eller avsökningsattack mot en extern resurs. 

![Varning om misstänkt utgående trafik](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig-10.png)

Den här varningen tillhandahåller information som hjälper dig att identifiera resursen som användes för att initiera attacken, den komprometterade datorn, tidpunkten för identifieringen, samt det protokoll och den port som användes. Det här bladet visar också en lista över de steg som du kan följa för att åtgärda problemet.

### Nätverkskommunikation med en obehörig dator
 
Med hjälp av Microsofts insamling av hotinformation kan Azure Security Center identifiera komprometterade datorer som kommunicerar med skadliga IP-adresser, ofta ett kommando- eller kontrollcenter. I detta fall upptäckte Azure Security Center att kommunikationen gjordes med programmet Pony Loader (även kallat [Fareit](https://www.microsoft.com/security/portal/threat/encyclopedia/entry.aspx?Name=PWS:Win32/Fareit.AF)).

![Varning om nätverkskommunikation med en obehörig dator](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig9-ga.png)

Den här varningen tillhandahåller information som hjälper dig att identifiera den resurs som användes för att initiera attacken, den angripna resursen, den drabbade IP-adressen, angriparens IP-adress och tidpunkten för identifieringen.

> [AZURE.NOTE] Live-IP-adresserna har tagits bort i den här skärmbilden i sekretessyfte.

### Shellcode-identifiering 

Shellcode är nyttolasten som körs när skadlig kod har utnyttjat en sårbarhet i ett program.  Den här varningen anger att en analys av kraschdumpfiler har identifierat körbar kod som uppvisar beteenden som vanligen utförs av skadliga nyttolaster.  Icke-skadlig programvara kan uppvisa detta beteende, men det är inte typiskt i normala programutvecklingsrutiner. 

Följande fält är gemensamma för alla varningar relaterade till kraschdumpar:

- DUMPFILE: Namnet på kraschdumpfilen. 
- PROCESS: Namnet på processen som kraschat. 
- PROCESSVERSION: Versionen för processen som kraschat. 

Den här varningen tillhandahåller följande ytterligare fält:

- ADDRESS: Shellcode-kodens plats i minnet

Det här är ett exempel på den här typen av varning:

![Shellcode-varning](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig10-ga.png)

### Identifierad kodinmatning

Kodinmatning är inmatningen av körbara moduler i processer eller trådar som körs.  Den här tekniken används av skadlig kod för att komma åt, dölja eller förhindra borttagningen av data (t.ex. persistence).  Den här varningen anger att en analys av kraschdumpfiler har identifierat en inmatad modul.
 
Legitima programutvecklare kan ibland utföra den här typen av kodinmatning för icke-skadliga ändamål, t.ex för att ändra eller utöka ett befintligt program eller en komponent i operativsystemet.  För att skilja mellan skadlig och icke-skadlig inmatning av moduler kontrollerar Azure Security Center om den inmatade modulen överensstämmer med en profil för misstänkt beteende. Resultatet av den här kontrollen anges i ”SIGNATURE”-fältet i varningen och avspeglas i varningens allvarlighetsgrad, varningsbeskrivningen och i stegen för att åtgärda varningen.  

Förutom fälten som beskrivs i avsnittet ”Shellcode-identifiering” ovan, visar den här varningen följande fält:

- ADDRESS: Den inmatade modulens plats i minnet.
- IMAGENAME: Namnet på den inmatade modulen. Observera att det kan vara tomt om avbildningsnamnet inte finns angivet i avbildningen.
- SIGNATURE: Anger om den inmatade modulen överensstämmer med en profil för misstänkt beteende. Följande tabell innehåller exempel på resultaten och deras beskrivning:

| **Signaturens värde**                  | **Beskrivning**                                                                                                   |
|--------------------------------------|-------------------------------------------------------------------------------------------------------------------|
| Misstänkt intrång med reflektiv inläsning | Den här typen av misstänkt beteende är ofta kopplat till inläsningen av inmatad kod oberoende av operativsystemets inläsningsfunktion. |
| Misstänkt intrång med inmatning av kod          | Den här typen av misstänkt intrång är ofta kopplat till inmatningen av kod i minnet.                                       |
| Misstänkt intrång med inmatad kod         | Den här typen av misstänkt intrång är ofta kopplat till användningen av kod som matats in i minnet.                                   |
| Misstänkt intrång med inmatat felsökningsverktyg | Den här typen av misstänkt intrång är ofta kopplat till identifieringen av eller försök att kringgå en felsökare.                         |
| Misstänkt intrång med fjärrinmatning   | Den här typen av misstänkt intrång är ofta kopplat till C2-scenarier (kommando och kontroll).                                 |

Det här är ett exempel på den här typen av varning:

![Identifierad kodinmatning](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig11-ga.png)

### Identifierad modulkapning

Windows är beroende av DLL:er (Dynamic Link Library) för att program ska kunna använda vanliga systemfunktioner i Windows.  DLL-kapning inträffar när skadlig kod ändrar inläsningsordningen för DLL-filer för att läsa in skadliga nyttolaster i minnet, där godtycklig kod kan köras. Den här varningen anger att en analys av kraschdumpfiler har upptäckt att en modul med liknande namn har lästs in från två olika sökvägar, där en av de inlästa sökvägarna kommer från en vanlig plats för binära Windows-systemfiler.

Legitima programutvecklare ändrar ibland inläsningsordningen för DLL-filer av icke-skadliga skäl, till exempel för instrumentering eller för att utöka Windows OS- eller Windows-program.  För att skilja mellan skadlig och potentiellt oskadliga ändringar av DLL-filernas inläsningsordning kontrollerar Azure Security Center om en inläst modul överensstämmer med en misstänkt profil eller inte.   Resultatet av den här kontrollen anges i ”SIGNATURE”-fältet i varningen och avspeglas i varningens allvarlighetsgrad, varningsbeskrivningen och i stegen för att åtgärda varningen.  En analys av en kopia av den kapade modulen på disk, t.ex. genom att verifiera filernas digitala signatur eller genom att köra en virusgenomsökning, kan ge mer information för att avgöra om kapningsmodulen är skadlig eller inte.

Förutom fälten som beskrivs i avsnittet ”Shellcode-identifiering” ovan, visar den här varningen följande fält:

- SIGNATURE: Anger om kapningsmodulen överensstämmer med en profil för misstänkt beteende.
- HIJACKEDMODULE: Namnet på den kapade Windows-systemmodulen.
- HIJACKEDMODULEPATH: Den kapade Windows-systemmodulens sökväg.
- HIJACKINGMODULEPATH: Kapningsmodulens sökväg. 

Det här är ett exempel på den här typen av varning:

![Varning om DLL-kapning](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig12-ga.png)

### Identifierad maskering av Windows-modul

Skadlig kod kan använda vanliga namn på binära Windows-systemfiler (t.ex. SVCHOST.EXE) eller moduler (t.ex. NTDLL.DLL) för att ”smälta in” och dölja den skadliga programvarans egentliga natur för administratören.  Den här varningen anger att en analys av kraschdumpfiler har upptäckt att en kraschdumpfil innehåller moduler som använder namn på Windows-systemmoduler, men inte uppfyller andra kriterier som är typiska för Windows-moduler. En analys av en kopia av den maskerande modulen på disk kan ge mer information för att avgöra om modulen är skadlig eller inte. Analysen kan inbegripa följande åtgärder:

- Bekräfta att den aktuella filen levereras som en del av ett legitimt programpaket.
- Kontrollera filens digitala signatur. 
- Kör en virusgenomsökning av filen.

Förutom fälten som beskrivs i avsnittet ”Shellcode-identifiering” ovan, visar den här varningen följande fält:

- INFORMATION: Beskriver om modulernas metadata är giltiga och om modulen lästes in från en systemsökväg.
- NAME: Namnet på den maskerande Windows-modulen.
- PATH: Sökvägen till den maskerande Windows-modulen.

Den här varningen extraherar och visar även vissa fält från modulens PE-huvud, till exempel ”CHECKSUM” och ”TIMESTAMP”.  De här fälten visas bara om fälten finns i modulen. Mer information om dessa fält finns i [Microsoft PE- och COFF-specifikationen](https://msdn.microsoft.com/windows/hardware/gg463119.aspx).

Det här är ett exempel på den här typen av varning:

![Varning om maskering](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig13-ga.png)

### Identifierad ändring av binär systemfil 

Skadlig kod kan ändra grundläggande systembinärfiler för att komma åt data eller ligga kvar i ett komprometterat system utan att bli upptäckt.  Den här varningen anger att en analys av kraschdumpfiler har upptäckt att grundläggande Windows OS-binärfiler har ändrats i minnet eller på disk. 

Legitima programutvecklare kan ibland ändra systemmoduler i minnet för icke-skadliga ändamål, till exempel Detours eller för programkompatibilitet. För att skilja mellan skadliga och potentiellt oskadliga moduler kontrollerar Azure Security Center om modulen överensstämmer med en misstänkt profil eller inte.   Resultatet av den här kontrollen avspeglas i varningens allvarlighetsgrad, varningsbeskrivningen och stegen för att åtgärda problemet. 

Förutom fälten som beskrivs i avsnittet ”Shellcode-identifiering” ovan, visar den här varningen följande fält:

- MODULNAME: Namnet på den modifierade systembinärfilen. 
- MODULEVERSION: Den modifierade systembinärfilens version.

Det här är ett exempel på den här typen av varning:

![Varning om ändrad binärfil](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig14-ga.png)


## Se även

I det här avsnittet har vi berättat hur du ställer in säkerhetsprinciper i Security Center. I följande avsnitt kan du lära dig mer om Security Center:

- [Hantera säkerhetsincidenter i Azure Security Center](security-center-incident.md)
- [Identifieringsfunktioner i Azure Security Center](security-center-detection-capabilities.md)
- [Planering- och bruksanvisning för Azure Security Center](security-center-planning-and-operations-guide.md)
- [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här finns vanliga frågor om tjänsten.
- [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/) – Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.



<!--HONumber=sep16_HO1-->


