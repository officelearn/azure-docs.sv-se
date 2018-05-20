---
title: Inställningar och dataroaming vanliga frågor och svar | Microsoft Docs
description: Ger svar på frågor IT-administratörer kan ha om inställningar och data appsynkronisering.
services: active-directory
keywords: Enterprise tillstånd centrala inställningar för windows-moln, vanliga frågor och svar för enterprise tillstånd centrala
documentationcenter: ''
author: tanning
manager: mtillman
editor: curtand
ms.assetid: c0824f5c-129b-4240-969f-921f6a64eae7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/14/2018
ms.author: markvi
ms.openlocfilehash: f33376d5f68d64495a7a90e62870f3ec14f73246
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
---
# <a name="settings-and-data-roaming-faq"></a>Vanliga frågor och svar om inställningar och dataväxling
Den här artikeln besvarar några frågor som IT-administratörer kan ha om inställningar och data appsynkronisering.

## <a name="what-data-roams"></a>Vilka data flyttas?
**Windows-inställningar**: datorinställningar som är inbyggda i Windows-operativsystemet. I allmänhet dessa finns inställningar som anpassar datorn och de innehåller följande kategorier:

* *Temat*, som innehåller funktioner, till exempel inställningar för skrivbord teman och Aktivitetsfältet.
* *Internet Explorer-inställningar*, inklusive nyligen öppnade flikar och Favoriter.
* *Kant webbläsarinställningar*, till exempel läsa listan och Favoriter.
* *Lösenord*, inklusive lösenord för Internet och Wi-Fi-profiler.
* *Språkinställningar*, som innehåller inställningar för tangentbordslayouter, systemspråk, datum och tid.
* *Enkel åtkomstfunktioner*, till exempel hög kontrast tema, Skärmförstoraren och Skärmläsaren.
* *Andra Windows-inställningar*, till exempel inställningar för Kommandotolken och lista.

**Programdata**: universella Windows-appar kan skriva inställningsdata till en central mapp och alla data som skrivs till den här mappen synkroniseras automatiskt. Den kan enskilda apputvecklare att utforma en app för att dra nytta av den här funktionen. Mer information om hur du utvecklar en universell Windows-appar som använder centrala finns i [appdata lagring API](https://msdn.microsoft.com/library/windows/apps/mt299098.aspx) och [Windows 8 appdata centrala bloggen för utvecklare](http://blogs.msdn.com/b/windowsappdev/archive/2012/07/17/roaming-your-app-data.aspx).

## <a name="what-account-is-used-for-settings-sync"></a>Vilket konto används för synkronisering av inställningar?
I Windows 8 och Windows 8.1 används synkronisera inställningar alltid konsumenten Microsoft-konton. Företagsanvändare hade möjlighet att ansluta ett Microsoft-konto till deras Active Directory domänkonto för att få åtkomst till inställningar för synkronisering. I Windows 10 anslutna detta Microsoft-konto funktioner ersätts med ett konto för primära och sekundära ramverk.

Det primära kontot har definierats som det konto som används för att logga in på Windows. Detta kan vara ett Microsoft-konto, ett konto i Azure Active Directory (Azure AD), en lokal Active Directory-konto eller ett lokalt konto. Utöver primära konto, Windows 10-användare kan lägga till ett eller flera sekundära molnet konton sin enhet. Ett sekundärt konto är vanligtvis ett Microsoft-konto, en Azure AD konto eller några andra t.ex Gmail eller Facebook. Dessa sekundära konton ger åtkomst till ytterligare tjänster, till exempel enkel inloggning och Windows Store, men de kan inte stänga synkronisera inställningar.

I Windows 10, det primära kontot för enheten kan användas för synkronisering av inställningar (se [hur uppgraderar jag från inställningar för synkronisering av Microsoft-konto i Windows 8 till Azure AD sync inställningar i Windows 10?](active-directory-windows-enterprise-state-roaming-faqs.md#how-do-i-upgrade-from-microsoft-account-settings-sync-in-windows-8-to-azure-ad-settings-sync-in-windows-10)).

Data blandas aldrig mellan olika användarkonton på enheten. Det finns två regler för synkronisering av inställningar:

* Windows-inställningar kommer alltid flyttas med det primära kontot.
* AppData kommer taggas med det konto som användes för att hämta appen. Endast appar som är märkta med primära konto synkroniseras. Appen ägarskap taggning bestäms när en app är sidoladdad via Windows Store eller hantering av mobila enheter (MDM).

Om en app ägare kan identifieras, kommer den flyttas med det primära kontot. Om en enhet har uppgraderats från Windows 8 eller Windows 8.1 till Windows 10 taggas alla appar som skapats av Microsoft-konto. Detta beror på att de flesta användare skaffa appar via Windows Store och det fanns inget Windows Store-stöd för Azure AD-konton innan Windows 10. Om en app har installerats via en offline-licens kommer appen taggas med det primära kontot på enheten.

> [!NOTE]
> Windows 10-enheter som ägs av företaget och är anslutna till Azure AD kan inte längre ansluta sitt Microsoft-konton till ett domänkonto. Möjligheten att ansluta ett Microsoft-konto till ett domänkonto och har alla användares datasynkronisering till Microsoft-konto (det vill säga den Microsoft-konto centrala via anslutna Microsoft-konto och Active Directory-funktioner) tas bort från Windows 10-enheter som är anslutna till en ansluten Active Directory eller Azure AD-miljö.
>
>

## <a name="how-do-i-upgrade-from-microsoft-account-settings-sync-in-windows-8-to-azure-ad-settings-sync-in-windows-10"></a>Hur uppgraderar jag från inställningar för synkronisering av Microsoft-konto i Windows 8 till Azure AD sync inställningar i Windows 10?
Om du är ansluten till Active Directory-domän som kör Windows 8 eller Windows 8.1 med anslutna Microsoft-konto synkroniseras inställningarna via ditt Microsoft-konto. När du har uppgraderat till Windows 10, fortsätter att synkronisera användarinställningar via Microsoft-konto så länge som du är ansluten till domänen användare och Active Directory-domänen kan inte anslutas till Azure AD.

Om lokala Active Directory-domän kan anslutas med Azure AD, försöker enheten synkronisera inställningar med hjälp av den anslutna Azure AD-kontot. Om Azure AD-administratör inte kan Enterprise tillstånd växling dina anslutna Azure AD-kontot kommer sluta synkronisera inställningar. Om du är en Windows 10-användare och du loggar in med en Azure AD-identitet, startar du synkroniserar windows-inställningar som administratören kan synkronisera inställningar via Azure AD.

Om du har lagrat personliga data på enheten företagets bör du vara medveten om att Windows-Operativsystemet och program börjar synkroniseras till Azure AD. Detta har följande effekter:

* Inställningarna för ditt personliga Microsoft-konto kommer driva förutom inställningarna på arbetet eller skolan Azure AD-konton. Detta beror på att Microsoft-konto och Azure AD-inställningar synkronisera nu använder separata konton.
* Personliga data, till exempel Wi-Fi-lösenord, web autentiseringsuppgifter och Favoriter i Internet Explorer som redan har synkroniserats via ett anslutna Microsoft-konto synkroniseras via Azure AD.

## <a name="how-do-microsoft-account-and-azure-ad-enterprise-state-roaming-interoperability-work"></a>Hur gör Microsoft-konto och Azure AD Enterprise tillstånd centrala samverkan arbete?
I November 2015 eller senare versioner av Windows 10 stöds Enterprise tillstånd centrala bara för ett enskilt konto i taget. Om du loggar in till Windows med ett arbets- eller skolkonto för Azure AD, synkroniseras alla data via Azure AD. Om du loggar in till Windows med ett personligt microsoftkonto synkroniseras alla data via Microsoft-konto. Universal appdata ska flyttas via endast det primära inloggning kontot på enheten och den kommer flyttas endast om appens licens ägs av det primära kontot. Universal appdata för appar som ägs av alla sekundära konton synkroniseras inte.

## <a name="do-settings-sync-for-azure-ad-accounts-from-multiple-tenants"></a>Inställningar synkroniseras för Azure AD-konton från flera klienter?
När flera Azure AD-konton från olika Azure AD-klienter är på samma enhet, måste du uppdatera enhetens registret för att kommunicera med Azure Rights Management-tjänsten för varje Azure AD-klient.  

1. Hitta GUID för varje Azure AD-klient. Öppna Azure-portalen och välj en Azure AD-klient. GUID för klienten är på egenskapssidan för den valda klientorganisationen (https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties), namngivna **katalog-ID**. 
2. När du har GUID som du behöver lägga till registernyckeln **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\SettingSync\WinMSIPC\<klient-ID-GUID >**.
   Från den **klient-ID-GUID** nyckeln, skapa ett nytt flersträngsvärde (REG-MULTI-SZ) med namnet **AllowedRMSServerUrls**. Ange licensiering distribution point URL: er i Azure klienter som ansluter till enheten för sina data.
3. Du kan hitta licensiering distribution point URL: er genom att köra den **Get-AadrmConfiguration** cmdlet från modulen AADRM. Om värdena för den **LicensingIntranetDistributionPointUrl** och **LicensingExtranetDistributionPointUrl** är olika, ange båda värdena. Om värdena är samma, värdet anges en gång.

## <a name="what-are-the-roaming-settings-options-for-existing-windows-desktop-applications"></a>Vad är alternativen centrala inställningar för befintliga Windows-datorprogram?
Centrala fungerar bara för universella Windows-appar. Det finns två alternativ för att aktivera roaming på ett befintligt stationära Windows-program:

* Den [Desktop Bridge](https://aka.ms/desktopbridge) hjälper dig att konfigurera dina befintliga Windows-program för universella Windows-plattformen. Från den här ska minimala kodändringar krävas för att dra nytta av Azure AD-appdata. Desktop-nätverksbrygga är dina appar med en app identitet, vilket krävs för att aktivera AppData för befintliga skrivbordsprogram.
* [Användarens upplevelse virtualisering (UE-V)](https://technet.microsoft.com/library/dn458947.aspx) kan du skapa en mall för anpassade inställningar för befintliga Windows-program och aktivera växling för Win32-appar. Det här alternativet kräver inte apputvecklaren att ändra koden i appen. UE-V är begränsad till lokala Active Directory roaming för kunder som har köpt Microsoft Desktop Optimization Pack.

Administratörer kan konfigurera UE-V för att flyttas Windows desktop AppData genom att ändra centrala inställningar för Windows-Operativsystemet och universell AppData via [grupprinciper för UE-V](https://technet.microsoft.com/itpro/mdop/uev-v2/configuring-ue-v-2x-with-group-policy-objects-both-uevv2), inklusive:

* Flytta Grupprincip för Windows-inställningar
* Synkronisera inte Grupprincip för Windows-appar
* Internet Explorer centrala i programavsnittet

Microsoft kan i framtiden, Undersök sätt att göra UE-V djupt integrerad i Windows och utöka UE-V för att flytta inställningar via Azure AD-molnet.

## <a name="can-i-store-synced-settings-and-data-on-premises"></a>Kan jag lagra synkroniserade inställningar och data lokalt?
Enterprise tillstånd centrala lagrar alla synkroniserade data i Azure-molnet. UE-V erbjuder en lokal centrala lösning.

## <a name="who-owns-the-data-thats-being-roamed"></a>Vem äger datan som är att flyttade?
Företag egna data flyttade via Enterprise tillstånd nätverksväxling. Data lagras i ett Azure-datacenter. Alla användardata krypteras både under överföring och i vila i molnet med Azure Rights Management-tjänsten från Azure Information Protection. Detta är en förbättring jämfört med Microsoft-konto-baserade inställningar sync, som krypterar vissa känsliga data, till exempel autentiseringsuppgifter innan de lämnar enheten.

Microsoft strävar efter att skydda kundens data. En enterprise-användare inställningsdata krypteras automatiskt med Azure Rights Management-tjänsten innan du lämnar en Windows 10-enhet, så att andra användare kan läsa informationen. Om din organisation har en betald prenumeration på Azure Rights Management-tjänsten, du kan använda andra skyddsfunktioner, till exempel spåra och återkalla dokument automatiskt skydda e-postmeddelanden som innehåller känslig information och hantera egna nycklar (i ”ta din egen nyckel ”lösning, kallas även BYOK). Mer information om de här funktionerna och hur den här skyddstjänsten fungerar finns [vad är Azure Rights Management](https://docs.microsoft.com/azure/information-protection/understand-explore/what-is-information-protection).

## <a name="can-i-manage-sync-for-a-specific-app-or-setting"></a>Kan jag hantera synkronisering för en viss app eller inställningen?
I Windows 10 finns det ingen MDM eller en grupprincip inställning att inaktivera roaming för ett enskilt program. Innehavaradministratörer kan inaktivera appdata synkronisering för alla program på en hanterad enhet, men det finns ingen ökad kontroll på en per app eller i app-nivå.

## <a name="how-can-i-enable-or-disable-roaming"></a>Hur kan aktivera eller inaktivera centrala?
I den **inställningar** app, gå till **konton** > **synkronisera dina inställningar**. Den här sidan ser du vilket konto används för att flytta inställningarna och du kan aktivera eller inaktivera enskilda grupper av inställningar för att vara flyttade.

## <a name="what-is-microsofts-recommendation-for-enabling-roaming-in-windows-10"></a>Vad är Microsofts rekommendation för att aktivera centrala i Windows 10?
Microsoft har några olika inställningar för roaming lösningar som är tillgängliga, inklusive centrala användarprofiler, UE-V och Enterprise tillstånd centrala.  Microsoft strävar efter att en investering i företag tillstånd centrala i framtida versioner av Windows. Om din organisation inte är klar eller bekväm med att flytta data till molnet, sedan rekommenderar vi att du använder UE-V som din primära centrala teknik. Om din organisation kräver centrala stöd för befintliga program för Windows-skrivbordet, men är gärna att flytta till molnet, rekommenderar vi att du använder Enterprise tillstånd centrala och UE-V. Men UE-V- och Enterprise tillstånd centrala är mycket liknande tekniker, är de inte ömsesidigt uteslutande. De kompletterar varandra för att säkerställa att din organisation tillhandahåller centrala tjänster som användarna behöver.  

När du använder Enterprise tillstånd centrala och UE-V, gäller följande regler:

* Enterprise tillstånd växling är primär centrala agenten på enheten. UE-V används för att komplettera ”Win32 lucka”.
* UE-V roaming för Windows-inställningar och data med moderna UWP-app ska inaktiveras när principerna för UE-V-grupp. Dessa omfattas redan av centrala Enterprise-tillstånd.

## <a name="how-does-enterprise-state-roaming-support-virtual-desktop-infrastructure-vdi"></a>Hur Enterprise tillstånd centrala stöder virtuell datorinfrastruktur (VDI)?
Enterprise tillstånd centrala stöds på Windows 10-klient SKU: er, men inte på servern SKU: er. Om en klient VM finns på en hypervisor-dator och du loggar in via fjärranslutning till den virtuella datorn, kommer dina data flyttas. Om flera användare delar samma OS och användare loggar in till en server för en fullständig Skrivbordsmiljö via fjärranslutning, kanske roaming inte fungerar. Det senare fallet sessionsbaserad stöds inte officiellt.

## <a name="what-happens-when-my-organization-purchases-a-subscription-that-includes-azure-rights-management-after-using-roaming"></a>Vad händer när organisationen köper en prenumeration som omfattar Azure Rights Management när du använder centrala?
Om din organisation redan använder nätverksväxling i Windows 10 med den kostnadsfria prenumerationen begränsad användning för Azure Rights Management, kan du köpa en [betald prenumeration](https://azure.microsoft.com/pricing/details/information-protection/) som innehåller Azure Rights Management inte har skyddstjänsten inverkan på funktionaliteten för den centrala och inga konfigurationsändringar krävs av IT-administratören.

## <a name="known-issues"></a>Kända problem
Finns i dokumentationen i den [felsökning](active-directory-windows-enterprise-state-roaming-troubleshooting.md) avsnittet för en lista över kända problem. 

## <a name="related-topics"></a>Relaterade ämnen
* [Övergripande översikt över Enterprise tillstånd](active-directory-windows-enterprise-state-roaming-overview.md)
* [Aktivera företagsroaming i Azure Active Directory](active-directory-windows-enterprise-state-roaming-enable.md)
* [Gruppen principer och MDM-inställningar för synkronisering av inställningar](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
* [Centrala referens för Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
* [Felsökning](active-directory-windows-enterprise-state-roaming-troubleshooting.md)
