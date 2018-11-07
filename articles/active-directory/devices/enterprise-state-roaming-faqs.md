---
title: Inställningar och dataväxling vanliga frågor och svar | Microsoft Docs
description: Ger svar på frågor IT-administratörer kan ha om inställningar och data appsynkronisering.
services: active-directory
keywords: Enterprise state roaminginställningarna, windows-molnet, vanliga frågor och svar på enterprise tillståndsväxling
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: curtand
ms.component: devices
ms.assetid: c0824f5c-129b-4240-969f-921f6a64eae7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2018
ms.author: markvi
ms.openlocfilehash: 24c7ddff1eea16fc2be3f99d04abd90ee0791f4c
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51247304"
---
# <a name="settings-and-data-roaming-faq"></a>Vanliga frågor och svar om inställningar och dataväxling
Den här artikeln får du svar på några frågor som IT-administratörer kan ha om inställningar och data appsynkronisering.

## <a name="what-data-roams"></a>Vilka data som flyttas?
**Inställningar för Windows**: datorinställningar som är inbyggda i operativsystemet Windows. I allmänhet bör dessa inställningar som anpassar datorn och de kan vara följande övergripande kategorier:

* *Tema*, som innehåller funktioner, till exempel inställningar för fjärrskrivbord tema och Aktivitetsfältet.
* *Internet Explorer-inställningarna*, inklusive nyligen öppnade flikar och Favoriter.
* *Edge-webbläsarinställningar*, till exempel Favoriter och läsa listan.
* *Lösenord*, inklusive Internet-lösenord och Wi-Fi-profiler.
* *Språkinställningar*, som innehåller inställningar för tangentbordslayout, systemspråk, datum och tid och mycket mer.
* *Enkel åtkomstfunktioner*, till exempel högkontrasttema, Skärmförstoraren och Skärmläsaren.
* *Andra Windows-inställningar*, t.ex musinställningar.

**Programdata**: universella Windows-appar kan skriva inställningsdata till en central mapp och alla data som skrivs till den här mappen synkroniseras automatiskt. Det är upp till enskilda apputvecklaren för att designa en app att dra nytta av den här funktionen. Mer information om hur du utvecklar en universell Windows-app som använder centrala finns i den [API: et för appdata storage](https://msdn.microsoft.com/library/windows/apps/mt299098.aspx) och [Windows 8 appdata roaming utvecklarblogg](https://blogs.msdn.com/b/windowsappdev/archive/2012/07/17/roaming-your-app-data.aspx).

## <a name="what-account-is-used-for-settings-sync"></a>Vilket konto används för synkronisering av inställningar?
I Windows 8.1 för synkronisering av alltid konsument Microsoft-konton. Företagsanvändare hade möjlighet att ansluta ett Microsoft-konto till deras Active Directory-domänkonto som ska få åtkomst till inställningar för synkronisering. I Windows 10 ansluten detta Microsoft-konto funktioner ersätts med ett konto för primär/sekundär ramverk.

Det primära kontot har definierats som används för att logga in på Windows-kontot. Detta kan vara ett Microsoft-konto, ett Azure Active Directory (Azure AD)-konto, en lokal Active Directory-konto eller ett lokalt konto. Förutom det primära kontot Windows 10-användare kan lägga till en eller flera sekundära molnkonton sin enhet. Ett sekundärt konto är vanligtvis ett Microsoft-konto, en Azure AD-konto eller några andra kontot, till exempel Gmail- eller Facebook. Dessa sekundära konton har åtkomst till ytterligare tjänster som enkel inloggning och Windows Store, men de kan inte stöda synkronisering.

I Windows 10, endast det primära kontot för enheten kan användas för synkronisering av inställningar (se [hur uppgraderar jag från synkronisera inställningar för Microsoft-konto i Windows 8 till Azure AD sync inställningar i Windows 10?](enterprise-state-roaming-faqs.md#how-do-i-upgrade-from-microsoft-account-settings-sync-in-windows-8-to-azure-ad-settings-sync-in-windows-10)).

Data är aldrig blandat mellan olika användarkonton på enheten. Det finns två regler för synkronisering av inställningar:

* Windows-inställningar kommer alltid att flyttas med det primära kontot.
* AppData taggas med det konto som användes för att hämta appen. Endast appar som taggats med det primära kontot synkroniseras. App ägarskap taggning bestäms när en app är sidoladdad via Windows Store eller hantering av mobilenheter (MDM).

Om en appägare inte kan identifieras, kommer den flyttas med det primära kontot. Om en enhet uppgraderas från Windows 8 eller Windows 8.1 till Windows 10, taggas alla appar som skapats av Microsoft-konto. Detta är eftersom de flesta användare hämta appar med hjälp av Windows Store och det fanns inget Windows Store-stöd för Azure AD-konton innan Windows 10. Om en app installeras via en offline-licens, taggas appen med det primära kontot på enheten.

> [!NOTE]
> Windows 10-enheter som ägs av företaget och är anslutna till Azure AD kan inte längre ansluta sina Microsoft-konton till ett domänkonto. Möjligheten att ansluta ett Microsoft-konto till ett domänkonto och har alla användarens data sync för att Microsoft-konto (det vill säga Microsoft-kontot nätverksväxling via anslutna Microsoft-konto och Active Directory-funktioner) tas bort från Windows 10 enheter som är anslutna till en ansluten Active Directory eller Azure AD-miljö.
>
>

## <a name="how-do-i-upgrade-from-microsoft-account-settings-sync-in-windows-8-to-azure-ad-settings-sync-in-windows-10"></a>Hur uppgraderar jag från synkronisera inställningar för Microsoft-konto i Windows 8 till Azure AD sync inställningar i Windows 10?
Om du är ansluten till Active Directory-domän som kör Windows 8.1 med ett anslutet microsoftkonto, kommer det att synkronisera inställningar via ditt Microsoft-konto. Efter uppgraderingen till Windows 10, fortsätter du att synkronisera användarinställningar via Microsoft-konto så länge du använder en domänansluten och Active Directory-domänen kan inte anslutas till Azure AD.

Om den lokala Active Directory-domän ansluter med Azure AD, försöker enheten synkronisera inställningar med hjälp av den anslutna Azure AD-konto. Om Azure AD-administratör inte har aktiverat för Enterprise State Roaming, dina anslutna slutar Azure AD-konto att synkronisera inställningar. Om du är en Windows 10-användare och du loggar in med en Azure AD-identitet, startar du synkronisera windows-inställningar som administratören gör det möjligt för synkronisering av inställningar via Azure AD.

Om du har lagrat personliga data på enheten företagets bör du vara medveten om att Windows-operativsystem och program börjar synkroniseras till Azure AD. Detta har följande konsekvenser:

* Inställningarna för ditt personliga Microsoft-konto kommer avviker förutom de inställningarna för ditt arbete eller skola Azure AD-konton. Detta beror på att Microsoft-konto och Azure AD-inställningar synkroniseras nu använder separata konton.
* Personliga data, till exempel Wi-Fi-lösenord, web-autentiseringsuppgifter och Favoriter i Internet Explorer som redan har synkroniserats via ett anslutna Microsoft-konto kommer att synkroniseras via Azure AD.

## <a name="how-do-microsoft-account-and-azure-ad-enterprise-state-roaming-interoperability-work"></a>Hur kan Microsoft-konto och Azure AD Enterprise tillstånd centrala samverkan arbete?
I November 2015 eller senare versioner av Windows 10 stöds Enterprise State Roaming bara för ett enskilt konto i taget. Om du logga in till Windows med ett arbets eller skolkonto för Azure AD, synkroniseras alla data via Azure AD. Om du loggar in på Windows med hjälp av ett personligt microsoftkonto, synkroniseras alla data via Microsoft-konto. Universal appdata ska flyttas med hjälp av endast det primära inloggning kontot på enheten och den kommer att flyttas endast om appens licens ägs av det primära kontot. Universal appdata för apparna som ägs av sekundära konton kommer inte att synkroniseras.

## <a name="do-settings-sync-for-azure-ad-accounts-from-multiple-tenants"></a>Inställningar synkroniseras för Azure AD-konton från flera klienter?
När flera Azure AD-konton från olika Azure AD-klienter är på samma enhet, måste du uppdatera enhetsregistret att kommunicera med Azure Rights Management-tjänsten för varje Azure AD-klient.  

1. Hitta GUID för varje Azure AD-klient. Öppna Azure portal och välj en Azure AD-klient. GUID för klienten är på egenskapssidan för den valda klientorganisationen (https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties), namngivna **katalog-ID**. 
2. När du har GUID som du behöver lägga till registernyckeln **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\SettingSync\WinMSIPC\<klient-ID GUID >**.
   Från den **klient-ID GUID** nyckeln, skapa ett nytt flersträngsvärde (REG-MULTI-SZ) med namnet **AllowedRMSServerUrls**. Ange licensiering distribution point URL: er i andra Azure-klienter som har åtkomst till enheten för sina data.
3. Du kan hitta licensiering distribution point URL: er genom att köra den **Get-AadrmConfiguration** från AADRM-modulen. Om värdena för den **LicensingIntranetDistributionPointUrl** och **LicensingExtranetDistributionPointUrl** är olika, ange båda värdena. Om värdena är samma, ange värdet bara en gång.

## <a name="what-are-the-roaming-settings-options-for-existing-windows-desktop-applications"></a>Vad är de centrala alternativen för kompatibilitetsinställningarna för befintliga Windows-datorprogram?
Roaming fungerar bara för universella Windows-appar. Det finns två alternativ för att aktivera roaming i ett befintligt stationära Windows-program:

* Den [Desktop brygga](https://aka.ms/desktopbridge) hjälper dig att använda dina befintliga Windows-skrivbordsappar för universella Windows-plattformen. Härifrån kan måste minimala kodändringar utföras för att dra nytta av Azure AD app dataroaming. Desktop bryggan ger dina appar med en app-identitet som behövs för att aktivera AppData roaming för befintliga skrivbordsappar.
* [Användarens upplevelse Virtualization (UE-V)](https://technet.microsoft.com/library/dn458947.aspx) hjälper dig att skapa en mall för anpassade inställningar för befintliga Windows-skrivbordsappar och aktivera växling för Win32-appar. Det här alternativet kräver inte apputvecklaren att ändra koden i appen. UE-V är begränsad till en lokal Active Directory roaming för kunder som har köpt Microsoft Desktop Optimization Pack.

Administratörer kan konfigurera UE-V för att flyttas Windows-skrivbordsapp data genom att ändra roaming för Windows OS-inställningar och data för universell app via [grupprinciper för UE-V](https://technet.microsoft.com/itpro/mdop/uev-v2/configuring-ue-v-2x-with-group-policy-objects-both-uevv2), inklusive:

* Flyttas Grupprincip för Windows-inställningar
* Synkronisera inte Grupprincip för Windows-appar
* Internet Explorer i programavsnittet

I framtiden, kan Microsoft undersöka sätt att göra UE-V som kommer att integreras i Windows och utöka UE-V för att flytta inställningar via Azure AD-molnet.

## <a name="can-i-store-synced-settings-and-data-on-premises"></a>Kan jag lagra synkroniserade inställningar och data lokalt?
Enterprise State Roaming lagrar alla synkroniserade data i Microsoft-molnet. UE-V erbjuder en lokal nätverksväxling lösning.

## <a name="who-owns-the-data-thats-being-roamed"></a>Vem äger de data som är att flyttade?
Företag egna data flyttade via Enterprise State Roaming. Data lagras i ett Azure-datacenter. Alla data krypteras både under överföring och i vila i molnet med Azure Rights Management-tjänsten från Azure Information Protection. Det här är en förbättring jämfört med Microsoft-konto-baserade inställningar sync, vilket endast vissa känsliga data, till exempel autentiseringsuppgifterna krypteras innan de lämnar enheten.

Microsoft strävar efter att skydda kunddata. En enterprise-användarens inställningsdata krypteras automatiskt av Azure Rights Management-tjänsten innan de lämnar en Windows 10-enhet, så att ingen annan användare kan läsa dessa data. Om din organisation har en betald prenumeration för Azure Rights Management-tjänsten, du kan använda andra funktioner för skydd, till exempel spåra och återkalla dokument automatiskt skydda e-postmeddelanden som innehåller känslig information och hantera dina egna nycklar (det ”ta med din egen nyckel ”lösning, kallas även BYOK). Mer information om dessa funktioner och hur den här skyddstjänsten fungerar finns i [vad är Azure Rights Management](/azure/information-protection/what-is-information-protection).

## <a name="can-i-manage-sync-for-a-specific-app-or-setting"></a>Kan jag hantera synkronisering för en viss app eller inställningen?
I Windows 10 finns det ingen inställning för MDM eller en Grupprincip att inaktivera för ett enskilt program. Innehavaradministratörer kan inaktivera synkronisering av appdata för alla appar på en hanterad enhet, men det finns ingen mer detaljerad kontroll på en per app- eller i appen.

## <a name="how-can-i-enable-or-disable-roaming"></a>Hur kan jag aktivera eller inaktivera nätverksväxling?
I den **inställningar** app, gå till **konton** > **synkronisera dina inställningar**. Den här sidan ser du vilket konto som används för att flytta inställningarna och du kan aktivera eller inaktivera enskilda grupper av inställningar för att vara flyttade.

## <a name="what-is-microsofts-recommendation-for-enabling-roaming-in-windows-10"></a>Vad är Microsofts rekommendation för att aktivera nätverksväxling i Windows 10?
Microsoft har ett par olika inställningar nätverksväxling lösningar som är tillgängliga, inklusive centrala användarprofiler, UE-V och Enterprise tillstånd centrala.  Microsoft strävar efter att göra en investering i Enterprise State Roaming i framtida versioner av Windows. Om din organisation inte är redo eller bekväm med att flytta data till molnet kan sedan rekommenderar vi att du använder UE-V som din primära centrala teknik. Om din organisation kräver nätverksväxling stöd för befintliga Windows-skrivbordsprogram, men är otålig och vill flytta till molnet, rekommenderar vi att du använder Enterprise tillstånd centrala och UE-V. Även om UE-V- och Enterprise State Roaming är mycket lik tekniker kan är de inte ömsesidigt uteslutande. De kompletterar varandra för att säkerställa att din organisation tillhandahåller centrala tjänster som användarna behöver.  

När du använder både Enterprise State Roaming och UE-V, gäller följande regler:

* Enterprise State Roaming är primär centrala agenten på enheten. UE-V används för att komplettera ”Win32 klyftan”.
* UE-V för Windows-inställningar och data med moderna UWP-app ska inaktiveras när med gruppen UE-V åtkomstprinciper. Dessa omfattas redan av Enterprise State Roaming.

## <a name="how-does-enterprise-state-roaming-support-virtual-desktop-infrastructure-vdi"></a>Hur Enterprise State Roaming stöder virtuell datorinfrastruktur (VDI)?
Enterprise State Roaming stöds på Windows 10-klient SKU: er, men inte på servern SKU: er. Om en VM-klient finns på en hypervisor-dator och du via en fjärranslutning loggar in på den virtuella datorn, kommer dina data flyttas. Om flera användare delar samma OS och användare logga in på en server för en fullständig Skrivbordsmiljö via en fjärranslutning, kanske roaming inte fungerar. Det senare fallet sessionsbaserad stöds inte officiellt.

## <a name="what-happens-when-my-organization-purchases-a-subscription-that-includes-azure-rights-management-after-using-roaming"></a>Vad händer om min organisation köper en prenumeration som omfattar Azure Rights Management när du använder centrala?
Om din organisation redan använder roaming i Windows 10 med den kostnadsfria prenumerationen för Azure Rights Management-begränsad användning, kan du köpa en [betald prenumeration](https://azure.microsoft.com/pricing/details/information-protection/) som innehåller Azure Rights Management inte kommer att ha skyddstjänsten deras eventuella inverkan på funktionerna i den övergripande funktionen och inga konfigurationsändringar krävs av IT-administratören.

## <a name="known-issues"></a>Kända problem
Se dokumentationen i den [felsökning](enterprise-state-roaming-troubleshooting.md) avsnittet för en lista över kända problem. 

## <a name="next-steps"></a>Nästa steg 

En översikt finns i [företagsroaming översikt](enterprise-state-roaming-overview.md)
