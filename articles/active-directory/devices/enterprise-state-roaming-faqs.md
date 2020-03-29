---
title: Vanliga frågor och svar om företagstillstånd – Azure Active Directory
description: Vanliga frågor och svar om ESR
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ad76835b0c72b691e1ef8810f2c58dedb8f597d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672377"
---
# <a name="settings-and-data-roaming-faq"></a>Vanliga frågor och svar om inställningar och dataväxling

Den här artikeln besvarar några frågor som IT-administratörer kan ha om inställningar och synkronisering av appdata.

## <a name="what-data-roams"></a>Vilka data rör sig?

**Windows-inställningar:** de datorinställningar som är inbyggda i Operativsystemet Windows. Det här är i allmänhet inställningar som anpassar datorn och de innehåller följande breda kategorier:

* *Tema*, som innehåller funktioner som skrivbordstema och inställningar i Aktivitetsfältet.
* *Inställningar för Internet Explorer*, inklusive nyligen öppnade flikar och favoriter.
* *Webbläsarinställningar för Microsoft Edge*, till exempel favoriter och läslista.
* *Lösenord*, inklusive Internet-lösenord, Wi-Fi-profiler och andra.
* *Språkinställningar*, som innehåller inställningar för tangentbordslayouter, systemspråk, datum och tid med mera.
* *Hjälpmedelsfunktioner,* till exempel tema med hög kontrast, Skärmläsaren och Förstoringsglaset.
* *Andra Windows-inställningar*, till exempel musinställningar.

> [!NOTE]
> Den här artikeln gäller den HTML-baserade webbläsaren Microsoft Edge Legacy som lanserades med Windows 10 i juli 2015. Artikeln gäller inte den nya Microsoft Edge Chromium-baserade webbläsaren som släpptes den 15 januari 2020. Mer information om synkroniseringsbeteendet för den nya Microsoft Edge finns i artikeln [Microsoft Edge Sync](/deployedge/microsoft-edge-enterprise-sync).

**Programdata:** Universella Windows-appar kan skriva in inställningsdata till en roamingmapp, och alla data som skrivs till den här mappen synkroniseras automatiskt. Det är upp till den enskilda apputvecklaren att utforma en app för att dra nytta av den här funktionen. Mer information om hur du utvecklar en Universell Windows-app som använder roaming finns i [api:et för lagring av appdata](https://msdn.microsoft.com/library/windows/apps/mt299098.aspx) och [utvecklarbloggen för Windows 8-appdata.](https://blogs.msdn.com/b/windowsappdev/archive/2012/07/17/roaming-your-app-data.aspx)

## <a name="what-account-is-used-for-settings-sync"></a>Vilket konto används för synkronisering av inställningar?

I Windows 8.1 synkroniseras alltid microsoft-konton för konsumenter. Företagsanvändare hade möjlighet att ansluta ett Microsoft-konto till sitt Active Directory-domänkonto för att få åtkomst till synkronisering av inställningar. I Windows 10 ersätts den här anslutna Microsoft-kontofunktionen med ett primärt/sekundärt kontoramverk.

Det primära kontot definieras som det konto som används för att logga in i Windows. Detta kan vara ett Microsoft-konto, ett Azure Active Directory-konto (Azure AD), ett lokalt Active Directory-konto eller ett lokalt konto. Förutom det primära kontot kan Windows 10-användare lägga till ett eller flera sekundära molnkonton på sin enhet. Ett sekundärt konto är i allmänhet ett Microsoft-konto, ett Azure AD-konto eller något annat konto som Gmail eller Facebook. Dessa sekundära konton ger åtkomst till ytterligare tjänster som enkel inloggning och Windows Store, men de kan inte aktivera synkronisering av inställningar.

I Windows 10 kan endast det primära kontot för enheten användas för synkronisering av inställningar (se [Hur uppgraderar jag från Microsoft-kontoinställningar synkroniseras i Windows 8 till Azure AD-inställningar synkroniseras i Windows 10?](enterprise-state-roaming-faqs.md#how-do-i-upgrade-from-microsoft-account-settings-sync-in-windows-8-to-azure-ad-settings-sync-in-windows-10)).

Data blandas aldrig mellan de olika användarkontona på enheten. Det finns två regler för synkronisering av inställningar:

* Windows-inställningarna kommer alltid att gå med det primära kontot.
* Appdata taggas med det konto som används för att hämta appen. Endast appar som taggats med det primära kontot synkroniseras. Taggning av appägarskap avgörs när en app läses in sida via Windows Store eller MDM (Mobile Device Management).

Om en apps ägare inte kan identifieras flyttas den med det primära kontot. Om en enhet uppgraderas från Windows 8 eller Windows 8.1 till Windows 10 kommer alla appar att taggas som de har förvärvats av Microsoft-kontot. Detta beror på att de flesta användare skaffar appar via Windows Store och det inte fanns något Windows Store-stöd för Azure AD-konton före Windows 10. Om en app installeras via en offlinelicens kommer appen att taggas med det primära kontot på enheten.

> [!NOTE]
> Windows 10-enheter som ägs av företag och är anslutna till Azure AD kan inte längre ansluta sina Microsoft-konton till ett domänkonto. Möjligheten att ansluta ett Microsoft-konto till ett domänkonto och få alla användarens datasynkronisering till Microsoft-kontot (det vill än Microsoft-konto roaming via det anslutna Microsoft-kontot och Active Directory-funktionen) tas bort från Windows 10 enheter som är anslutna till en ansluten Active Directory- eller Azure AD-miljö.

## <a name="how-do-i-upgrade-from-microsoft-account-settings-sync-in-windows-8-to-azure-ad-settings-sync-in-windows-10"></a>Hur uppgraderar jag från Microsoft-kontoinställningar synkroniseras i Windows 8 till Azure AD-inställningar synkroniseras i Windows 10?

Om du är ansluten till Active Directory-domänen som kör Windows 8.1 med ett anslutet Microsoft-konto synkroniserar du inställningarna via ditt Microsoft-konto. När du har uppgraderat till Windows 10 fortsätter du att synkronisera användarinställningar via Microsoft-konto så länge du är domänansluten användare och Active Directory-domänen inte ansluter till Azure AD.

Om den lokala Active Directory-domänen ansluter till Azure AD försöker enheten synkronisera inställningarna med det anslutna Azure AD-kontot. Om Azure AD-administratören inte aktiverar Roaming i företagstillstånd stoppas synkroniseringsinställningarna för ditt anslutna Azure AD-konto. Om du är en Windows 10-användare och loggar in med en Azure AD-identitet börjar du synkronisera Windows-inställningar så snart administratören aktiverar synkronisering av inställningar via Azure AD.

Om du har lagrat några personuppgifter på företagets enhet bör du vara medveten om att Windows OS och programdata börjar synkroniseras med Azure AD. Detta har följande konsekvenser:

* Dina personliga Microsoft-kontoinställningar kommer att glida bort från inställningarna på ditt arbete eller din skola Azure AD-konton. Detta beror på att Synkroniseringen av Microsoft-kontot och Azure AD-inställningarna nu använder separata konton.
* Personliga data som Wi-Fi-lösenord, webbautentiseringsuppgifter och Favoriter i Internet Explorer som tidigare synkroniserats via ett anslutet Microsoft-konto synkroniseras via Azure AD.

## <a name="how-do-microsoft-account-and-azure-ad-enterprise-state-roaming-interoperability-work"></a>Hur fungerar Microsoft-konto och Azure AD Enterprise State Roaming-interoperabilitet?

I november 2015 eller senare versioner av Windows 10 stöds Enterprise State Roaming endast för ett enda konto åt gången. Om du loggar in i Windows med hjälp av ett Azure AD-konto för arbete eller skola synkroniseras alla data via Azure AD. Om du loggar in i Windows med ett personligt Microsoft-konto synkroniseras alla data via Microsoft-kontot. Universella appdata kommer att flytta med endast det primära inloggningskontot på enheten, och det kommer att röra sig bara om appens licens ägs av det primära kontot. Universella appdata för de appar som ägs av sekundära konton synkroniseras inte.

## <a name="do-settings-sync-for-azure-ad-accounts-from-multiple-tenants"></a>Synkroniserar inställningarna för Azure AD-konton från flera klienter?

När flera Azure AD-konton från olika Azure AD-klienter finns på samma enhet måste du uppdatera enhetens register för att kommunicera med Azure Rights Management-tjänsten för varje Azure AD-klientorganisation.  

1. Hitta GUID för varje Azure AD-klientorganisation. Öppna Azure-portalen och välj en Azure AD-klientorganisation. GUID för klienten finns på sidan Egenskaperhttps://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties)för den valda klienten ( , märkt **katalog-ID**. 
2. När du har GUID måste du lägga till registernyckeln **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\SettingSync\WinMSIPC-klient-ID\<GUID>**.
   Skapa ett nytt multisträngvärde (REG-MULTI-SZ) med namnet **AllowedRMSServerUrls**från **klient-ID-GUID.From the tenant ID GUID** key, create a new Multi-String value (REG-MULTI-SZ) named AllowedRMSServerUrls . För dess data anger du url:er för licensieringsdistributionsplats för andra Azure-klienter som enheten har åtkomst till.
3. Du hittar url:erna för licensieringsdistributionsplatser genom att köra cmdleten **Get-AadrmConfiguration** från AADRM-modulen. Om värdena för **LicensingIntranetDistributionPointUrl** och **LicensingExtranetDistributionPointUrl** är olika anger du båda värdena. Om värdena är desamma anger du värdet bara en gång.

## <a name="what-are-the-roaming-settings-options-for-existing-windows-desktop-applications"></a>Vilka är alternativen för centrala inställningar för befintliga Windows-skrivbordsprogram?

Roaming fungerar bara för Universella Windows-appar. Det finns två alternativ för att aktivera roaming i ett befintligt Windows-skrivbordsprogram:

* [Skrivbordsbrygga](https://aka.ms/desktopbridge) hjälper dig att ta med dina befintliga Windows-skrivbordsappar till den universella Windows-plattformen. Härifrån krävs minimala kodändringar för att dra nytta av Azure AD-appdataroaming. Skrivbordsbrygga förser dina appar med en appidentitet, som behövs för att aktivera appdataroaming för befintliga skrivbordsappar.
* [Virtualisering av användarupplevelse (UE-V)](https://technet.microsoft.com/library/dn458947.aspx) hjälper dig att skapa en anpassad inställningsmall för befintliga Windows-skrivbordsappar och aktivera roaming för Win32-appar. Det här alternativet kräver inte att apputvecklaren ändrar koden för appen. UE-V är begränsat till lokal Active Directory-roaming för kunder som har köpt Microsoft Desktop Optimization Pack.

Administratörer kan konfigurera UE-V för att flytta Windows-skrivbordsappdata genom att ändra roaming av Windows OS-inställningar och universella appdata via [UE-V-gruppprinciper,](https://technet.microsoft.com/itpro/mdop/uev-v2/configuring-ue-v-2x-with-group-policy-objects-both-uevv2)inklusive:

* Grupprincip för Roam Windows-inställningar
* Synkronisera inte windows apps-grupprincipen
* Internet Explorer roaming i programavsnittet

I framtiden kan Microsoft undersöka olika sätt att göra UE-V djupt integrerat i Windows och utöka UE-V för att flytta inställningar via Azure AD-molnet.

## <a name="can-i-store-synced-settings-and-data-on-premises"></a>Kan jag lagra synkroniserade inställningar och data lokalt?

Företagstillståndsroaming lagrar alla synkroniserade data i Microsoft-molnet. UE-V erbjuder en lokal roaminglösning.

## <a name="who-owns-the-data-thats-being-roamed"></a>Vem äger data som strövar omkring?

Företagen äger de uppgifter som förs via Företagsstat Roaming. Data lagras i ett Azure-datacenter. Alla användardata krypteras både under överföring och i vila i molnet med hjälp av Azure Rights Management-tjänsten från Azure Information Protection. Detta är en förbättring jämfört med Microsofts kontobaserade inställningar synkronisering, som krypterar endast vissa känsliga data såsom användaruppgifter innan den lämnar enheten.

Microsoft strävar efter att skydda kunddata. En företagsanvändares inställningsdata krypteras automatiskt av Azure Rights Management-tjänsten innan den lämnar en Windows 10-enhet, så att ingen annan användare kan läsa dessa data. Om din organisation har en betald prenumeration för Azure Rights Management-tjänsten kan du använda andra skyddsfunktioner, till exempel spåra och återkalla dokument, automatiskt skydda e-postmeddelanden som innehåller känslig information och hantera dina egna nycklar ("ta med din egen nyckel" lösning, även känd som BYOK). Mer information om dessa funktioner och hur den här skyddstjänsten fungerar finns i [Vad är Azure Rights Management](/azure/information-protection/what-is-information-protection).

## <a name="can-i-manage-sync-for-a-specific-app-or-setting"></a>Kan jag hantera synkronisering för en viss app eller inställning?

I Windows 10 finns det ingen MDM- eller grupprincipinställning för att inaktivera roaming för ett enskilt program. Klientadministratörer kan inaktivera appdatasynkronisering för alla appar på en hanterad enhet, men det finns ingen bättre kontroll på en nivå per app eller inom appen.

## <a name="how-can-i-enable-or-disable-roaming"></a>Hur aktiverar eller inaktiverar jag roaming?

Gå till **Konton** > **synkronisera inställningarna**i appen **Inställningar** . Från den här sidan kan du se vilket konto som används för att flytta inställningar, och du kan aktivera eller inaktivera enskilda grupper av inställningar som ska flyttas.

## <a name="what-is-microsofts-recommendation-for-enabling-roaming-in-windows-10"></a>Vad är Microsofts rekommendation för att aktivera roaming i Windows 10?

Microsoft har några olika centrala inställningar tillgängliga, inklusive centrala användarprofiler, UE-V och Företagsstat Roaming.  Microsoft har åtagit sig att göra en investering i Företagsstat Roaming i framtida versioner av Windows. Om din organisation inte är redo eller bekväm med att flytta data till molnet rekommenderar vi att du använder UE-V som din primära roamingteknik. Om din organisation kräver roamingstöd för befintliga Windows-skrivbordsprogram men är angelägen om att flytta till molnet rekommenderar vi att du använder både Roaming i Företagsstat och UE-V. Även om UE-V och Företagsstat Roaming är mycket liknande teknik, utesluter de inte varandra. De kompletterar varandra för att se till att din organisation tillhandahåller de roamingtjänster som användarna behöver.  

När du använder både Roaming i företagsstater och UE-V gäller följande regler:

* Företagsstat Roaming är den primära roamingagenten på enheten. UE-V används för att komplettera "Win32 gapet."
* UE-V-roaming för Windows-inställningar och moderna UWP-appdata bör inaktiveras när UE-V-gruppprinciper används. Dessa omfattas redan av Företagsstat Roaming.

## <a name="how-does-enterprise-state-roaming-support-virtual-desktop-infrastructure-vdi"></a>Hur stöder Företagsstatsroaming virtuell skrivbordsinfrastruktur (VDI)?

Roaming för företagtillstånd stöds på Windows 10-klientSKU:er, men inte på serverSKU:er. Om en klient-VM finns på en hypervisor-dator och du fjärrintecknar på den virtuella datorn, kommer dina data att flytta. Om flera användare delar samma operativsystem och användare fjärrinnjuts på en server för en fullständig skrivbordsupplevelse kanske roaming inte fungerar. Det senare sessionsbaserade scenariot stöds inte officiellt.

## <a name="what-happens-when-my-organization-purchases-a-subscription-that-includes-azure-rights-management-after-using-roaming"></a>Vad händer när min organisation köper en prenumeration som innehåller Azure Rights Management efter att ha använt roaming?

Om din organisation redan använder roaming i Windows 10 med azure rights management-prenumerationen med begränsad användning, kommer det inte att påverka funktionen för att köpa en [betald prenumeration](https://azure.microsoft.com/pricing/details/information-protection/) som innehåller Azure Rights Management-skyddstjänsten och inga konfigurationsändringar krävs av IT-administratören.

## <a name="known-issues"></a>Kända problem

Mer om hur [troubleshooting](enterprise-state-roaming-troubleshooting.md) du gör finns i dokumentationen i felsökningsavsnittet. 

## <a name="next-steps"></a>Nästa steg 

En översikt finns i [roaming översikt över företagstillstånd](enterprise-state-roaming-overview.md)
