<properties
   pageTitle="Vanliga frågor och svar om Azure Backup | Microsoft Azure"
   description="Svar på vanliga frågor om säkerhetskopieringstjänsten, säkerhetskopieringsagenten, säkerhetskopiering och kvarhållning, återställning, säkerhet och andra vanliga frågor om säkerhetskopiering och haveriberedskap."
   services="backup"
   documentationCenter=""
   authors="markgalioto"
   manager="jwhit"
   editor=""
   keywords="backup and disaster recovery; backup service"/>

<tags
   ms.service="backup"
   ms.workload="storage-backup-recovery"
     ms.tgt_pltfrm="na"
     ms.devlang="na"
     ms.topic="get-started-article"
     ms.date="04/04/2016"
     ms.author="trinadhk; giridham; arunak; markgal; jimpark;"/>

# Vanliga frågor och svar om tjänsten Azure Backup

> [AZURE.SELECTOR]
- [Vanliga frågor och svar om säkerhetskopiering i klassiskt läge](backup-azure-backup-faq.md)
- [Vanliga frågor och svar om säkerhetskopiering i ARM-läge](backup-azure-backup-ibiza-faq.md)

Den här artikeln innehåller en lista över vanliga frågor och svar om Azure Backup-tjänsten. Vår community svarar snabbt, och vanliga frågor publiceras i den här artikeln. Svaren på frågorna innehåller ofta referens- eller supportinformation. Du kan ställa frågor om Azure Backup i Disqus-rutan i den här eller en relaterad artikel. Du kan också ställa frågor om Azure Backup-tjänsten i [diskussionsforumet](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

## Installation och konfiguration
**F1. Från vilka operativsystem kan jag säkerhetskopiera till Azure med hjälp av Azure Backup?** <br/>
S1. Azure Backup stöder följande operativsystem


| Operativsystem        | Plattform           | SKU  |
| :------------- |-------------| :-----|
| Windows 8 och senaste Service Pack      | 64-bitars | Enterprise, Pro |
| Windows 7 och senaste Service Pack      | 64-bitars | Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter |
| Windows 8.1 och senaste Service Pack | 64-bitars      |    Enterprise, Pro |
| Windows 10      | 64-bitars | Enterprise, Pro, Home |
|Windows Server 2012 R2 och senaste Service Pack| 64-bitars| Standard, Datacenter, Foundation|
|Windows Server 2012 och senaste Service Pack|    64-bitars| Datacenter, Foundation, Standard|
|Windows Storage Server 2012 R2 och senaste Service Pack  |64-bitars|    Standard, Workgroup|
|Windows Storage Server 2012 och senaste Service Pack |64-bitars |Standard, Workgroup
|Windows Server 2012 R2 och senaste Service Pack  |64-bitars|    Essential|
|Windows Server 2008 R2 SP1 |64-bitars|    Standard, Enterprise, Datacenter, Foundation|
|Windows Server 2008 SP2    |64-bitars|    Standard, Enterprise, Datacenter, Foundation|

**F2. Var kan jag hämta den senaste Azure Backup-agenten?** <br/>
S2. Du kan hämta den senaste agenten för säkerhetskopiering av Windows Server, System Center DPM eller Windows-klienten [här](http://aka.ms/azurebackup_agent). Om du vill säkerhetskopiera en virtuell dator använder du VM-agenten (som automatiskt installerar rätt tillägg). VM-agenten finns redan på virtuella datorer som skapats från Azure-galleriet.

**F3. Vilken version av SCDPM-servern stöds?** <br/>
S3. Vi rekommenderar att du installerar den [senaste](http://aka.ms/azurebackup_agent) Azure Backup-agenten med den senaste samlade uppdateringen av SCDPM (UR6 i juli 2015)

**F4. När jag konfigurerar Azure Backup-agenten uppmanas jag att ange **valvautentiseringsuppgifter**. Slutar valvautentiseringsuppgifterna att gälla?
S4. Ja, valvautentiseringsuppgifterna upphör att gälla efter 48 timmar. Om filen går ut loggar du in på Azure-portalen och laddar ned filerna med valvautentiseringsuppgifterna från Backup-valvet.

**F5. Finns det någon gräns för antalet säkerhetskopieringsvalv som kan skapas i varje Azure-prenumeration?** <br/>
S5. Ja. Från och med juli 2015 kan du skapa 25 valv per prenumeration. Om du behöver flera valv skapar du en ny prenumeration.

**F6. Kan ett valv ses som en faktureringsenhet?** <br/>
S6. Du kan få en detaljerad faktura för varje valv, men vi rekommenderar att du i stället hanterar en Azure-prenumeration som en faktureringsenhet. Den är konstant över alla tjänster och är enklare att hantera.

**F7. Finns det några begränsningar för hur många servrar/datorer som kan registreras mot varje valv?** <br/>
S7. Ja, du kan registrera upp till 50 datorer per valv. För virtuella Azure IaaS-datorer är gränsen 200 virtuella datorer per valv. Om du behöver registrera fler datorer skapar du ett nytt valv.

**F8. Finns det några begränsningar för mängden data som kan säkerhetskopieras från en Windows-server, Windows-klient eller SCDPM-server?** <br/>
S8. Nej.

**F9. Hur registrerar jag min server till ett annat datacenter?**<br/>
S9. Säkerhetskopierade data skickas till datacentret för Backup-tjänsten som den har registrerats för. Det enklaste sättet att ändra datacentret är att avinstallera och installera om agenten och registrera ett nytt datacenter.

**F10. Vad händer om jag byter namn på en Windows-server som säkerhetskopierar data till Azure?**<br/>
S10. När du byter namn på en server stoppas alla säkerhetskopieringar som är konfigurerade för närvarande.
Du måste registrera det nya namnet på servern med Backup-valvet. När du skapar en ny registrering är den första säkerhetskopieringen en fullständig säkerhetskopiering, inte en inkrementell säkerhetskopiering. Om du behöver återställa data som tidigare har säkerhetskopierats till valvet med det gamla servernamnet kan du återställa dessa data med hjälp av alternativet [**En annan server**](backup-azure-restore-windows-server.md#recover-to-an-alternate-machine) i guiden **Återställ data**.


**F11. Från vilka typer av enheter kan jag säkerhetskopiera filer och mappar?** <br/>
S11. Följande uppsättning enheter/volymer kan inte säkerhetskopieras:

- Flyttbart medium: Enheten måste rapporteras som fast för att kunna användas som källa för säkerhetskopior.
- Skrivskyddade volymer: Volymen måste vara skrivbar för att VSS-tjänsten (Volume Shadow Copy) ska fungera.
- Offlinevolymer: Volymen måste vara online för att VSS ska fungera.
- Nätverksresurs: Volymen måste vara lokal på servern för att kunna säkerhetskopieras med onlinesäkerhetskopiering.
- BitLocker-skyddade volymer: Volymen måste vara upplåst innan säkerhetskopieringen kan utföras.
- Filsystemidentifiering: NTFS är det enda filsystemet som stöds för den här versionen av onlinesäkerhetskopieringstjänsten.

**F12. Vilka typer av filer och mappar kan jag säkerhetskopiera från servern?**<br/>
S12. Följande typer stöds:

- Krypterade
- Komprimerade
- Utspridda
- Komprimerade + utspridda
- Hårda länkar: Stöds inte, hoppas över
- Referenspunkt: Stöds inte, hoppas över
- Krypterade + komprimerade: Stöds inte, hoppas över
- Krypterade + utspridda: Stöds inte, hoppas över
- Komprimerad dataström: Stöds inte, hoppas över
- Utspridd dataström: Stöds inte, hoppas över

**F13. Vilken är den minsta nödvändiga storleken på cachelagringsmappen?** <br/>
S13. Storleken på cachelagringsmappen avgör mängden data som säkerhetskopieras. Cachelagringsmappens storlek bör vara 5 % av det utrymme som krävs för att lagra data.

**F14. Min organisation har ett säkerhetskopieringsvalv. Hur kan jag isolera en servers data från en annan server när jag återställer data?**<br/>
S14. Alla servrar som är registrerade för samma valv kan återställa data som säkerhetskopierats av andra servrar *som använder samma lösenfras*. Om du vill isolera säkerhetskopierade data på en server från andra servrar i organisationen kan du använda en annan lösenfras för dessa servrar. HR-servrarna kan till exempel använda en krypteringslösenfras, redovisningsservrarna en annan och lagringsservrar en tredje.

**S15. Kan jag ”migrera” mina säkerhetskopierade data mellan prenumerationer?** <br/>
S15: Nej.

**F16. Kan jag ”migrera” mitt säkerhetskopierade valv mellan prenumerationer?** <br/>
S16: Nej. Valvet skapas på prenumerationsnivå och kan inte tilldelas en annan prenumeration när det har skapats.

**F17. Fungerar Azure Backup Agent på en server som använder Windows Server 2012-deduplicering?** <br/>
S17: Ja. Agenttjänsten konverterar deduplicerade data till vanliga data när den förbereder säkerhetskopieringen. Därefter optimerar tjänsten data för säkerhetskopiering, krypterar dessa data och skickar dem till onlinesäkerhetskopieringstjänsten.

**F18. Tas säkerhetskopierade data som redan har överförts bort om jag avbryter ett säkerhetskopieringsjobb som redan har startat?** <br/>
S18: Nej. Säkerhetskopieringsvalvet lagrar säkerhetskopierade data som har överförts fram till den punkt då jobbet avbröts. Azure Backup använder en kontrollpunktsmekanism för att då och då lägga till kontrollpunkter till säkerhetskopierade data under säkerhetskopieringen. Eftersom det finns kontrollpunkter i säkerhetskopian kan nästa säkerhetskopiering validera filernas integritet. Nästa säkerhetskopiering är en inkrementell säkerhetskopiering som körs mot tidigare säkerhetskopierade data. En inkrementell säkerhetskopiering utnyttjar bandbredden bättre, så att du inte behöver överföra samma data flera gånger.

**F19. Varför visas en varning om att inga Azure-säkerhetskopieringar har konfigurerats för servern, trots att jag har schemalagt regelbundna säkerhetskopieringar tidigare?** <br/>
S19: Den här varningen visas om inställningarna för säkerhetskopieringsschemat på den lokala servern inte är samma som inställningarna som lagras i säkerhetskopieringsvalvet. Om servern eller inställningarna har återställts till ett fungerande tillstånd kan synkroniseringen av säkerhetskopieringsscheman brytas. Om den här varningen visas [omkonfigurerar du säkerhetskopieringspolicyn](backup-azure-manage-windows-server.md) och väljer **Kör säkerhetskopiering nu** för att synkronisera om den lokala servern med Azure.

**F20. Vilka brandväggsregler ska jag konfigurera för Azure Backup?** <br/>
S20. För sömlöst skydd av ”lokala till Azure”-data och ”arbetsbelastning till Azure”-data rekommenderar vi att du tillåter att brandväggen kommunicerar med följande URL:er:

- www.msftncsi.com
- \*.Microsoft.com
- \*.WindowsAzure.com
- \*.microsoftonline.com
- \*.windows.net

**F21. Kan jag installera Azure Backup-agenten på en virtuell dator i Azure som redan har säkerhetskopierats av Azure Backup-tjänsten med hjälp av VM-tillägget?** <br/>
S21. Absolut. Azure Backup stöder säkerhetskopiering på VM-nivå för virtuella datorer i Azure med hjälp av VM-tillägget. Du kan installera Azure Backup-agenten i ett Windows-gästoperativsystem för att skydda filer och mappar i gästoperativsystemet.

**F22. Kan jag installera Azure Backup-agenten på en virtuell dator i Azure om jag vill säkerhetskopiera filer och mappar med tillfällig lagring på den virtuella Azure-datorn?** <br/>
S22. Du kan installera Azure Backup-agenten i Windows-gästoperativsystemet och säkerhetskopiera filer och mappar till ett tillfälligt lagringsutrymme. Observera dock att säkerhetskopieringen misslyckas om du rensar data i tillfällig lagring. Om data i tillfällig lagring har tagits bort kan du bara återställa till beständig lagring.

**F23. Jag har installerat Azure Backup-agenten för att skydda mina filer och mappar. Kan jag nu installera SCDPM för användning med Azure Backup-agenten om jag vill skydda lokala program/VM-arbetsbelastningar i Azure?** <br/>
S23. Om du vill använda Azure Backup med SCDPM rekommenderar vi att du installerar SCDPM först och sedan installerar Azure Backup-agenten. Detta säkerställer en smidig integrering av Azure Backup-agenten med SCDPM och gör att du kan skydda filer/mappar, program och arbetsbelastningar och virtuella datorer i Azure direkt från SCDPM-hanteringskonsolen. Installera inte SCDPM efter att du har installerat Azure Backup-agenten för ovan nämnda ändamål. Detta varken rekommenderas eller stöds.

**F24. Vilken längd på filsökvägar stöds i Azure Backup-principen när Azure Backup-agenten används?** <br/>  
S24. Azure Backup-agenten använder NTFS. [Specifikationen för filsökvägarnas längd begränsas av Windows-API:et](https://msdn.microsoft.com/library/aa365247.aspx#fully_qualified_vs._relative_paths). Om du behöver säkerhetskopiera filer med sökvägar som är längre än vad som tillåts av Windows-API:et kan du välja att säkerhetskopiera den överordnade mappen eller diskenheten där filerna finns.  

**F25. Vilka tecken tillåts i filsökvägar i Azure Backup-principen när Azure Backup-agenten används?** <br>  
S25. Azure Backup-agenten använder NTFS. [Tecken som stöds av NTFS](https://msdn.microsoft.com/library/aa365247.aspx#naming_conventions) stöds enligt filspecifikationen.  

**F26. Kan jag använda Azure Backup Server för att skapa en BMR-säkerhetskopia (Bare Metal Recovery) för en fysisk server?** <br/>
S26. Ja.

**F27. Kan jag konfigurera Backup-tjänsten så att ett e-postmeddelande skickas om ett säkerhetskopieringsjobb misslyckas?** <br/>
S27. Ja, Backup-tjänsten har flera händelsebaserade aviseringar som du kan använda med PowerShell-skript. En fullständig beskrivning finns i [Varningsmeddelanden](backup-azure-manage-vms.md#alert-notifications)



## Säkerhetskopiering och kvarhållning
**F1. Finns det någon storleksgräns för en datakälla som säkerhetskopieras?** <br/>
S1. Från och med augusti 2015 är den största storleken på datakällor för operativsystem som stöds:

|Nr | Operativsystem |  Största storlek på datakälla |
| :-------------: |:-------------| :-----|
|1| Windows Server 2012 eller senare| 54 400 GB|
|2| Windows 8 eller senare| 54 400 GB|
|3| Windows Server 2008, Windows Server 2008 R2 | 1 700 GB|
|4| Windows 7 | 1 700 GB|

Följande tabell beskriver hur datakällans storlek bestäms.

|   Datakälla  |   Detaljer |
| :-------------: |:-------------|
|Volym |Mängden data som säkerhetskopieras från en enskild volym på en server eller klientdator|
|Virtuell Hyper-V-dator | Summan av data på alla virtuella hårddiskar på den virtuella datorn som säkerhetskopieras|
|Microsoft SQL Server-databas | Storleken på en enskild SQL-databas som säkerhetskopieras |
|Microsoft SharePoint |Summan av innehållet och konfigurationsdatabaserna i en SharePoint-servergrupp som säkerhetskopieras|
|Microsoft Exchange |Summan av alla Exchange-databaser på en Exchange-server som säkerhetskopieras|
|BMR/systemtillstånd |Varje enskild kopia av BMR eller systemtillstånd på datorn som säkerhetskopieras|

**F2. Finns det någon gräns för hur många gånger ett säkerhetskopieringsjobb kan schemaläggas per dag?**<br/>
S2. Ja, du kan köra säkerhetskopieringsjobb på Windows Server eller Windows-klienten upp till tre gånger per dag. Du kan köra säkerhetskopieringsjobb i System Center DPM upp till två gånger om dagen. Du kan köra ett säkerhetskopieringsjobb för virtuella IaaS-datorer en gång om dagen.

**F3. Finns det någon tidsskillnad mellan schemaläggningsprincipen för DPM och Windows Server (dvs. på Windows Server utan DPM)?** <br/>
S3. Ja. Med DPM kan du definiera dags-, vecko-, månads- och årsscheman. Med Windows Server (utan DPM) kan du bara definiera dags- och veckoscheman.

**F4. Finns det någon skillnad mellan bevarandeprincipen för DPM och Windows Server/Windows-klienten (dvs. på Windows Server utan DPM)?**<br/>
S4. Ingen, både DPM och Windows Server/Windows-klienten har dagliga, veckovisa, månatliga och årliga bevarandeprinciper.

**F5. Kan jag konfigurera mina bevarandeprinciper selektivt, dvs. konfigurera veckovisa och dagliga men inte årliga och månatliga?**<br/>
S5. Ja, bevarandestrukturen i Azure Backup är mycket flexibel och du kan definiera bevarandeprincipen efter dina behov.

**F6. Kan jag ”schemalägga en säkerhetskopiering” kl. 18:00 och ange ”bevarandeprinciper” vid en annan tidpunkt?**<br/>
S6. Nej. Bevarandeprinciper kan bara användas med säkerhetskopieringspunkter. I följande bild har bevarandeprincipen angetts för säkerhetskopieringar som körs kl. 12:00 och 18:00. <br/>

![Schemalägga säkerhetskopiering och kvarhållning](./media/backup-azure-backup-faq/Schedule.png)
<br/>

**F7. Överförs en inkrementell kopia för de schemalagda bevarandeprinciperna?** <br/>
S7. Nej, den inkrementella kopian skickas baserat på tiden som anges på sidan för säkerhetskopieringsschemat. Vilka punkter som kan bevaras avgörs baserat på bevarandeprincipen.

**F8. Tar det längre tid att återställa en äldre datapunkt om en säkerhetskopia bevaras under lång tid?** <br/>
S8. Nej. Det tar lika lång tid att återställa den äldsta och den senaste punkten. Varje återställningspunkt beter sig som en fullständig punkt.

**F9. Om varje återställningspunkt fungerar som en fullständig punkt, påverkas i så fall den totalt fakturerbara lagringen av säkerhetskopior?**<br/>
S9.  Typiska produkter för långsiktiga kvarhållningspunkter lagrar säkerhetskopierade data som fullständiga punkter. De fullständiga punkterna är *ineffektiva* ur lagringssynvinkel men är enklare och snabbare att återställa. Inkrementella kopior är *lagringseffektiva* men kräver att du återställer en kedja med data som påverkar återställningstiden. Azure Backup-lagringsarkitekturen ger dig det bästa av två världar genom att lagra data för snabb återställning till låga lagringskostnader. Den här datalagringsmetoden säkerställer att den in- och utgående bandbredden används effektivt. Både mängden datalagring och tiden som krävs för att återställa data hålls nere på ett minimum.

**F10. Finns det någon gräns för antalet återställningspunkter som kan skapas?**<br/>
S10. Nej. Vi har tagit bort gränserna för återställningspunkter. Du kan skapa så många återställningspunkter du vill.

**F11. Varför är mängden data som överförs under en säkerhetskopiering inte samma som mängden säkerhetskopierade data?**<br/>
S11. Alla data som säkerhetskopieras komprimeras och krypteras innan de överförs. När komprimering och kryptering tillämpats är storleken på data i säkerhetskopieringsvalvet 30–40 % mindre.

**F12. Kan jag justera mängden bandbredd som används av Backup-tjänsten?**<br/>
S12. Ja, använd alternativet **Ändra egenskaper** i Backup-agenten om du vill justera bandbredden. Justera mängden bandbredd och tiderna då du använder den bandbredden. Mer information finns i [Nätverksbegränsning](../backup-configure-vault.md#enable-network-throttling).

**F13. Min Internetbandbredd är begränsad för den mängd data som jag behöver säkerhetskopiera. Kan jag flytta data till en plats med en stor nätverkspipeline och skicka dessa data till Azure?** <br/>
S13. Du kan säkerhetskopiera data till Azure via standardprocessen för säkerhetskopiering online eller använda tjänsten Azure Import/Export för att överföra data till Blob Storage i Azure. Det finns inga andra sätt att skicka säkerhetskopierade data till Azure-lagring. Information om hur du använder tjänsten Azure Import/Export med Azure Backup finns i artikeln [Arbetsflöde för säkerhetskopiering offline](backup-azure-backup-import-export.md).


## Återställning
**F1. Hur många återställningar kan jag göra av data som säkerhetskopierats till Azure?**<br/>
S1. Det finns ingen gräns för antalet återställningar från Azure Backup.

**F2. Måste jag betala för utgående trafik från Azure-datacentret under återställningar?**<br/>
S2. Nej. Återställningarna är gratis och du debiteras inte för den utgående trafiken.

## Säkerhet
**F1. Krypteras informationen som skickas till Azure?** <br/>
S1. Ja. Data krypteras på den lokala server-/klient-/SCDPM-datorn med hjälp av AES256 och informationen skickas via en säker HTTPS-anslutning.

**F2. Är säkerhetskopierade data i Azure också krypterade?**<br/>
S2. Ja. Data som skickas till Azure förblir krypterade (i vila). Microsoft dekrypterar aldrig dina säkerhetskopierade data.

**F3. Vilken är den minsta längden på krypteringsnyckeln som används för att kryptera säkerhetskopierade data?** <br/>
S3. Krypteringsnyckeln ska vara minst 16 tecken.

**F4. Vad händer om jag tappar bort krypteringsnyckeln? Kan jag (eller Microsoft) återställa mina data?** <br/>
S4. Nyckeln som används för att kryptera säkerhetskopierade data finns bara hos kunden. Microsoft sparar ingen kopia i Azure och har inte åtkomst till nyckeln. Om du tappar bort nyckeln kan inte Microsoft återställa dina säkerhetskopierade data.
 

## Säkerhetskopieringscache

**F1. Hur ändrar jag cachelagringsplatsen för Azure Backup-agenten?**<br/>
S1. Gå igenom punktlistan nedan en punkt i taget om du vill ändra plats för cachelagringen.
- Stoppa Backup-motorn genom att köra följande kommando från en upphöjd kommandotolk:

  ```PS C:\> Net stop obengine```

- Flytta inte filerna. Kopiera i stället cachelagringsmappen till en annan enhet med tillräckligt med utrymme. Du kan ta bort det ursprungliga cachelagringsutrymmet när du har bekräftat att säkerhetskopieringarna fungerar med den nya cachelagringsplatsen.

- Uppdatera följande registerposter med sökvägen till den nya cachelagringsmappen.<br/>

|Sökväg i registret | Registernyckel | Värde |
| ------ | ------- | ------|
| `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` | ScratchLocation | *Ny plats för cachelagringsmappen* |
| `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` | ScratchLocation | *Ny plats för cachelagringsmappen* |

- Starta om Backup-motorn genom att köra följande kommando från en upphöjd kommandotolk:

  ```PS C:\> Net start obengine```

  När säkerhetskopian har skapats på den nya cachelagringsplatsen kan du ta bort den ursprungliga cachelagringsmappen.

**F2. Var kan jag placera cachelagringsmappen så att Azure Backup Agent fungerar korrekt?**<br/>
S2. Följande platser rekommenderas inte för cachelagringsmappen:

- Nätverksresurs eller flyttbart medium: Cachelagringsmappen måste vara lokal på servern som ska säkerhetskopieras med onlinesäkerhetskopiering. Nätverksplatser eller flyttbara medier som USB-enheter stöds inte.
- Offlinevolymer: Cachelagringsmappen måste vara online för säkerhetskopiering med Azure Backup Agent.

**F3. Finns det några attribut för cachelagringsmappen som inte stöds?**<br/>
S3. Följande attribut eller deras kombinationer stöds inte för cachelagringsmappen:

- Krypterade
- Deduplicerade
- Komprimerade
- Utspridda
- Referenspunkt

Vi rekommenderar att du inte använder attributen ovan för cachelagringsmappen eller för den virtuella hårddisken med metadata för att Azure Backup-agenten ska fungera korrekt.



<!--HONumber=jun16_HO2-->


