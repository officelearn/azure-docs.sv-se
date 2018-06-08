---
title: 'Azure Active Directory Connect: Vanliga frågor och svar - | Microsoft Docs'
description: Den här sidan har vanliga frågor om Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: 4e47a087-ebcd-4b63-9574-0c31907a39a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 2e5a7cab5c9db0c13ca0c0986c18c86adf675562
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34850294"
---
# <a name="frequently-asked-questions-for-azure-active-directory-connect"></a>Vanliga frågor om Azure Active Directory Connect

## <a name="general-installation"></a>Allmän installation
**F: installationen fungerar om den Azure AD-administratör har aktiverat 2FA?**  
Det här scenariot stöds med versionerna från februari 2016.

**F: finns det ett sätt att installera Azure AD Connect obevakad?**  
Det kan bara användas för att installera Azure AD Connect med installationsguiden. En obevakad och tyst installation stöds inte.

**F: Jag har en skog där en domän inte kan kontaktas. Hur installerar Azure AD Connect?**  
Det här scenariot stöds med versionerna från februari 2016.

**F: hälsoagenten AD DS fungerar på server core?**  
Ja. När du har installerat agenten måste slutföra du registreringen med följande PowerShell-cmdlet: 

`Register-AzureADConnectHealthADDSAgent -Credentials $cred`

**F: AADConnect stöder Synkronisera från två domäner till på Azure AD?**</br>
Ja, det här scenariot stöds. Referera till [flera domäner](active-directory-aadconnect-multiple-domains.md)
 
**F: kan du ha flera kopplingar för samma Active Directory-domän i Azure AD connect?**</br> Ingen, flera kopplingar för samma AD-domänen stöds inte. 

**F: kan jag flytta Azure AD Connect-databasen från den lokala databasen till en fjärransluten SQL Server?**</br> Ja, följande steg ger allmänna riktlinjer för hur du gör detta.  Vi arbetar på ett mer detaljerad dokument som blir snart tillgänglig.


   1. Säkerhetskopiera LocalDB ”ADSync” databasen det enklaste sättet att göra detta är att använda SQL Server Management Studio installerat på samma dator som Azure AD Connect. Ansluta till ”(localdb)\.\ADSync” – sedan säkerhetskopiera databasen ADSync
   2. Återställa databasen ”ADSync” till fjärr-SQL-instans
   3. Installera Azure AD Connect mot den befintliga [fjärranslutna SQL-databas](active-directory-aadconnect-existing-database.md) länken visar de steg som krävs när du migrerar till med hjälp av en lokal SQL-databas. Om du migrerar till att använda en fjärransluten SQL-databas i steg 5 i den här processen kommer också måste du ange ett befintligt tjänstkonto som använder Windows-synkroniseringstjänsten. Den här motorn synkroniseringstjänstkontot som beskrivs här:</br></br>
   **Använd ett befintligt tjänstkonto**– som standard som Azure AD Connect använder ett virtuellt tjänstkonto för Sync services för att använda. Om du använder en fjärransluten SQLServer eller använder en proxyserver som kräver autentisering, måste du använda ett hanterat tjänstkonto eller använda ett tjänstkonto i domänen och känna till lösenordet. I detta fall anger du det konto som ska användas. Kontrollera att användaren som kör installationen är en SA i SQL så att en inloggning för tjänstkontot kan skapas. Se [Azure AD Connect: Konton och behörigheter](active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-account).</br></br> Med den senaste versionen kan SQL-administratören nu distribuera databasen ”out of band” och därefter kan den installeras av Azure AD Connect-administratören med databasägarrättigheter. Läs mer i informationen om hur du [installerar Azure AD Connect med SQL-delegerade administratörsbehörigheter](active-directory-aadconnect-sql-delegation.md).

Om du vill behålla saker enkla rekommenderas det att användaren installerar Azure AD Connect är en SA i SQL. (Men med nya versioner kan du nu använda delegerad SQL-administratör enligt [här](active-directory-aadconnect-sql-delegation.md).

## <a name="network"></a>Nätverk
**F: Jag har en brandvägg, nätverksenhet eller något annat som begränsar de maximala tiden anslutningarna kan vara öppen i nätverket. Hur lång tid ska min klientsidan timeout tröskelvärdet vara när du använder Azure AD Connect?**  
Alla nätverksprogramvara fysiska enheter eller något annat som begränsar den maximala tiden anslutningar kan förbli öppen bör använda ett tröskelvärde på minst 5 minuter (300 sekunder) för anslutningen mellan den server där Azure AD Connect-klienten är installerad och Azure Active Directory. Den här rekommendationen gäller även alla tidigare Microsoft Identity synkroniseringsverktyg.

**F: är SLDs (enkel etikett domäner) stöds?**  
Nej, Azure AD Connect har inte stöd för lokala skogar och domäner med SLDs.

**F: är skogar med åtskilda AD-domäner som stöds?**  
Nej, Azure AD Connect har inte stöd för lokala skogar som innehåller åtskilda namnområden.

**F: är ”prickad” NetBios-namnet stöds?**  
Nej, Azure AD Connect har inte stöd för lokala skogar och domäner där NetBios-namnet innehåller en punkt ””. i namnet.

**F: är rent IPv6-miljö som stöds?**  
Nej, Azure AD Connect inte stöder endast IPv6-miljö.

## <a name="federation"></a>Federation
**F: Vad gör jag om jag får ett e-postmeddelande som ber mig att förnya certifikatet min Office 365**  
Använd de anvisningar som beskrivs i den [förnya certifikat](active-directory-aadconnect-o365-certs.md) dokument om hur du förnyar certifikatet.

**F: Jag har ”Uppdatera automatiskt förlitande part” för O365 förlitande part. Måste jag göra något när min certifikatet för tokensignering automatiskt rullar över?**  
Använd de anvisningar som beskrivs i artikel [förnya certifikat](active-directory-aadconnect-o365-certs.md).

## <a name="environment"></a>Miljö
**F: finns det stöd för att byta namn på servern när du har installerat Azure AD Connect?**  
Nej. Ändrar namnet på servern kan Synkroniseringsmotorn inte kan ansluta till SQL-databasen och tjänsten kommer inte att kunna starta.

## <a name="identity-data"></a>Identitetsdata
**F: UPN (userPrincipalName) attributet i Azure AD matchar inte lokal UPN - varför?**  
Finns i följande artiklar:

* [Användarnamnen i Office 365, Azure eller Intune matchar inte lokal UPN eller alternativ inloggnings-ID](https://support.microsoft.com/en-us/kb/2523192)
* [Ändringarna synkroniserats inte med verktyget Azure Active Directory-synkronisering när du ändrar UPN-namnet för ett användarkonto om du vill använda en annan federerad domän](https://support.microsoft.com/en-us/kb/2669550)

Du kan också konfigurera Azure AD för att tillåta att uppdatera userPrincipalName som beskrivs i Synkroniseringsmotorn [Azure AD Connect sync tjänstens funktioner](active-directory-aadconnectsyncservice-features.md).

**F: är det stöd till mjuka matchning lokala AD-gruppen/kontakt-objekt med den befintliga Azure AD-grupp/kontakta objekt?**  
Ja, mjuk matchningen baseras på proxyAddress.  Mjuka matchar stöds inte för grupper som inte är aktiverade för e-post.

**F: är det stöd för att manuellt ange ImmutableId-attributet på befintliga Azure AD-grupp/kontakt-objekt som matchar den hårddisk lokala AD-gruppen/kontakt objekt?**  
Nej, manuellt ställa in attributet ImmutableId på ett befintligt Azure AD-grupp/kontakt-objekt till hårddisken matchar den för närvarande stöds inte.

## <a name="custom-configuration"></a>Anpassad konfiguration
**F: var dokumenteras PowerShell-cmdlets för Azure AD Connect?**  
Andra PowerShell-cmdletar finns i Azure AD Connect stöds inte för kundens användning med undantag för de cmdlets som beskrivs i den här platsen.

**F: kan jag använda ”Server exportservern/importera” hittades i *Synchronization Service Manager* att flytta configuration mellan servrar?**  
Nej. Det här alternativet kommer inte att hämta alla konfigurationsinställningar och ska inte användas. I stället bör du använda guiden för att skapa den grundläggande konfigurationen på den andra servern och använda redigeraren för synkronisering av regeln för att generera PowerShell-skript för att flytta en anpassad regel mellan servrar. Se [svänga migrering](active-directory-aadconnect-upgrade-previous-version.md#swing-migration).

**F: kan cachelagras lösenord för Azure inloggningssidan och kan detta förhindras eftersom den innehåller ett lösenord indataelementet med Komplettera automatiskt = ”false” attributet?**</br>
Ändra HTML-attribut för ett lösenordsfält, stöds inklusive autocomplete-taggen inte. En funktion som tillåter för anpassad Javascript, så att du kan lägga till ett attribut i lösenordsfältet är för tillfället.

**F: på Azure-inloggning sidan visas användarnamn för användare som har tidigare har loggat in.  Det här beteendet stängas av?**</br>
Ändra HTML-attribut för ett lösenordsfält, stöds inklusive autocomplete-taggen inte. En funktion som tillåter för anpassad Javascript, så att du kan lägga till ett attribut i lösenordsfältet är för tillfället.

**F: finns det ett sätt att förhindra samtidiga sessioner?**</br>
Nej.

## <a name="auto-upgrade"></a>Uppgradera automatiskt

**F: Vad är fördelarna och konsekvenserna av att använda automatisk uppgradering?**</br>
Alla kunder bör aktivera automatisk uppgradering för sina Azure AD Connect-installationen. Fördelarna är att de alltid får de senaste korrigeringarna, inklusive säkerhetsuppdateringar säkerhetsproblem som har hittats i Azure AD Connect. Uppgraderingsprocessen är problemfritt och sker automatiskt när en ny version är tillgänglig. Azure AD Connect kunder tusentals använda uppgradera automatiskt med varje ny utgåva.

Automatisk uppgraderingsprocessen alltid först upprättar om en installation lämpar sig för automatisk uppgradering (den här processen omfattar söker efter egna ändringar till regler, specifika miljöfaktorer etc.) och om så uppgraderingen utförs och testas. Om testerna visar att en uppgradering inte har lyckats, kommer den tidigare versionen automatiskt hämta återställas.

Processen kan ta några timmar beroende på storleken på miljön och medan uppgraderingen sker ingen synkronisering mellan Windows Server AD och Azure AD sker.

**F: Jag har fått ett e-postmeddelande om att uppgradera min automatiskt fungerar inte längre som jag behöver installera en ny version. Varför måste jag göra detta?**</br>
Senaste året, en version av Azure AD Connect som under vissa omständigheter kan ha inaktiverats automatiskt uppgradera funktionen på servern, släpptes. Det här problemet har korrigerats i Azure AD Connect version 1.1.750.0. Kunder som har drabbats av det här problemet måste du köra ett PowerShell-skript för att åtgärda detta eller uppgradera till den senaste versionen av Azure AD Connect för att minimera problemet manuellt. 

Kör PowerShell-skriptet genom att ladda ned skriptet från [här](https://aka.ms/repairaadconnect) och kör skript på AADConnect-servern i ett administrativa PowerShell-fönster. [Det här är en kort video](https://aka.ms/repairaadcau) som beskrivs i detalj hur du gör detta.

Om du vill uppgradera manuellt, måste du hämta och kör den senaste versionen av filen AADConnect.msi.
 
-  Om din nuvarande version är äldre än 1.1.750.0, måste du uppgradera till den senaste versionen [som kan hämtas](https://www.microsoft.com/en-us/download/details.aspx?id=47594).
- Om din Azure AD Connect-version är 1.1.750.0 eller senare, behöver du inte göra något för att minimera uppgradera problemet automatiskt som du redan på den version som har en korrigering för detta. 

**F: Jag har fått ett e-postmeddelande om att uppgradera till den senaste versionen för att aktivera automatisk uppgradering på nytt. Jag är på 1.1.654.0, måste du uppgradera?** </br>    
Ja, du måste uppgradera till 1.1.750 eller nyare återaktivera uppgradera automatiskt. Här är en länk som förklarar hur du uppgraderar till en nyare version

**F: Jag har fått ett e-postmeddelande om att uppgradera till den senaste versionen för att aktivera automatisk uppgradering på nytt. Jag har använt PowerShell för att aktivera automatisk uppgradering, behöver jag fortfarande installera den senaste versionen?**</br>    
Ja, du fortfarande behöver uppgradera till version 1.1.750.0 eller senare. Aktivera automatisk uppgradering tjänsten med PowerShell minska inte automatiskt uppgradera problemet finns i versionerna innan 1.1.750

**F: Jag vill uppgradera till en nyare version, men jag är inte säker som har installerat Azure AD Connect och vi har inte användarnamn och lösenord.  Behövs detta?**</br>
Du behöver inte veta användarnamnet och lösenord som ursprungligen användes för att uppgradera Azure AD Connect – Azure AD-kontot som har rollen Global administratör kan användas.

**F: hur kan jag hitta vilken version av Azure AD Connect jag på?**</br>   
Om du vill kontrollera vilken version av Azure AD Connect är installerat på servern, gå till Kontrollpanelen och leta upp den installerade versionen av Microsoft Azure AD Connect i ”program > program och-funktioner:

![version](media/active-directory-aadconnect-faq/faq1.png)

**F: hur utför en uppgradering till den senaste versionen av AADConnect?**</br>    
Detta [artikel](active-directory-aadconnect-upgrade-previous-version.md) förklarar hur du uppgraderar till en nyare version. 

**F: vi redan har uppgraderats till den senaste versionen av AADConnect förra året, behöver vi att uppgradera igen?**</br> Azure AD Connect-team gör uppdateras ofta till tjänsten och det är viktigt att servern är uppdaterad med den senaste versionen för att dra nytta av felkorrigeringar och säkerhetsuppdateringar samt nya funktioner. Om du aktiverar automatisk uppgradering programvaruversion kommer att uppdateras automatiskt. Om du vill hitta versionshistorik av Azure AD Connect, följer du detta [länk](active-directory-aadconnect-version-history.md).

**F: hur lång tid tar det för att utföra uppgraderingen och vad som är effekten på Mina användare?**</br>    
Den tid som behövs för att uppgradera beror på storleken på din klient och för större organisationer kan det vara bäst att göra detta i kväll eller helg. Inga synkroniseringsåtgärden äger rum under uppgraderingen.

**F: Jag tror att jag har uppgraderat till AADConnect men i Office-portalen fortfarande nämns DirSync.  Varför är detta?**</br>    
Office-teamet arbetar för att hämta Office-portalen uppdateringar så att den återger produktnamnet på aktuella – avspeglar inte vilka synkroniseringsverktyget som du använder.

**F: jag kontrolleras min uppgradera automatiskt status och det står ”avbruten”. Varför är det avbruten? Bör jag aktivera det?**</br>     
Ett programfel introducerades i en tidigare version som i vissa fall skulle innebära att automatisk uppgraderingsstatus inställd på ”avbrutna”. Manuellt om du aktiverar det är tekniskt möjligt men kräver flera komplexa steg så att det bästa som du kan göra är att installera den senaste versionen av Azure AD Connect

**F: mitt företag har strikt ändra verksamhetskrav och vill styra när den skickas. Kan du styra när automatiska uppgradering startas?**</br> Nej, det finns ingen sådan funktion idag, den här funktionen är något som utvärderar för framtida versioner.

**F: kan jag får ett e-postmeddelande om den automatiska uppgraderingen misslyckades? Hur vet jag att lyckades?**</br>     
Du meddelas inte om resultatet av uppgraderingen, den här funktionen är något som utvärderar för framtida versioner.

**Q:do du publicerar en tidslinje för när du planerar att skicka ut automatiska uppgraderingar?**</br>    
Uppgradera automatiskt är det första steget i processen för versionen av en nyare version, så när det finns en ny version automatiska uppgraderingar ska skickas. Nyare versioner av Azure AD Connect är förhand angivna i den [översikt över Azure AD](../../active-directory/whats-new.md).

**F: automatiska uppgradering uppgradera AAD Connect Health?**</br>   Ja, uppgradera automatiskt uppgraderas AAD Connect Health även

**F: behöver du också uppgradera automatiskt AAD Connect-servrar i Mellanlagringsläge?**</br>   
Ja, du kan automatiska uppgradering en Azure AD Connect-server som är i mellanlagringsläge.

**F: om automatiska uppgradering misslyckas och servern AAD Connect inte startar, vad ska jag göra?**</br>   
I sällsynta fall kan startar Azure AD Connect-tjänsten inte efter uppgraderingen. I dessa fall startar du om servern, vilket vanligtvis för att rätta till problemet. Öppna ett supportärende om Azure AD Connect-tjänsten fortfarande inte startar. Här är en [länken](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/) som förklarar hur du gör. 

**F: Jag vet inte vilka risker är när du uppgraderar till en nyare version av Azure AD Connect. Kan du anropa mig om du vill hjälpa mig med uppgraderingen?**</br>
Om du behöver hjälp uppgradering till en nyare version av Azure AD Connect, öppna ett supportärende här är en [länken](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/) som visar hur du gör.

## <a name="troubleshooting"></a>Felsökning
**F: hur kan jag få hjälp med Azure AD Connect?**

[Sök i Microsoft Knowledge Base (KB)](https://www.microsoft.com/en-us/Search/result.aspx?q=azure%20active%20directory%20connect&form=mssupport)

* Söka i Microsoft Knowledge Base (KB) för tekniska lösningar på vanliga problem med reparations om stöd för Azure AD Connect.

[Microsoft Azure Active Directory-forum](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)

* Du kan söka och Bläddra för tekniska frågor och svar från gemenskapen eller egna fråga genom att klicka på [här](https://social.msdn.microsoft.com/Forums/azure/en-US/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).

[Hur du kan få support för Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto)



