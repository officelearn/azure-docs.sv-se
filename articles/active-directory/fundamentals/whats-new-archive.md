---
title: Arkiv för Nyheter i Azure Active Directory? | Microsoft Docs
description: De nya versionsanteckningarna i avsnittet Översikt i den här innehållsuppsättningen innehåller 6 månaders aktivitet. Efter 6 månader tas objekten bort från huvudartikeln och läggs in i den här arkivartikeln.
services: active-directory
author: msmimart
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mimart
ms.reviewer: dhanyahk
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 15f5563c11e6abc5452ace01822e5559d04808df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369649"
---
# <a name="archive-for-whats-new-in-azure-active-directory"></a>Arkiv för Nyheter i Azure Active Directory?

Den primära artikeln [Nyheter i Azure Active Directory?](whats-new.md)

Vad är nytt i Azure Active Directory? viktig information innehåller information om:

- De senaste versionerna
- Kända problem
- Felkorrigeringar
- Föråldrade funktioner
- Planer för förändringar

---

## <a name="september-2019"></a>September 2019

### <a name="plan-for-change-deprecation-of-the-power-bi-content-packs"></a>Planera för ändring: Utfasning av Power BI-innehållspaket

**Typ:** Planera för förändring  
**Servicekategori:** Rapportering  
**Produktkapacitet:** Övervakning & rapportering

Från och med den 1 oktober 2019 börjar Power BI att föråldrat alla innehållspaket, inklusive Azure AD Power BI-innehållspaketet. Som ett alternativ till det här innehållspaketet kan du använda Azure AD-arbetsböcker för att få insikter om dina Azure AD-relaterade tjänster. Ytterligare arbetsböcker kommer, inklusive arbetsböcker om principer för villkorlig åtkomst i endast rapportläge, appmedgivande-baserade insikter med mera.

Mer information om arbetsböckerna finns i [Så här använder du Azure Monitor-arbetsböcker för Azure Active Directory-rapporter](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks). Mer information om utfasningen av innehållspaketen finns i blogginlägget om allmänt tillgänglighet för meddelande om [Power BI-mallappar.](https://powerbi.microsoft.com/blog/announcing-power-bi-template-apps-general-availability/)

---

### <a name="my-profile-is-renaming-and-integrating-with-the-microsoft-office-account-page"></a>Min profil byter namn på och integreras med microsoft Office-kontosidan

**Typ:** Planera för förändring  
**Servicekategori:** Min profil/konto  
**Produktkapacitet:** Samarbete

Från och med oktober blir min profil-upplevelsen Mitt konto. Som en del av den ändringen, överallt som för närvarande säger, kommer **Min profil** att **ändras**till Mitt konto . Utöver namnändringen och vissa designförbättringar kommer den uppdaterade upplevelsen att erbjuda ytterligare integrering med Microsoft Office-kontosidan. Du kan komma åt Office-installationer och prenumerationer från sidan **Översiktskonto,** tillsammans med Office-relaterade kontaktinställningar från sidan **Sekretess.**

Mer information om upplevelsen Av Min profil (förhandsgranskning) finns i [Översikt över Portalen Min profil (förhandsversion).](https://docs.microsoft.com/azure/active-directory/user-help/myprofile-portal-overview)

---

### <a name="bulk-manage-groups-and-members-using-csv-files-in-the-azure-ad-portal-public-preview"></a>Masshantera grupper och medlemmar med CSV-filer i Azure AD-portalen (Offentlig förhandsversion)

**Typ:** Ny funktion  
**Servicekategori:** Gruppledning  
**Produktkapacitet:** Samarbete

Vi är glada att kunna meddela offentlig förhandsversion av massgrupphanteringsupplevelserna i Azure AD-portalen. Du kan nu använda en CSV-fil och Azure AD-portalen för att hantera grupper och medlemslistor, inklusive:

- Lägga till eller ta bort medlemmar från en grupp.

- Hämtar listan över grupper från katalogen.

- Hämta listan över gruppmedlemmar för en viss grupp.

Mer information finns i [Listan Masstillägg för medlemmar,](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-import-members) [Massborttagning av medlemmar,](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-remove-members) [listan Över masshämtningsmedlemmar](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download-members)och [gruppgruppslistan För masshämtning](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download).

---

### <a name="dynamic-consent-is-now-supported-through-a-new-admin-consent-endpoint"></a>Dynamiskt samtycke stöds nu via en ny slutpunkt för administratörsgodkännande

**Typ:** Ny funktion  
**Servicekategori:** Autentiseringar (inloggningar)  
**Produktkapacitet:** Användarautentisering

Vi har skapat en ny slutpunkt för administratörsmedgivande för att stödja dynamiskt samtycke, vilket är användbart för appar som vill använda modellen för dynamiskt medgivande på Microsoft Identity-plattformen.

Mer information om hur du använder den nya slutpunkten finns i [Använda slutpunkten för administratörsmedgivande](https://docs.microsoft.com/azure/active-directory/develop/v2-admin-consent).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2019"></a>Nya federerade appar tillgängliga i Azure AD App-galleriet – september 2019

**Typ:** Ny funktion  
**Servicekategori:** Företagsappar  
**Produktkapacitet:** Integration från tredje part

I september 2019 har vi lagt till dessa 29 nya appar med federationsstöd i appgalleriet:

[ScheduleLook](https://schedulelook.bbsonlineservices.net/), [MS Azure SSO Access för&trade; Ethidex Compliance Office - Enkel inloggning,](https://docs.microsoft.com/azure/active-directory/saas-apps/ms-azure-sso-access-for-ethidex-compliance-office-tutorial) [iServer Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/iserver-portal-tutorial), [SKYSITE](https://docs.microsoft.com/azure/active-directory/saas-apps/skysite-tutorial), [Concur Resor och utgifter](https://docs.microsoft.com/azure/active-directory/saas-apps/concur-travel-and-expense-tutorial), [WorkBoard](https://docs.microsoft.com/azure/active-directory/saas-apps/workboard-tutorial) `https://apps.yeeflow.com/`, , [ARC-anläggningar](https://docs.microsoft.com/azure/active-directory/saas-apps/arc-facilities-tutorial), [Luware Stratus Team](https://stratus.emea.luware.cloud/login), Breda [idéer](https://wideideas.online/wideideas/), [Prisma Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/prisma-cloud-tutorial), [JDLT Client Hub](https://clients.jdlt.co.uk/login), [RENRAKU](https://docs.microsoft.com/azure/active-directory/saas-apps/renraku-tutorial), [SealPath Secure Browser](https://protection.sealpath.com/SealPathInterceptorWopiSaas/Open/InstallSealPathEditorOneDrive), [Prisma Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/prisma-cloud-tutorial), `https://app.penneo.com/`, `https://app.testhtm.com/settings/email-integration`, [Cintoo Cloud](https://aec.cintoo.com/login), [ Whitesource](https://docs.microsoft.com/azure/active-directory/saas-apps/whitesource-tutorial), [Hosted Heritage Online SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/hosted-heritage-online-sso-tutorial), [IDC](https://docs.microsoft.com/azure/active-directory/saas-apps/idc-tutorial), [CakeHR](https://docs.microsoft.com/azure/active-directory/saas-apps/cakehr-tutorial), [BIS](https://docs.microsoft.com/azure/active-directory/saas-apps/bis-tutorial), [Coo Kai Team Build](https://ms-contacts.coo-kai.jp/), [Sonarqube](https://docs.microsoft.com/azure/active-directory/saas-apps/sonarqube-tutorial), [Adobe Identity Management](https://docs.microsoft.com/azure/active-directory/saas-apps/adobe-identity-management-tutorial), Discovery Fördelar [SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/discovery-benefits-sso-tutorial), [Amelio](https://app.amelio.co/),`https://itask.yipinapp.com/`

Mer information om apparna finns i [SaaS-programintegrering med Azure Active Directory](https://aka.ms/appstutorial). Mer information om hur du listar ditt program i Azure AD-appgalleriet finns [i Lista ditt program i Azure Active Directory-programgalleriet](https://aka.ms/azureadapprequest).

---

### <a name="new-azure-ad-global-reader-role"></a>Ny global azure AD-läsareroll

**Typ:** Ny funktion  
**Servicekategori:** Rbac  
**Produktkapacitet:** Åtkomstkontroll

Från och med den 24 september 2019 kommer vi att börja lansera en ny Azure Active Directory -roll (AD) som heter Global Reader. Den här lanseringen börjar med produktion och globala molnkunder (GCC), och avslutas över hela världen i oktober.

Rollen Global Reader är den skrivskyddade motsvarigheten till global administratör. Användare i den här rollen kan läsa inställningar och administrativ information för Microsoft 365-tjänster, men kan inte vidta hanteringsåtgärder. Vi har skapat rollen Global Reader för att minska antalet globala administratörer i din organisation. Eftersom globala administratörskonton är kraftfulla och sårbara för angrepp rekommenderar vi att du har färre än fem globala administratörer. Vi rekommenderar att du använder rollen Global Reader för planering, granskningar eller utredningar. Vi rekommenderar också att du använder rollen Global Reader i kombination med andra begränsade administratörsroller, till exempel Exchange Administrator, för att få jobbet gjort utan att den globala administratörsrollen krävs.

Rollen Global Reader fungerar med det nya administrationscentret för Microsoft 365, Administrationscenter för Exchange, Administrationscenter för team, Administrationscenter för team, Säkerhetscenter, Compliance Center, Azure AD Admin Center och Administrationsadministration för enhet.

>[!NOTE]
> I början av den offentliga förhandsversionen fungerar inte rollen Global Reader med: SharePoint, Hantering av privilegierad åtkomst, Customer Lockbox, känslighetsetiketter, Teams Lifecycle, Teams Reporting & Call Analytics, Teams IP Phone Device Management och Teams App Catalog. 

Mer information finns [i Administratörsrollbehörigheter i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).

---

### <a name="access-an-on-premises-report-server-from-your-power-bi-mobile-app-using-azure-active-directory-application-proxy"></a>Få tillgång till en lokal rapportserver från Power BI Mobile-appen med Azure Active Directory Application Proxy

**Typ:** Ny funktion  
**Servicekategori:** App Proxy  
**Produktkapacitet:** Åtkomstkontroll

Med den nya integreringen mellan Power BI-mobilappen och Azure AD Application Proxy kan du logga in på ett säkert sätt i Power BI-mobilappen och visa alla rapporter från organisationen som finns på den lokala Power BI Report Server.

Information om Power BI Mobile-appen, inklusive var appen ska hämtas, finns på [Power BI-webbplatsen](https://powerbi.microsoft.com/mobile/). Mer information om hur du konfigurerar Power BI-mobilappen med Azure AD Application Proxy finns i [Aktivera fjärråtkomst till Power BI Mobile med Azure AD Application Proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-integrate-with-power-bi).

---

### <a name="new-version-of-the-azureadpreview-powershell-module-is-available"></a>Ny version av AzureADPreview PowerShell-modulen är tillgänglig

**Typ:** Ändrad funktion  
**Servicekategori:** Andra  
**Produktkapacitet:** Katalog

Nya cmdlets har lagts till i AzureADPreview-modulen för att definiera och tilldela anpassade roller i Azure AD, inklusive:

- `Add-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Get-AzureADMSFeatureRolloutPolicy`
- `New-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Set-AzureADMSFeatureRolloutPolicy`

---

### <a name="new-version-of-azure-ad-connect"></a>Ny version av Azure AD Connect

**Typ:** Ändrad funktion  
**Servicekategori:** Andra  
**Produktkapacitet:** Katalog

Vi har släppt en uppdaterad version av Azure AD Connect för kunder med automatisk uppgradering. Den här nya versionen innehåller flera nya funktioner, förbättringar och buggfixar. Mer information om den här nya versionen finns i [Azure AD Connect: Versionsversionshistorik](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-version-history#14250).

---

### <a name="azure-multi-factor-authentication-mfa-server-version-802-is-now-available"></a>Azure Multi-Factor Authentication (MFA) Server, version 8.0.2 är nu tillgänglig

**Typ:** Fast  
**Servicekategori:** Mfa  
**Produktkapacitet:** Skydd & identitetssäkerhet

Om du är en befintlig kund som aktiverade MFA Server före den 1 juli 2019 kan du nu hämta den senaste versionen av MFA Server (version 8.0.2). I denna nya version, vi:

- Åtgärdade ett problem så när Azure AD-synkroniseringen ändrar en användare från Inaktiverad till Aktiverad skickas ett e-postmeddelande till användaren.

- Åtgärdade ett problem så att kunderna kunde uppgradera, samtidigt som de fortsatte att använda taggarfunktionen.

- Lade till landskoden kosovo (+383).

- Lade till granskningsloggning vid en gång i MultiFactorAuthSvc.log.

- Förbättrad prestanda för webbtjänsten SDK.

- Fixade andra mindre buggar.

Från och med den 1 juli 2019 slutade Microsoft att erbjuda MFA Server för nya distributioner. Nya kunder som kräver multifaktorautentisering bör använda molnbaserad Azure Multi-Factor-autentisering. Mer information finns i [Planera en molnbaserad Azure Multi-Factor Authentication-distribution](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted).

---

## <a name="august-2019"></a>Augusti 2019

### <a name="enhanced-search-filtering-and-sorting-for-groups-is-available-in-the-azure-ad-portal-public-preview"></a>Förbättrad sökning, filtrering och sortering av grupper är tillgänglig i Azure AD-portalen (Offentlig förhandsversion)

**Typ:** Ny funktion  
**Servicekategori:** Gruppledning  
**Produktkapacitet:** Samarbete

Vi är glada att kunna meddela offentlig förhandsversion av de förbättrade grupprelaterade upplevelserna i Azure AD-portalen. De här förbättringarna hjälper dig att hantera grupper och medlemslistor bättre genom att tillhandahålla:

- Avancerade sökfunktioner, till exempel delsträngssökning i grupplistor.
- Avancerade filtrerings- och sorteringsalternativ på medlems- och ägarlistor.
- Nya sökfunktioner för medlems- och ägarlistor.
- Mer exakta gruppantal för stora grupper.

Mer information finns [i Hantera grupper i Azure-portalen](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-members-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context).

---

### <a name="new-custom-roles-are-available-for-app-registration-management-public-preview"></a>Nya anpassade roller är tillgängliga för hantering av appregistrering (Offentlig förhandsversion)

**Typ:** Ny funktion  
**Servicekategori:** Rbac  
**Produktkapacitet:** Åtkomstkontroll

Anpassade roller (tillgängliga med en Azure AD P1- eller P2-prenumeration) kan nu hjälpa dig med detaljerad åtkomst genom att låta dig skapa rolldefinitioner med specifika behörigheter och sedan tilldela dessa roller till specifika resurser. För närvarande skapar du anpassade roller genom att använda behörigheter för att hantera appregistreringar och sedan tilldela rollen till en viss app. Mer information om anpassade roller finns [i Anpassade administratörsroller i Azure Active Directory (förhandsversion)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-custom-overview).

Om du behöver ytterligare behörigheter eller resurser som stöds, som du för närvarande inte ser, kan du skicka feedback till vår [Azure-feedbackwebbplats](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032) och vi lägger till din begäran i vår uppdateringsöversikt.

---

### <a name="new-provisioning-logs-can-help-you-monitor-and-troubleshoot-your-app-provisioning-deployment-public-preview"></a>Nya etableringsloggar kan hjälpa dig att övervaka och felsöka distributionen av appetablering (offentlig förhandsversion)

**Typ:** Ny funktion  
**Servicekategori:** Etablering av appar  
**Produktkapacitet:** Hantering av identitetslivscykeln

Nya etableringsloggar finns tillgängliga för att hjälpa dig att övervaka och felsöka distributionen för användare och gruppetablering. Dessa nya loggfiler innehåller information om:

- Vilka grupper har skapats i [ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-provisioning-tutorial)
- Vilka roller importerades från [Amazon Web Services (AWS)](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial#configure-and-test-azure-ad-single-sign-on-for-amazon-web-services-aws)
- Vilka anställda importerades inte från [Workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial)

Mer information finns [i Etablering av rapporter i Azure Active Directory-portalen (förhandsversion)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs).

---

### <a name="new-security-reports-for-all-azure-ad-administrators-general-availability"></a>Nya säkerhetsrapporter för alla Azure AD-administratörer (allmän tillgänglighet)

**Typ:** Ny funktion  
**Servicekategori:** Identitetsskydd  
**Produktkapacitet:** Skydd & identitetssäkerhet

Som standard kan alla Azure AD-administratörer snart komma åt moderna säkerhetsrapporter i Azure AD. Fram till slutet av september kommer du att kunna använda banderollen högst upp i de moderna säkerhetsrapporterna för att återgå till de gamla rapporterna.

De moderna säkerhetsrapporterna ger ytterligare funktioner från de äldre versionerna, inklusive:

- Avancerad filtrering och sortering
- Massåtgärder, till exempel att avvisa användarrisk
- Bekräftelse av komprometterade eller säkra enheter
- Risktillstånd, som täcker: I riskzonen, avvisas, åtgärdas och bekräftas äventyras
- Nya riskrelaterade identifieringar (tillgängliga för Azure AD Premium-prenumeranter)

Mer information finns i [Riskfyllda användare,](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users) [Riskfyllda inloggningar](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins)och [Riskidentifieringar](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risk-detections).

---

### <a name="user-assigned-managed-identity-is-available-for-virtual-machines-and-virtual-machine-scale-sets-general-availability"></a>Användartilldelade hanterade identitet är tillgänglig för virtuella datorer och skalningsuppsättningar för virtuella datorer (allmän tillgänglighet)

**Typ:** Ny funktion  
**Servicekategori:** Hanterade identiteter för Azure-resurser  
**Produktkapacitet:** Utvecklarupplevelse

Användartilldelade hanterade identiteter är nu allmänt tillgängliga för virtuella datorer och skaluppsättningar för virtuella datorer och virtuella datorer. Som en del av detta kan Azure skapa en identitet i Azure AD-klienten som är betrodd av den prenumeration som används och kan tilldelas en eller flera Azure-tjänstinstanser. Mer information om användartilldelade hanterade identiteter finns i [Vad är hanterade identiteter för Azure-resurser?](https://aka.ms/azuremanagedidentity).

---

### <a name="users-can-reset-their-passwords-using-a-mobile-app-or-hardware-token-general-availability"></a>Användare kan återställa sina lösenord med hjälp av en mobilapp eller maskinvarutoken (allmän tillgänglighet)

**Typ:** Ändrad funktion  
**Servicekategori:** Återställning av lösenord för självbetjäning  
**Produktkapacitet:** Användarautentisering

Användare som har registrerat en mobilapp hos din organisation kan nu återställa sitt eget lösenord genom att godkänna ett meddelande från Microsoft Authenticator-appen eller genom att ange en kod från sin mobilapp eller maskinvarutoken.

Mer information finns i [Så här fungerar det: Azure AD-självbetjäningslösenordsåterställning](https://aka.ms/authappsspr). Mer information om användarupplevelsen finns i [Återställ din egen översikt över arbets- eller skollösenord](https://docs.microsoft.com/azure/active-directory/user-help/user-help-password-reset-overview).

---

### <a name="adalnet-ignores-the-msalnet-shared-cache-for-on-behalf-of-scenarios"></a>ADAL.NET ignorerar den MSAL.NET delade cacheminnet för scenarier för flera av scenarierna för flera fall

**Typ:** Fast  
**Servicekategori:** Autentiseringar (inloggningar)  
**Produktkapacitet:** Användarautentisering

Från och med Azure AD-autentiseringsbibliotek (ADAL.NET) version 5.0.0-förhandsversion måste apputvecklare [serialisera en cache per konto för webbappar och webb-API:er](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization#custom-token-cache-serialization-in-web-applications--web-api). Annars kan vissa scenarier som använder [flödet för den för den skull](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-api-call-api-app-configuration#on-behalf-of-flow), tillsammans med vissa specifika användningsfall av `UserAssertion`, resultera i en behörighetshöjning. För att undvika det här säkerhetsproblemet ignorerar ADAL.NET nu Microsofts autentiseringsbibliotek för dotnet (MSAL.NET) delad cache för scenarier för flera scenarier.

Mer information om det här problemet finns i [Azure Active Directory Authentication Library Elevation of Privilege Vulnerability](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1258).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2019"></a>Nya federerade appar tillgängliga i Azure AD App-galleriet – augusti 2019

**Typ:** Ny funktion  
**Servicekategori:** Företagsappar  
**Produktkapacitet:** Integration från tredje part

I augusti 2019 har vi lagt till dessa 26 nya appar med federationsstöd i appgalleriet:

[Civic Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/civic-platform-tutorial), [Amazon Business](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-business-tutorial), [ProNovos Ops Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-ops-manager-tutorial), [Cognidox](https://docs.microsoft.com/azure/active-directory/saas-apps/cognidox-tutorial), [Viareport's Inativ Portal (Europa)](https://docs.microsoft.com/azure/active-directory/saas-apps/viareports-inativ-portal-europe-tutorial), [Azure Databricks](https://azure.microsoft.com/services/databricks), [Robin](https://docs.microsoft.com/azure/active-directory/saas-apps/robin-tutorial), [Academy Närvaro](https://docs.microsoft.com/azure/active-directory/saas-apps/academy-attendance-tutorial), [Prioritet Matrix](https://sync.appfluence.com/pmwebng/), [Cousto MySpace](https://cousto.platformers.be/account/login), [Uploadcare](https://uploadcare.com/accounts/signup/), [Carbonite Endpoint Backup](https://docs.microsoft.com/azure/active-directory/saas-apps/carbonite-endpoint-backup-tutorial), [CPQSync av Cincom](https://docs.microsoft.com/azure/active-directory/saas-apps/cpqsync-by-cincom-tutorial), [Chargebee](https://docs.microsoft.com/azure/active-directory/saas-apps/chargebee-tutorial), [&trade; deliver.media Portal](https://portal.deliver.media), Frontline [Education](https://docs.microsoft.com/azure/active-directory/saas-apps/frontline-education-tutorial), [F5](https://www.f5.com/products/security/access-policy-manager), [ stashcat AD ansluta](https://www.stashcat.com), [Blink](https://docs.microsoft.com/azure/active-directory/saas-apps/blink-tutorial), [Vocoli](https://docs.microsoft.com/azure/active-directory/saas-apps/vocoli-tutorial), [ProNovos Analytics](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-analytics-tutorial), [Sigstr](https://docs.microsoft.com/azure/active-directory/saas-apps/sigstr-tutorial), [Darwinbox](https://docs.microsoft.com/azure/active-directory/saas-apps/darwinbox-tutorial), [Klocka efter färger,](https://docs.microsoft.com/azure/active-directory/saas-apps/watch-by-colors-tutorial) [Sele](https://docs.microsoft.com/azure/active-directory/saas-apps/harness-tutorial), [EAB Navigera Strategisk Vård](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-strategic-care-tutorial)

Mer information om apparna finns i [SaaS-programintegrering med Azure Active Directory](https://aka.ms/appstutorial). Mer information om hur du listar ditt program i Azure AD-appgalleriet finns [i Lista ditt program i Azure Active Directory-programgalleriet](https://aka.ms/azureadapprequest).

---

### <a name="new-versions-of-the-azuread-powershell-and-azureadpreview-powershell-modules-are-available"></a>Nya versioner av AzureAD PowerShell- och AzureADPreview PowerShell-modulerna är tillgängliga

**Typ:** Ändrad funktion  
**Servicekategori:** Andra  
**Produktkapacitet:** Katalog

Nya uppdateringar av AzureAD- och AzureAD Preview PowerShell-modulerna är tillgängliga:

- En `-Filter` ny parameter har `Get-AzureADDirectoryRole` lagts till parametern i AzureAD-modulen. Den här parametern hjälper dig att filtrera på katalogrollerna som returneras av cmdleten.
- Nya cmdlets har lagts till i AzureADPreview-modulen för att definiera och tilldela anpassade roller i Azure AD, inklusive:

    - `Get-AzureADMSRoleAssignment`
    - `Get-AzureADMSRoleDefinition`
    - `New-AzureADMSRoleAssignment`
    - `New-AzureADMSRoleDefinition`
    - `Remove-AzureADMSRoleAssignment`
    - `Remove-AzureADMSRoleDefinition`
    - `Set-AzureADMSRoleDefinition`

---

### <a name="improvements-to-the-ui-of-the-dynamic-group-rule-builder-in-the-azure-portal"></a>Förbättringar av användargränssnittet för den dynamiska gruppregelbyggaren i Azure-portalen

**Typ:** Ändrad funktion  
**Servicekategori:** Gruppledning  
**Produktkapacitet:** Samarbete

Vi har gjort vissa förbättringar av användargränssnittet till den dynamiska gruppregelbyggaren, som är tillgänglig i Azure-portalen, för att hjälpa dig att lättare konfigurera en ny regel eller ändra befintliga regler. Med den här designförbättringen kan du skapa regler med upp till fem uttryck, i stället för bara ett. Vi har också uppdaterat enhetsegenskapslistan för att ta bort inaktuella enhetsegenskaper.

Mer information finns i [Hantera dynamiska medlemskapsregler](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership).

---

### <a name="new-microsoft-graph-app-permission-available-for-use-with-access-reviews"></a>Ny behörighet för Microsoft Graph-appen är tillgänglig för användning med åtkomstgranskningar

**Typ:** Ändrad funktion  
**Servicekategori:** Access Recensioner  
**Produktkapacitet:** Identitetsstyrning

Vi har infört en ny behörighet `AccessReview.ReadWrite.Membership`för Microsoft Graph-appen, som gör att appar automatiskt kan skapa och hämta åtkomstgranskningar för gruppmedlemskap och apptilldelningar. Den här behörigheten kan användas av dina schemalagda jobb eller som en del av din automatisering, utan att kräva en inloggad användarkontext.

Mer information finns i [Exempel på hur du skapar Azure AD-åtkomstgranskningar med hjälp av Microsoft Graph-appbehörigheter med PowerShell-blogg](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-how-to-create-Azure-AD-access-reviews-using-Microsoft/m-p/807241).

---

### <a name="azure-ad-activity-logs-are-now-available-for-government-cloud-instances-in-azure-monitor"></a>Azure AD-aktivitetsloggar är nu tillgängliga för myndighetsmolninstanser i Azure Monitor

**Typ:** Ändrad funktion  
**Servicekategori:** Rapportering  
**Produktkapacitet:** Övervakning & rapportering

Vi är glada att kunna meddela att Azure AD-aktivitetsloggar nu är tillgängliga för myndighetsmolninstanser i Azure Monitor. Du kan nu skicka Azure AD-loggar till ditt lagringskonto eller till en händelsehubb för att integrera med dina SIEM-verktyg, till exempel [Sumologic,](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-sumologic) [Splunk](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-splunk)och [ArcSight](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-arcsight). 

Mer information om hur du konfigurerar Azure Monitor finns [i Azure AD-aktivitetsloggar i Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-activity-logs-azure-monitor#cost-considerations).

---

### <a name="update-your-users-to-the-new-enhanced-security-info-experience"></a>Uppdatera användarna till den nya, förbättrade säkerhetsinformationsupplevelsen

**Typ:** Ändrad funktion  
**Servicekategori:**  Autentiseringar (inloggningar)   
**Produktkapacitet:** Användarautentisering

Den 25 september 2019 kommer vi att stänga av den gamla, icke-förbättrade säkerhetsinformationsupplevelsen för att registrera och hantera användarsäkerhetsinformation och bara slå på den nya, [förbättrade versionen](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271). Det innebär att användarna inte längre kan använda den gamla upplevelsen.

Mer information om den förbättrade säkerhetsinformationsupplevelsen finns i vår [administratörsdokumentation](https://aka.ms/securityinfodocs) och vår [användardokumentation](https://aka.ms/securityinfoguide).

#### <a name="to-turn-on-this-new-experience-you-must"></a>Om du vill aktivera den här nya upplevelsen måste du:

1. Logga in på Azure-portalen som global administratör eller användaradministratör.

2. Gå till **Azure Active Directory > Användarinställningar > Hantera inställningar för förhandsgranskningsfunktioner på åtkomstpanelen**.

3. I **användarna kan använda förhandsgranskningsfunktioner för att registrera och hantera säkerhetsinformation - utökat** område, välj **Valt**och välj sedan antingen en grupp användare eller välj **Alla** för att aktivera den här funktionen för alla användare i klienten.

4. I området **Användare kan använda förhandsgranskningsfunktioner för att registrera och hantera säkerhet **info**** väljer du **Ingen**.

5. Spara inställningarna.

    När du har sparat inställningarna har du inte längre tillgång till den gamla säkerhetsinformationsupplevelsen.

>[!Important]
>Om du inte slutför de här stegen före den 25 september 2019 aktiveras din Azure Active Directory-klient automatiskt för den förbättrade upplevelsen. Om du har frågor, registrationpreview@microsoft.comvänligen kontakta oss på .

---

### <a name="authentication-requests-using-post-logins-will-be-more-strictly-validated"></a>Autentiseringsbegäranden med POST-inloggningar kommer att valideras striktare

**Typ:** Ändrad funktion  
**Servicekategori:** Autentiseringar (inloggningar)  
**Produktkapacitet:** Standarder

Från och med den 2 september 2019 valideras autentiseringsbegäranden med POST-metoden striktare mot HTTP-standarderna. Blanksteg och dubbelcitat (") tas inte längre bort från formulärvärden för begäran. Dessa ändringar förväntas inte bryta några befintliga klienter och hjälper till att se till att begäranden som skickas till Azure AD hanteras på ett tillförlitligt sätt varje gång.

Mer information finns i [meddelandena](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored)för att bryta ändringar i Azure AD .

---

## <a name="july-2019"></a>Juli 2019

### <a name="plan-for-change-application-proxy-service-update-to-support-only-tls-12"></a>Planera för ändring: Uppdatering av programproxytjänsten för att endast stödja TLS 1.2

**Typ:** Planera för förändring  
**Servicekategori:** App Proxy  
**Produktkapacitet:** Åtkomstkontroll

För att ge dig vår starkaste kryptering kommer vi att börja begränsa Application Proxy-tjänstens åtkomst till endast TLS 1.2-protokoll. Den här begränsningen kommer inledningsvis att distribueras till kunder som redan använder TLS 1.2-protokoll, så att du inte ser effekten. Fullständig utfasning av TLS 1.0- och TLS 1.1-protokollen kommer att slutföras den 31 augusti 2019. Kunder som fortfarande använder TLS 1.0 och TLS 1.1 får ett avancerat meddelande för att förbereda sig för den här ändringen.

Om du vill behålla anslutningen till application proxy-tjänsten under hela den här ändringen rekommenderar vi att du ser till att kombinationerna av klientserver och webbläsare och servrar uppdateras för att använda TLS 1.2. Vi rekommenderar också att du ser till att inkludera alla klientsystem som används av dina anställda för att komma åt appar som publiceras via application proxy-tjänsten.

Mer information finns i [Lägga till ett lokalt program för fjärråtkomst via programproxy i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application).

---

### <a name="plan-for-change-design-updates-are-coming-for-the-application-gallery"></a>Plan för förändring: Designuppdateringar kommer till programgalleriet

**Typ:** Planera för förändring  
**Servicekategori:** Företagsappar  
**Produktkapacitet:** Sso

Nya ändringar i användargränssnittet kommer till utformningen av **området Lägg till från galleriet** i **bladet Lägg till ett program.** Dessa ändringar hjälper dig att lättare hitta dina appar som stöder automatisk etablering, OpenID Connect, Security Assertion Markup Language (SAML) och Password single sign-on (SSO).

---

### <a name="plan-for-change-removal-of-the-mfa-server-ip-address-from-the-office-365-ip-address"></a>Planera för ändring: Borttagning av IP-adressen för MFA-servern från OFFICE 365 IP-adressen

**Typ:** Planera för förändring  
**Servicekategori:** Mfa  
**Produktkapacitet:** Skydd & identitetssäkerhet

Vi tar bort IP-adressen för MFA-servern från [webbtjänsten Office 365 IP-adress och URL](https://docs.microsoft.com/office365/enterprise/office-365-ip-web-service). Om du för närvarande förlitar dig på dessa sidor för att uppdatera brandväggsinställningarna måste du se till att du även inkluderar listan över IP-adresser som dokumenteras i **brandväggen för Azure Multi-Factor Authentication Server** i artikeln Komma igång med azure [multifaktorautentiseringsservern.](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy#azure-multi-factor-authentication-server-firewall-requirements)

---

### <a name="app-only-tokens-now-require-the-client-app-to-exist-in-the-resource-tenant"></a>App-bara-token kräver nu att klientappen finns i resursklienten

**Typ:** Fast  
**Servicekategori:** Autentiseringar (inloggningar)  
**Produktkapacitet:** Användarautentisering

Den 26 juli 2019 ändrade vi hur vi tillhandahåller app-bara-tokens via [klientautentiseringsbeviljandet](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow). Tidigare kunde appar hämta token för att anropa andra appar, oavsett om klientappen fanns i klienten. Vi har uppdaterat det här beteendet så att resurser för en klient, ibland kallade webb-API:er, bara kan anropas av klientappar som finns i resursklienten.

Om din app inte finns i resursklienten får du ett `The service principal named <app_name> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.` felmeddelande som säger: För att åtgärda problemet måste du skapa huvudnamnet för klientapptjänsten i klienten med hjälp av [slutpunkten för administratörsgodkännande](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#using-the-admin-consent-endpoint) eller [via PowerShell](https://docs.microsoft.com/azure/active-directory/develop/howto-authenticate-service-principal-powershell), vilket säkerställer att din klient har gett appen behörighet att fungera i klienten.

Mer information finns i [Nyheter för autentisering?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant).

> [!NOTE]
> Befintligt medgivande mellan klienten och API:et fortsätter inte att krävas. Appar bör fortfarande göra sina egna auktoriseringskontroller.

---

### <a name="new-passwordless-sign-in-to-azure-ad-using-fido2-security-keys"></a>Ny lösenordslös inloggning till Azure AD med fido2-säkerhetsnycklar

**Typ:** Ny funktion  
**Servicekategori:** Autentiseringar (inloggningar)  
**Produktkapacitet:** Användarautentisering

Azure AD-kunder kan nu ange principer för att hantera FIDO2-säkerhetsnycklar för organisationens användare och grupper. Slutanvändare kan också själv registrera sina säkerhetsnycklar, använda nycklarna för att logga in på sina Microsoft-konton på webbplatser på webbplatser medan de är på FIDO-kompatibla enheter samt logga in på sina Azure AD-anslutna Windows 10-enheter.

Mer information finns [i Aktivera lösenordslös inloggning för Azure AD (förhandsversion)](/azure/active-directory/authentication/concept-authentication-passwordless) för administratörsrelaterad information och [Konfigurera säkerhetsinformation för att använda en säkerhetsnyckel (förhandsversion)](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-security-key) för slutanvändarrelaterad information.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2019"></a>Nya federerade appar tillgängliga i Azure AD App-galleriet – juli 2019

**Typ:** Ny funktion  
**Servicekategori:** Företagsappar  
**Produktkapacitet:** Integration från tredje part

I juli 2019 har vi lagt till dessa 18 nya appar med federationsstöd i appgalleriet:

[Ungerboeck Software](https://docs.microsoft.com/azure/active-directory/saas-apps/ungerboeck-software-tutorial), [Bright Pattern Omnichannel Contact Center](https://docs.microsoft.com/azure/active-directory/saas-apps/bright-pattern-omnichannel-contact-center-tutorial), Clever [Nelly](https://docs.microsoft.com/azure/active-directory/saas-apps/clever-nelly-tutorial), [AcquireIO](https://docs.microsoft.com/azure/active-directory/saas-apps/acquireio-tutorial), [Looop](https://www.looop.co/schedule-a-demo/), [productboard](https://docs.microsoft.com/azure/active-directory/saas-apps/productboard-tutorial), [MS Azure SSO Access för Ethidex Compliance&trade;Office](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on#password-based-sso), [Hype](https://docs.microsoft.com/azure/active-directory/saas-apps/hype-tutorial), [Abstrakt](https://docs.microsoft.com/azure/active-directory/saas-apps/abstract-tutorial), [Ascentis](https://docs.microsoft.com/azure/active-directory/saas-apps/ascentis-tutorial), [Flipsnack](https://www.flipsnack.com/accounts/sign-in-sso.html), [Wandera](https://docs.microsoft.com/azure/active-directory/saas-apps/wandera-tutorial), [TwineSocial](https://twinesocial.com/), [Kallidus](https://docs.microsoft.com/azure/active-directory/saas-apps/kallidus-tutorial), [HyperAnna](https://docs.microsoft.com/azure/active-directory/saas-apps/hyperanna-tutorial), [PharmID WasteWitness](https://www.pharmid.com/), [i2B Connect](https://www.i2b-online.com/sign-up-to-use-i2b-connect-here-sso-access/), [JFrog Artifactory](https://docs.microsoft.com/azure/active-directory/saas-apps/jfrog-artifactory-tutorial)

Mer information om apparna finns i [SaaS-programintegrering med Azure Active Directory](https://aka.ms/appstutorial). Mer information om hur du listar ditt program i Azure AD-appgalleriet finns [i Lista ditt program i Azure Active Directory-programgalleriet](https://aka.ms/azureadapprequest).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatisera etablering av användarkonton för dessa nyligen stödda SaaS-appar

**Typ:** Ny funktion  
**Servicekategori:** Företagsappar  
**Produktkapacitet:** Övervakning & rapportering

Nu kan du automatisera skapandet, uppdateringen och ta bort användarkonton för dessa nyligen integrerade appar:

- [Dialpad](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [Federerad katalog](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

Mer information om hur du skyddar din organisation bättre genom att använda automatisk etablering av användarkonton finns i [Automatisera användaretablering till SaaS-program med Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

---

### <a name="new-azure-ad-domain-services-service-tag-for-network-security-group"></a>Nytt Azure AD Domain Services-tjänsttag för nätverkssäkerhetsgrupp

**Typ:** Ny funktion  
**Servicekategori:** Azure AD-domäntjänster  
**Produktkapacitet:** Azure AD-domäntjänster

Om du är trött på att hantera långa listor med IP-adresser och intervall kan du använda det nya **AzureActiveDirectoryDomainServices-nätverkstjänstmärket** i din Azure-nätverkssäkerhetsgrupp för att skydda inkommande trafik till ditt virtuella azure AD Domain Services-undernät.

Mer information om det nya tjänstmärket finns i [Nätverkssäkerhetsgrupper för Azure AD Domain Services](../../active-directory-domain-services/network-considerations.md#network-security-groups-and-required-ports).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Nya säkerhetsgranskningar för Azure AD Domain Services (offentlig förhandsversion)

**Typ:** Ny funktion  
**Servicekategori:** Azure AD-domäntjänster  
**Produktkapacitet:** Azure AD-domäntjänster

Vi är glada att kunna presentera lanseringen av Azure AD Domain Service Security Auditing till offentlig förhandsversion. Säkerhetsgranskning hjälper dig att ge dig viktig inblick i dina autentiseringstjänster genom att strömma säkerhetsgranskningshändelser till riktade resurser, inklusive Azure Storage, Azure Log Analytics-arbetsytor och Azure Event Hub, med hjälp av Azure AD Domain Service Portal.

Mer information finns i [Aktivera säkerhetsgranskningar för Azure AD Domain Services (förhandsversion)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events).

---

### <a name="new-authentication-methods-usage--insights-public-preview"></a>Användning av nya autentiseringsmetoder & insikter (offentlig förhandsversion)

**Typ:** Ny funktion  
**Servicekategori:** Återställning av lösenord för självbetjäning  
**Produktkapacitet:** Övervakning & rapportering

De nya autentiseringsmetodernas användning & insikter rapporter kan hjälpa dig att förstå hur funktioner som Azure Multi-Factor Authentication och självbetjäning lösenordsåterställning registreras och används i din organisation, inklusive antalet registrerade användare för varje funktion, hur ofta självbetjäningslösenordåterställning används för att återställa lösenord och med vilken metod återställningen sker.

Mer information finns i [Användning av Autentiseringsmetoder & insikter (förhandsgranskning)](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-methods-usage-insights).

---

### <a name="new-security-reports-are-available-for-all-azure-ad-administrators-public-preview"></a>Nya säkerhetsrapporter är tillgängliga för alla Azure AD-administratörer (Offentlig förhandsversion)

**Typ:** Ny funktion  
**Servicekategori:** Identitetsskydd  
**Produktkapacitet:** Skydd & identitetssäkerhet

Alla Azure AD-administratörer kan nu välja bannern högst upp i befintliga säkerhetsrapporter, till exempel de användare som **flaggats för riskrapport, för** att börja använda den nya säkerhetsupplevelsen som visas i rapporterna **Risky-användare** och **riska anmälningsrapporter.** Med tiden flyttas alla säkerhetsrapporter från de äldre versionerna till de nya versionerna, med de nya rapporterna som ger dig följande ytterligare funktioner:

- Avancerad filtrering och sortering

- Massåtgärder, till exempel att avvisa användarrisk

- Bekräftelse av komprometterade eller säkra enheter

- Risktillstånd, som täcker: I riskzonen, avvisas, åtgärdas och bekräftas äventyras

Mer information finns i [rapporten Risky users report](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users) och [Risky sign-ins report](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Nya säkerhetsgranskningar för Azure AD Domain Services (offentlig förhandsversion)

**Typ:** Ny funktion  
**Servicekategori:** Azure AD-domäntjänster  
**Produktkapacitet:** Azure AD-domäntjänster

Vi är glada att kunna presentera lanseringen av Azure AD Domain Service Security Auditing till offentlig förhandsversion. Säkerhetsgranskning hjälper dig att ge dig viktig inblick i dina autentiseringstjänster genom att strömma säkerhetsgranskningshändelser till riktade resurser, inklusive Azure Storage, Azure Log Analytics-arbetsytor och Azure Event Hub, med hjälp av Azure AD Domain Service Portal.

Mer information finns i [Aktivera säkerhetsgranskningar för Azure AD Domain Services (förhandsversion)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events).

---

### <a name="new-b2b-direct-federation-using-samlws-fed-public-preview"></a>Ny B2B-direkt federation med SAML/WS-Fed (offentlig förhandsversion)

**Typ:** Ny funktion  
**Servicekategori:** B2b  
**Produktkapacitet:** B2B/B2C

Direkt federation hjälper till att göra det enklare för dig att arbeta med partner vars IT-hanterade identitetslösning inte är Azure AD, genom att arbeta med identitetssystem som stöder SAML- eller WS-Fed-standarderna. När du har skapat en direkt federationsrelation med en partner kan alla nya gästanvändare som du bjuder in från den domänen samarbeta med dig med hjälp av sitt befintliga organisationskonto, vilket gör användarupplevelsen för dina gäster mer sömlös.

Mer information finns i [Direkt federation med AD FS och tredjepartsleverantörer för gästanvändare (förhandsversion).](https://docs.microsoft.com/azure/active-directory/b2b/direct-federation)

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatisera etablering av användarkonton för dessa nyligen stödda SaaS-appar

**Typ:** Ny funktion  
**Servicekategori:** Företagsappar  
**Produktkapacitet:** Övervakning & rapportering

Nu kan du automatisera skapandet, uppdateringen och ta bort användarkonton för dessa nyligen integrerade appar:

- [Dialpad](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [Federerad katalog](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

Mer information om hur du skyddar din organisation bättre genom att använda automatisk etablering av användarkonton finns i [Automatisera användaretablering till SaaS-program med Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="new-check-for-duplicate-group-names-in-the-azure-ad-portal"></a>Ny kontroll efter dubblettgruppnamn i Azure AD-portalen

**Typ:** Ny funktion  
**Servicekategori:** Gruppledning  
**Produktkapacitet:** Samarbete

Nu när du skapar eller uppdaterar ett gruppnamn från Azure AD-portalen, gör vi en kontroll för att se om du duplicerar ett befintligt gruppnamn i din resurs. Om vi fastställer att namnet redan används av en annan grupp uppmanas du att ändra ditt namn.

Mer information finns [i Hantera grupper i Azure AD-portalen](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context).

---

### <a name="azure-ad-now-supports-static-query-parameters-in-reply-redirect-uris"></a>Azure AD stöder nu statiska frågeparametrar i svars-URI:er (omdirigering)

**Typ:** Ny funktion  
**Servicekategori:** Autentiseringar (inloggningar)  
**Produktkapacitet:** Användarautentisering

Azure AD-appar kan nu registrera och använda svars-(omdirigera) `https://contoso.com/oauth2?idp=microsoft`URI:er med statiska frågeparametrar (till exempel ) för OAuth 2.0-begäranden. Den statiska frågeparametern är föremål för strängmatchning för svars-URI:er, precis som alla andra delar av svars-URI.The static query parameter is subject to string matching for reply URI, just like any other part of the reply URI. Om det inte finns någon registrerad sträng som matchar url-avkodad redirect-uri avvisas begäran. Om svars-URI hittas används hela strängen för att omdirigera användaren, inklusive den statiska frågeparametern.

Dynamiska svars-URI:er är fortfarande förbjudna eftersom de utgör en säkerhetsrisk och inte kan användas för att behålla tillståndsinformation över en autentiseringsbegäran. Använd parametern för `state` detta ändamål.

För närvarande blockerar appregistreringsskärmarna för Azure-portalen fortfarande frågeparametrar. Du kan dock redigera appmanifestet manuellt för att lägga till och testa frågeparametrar i appen. Mer information finns i [Nyheter för autentisering?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#redirect-uris-can-now-contain-query-string-parameters).

---

### <a name="activity-logs-ms-graph-apis-for-azure-ad-are-now-available-through-powershell-cmdlets"></a>Aktivitetsloggar (MS Graph API:er) för Azure AD är nu tillgängliga via PowerShell-cmdlets

**Typ:** Ny funktion  
**Servicekategori:** Rapportering  
**Produktkapacitet:** Övervakning & rapportering

Vi är glada att kunna meddela att Azure AD-aktivitetsloggar (gransknings- och inloggningsrapporter) nu är tillgängliga via Azure AD PowerShell-modulen. Tidigare kunde du skapa egna skript med MS Graph API-slutpunkter, och nu har vi utökat den funktionen till PowerShell-cmdlets.

Mer information om hur du använder dessa cmdlets finns i [Azure AD PowerShell-cmdletar för rapportering](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-powershell-reporting).

---

### <a name="updated-filter-controls-for-audit-and-sign-in-logs-in-azure-ad"></a>Uppdaterade filterkontroller för gransknings- och inloggningsloggar i Azure AD

**Typ:** Ändrad funktion  
**Servicekategori:** Rapportering  
**Produktkapacitet:** Övervakning & rapportering

Vi har uppdaterat loggrapporterna för granskning och inloggning så att du nu kan använda olika filter utan att behöva lägga till dem som kolumner på rapportskärmarna. Dessutom kan du nu bestämma hur många filter du vill visa på skärmen. Dessa uppdateringar fungerar alla tillsammans för att göra dina rapporter lättare att läsa och mer begränsade till dina behov.

Mer information om dessa uppdateringar finns i [Filtrera granskningsloggar](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#filtering-audit-logs) och [Filtrera inloggningsaktiviteter](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins#filter-sign-in-activities).

---

## <a name="june-2019"></a>Juni 2019

### <a name="new-riskdetections-api-for-microsoft-graph-public-preview"></a>Nytt API för riskdetections för Microsoft Graph (offentlig förhandsversion)

**Typ:** Ny funktion  
**Servicekategori:** Identitetsskydd  
**Produktkapacitet:** Skydd & identitetssäkerhet

Vi är glada att kunna presentera den nya riskDetections API för Microsoft Graph är nu i offentlig förhandsversion. Du kan använda det här nya API:et för att visa en lista över organisationens identitetsskyddsrelaterade användare och inloggningsriskidentifieringar. Du kan också använda det här API:et för att mer effektivt fråga dina riskidentifieringar, inklusive information om identifieringstyp, status, nivå med mera.

Mer information finns i [referensdokumentationen för API-referensdokumentation för riskidentifiering](https://docs.microsoft.com/graph/api/resources/riskdetection?view=graph-rest-beta).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2019"></a>Nya federerade appar tillgängliga i Azure AD-appgalleriet – juni 2019

**Typ:** Ny funktion  
**Servicekategori:** Företagsappar  
**Produktkapacitet:** Integration från tredje part

I juni 2019 har vi lagt till dessa 22 nya appar med federationsstöd i appgalleriet:

[Azure AD SAML Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/saml-toolkit-tutorial), [Otsuka Shokai (中ン](https://docs.microsoft.com/azure/active-directory/saas-apps/otsuka-shokai-tutorial)ンン) , [ANAQUA](https://docs.microsoft.com/azure/active-directory/saas-apps/anaqua-tutorial), [Azure VPN Client](https://portal.azure.com/), [ExpenseIn](https://docs.microsoft.com/azure/active-directory/saas-apps/expensein-tutorial), [Helper Helper](https://docs.microsoft.com/azure/active-directory/saas-apps/helper-helper-tutorial), [Costpoint](https://docs.microsoft.com/azure/active-directory/saas-apps/costpoint-tutorial), [GlobalOne](https://docs.microsoft.com/azure/active-directory/saas-apps/globalone-tutorial), [Mercedes-Benz In-Car Office](https://me.secure.mercedes-benz.com/), [Skore](https://app.justskore.it/), [Oracle Cloud Infrastructure Console](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial), [CyberArk SAML Authentication](https://docs.microsoft.com/azure/active-directory/saas-apps/cyberark-saml-authentication-tutorial), [Scrible Edu](https://www.scrible.com/sign-in/#/create-account), [PandaDoc](https://docs.microsoft.com/azure/active-directory/saas-apps/pandadoc-tutorial), [Perceptyx](https://apexdata.azurewebsites.net/docs.microsoft.com/azure/active-directory/saas-apps/perceptyx-tutorial), [Proptimise OS](https://proptimise.co.uk/software/), [Vtiger CRM (SAML)](https://docs.microsoft.com/azure/active-directory/saas-apps/vtiger-crm-saml-tutorial), Oracle Access Manager för Oracle Retail Merchandising, Oracle Access Manager för Oracle E-Business Suite, Oracle IDCS för E-Business Suite, Oracle IDCS för PeopleSoft, Oracle IDCS för JD Edwards

Mer information om apparna finns i [SaaS-programintegrering med Azure Active Directory](https://aka.ms/appstutorial). Mer information om hur du listar ditt program i Azure AD-appgalleriet finns [i Lista ditt program i Azure Active Directory-programgalleriet](https://aka.ms/azureadapprequest).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatisera etablering av användarkonton för dessa nyligen stödda SaaS-appar

**Typ:** Ny funktion  
**Servicekategori:** Företagsappar  
**Produktkapacitet:** Övervakning & rapportering

Nu kan du automatisera skapandet, uppdateringen och ta bort användarkonton för dessa nyligen integrerade appar:

- [Zoom](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-provisioning-tutorial)

- [Envoy](https://docs.microsoft.com/azure/active-directory/saas-apps/envoy-provisioning-tutorial)

- [Proxyclick](https://docs.microsoft.com/azure/active-directory/saas-apps/proxyclick-provisioning-tutorial)

- [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-provisioning-tutorial)

Mer information om hur du skyddar din organisation bättre genom att använda automatisk etablering av användarkonton finns i [Automatisera användaretablering till SaaS-program med Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

---

### <a name="view-the-real-time-progress-of-the-azure-ad-provisioning-service"></a>Visa realtidsförloppet för Azure AD-etableringstjänsten

**Typ:** Ändrad funktion  
**Servicekategori:** Etablering av appar  
**Produktkapacitet:** Hantering av identitetslivscykeln

Vi har uppdaterat Azure AD-etableringsupplevelsen för att inkludera ett nytt förloppsfält som visar hur långt du befinner dig i processen för användaretablering. Den här uppdaterade upplevelsen ger också information om antalet användare som etablerats under den aktuella cykeln, samt hur många användare som har etablerats hittills.

Mer information finns [i Kontrollera status för användaretablering](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user).

---

### <a name="company-branding-now-appears-on-sign-out-and-error-screens"></a>Företagets varumärke visas nu på ut logga ut och fel skärmar

**Typ:** Ändrad funktion  
**Servicekategori:** Autentiseringar (inloggningar)  
**Produktkapacitet:** Användarautentisering

Vi har uppdaterat Azure AD så att ditt företags varumärke nu visas på ut logga ut och fel skärmar, samt inloggningssidan. Du behöver inte göra något för att aktivera den här funktionen, Azure AD använder helt enkelt de resurser som du redan har konfigurerat i området **Företagsprofilering** på Azure-portalen.

Mer information om hur du konfigurerar företagets varumärke finns i [Lägga till varumärkesprofilering på organisationens Azure Active Directory-sidor](https://docs.microsoft.com/azure/active-directory/fundamentals/customize-branding).

---

### <a name="azure-multi-factor-authentication-mfa-server-is-no-longer-available-for-new-deployments"></a>MFA-server (Azure Multi-Factor Authentication) är inte längre tillgänglig för nya distributioner

**Typ:** Deprecated  
**Servicekategori:** Mfa  
**Produktkapacitet:** Skydd & identitetssäkerhet

Från och med den 1 juli 2019 kommer Microsoft inte längre att erbjuda MFA Server för nya distributioner. Nya kunder som vill kräva multifaktorautentisering i sin organisation måste nu använda molnbaserad Azure Multi-Factor-autentisering. Kunder som aktiverat MFA Server före den 1 juli kommer inte att se någon ändring. Du kan fortfarande hämta den senaste versionen, få framtida uppdateringar och generera aktiveringsautentiseringsuppgifter.

Mer information finns [i Komma igång med Azure Multi-Factor Authentication Server](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy). Mer information om molnbaserad Azure Multi-Factor-autentisering finns i [Planera en molnbaserad Azure Multi-Factor Authentication-distribution](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted).

---

## <a name="may-2019"></a>Maj 2019

### <a name="service-change-future-support-for-only-tls-12-protocols-on-the-application-proxy-service"></a>Tjänständring: Framtida stöd för endast TLS 1.2-protokoll på tjänsten Application Proxy

**Typ:** Planera för förändring  
**Servicekategori:** App Proxy  
**Produktkapacitet:** Åtkomstkontroll

För att ge förstklassig kryptering för våra kunder begränsar vi åtkomsten till endast TLS 1.2-protokoll på application proxy-tjänsten. Den här ändringen distribueras gradvis till kunder som redan bara använder TLS 1.2-protokoll, så du bör inte se några ändringar.

Utfasning av TLS 1.0 och TLS 1.1 inträffar den 31 augusti 2019, men vi kommer att ge ytterligare förhandsmeddelande, så du får tid att förbereda dig för den här ändringen. För att förbereda för den här ändringen se till att kombinationerna av klientserver och webbläsare, inklusive alla klienter som användarna använder för att komma åt appar som publiceras via Programproxy, uppdateras för att använda TLS 1.2-protokollet för att upprätthålla anslutningen till programmet Proxytjänst. Mer information finns i [Lägga till ett lokalt program för fjärråtkomst via programproxy i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#before-you-begin).

---

### <a name="use-the-usage-and-insights-report-to-view-your-app-related-sign-in-data"></a>Använd användnings- och statistikrapporten för att visa apprelaterade inloggningsdata

**Typ:** Ny funktion  
**Servicekategori:** Företagsappar  
**Produktkapacitet:** Övervakning & rapportering

Du kan nu använda användnings- och insiktsrapporten, som finns i området **Enterprise-program** i Azure-portalen, för att få en programcentrerad vy över dina inloggningsdata, inklusive information om:

- De appar som används högst för din organisation

- Appar med de mest misslyckade inloggningarna

- De vanligaste inloggningsfelen för varje app

Mer information om den här funktionen finns [i rapporten Användning och insikter i Azure Active Directory-portalen](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-usage-insights-report)

---

### <a name="automate-your-user-provisioning-to-cloud-apps-using-azure-ad"></a>Automatisera din användaretablering till molnappar med Azure AD

**Typ:** Ny funktion  
**Servicekategori:** Företagsappar  
**Produktkapacitet:** Övervakning & rapportering

Följ dessa nya självstudier om du vill använda Azure AD-etableringstjänsten för att automatisera skapandet, borttagningen och uppdateringen av användarkonton för följande molnbaserade appar:

- [Comeet (komet)](https://docs.microsoft.com/azure/active-directory/saas-apps/comeet-recruiting-software-provisioning-tutorial)

- [Dynamisksignal](https://docs.microsoft.com/azure/active-directory/saas-apps/dynamic-signal-provisioning-tutorial)

- [KeeperSäkerhet](https://docs.microsoft.com/azure/active-directory/saas-apps/keeper-password-manager-digitalvault-provisioning-tutorial)

Du kan också följa den här nya [Dropbox-självstudien](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial), som innehåller information om hur du etablerar gruppobjekt.

Mer information om hur du skyddar din organisation bättre genom automatisk etablering av användarkonton finns i [Automatisera användaretablering till SaaS-program med Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="identity-secure-score-is-now-available-in-azure-ad-general-availability"></a>Identitetssäker poäng är nu tillgänglig i Azure AD (Allmän tillgänglighet)

**Typ:** Ny funktion  
**Servicekategori:** Ej mycket  
**Produktkapacitet:** Skydd & identitetssäkerhet

Du kan nu övervaka och förbättra din identitetssäkerhetsposition med hjälp av funktionen identitetssäker poäng i Azure AD. Funktionen identitetssäker poäng använder en enda instrumentpanel för att hjälpa dig:

- Objektivt mäta din identitetssäkerhet hållning, baserat på en poäng mellan 1 och 223.

- Planera för förbättringar av identitetssäkerheten

- Granska framgången för dina säkerhetsförbättringar

Mer information om funktionen identitetssäkerhetspoäng finns [i Vad är den identitetssäkra poängen i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score).

---

### <a name="new-app-registrations-experience-is-now-available-general-availability"></a>Ny appregistreringar erfarenhet är nu tillgänglig (Allmän tillgänglighet)

**Typ:** Ny funktion  
**Servicekategori:** Autentiseringar (inloggningar)  
**Produktkapacitet:** Utvecklarupplevelse

Den nya [appregistreringsupplevelsen](https://aka.ms/appregistrations) är nu i allmän tillgänglighet. Den här nya upplevelsen innehåller alla viktiga funktioner som du är bekant med från Azure-portalen och programregistreringsportalen och förbättrar dem genom:

- **Bättre apphantering.** I stället för att se dina appar på olika portaler kan du nu se alla dina appar på en plats.

- **Förenklad appregistrering.** Från den förbättrade navigeringsupplevelsen till den förnyade behörighetsvalsupplevelsen är det nu enklare att registrera och hantera dina appar.

- **Mer detaljerad information.** Du kan hitta mer information om din app, inklusive snabbstartsguider med mera.

Mer information finns i [Microsofts identitetsplattform](https://docs.microsoft.com/azure/active-directory/develop/) och [appregistreringsupplevelsen är nu allmänt tillgänglig!](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) blogg tillkännagivande.

---

### <a name="new-capabilities-available-in-the-risky-users-api-for-identity-protection"></a>Nya funktioner som är tillgängliga i API:et för riskfyllda användare för identitetsskydd

**Typ:** Ny funktion  
**Servicekategori:** Identitetsskydd  
**Produktkapacitet:** Skydd & identitetssäkerhet

Vi är glada att kunna meddela att du nu kan använda Api:et för riskfyllda användare för att hämta användarnas riskhistorik, stänga av riskfyllda användare och bekräfta användare som komprometterade. Den här ändringen hjälper dig att mer effektivt uppdatera användarnas riskstatus och förstå deras riskhistorik.

Mer information finns i [referensdokumentationen för Risky Users API](https://docs.microsoft.com/graph/api/resources/riskyuser?view=graph-rest-beta).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2019"></a>Nya federerade appar tillgängliga i Azure AD-appgalleriet – maj 2019

**Typ:** Ny funktion  
**Servicekategori:** Företagsappar  
**Produktkapacitet:** Integration från tredje part

I maj 2019 har vi lagt till dessa 21 nya appar med federationsstöd i appgalleriet:

[Freedcamp](https://docs.microsoft.com/azure/active-directory/saas-apps/freedcamp-tutorial), [Real Links](https://docs.microsoft.com/azure/active-directory/saas-apps/real-links-tutorial), [Kianda](https://app.kianda.com/sso/OpenID/AzureAD/), [Simple Sign](https://docs.microsoft.com/azure/active-directory/saas-apps/simple-sign-tutorial), [Braze](https://docs.microsoft.com/azure/active-directory/saas-apps/braze-tutorial), [Displayr](https://docs.microsoft.com/azure/active-directory/saas-apps/displayr-tutorial), [Templafy](https://docs.microsoft.com/azure/active-directory/saas-apps/templafy-tutorial), [Marketo Sales Engage](https://toutapp.com/login), [ACLP](https://docs.microsoft.com/azure/active-directory/saas-apps/aclp-tutorial), [OutSystems](https://docs.microsoft.com/azure/active-directory/saas-apps/outsystems-tutorial), [Meta4 Global HR](https://docs.microsoft.com/azure/active-directory/saas-apps/meta4-global-hr-tutorial), Quantum [Workplace](https://docs.microsoft.com/azure/active-directory/saas-apps/quantum-workplace-tutorial), [Kobolt](https://docs.microsoft.com/azure/active-directory/saas-apps/cobalt-tutorial), [webMethods API Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [RedFlag](https://pocketstop.com/redflag/), [Whatfix](https://docs.microsoft.com/azure/active-directory/saas-apps/whatfix-tutorial), [Kontroll](https://docs.microsoft.com/azure/active-directory/saas-apps/control-tutorial), [JOBHUB](https://docs.microsoft.com/azure/active-directory/saas-apps/jobhub-tutorial), [NEOGOV](https://docs.microsoft.com/azure/active-directory/saas-apps/neogov-tutorial), [Foodee](https://docs.microsoft.com/azure/active-directory/saas-apps/foodee-tutorial), [MyVR](https://docs.microsoft.com/azure/active-directory/saas-apps/myvr-tutorial)

Mer information om apparna finns i [SaaS-programintegrering med Azure Active Directory](https://aka.ms/appstutorial). Mer information om hur du listar ditt program i Azure AD-appgalleriet finns [i Lista ditt program i Azure Active Directory-programgalleriet](https://aka.ms/azureadapprequest).

---

### <a name="improved-groups-creation-and-management-experiences-in-the-azure-ad-portal"></a>Förbättrade skapande- och hanteringsupplevelser för grupper i Azure AD-portalen

**Typ:** Ny funktion  
**Servicekategori:** Gruppledning  
**Produktkapacitet:** Samarbete

Vi har gjort förbättringar av de grupprelaterade upplevelserna i Azure AD-portalen. Dessa förbättringar gör det möjligt för administratörer att hantera grupper, medlemslistor och tillhandahålla ytterligare alternativ för att skapa.

Bland förbättringarna finns:

- Grundläggande filtrering efter medlemstyp och grupptyp.

- Tillägg av nya kolumner, till exempel Källa och E-postadress.

- Möjlighet att välja grupper, medlemmar och ägarlistor för enkel borttagning.

- Möjlighet att välja en e-postadress och lägga till ägare när gruppen skapas.

Mer information finns i [Skapa en grundläggande grupp och lägga till medlemmar med hjälp av Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-general-availability"></a>Konfigurera en namngivningsprincip för Office 365-grupper i Azure AD-portalen (allmän tillgänglighet)

**Typ:** Ändrad funktion  
**Servicekategori:** Gruppledning  
**Produktkapacitet:** Samarbete

Administratörer kan nu konfigurera en namngivningsprincip för Office 365-grupper med hjälp av Azure AD-portalen. Den här ändringen bidrar till att tillämpa konsekventa namngivningskonventioner för Office 365-grupper som skapats eller redigerats av användare i organisationen.

Du kan konfigurera namngivningsprincipen för Office 365-grupper på två olika sätt:

- Definiera prefix eller suffix som läggs till automatiskt i ett gruppnamn.

- Ladda upp en anpassad uppsättning blockerade ord för din organisation, som inte är tillåtna i gruppnamn (till exempel "VD, Lön, HR").

Mer information finns i [Tillämpa en namngivningsprincip för Office 365-grupper](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="microsoft-graph-api-endpoints-are-now-available-for-azure-ad-activity-logs-general-availability"></a>Microsoft Graph API-slutpunkter är nu tillgängliga för Azure AD-aktivitetsloggar (allmän tillgänglighet)

**Typ:** Ändrad funktion  
**Servicekategori:** Rapportering  
**Produktkapacitet:** Övervakning & rapportering

Vi är glada att kunna meddela allmän tillgänglighet för Microsoft Graph API-slutpunkter stöd för Azure AD aktivitetsloggar. Med den här versionen kan du nu använda version 1.0 av både Azure AD-granskningsloggarna och api:erna för inloggningsloggar.

Mer information finns i [Azure AD-granskningsloggens API-översikt](https://docs.microsoft.com/graph/api/resources/azure-ad-auditlog-overview?view=graph-rest-1.0).

---

### <a name="administrators-can-now-use-conditional-access-for-the-combined-registration-process-public-preview"></a>Administratörer kan nu använda villkorlig åtkomst för den kombinerade registreringsprocessen (Offentlig förhandsversion)

**Typ:** Ny funktion  
**Servicekategori:** Villkorlig åtkomst  
**Produktkapacitet:** Skydd & identitetssäkerhet  

Administratörer kan nu skapa principer för villkorlig åtkomst som kan användas av den kombinerade registreringssidan. Detta inkluderar att tillämpa principer för att tillåta registrering om:

- Användarna finns i ett betrott nätverk.

- Användare är en låg inloggningsrisk.

- Användarna finns på en hanterad enhet.

- Användarna godkänner organisationens användarvillkor (TOU).

Mer information om villkorlig åtkomst och återställning av lösenord finns i [blogginlägget för villkorlig åtkomst för Azure AD kombinerad MFA och registreringsupplevelse för registrering av lösenordsåterställning](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Conditional-access-for-the-Azure-AD-combined-MFA-and-password/ba-p/566348). Mer information om principer för villkorlig åtkomst för den kombinerade registreringsprocessen finns i [principer för villkorlig åtkomst för kombinerad registrering](https://docs.microsoft.com/azure/active-directory/authentication/howto-registration-mfa-sspr-combined#conditional-access-policies-for-combined-registration). Mer information om azure AD-användningsfunktionen finns i [Azure Active Directory-användningsvillkor funktionen](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

## <a name="april-2019"></a>April 2019

### <a name="new-azure-ad-threat-intelligence-detection-is-now-available-as-part-of-azure-ad-identity-protection"></a>Identifiering av ny Azure AD-hotinformation är nu tillgänglig som en del av Azure AD Identity Protection

**Typ:** Ny funktion  
**Servicekategori:** Skydd mot Azure AD-identitet  
**Produktkapacitet:** Skydd & identitetssäkerhet

Azure AD threat intelligence-identifiering är nu tillgänglig som en del av den uppdaterade Azure AD Identity Protection-funktionen. Den här nya funktionen hjälper till att indikera ovanlig användaraktivitet för en viss användare eller aktivitet som överensstämmer med kända attackmönster baserat på Microsofts interna och externa hotinformationskällor.

Mer information om den uppdaterade versionen av Azure AD Identity Protection finns i de [fyra stora förbättringarna av Azure AD-identitetsskydd finns nu i den offentliga förhandsversionen](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Four-major-Azure-AD-Identity-Protection-enhancements-are-now-in/ba-p/326935) och [Azure Active Directory Identity Protection (uppdateras)?](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-v2) . Mer information om Identifiering av Azure AD-hotinformation finns i artikeln [för riskidentifiering av Azure Active Directory Identity Protection.](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-risks)

---

### <a name="azure-ad-entitlement-management-is-now-available-public-preview"></a>Hantering av Azure AD-berättigande är nu tillgänglig (offentlig förhandsversion)

**Typ:** Ny funktion  
**Servicekategori:** Identitetsstyrning  
**Produktkapacitet:** Identitetsstyrning

Azure AD-berättigandehantering, som nu är i offentlig förhandsversion, hjälper kunder att delegera hantering av åtkomstpaket, som definierar hur anställda och affärspartners kan begära åtkomst, vem som måste godkänna och hur länge de har åtkomst. Åtkomstpaket kan hantera medlemskap i Azure AD- och Office 365-grupper, rolltilldelningar i företagsprogram och rolltilldelningar för SharePoint Online-webbplatser. Läs mer om rättighetshantering i [översikten över Azure AD-berättigandehantering](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview). Mer information om bredden på Azure AD Identity Governance-funktioner, inklusive privilegierad identitetshantering, åtkomstgranskningar och användningsvillkor, finns i [Vad är Azure AD Identity Governance?](../governance/identity-governance-overview.md).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-public-preview"></a>Konfigurera en namngivningsprincip för Office 365-grupper i Azure AD-portal (offentlig förhandsversion)

**Typ:** Ny funktion  
**Servicekategori:** Gruppledning  
**Produktkapacitet:** Samarbete

Administratörer kan nu konfigurera en namngivningsprincip för Office 365-grupper med hjälp av Azure AD-portalen. Den här ändringen bidrar till att tillämpa konsekventa namngivningskonventioner för Office 365-grupper som skapats eller redigerats av användare i organisationen.

Du kan konfigurera namngivningsprincipen för Office 365-grupper på två olika sätt:

- Definiera prefix eller suffix som läggs till automatiskt i ett gruppnamn.

- Ladda upp en anpassad uppsättning blockerade ord för din organisation, som inte är tillåtna i gruppnamn (till exempel "VD, Lön, HR").

Mer information finns i [Tillämpa en namngivningsprincip för Office 365-grupper](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>Azure AD-aktivitetsloggar är nu tillgängliga i Azure Monitor (Allmän tillgänglighet)

**Typ:** Ny funktion  
**Servicekategori:** Rapportering  
**Produktkapacitet:** Övervakning & rapportering

För att hjälpa dig att hantera din feedback om visualiseringar med Azure AD-aktivitetsloggarna introducerar vi en ny Insights-funktion i Logganalys. Den här funktionen hjälper dig att få insikter om dina Azure AD-resurser med hjälp av våra interaktiva mallar, så kallade arbetsböcker. Dessa färdiga arbetsböcker kan innehålla information för appar eller användare och inkluderar:

- **Logga in.** Innehåller information om appar och användare, inklusive inloggningsplats, operativsystemet i drift eller webbläsarklienten och-versionen, och antalet lyckade eller misslyckade inloggningar.

- **Äldre autentisering och villkorlig åtkomst.** Innehåller information om appar och användare som använder äldre autentisering, inklusive användning av multifaktorautentisering som utlöses av principer för villkorlig åtkomst, appar med principer för villkorlig åtkomst och så vidare.

- **Inloggningsanalys.** Hjälper dig att avgöra om dina inloggningsfel uppstår på grund av en användaråtgärd, principproblem eller din infrastruktur.

- **Anpassade rapporter.** Du kan skapa nya eller redigera befintliga arbetsböcker för att anpassa insights-funktionen för din organisation.

Mer information finns i [Så här använder du Azure Monitor-arbetsböcker för Azure Active Directory-rapporter](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2019"></a>Nya federerade appar tillgängliga i Azure AD-appgalleriet – april 2019

**Typ:** Ny funktion  
**Servicekategori:** Företagsappar  
**Produktkapacitet:** Integration från tredje part

I april 2019 har vi lagt till dessa 21 nya appar med federationsstöd i appgalleriet:

[SAP Fiori](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-fiori-tutorial), [HRworks Enkel inloggning](https://docs.microsoft.com/azure/active-directory/saas-apps/hrworks-single-sign-on-tutorial), [Percolate](https://docs.microsoft.com/azure/active-directory/saas-apps/percolate-tutorial), [MobiControl](https://docs.microsoft.com/azure/active-directory/saas-apps/mobicontrol-tutorial), [Citrix NetScaler](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial), [Shibumi](https://docs.microsoft.com/azure/active-directory/saas-apps/shibumi-tutorial), [Benchling](https://docs.microsoft.com/azure/active-directory/saas-apps/benchling-tutorial), [MileIQ](https://mileiq.onelink.me/991934284/7e980085), [PageDNA](https://docs.microsoft.com/azure/active-directory/saas-apps/pagedna-tutorial), [EduBrite LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/edubrite-lms-tutorial), [RStudio Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/rstudio-connect-tutorial), [AMMS](https://docs.microsoft.com/azure/active-directory/saas-apps/amms-tutorial), [Mitel Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/mitel-connect-tutorial), Alibaba Cloud [(Roll-baserade SSO)](https://docs.microsoft.com/azure/active-directory/saas-apps/alibaba-cloud-service-role-based-sso-tutorial), [Certent Equity Management](https://docs.microsoft.com/azure/active-directory/saas-apps/certent-equity-management-tutorial), [Sectigo Certificate Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/sectigo-certificate-manager-tutorial), [GreenOrbit](https://docs.microsoft.com/azure/active-directory/saas-apps/greenorbit-tutorial), [Workgrid](https://docs.microsoft.com/azure/active-directory/saas-apps/workgrid-tutorial), [monday.com](https://docs.microsoft.com/azure/active-directory/saas-apps/mondaycom-tutorial), [ SurveyMonkey Företag](https://docs.microsoft.com/azure/active-directory/saas-apps/surveymonkey-enterprise-tutorial), [Indiggo](https://indiggolead.com/)

Mer information om apparna finns i [SaaS-programintegrering med Azure Active Directory](https://aka.ms/appstutorial). Mer information om hur du listar ditt program i Azure AD-appgalleriet finns [i Lista ditt program i Azure Active Directory-programgalleriet](https://aka.ms/azureadapprequest).

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>Nytt frekvensalternativ för åtkomstgranskningar och val av flera roller

**Typ:** Ny funktion  
**Servicekategori:** Access Recensioner  
**Produktkapacitet:** Identitetsstyrning

Nya uppdateringar i Azure AD-åtkomstgranskningar gör att du kan:

- Ändra frekvensen för dina åtkomstgranskningar till **halvårsvis,** utöver de tidigare befintliga alternativen för veckovisa, månadsvisa, kvartalsvisa och årligen.

- Välj flera Azure AD- och Azure-resursroller när du skapar en enda åtkomstgranskning. I det här fallet ställs alla roller in med samma inställningar och alla granskare meddelas samtidigt.

Mer information om hur du skapar en åtkomstgranskning finns i [Skapa en åtkomstgranskning av grupper eller program i Azure AD-åtkomstgranskningar](https://docs.microsoft.com/azure/active-directory/governance/create-access-review).

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>Azure AD Connect e-postaviseringssystem övergår och skickar ny e-postavsändaresinformation för vissa kunder

**Typ:** Ändrad funktion  
**Servicekategori:** AD-synkronisering  
**Produktkapacitet:** Plattform

Azure AD Connect håller på att övergå i vårt e-postaviseringssystem, vilket kan visa vissa kunder en ny e-postavsändare. För att åtgärda detta `azure-noreply@microsoft.com` måste du lägga till organisationens tillåt-lista eller så kan du inte fortsätta att ta emot viktiga aviseringar från dina Office 365-, Azure- eller Synkroniseringstjänster.

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>UPN-suffixändringar lyckas nu mellan Federerade domäner i Azure AD Connect

**Typ:** Fast  
**Servicekategori:** AD-synkronisering  
**Produktkapacitet:** Plattform

Du kan nu ändra en användares UPN-suffix från en Federerad domän till en annan Federerad domän i Azure AD Connect. Den här korrigeringsfilen innebär att du inte längre ska uppleva felmeddelandet FederatedDomainChangeError under synkroniseringscykeln eller få ett e-postmeddelande med texten "Det går inte att uppdatera objektet i Azure Active Directory, eftersom attributet [ FederatedUser.UserPrincipalName] är ogiltigt. Uppdatera värdet i dina lokala katalogtjänster".

Mer information finns i [Felsöka fel under synkroniseringen](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sync-errors#federateddomainchangeerror).

---

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>Ökad säkerhet med hjälp av appskyddsbaserad princip för villkorlig åtkomst i Azure AD (offentlig förhandsversion)

**Typ:** Ny funktion  
**Servicekategori:** Villkorlig åtkomst  
**Produktkapacitet:** Skydd & identitetssäkerhet

Appskyddsbaserad villkorlig åtkomst är nu tillgänglig med hjälp av principen **Kräv appskydd.** Den här nya principen bidrar till att öka organisationens säkerhet genom att förhindra:

- Användare får åtkomst till appar utan Microsoft Intune-licens.

- Användare kan inte få en Microsoft Intune-appskyddsprincip.

- Användare får åtkomst till appar utan en konfigurerad Microsoft Intune-appskyddsprincip.

Mer information finns i [Så här kräver du appskyddsprincip för åtkomst till molnapp med villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/app-protection-based-conditional-access).

---

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>Nytt stöd för enkel Azure AD-inloggning och villkorlig åtkomst i Microsoft Edge (offentlig förhandsversion)

**Typ:** Ny funktion  
**Servicekategori:** Villkorlig åtkomst  
**Produktkapacitet:** Skydd & identitetssäkerhet

Vi har förbättrat vårt Azure AD-stöd för Microsoft Edge, inklusive att tillhandahålla nytt stöd för azure AD enkel inloggning och villkorlig åtkomst. Om du tidigare har använt Microsoft Intune Managed Browser kan du nu använda Microsoft Edge i stället.

Mer information om hur du konfigurerar och hanterar dina enheter och appar med villkorlig åtkomst finns i [Kräv hanterade enheter för molnappåtkomst med villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices) och [Kräv godkända klientappar för molnappåtkomst med villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). Mer information om hur du hanterar åtkomst med Microsoft Edge med Microsoft Intune-principer finns i [Hantera Internet-åtkomst med hjälp av en Microsoft Intune-principskyddad webbläsare](https://docs.microsoft.com/intune/app-configuration-managed-browser).

---

## <a name="march-2019"></a>Mars 2019

### <a name="identity-experience-framework-and-custom-policy-support-in-azure-active-directory-b2c-is-now-available-ga"></a>Identity Experience Framework och anpassat principstöd i Azure Active Directory B2C är nu tillgängligt (GA)

**Typ:** Ny funktion  
**Servicekategori:** B2C - Hantering av konsumentidentiteter  
**Produktkapacitet:** B2B/B2C

Du kan nu skapa anpassade principer i Azure AD B2C, inklusive följande uppgifter, som stöds i stor skala och under vårt Azure SLA:

- Skapa och ladda upp anpassade autentiseringsanvändarresor med hjälp av anpassade principer.

- Beskriv användarresor steg för steg som utbyten mellan skadeleverantörer.

- Definiera villkorlig förgrening i användarresor.

- Omvandla och mappa anspråk för användning i realtidsbeslut och kommunikation.

- Använd REST API-aktiverade tjänster i dina anpassade autentiseringsanvändarresor. Till exempel med e-postleverantörer, CRMs och proprietära auktoriseringssystem.

- Federera med identitetsleverantörer som är kompatibla med OpenIDConnect-protokollet. Till exempel med Azure AD med flera innehavare, leverantörer av sociala konton eller tvåfaktorsverifieringsleverantörer.

Mer information om hur du skapar anpassade principer finns i [Utvecklaranteckningar för anpassade principer i Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-developer-notes-custom) och läsa [Alex Simons blogginlägg, inklusive fallstudier](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-custom-policies-to-build-your-own-identity-journeys/ba-p/382791).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2019"></a>Nya federerade appar tillgängliga i Azure AD-appgalleriet – mars 2019

**Typ:** Ny funktion  
**Servicekategori:** Företagsappar  
**Produktkapacitet:** Integration från tredje part

I mars 2019 har vi lagt till dessa 14 nya appar med federationsstöd i appgalleriet:

[ISEC7 Mobile Exchange Delegate](https://www.isec7.com/english/), [MediusFlow](https://office365.cloudapp.mediusflow.com/), [ePlatform](https://docs.microsoft.com/azure/active-directory/saas-apps/eplatform-tutorial), [Fulcrum](https://docs.microsoft.com/azure/active-directory/saas-apps/fulcrum-tutorial), [ExcelityGlobal](https://docs.microsoft.com/azure/active-directory/saas-apps/excelityglobal-tutorial), [Förklaringsbaserat granskningssystem](https://docs.microsoft.com/azure/active-directory/saas-apps/explanation-based-auditing-system-tutorial), [Lean](https://docs.microsoft.com/azure/active-directory/saas-apps/lean-tutorial), [Powerschool Performance Matters](https://docs.microsoft.com/azure/active-directory/saas-apps/powerschool-performance-matters-tutorial), [Cinode](https://cinode.com/), [Iris Intranät](https://docs.microsoft.com/azure/active-directory/saas-apps/iris-intranet-tutorial), [Empactis](https://docs.microsoft.com/azure/active-directory/saas-apps/empactis-tutorial), [SmartDraw](https://docs.microsoft.com/azure/active-directory/saas-apps/smartdraw-tutorial), [Confirmit Horizons](https://docs.microsoft.com/azure/active-directory/saas-apps/confirmit-horizons-tutorial), [TAS](https://docs.microsoft.com/azure/active-directory/saas-apps/tas-tutorial)

Mer information om apparna finns i [SaaS-programintegrering med Azure Active Directory](https://aka.ms/appstutorial). Mer information om hur du listar ditt program i Azure AD-appgalleriet finns [i Lista ditt program i Azure Active Directory-programgalleriet](https://aka.ms/azureadapprequest).

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>Nya Zscaler- och Atlassian-etableringsanslutningar i Azure AD-galleriet – mars 2019

**Typ:** Ny funktion  
**Servicekategori:** Etablering av appar  
**Produktkapacitet:** Integration från tredje part

Automatisera skapande, uppdatering och borttagning av användarkonton för följande appar:

[Zscaler](https://aka.ms/ZscalerProvisioning), [Zscaler Beta](https://aka.ms/ZscalerBetaProvisioning), [Zscaler One](https://aka.ms/ZscalerOneProvisioning), [Zscaler Två](https://aka.ms/ZscalerTwoProvisioning), [Zscaler Tre](https://aka.ms/ZscalerThreeProvisioning), [Zscaler ZSCloud](https://aka.ms/ZscalerZSCloudProvisioning), [Atlassian Cloud](https://aka.ms/atlassianCloudProvisioning)

Mer information om hur du skyddar din organisation bättre genom automatisk etablering av användarkonton finns i [Automatisera användaretablering till SaaS-program med Azure AD](https://aka.ms/ProvisioningDocumentation).

---

### <a name="restore-and-manage-your-deleted-office-365-groups-in-the-azure-ad-portal"></a>Återställa och hantera borttagna Office 365-grupper i Azure AD-portalen

**Typ:** Ny funktion  
**Servicekategori:** Gruppledning  
**Produktkapacitet:** Samarbete

Nu kan du visa och hantera dina borttagna Office 365-grupper från Azure AD-portalen. Den här ändringen hjälper dig att se vilka grupper som är tillgängliga för återställning, tillsammans med att låta dig ta bort alla grupper som inte behövs permanent av din organisation.

Mer information finns i [Återställ utgångna eller borttagna grupper](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-restore-deleted#view-and-manage-the-deleted-office-365-groups-that-are-available-to-restore).

---

### <a name="single-sign-on-is-now-available-for-azure-ad-saml-secured-on-premises-apps-through-application-proxy-public-preview"></a>Enkel inloggning är nu tillgänglig för Azure AD SAML-säkrade lokala appar via Application Proxy (offentlig förhandsversion)

**Typ:** Ny funktion  
**Servicekategori:** App Proxy  
**Produktkapacitet:** Åtkomstkontroll

Du kan nu tillhandahålla en enkel inloggningsupplevelse (SSO) för lokala SAML-autentiserade appar, tillsammans med fjärråtkomst till dessa appar via Application Proxy. Mer information om hur du konfigurerar SAML SSO med dina lokala appar finns i [SAML:s enda inloggning för lokala program med Application Proxy (Preview).](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-on-premises-apps)

---

### <a name="client-apps-in-request-loops-will-be-interrupted-to-improve-reliability-and-user-experience"></a>Klientappar i förfrågningsloopar avbryts för att förbättra tillförlitligheten och användarupplevelsen

**Typ:** Ny funktion  
**Servicekategori:** Autentiseringar (inloggningar)  
**Produktkapacitet:** Användarautentisering

Klientappar kan felaktigt utfärda hundratals av samma inloggningsbegäranden under en kort tidsperiod. Dessa begäranden, oavsett om de lyckas eller inte, bidrar alla till en dålig användarupplevelse och ökade arbetsbelastningar för IDP, vilket ökar svarstiden för alla användare och minskar tillgängligheten för IDP.

Den här `invalid_grant` uppdateringen `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` skickar ett fel: till klientappar som utfärdar dubblettbegäranden flera gånger under en kort tidsperiod, utöver omfattningen av normal drift. Klientappar som stöter på det här problemet bör visa en interaktiv uppmaning som kräver att användaren loggar in igen. Mer information om den här ändringen och hur du åtgärdar din app om den stöter på det här felet finns i [Vad är nytt för autentisering?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#looping-clients-will-be-interrupted).

---

### <a name="new-audit-logs-user-experience-now-available"></a>Användarupplevelse för nya granskningsloggar är nu tillgänglig

**Typ:** Ändrad funktion  
**Servicekategori:** Rapportering  
**Produktkapacitet:** Övervakning & rapportering

Vi har skapat en ny sida för Azure AD **Audit-logger** för att förbättra både läsbarheten och hur du söker efter din information. Om du vill se den nya sidan **granskningsloggar** väljer du **Granskningsloggar** i avsnittet **Aktivitet** i Azure AD.

![Sidan Nya granskningsloggar med exempelinformation](media/whats-new/audit-logs-page.png)

Mer information om den nya sidan **Granskningsloggar** finns [i Granska aktivitetsrapporter i Azure Active Directory-portalen](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#audit-logs).

---

### <a name="new-warnings-and-guidance-to-help-prevent-accidental-administrator-lockout-from-misconfigured-conditional-access-policies"></a>Nya varningar och vägledning för att förhindra oavsiktlig administratörsutelåsning från felaktigt konfigurerade principer för villkorlig åtkomst

**Typ:** Ändrad funktion  
**Servicekategori:** Villkorlig åtkomst  
**Produktkapacitet:** Skydd & identitetssäkerhet

För att förhindra att administratörer av misstag låser sig utanför sina egna klienter genom felaktigt konfigurerade principer för villkorlig åtkomst har vi skapat nya varningar och uppdaterad vägledning i Azure-portalen. Mer information om den nya vägledningen finns [i Vad är tjänstberoenden i Azure Active Directory Villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/service-dependencies).

---

### <a name="improved-end-user-terms-of-use-experiences-on-mobile-devices"></a>Förbättrade användningsupplevelser för slutanvändare på mobila enheter

**Typ:** Ändrad funktion  
**Servicekategori:** Användningsvillkor  
**Produktkapacitet:** Styrelseformer

Vi har uppdaterat våra befintliga användarvillkor för att förbättra hur du granskar och samtycker till användarvillkoren på en mobil enhet. Du kan nu zooma in och ut, gå tillbaka, ladda ned informationen och välja hyperlänkar. Mer information om de uppdaterade användningsvillkoren finns i [Azure Active Directory användningsvillkor](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#what-terms-of-use-looks-like-for-users)funktionen .

---

### <a name="new-azure-ad-activity-logs-download-experience-available"></a>Ny nedladdningsupplevelse för Azure AD-aktivitetsloggar

**Typ:** Ändrad funktion  
**Servicekategori:** Rapportering  
**Produktkapacitet:** Övervakning & rapportering

Du kan nu hämta stora mängder aktivitetsloggar direkt från Azure-portalen. Med den här uppdateringen kan du:

- Ladda ner upp till 250 000 rader.

- Få ett meddelande när hämtningen är klar.

- Anpassa filnamnet.

- Bestäm utdataformatet, antingen JSON eller CSV.

Mer information om den här funktionen finns i [Snabbstart: Hämta en granskningsrapport med Azure-portalen](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-download-audit-report)

---

### <a name="breaking-change-updates-to-condition-evaluation-by-exchange-activesync-eas"></a>Bryta förändring: Uppdateringar av villkorsutvärdering av Exchange ActiveSync (EAS)

**Typ:** Planera för förändring  
**Servicekategori:** Villkorlig åtkomst  
**Produktkapacitet:** Åtkomstkontroll

Vi håller på att uppdatera hur Exchange ActiveSync (EAS) utvärderar följande villkor:

- Användarplats, baserat på land, region eller IP-adress

- Inanmälningsrisk

- Enhetsplattform

Om du tidigare har använt dessa villkor i principerna för villkorlig åtkomst bör du vara medveten om att tillståndsbeteendet kan ändras. Om du till exempel tidigare har använt användarens platsvillkor i en princip kan du hitta den princip som nu hoppas över baserat på användarens plats.

---

## <a name="february-2019"></a>Februari 2019

### <a name="configurable-azure-ad-saml-token-encryption-public-preview"></a>Konfigurerbar Azure AD SAML-tokenkryptering (offentlig förhandsversion) 

**Typ:** Ny funktion  
**Servicekategori:** Företagsappar  
**Produktkapacitet:** Sso

Du kan nu konfigurera alla SAML-appar som stöds för att ta emot krypterade SAML-token. När Azure AD konfigureras och används med en app krypterar den utsända SAML-kontrollen med hjälp av en offentlig nyckel som hämtats från ett certifikat som lagras i Azure AD.

Mer information om hur du konfigurerar din SAML-tokenkryptering finns i [Konfigurera Azure AD SAML-tokenkryptering](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption).

---

### <a name="create-an-access-review-for-groups-or-apps-using-azure-ad-access-reviews"></a>Skapa en åtkomstgranskning för grupper eller appar med Hjälp av Azure AD Access-granskningar

**Typ:** Ny funktion  
**Servicekategori:** Access Recensioner  
**Produktkapacitet:** Styrelseformer

Du kan nu inkludera flera grupper eller appar i en enda Azure AD-åtkomstgranskning för gruppmedlemskap eller apptilldelning. Åtkomstgranskningar med flera grupper eller appar ställs in med samma inställningar och alla inkluderade granskare meddelas samtidigt.

Mer information om hur du skapar en åtkomstgranskning med Hjälp av Azure AD Access Reviews finns i [Skapa en åtkomstgranskning av grupper eller program i Azure AD Access-granskningar](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2019"></a>Nya federerade appar tillgängliga i Azure AD-appgalleriet – februari 2019

**Typ:** Ny funktion  
**Servicekategori:** Företagsappar  
**Produktkapacitet:** Integration från tredje part
 
I februari 2019 har vi lagt till dessa 27 nya appar med federationsstöd i appgalleriet:

[Euromonitor Passport](https://docs.microsoft.com/azure/active-directory/saas-apps/euromonitor-passport-tutorial), [MindTickle](https://docs.microsoft.com/azure/active-directory/saas-apps/mindtickle-tutorial), [FAT FINGER](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7), [AirStack](https://docs.microsoft.com/azure/active-directory/saas-apps/airstack-tutorial), [Oracle Fusion ERP](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-fusion-erp-tutorial), [IDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/idrive-tutorial), [Skyward Qmlativ](https://docs.microsoft.com/azure/active-directory/saas-apps/skyward-qmlativ-tutorial), [Brightidea](https://docs.microsoft.com/azure/active-directory/saas-apps/brightidea-tutorial), [AlertOps](https://docs.microsoft.com/azure/active-directory/saas-apps/alertops-tutorial), [Soloinsight-CloudGate SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial), Permission Click, [Brandfolder](https://docs.microsoft.com/azure/active-directory/saas-apps/brandfolder-tutorial), [StoregateSmartFile](https://docs.microsoft.com/azure/active-directory/saas-apps/smartfile-tutorial), [Pexip](https://docs.microsoft.com/azure/active-directory/saas-apps/pexip-tutorial), [Stormboard](https://docs.microsoft.com/azure/active-directory/saas-apps/stormboard-tutorial), [Seismiska](https://docs.microsoft.com/azure/active-directory/saas-apps/seismic-tutorial), [Dela en dröm](https://www.shareadream.org/how-it-works), [Bugsnag](https://docs.microsoft.com/azure/active-directory/saas-apps/bugsnag-tutorial), [webMethods Integration Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [Kunskap Anywhere LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/knowledge-anywhere-lms-tutorial), [OU Campus](https://docs.microsoft.com/azure/active-directory/saas-apps/ou-campus-tutorial), [Periscope Data](https://docs.microsoft.com/azure/active-directory/saas-apps/periscope-data-tutorial), [Netop Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/netop-portal-tutorial), [smartvid.io](https://docs.microsoft.com/azure/active-directory/saas-apps/smartvid.io-tutorial), [PureCloud av Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial), [ClickUp Produktivitetsplattform](https://docs.microsoft.com/azure/active-directory/saas-apps/clickup-productivity-platform-tutorial)

Mer information om apparna finns i [SaaS-programintegrering med Azure Active Directory](https://aka.ms/appstutorial). Mer information om hur du listar ditt program i Azure AD-appgalleriet finns [i Lista ditt program i Azure Active Directory-programgalleriet](https://aka.ms/azureadapprequest).

---

### <a name="enhanced-combined-mfasspr-registration"></a>Förbättrad kombinerad MFA/SSPR-registrering

**Typ:** Ändrad funktion  
**Servicekategori:** Återställning av lösenord för självbetjäning  
**Produktkapacitet:** Användarautentisering

Som svar på feedback från kunder har vi förbättrat den kombinerade förhandsversionen av MFA/SSPR-registreringen, vilket hjälper användarna att snabbare registrera sin säkerhetsinformation för både MFA och SSPR. 

**Så här aktiverar du den förbättrade upplevelsen för användarnas dag:**

1. Som global administratör eller användaradministratör loggar du in på Azure-portalen och går till **Azure Active Directory > användarinställningar > Hantera inställningar för förhandsversion av åtkomstpanelen**. 

2. I **alternativet Användare som kan använda förhandsgranskningsfunktionerna för att registrera och hantera säkerhetsinformation – uppdatera** alternativet väljer du att aktivera funktionerna för en **vald grupp användare** eller för alla **användare**.

Under de närmaste veckorna kommer vi att ta bort möjligheten att slå på den gamla kombinerade MFA / SSPR registrering förhandsvisning erfarenhet för hyresgäster som inte redan har den påslagen.

**Så här kontrollerar du om kontrollen tas bort för din klientorganisation:**

1. Som global administratör eller användaradministratör loggar du in på Azure-portalen och går till **Azure Active Directory > användarinställningar > Hantera inställningar för förhandsversion av åtkomstpanelen**.  

2. Om de **användare som kan använda förhandsgranskningsfunktionerna för att registrera och hantera säkerhetsinformation** är inställt på **Ingen**tas alternativet bort från din klientorganisation.

Oavsett om du tidigare har aktiverat den gamla kombinerade förhandsversionen av MFA/SSPR-registrering för användare eller inte, kommer den gamla upplevelsen att stängas av vid ett framtida datum. På grund av detta föreslår vi starkt att du flyttar till den nya, förbättrade upplevelsen så snart som möjligt.

Mer information om den förbättrade registreringsupplevelsen finns i [den häftiga förbättringarna av Azure AD-kombinerade MFA- och registreringsupplevelsen för återställning av lösenord](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271).

---

### <a name="updated-policy-management-experience-for-user-flows"></a>Uppdaterad policyhanteringsupplevelse för användarflöden

**Typ:** Ändrad funktion  
**Servicekategori:** B2C - Hantering av konsumentidentiteter  
**Produktkapacitet:** B2B/B2C

Vi har uppdaterat processen för att skapa och hantera principer för användarflöden (tidigare kallade, inbyggda principer) enklare. Den här nya upplevelsen är nu standard för alla dina Azure AD-klienter.

Du kan ge ytterligare feedback och förslag genom att använda ikonerna för leende eller rynka pannan i feedbackområdet **Skicka oss** högst upp på portalskärmen.

Mer information om den nya policyhanteringsupplevelsen finns i [Azure AD B2C nu har JavaScript-anpassning och många fler nya funktioner](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) blogg.

---

### <a name="choose-specific-page-element-versions-provided-by-azure-ad-b2c"></a>Välj specifika sidelementversioner som tillhandahålls av Azure AD B2C

**Typ:** Ny funktion  
**Servicekategori:** B2C - Hantering av konsumentidentiteter  
**Produktkapacitet:** B2B/B2C

Du kan nu välja en specifik version av sidelementen som tillhandahålls av Azure AD B2C. Genom att välja en viss version kan du testa uppdateringarna innan de visas på en sida och du kan få förutsägbart beteende. Dessutom kan du nu välja att tillämpa specifika sidversioner för att tillåta JavaScript-anpassningar. Om du vill aktivera den här funktionen går du till sidan **Egenskaper** i dina användarflöden.

Mer information om hur du väljer specifika versioner av sidelement finns i [Azure AD B2C nu har JavaScript-anpassning och många fler nya funktioner](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) blogg.

---

### <a name="configurable-end-user-password-requirements-for-b2c-ga"></a>Konfigurerbara lösenordskrav för slutanvändare för B2C (GA)

**Typ:** Ny funktion  
**Servicekategori:** B2C - Hantering av konsumentidentiteter  
**Produktkapacitet:** B2B/B2C

Du kan nu ställa in organisationens lösenordskomplexitet för dina slutanvändare, i stället för att behöva använda din ursprungliga Azure AD-lösenordsprincip. Från egenskapsbladet **för** dina användarflöden (tidigare kallade inbyggda principer) kan du välja en lösenordskomplexitet för **Enkel** eller **Stark**eller så kan du skapa en **anpassad** uppsättning krav.

Mer information om konfiguration av lösenordskomplexitetsbehov finns i [Konfigurera komplexitetskrav för lösenord i Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).

---

### <a name="new-default-templates-for-custom-branded-authentication-experiences"></a>Nya standardmallar för anpassade autentiseringsupplevelser

**Typ:** Ny funktion  
**Servicekategori:** B2C - Hantering av konsumentidentiteter  
**Produktkapacitet:** B2B/B2C

Du kan använda våra nya standardmallar, som finns på **sidlayoutbladet** för dina användarflöden (tidigare kallade inbyggda principer), för att skapa en anpassad autentiseringsupplevelse för användarna.

Mer information om hur du använder mallarna finns i [Azure AD B2C har nu JavaScript-anpassning och många fler nya funktioner](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595).

---

## <a name="january-2019"></a>Januari 2019

### <a name="active-directory-b2b-collaboration-using-one-time-passcode-authentication-public-preview"></a>Active Directory B2B-samarbete med engångslösenkodsautentisering (offentlig förhandsversion)

**Typ:** Ny funktion  
**Servicekategori:** B2b  
**Produktkapacitet:** B2B/B2C

Vi har infört engångskodautentisering (OTP) för B2B-gästanvändare som inte kan autentiseras på andra sätt som Azure AD, ett Microsoft-konto (MSA) eller Google federation. Den här nya autentiseringsmetoden innebär att gästanvändare inte behöver skapa ett nytt Microsoft-konto. När en inbjudan löses in eller åtkomst till en delad resurs kan en gästanvändare i stället begära att en tillfällig kod skickas till en e-postadress. Med den här temporära koden kan gästanvändaren fortsätta att logga in.

Mer information finns i [E-postautentisering (förhandskod)](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) och bloggen, [Azure AD gör delning och samarbete sömlöst för alla användare med alla konton](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-makes-sharing-and-collaboration-seamless-for-any-user/ba-p/325949).

### <a name="new-azure-ad-application-proxy-cookie-settings"></a>Nya inställningar för Azure AD-programproxycoy

**Typ:** Ny funktion  
**Servicekategori:** App Proxy  
**Produktkapacitet:** Åtkomstkontroll

Vi har infört tre nya cookie-inställningar som är tillgängliga för dina appar som publiceras via Programproxy:

- **Använd HTTP-Only cookie.** Ställer in **HTTPOnly-flaggan** för din application proxy-åtkomst och sessionscookies. Om du aktiverar den här inställningen kan du använda ytterligare säkerhetsfördelar, till exempel att förhindra kopiering eller ändring av cookies via skript på klientsidan. Vi rekommenderar att du aktiverar den här flaggan (välj **Ja**) för de extra förmånerna.

- **Använd säker cookie.** Ställer in den **säkra** flaggan för din application proxy-åtkomst och sessionscookies. Aktivera den här inställningen ger ytterligare säkerhetsfördelar genom att se till att cookies endast överförs via TLS-säkra kanaler, till exempel HTTPS. Vi rekommenderar att du aktiverar den här flaggan (välj **Ja**) för de extra förmånerna.

- **Använd beständig cookie.** Förhindrar att åtkomstcookies upphör att gälla när webbläsaren stängs. Dessa cookies varar under åtkomsttokens livstid. Cookies återställs dock om utgångstiden har uppnåtts eller om användaren manuellt tar bort cookien. Vi rekommenderar att du behåller standardinställningen **Nej**, bara aktivera inställningen för äldre appar som inte delar cookies mellan processer.

Mer information om de nya cookies finns i [Cookie-inställningar för åtkomst till lokala program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2019"></a>Nya federerade appar tillgängliga i Azure AD-appgalleriet – januari 2019

**Typ:** Ny funktion  
**Servicekategori:** Företagsappar  
**Produktkapacitet:** Integration från tredje part
 
I januari 2019 har vi lagt till dessa 35 nya appar med federationsstöd i appgalleriet:

[Firstbird](https://docs.microsoft.com/azure/active-directory/saas-apps/firstbird-tutorial), [Folloze](https://docs.microsoft.com/azure/active-directory/saas-apps/folloze-tutorial), [Talent Palette](https://docs.microsoft.com/azure/active-directory/saas-apps/talent-palette-tutorial), [Infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloud-suite-tutorial), [Cisco Umbrella](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-umbrella-tutorial), [Zscaler Internet Access Administrator](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-internet-access-administrator-tutorial), Expiration [Reminder](https://docs.microsoft.com/azure/active-directory/saas-apps/expiration-reminder-tutorial), [InstaVR Viewer](https://docs.microsoft.com/azure/active-directory/saas-apps/instavr-viewer-tutorial), [CorpTax](https://docs.microsoft.com/azure/active-directory/saas-apps/corptax-tutorial), [Verb](https://app.verb.net/login), [OpenLattice](https://openlattice.com/agora), [TheOrgWiki](https://www.theorgwiki.com/signup), [Pavaso Digital Close](https://docs.microsoft.com/azure/active-directory/saas-apps/pavaso-digital-close-tutorial), [GoodPractice Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/goodpractice-toolkit-tutorial), [Cloud Service PICCO](https://docs.microsoft.com/azure/active-directory/saas-apps/cloud-service-picco-tutorial), [AuditBoard](https://docs.microsoft.com/azure/active-directory/saas-apps/auditboard-tutorial), [iProva](https://docs.microsoft.com/azure/active-directory/saas-apps/iprova-tutorial), [Användbar](https://docs.microsoft.com/azure/active-directory/saas-apps/workable-tutorial), [CallPlease](https://webapp.callplease.com/create-account/create-account.html), [GTNexus SSO System](https://docs.microsoft.com/azure/active-directory/saas-apps/gtnexus-sso-module-tutorial), [CBRE ServiceInsight](https://docs.microsoft.com/azure/active-directory/saas-apps/cbre-serviceinsight-tutorial), [Deskradar](https://docs.microsoft.com/azure/active-directory/saas-apps/deskradar-tutorial), [Coralogixv](https://docs.microsoft.com/azure/active-directory/saas-apps/coralogix-tutorial), [Signagelive](https://docs.microsoft.com/azure/active-directory/saas-apps/signagelive-tutorial), [ARES for Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/ares-for-enterprise-tutorial), [K2 för Office 365](https://www.k2.com/O365), [Xledger](https://www.xledger.net/), [iDiD Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/idid-manager-tutorial), [HighGear](https://docs.microsoft.com/azure/active-directory/saas-apps/highgear-tutorial), [Visitly](https://docs.microsoft.com/azure/active-directory/saas-apps/visitly-tutorial), [Korn Ferry ALP](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-alp-tutorial), [Acadia](https://docs.microsoft.com/azure/active-directory/saas-apps/acadia-tutorial), [Adoddle cSaas Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/adoddle-csaas-platform-tutorial)<!-- , [CaféX Portal (Meetings)](https://docs.microsoft.com/azure/active-directory/saas-apps/cafexportal-meetings-tutorial), [MazeMap Link](https://docs.microsoft.com/azure/active-directory/saas-apps/mazemaplink-tutorial)-->  

Mer information om apparna finns i [SaaS-programintegrering med Azure Active Directory](https://aka.ms/appstutorial). Mer information om hur du listar ditt program i Azure AD-appgalleriet finns [i Lista ditt program i Azure Active Directory-programgalleriet](https://aka.ms/azureadapprequest).

---

### <a name="new-azure-ad-identity-protection-enhancements-public-preview"></a>Nya förbättringar av Azure AD-identitetsskydd (offentlig förhandsversion)

**Typ:** Ändrad funktion  
**Servicekategori:** Identitetsskydd  
**Produktkapacitet:** Skydd & identitetssäkerhet

Vi är glada att kunna meddela att vi har lagt till följande förbättringar i Azure AD Identity Protection offentliga förhandsversioner erbjudande, inklusive:

- Ett uppdaterat och mer integrerat användargränssnitt

- Ytterligare API:er

- Förbättrad riskbedömning genom maskininlärning

- Produktomfattande anpassning över riskfyllda användare och riskfyllda inloggningar

Mer information om förbättringarna finns i [Vad är Azure Active Directory Identity Protection (uppdateras)?](https://aka.ms/IdentityProtectionDocs) om du vill veta mer och dela med dig av dina tankar genom produktuppmaningar.

---

### <a name="new-app-lock-feature-for-the-microsoft-authenticator-app-on-ios-and-android-devices"></a>Ny applåsfunktion för Microsoft Authenticator-appen på iOS- och Android-enheter

**Typ:** Ny funktion  
**Servicekategori:** Microsoft Authenticator-appen  
**Produktkapacitet:** Skydd & identitetssäkerhet

Om du vill skydda dina engångslösenord, appinformation och appinställningar kan du aktivera applåsfunktionen i Microsoft Authenticator-appen. Om du aktiverar App Lock blir du ombedd att autentisera med din PIN-kod eller biometriska varje gång du öppnar Microsoft Authenticator-appen.

Mer information finns i [vanliga frågor och svar om Appen Microsoft Authenticator](https://docs.microsoft.com/azure/active-directory/user-help/microsoft-authenticator-app-faq).

---

### <a name="enhanced-azure-ad-privileged-identity-management-pim-export-capabilities"></a>Förbättrade pim-exportfunktioner (Azure AD Privileged Identity Management)

**Typ:** Ny funktion  
**Servicekategori:** Privilegierad identitetshantering  
**Produktkapacitet:** Privilegierad identitetshantering

PIM-administratörer (Privileged Identity Management) kan nu exportera alla aktiva och kvalificerade rolltilldelningar för en viss resurs, vilket inkluderar rolltilldelningar för alla underordnade resurser. Tidigare var det svårt för administratörer att få en fullständig lista över rolltilldelningar för en prenumeration och de var tvungna att exportera rolltilldelningar för varje specifik resurs.

Mer information finns i [Visa aktivitets- och granskningshistorik för Azure-resursroller i PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---

## <a name="novemberdecember-2018"></a>November/december 2018

### <a name="users-removed-from-synchronization-scope-no-longer-switch-to-cloud-only-accounts"></a>Användare som tagits bort från synkroniseringsomfånget växlar inte längre till molnkonton

**Typ:** Fast  
**Servicekategori:** Användarhantering  
**Produktkapacitet:** Katalog

>[!Important]
>Vi har hört och förstår din frustration på grund av denna fix. Därför har vi återställt den här ändringen tills vi kan göra korrigeringen enklare för dig att implementera i din organisation.

Vi har åtgärdat ett fel där DirSyncEnabled-flaggan för en användare felaktigt skulle växlas till **False** när AD DS-objektet (Active Directory Domain Services) uteslöts från synkroniseringsomfånget och sedan flyttades till Papperskorgen i Azure AD på följande synkroniseringscykel. Om användaren är utesluten från synkroniseringsomfånget och sedan återställs från Azure AD-papperskorgen förblir användarkontot som synkroniserat från lokalt AD, som förväntat, och kan inte hanteras i molnet eftersom dess auktoritetskälla (SoA) förblir som synkroniserad från lokal AD, som förväntat, och kan inte hanteras i molnet eftersom dess auktoritetskälla (SoA) förblir som synkroniserad från lokala AD, som förväntat, och kan inte hanteras i molnet eftersom dess myndighetskälla (SoA) förblir som synkroniserad från lokala AD, som förväntat, och kan inte hanteras i molnet eftersom dess myndighetskälla (SoA) förblir som synkroniserad från lokala AD, som förväntat, och kan inte hanteras i molnet eftersom dess myndighetskälla (SoA) förblir som synkroniserad från lokala AD, som förväntat, och kan inte hanteras i molnet eftersom dess myndighetskälla (SoA) förblir lokalt AD.

Före den här korrigeringsfilen uppstod ett problem när DirSyncEnabled-flaggan växlades till False. Det gav fel intryck av att dessa konton konverterades till moln-bara objekt och att kontona kunde hanteras i molnet. Kontona behöll dock fortfarande sin SoA som lokala och alla synkroniserade egenskaper (skuggattribut) som kommer från lokala AD. Det här tillståndet orsakade flera problem i Azure AD och andra molnarbetsbelastningar (som Exchange Online) som förväntades behandla dessa konton som synkroniserade från AD men som nu beter sig som molnkonton.

För närvarande är det enda sättet att verkligen konvertera ett synkroniserat från-AD-konto till cloud-only-konto genom att inaktivera DirSync på klientnivå, vilket utlöser en serverd-åtgärd för att överföra SoA. Den här typen av SoA-ändring kräver (men är inte begränsad till) rensning av alla lokala relaterade attribut (till exempel LastDirSyncTime och skuggattribut) och skicka en signal till andra molnarbetsbelastningar för att få sina respektive objekt konverterade till ett molnkonto också .

Den här korrigeringsfilen förhindrar därför direkta uppdateringar på Attributet ImmutableID för en användare som synkroniserats från AD, vilket i vissa fall tidigare har krävts. Avsiktligt är immutableID för ett objekt i Azure AD, som namnet antyder, tänkt att vara oföränderligt. Nya funktioner som implementeras i Azure AD Connect Health- och Azure AD Connect-synkroniseringsklienten är tillgängliga för att hantera sådana scenarier:

- **Storskalig ImmutableID-uppdatering för många användare i en stegvis metod**
  
  Du måste till exempel göra en lång AD DS-migrering mellan skogar. Lösning: Använd Azure AD Connect för att **konfigurera källankare** och kopiera de befintliga ImmutableID-värdena från Azure AD till den lokala AD DS-användarens ms-DS-Konsekvens-Guid-attribut för den nya skogen. Mer information finns i [Använda ms-DS-ConsistencyGuid som sourceAnchor](/azure/active-directory/hybrid/plan-connect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor).

- **Storskaliga ImmutableID-uppdateringar för många användare i ett skott**

  När du till exempel implementerar Azure AD Connect gör du ett misstag, och nu måste du ändra Attributet SourceAnchor. Lösning: Inaktivera DirSync på klientnivå och rensa alla ogiltiga ImmutableID-värden. Mer information finns i [Inaktivera katalogsynkronisering för Office 365](/office365/enterprise/turn-off-directory-synchronization).

- **Matcha om lokalt med en befintlig användare i Azure AD** En användare som har återskapats i AD DS genererar till exempel en dubblett i Azure AD-konto i stället för att matcha om den med ett befintligt Azure AD-konto (övergivet objekt). Lösning: Använd Azure AD Connect Health i Azure-portalen för att mappa om källankaret/oföränderbartID. Mer information finns i [Scenariot Överblivna objekt](/azure/active-directory/hybrid/how-to-connect-health-diagnose-sync-errors#orphaned-object-scenario).

### <a name="breaking-change-updates-to-the-audit-and-sign-in-logs-schema-through-azure-monitor"></a>Breaking Change: Uppdateringar av gransknings- och inloggningsloggschemat via Azure Monitor

**Typ:** Ändrad funktion  
**Servicekategori:** Rapportering  
**Produktkapacitet:** Övervakning & rapportering

Vi publicerar för närvarande både gransknings- och inloggningsloggströmmarna via Azure Monitor, så att du sömlöst kan integrera loggfilerna med dina SIEM-verktyg eller med Log Analytics. Baserat på din feedback och som förberedelse för den här funktionens allmänna tillgänglighetsmeddelande gör vi följande ändringar i vårt schema. Dessa schemaändringar och tillhörande dokumentationsuppdateringar kommer att ske under den första veckan i januari.

#### <a name="new-fields-in-the-audit-schema"></a>Nya fält i granskningsschemat
Vi lägger till ett nytt **operationstypsfält** för att ange vilken typ av åtgärd som utförs på resursen. Lägg till **exempel,** **uppdatera**eller **ta bort**.

#### <a name="changed-fields-in-the-audit-schema"></a>Ändrade fält i granskningsschemat
Följande fält ändras i granskningsschemat:

|Fältnamn|Vad förändrades|Gamla värden|Nya värden|
|----------|------------|----------|----------|
|Kategori|Det här var fältet **Servicenamn.** Det är nu fältet **Granskningskategorier.** **Tjänstnamnet** har bytt namn till fältet **loggedByService.**|<ul><li>Kontoetablering</li><li>Kärnkatalog</li><li>Återställning av lösenord med självbetjäning</li></ul>|<ul><li>Användarhantering</li><li>Grupphantering</li><li>Apphantering</li></ul>|
|targetResources|Inkluderar **TargetResourceType** på den översta nivån.|&nbsp;|<ul><li>Princip</li><li>App</li><li>Användare</li><li>Grupp</li></ul>|
|loggatAvService|Innehåller namnet på tjänsten som genererade granskningsloggen.|Null|<ul><li>Kontoetablering</li><li>Kärnkatalog</li><li>Återställning av lösenord för självbetjäning</li></ul>|
|Resultat|Ger resultatet av granskningsloggarna. Tidigare var detta uppräknat, men vi visar nu det faktiska värdet.|<ul><li>0</li><li>1</li></ul>|<ul><li>Lyckades</li><li>Fel</li></ul>|

#### <a name="changed-fields-in-the-sign-in-schema"></a>Ändrade fält i inloggningsschemat
Följande fält ändras i inloggningsschemat:

|Fältnamn|Vad förändrades|Gamla värden|Nya värden|
|----------|------------|----------|----------|
|appliedConditionalAccessPolicies|Detta var **fältet villkorasyrutas.** Det är nu **fältet appliedConditionalAccessPolicies.**|Ingen ändring|Ingen ändring|
|conditionalAccessStatus|Ger resultatet av principstatus för villkorlig åtkomst vid inloggning. Tidigare var detta uppräknat, men vi visar nu det faktiska värdet.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Lyckades</li><li>Fel</li><li>Används inte</li><li>Disabled</li></ul>|
|appliedConditionalAccessPolicies: resultat|Ger resultatet av den enskilda principstatusen för villkorlig åtkomst vid inloggning. Tidigare var detta uppräknat, men vi visar nu det faktiska värdet.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Lyckades</li><li>Fel</li><li>Används inte</li><li>Disabled</li></ul>|

Mer information om schemat finns [i Tolka Azure AD-granskningsloggar schema i Azure Monitor (förhandsversion)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema)

---

### <a name="identity-protection-improvements-to-the-supervised-machine-learning-model-and-the-risk-score-engine"></a>Förbättringar av identitetsskydd till den övervakade maskininlärningsmodellen och riskpoängmotorn

**Typ:** Ändrad funktion  
**Servicekategori:** Identitetsskydd  
**Produktkapacitet:** Riskpoäng

Förbättringar av den identitetsskyddsrelaterade användar- och inloggningsriskbedömningsmotorn kan bidra till att förbättra användarrisknoggrannheten och täckningen. Administratörer kan märka att användarrisknivån inte längre är direkt kopplad till risknivån för specifika identifieringar och att antalet riskfyllda inloggningshändelser ökar.

Riskidentifieringar utvärderas nu av den övervakade maskininlärningsmodellen, som beräknar användarrisken med hjälp av ytterligare funktioner i användarens inloggningar och ett mönster av identifieringar. Baserat på den här modellen kan administratören hitta användare med höga riskpoäng, även om identifieringar som är associerade med den användaren är av låg eller medelhög risk. 

---

### <a name="administrators-can-reset-their-own-password-using-the-microsoft-authenticator-app-public-preview"></a>Administratörer kan återställa sitt eget lösenord med hjälp av Microsoft Authenticator-appen (Offentlig förhandsversion)

**Typ:** Ändrad funktion  
**Servicekategori:** Återställning av lösenord för självbetjäning  
**Produktkapacitet:** Användarautentisering

Azure AD-administratörer kan nu återställa sitt eget lösenord med hjälp av Microsoft Authenticator-appmeddelanden eller en kod från en mobil autentiseringsapp eller maskinvarutoken. För att återställa sitt eget lösenord kan administratörer nu använda två av följande metoder:

- Meddelande om Microsoft Authenticator-appen

- Annan mobil autentiserarapp / Maskinvarutokenkod

- E-post

- Telefonsamtal

- Textmeddelande

Mer information om hur du använder Microsoft Authenticator-appen för att återställa lösenord finns i [Azure AD-återställning av lösenord för självbetjäning – mobilapp och SSPR (förhandsversion)](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#mobile-app-and-sspr)

---

### <a name="new-azure-ad-cloud-device-administrator-role-public-preview"></a>Ny azure AD-administratörsroll för molnenheter (offentlig förhandsversion)

**Typ:** Ny funktion  
**Servicekategori:** Registrering och hantering av enheter  
**Produktkapacitet:** Åtkomstkontroll

Administratörer kan tilldela användare till den nya rollen Cloud Device Administrator för att utföra administratörsuppgifter för molnenheter. Användare som tilldelats rollen Administratörer för molnenheter kan aktivera, inaktivera och ta bort enheter i Azure AD, tillsammans med att kunna läsa Windows 10 BitLocker-nycklar (om sådana finns) i Azure-portalen.

Mer information om roller och behörigheter finns [i Tilldela administratörsroller i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

---

### <a name="manage-your-devices-using-the-new-activity-timestamp-in-azure-ad-public-preview"></a>Hantera dina enheter med den nya aktivitetstidsstämpeln i Azure AD (offentlig förhandsversion)

**Typ:** Ny funktion  
**Servicekategori:** Registrering och hantering av enheter  
**Produktkapacitet:** Hantering av enhetens livscykel

Vi inser att med tiden måste du uppdatera och dra tillbaka dina organisationers enheter i Azure AD, för att undvika att ha inaktuella enheter i din miljö. För att hjälpa till med den här processen uppdaterar Azure AD nu dina enheter med en ny aktivitetstidsstämpel som hjälper dig att hantera enhetens livscykel.

Mer information om hur du hämtar och använder den här tidsstämpeln finns i [Så här: Hantera de inaktuella enheterna i Azure AD](https://docs.microsoft.com/azure/active-directory/devices/manage-stale-devices)

---

### <a name="administrators-can-require-users-to-accept-a-terms-of-use-on-each-device"></a>Administratörer kan kräva att användare godkänner ett användningsvillkor på varje enhet

**Typ:** Ny funktion  
**Servicekategori:** Användningsvillkor  
**Produktkapacitet:** Styrelseformer
 
Administratörer kan nu aktivera **Alternativet Kräv användare att samtycka till varje enhetsalternativ** för att kräva att användarna accepterar dina användarvillkor på varje enhet de använder på din klientorganisation.

Mer information finns i [avsnittet Användning per enhet i azure Active Directory-användningsvillkorsfunktionen](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#per-device-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-a-recurring-schedule"></a>Administratörer kan konfigurera ett användningsvillkor som ska upphöra att gälla baserat på ett återkommande schema

**Typ:** Ny funktion  
**Servicekategori:** Användningsvillkor  
**Produktkapacitet:** Styrelseformer
 

Administratörer kan nu aktivera alternativet Upphör att gälla för att få **användningsvillkoren** att upphöra för alla användare baserat på ditt angivna återkommande schema. Schemat kan vara årligen, vartannat år, kvartal eller månad. När användarvillkoren har upphört att gälla måste användarna acceptera det igen.

Mer information finns i [avsnittet Lägg till användningsvillkor i azure Active Directory-användningsvillkorsfunktionen](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-each-users-schedule"></a>Administratörer kan konfigurera ett användningsvillkor som ska upphöra att gälla baserat på varje användares schema

**Typ:** Ny funktion  
**Servicekategori:** Användningsvillkor  
**Produktkapacitet:** Styrelseformer

Administratörer kan nu ange en varaktighet som användaren måste acceptera ett användningsvillkor. Administratörer kan till exempel ange att användare måste acceptera ett nytt användningsvillkor var 90:e dag.

Mer information finns i [avsnittet Lägg till användningsvillkor i azure Active Directory-användningsvillkorsfunktionen](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use).
 
---

### <a name="new-azure-ad-privileged-identity-management-pim-emails-for-azure-active-directory-roles"></a>Nya AZURE AD-privilegierade identitetshanteringsmeddelanden (PIM) för Azure Active Directory-roller

**Typ:** Ny funktion  
**Servicekategori:** Privilegierad identitetshantering  
**Produktkapacitet:** Privilegierad identitetshantering
 
Kunder som använder Azure AD Privileged Identity Management (PIM) kan nu få ett sammanfattat veckomeddelande, inklusive följande information för de senaste sju dagarna:

- Översikt över de högsta kvalificerade och permanenta rolltilldelningarna

- Antal användare som aktiverar roller

- Antal användare som tilldelats roller i PIM

- Antal användare som tilldelats roller utanför PIM

- Antal användare "permanent" i PIM

Mer information om PIM och tillgängliga e-postmeddelanden finns [i E-postmeddelanden i PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-email-notifications).

---

### <a name="group-based-licensing-is-now-generally-available"></a>Koncernbaserad licensiering är nu allmänt tillgänglig

**Typ:** Ändrad funktion  
**Servicekategori:** Andra  
**Produktkapacitet:** Katalog

Gruppbaserad licensiering är inte offentlig förhandsgranskning och är nu allmänt tillgänglig. Som en del av den här allmänna versionen har vi gjort den här funktionen mer skalbar och har lagt till möjligheten att bearbeta gruppbaserade licenstilldelningar för en enskild användare och möjligheten att använda gruppbaserad licensiering med Office 365 E3/A3-licenser.

Mer information om gruppbaserad licensiering finns [i Vad är gruppbaserad licensiering i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2018"></a>Nya federerade appar tillgängliga i Azure AD-appgalleriet – november 2018

**Typ:** Ny funktion  
**Servicekategori:** Företagsappar  
**Produktkapacitet:** Integration från tredje part
 
I november 2018 har vi lagt till dessa 26 nya appar med federationsstöd i appgalleriet:

[CoreStack](https://cloud.corestack.io/site/login), [HubSpot](https://docs.microsoft.com/azure/active-directory/saas-apps/HubSpot-tutorial), [GetThere](https://docs.microsoft.com/azure/active-directory/saas-apps/getthere-tutorial), [Gra-Pe](https://docs.microsoft.com/azure/active-directory/saas-apps/grape-tutorial), [eHour](https://getehour.com/try-now), [Consent2Go](https://docs.microsoft.com/azure/active-directory/saas-apps/Consent2Go-tutorial), [Appinux](https://docs.microsoft.com/azure/active-directory/saas-apps/appinux-tutorial), [DriveDollar](https://azuremarketplace.microsoft.com/marketplace/apps/savitas.drivedollar-azuread?tab=Overview), [Useall](https://docs.microsoft.com/azure/active-directory/saas-apps/useall-tutorial), [Infinite Campus](https://docs.microsoft.com/azure/active-directory/saas-apps/infinitecampus-tutorial), [Alaya](https://alayagood.com/en/demo/), [HeyBuddy](https://docs.microsoft.com/azure/active-directory/saas-apps/heybuddy-tutorial), [Wrike SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/wrike-tutorial), [Drift](https://docs.microsoft.com/azure/active-directory/saas-apps/drift-tutorial), [Zenegy for Business Central 365](https://accounting.zenegy.com/), [Everbridge Member Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/everbridge-tutorial), [IDEO](https://profile.ideo.com/users/sign_up), [Ivanti Service Manager (ISM)](https://docs.microsoft.com/azure/active-directory/saas-apps/ivanti-service-manager-tutorial), [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-tutorial), [Allbound SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/allbound-sso-tutorial), [Plex Apps - Classic Test ](https://test.plexonline.com/signon), [Plex Apps – Classic](https://www.plexonline.com/signon), [Plex Apps – UX-test](https://test.cloud.plex.com/sso), [Plex-appar – UX,](https://cloud.plex.com/sso) [Plex-appar – IAM,](https://accounts.plex.com/) [CRAFTS – Barnomsorgsregister, Närvaro, & finansiellt spårningssystem](https://getcrafts.ca/craftsregistration) 

Mer information om apparna finns i [SaaS-programintegrering med Azure Active Directory](https://aka.ms/appstutorial). Mer information om hur du listar ditt program i Azure AD-appgalleriet finns [i Lista ditt program i Azure Active Directory-programgalleriet](https://aka.ms/azureadapprequest).

---

## <a name="october-2018"></a>Oktober 2018

### <a name="azure-ad-logs-now-work-with-azure-log-analytics-public-preview"></a>Azure AD-loggar fungerar nu med Azure Log Analytics (Offentlig förhandsversion)

**Typ:** Ny funktion  
**Servicekategori:** Rapportering  
**Produktkapacitet:** Övervakning & rapportering

Vi är glada att kunna meddela att du nu kan vidarebefordra dina Azure AD-loggar till Azure Log Analytics! Den här funktionen som efterfrågas på bästa sätt ger dig ännu bättre åtkomst till analyser för ditt företag, din verksamhet och säkerhet, samt ett sätt att övervaka infrastrukturen. Mer information finns [i Azure Active Directory Activity loggarna i Azure Log Analytics nu tillgänglig](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-Activity-logs-in-Azure-Log-Analytics-now/ba-p/274843) blogg.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2018"></a>Nya federerade appar tillgängliga i Azure AD-appgalleriet – oktober 2018

**Typ:** Ny funktion  
**Servicekategori:** Företagsappar  
**Produktkapacitet:** Integration från tredje part

I oktober 2018 har vi lagt till dessa 14 nya appar med federationsstöd i appgalleriet:

[Min Award Points](https://docs.microsoft.com/azure/active-directory/saas-apps/myawardpoints-tutorial), [Vibe HCM](https://docs.microsoft.com/azure/active-directory/saas-apps/vibehcm-tutorial), ambyint, [MyWorkDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/myworkdrive-tutorial), [BorrowBox](https://docs.microsoft.com/azure/active-directory/saas-apps/borrowbox-tutorial), Dialpad, [ON24 Virtuell miljö](https://docs.microsoft.com/azure/active-directory/saas-apps/on24-tutorial), [RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-tutorial), [Zscaler Tre](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-three-tutorial), [Phraseanet](https://docs.microsoft.com/azure/active-directory/saas-apps/phraseanet-tutorial), [Appraisd](https://docs.microsoft.com/azure/active-directory/saas-apps/appraisd-tutorial), [Workspot Control](https://docs.microsoft.com/azure/active-directory/saas-apps/workspotcontrol-tutorial), [Shuccho Navi](https://docs.microsoft.com/azure/active-directory/saas-apps/shucchonavi-tutorial), [Glassfrog](https://docs.microsoft.com/azure/active-directory/saas-apps/glassfrog-tutorial)

Mer information om apparna finns i [SaaS-programintegrering med Azure Active Directory](https://aka.ms/appstutorial). Mer information om hur du listar ditt program i Azure AD-appgalleriet finns [i Lista ditt program i Azure Active Directory-programgalleriet](https://aka.ms/azureadapprequest).

---

### <a name="azure-ad-domain-services-email-notifications"></a>E-postmeddelanden om Azure AD-domäntjänster

**Typ:** Ny funktion  
**Servicekategori:** Azure AD-domäntjänster  
**Produktkapacitet:** Azure AD-domäntjänster

Azure AD Domain Services tillhandahåller aviseringar på Azure-portalen om felkonfigurationer eller problem med din hanterade domän. Dessa aviseringar innehåller steg-för-steg-stödlinjer så att du kan försöka åtgärda problemen utan att behöva kontakta supporten.

Från och med oktober kan du anpassa aviseringsinställningarna för den hanterade domänen så att ett e-postmeddelande skickas till en utsedd grupp personer när nya aviseringar inträffar, vilket eliminerar behovet av att ständigt kontrollera portalen efter uppdateringar.

Mer information finns [i Meddelandeinställningar i Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-notifications).

---

### <a name="azure-ad-portal-supports-using-the-forcedelete-domain-api-to-delete-custom-domains"></a>Azure AD-portalen stöder användning av ForceDelete-domän-API:et för att ta bort anpassade domäner 

**Typ:** Ändrad funktion  
**Servicekategori:** Kataloghantering  
**Produktkapacitet:** Katalog

Vi är glada att kunna meddela att du nu kan använda ForceDelete-domän-API:et för att ta bort dina egna domännamn genom att asynkront byta namn på referenser, till exempel användare, grupper och appar från ditt eget domännamn (contoso.com) tillbaka till det ursprungliga standarddomännamnet ( contoso.onmicrosoft.com).

Den här ändringen hjälper dig att snabbare ta bort dina egna domännamn om din organisation inte längre använder namnet, eller om du behöver använda domännamnet med en annan Azure AD.

Mer information finns i [Ta bort ett eget domännamn](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-manage#delete-a-custom-domain-name).

---

## <a name="september-2018"></a>September 2018
 
### <a name="updated-administrator-role-permissions-for-dynamic-groups"></a>Uppdaterade administratörsrollbehörigheter för dynamiska grupper

**Typ:** Fast  
**Servicekategori:** Gruppledning  
**Produktkapacitet:** Samarbete

Vi har åtgärdat ett problem så att specifika administratörsroller nu kan skapa och uppdatera dynamiska medlemskapsregler, utan att behöva vara ägare till gruppen.

Rollerna är:

- Global administratör

- Intune-administratör

- Användaradministratör

Mer information finns i [Skapa en dynamisk grupp och kontrollera status](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule)

---

### <a name="simplified-single-sign-on-sso-configuration-settings-for-some-third-party-apps"></a>Förenklade konfigurationsinställningar för enkel inloggning (SSO) för vissa appar från tredje part

**Typ:** Ny funktion  
**Servicekategori:** Företagsappar  
**Produktkapacitet:** Sso

Vi inser att det kan vara svårt att konfigurera SaaS-appar (Single Sign-On) för Programvara som en tjänst (SaaS) på grund av den unika karaktären hos varje appkonfiguration. Vi har skapat en förenklad konfigurationsupplevelse för att automatiskt fylla i SSO-konfigurationsinställningarna för följande SaaS-appar från tredje part:

- Zendesk

- ArcGis Online

- Jamf Pro

Om du vill börja använda den här upplevelsen med ett klick går du till konfigurationssidan för **Azure portal** > **SSO** för appen. Mer information finns i [SaaS-programintegrering med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)

---

### <a name="azure-active-directory---where-is-your-data-located-page"></a>Azure Active Directory - Var finns dina data? Sida

**Typ:** Ny funktion  
**Servicekategori:** Andra  
**Produktkapacitet:** GoLocal (på)GoLocal )

Välj företagets region från **Azure Active Directory - Var finns din datasida** för att visa vilken Azure datacenter som rymmer dina Azure AD-data i vila för alla Azure AD-tjänster. Du kan filtrera informationen efter specifika Azure AD-tjänster för företagets region.

Information om hur du kommer åt den här funktionen och mer information finns i [Azure Active Directory - Var finns dina data](https://aka.ms/AADDataMap).

---

### <a name="new-deployment-plan-available-for-the-my-apps-access-panel"></a>Nytt distributionsplan tillgängligt för panelen Åtkomst till Mina appar

**Typ:** Ny funktion  
**Servicekategori:** Mina appar  
**Produktkapacitet:** Sso

Kolla in den nya distributionsplanen som är tillgänglighttps://aka.ms/deploymentplans)för panelen Åtkomst till Mina appar ( .
Panelen Åtkomst till mina appar ger användarna en enda plats där de kan hitta och komma åt sina appar. Den här portalen ger också användare självbetjäningsmöjligheter, till exempel att begära åtkomst till appar och grupper eller hantera åtkomst till dessa resurser för andras räkning.

Mer information finns i [Vad är portalen Mina appar?](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)

---

### <a name="new-troubleshooting-and-support-tab-on-the-sign-ins-logs-page-of-the-azure-portal"></a>Fliken Ny felsökning och support på sidan Inloggningsloggar i Azure-portalen

**Typ:** Ny funktion  
**Servicekategori:** Rapportering  
**Produktkapacitet:** Övervakning & rapportering

Den nya fliken **Felsökning och support** på **inloggningssidan i Azure-portalen** är avsedd att hjälpa administratörer och supporttekniker att felsöka problem relaterade till Azure AD-inloggningar. Den här nya fliken innehåller felkod, felmeddelande och reparationsrekommendationer (om några) för att lösa problemet. Om du inte kan lösa problemet ger vi dig också ett nytt sätt att skapa en supportbiljett med hjälp av upplevelsen **Kopiera till Urklipp,** som fyller i fälten **Request ID** och **Date (UTC)** för loggfilen i supportbiljetten.  

![Inloggningsloggar som visar den nya fliken](media/whats-new/troubleshooting-and-support.png)

---

### <a name="enhanced-support-for-custom-extension-properties-used-to-create-dynamic-membership-rules"></a>Förbättrat stöd för anpassade tilläggsegenskaper som används för att skapa dynamiska medlemskapsregler

**Typ:** Ändrad funktion  
**Servicekategori:** Gruppledning  
**Produktkapacitet:** Samarbete

Med den här uppdateringen kan du nu klicka på länken **Hämta anpassade tilläggsegenskaper** från den dynamiska användargruppsregelverktyget, ange ditt unika app-ID och få en fullständig lista över anpassade tilläggsegenskaper som ska användas när du skapar en dynamisk medlemskapsregel för användare. Den här listan kan också uppdateras för att få nya anpassade tilläggsegenskaper för den appen.

Mer information om hur du använder anpassade tilläggsegenskaper för dynamiska medlemskapsregler finns i [Tilläggsegenskaper och anpassade tilläggsegenskaper](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership#extension-properties-and-custom-extension-properties)

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nya godkända klientappar för Azure AD-appbaserad villkorlig åtkomst

**Typ:** Planera för förändring  
**Servicekategori:** Villkorlig åtkomst  
**Produktkapacitet:** Identitetssäkerhet och identitetsskydd

Följande appar finns med i listan över [godkända klientappar:](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)

- Microsoft To-Do

- Microsoft Stream

Mer information finns i:

- [Azure AD-appbaserad villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="new-support-for-self-service-password-reset-from-the-windows-7881-lock-screen"></a>Nytt stöd för återställning av lösenord för självbetjäning från låsskärmen i Windows 7/8/8.1

**Typ:** Ny funktion  
**Servicekategori:** SSPR (SSPR)  
**Produktkapacitet:** Användarautentisering

När du har konfigurerat den här nya funktionen visas en länk för att återställa lösenordet från **låsskärmen** på en enhet som kör Windows 7, Windows 8 eller Windows 8.1. Genom att klicka på den länken guidas användaren genom samma lösenordsåterställningsflöde som via webbläsaren.

Mer information finns i [Så här aktiverar du återställning av lösenord från Windows 7, 8 och 8.1](https://aka.ms/ssprforwindows78)

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Ändringsmeddelande: Auktoriseringskoder kommer inte längre att vara tillgängliga för återanvändning 

**Typ:** Planera för förändring  
**Servicekategori:** Autentiseringar (inloggningar)  
**Produktkapacitet:** Användarautentisering

Från och med den 15 november 2018 slutar Azure AD att acceptera tidigare använda autentiseringskoder för appar. Den här säkerhetsändringen hjälper till att anpassa Azure AD till OAuth-specifikationen och kommer att tillämpas på både v1- och v2-slutpunkterna.

Om appen återanvänder auktoriseringskoder för att hämta token för flera resurser rekommenderar vi att du använder koden för att hämta en uppdateringstoken och sedan använda den uppdateringstoken för att hämta ytterligare token för andra resurser. Auktoriseringskoder kan bara användas en gång, men uppdateringstoken kan användas flera gånger över flera resurser. En app som försöker återanvända en autentiseringskod under OAuth-kodflödet får ett invalid_grant fel.

För de här och andra protokollrelaterade ändringar finns [i den fullständiga listan över vad som är nytt för autentisering](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2018"></a>Nya federerade appar tillgängliga i Azure AD-appgalleriet – september 2018

**Typ:** Ny funktion  
**Servicekategori:** Företagsappar  
**Produktkapacitet:** Integration från tredje part
 
I september 2018 har vi lagt till dessa 16 nya appar med federationsstöd i appgalleriet:

[Uberflip](https://docs.microsoft.com/azure/active-directory/saas-apps/uberflip-tutorial), [Comeet Rekrytering Software](https://docs.microsoft.com/azure/active-directory/saas-apps/comeetrecruitingsoftware-tutorial), [Workteam](https://docs.microsoft.com/azure/active-directory/saas-apps/workteam-tutorial), [ArcGIS Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/arcgisenterprise-tutorial), [Nuclino](https://docs.microsoft.com/azure/active-directory/saas-apps/nuclino-tutorial), [JDA Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/jdacloud-tutorial), [Snowflake](https://docs.microsoft.com/azure/active-directory/saas-apps/snowflake-tutorial), NavigoCloud, [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-tutorial), join.me, [ZephyrSSO](https://docs.microsoft.com/azure/active-directory/saas-apps/zephyrsso-tutorial), [Silverback](https://docs.microsoft.com/azure/active-directory/saas-apps/silverback-tutorial), Riverbed Xirrus EasyPass, [Rackspace SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/rackspacesso-tutorial), Enlyft SSO för Azure, SurveyMonkey, [Convene](https://docs.microsoft.com/azure/active-directory/saas-apps/convene-tutorial), [dmarcian](https://docs.microsoft.com/azure/active-directory/saas-apps/dmarcian-tutorial)

Mer information om apparna finns i [SaaS-programintegrering med Azure Active Directory](https://aka.ms/appstutorial). Mer information om hur du listar ditt program i Azure AD-appgalleriet finns [i Lista ditt program i Azure Active Directory-programgalleriet](https://aka.ms/azureadapprequest).

---

### <a name="support-for-additional-claims-transformations-methods"></a>Stöd för ytterligare metoder för skadeomvandling

**Typ:** Ny funktion  
**Servicekategori:** Företagsappar  
**Produktkapacitet:** Sso

Vi har infört nya anspråkstransformationsmetoder, ToLower() och ToUpper(), som kan tillämpas på SAML-token från sidan SAML-baserad **konfiguration av enkel inloggning.**

Mer information finns [i Så här anpassar du anspråk som utfärdats i SAML-token för företagsprogram i Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

---

### <a name="updated-saml-based-app-configuration-ui-preview"></a>Uppdaterat SAML-baserat appkonfigurationsgränssnitt (förhandsversion)

**Typ:** Ändrad funktion  
**Servicekategori:** Företagsappar  
**Produktkapacitet:** Sso

Som en del av vårt uppdaterade SAML-baserade appkonfigurationsgränssnitt får du:

- En uppdaterad genomgångsupplevelse för att konfigurera dina SAML-baserade appar.

- Mer synlighet om vad som saknas eller är felaktigt i konfigurationen.

- Möjligheten att lägga till flera e-postadresser för meddelande om förfallodatum.

- Nya anspråkstransformationsmetoder, ToLower() och ToUpper() med mera.

- Ett sätt att ladda upp ditt eget tokensigneringscertifikat för dina företagsappar.

- Ett sätt att ange NameID-format för SAML-appar och ett sätt att ange NameID-värdet som katalogtillägg.

Om du vill aktivera den här uppdaterade vyn klickar du på länken **Prova vår nya upplevelse** högst upp på sidan Enkel **inloggning.** Mer information finns i [Självstudiekurs: Konfigurera SAML-baserad enkel inloggning för ett program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-non-gallery-applications).

---

## <a name="august-2018"></a>Augusti 2018

### <a name="changes-to-azure-active-directory-ip-address-ranges"></a>Ändringar i Azure Active Directory IP-adressintervall

**Typ:** Planera för förändring  
**Servicekategori:** Andra  
**Produktkapacitet:** Plattform

Vi introducerar större IP-intervall till Azure AD, vilket innebär att om du har konfigurerat Azure AD IP-adressintervall för brandväggar, routrar eller nätverkssäkerhetsgrupper måste du uppdatera dem. Vi gör den här uppdateringen så att du inte behöver ändra ip-intervallkonfigurationerna för brandvägg, router eller nätverkssäkerhetsgrupper igen när Azure AD lägger till nya slutpunkter. 

Nätverkstrafiken går till dessa nya intervall under de kommande två månaderna. Om du vill fortsätta med oavbruten tjänst måste du lägga till dessa uppdaterade värden i dina IP-adresser före den 10 september 2018:

- 20.190.128.0/18 

- 40.126.0.0/18 

Vi rekommenderar starkt att du inte tar bort de gamla IP-adressintervallen förrän all nätverkstrafik har flyttats till de nya intervallen. Uppdateringar om flytten och hur du tar reda på när du kan ta bort de gamla intervallen finns i [Url:er och IP-adressintervall för Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Ändringsmeddelande: Auktoriseringskoder kommer inte längre att vara tillgängliga för återanvändning 

**Typ:** Planera för förändring  
**Servicekategori:** Autentiseringar (inloggningar)  
**Produktkapacitet:** Användarautentisering

Från och med den 15 november 2018 slutar Azure AD att acceptera tidigare använda autentiseringskoder för appar. Den här säkerhetsändringen hjälper till att anpassa Azure AD till OAuth-specifikationen och kommer att tillämpas på både v1- och v2-slutpunkterna.

Om appen återanvänder auktoriseringskoder för att hämta token för flera resurser rekommenderar vi att du använder koden för att hämta en uppdateringstoken och sedan använda den uppdateringstoken för att hämta ytterligare token för andra resurser. Auktoriseringskoder kan bara användas en gång, men uppdateringstoken kan användas flera gånger över flera resurser. En app som försöker återanvända en autentiseringskod under OAuth-kodflödet får ett invalid_grant fel.

För de här och andra protokollrelaterade ändringar finns [i den fullständiga listan över vad som är nytt för autentisering](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).
 
---

### <a name="converged-security-info-management-for-self-service-password-sspr-and-multi-factor-authentication-mfa"></a>Hantering av konvergerad säkerhetsinformation för lösenord för självbetjäning (SSPR) och MFA (MultiFaktor Authentication)

**Typ:** Ny funktion  
**Servicekategori:** SSPR (SSPR)  
**Produktkapacitet:** Användarautentisering
 
Den här nya funktionen hjälper personer att hantera sin säkerhetsinformation (till exempel telefonnummer, mobilapp och så vidare) för SSPR och MFA på en enda plats och upplevelse. jämfört med tidigare, där det gjordes på två olika platser.

Denna konvergerade upplevelse fungerar också för personer som använder antingen SSPR eller MFA. Om din organisation inte tillämpar MFA- eller SSPR-registrering kan personer dessutom fortfarande registrera alla MFA- eller SSPR-säkerhetsinformationsmetoder som tillåts av din organisation från Portalen Mina appar.

Detta är en opt-in offentlig förhandsvisning. Administratörer kan aktivera den nya upplevelsen (om så önskas) för en vald grupp eller för alla användare i en klientorganisation. Mer information om den konvergerade upplevelsen finns i [bloggen Konvergerad upplevelse](https://cloudblogs.microsoft.com/enterprisemobility/2018/08/06/mfa-and-sspr-updates-now-in-public-preview/)

---

### <a name="new-http-only-cookies-setting-in-azure-ad-application-proxy-apps"></a>Ny http-only-cookies-inställning i Azure AD Application-proxyappar

**Typ:** Ny funktion  
**Servicekategori:** App Proxy  
**Produktkapacitet:** Åtkomstkontroll

Det finns en ny inställning som heter **HTTP-Only Cookies** i dina Programproxy-appar. Den här inställningen ger extra säkerhet genom att inkludera HTTPOnly-flaggan i HTTP-svarshuvudet för både application proxy-åtkomst och sessionscookies, stoppa åtkomsten till cookien från ett klientskript och ytterligare förhindra åtgärder som kopiering eller ändra cookien. Även om den här flaggan inte har använts tidigare har dina cookies alltid krypterats och överförts med hjälp av en TLS-anslutning för att skydda mot felaktiga ändringar.

Den här inställningen är inte kompatibel med appar som använder ActiveX-kontroller, till exempel Fjärrskrivbord. Om du är i den här situationen rekommenderar vi att du inaktiverar den här inställningen.

Mer information om inställningen HTTP-Only Cookies finns i [Publicera program med Azure AD Application Proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-publish-azure-portal).

---

### <a name="privileged-identity-management-pim-for-azure-resources-supports-management-group-resource-types"></a>Pim (Privileged Identity Management) för Azure-resurser stöder resurstyper för hanteringsgrupp

**Typ:** Ny funktion  
**Servicekategori:** Privilegierad identitetshantering  
**Produktkapacitet:** Privilegierad identitetshantering
 
Just-In-Time aktivering och tilldelningsinställningar kan nu tillämpas på resurstyper för hanteringsgrupp, precis som du redan gör för prenumerationer, resursgrupper och resurser (till exempel virtuella datorer, App Services med mera). Dessutom kan alla med en roll som ger administratörsbehörighet för en hanteringsgrupp identifiera och hantera den resursen i PIM.

Mer information om PIM- och Azure-resurser finns i [Identifiera och hantera Azure-resurser med hjälp av Privilegierad identitetshantering](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-resource-roles-discover-resources)
 
---

### <a name="application-access-preview-provides-faster-access-to-the-azure-ad-portal"></a>Programåtkomst (förhandsversion) ger snabbare åtkomst till Azure AD-portalen

**Typ:** Ny funktion  
**Servicekategori:** Privilegierad identitetshantering  
**Produktkapacitet:** Privilegierad identitetshantering
 
Idag, när du aktiverar en roll med PIM, kan det ta över 10 minuter för behörigheterna att börja gälla. Om du väljer att använda programåtkomst, som för närvarande är i offentlig förhandsversion, kan administratörer komma åt Azure AD-portalen så snart aktiveringsbegäran har slutförts.

För närvarande stöder programåtkomst endast Azure AD-portalupplevelsen och Azure-resurser. Mer information om PIM- och programåtkomst finns i [Vad är Azure AD Privileged Identity Management?](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure)
 
---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2018"></a>Nya federerade appar tillgängliga i Azure AD-appgalleriet – augusti 2018

**Typ:** Ny funktion  
**Servicekategori:** Företagsappar  
**Produktkapacitet:** Integration från tredje part
 
I augusti 2018 har vi lagt till dessa 16 nya appar med federationsstöd i appgalleriet:

[Hornbill](https://docs.microsoft.com/azure/active-directory/saas-apps/hornbill-tutorial), [Bridgeline Obunden](https://docs.microsoft.com/azure/active-directory/saas-apps/bridgelineunbound-tutorial), [Sauce Labs - Mobil och webbtestning](https://docs.microsoft.com/azure/active-directory/saas-apps/saucelabs-mobileandwebtesting-tutorial), [Meta Networks Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/metanetworksconnector-tutorial), Way We [Do](https://docs.microsoft.com/azure/active-directory/saas-apps/waywedo-tutorial), [Spotinst](https://docs.microsoft.com/azure/active-directory/saas-apps/spotinst-tutorial), [ProMaster (av Inlogik)](https://docs.microsoft.com/azure/active-directory/saas-apps/promaster-tutorial), SchoolBooking, [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-tutorial), [Dossier](https://docs.microsoft.com/azure/active-directory/saas-apps/DOSSIER-tutorial), [N2F - Utgiftsrapporter](https://docs.microsoft.com/azure/active-directory/saas-apps/n2f-expensereports-tutorial), [Comm100 Live Chat](https://docs.microsoft.com/azure/active-directory/saas-apps/comm100livechat-tutorial), [SafeConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/safeconnect-tutorial), [ZenQMS](https://docs.microsoft.com/azure/active-directory/saas-apps/zenqms-tutorial), [eLuminate](https://docs.microsoft.com/azure/active-directory/saas-apps/eluminate-tutorial), [Dovetale](https://docs.microsoft.com/azure/active-directory/saas-apps/dovetale-tutorial).

Mer information om apparna finns i [SaaS-programintegrering med Azure Active Directory](https://aka.ms/appstutorial). Mer information om hur du listar ditt program i Azure AD-appgalleriet finns [i Lista ditt program i Azure Active Directory-programgalleriet](https://aka.ms/azureadapprequest).

---

### <a name="native-tableau-support-is-now-available-in-azure-ad-application-proxy"></a>Native Tableau-stöd är nu tillgängligt i Azure AD Application Proxy

**Typ:** Ändrad funktion  
**Servicekategori:** App Proxy  
**Produktkapacitet:** Åtkomstkontroll

Med vår uppdatering från OpenID Connect till OAuth 2.0 Code Grant-protokollet för vårt protokoll för förhandsautentisering behöver du inte längre göra någon ytterligare konfiguration för att använda Tableau med Application Proxy. Den här protokolländringen hjälper också Application Proxy att bättre stödja modernare appar genom att bara använda HTTP-omdirigeringar, som ofta stöds i JavaScript- och HTML-taggar.

Mer information om vårt inbyggda stöd för Tableau finns i [Azure AD Application Proxy nu med inbyggt Tableau-stöd](https://blogs.technet.microsoft.com/applicationproxyblog/2018/08/14/azure-ad-application-proxy-now-with-native-tableau-support).

---

### <a name="new-support-to-add-google-as-an-identity-provider-for-b2b-guest-users-in-azure-active-directory-preview"></a>Nytt stöd för att lägga till Google som identitetsleverantör för B2B-gästanvändare i Azure Active Directory (förhandsversion)

**Typ:** Ny funktion  
**Servicekategori:** B2b  
**Produktkapacitet:** B2B/B2C

Genom att konfigurera federation med Google i din organisation kan du låta inbjudna Gmail-användare logga in på dina delade appar och resurser med sitt befintliga Google-konto, utan att behöva skapa ett personligt Microsoft-konto (MSA) eller ett Azure AD-konto.

Detta är en opt-in offentlig förhandsvisning. Mer information om Googles federation finns i [Lägga till Google som identitetsleverantör för B2B-gästanvändare](https://docs.microsoft.com/azure/active-directory/b2b/google-federation).

---

## <a name="july-2018"></a>Juli 2018

### <a name="improvements-to-azure-active-directory-email-notifications"></a>Förbättringar av Azure Active Directory-e-postmeddelanden

**Typ:** Ändrad funktion  
**Servicekategori:** Andra  
**Produktkapacitet:** Hantering av identitetslivscykeln
 
Azure Active Directory (Azure AD) e-postmeddelanden har nu en uppdaterad design, samt ändringar i avsändarens e-postadress och avsändarens visningsnamn, när de skickas från följande tjänster:
 
- Azure AD Access-granskningar
- Azure AD Connect Health 
- Azure AD Identity Protection 
- Azure AD Privileged Identity Management
- Certifikatmeddelanden för företagsapp som upphör att gälla
- Meddelanden om etablering av företagsapp
 
E-postmeddelandena skickas från följande e-postadress och visningsnamn:

- E-postadress:azure-noreply@microsoft.com
- Visningsnamn: Microsoft Azure
 
Ett exempel på några av de nya e-postdesignerna och mer information finns [i E-postmeddelanden i Azure AD PIM](https://go.microsoft.com/fwlink/?linkid=2005832).

---

### <a name="azure-ad-activity-logs-are-now-available-through-azure-monitor"></a>Azure AD-aktivitetsloggar är nu tillgängliga via Azure Monitor

**Typ:** Ny funktion  
**Servicekategori:** Rapportering  
**Produktkapacitet:** Övervakning & rapportering

Azure AD-aktivitetsloggarna är nu tillgängliga i offentlig förhandsversion för Azure Monitor (Azures plattformsomfattande övervakningstjänst). Azure Monitor erbjuder dig långsiktig lagring och sömlös integrering, utöver dessa förbättringar:

- Långsiktig kvarhållning genom att dirigera dina loggfiler till ditt eget Azure-lagringskonto.

- Sömlös SIEM-integrering, utan att du behöver skriva eller underhålla anpassade skript.

- Sömlös integrering med dina egna anpassade lösningar, analysverktyg eller incidenthanteringslösningar.

Mer information om de här nya funktionerna finns i våra azure [AD-aktivitetsloggar i Azure Monitor-diagnostiken finns nu i offentlig förhandsversion](https://cloudblogs.microsoft.com/enterprisemobility/2018/07/26/azure-ad-activity-logs-in-azure-monitor-diagnostics-now-in-public-preview/) och vår dokumentation, [Azure Active Directory-aktivitetsloggar i Azure Monitor (förhandsversion).](https://docs.microsoft.com/azure/active-directory/reporting-azure-monitor-diagnostics-overview)

---

### <a name="conditional-access-information-added-to-the-azure-ad-sign-ins-report"></a>Information om villkorlig åtkomst som lagts till i azure AD-inloggningsrapporten

**Typ:** Ny funktion  
**Servicekategori:** Rapportering  
**Produktkapacitet:** Skydd & identitetssäkerhet
 
Med den här uppdateringen kan du se vilka principer som utvärderas när en användare loggar in tillsammans med principutfallet. Dessutom innehåller rapporten nu den typ av klientapp som används av användaren, så att du kan identifiera äldre protokolltrafik. Rapportposter kan nu också sökas efter ett korrelations-ID, som finns i felmeddelandet om användaren och som kan användas för att identifiera och felsöka den matchande inloggningsbegäran.

---

### <a name="view-legacy-authentications-through-sign-ins-activity-logs"></a>Visa äldre autentiseringar via aktivitetsloggar för inloggningar

**Typ:** Ny funktion  
**Servicekategori:** Rapportering  
**Produktkapacitet:** Övervakning & rapportering
 
När fältet **Klientapp** har introduktionen i aktivitetsloggarna för inloggning kan kunder nu se användare som använder äldre autentiseringar. Kunder kan komma åt den här informationen med hjälp av Microsoft Graph-API:et eller inloggningsaktivitetsloggarna i Azure AD-portalen där du kan använda **kontrollen Klientapp** för att filtrera efter äldre autentiseringar. Läs dokumentationen för mer information.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2018"></a>Nya federerade appar tillgängliga i Azure AD-appgalleriet – juli 2018

**Typ:** Ny funktion  
**Servicekategori:** Företagsappar  
**Produktkapacitet:** Integration från tredje part
 
I juli 2018 har vi lagt till dessa 16 nya appar med federationsstöd i appgalleriet:

[Innovation Hub](https://docs.microsoft.com/azure/active-directory/saas-apps/innovationhub-tutorial), [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-tutorial), [Vissa Admin SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/certainadminsso-tutorial), PSUC Mellanlagring, [iPass SmartConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/ipasssmartconnect-tutorial), [Screencast-O-Matic](https://docs.microsoft.com/azure/active-directory/saas-apps/screencast-tutorial), PowerSchool Unified Classroom, [Eli Onboarding](https://docs.microsoft.com/azure/active-directory/saas-apps/elionboarding-tutorial), [Bomgar Remote Support](https://docs.microsoft.com/azure/active-directory/saas-apps/bomgarremotesupport-tutorial), [Nimblex](https://docs.microsoft.com/azure/active-directory/saas-apps/nimblex-tutorial), [Imagineer WebVision](https://docs.microsoft.com/azure/active-directory/saas-apps/imagineerwebvision-tutorial), [Insight4GRC](https://docs.microsoft.com/azure/active-directory/saas-apps/insight4grc-tutorial), [SecureW2 JoinNow Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/securejoinnow-tutorial), [Kanbanize](../saas-apps/kanbanize-tutorial.md), [SmartLPA](../saas-apps/smartlpa-tutorial.md), [Skills Base](https://docs.microsoft.com/azure/active-directory/saas-apps/skillsbase-tutorial)

Mer information om apparna finns i [SaaS-programintegrering med Azure Active Directory](https://aka.ms/appstutorial). Mer information om hur du listar ditt program i Azure AD-appgalleriet finns [i Lista ditt program i Azure Active Directory-programgalleriet](https://aka.ms/azureadapprequest).

---
 
### <a name="new-user-provisioning-saas-app-integrations---july-2018"></a>Nya integreringar av SaaS-appar – juli 2018

**Typ:** Ny funktion  
**Servicekategori:** Etablering av appar  
**Produktkapacitet:** Integration från tredje part
 
Med Azure AD kan du automatisera skapandet, underhållet och borttagningen av användaridentiteter i SaaS-program som Dropbox, Salesforce, ServiceNow med mera. I juli 2018 har vi lagt till stöd för användaretablering för följande program i Azure AD-appgalleriet:

- [Cisco WebEx](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-webex-provisioning-tutorial)

- [Bonusly](https://docs.microsoft.com/azure/active-directory/saas-apps/bonusly-provisioning-tutorial)

En lista över alla program som stöder användaretablering i Azure AD-galleriet finns i [SaaS-programintegrering med Azure Active Directory](https://aka.ms/appstutorial).

---

### <a name="connect-health-for-sync---an-easier-way-to-fix-orphaned-and-duplicate-attribute-sync-errors"></a>Anslut hälsa för synkronisering - Ett enklare sätt att åtgärda överblivna och dubbla attributsynkroniseringsfel

**Typ:** Ny funktion  
**Servicekategori:** AD Connect  
**Produktkapacitet:** Övervakning & rapportering
 
Azure AD Connect Health introducerar självbetjäningsåtgärd som hjälper dig att markera och åtgärda synkroniseringsfel. Den här funktionen felsöker dubblerade attributsynkroniseringsfel och åtgärdar objekt som är överblivna från Azure AD. Denna diagnos har följande fördelar:

- Begränsar dubbletter av attributsynkroniseringsfel, förutsatt att specifika korrigeringar

- Använder en korrigering för dedikerade Azure AD-scenarier, löser fel i ett enda steg

- Ingen uppgradering eller konfiguration krävs för att aktivera och använda den här funktionen

Mer information finns i [Diagnostisera och åtgärda dubbla attributsynkroniseringsfel](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-diagnose-sync-errors)

---

### <a name="visual-updates-to-the-azure-ad-and-msa-sign-in-experiences"></a>Visuella uppdateringar av azure AD- och MSA-inloggningsupplevelserna

**Typ:** Ändrad funktion  
**Servicekategori:** Azure AD  
**Produktkapacitet:** Användarautentisering

Vi har uppdaterat inloggningsupplevelsen för användargränssnittet för Microsofts onlinetjänster, till exempel för Office 365 och Azure. Den här ändringen gör skärmarna mindre röriga och enklare. Mer information om den här ändringen finns [i bloggen Kommande förbättringar av Azure AD-inloggningsbloggen.](https://cloudblogs.microsoft.com/enterprisemobility/2018/04/04/upcoming-improvements-to-the-azure-ad-sign-in-experience/)

---

### <a name="new-release-of-azure-ad-connect---july-2018"></a>Ny version av Azure AD Connect – juli 2018

**Typ:** Ändrad funktion  
**Servicekategori:** Etablering av appar  
**Produktkapacitet:** Hantering av identitetslivscykeln

Den senaste versionen av Azure AD Connect innehåller: 

- Felkorrigeringar och uppdateringar av supporten 

- Allmän tillgänglighet för Ping-Federate integration

- Uppdateringar av den senaste SQL 2012-klienten 

Mer information om den här uppdateringen finns i [Azure AD Connect: Versionsversionshistorik](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history)

---

### <a name="updates-to-the-terms-of-use-end-user-ui"></a>Uppdateringar av användningsvillkoren för slutanvändarens användargränssnitt

**Typ:** Ändrad funktion  
**Servicekategori:** Användningsvillkor  
**Produktkapacitet:** Styrelseformer

Vi uppdaterar acceptanssträngen i tou-slutanvändarens användargränssnitt.

**Aktuell text.** För att komma åt [klientnamn] resurser måste du acceptera användarvillkoren.<br>**Ny text.** För att komma åt [tenantName]-resursen måste du läsa användningsvillkoren.

**Aktuell text:** Att välja att acceptera innebär att du godkänner alla ovanstående användarvillkor.<br>**Ny text:** Klicka på Acceptera för att bekräfta att du har läst och förstått användarvillkoren.

---
 
### <a name="pass-through-authentication-supports-legacy-protocols-and-applications"></a>Direktautentisering stöder äldre protokoll och program

**Typ:** Ändrad funktion  
**Servicekategori:** Autentiseringar (inloggningar)  
**Produktkapacitet:** Användarautentisering
 
Direktautentisering stöder nu äldre protokoll och appar. Följande begränsningar stöds nu fullt ut:

- Användarinloggningar till äldre Office-klientprogram, Office 2010 och Office 2013, utan att kräva modern autentisering.

- Endast åtkomst till kalenderdelning och ledig/upptagen-information i Exchange-hybridmiljöer på Office 2010.

- Användarloggningar till Skype för företag-klientprogram utan att kräva modern autentisering.

- Användarens inloggningar till PowerShell version 1.0.

- Apple Device Enrollment Program (Apple DEP) med installationsassistenten för iOS. 

---
 
### <a name="converged-security-info-management-for-self-service-password-reset-and-multi-factor-authentication"></a>Hantering av konvergerad säkerhetsinformation för återställning av lösenord med självbetjäning och multifaktorautentisering

**Typ:** Ny funktion  
**Servicekategori:** SSPR (SSPR)  
**Produktkapacitet:** Användarautentisering

Med den här nya funktionen kan användare hantera sin säkerhetsinformation (till exempel telefonnummer, e-postadress, mobilapp och så vidare) för återställning av lösenord för självbetjäning (SSPR) och MFA (Multi Factor Authentication) i en enda upplevelse. Användarna behöver inte längre registrera samma säkerhetsinformation för SSPR och MFA i två olika upplevelser. Den här nya upplevelsen gäller även användare som har antingen SSPR eller MFA.

Om en organisation inte tillämpar MFA- eller SSPR-registrering kan användare registrera sin säkerhetsinformation via portalen **Mina appar.** Därifrån kan användare registrera alla metoder som är aktiverade för MFA eller SSPR. 

Detta är en opt-in offentlig förhandsvisning. Administratörer kan aktivera den nya upplevelsen (om så önskas) för en vald grupp av användare eller alla användare i en klientorganisation.

---
 
### <a name="use-the-microsoft-authenticator-app-to-verify-your-identity-when-you-reset-your-password"></a>Använda Microsoft Authenticator-appen för att verifiera din identitet när du återställer lösenordet

**Typ:** Ändrad funktion  
**Servicekategori:** SSPR (SSPR)  
**Produktkapacitet:** Användarautentisering

Med den här funktionen kan icke-administratörer verifiera sin identitet samtidigt som ett lösenord återställs med hjälp av ett meddelande eller en kod från Microsoft Authenticator (eller någon annan autentiseringsapp). När administratörer har aktiverat den här metoden för återställning av lösenord med självbetjäning kan användare som har registrerat en mobilapp via aka.ms/mfasetup eller aka.ms/setupsecurityinfo använda sin mobilapp som verifieringsmetod samtidigt som de återställer sitt lösenord.

Meddelande om mobilappar kan bara aktiveras som en del av en princip som kräver två metoder för att återställa ditt lösenord.

---

## <a name="june-2018"></a>Juni 2018

### <a name="change-notice-security-fix-to-the-delegated-authorization-flow-for-apps-using-azure-ad-activity-logs-api"></a>Ändringsmeddelande: Säkerhetskorrigering till det delegerade auktoriseringsflödet för appar med API för Azure AD-aktivitetsloggar

**Typ:** Planera för förändring  
**Servicekategori:** Rapportering  
**Produktkapacitet:** Övervakning & rapportering

På grund av vår starkare säkerhetskontroll har vi varit tvungna att ändra behörigheterna för appar som använder ett delegerat auktoriseringsflöde för att komma åt [Azure AD-aktivitetsloggar API:er](https://aka.ms/aadreportsapi). Denna förändring kommer att ske senast **den 26 juni 2018**.

Om någon av dina appar använder Azure AD Activity Log API:er följer du dessa steg för att säkerställa att appen inte går sönder när ändringen sker.

**Så här uppdaterar du dina appbehörigheter**

1. Logga in på Azure-portalen, välj **Azure Active Directory**och välj sedan **Appregistreringar**.
2. Välj din app som använder API:et för Azure AD-aktivitetsloggar, välj **Inställningar**, välj **Obligatoriska behörigheter**och välj sedan **Windows Azure Active Directory** API.
3. Markera rutan bredvid **Läs katalogdata** i området **Delegerade behörigheter** i bladet **Aktivera åtkomst** och välj sedan **Spara**.
4. Välj **Bevilja behörigheter**och välj sedan **Ja**.
    
    >[!Note]
    >Du måste vara global administratör för att kunna bevilja behörigheter till appen.

Mer information finns i området [Bevilja behörigheter](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-prerequisites-azure-portal#grant-permissions) i förutsättningar för åtkomst till azure AD-rapporterings-API-artikeln.

---

### <a name="configure-tls-settings-to-connect-to-azure-ad-services-for-pci-dss-compliance"></a>Konfigurera TLS-inställningar för att ansluta till Azure AD-tjänster för PCI DSS-efterlevnad

**Typ:** Ny funktion  
**Servicekategori:** Ej mycket  
**Produktkapacitet:** Plattform

TLS (Transport Layer Security) är ett protokoll som ger sekretess och dataintegritet mellan två kommunicerande program och är det mest distribuerade säkerhetsprotokollet som används idag.

[PCI Security Standards Council](https://www.pcisecuritystandards.org/) har fastställt att tidiga versioner av TLS och Secure Sockets Layer (SSL) måste inaktiveras till förmån för att aktivera nya och säkrare appprotokoll, med efterlevnad från och med **den 30 juni 2018**. Den här ändringen innebär att om du ansluter till Azure AD-tjänster och kräver PCI DSS-efterlevnad måste du inaktivera TLS 1.0. Flera versioner av TLS är tillgängliga, men TLS 1.2 är den senaste versionen som är tillgänglig för Azure Active Directory Services. Vi rekommenderar starkt att du flyttar direkt till TLS 1.2 för både kombinationer av klient/server och webbläsare/server.

Inaktuella webbläsare kanske inte stöder nyare TLS-versioner, till exempel TLS 1.2. Om du vill se vilka versioner av TLS som stöds av din webbläsare går du till webbplatsen [Qualys SSL Labs](https://www.ssllabs.com/) och klickar på **Testa din webbläsare**. Vi rekommenderar att du uppgraderar till den senaste versionen av din webbläsare och helst aktiverar endast TLS 1.2.

**Så här aktiverar du TLS 1.2, efter webbläsare**

- **Microsoft Edge och Internet Explorer (båda är inställda med Internet Explorer)**

    1. Öppna Internet Explorer, välj **Avancerade verktyg** > **för Internetalternativ** > **.**
    2. I **säkerhetsområdet** väljer du **använd TLS 1.2**och väljer sedan **OK**.
    3. Stäng alla webbläsarfönster och starta om Internet Explorer. 

- **Google Chrome**

    1. Öppna Google Chrome, skriv *chrome://settings/* i adressfältet och tryck på **Retur**.
    2. Expandera **alternativen Avancerat,** gå till området **System** och välj **Öppna proxyinställningar**.
    3. I rutan **Egenskaper för Internet** markerar du fliken **Avancerat,** går till **säkerhetsområdet,** väljer **TLS 1.2**och väljer sedan **OK**.
    4. Stäng alla webbläsarfönster och starta om Google Chrome.

- **Mozilla Firefox**

    1. Öppna Firefox, skriv *om:config* i adressfältet och tryck sedan **på Retur**.
    2. Sök efter termen *TLS*och välj sedan posten **security.tls.version.max.**
    3. Ange värdet till **3** för att tvinga webbläsaren att använda upp till version TLS 1.2 och välj sedan **OK**.

        >[!NOTE]
        >Firefox version 60.0 stöder TLS 1.3, så du kan också ställa in security.tls.version.max värde till **4**.

    4. Stäng alla webbläsarfönster och starta om Mozilla Firefox.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2018"></a>Nya federerade appar tillgängliga i Azure AD-appgalleriet – juni 2018

**Typ:** Ny funktion  
**Servicekategori:** Företagsappar  
**Produktkapacitet:** Integration från tredje part
 
I juni 2018 har vi lagt till dessa 15 nya appar med federationsstöd i appgalleriet:

[Skytap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skytap-tutorial), [Lösa musik](https://docs.microsoft.com/azure/active-directory/active-directory-saas-settlingmusic-tutorial), [SAML 1.1 Token aktiverat LOB App](https://docs.microsoft.com/azure/active-directory/active-directory-saas-saml-tutorial), [Supermood](https://docs.microsoft.com/azure/active-directory/active-directory-saas-supermood-tutorial), [Autotask](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), [Endpoint Backup](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), [Skyhigh Networks](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skyhighnetworks-tutorial), Smartway2, [TonicDM](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tonicdm-tutorial), [Moconavi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-moconavi-tutorial), [Zoho One](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zohoone-tutorial), [SharePoint lokalt](https://docs.microsoft.com/azure/active-directory/active-directory-saas-sharepoint-on-premises-tutorial), [ForeSee CX Suite](https://docs.microsoft.com/azure/active-directory/active-directory-saas-foreseecxsuite-tutorial), [Vidyard](https://docs.microsoft.com/azure/active-directory/active-directory-saas-vidyard-tutorial), [ChronicX](https://docs.microsoft.com/azure/active-directory/active-directory-saas-chronicx-tutorial)

Mer information om apparna finns i [SaaS-programintegrering med Azure Active Directory](https://aka.ms/appstutorial). Mer information om hur du listar ditt program i Azure AD-appgalleriet finns [i Lista ditt program i Azure Active Directory-programgalleriet](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---

### <a name="azure-ad-password-protection-is-available-in-public-preview"></a>Azure AD Password Protection är tillgängligt i offentlig förhandsversion

**Typ:** Ny funktion  
**Servicekategori:** Identitetsskydd  
**Produktkapacitet:** Användarautentisering

Använd Azure AD Password Protection för att eliminera lösenord som enkelt gissas från din miljö. Eliminera dessa lösenord bidrar till att minska risken för kompromisser från ett lösenord spray typ av attack.

Azure AD-lösenordsskydd hjälper dig att:

- Skydda organisationens konton i både Azure AD och Windows Server Active Directory (AD). 
- Hindrar användarna från att använda lösenord på en lista med mer än 500 av de vanligaste lösenorden och över 1 miljon teckenersättningsvarianter av dessa lösenord.
- Administrera Azure AD Password Protection från en enda plats i Azure AD-portalen, för både Azure AD och lokala Windows Server AD.

Mer information om Azure AD-lösenordsskydd finns [i Eliminera felaktiga lösenord i organisationen](https://aka.ms/aadpasswordprotectiondocs).

---

### <a name="new-all-guests-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Ny principmall för "alla gäster" villkorlig åtkomst som skapats när användningsvillkoren skapades

**Typ:** Ny funktion  
**Servicekategori:** Användningsvillkor  
**Produktkapacitet:** Styrelseformer

När du skapar dina användarvillkor skapas även en ny principmall för villkorlig åtkomst för "alla gäster" och "alla appar". Den här nya principmallen tillämpar den nyskapade innehållsförteckningen, vilket effektiviserar skapandet och verkställighetsprocessen för gäster.

Mer information finns i [Azure Active Directory Användarvillkor funktionen](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="new-custom-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Ny "anpassad" principmall för villkorlig åtkomst som skapats när användningsvillkoren skapades

**Typ:** Ny funktion  
**Servicekategori:** Användningsvillkor  
**Produktkapacitet:** Styrelseformer

När du skapar användningsvillkoren skapas också en ny principmall för "anpassad" villkorlig åtkomst. Med den här nya principmallen kan du skapa åtkomstversionen och sedan omedelbart gå till bladet för att skapa principer för villkorlig åtkomst, utan att behöva navigera manuellt genom portalen.

Mer information finns i [Azure Active Directory Användarvillkor funktionen](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="new-and-comprehensive-guidance-about-deploying-azure-multi-factor-authentication"></a>Ny och omfattande vägledning om distribution av Azure Multi-Factor Authentication

**Typ:** Ny funktion  
**Servicekategori:** Andra  
**Produktkapacitet:** Skydd & identitetssäkerhet
 
Vi har släppt ny steg-för-steg-vägledning om hur du distribuerar Azure Multi-Factor Authentication (MFA) i din organisation.

Om du vill visa MFA-distributionsguiden går du till repoan för [identitetsdistributionsguider](https://aka.ms/DeploymentPlans) på GitHub. Om du vill ge feedback om distributionsguiderna använder du [formuläret Distributionsplan feedback](https://aka.ms/deploymentplanfeedback). Om du har några frågor om distributionsguiderna kontaktar du oss på [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="azure-ad-delegated-app-management-roles-are-in-public-preview"></a>Azure AD-delegerade apphanteringsroller är i offentlig förhandsversion

**Typ:** Ny funktion  
**Servicekategori:** Företagsappar  
**Produktkapacitet:** Åtkomstkontroll

Administratörer kan nu delegera apphanteringsuppgifter utan att tilldela rollen Global administratör. De nya rollerna och funktionerna är:

- **Nya standardroller för Azure AD-administratörer:**

    - **Programadministratör.** Ger möjlighet att hantera alla aspekter av alla appar, inklusive registrering, SSO-inställningar, apptilldelningar och licensiering, inställningar för appproxy och samtycke (förutom Azure AD-resurser).

    - **Administratör för molnprogram.** Ger alla programadministratörsfunktioner, förutom App-proxy eftersom den inte ger lokal åtkomst.

    - **Programutvecklare.** Ger möjlighet att skapa appregistreringar, även om alternativet **tillåt användare att registrera appar** är inaktiverat.

- **Ägarskap (konfigurera registrering per app och per företagsapp, liknande gruppägarskapsprocessen:**
 
    - **Ägare av appregistrering.** Ger möjlighet att hantera alla aspekter av ägd appregistrering, inklusive appmanifestet och lägga till ytterligare ägare.

    - **Ägare av företagsappar.** Ger möjlighet att hantera många aspekter av ägda företagsappar, inklusive SSO-inställningar, apptilldelningar och samtycke (förutom Azure AD-resurser).

Mer information om offentlig förhandsversion finns [i azure AD-delegerade programhanteringsroller i offentlig förhandsversion!](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/13/hallelujah-azure-ad-delegated-application-management-roles-are-in-public-preview/) Blogg. Mer information om roller och behörigheter finns i [Tilldela administratörsroller i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal).

---

## <a name="may-2018"></a>Maj 2018

### <a name="expressroute-support-changes"></a>Supportändringar för ExpressRoute

**Typ:** Planera för förändring  
**Servicekategori:** Autentiseringar (inloggningar)  
**Produktkapacitet:** Plattform  

Programvara som ett tjänsterbjudande, till exempel Azure Active Directory (Azure AD) är utformade för att fungera bäst genom att gå direkt via Internet, utan att kräva ExpressRoute eller andra privata VPN-tunnlar. På grund av detta, den **1 augusti 2018,** kommer vi att sluta stödja ExpressRoute för Azure AD-tjänster med Azure public peering och Azure-grupper i Microsoft peering. Alla tjänster som påverkas av den här ändringen kan märka Azure AD-trafik gradvis flyttas från ExpressRoute till Internet.

När vi ändrar vårt stöd vet vi också att det fortfarande finns situationer där du kan behöva använda en dedikerad uppsättning kretsar för din autentiseringstrafik. På grund av detta fortsätter Azure AD att stödja IP-intervallbegränsningar per klient med Hjälp av ExpressRoute och tjänster som redan finns på Microsoft-peering med communityn "Övriga Office 365 Online-tjänster". Om dina tjänster påverkas, men du behöver ExpressRoute, måste du göra följande:

- **Om du använder offentlig Azure-peering.** Gå till Microsoft-peering och registrera dig för andra **Office 365 Online-tjänster (12076:5100)** community. Mer information om hur du flyttar från offentlig Azure-peering till Microsoft-peering finns i artikeln [Flytta en offentlig peering till Microsoft peering.](https://docs.microsoft.com/azure/expressroute/how-to-move-peering)

- **Om du använder Microsoft-peering.** Registrera dig för den **andra Office 365 Online-tjänsten (12076:5100)** community. Mer information om routningskrav finns i [avsnittet Stöd för BGP-grupper](https://docs.microsoft.com/azure/expressroute/expressroute-routing#bgp) i artikeln För Routningsbehov i ExpressRoute.

Om du måste fortsätta att använda dedikerade kretsar måste du prata med ditt Microsoft-kontoteam om hur du får auktorisering att använda andra **Office 365 Online-tjänsten (12076:5100)** communityn. Ms Office-hanterade granskningskort kontrollerar om du behöver dessa kretsar och ser till att du förstår de tekniska konsekvenserna av att behålla dem. Obehöriga prenumerationer som försöker skapa flödesfilter för Office 365 visas ett felmeddelande. 
 
---

### <a name="microsoft-graph-apis-for-administrative-scenarios-for-tou"></a>Microsoft Graph API:er för administrativa scenarier för TOU

**Typ:** Ny funktion  
**Servicekategori:** Användningsvillkor  
**Produktkapacitet:** Utvecklarupplevelse
 
Vi har lagt till Microsoft Graph API:er för administration av Azure AD-användningsvillkor. Du kan skapa, uppdatera, ta bort användningsvillkoren objekt.

---

### <a name="add-azure-ad-multi-tenant-endpoint-as-an-identity-provider-in-azure-ad-b2c"></a>Lägga till Azure AD-slutpunkt för flera innehavare som en identitetsprovider i Azure AD B2C

**Typ:** Ny funktion  
**Servicekategori:** B2C - Hantering av konsumentidentiteter  
**Produktkapacitet:** B2B/B2C
 
Med hjälp av anpassade principer kan du nu lägga till den gemensamma slutpunkten för Azure AD som identitetsprovider i Azure AD B2C. På så sätt kan du ha en enda ingång för alla Azure AD-användare som loggar in på dina program. Mer information finns [i Azure Active Directory B2C: Tillåt användare att logga in på en Azure AD-identitetsprovider med flera innehavare med hjälp av anpassade principer](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-commonaad-custom).

---

### <a name="use-internal-urls-to-access-apps-from-anywhere-with-our-my-apps-sign-in-extension-and-the-azure-ad-application-proxy"></a>Använd interna url:er för att komma åt appar var som helst med vårt inloggningstillägg för mina appar och Azure AD-programproxyn

**Typ:** Ny funktion  
**Servicekategori:** Mina appar  
**Produktkapacitet:** Sso
 
Användare kan nu komma åt program via interna url:er även utanför företagets nätverk med hjälp av tillägget Säker inloggning för Mina appar för Azure AD. Detta fungerar med alla program som du har publicerat med Azure AD Application Proxy, i alla webbläsare som också har webbläsartillägget Access Panel installerat. URL-omdirigeringsfunktionen aktiveras automatiskt när en användare loggar in i tillägget. Tillägget är tillgängligt för nedladdning på [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176), [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)och [Firefox](https://go.microsoft.com/fwlink/?linkid=866366).

---
 
### <a name="azure-active-directory---data-in-europe-for-europe-customers"></a>Azure Active Directory - Data i Europa för Europa kunder

**Typ:** Ny funktion  
**Servicekategori:** Andra  
**Produktkapacitet:** GoLocal (på)GoLocal )

Kunder i Europa kräver att deras data stannar i Europa och inte replikeras utanför europeiska datacenter för att uppfylla sekretess och europeiska lagar. Den här [artikeln](https://go.microsoft.com/fwlink/?linkid=872328) innehåller specifik information om vilken identitetsinformation som kommer att lagras i Europa och även innehålla information om information som kommer att lagras utanför europeiska datacenter. 

---
 
### <a name="new-user-provisioning-saas-app-integrations---may-2018"></a>Nya integreringar av SaaS-appar – maj 2018

**Typ:** Ny funktion  
**Servicekategori:** Etablering av appar  
**Produktkapacitet:** Integration från tredje part
 
Med Azure AD kan du automatisera skapandet, underhållet och borttagningen av användaridentiteter i SaaS-program som Dropbox, Salesforce, ServiceNow med mera. I maj 2018 har vi lagt till stöd för användaretablering för följande program i Azure AD-appgalleriet:

- [BlueJeans](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bluejeans-provisioning-tutorial)

- [Cornerstone OnDemand](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cornerstone-ondemand-provisioning-tutorial)

- [Zendesk](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zendesk-provisioning-tutorial)

En lista över alla program som stöder etablering av [https://aka.ms/appstutorial](https://aka.ms/appstutorial)användare i Azure AD-galleriet finns i .

---
 
### <a name="azure-ad-access-reviews-of-groups-and-app-access-now-provides-recurring-reviews"></a>Azure AD-åtkomstgranskningar av grupper och appåtkomst ger nu återkommande recensioner

**Typ:** Ny funktion  
**Servicekategori:** Access Recensioner  
**Produktkapacitet:** Styrelseformer
 
Åtkomstgranskning av grupper och appar är nu allmänt tillgänglig som en del av Azure AD Premium P2.  Administratörer kan konfigurera åtkomstgranskningar av gruppmedlemskap och programtilldelningar för att automatiskt återkomma med jämna mellanrum, till exempel månadsvis eller kvartalsvis.

---

### <a name="azure-ad-activity-logs-sign-ins-and-audit-are-now-available-through-ms-graph"></a>Azure AD-aktivitetsloggar (inloggningar och granskning) är nu tillgängliga via MS Graph

**Typ:** Ny funktion  
**Servicekategori:** Rapportering  
**Produktkapacitet:** Övervakning & rapportering
 
Azure AD-aktivitetsloggar, som innehåller inloggningar och granskningsloggar, är nu tillgängliga via Microsoft Graph API. Vi har exponerat två slutpunkter via Microsoft Graph API för att komma åt dessa loggar. Kolla in våra [dokument](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal) för programmatisk åtkomst till Azure AD Reporting API:er för att komma igång. 

---
 
### <a name="improvements-to-the-b2b-redemption-experience-and-leave-an-org"></a>Förbättringar av B2B inlösen erfarenhet och lämna en organisation

**Typ:** Ny funktion  
**Servicekategori:** B2b  
**Produktkapacitet:** B2B/B2C

**Precis i tid inlösen:** När du delar en resurs med en gästanvändare med B2B API - du behöver inte skicka ut en särskild inbjudan e-post. I de flesta fall kan gästanvändaren komma åt resursen och kommer att tas genom inlösenupplevelsen precis i tid. Ingen mer påverkan på grund av missade e-postmeddelanden. Inga fler frågar dina gästanvändare "Har du klickat på att inlösen länken systemet skickade dig?". Detta innebär att när SPO använder inbjudningshanteraren – grumliga bilagor kan ha samma kanoniska WEBBADRESS för alla användare – interna och externa – i alla inlösentillstånd.

**Modern inlösenupplevelse:** Ingen mer delad skärm inlösen målsida. Användarna ser en modern samtyckesupplevelse med den inbjudande organisationens sekretesspolicy, precis som de gör för appar från tredje part.

**Gästanvändare kan lämna org:** När en användares relation med en organisation är över kan de självbetjäning lämna organisationen. Inget mer ringer den inbjudande org admin att "tas bort", inte mer höja stöd biljetter.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2018"></a>Nya federerade appar tillgängliga i Azure AD-appgalleriet – maj 2018

**Typ:** Ny funktion  
**Servicekategori:** Företagsappar  
**Produktkapacitet:** Integration från tredje part
 
I maj 2018 har vi lagt till dessa 18 nya appar med federationssupport i vårt appgalleri:

[AwardSpring](https://docs.microsoft.com/azure/active-directory/active-directory-saas-awardspring-tutorial), Infogix Data3Sixty styra, [Yodeck](https://docs.microsoft.com/azure/active-directory/active-directory-saas-infogix-tutorial), [Jamf Pro](https://docs.microsoft.com/azure/active-directory/active-directory-saas-jamfprosamlconnector-tutorial), [KnowledgeOwl](https://docs.microsoft.com/azure/active-directory/active-directory-saas-knowledgeowl-tutorial), [Envi MMIS](https://docs.microsoft.com/azure/active-directory/active-directory-saas-envimmis-tutorial), [LaunchDarkly](https://docs.microsoft.com/azure/active-directory/active-directory-saas-launchdarkly-tutorial), [Adobe Captivate Prime](https://docs.microsoft.com/azure/active-directory/active-directory-saas-adobecaptivateprime-tutorial), [Montage Online](https://docs.microsoft.com/azure/active-directory/active-directory-saas-montageonline-tutorial),[中](https://docs.microsoft.com/azure/active-directory/active-directory-saas-manabipocket-tutorial)ンンンンンンンンンンン , OpenReel, Arc Publishing [- SSO](https://docs.microsoft.com/azure/active-directory/active-directory-saas-arc-tutorial), [PlanGrid](https://docs.microsoft.com/azure/active-directory/active-directory-saas-plangrid-tutorial), [iWellnessNow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-iwellnessnow-tutorial), [Proxyclick](https://docs.microsoft.com/azure/active-directory/active-directory-saas-proxyclick-tutorial), [Riskware](https://docs.microsoft.com/azure/active-directory/active-directory-saas-riskware-tutorial), [Flock](https://docs.microsoft.com/azure/active-directory/active-directory-saas-flock-tutorial), [Reviewsnap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-reviewsnap-tutorial)

Mer information om apparna finns i [SaaS-programintegrering med Azure Active Directory](https://aka.ms/appstutorial).

Mer information om hur du listar ditt program i Azure AD-appgalleriet finns [i Lista ditt program i Azure Active Directory-programgalleriet](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

---
 
### <a name="new-step-by-step-deployment-guides-for-azure-active-directory"></a>Nya steg-för-steg-distributionsguider för Azure Active Directory

**Typ:** Ny funktion  
**Servicekategori:** Andra  
**Produktkapacitet:** Katalog
 
Ny, steg-för-steg-vägledning om hur du distribuerar Azure Active Directory (Azure AD), inklusive självbetjäningslösenordsåterställning (SSPR), enkel inloggning (SSO), villkorlig åtkomst (CERTIFIKAT), Appproxy, Användaretablering, Active Directory Federation Services (ADFS) till Direktautentisering (PTA) och ADFS till lösenord hash-synkronisering (PHS).

Om du vill visa distributionsguiderna går du till de [identitetsdistributionsguider](https://aka.ms/DeploymentPlans) som finns på GitHub. Om du vill ge feedback om distributionsguiderna använder du [formuläret Distributionsplan feedback](https://aka.ms/deploymentplanfeedback). Om du har några frågor om distributionsguiderna kontaktar du oss på [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="enterprise-applications-search---load-more-apps"></a>Sökning i företagsprogram – läs in fler appar

**Typ:** Ny funktion  
**Servicekategori:** Företagsappar  
**Produktkapacitet:** Sso
 
Har du problem med att hitta dina program / servicehuvudnamn? Vi har lagt till möjligheten att läsa in fler program i din företagsapplikationer som alla programlista. Som standard visar vi 20 program. Du kan nu klicka, **Läs in mer** för att visa ytterligare program. 

---
 
### <a name="the-may-release-of-aadconnect-contains-a-public-preview-of-the-integration-with-pingfederate-important-security-updates-many-bug-fixes-and-new-great-new-troubleshooting-tools"></a>Majversionen av AADConnect innehåller en offentlig förhandsversion av integrationen med PingFederate, viktiga säkerhetsuppdateringar, många buggfixar och nya nya felsökningsverktyg. 

**Typ:** Ändrad funktion  
**Servicekategori:** AD Connect  
**Produktkapacitet:** Hantering av identitetslivscykeln
 
Majversionen av AADConnect innehåller en offentlig förhandsversion av integrationen med PingFederate, viktiga säkerhetsuppdateringar, många buggfixar och nya nya felsökningsverktyg. Du hittar viktig information [här](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#118190).

---

### <a name="azure-ad-access-reviews-auto-apply"></a>Azure AD-åtkomstgranskningar: tillämpa automatiskt

**Typ:** Ändrad funktion  
**Servicekategori:** Access Recensioner  
**Produktkapacitet:** Styrelseformer

Åtkomstgranskningar av grupper och appar är nu allmänt tillgängliga som en del av Azure AD Premium P2. En administratör kan konfigurera för att automatiskt tillämpa granskarens ändringar på den gruppen eller appen när åtkomstgranskningen är klar. Administratören kan också ange vad som händer med användarens fortsatta åtkomst om granskarna inte svarade, tog bort åtkomst, behåller åtkomsten eller tar systemrekommendationer. 

---

### <a name="id-tokens-can-no-longer-be-returned-using-the-query-response_mode-for-new-apps"></a>ID-token kan inte längre returneras med hjälp av frågan response_mode för nya appar. 

**Typ:** Ändrad funktion  
**Servicekategori:** Autentiseringar (inloggningar)  
**Produktkapacitet:** Användarautentisering
 
Appar som skapats den 25 april 2018 eller senare kan inte längre begära en **id_token** med **frågan** response_mode.  Detta ger Azure AD infogad med OIDC-specifikationerna och hjälper till att minska din appattackyta.  Appar som skapats före den 25 april 2018 blockeras inte från att använda **frågan** response_mode med en response_type **id_token**.  Felet som returneras när du begär en id_token från AAD är **AADSTS70007: "query" är inte ett värde som stöds av "response_mode" när du begär en token**.

**Fragmentet** och **form_post** response_modes fortsätta att fungera – när du skapar nya programobjekt (till exempel för App Proxy-användning) säkerställer du användningen av en av dessa response_modes innan de skapar ett nytt program.  

---
 
## <a name="april-2018"></a>April 2018 

### <a name="azure-ad-b2c-access-token-are-ga"></a>Azure AD B2C Access-token är GA

**Typ:** Ny funktion  
**Servicekategori:** B2C - Hantering av konsumentidentiteter  
**Produktkapacitet:** B2B/B2C 

Du kan nu komma åt webb-API:er som skyddas av Azure AD B2C med hjälp av åtkomsttoken. Funktionen flyttas från offentlig förhandsversion till GA. Användargränssnittsupplevelsen för att konfigurera Azure AD B2C-program och webb-API:er har förbättrats och andra mindre förbättringar har gjorts.
 
Mer information finns i [Azure AD B2C: Begära åtkomsttoken](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-access-tokens).

---

### <a name="test-single-sign-on-configuration-for-saml-based-applications"></a>Testa en inloggningskonfiguration för SAML-baserade program

**Typ:** Ny funktion  
**Servicekategori:** Företagsappar  
**Produktkapacitet:** Sso

När du konfigurerar SAML-baserade SSO-program kan du testa integreringen på konfigurationssidan. Om du stöter på ett fel under inloggningen kan du ange felet i testupplevelsen och Azure AD ger dig hjälp med lösningssteg för att lösa det specifika problemet.

Mer information finns i:

- [Konfigurera enkel inloggning för program som inte ingår i Azure Active Directory-programgalleriet](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps)
- [Felsöka SAML-baserad enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

---
 
### <a name="azure-ad-terms-of-use-now-has-per-user-reporting"></a>Azure AD användarvillkor har nu per användare rapportering

**Typ:** Ny funktion  
**Servicekategori:** Användningsvillkor  
**Produktkapacitet:** Överensstämmelse
 
Administratörer kan nu välja en viss tou och se alla användare som har samtyckt till den tou och vilket datum/tid det ägde rum.

Mer information finns i [azure AD-användningsfunktionen](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---
 
### <a name="azure-ad-connect-health-risky-ip-for-ad-fs-extranet-lockout-protection"></a>Azure AD Connect-hälsotillstånd: Riskabel IP för AD FS extranätsutelåsningsskydd 

**Typ:** Ny funktion  
**Servicekategori:** Andra  
**Produktkapacitet:** Övervakning & rapportering

Connect Health stöder nu möjligheten att identifiera IP-adresser som överskrider ett tröskelvärde för misslyckade U/P-inloggningar per timme eller dagligen. Funktionerna i den här funktionen är:

- Omfattande rapport som visar IP-adress och antalet misslyckade inloggningar som genereras per timme /dagligen med anpassningsbar tröskelvärde.
- E-postbaserade aviseringar som visar när en viss IP-adress har överskridit tröskelvärdet för misslyckade U/P-inloggningar per timme/dag.
- Ett nedladdningsalternativ för att göra en detaljerad analys av

Mer information finns i [Risky IP Report](https://aka.ms/aadchriskyip).

---
 
### <a name="easy-app-config-with-metadata-file-or-url"></a>Enkel app config med metadatafil eller URL

**Typ:** Ny funktion  
**Servicekategori:** Företagsappar  
**Produktkapacitet:** Sso

På sidan Enterprise-program kan administratörer ladda upp en SAML-metadatafil för att konfigurera SAML-baserad inloggning för AAD-galleri- och icke-galleriprogram.

Dessutom kan du använda Url för Azure AD-programfederationsmetadata för att konfigurera SSO med det riktade programmet.

Mer information finns i [Konfigurera enkel inloggning till program som inte finns i Azure Active Directory-programgalleriet](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps).

---

### <a name="azure-ad-terms-of-use-now-generally-available"></a>Azure AD Användarvillkor nu allmänt tillgängliga

**Typ:** Ny funktion  
**Servicekategori:** Användningsvillkor  
**Produktkapacitet:** Överensstämmelse
 

Azure AD användarvillkor har flyttats från offentlig förhandsversion till allmänt tillgängliga.

Mer information finns i [azure AD-användningsfunktionen](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Tillåta eller blockera inbjudningar till B2B-användare från specifika organisationer

**Typ:** Ny funktion  
**Servicekategori:** B2b  
**Produktkapacitet:** B2B/B2C
 

Du kan nu ange vilka partnerorganisationer du vill dela och samarbeta med i Azure AD B2B Collaboration. För att göra detta kan du välja att skapa en lista över specifika tillåt eller neka domäner. När en domän blockeras med hjälp av dessa funktioner kan anställda inte längre skicka inbjudningar till personer i den domänen.

Detta hjälper dig att kontrollera åtkomsten till dina resurser, samtidigt som du möjliggör en smidig upplevelse för godkända användare.

Den här B2B-samarbetsfunktionen är tillgänglig för alla Azure Active Directory-kunder och kan användas tillsammans med Azure AD Premium-funktioner som villkorlig åtkomst och identitetsskydd för mer detaljerad kontroll över när och hur externa företagsanvändare undertecknar in och få tillgång.

Mer information finns i [Tillåt eller blockera inbjudningar till B2B-användare från specifika organisationer](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-allow-deny-list).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nya federerade appar tillgängliga i Azure AD-appgalleriet

**Typ:** Ny funktion  
**Servicekategori:** Företagsappar  
**Produktkapacitet:** Integration från tredje part

I april 2018 har vi lagt till dessa 13 nya appar med federationssupport i vårt appgalleri:

Kriterium HCM, [FiscalNote](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fiscalnote-tutorial), [Hemlig server (lokalt)](https://docs.microsoft.com/azure/active-directory/active-directory-saas-secretserver-on-premises-tutorial), [dynamisk signal](https://docs.microsoft.com/azure/active-directory/active-directory-saas-dynamicsignal-tutorial), [mindWireless](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mindwireless-tutorial), [OrgChart Now](https://docs.microsoft.com/azure/active-directory/active-directory-saas-orgchartnow-tutorial), [Ziflow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ziflow-tutorial), [AppNeta Performance Monitor](https://docs.microsoft.com/azure/active-directory/active-directory-saas-appneta-tutorial), [Elium](https://docs.microsoft.com/azure/active-directory/active-directory-saas-elium-tutorial) , [Fluxx Labs](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fluxxlabs-tutorial), [Cisco Cloud](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ciscocloud-tutorial), Shelf, [SafetyNet](https://docs.microsoft.com/azure/active-directory/active-directory-saas-safetynet-tutorial)

Mer information om apparna finns i [SaaS-programintegrering med Azure Active Directory](https://aka.ms/appstutorial).

Mer information om hur du listar ditt program i Azure AD-appgalleriet finns [i Lista ditt program i Azure Active Directory-programgalleriet](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

---
 
### <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications-public-preview"></a>Bevilja B2B-användare i Azure AD-åtkomst till dina lokala program (offentlig förhandsversion)

**Typ:** Ny funktion  
**Servicekategori:** B2b  
**Produktkapacitet:** B2B/B2C

Som en organisation som använder Azure Active Directory (Azure AD) B2B-samarbetsfunktioner för att bjuda in gästanvändare från partnerorganisationer till din Azure AD kan du nu ge dessa B2B-användare åtkomst till lokala appar. Dessa lokala appar kan använda SAML-baserad autentisering eller integrerad Windows-autentisering (IWA) med Kerberos-begränsad delegering (KCD).

Mer information finns [i Bevilja B2B-användare i Azure AD-åtkomst till dina lokala program](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-hybrid-cloud-to-on-premises).

---
 
### <a name="get-sso-integration-tutorials-from-the-azure-marketplace"></a>Hämta självstudier för SSO-integrering från Azure Marketplace

**Typ:** Ändrad funktion  
**Servicekategori:** Andra  
**Produktkapacitet:** Integration från tredje part

Om ett program som visas på [Azure-marknadsplatsen](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1) stöder SAML-baserad enkel inloggning, ger du integreringshandledningen som är associerad med programmet genom att klicka på Hämta det **nu.** 

---

### <a name="faster-performance-of-azure-ad-automatic-user-provisioning-to-saas-applications"></a>Snabbare prestanda för Azure AD automatisk användaretablering till SaaS-program

**Typ:** Ändrad funktion  
**Servicekategori:** Etablering av appar  
**Produktkapacitet:** Integration från tredje part
 
Tidigare kan kunder som använder Azure Active Directory-användaretableringsapparna för SaaS-program (till exempel Salesforce, ServiceNow och Box) få långsam prestanda om deras Azure AD-klienter innehöll över 100 000 kombinerade användare och grupper och de använde användar- och grupptilldelningar för att avgöra vilka användare som ska etableras.

Den 2 april 2018 distribuerades betydande prestandaförbättringar till Azure AD-etableringstjänsten som avsevärt minskar den tid som krävs för att utföra inledande synkroniseringar mellan Azure Active Directory och mål-SaaS-program.

Som ett resultat, många kunder som hade inledande synkroniseringar till apps som tog många dagar eller aldrig slutfört, nu slutföras inom några minuter eller timmar.

Mer information finns i [Vad händer under etableringen?](/azure//active-directory/app-provisioning/how-provisioning-works)

---

### <a name="self-service-password-reset-from-windows-10-lock-screen-for-hybrid-azure-ad-joined-machines"></a>Återställning av lösenord för självbetjäning från Windows 10-låsskärmen för hybrid-Azure AD-anslutna datorer

**Typ:** Ändrad funktion  
**Servicekategori:** Återställning av lösenord för självbetjäning  
**Produktkapacitet:** Användarautentisering
 
Vi har uppdaterat Windows 10 SSPR-funktionen för att inkludera stöd för datorer som är hybrid Azure AD-anslutna. Den här funktionen är tillgänglig i Windows 10 RS4 tillåter användare att återställa sitt lösenord från låsskärmen på en Windows 10-maskin. Användare som är aktiverade och registrerade för återställning av lösenord med självbetjäning kan använda den här funktionen.

Mer information finns i [Azure AD-lösenordsåterställning från inloggningsskärmen](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-windows).

---

## <a name="march-2018"></a>Mars 2018
 
### <a name="certificate-expire-notification"></a>Meddelande om att certifikatet upphör att gälla

**Typ:** Fast  
**Servicekategori:** Företagsappar  
**Produktkapacitet:** Sso
 
Azure AD skickar ett meddelande när ett certifikat för ett galleri eller icke-galleriprogram håller på att upphöra att gälla. 

Vissa användare har inte fått meddelanden för företagsprogram som konfigurerats för SAML-baserad enkel inloggning. Det här problemet löstes. Azure AD skickar meddelanden för certifikat som löper ut om 7, 30 och 60 dagar. Du kan se den här händelsen i granskningsloggarna. 

Mer information finns i:

- [Hantera certifikat för federerade enstaka inloggning i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs)
- [Granska aktivitetsrapporter i Azure Active Directory-portalen](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)
 
---
 
### <a name="twitter-and-github-identity-providers-in-azure-ad-b2c"></a>Twitter- och GitHub-identitetsleverantörer i Azure AD B2C

**Typ:** Ny funktion  
**Servicekategori:** B2C - Hantering av konsumentidentiteter  
**Produktkapacitet:** B2B/B2C
 
Du kan nu lägga till Twitter eller GitHub som en identitetsleverantör i Azure AD B2C. Twitter går från offentlig förhandsvisning till GA. GitHub släpps i offentlig förhandsversion.

Mer information finns i [Vad är Azure AD B2B-samarbete?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
 
---

### <a name="restrict-browser-access-using-intune-managed-browser-with-azure-ad-application-based-conditional-access-for-ios-and-android"></a>Begränsa webbläsaråtkomst med Intune-hanterad webbläsare med Azure AD-programbaserad villkorlig åtkomst för iOS och Android

**Typ:** Ny funktion  
**Servicekategori:** Villkorlig åtkomst  
**Produktkapacitet:** Skydd & identitetssäkerhet
 
**Nu i offentlig förhandsvisning!**

**Intune hanterad webbläsare SSO:** Dina anställda kan använda enkel inloggning över inbyggda klienter (som Microsoft Outlook) och Intune Managed Browser för alla Azure AD-anslutna appar.

**Support för villkorlig åtkomst i Intune-hanterade webbläsare:** Du kan nu kräva att anställda använder webbläsaren Intune Managed med hjälp av programbaserade principer för villkorlig åtkomst.

Läs mer om detta i vårt [blogginlägg](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/15/the-intune-managed-browser-now-supports-azure-ad-sso-and-conditional-access/).

Mer information finns i:

- [Konfigurera programbaserad villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

- [Konfigurera principer för hanterade webbläsare](https://aka.ms/managedbrowser)  

---
 
### <a name="app-proxy-cmdlets-in-powershell-ga-module"></a>App Proxy Cmdlets i PowerShell GA-modul

**Typ:** Ny funktion  
**Servicekategori:** App Proxy  
**Produktkapacitet:** Åtkomstkontroll
 
Stöd för Application Proxy cmdlets är nu i PowerShell GA Module! Detta kräver att du håller dig uppdaterad på PowerShell-moduler - om du blir mer än ett år efter kan vissa cmdlets sluta fungera. 

Mer information finns i [AzureAD](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0).
 
---
 
### <a name="office-365-native-clients-are-supported-by-seamless-sso-using-a-non-interactive-protocol"></a>Office 365-inbyggda klienter stöds av Seamless SSO med hjälp av ett icke-interaktivt protokoll

**Typ:** Ny funktion  
**Servicekategori:** Autentiseringar (inloggningar)  
**Produktkapacitet:** Användarautentisering
 
Användare som använder inbyggda Office 365-klienter (version 16.0.8730.xxxx och högre) får en tyst inloggningsupplevelse med Seamless SSO. Det här stödet tillhandahålls av tillägget ett icke-interaktivt protokoll (WS-Trust) till Azure AD.

Mer information finns [i Hur fungerar inloggning på en inbyggd klient med Sömlös SSO?](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-how-it-works#how-does-sign-in-on-a-native-client-with-seamless-sso-work)
 
---

### <a name="users-get-a-silent-sign-on-experience-with-seamless-sso-if-an-application-sends-sign-in-requests-to-azure-ads-tenant-endpoints"></a>Användare får en tyst inloggningsupplevelse med Seamless SSO om ett program skickar inloggningsbegäranden till Azure AD:s slutpunkter för klientn

**Typ:** Ny funktion  
**Servicekategori:** Autentiseringar (inloggningar)  
**Produktkapacitet:** Användarautentisering
 
Användare får en tyst inloggningsupplevelse, med Seamless SSO, `https://contoso.sharepoint.com`om ett program (till exempel) skickar inloggningsbegäranden `https://login.microsoftonline.com/contoso.com/<..>` till `https://login.microsoftonline.com/<tenant_ID>/<..>` Azure AD:s slutpunkter för`https://login.microsoftonline.com/common/<...>`klientorganisationar – det vill än Azure AD:s gemensamma slutpunkt ( ).

Mer information finns i [Azure Active Directory Seamless Single Sign-On](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). 

---
 
### <a name="need-to-add-only-one-azure-ad-url-instead-of-two-urls-previously-to-users-intranet-zone-settings-to-roll-out-seamless-sso"></a>Behöver bara lägga till en Azure AD-URL, i stället för två webbadresser tidigare, till användarnas zoninställningar för intranät för att distribuera Sömlös SSO

**Typ:** Ny funktion  
**Servicekategori:** Autentiseringar (inloggningar)  
**Produktkapacitet:** Användarautentisering
 
Om du vill distribuera Sömlös SSO till användarna behöver du bara lägga till en Azure AD-URL `https://autologon.microsoftazuread-sso.com`i användarnas intranätzonsinställningar med hjälp av grupprincipen i Active Directory: . Tidigare var kunderna tvungna att lägga till två webbadresser.

Mer information finns i [Azure Active Directory Seamless Single Sign-On](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). 
 
---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nya federerade appar finns i Azure AD-appgalleriet

**Typ:** Ny funktion  
**Servicekategori:** Företagsappar  
**Produktkapacitet:** Integration från tredje part

I mars 2018 har vi lagt till dessa 15 nya appar med federationssupport i vårt appgalleri:

[Boxcryptor](https://docs.microsoft.com/azure/active-directory/active-directory-saas-boxcryptor-tutorial), [CylancePROTECT](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cylanceprotect-tutorial), Wrike, [SignalFx](https://docs.microsoft.com/azure/active-directory/active-directory-saas-signalfx-tutorial), Assistent av FirstAgenda, [YardiOne](https://docs.microsoft.com/azure/active-directory/active-directory-saas-yardione-tutorial), Vtiger CRM, inwink, [Amplitude](https://docs.microsoft.com/azure/active-directory/active-directory-saas-amplitude-tutorial), [Spacio](https://docs.microsoft.com/azure/active-directory/active-directory-saas-spacio-tutorial), [ContractWorks](https://docs.microsoft.com/azure/active-directory/active-directory-saas-contractworks-tutorial), [Bersin](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bersin-tutorial), [Mercell](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mercell-tutorial), [Trisotech Digital Enterprise Server](https://docs.microsoft.com/azure/active-directory/active-directory-saas-trisotechdigitalenterpriseserver-tutorial), [Qumu Cloud](https://docs.microsoft.com/azure/active-directory/active-directory-saas-qumucloud-tutorial).
 
Mer information om apparna finns i [SaaS-programintegrering med Azure Active Directory](https://aka.ms/appstutorial).

Mer information om hur du listar ditt program i Azure AD-appgalleriet finns [i Lista ditt program i Azure Active Directory-programgalleriet](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---
 
### <a name="pim-for-azure-resources-is-generally-available"></a>PIM för Azure-resurser är allmänt tillgängligt

**Typ:** Ny funktion  
**Servicekategori:** Privilegierad identitetshantering  
**Produktkapacitet:** Privilegierad identitetshantering
 
Om du använder Azure AD Privileged Identity Management för katalogroller kan du nu använda PIM:s tidsbundna åtkomst- och tilldelningsfunktioner för Azure Resource-roller som Prenumerationer, resursgrupper, virtuella datorer och andra resurser som stöds av Azure Resource Manager. Framtvinga multifaktorautentisering när du aktiverar roller Just-In-Time och schemalägg aktiveringar i samordning med godkända ändringsfönster. Dessutom lägger den här versionen till förbättringar som inte är tillgängliga under offentlig förhandsversion, inklusive ett uppdaterat användargränssnitt, godkännandearbetsflöden och möjligheten att utöka roller som löper ut snart och förnya utgångna roller.

Mer information finns i [PIM för Azure-resurser (förhandsversion)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac)
 
---
 
### <a name="adding-optional-claims-to-your-apps-tokens-public-preview"></a>Lägga till valfria anspråk i dina apptokens (offentlig förhandsversion)

**Typ:** Ny funktion  
**Servicekategori:** Autentiseringar (inloggningar)  
**Produktkapacitet:** Användarautentisering
 
Din Azure AD-app kan nu begära anpassade eller valfria anspråk i JWTs eller SAML-token.  Dessa är anspråk om användaren eller klienten som inte ingår som standard i token, på grund av storlek eller tillämplighetsbegränsningar.  Detta är för närvarande i offentlig förhandsversion för Azure AD-appar på v1.0- och v2.0-slutpunkterna.  Mer information om vilka anspråk som kan läggas till finns i dokumentationen och hur du redigerar programmanifestet för att begära dem.  

Mer information finns [i Valfria anspråk i Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims).
 
---
 
### <a name="azure-ad-supports-pkce-for-more-secure-oauth-flows"></a>Azure AD stöder PKCE för säkrare OAuth-flöden

**Typ:** Ny funktion  
**Servicekategori:** Autentiseringar (inloggningar)  
**Produktkapacitet:** Användarautentisering
 
Azure AD-dokument har uppdaterats för att notera stöd för PKCE, vilket möjliggör säkrare kommunikation under beviljandeflödet av OAuth 2.0-auktoriseringskod.  Både S256 och klartext code_challenges stöds på v1.0- och v2.0-slutpunkterna. 

Mer information finns i [Begär en auktoriseringskod](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols-oauth-code#request-an-authorization-code). 
 
---
 
### <a name="support-for-provisioning-all-user-attribute-values-available-in-the-workday-get_workers-api"></a>Stöd för etablering av alla användarattributvärden som är tillgängliga i arbetsdagars-Get_Workers API

**Typ:** Ny funktion  
**Servicekategori:** Etablering av appar  
**Produktkapacitet:** Integration från tredje part
 
Den offentliga förhandsversionen av inkommande etablering från Arbetsdag till Active Directory och Azure AD stöder nu möjligheten att extrahera och etablera alla attributvärden som är tillgängliga i Arbetsdagar Get_Workers API. Detta lägger till stöd för hundratals ytterligare standard- och anpassade attribut utöver de som levererades med den ursprungliga versionen av lösningskopplingen för ingående arbetsdag.

Mer information finns i: [Anpassa listan över användarattribut för Arbetsdag](https://docs.microsoft.com/azure/active-directory/active-directory-saas-workday-inbound-tutorial#customizing-the-list-of-workday-user-attributes)

---

### <a name="changing-group-membership-from-dynamic-to-static-and-vice-versa"></a>Ändra gruppmedlemskap från dynamisk till statisk och vice versa

**Typ:** Ny funktion  
**Servicekategori:** Gruppledning  
**Produktkapacitet:** Samarbete
 
Det är möjligt att ändra hur medlemskap hanteras i en grupp. Detta är användbart när du vill behålla samma gruppnamn och ID i systemet, så alla befintliga referenser till gruppen är fortfarande giltiga. för att skapa en ny grupp måste dessa referenser uppdateras.
Vi har uppdaterat Azure AD Admin Center för att stödja den här funktionen. Nu kan kunder konvertera befintliga grupper från dynamiskt medlemskap till tilldelat medlemskap och vice versa. De befintliga PowerShell-cmdlets är också fortfarande tillgängliga.

Mer information finns i [Dynamiska medlemskapsregler för grupper i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)

---

### <a name="improved-sign-out-behavior-with-seamless-sso"></a>Förbättrat ut logga ut med Sömlös SSO

**Typ:** Ändrad funktion  
**Servicekategori:** Autentiseringar (inloggningar)  
**Produktkapacitet:** Användarautentisering
 
Tidigare, även om användare uttryckligen loggat ut från ett program som skyddas av Azure AD, skulle de automatiskt loggas tillbaka med Sömlös SSO om de försökte komma åt ett Azure AD-program igen inom deras corpnet från sina domänanslutna enheter. Med den här ändringen stöds ut logga ut.  På så sätt kan användare välja samma eller olika Azure AD-konto att logga in igen med, i stället för att automatiskt loggas in med Seamless SSO.

Mer information finns i [Azure Active Directory Seamless Single Sign-On](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso)
 
---
 
### <a name="application-proxy-connector-version-154020-released"></a>Application Proxy Connector Version 1.5.402.0 Släppt

**Typ:** Ändrad funktion  
**Servicekategori:** App Proxy  
**Produktkapacitet:** Skydd & identitetssäkerhet
 
Den här anslutningsversionen distribueras gradvis ut till november. Den här nya kopplingsversionen innehåller följande ändringar:

- Anslutningen ställer nu in domännivåcookies i stället underdomännivå. Detta säkerställer en smidigare SSO-upplevelse och undviker redundanta autentiseringsmeddelanden.
- Stöd för segmenterade kodningsbegäranden
- Förbättrad hälsoövervakning av anslutning 
- Flera buggfixar och stabilitetsförbättringar

Mer information finns i [Förstå Azure AD Application Proxy-kopplingar](https://docs.microsoft.com/azure/active-directory/application-proxy-understand-connectors).
 
---

## <a name="february-2018"></a>Februari 2018
 
### <a name="improved-navigation-for-managing-users-and-groups"></a>Förbättrad navigering för att hantera användare och grupper

**Typ:** Planera för förändring  
**Servicekategori:** Kataloghantering  
**Produktkapacitet:** Katalog

Navigeringsupplevelsen för att hantera användare och grupper har effektiviserats. Du kan nu navigera från katalogöversikten direkt till listan över alla användare, med enklare åtkomst till listan över borttagna användare. Du kan också navigera från katalogöversikten direkt till listan över alla grupper, med enklare åtkomst till grupphanteringsinställningar. Och även från katalogöversiktssidan kan du söka efter en användare, grupp, ett företagsprogram eller en appregistrering. 

---

### <a name="availability-of-sign-ins-and-audit-reports-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>Tillgänglighet för inloggningar och granskningsrapporter i Microsoft Azure som drivs av 21Vianet (Azure China 21Vianet)

**Typ:** Ny funktion  
**Servicekategori:** Azure-stack  
**Produktkapacitet:** Övervakning & rapportering

Azure AD-aktivitetsloggrapporter är nu tillgängliga i Microsoft Azure som drivs av 21Vianet-instanser (Azure China 21Vianet). Följande loggar ingår:

- **Aktivitetsloggar för inloggningar** – innehåller alla inloggningsloggar som är kopplade till din klient.

- **Självbetjäningsloggar för lösenordsgranskning** – innehåller alla SSPR-granskningsloggar.

- **Kataloghanteringsgranskningsloggar** – innehåller alla kataloghanteringsrelaterade granskningsloggar som användarhantering, apphantering och andra.

Med dessa loggar kan du få insikter om hur det går för din miljö. Med dessa data kan du:

- Bestäm hur dina appar och tjänster används av användarna.

- Felsöka problem som hindrar användarna från att få jobbet gjort.

Mer information om hur du använder dessa rapporter finns i [Azure Active Directory-rapportering](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal).

---

### <a name="use-report-reader-role-non-admin-role-to-view-azure-ad-activity-reports"></a>Använda rollen "Rapportläsare" (roll som inte är administratör) för att visa Azure AD-aktivitetsrapporter

**Typ:** Ny funktion  
**Servicekategori:** Rapportering  
**Produktkapacitet:** Övervakning & rapportering

Som en del av kundernas feedback för att göra det möjligt för icke-administratörsroller att få åtkomst till Azure AD-aktivitetsloggar har vi aktiverat möjligheten för användare som är i rollen "Rapportläsare" att komma åt inloggningar och granskningsaktivitet inom Azure-portalen samt använda Microsoft Graph Api. 

Mer information om hur du använder dessa rapporter finns i [Azure Active Directory-rapportering](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). 

---

### <a name="employeeid-claim-available-as-user-attribute-and-user-identifier"></a>EmployeeID-anspråk tillgängligt som användarattribut och användaridentifierare

**Typ:** Ny funktion  
**Servicekategori:** Företagsappar  
**Produktkapacitet:** Sso
 
Du kan konfigurera **EmployeeID** som användaridentifierare och användarattribut för medlemsanvändare och B2B-gäster i SAML-baserade inloggningsprogram från företagsprogramgränssnittet.

Mer information finns [i Anpassa anspråk som utfärdats i SAML-token för företagsprogram i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).

---

### <a name="simplified-application-management-using-wildcards-in-azure-ad-application-proxy"></a>Förenklad programhantering med jokertecken i Azure AD Application Proxy

**Typ:** Ny funktion  
**Servicekategori:** App Proxy  
**Produktkapacitet:** Användarautentisering
 
För att göra programdistributionen enklare och minska dina administrativa kostnader stöder vi nu möjligheten att publicera program med jokertecken. Om du vill publicera ett jokerteckenprogram kan du följa standardpubliceringsflödet för program, men använda ett jokertecken i de interna och externa webbadresserna.

Mer information finns [i Jokerteckenprogram i Azure Active Directory-programproxyn](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-wildcard)

---

### <a name="new-cmdlets-to-support-configuration-of-application-proxy"></a>Nya cmdlets som stöder konfiguration av Application Proxy

**Typ:** Ny funktion  
**Servicekategori:** App Proxy  
**Produktkapacitet:** Plattform

Den senaste versionen av AzureAD PowerShell Preview-modulen innehåller nya cmdlets som gör det möjligt för kunder att konfigurera programproxyprogram med PowerShell.

De nya cmdlets är: 

- Hämta AzureADApplicationProxyApplication
- Hämta AzureADApplicationProxyApplicationConnectorGroup
- Hämta AzureADApplicationProxyConnector
- Hämta AzureADApplicationProxyConnectorGroup
- Få-AzureADApplicationProxyConnectorGroupMedlemmar
- Hämta AzureADApplicationProxyConnectorMemberOf
- Nya AzureADApplicationProxyApplication
- Ny-AzureADApplicationProxyConnectorGroup
- Ta bort-AzureADApplicationProxyApplication
- Ta bort-AzureADApplicationProxyApplicationConnectorGroup
- Ta bort AzureADApplicationProxyConnectorGroup
- Set-AzureADApplicationProxyApplication
- Set-AzureADApplicationProxyApplicationConnectorGroup
- Set-AzureADApplicationProxyApplicationCustomDomainCertificate
- Set-AzureADApplicationProxyApplicationSingleSignOn
- Set-AzureADApplicationProxyConnector
- Set-AzureADApplicationProxyConnectorGroup

---
 
### <a name="new-cmdlets-to-support-configuration-of-groups"></a>Nya cmdlets som stöder konfiguration av grupper

**Typ:** Ny funktion  
**Servicekategori:** App Proxy  
**Produktkapacitet:** Plattform

Den senaste versionen av AzureAD PowerShell-modulen innehåller cmdlets för att hantera grupper i Azure AD. Dessa cmdlets var tidigare tillgängliga i AzureADPreview-modulen och läggs nu till i AzureAD-modulen

De grupp-cmdlets som nu släpps för allmän tillgänglighet är: 

- Hämta AzureADMSGroup
- Ny-AzureADMSGroup
- Ta bort AzureADMSGroup
- Ange AzureADMSGroup
- Hämta-AzureADMSGroupLifecyclePolicy
- Ny-AzureADMSGroupLifecyclePolicy
- Ta bort AzureADMSGroupLifecyclePolicy
- Lägg till AzureADMSLifecyclePolicyGroup
- Ta bort AzureADMSLifecyclePolicyGroup
- Återställ-AzureADMSLifeCycleGroup   
- Hämta AzureADMSLifecyclePolicyGroup

---
 
### <a name="a-new-release-of-azure-ad-connect-is-available"></a>En ny version av Azure AD Connect är tillgänglig

**Typ:** Ny funktion  
**Servicekategori:** AD-synkronisering  
**Produktkapacitet:** Plattform
 
Azure AD Connect är det verktyg som föredras för att synkronisera data mellan Azure AD och lokala datakällor, inklusive Windows Server Active Directory och LDAP.

>[!Important]
>Den här versionen introducerar schema- och synkroniseringsregeländringar. Azure AD Connect-synkroniseringstjänsten utlöser ett steg med fullständig import och fullständig synkronisering efter en uppgradering. Information om hur du ändrar det här beteendet finns i [Så här skjuter du upp fullständig synkronisering efter uppgraderingen](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version#how-to-defer-full-synchronization-after-upgrade).

Den här versionen har följande uppdateringar och ändringar:

**Åtgärdade problem**

- Åtgärda tidsfönstret för bakgrundsuppgifter för partitionsfiltreringssidan när du växlar till nästa sida.

- Fixade ett fel som orsakade åtkomstfel under den anpassade åtgärden ConfigDB.

- Fixade en bugg för att återställa från SQL-anslutningstidsgränsen.

- Fixade ett fel där certifikat med SAN-jokertecken misslyckas före omfrågningskontrollen.

- Fixade ett fel som orsakar att miiserver.exe kraschar under AAD-anslutningsexport.

- Fixade ett fel där ett felaktigt lösenordsförsök loggats på DC när du kör orsakade att AAD-anslutningsguiden ändrade konfigurationen

**Nya funktioner och förbättringar**
 
- Programtelemetri - Administratörer kan aktivera/inaktivera den här dataklassen.

- Azure AD Health-data - Administratörer måste besöka hälsoportalen för att kontrollera sina hälsoinställningar. När servicepolicyn har ändrats kommer agenterna att läsa och genomdriva den.

- Lade till konfigurationsåtgärder för enhetsåterskrivning och en förloppsindikator för sidinitualisering.

- Förbättrad allmän diagnostik med HTML-rapport och fullständig datainsamling i en ZIP-Text / HTML-rapport.

- Förbättrad tillförlitlighet för automatisk uppgradering och lagt till ytterligare telemetri för att säkerställa att serverns hälsa kan fastställas.

- Begränsa behörigheter som är tillgängliga för privilegierade konton på AD Connector-konto. För nya installationer begränsar guiden de behörigheter som privilegierade konton har på MSOL-kontot när MSOL-kontot har skapats. Ändringarna påverkar expressinstallationer och anpassade installationer med kontot Skapa automatiskt.

- Ändrade installationsprogrammet för att inte kräva SA-behörighet vid ren installation av AADConnect.

- Nytt verktyg för att felsöka synkroniseringsproblem för ett visst objekt. För närvarande söker verktyget efter följande:

    - UserPrincipalName matchar inte mellan synkroniserat användarobjekt och användarkontot i Azure AD-klienten.
  
    - Om objektet filtreras från synkronisering på grund av domänfiltrering
  
    - Om objektet filtreras från synkronisering på grund av organisationsenhetsfiltrering

- Nytt verktyg för att synkronisera den aktuella lösenordshã¤rinren som lagras i den lokala Active Directory för ett visst användarkonto. Verktyget kräver ingen lösenordsändring. 

---
 
### <a name="applications-supporting-intune-app-protection-policies-added-for-use-with-azure-ad-application-based-conditional-access"></a>Program som stöder Intune App Protection-principer som lagts till för användning med Azure AD-programbaserad villkorlig åtkomst

**Typ:** Ändrad funktion  
**Servicekategori:** Villkorlig åtkomst  
**Produktkapacitet:** Skydd & identitetssäkerhet

Vi har lagt till fler program som stöder programbaserad villkorlig åtkomst. Nu kan du få åtkomst till Office 365 och andra Azure AD-anslutna molnappar med hjälp av dessa godkända klientappar.

Följande ansökningar kommer att läggas till i slutet av februari:

- Microsoft Power BI

- Microsoft Launcher

- Microsoft Invoicing

Mer information finns i:

- [Krav på godkända klientappar](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)
- [Azure AD-appbaserad villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-update-to-mobile-experience"></a>Användningsvillkor uppdateras till mobil upplevelse 

**Typ:** Ändrad funktion  
**Servicekategori:** Användningsvillkor  
**Produktkapacitet:** Överensstämmelse

När användningsvillkoren visas kan du nu klicka på **Har du problem med att visa? Klicka här**. Om du klickar på den här länken öppnas användningsvillkoren internt på enheten. Oavsett teckenstorleken i dokumentet eller enhetens skärmstorlek kan du zooma och läsa dokumentet efter behov. 

---
 
## <a name="january-2018"></a>Januari 2018
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nya federerade appar finns i Azure AD-appgalleriet 

**Typ:** Ny funktion  
**Servicekategori:** Företagsappar  
**Produktkapacitet:** Integration från tredje part

I januari 2018 lades följande nya appar med federationsstöd till i appgalleriet:

[IBM OpenPages](https://go.microsoft.com/fwlink/?linkid=864698), [OneTrust Privacy Management Software](https://go.microsoft.com/fwlink/?linkid=861660), [Dealpath](https://go.microsoft.com/fwlink/?linkid=863526), [IriusRisk Federerad katalog och [Fidelity NetBenefits](https://go.microsoft.com/fwlink/?linkid=864701).

Mer information om apparna finns i [SaaS-programintegrering med Azure Active Directory](https://aka.ms/appstutorial).

Mer information om hur du listar ditt program i Azure AD-appgalleriet finns [i Lista ditt program i Azure Active Directory-programgalleriet](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---
 
### <a name="sign-in-with-additional-risk-detected"></a>Logga in med ytterligare risk upptäckt

**Typ:** Ny funktion  
**Servicekategori:** Identitetsskydd  
**Produktkapacitet:** Skydd & identitetssäkerhet

Den insikt du får för en identifierad riskidentifiering är knuten till din Azure AD-prenumeration. Med Azure AD Premium P2-utgåvan får du den mest detaljerade informationen om alla underliggande identifieringar.

Med Azure AD Premium P1-utgåvan visas identifieringar som inte omfattas av din licens som riskidentifierings inloggning med ytterligare riskidentifiering.

Mer information finns i [Azure Active Directory-riskidentifieringar](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events).
 
---

### <a name="hide-office-365-applications-from-end-users-access-panels"></a>Dölj Office 365-program från slutanvändarens åtkomstpaneler

**Typ:** Ny funktion  
**Servicekategori:** Mina appar  
**Produktkapacitet:** Sso

Nu kan du bättre hantera hur Office 365-program visas på användarens åtkomstpaneler via en ny användarinställning. Det här alternativet är användbart för att minska antalet appar i en användares åtkomstpaneler om du föredrar att bara visa Office-appar i Office-portalen. Inställningen finns i **användarinställningarna** och är märkt, **Användare kan bara se Office 365-appar i Office 365-portalen**.

Mer information finns i [Dölj ett program från användarens upplevelse i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

---
 
### <a name="seamless-sign-into-apps-enabled-for-password-sso-directly-from-apps-url"></a>Sömlös inloggning till appar som är aktiverade för Lösenord SSO direkt från appens URL 

**Typ:** Ny funktion  
**Servicekategori:** Mina appar  
**Produktkapacitet:** Sso

Webbläsartillägget Mina appar är nu tillgängligt via ett praktiskt verktyg som ger dig funktionen Mina appar med enkel inloggning som en genväg i din webbläsare. Efter installationen kommer användarens att se en våffelikon i sin webbläsare som ger dem snabb åtkomst till appar. Användare kan nu dra nytta av:

- Möjligheten att logga in direkt på lösenordsbaserade SSO-baserade appar från appens inloggningssida
- Starta alla appar med hjälp av snabbsökningsfunktionen
- Genvägar till nyligen använda appar från tillägget
- Tillägget är tillgängligt för Microsoft Edge, Chrome och Firefox.
 
Mer information finns [i Mitt program Säkert inloggningstillägg](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension).

---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>Azure AD-administrationsupplevelsen i Azure Classic Portal har dragits tillbaka

**Typ:** Deprecated   
**Servicekategori:** Azure AD  
**Produktkapacitet:** Katalog

Från och med den 8 januari 2018 har Azure AD-administrationsupplevelsen i den klassiska Azure-portalen dragits tillbaka. Detta skedde i samband med pensioneringen av azure classic portalen själv. I framtiden bör du använda [Azure AD-administrationscentret](https://aad.portal.azure.com) för all portalbaserad administration av Azure AD.
 
---

### <a name="the-phonefactor-web-portal-has-been-retired"></a>PhoneFactor-webbportalen har dragits tillbaka

**Typ:** Deprecated  
**Servicekategori:** Azure AD  
**Produktkapacitet:** Katalog
 
Från och med den 8 januari 2018 har PhoneFactors webbportal dragits tillbaka. Den här portalen användes för administration av MFA-server, men dessa funktioner har flyttats till Azure-portalen portal.azure.com. 

MFA-konfigurationen finns på: **Azure Active Directory \> MFA Server**
 
---
 
### <a name="deprecate-azure-ad-reports"></a>Inaktuella Azure AD-rapporter

**Typ:** Deprecated  
**Servicekategori:** Rapportering  
**Produktkapacitet:** Hantering av identitetslivscykeln  


Med den allmänna tillgängligheten för den nya Azure Active Directory Administration-konsolen och nya API:er som nu är tillgängliga för både aktivitets- och säkerhetsrapporter har rapport-API:erna under slutpunkten "/rapporter" dragits tillbaka i slutet av den 31 december 2017.

**Vad finns tillgängligt?**

Som en del av övergången till den nya administratörskonsolen har vi gjort två nya API:er tillgängliga för hämtning av Azure AD-aktivitetsloggar. Den nya uppsättningen API:er ger rikare filtrerings- och sorteringsfunktioner förutom att tillhandahålla rikare gransknings- och inloggningsaktiviteter. De data som tidigare var tillgängliga via säkerhetsrapporterna kan nu nås via API:et för identitetsskyddsriskidentifieringar i Microsoft Graph.

Mer information finns i:

- [Komma igång med Azure Active Directory-rapporterings-API:et](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal)

- [Komma igång med Azure Active Directory Identity Protection och Microsoft Graph](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-graph-getting-started)

---

## <a name="december-2017"></a>December 2017

### <a name="terms-of-use-in-the-access-panel"></a>Användningsvillkor i åtkomstpanelen

**Typ:** Ny funktion  
**Servicekategori:** Användningsvillkor  
**Produktkapacitet:** Överensstämmelse
 
Nu kan du gå till åtkomstpanelen och visa de användningsvillkor som du tidigare har accepterat.

Följ de här stegen:

1. Gå till [MyApps-portalen](https://myapps.microsoft.com)och logga in.

2. I det övre högra hörnet väljer du ditt namn och väljer sedan **Profil** i listan. 

3. Välj **Granska användningsvillkor**i **din profil**. 

4. Nu kan du granska användarvillkoren som du har godkänt. 

Mer information finns i [Azure AD-användningsfunktionen (förhandsversion)](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).
 
---
 
### <a name="new-azure-ad-sign-in-experience"></a>Ny Azure AD-inloggningsupplevelse

**Typ:** Ny funktion  
**Servicekategori:** Azure AD  
**Produktkapacitet:** Autentisering av användare
 
Api:et för Azure AD- och Microsoft-kontoidentitetssystemet har gjorts om så att de får ett konsekvent utseende och känsla. Dessutom samlar inloggningssidan för Azure AD in användarnamnet först, följt av autentiseringsuppgifterna på en andra skärm.

Mer information finns [i Den nya Azure AD-inloggningsupplevelsen är nu i offentlig förhandsversion](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/).
 
---
 
### <a name="fewer-sign-in-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-sign-in"></a>Färre inloggningsmeddelanden: En ny "keep me signed in"-upplevelse för Azure AD-inloggning

**Typ:** Ny funktion  
**Servicekategori:** Azure AD  
**Produktkapacitet:** Autentisering av användare
 
Kryssrutan **Håll mig inloggad** på inloggningssidan för Azure AD ersattes med en ny fråga som visas när du har autentiserats. 

Om du svarar **Ja** på den här prompten ger tjänsten dig en beständig uppdateringstoken. Det här beteendet är detsamma som när du markerade kryssrutan **Håll mig inloggad i** den gamla upplevelsen. För federerade klienter visas den här frågan när du har autentiserats med den federerade tjänsten.

Mer information finns [i Färre inloggningsuppmaningar: Den nya "keep me signed in"-upplevelsen för Azure AD är i förhandsversion](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/). 

---

### <a name="add-configuration-to-require-the-terms-of-use-to-be-expanded-prior-to-accepting"></a>Lägg till konfiguration för att kräva att användningsvillkoren ska utökas innan du accepterar

**Typ:** Ny funktion  
**Servicekategori:** Användningsvillkor  
**Produktkapacitet:** Överensstämmelse
 
Ett alternativ för administratörer kräver att användarna utökar användarvillkoren innan de accepterar villkoren.

Välj **Antingen På** eller **Av** om du vill att användarna ska utöka användningsvillkoren. **Inställningen På** kräver att användarna visar användningsvillkoren innan de accepteras.

Mer information finns i [Azure AD-användningsfunktionen (förhandsversion)](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).
 
---

### <a name="scoped-activation-for-eligible-role-assignments"></a>Begränsad aktivering för kvalificerade rolltilldelningar

**Typ:** Ny funktion  
**Servicekategori:** Privilegierad identitetshantering  
**Produktkapacitet:** Privilegierad identitetshantering
 
Du kan använda begränsad aktivering för att aktivera kvalificerade Azure-resursrolltilldelningar med mindre autonomi än de ursprungliga tilldelningsinställningarna. Ett exempel är om du är tilldelad som ägare till en prenumeration i din klientorganisation. Med begränsad aktivering kan du aktivera ägarrollen för upp till fem resurser som finns i prenumerationen (till exempel resursgrupper och virtuella datorer). Om du inaktiverar aktiveringen kan det minska risken för att oönskade ändringar i kritiska Azure-resurser körs.

Mer information finns i [Vad är Azure AD Privileged Identity Management?](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure).
 
---
 
### <a name="new-federated-apps-in-the-azure-ad-app-gallery"></a>Nya federerade appar i Azure AD-appgalleriet

**Typ:** Ny funktion  
**Servicekategori:** Företagsappar  
**Produktkapacitet:** Integration från tredje part

I december 2017 har vi lagt till dessa nya appar med federationssupport i vårt appgalleri:

[Accredible](https://go.microsoft.com/fwlink/?linkid=863523), Adobe Experience Manager, [EFI Digital StoreFront](https://go.microsoft.com/fwlink/?linkid=861685), [Communifire](https://go.microsoft.com/fwlink/?linkid=861676) CybSafe, [FactSet](https://go.microsoft.com/fwlink/?linkid=863525), [IMAGE WORKS](https://go.microsoft.com/fwlink/?linkid=863517), [MOBI](https://go.microsoft.com/fwlink/?linkid=863521), [MobileIron Azure AD integration](https://go.microsoft.com/fwlink/?linkid=858027), [Reflektive](https://go.microsoft.com/fwlink/?linkid=863518), [SAML SSO for Bamboo by resolution GmbH](https://go.microsoft.com/fwlink/?linkid=863520), [SAML SSO for Bitbucket by resolution GmbH,](https://go.microsoft.com/fwlink/?linkid=863519) [Vodeclic](https://go.microsoft.com/fwlink/?linkid=863522), WebHR, Zenegy Azure AD Integration.

Mer information om apparna finns i [SaaS-programintegrering med Azure Active Directory](https://aka.ms/appstutorial).

Mer information om hur du listar ditt program i Azure AD-appgalleriet finns [i Lista ditt program i Azure Active Directory-programgalleriet](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 
 
---

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Arbetsflöden för godkännande för Azure AD-katalogroller

**Typ:** Ändrad funktion  
**Servicekategori:** Privilegierad identitetshantering  
**Produktkapacitet:** Privilegierad identitetshantering
 
Arbetsflöde för godkännande för Azure AD-katalogroller är allmänt tillgängligt.

Med arbetsflödet för godkännande kan administratörer med privilegierade roller kräva att medlemmar med kvalificerad roll begär rollaktivering innan de kan använda den privilegierade rollen. Flera användare och grupper kan delegeras godkännandeansvar. Berättigade rollmedlemmar får meddelanden när godkännandet är klart och deras roll är aktiv.

---
 
### <a name="pass-through-authentication-skype-for-business-support"></a>Direktautentisering: Stöd för Skype för företag

**Typ:** Ändrad funktion  
**Servicekategori:** Autentiseringar (inloggningar)  
**Produktkapacitet:** Autentisering av användare

Direktautentisering stöder nu användarinloggningar till Skype för företag-klientprogram som stöder modern autentisering, vilket inkluderar online- och hybridtopologier. 

Mer information finns i [Skype för företag-topologier som stöds med modern autentisering](https://technet.microsoft.com/library/mt803262.aspx).
 
---

### <a name="updates-to-azure-ad-privileged-identity-management-for-azure-rbac-preview"></a>Uppdateringar av Azure AD Privileged Identity Management för Azure RBAC (förhandsversion)

**Typ:** Ändrad funktion  
**Servicekategori:** Privilegierad identitetshantering  
**Produktkapacitet:** Privilegierad identitetshantering
 
Med den offentliga förhandsversionen av Azure AD Privileged Identity Management (PIM) för RBAC (Azure Role-Based Access Control) kan du nu:

* Använd precis tillräckligt med administration.
* Kräv godkännande för att aktivera resursroller.
* Schemalägg en framtida aktivering av en roll som kräver godkännande för både Azure AD- och Azure RBAC-roller.
 
Mer information finns i [Privilegierad identitetshantering för Azure-resurser (förhandsversion)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---
 
## <a name="november-2017"></a>November 2017
 
### <a name="access-control-service-retirement"></a>Pensionering av åtkomstkontrolltjänsten

**Typ:** Planera för förändring  
**Servicekategori:** Tjänsten Åtkomstkontroll  
**Produktkapacitet:** Tjänsten Åtkomstkontroll 

Azure Active Directory Access Control (kallas även åtkomstkontrolltjänsten) kommer att dras tillbaka i slutet av 2018. Mer information som innehåller ett detaljerat schema och vägledning om migration på hög nivå kommer att tillhandahållas under de närmaste veckorna. Du kan lämna kommentarer på den här sidan med frågor om åtkomstkontrolltjänsten, och en gruppmedlem svarar på dem.

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>Begränsa webbläsarens åtkomst till Intune-hanterade webbläsare 

**Typ:** Planera för förändring  
**Servicekategori:** Villkorlig åtkomst  
**Produktkapacitet:** Identitetssäkerhet och identitetsskydd

Du kan begränsa webbläsarens åtkomst till Office 365 och andra Azure AD-anslutna molnappar genom att använda Intune Managed Browser som ett godkänt program. 

Du kan nu konfigurera följande villkor för programbaserad villkorlig åtkomst:

**Klientappar:** Webbläsare

**Vad är effekten av förändringen?**

Idag blockeras åtkomsten när du använder det här villkoret. När förhandsgranskningen är tillgänglig kräver all åtkomst användning av det hanterade webbläsarprogrammet. 

Leta efter den här funktionen och mer information i kommande bloggar och viktig information. 

Mer information finns [i Villkorlig åtkomst i Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal).
 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nya godkända klientappar för Azure AD-appbaserad villkorlig åtkomst

**Typ:** Planera för förändring  
**Servicekategori:** Villkorlig åtkomst  
**Produktkapacitet:** Identitetssäkerhet och identitetsskydd

Följande appar finns med i listan över [godkända klientappar:](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)
- Microsoft StaffHub

Mer information finns i:

- [Krav på godkända klientappar](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)
- [Azure AD-appbaserad villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-support-for-multiple-languages"></a>Användningsvillkor för flera språk

**Typ:** Ny funktion    
**Servicekategori:** Användningsvillkor  
**Produktkapacitet:** Överensstämmelse

Administratörer kan nu skapa nya användningsvillkor som innehåller flera PDF-dokument. Du kan tagga dessa PDF-dokument med ett motsvarande språk. Användare visas PDF med matchande språk baserat på deras preferenser. Om det inte finns någon matchning visas standardspråket.

---
 
### <a name="real-time-password-writeback-client-status"></a>Klientstatus för tillbakaskrivning av lösenord i realtid

**Typ:** Ny funktion  
**Servicekategori:** Återställning av lösenord för självbetjäning  
**Produktkapacitet:** Autentisering av användare

Nu kan du granska statusen för din lokala återställningsklient för lösenord. Det här alternativet är tillgängligt i avsnittet **Lokal integrering** på sidan Återställning av [lösenord.](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset) 

Om det finns problem med anslutningen till den lokala återskrivningsklienten visas ett felmeddelande som ger dig:

- Information om varför du inte kan ansluta till din lokala återskrivningsklient.
- En länk till dokumentation som hjälper dig att lösa problemet. 

Mer information finns i [lokal integration](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-how-it-works#on-premises-integration).

---

### <a name="azure-ad-app-based-conditional-access"></a>Azure AD-appbaserad villkorlig åtkomst 
 
**Typ:** Ny funktion  
**Servicekategori:** Azure AD  
**Produktkapacitet:** Identitetssäkerhet och identitetsskydd

Du kan nu begränsa åtkomsten till Office 365 och andra Azure AD-anslutna molnappar till [godkända klientappar](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview) som stöder Intune-appskyddsprinciper med hjälp av [Azure AD-appbaserad villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). Intune app skyddsprinciper används för att konfigurera och skydda företagsdata på dessa klientprogram.

Genom att kombinera [appbaserade](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) med [enhetsbaserade](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications) principer för villkorlig åtkomst har du flexibiliteten att skydda data för personliga enheter och företagsenheter.

Följande villkor och kontroller är nu tillgängliga för användning med appbaserad villkorlig åtkomst:

**Plattformsvillkor som stöds**

- iOS
- Android

**Villkor för klientappar**

- Mobilappar och skrivbordsklienter

**Åtkomstkontroll**

- Kräv godkänd klientapp

Mer information finns i [Azure AD-appbaserad villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access).
 
---

### <a name="manage-azure-ad-devices-in-the-azure-portal"></a>Hantera Azure AD-enheter i Azure-portalen

**Typ:** Ny funktion  
**Servicekategori:** Registrering och hantering av enheter  
**Produktkapacitet:** Identitetssäkerhet och identitetsskydd

Nu hittar du alla dina enheter som är anslutna till Azure AD och de enhetsrelaterade aktiviteterna på ett ställe. Det finns en ny administrationsupplevelse för att hantera alla dina enhetsidentiteter och inställningar i Azure-portalen. I den här versionen kan du:

- Visa alla dina enheter som är tillgängliga för villkorlig åtkomst i Azure AD.
- Visa egenskaper som inkluderar dina hybrid-Azure AD-anslutna enheter.
- Hitta BitLocker-nycklar för dina Azure AD-anslutna enheter, hantera din enhet med Intune med mera.
- Hantera Azure AD-enhetsrelaterade inställningar.

Mer information finns i [Hantera enheter med hjälp av Azure-portalen](https://docs.microsoft.com/azure/active-directory/device-management-azure-portal).

---

### <a name="support-for-macos-as-a-device-platform-for-azure-ad-conditional-access"></a>Stöd för macOS som en enhetsplattform för Azure AD Villkorlig åtkomst 

**Typ:** Ny funktion    
**Servicekategori:** Villkorlig åtkomst  
**Produktkapacitet:** Identitetssäkerhet och identitetsskydd 

Du kan nu inkludera (eller utesluta) macOS som ett enhetsplattformsvillkor i din Azure AD-princip för villkorlig åtkomst. Med tillägg av macOS till de enhetsplattformar som stöds kan du:

- **Registrera och hantera macOS-enheter med Hjälp av Intune.** I likhet med andra plattformar som iOS och Android är ett företagsportalprogram tillgängligt för macOS att göra enhetliga registreringar. Du kan använda den nya företagsportalappen för macOS för att registrera en enhet med Intune och registrera den med Azure AD.
- **Se till att macOS-enheter följer organisationens efterlevnadsprinciper som definierats i Intune.** I Intune på Azure-portalen kan du nu ställa in efterlevnadsprinciper för macOS-enheter. 
- **Begränsa åtkomsten till program i Azure AD till endast kompatibla macOS-enheter.** Principförfattare för villkorlig åtkomst har macOS som ett separat enhetsplattformsalternativ. Nu kan du skapa macOS-specifika principer för villkorlig åtkomst för den riktade programuppsättningen i Azure.

Mer information finns i:

- [Skapa en princip för enhetsefterlevnad för macOS-enheter med Intune](https://aka.ms/macoscompliancepolicy)
- [Villkorlig åtkomst för Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)
 
---

### <a name="network-policy-server-extension-for-azure-multi-factor-authentication"></a>Servertillägg för nätverk princip för Azure Multi Factor-autentisering 

**Typ:** Ny funktion    
**Servicekategori:**  Multifaktorautentisering  
**Produktkapacitet:** Autentisering av användare

Nätverksprincipservertillägget för Azure Multi-Factor Authentication lägger till molnbaserade multifaktorautentiseringsfunktioner i autentiseringsinfrastrukturen med hjälp av dina befintliga servrar. Med tillägget Nätverksprincipserver kan du lägga till telefonsamtal, sms eller telefonappverifiering i ditt befintliga autentiseringsflöde. Du behöver inte installera, konfigurera och underhålla nya servrar. 

Det här tillägget skapades för organisationer som vill skydda virtuella privata nätverksanslutningar utan att distribuera Azure Multi-Factor Authentication Server. Tillägget Network Policy Server fungerar som ett kort mellan RADIUS och molnbaserad Azure Multi-Factor-autentisering för att tillhandahålla en andra autentiseringsfaktor för federerade eller synkroniserade användare.

Mer information finns i [Integrera din befintliga infrastruktur för nätverksprincipserver med Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-nps-extension).
 
---

### <a name="restore-or-permanently-remove-deleted-users"></a>Återställa eller ta bort borttagna användare permanent

**Typ:** Ny funktion    
**Servicekategori:** Användarhantering  
**Produktkapacitet:** Katalog 

I Azure AD-administrationscentret kan du nu:

- Återställ en borttagen användare. 
- Ta bort en användare permanent.

**Så här provar du det:**

1. I administrationscentret för Azure AD väljer du [Alla användare](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All) i avsnittet **Hantera.** 

2. Välj **Nyligen borttagna användare**i listan **Visa** . 

3. Markera en eller flera nyligen borttagna användare och återställ dem eller ta bort dem permanent.
 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nya godkända klientappar för Azure AD-appbaserad villkorlig åtkomst
 
**Typ:** Ändrad funktion  
**Servicekategori:** Villkorlig åtkomst  
**Produktkapacitet:** Identitetssäkerhet och identitetsskydd

Följande appar har lagts till i listan över [godkända klientappar:](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)

- Microsoft-planerare
- Azure Information Protection 

Mer information finns i:

- [Krav på godkända klientappar](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)
- [Azure AD-appbaserad villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="use-or-between-controls-in-a-conditional-access-policy"></a>Använda "ELLER" mellan kontroller i en princip för villkorlig åtkomst 

**Typ:** Ändrad funktion    
**Servicekategori:** Villkorlig åtkomst  
**Produktkapacitet:** Identitetssäkerhet och identitetsskydd
 
Nu kan du använda "ELLER" (kräver en av de valda kontrollerna) för kontroller för villkorlig åtkomst. Du kan använda den här funktionen för att skapa principer med "ELLER" mellan åtkomstkontroller. Du kan till exempel använda den här funktionen för att skapa en princip som kräver att en användare loggar in med multifaktorautentisering "ELLER" för att vara på en kompatibel enhet.

Mer information finns [i Kontroller i Azure AD Villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-controls).
 
---

### <a name="aggregation-of-real-time-risk-detections"></a>Aggregering av riskidentifiering i realtid

**Typ:** Ändrad funktion    
**Servicekategori:** Identitetsskydd  
**Produktkapacitet:** Identitetssäkerhet och identitetsskydd

I Azure AD Identity Protection aggregeras nu alla riskidentifieringar i realtid som kommer från samma IP-adress en viss dag för varje riskidentifieringstyp. Den här ändringen begränsar volymen av riskidentifieringar som visas utan någon förändring av användarsäkerheten.

Den underliggande realtidsidentifieringen fungerar varje gång användaren loggar in. Om du har en säkerhetsprincip för inloggningsrisk inställd på multifaktorautentisering eller blockera åtkomst utlöses den fortfarande under varje riskfylld inloggning.
 
---
 
## <a name="october-2017"></a>Oktober 2017

### <a name="deprecate-azure-ad-reports"></a>Inaktuella Azure AD-rapporter

**Typ:** Planera för förändring  
**Servicekategori:** Rapportering  
**Produktkapacitet:** Hantering av identitetslivscykeln  

Azure-portalen ger dig:

- En ny Azure AD-administrationskonsol.
- Nya API:er för aktivitets- och säkerhetsrapporter.
 
På grund av dessa nya funktioner drogs rapport-API:erna under slutpunkten /rapporter tillbaka den 10 december 2017. 

---

### <a name="automatic-sign-in-field-detection"></a>Automatisk identifiering av inloggningsfält

**Typ:** Fast   
**Servicekategori:** Mina appar  
**Produktkapacitet:** Enkel inloggning  

Azure AD stöder automatisk inloggningsfältidentifiering för program som återger ett HTML-användarnamn och lösenordsfält. De här stegen dokumenteras i [Så här samlar du automatiskt in inloggningsfält för ett program](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-password-single-sign-on-non-gallery-applications-problems#manually-capture-sign-in-fields-for-an-app). Du hittar den här funktionen genom att lägga till ett program *som inte är galleri* på sidan Enterprise **Applications** i [Azure-portalen](https://aad.portal.azure.com). Dessutom kan du konfigurera **läget Enkel inloggning** på det nya programmet till **Lösenordsbaserad enkel inloggning,** ange en webb-URL och sedan spara sidan.
 
På grund av ett serviceproblem inaktiverades den här funktionen tillfälligt. Problemet löstes och den automatiska inloggningsfältidentifieringen är tillgänglig igen.

---

### <a name="new-multi-factor-authentication-features"></a>Nya multifaktorautentiseringsfunktioner

**Typ:** Ny funktion  
**Servicekategori:** Multifaktorautentisering  
**Produktkapacitet:** Identitetssäkerhet och identitetsskydd  

MFA (Multifaktorautentisering) är en viktig del av att skydda din organisation. För att göra autentiseringsuppgifterna mer anpassningsbara och upplevelsen mer sömlös har följande funktioner lagts till: 

- Multifaktorutmaningsresultat är direkt integrerade i Azure AD-inloggningsrapporten, som innehåller programmatisk åtkomst till MFA-resultat.
- MFA-konfigurationen är mer integrerad i Azure AD-konfigurationsupplevelsen i Azure-portalen.

Med den här offentliga förhandsversionen är MFA-hantering och rapportering en integrerad del av azure AD-konfigurationsupplevelsen. Nu kan du hantera MFA-hanteringsportalfunktionen i Azure AD-upplevelsen.

Mer information finns [i Referens för MFA-rapportering i Azure-portalen](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins-mfa). 

---

### <a name="terms-of-use"></a>Användningsvillkor

**Typ:** Ny funktion  
**Servicekategori:** Användningsvillkor  
**Produktkapacitet:** Överensstämmelse  

Du kan använda Azure AD-användningsvillkor för att presentera information som relevanta ansvarsfriskrivningar för juridiska krav eller efterlevnadskrav för användare.

Du kan använda Azure AD-användningsvillkor i följande scenarier:

- Allmänna användningsvillkor för alla användare i organisationen
- Specifika användningsvillkor baserade på en användares attribut (till exempel läkare kontra sjuksköterskor eller inhemska kontra internationella anställda, som utförs av dynamiska grupper)
- Specifika användningsvillkor för åtkomst till affärsappar med stor genomslagskraft, till exempel Salesforce

Mer information finns i [Azure AD-användarvillkoren](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="enhancements-to-privileged-identity-management"></a>Förbättringar av privilegierad identitetshantering

**Typ:** Ny funktion  
**Servicekategori:** Privilegierad identitetshantering  
**Produktkapacitet:** Privilegierad identitetshantering  

Med Azure AD Privileged Identity Management kan du hantera, kontrollera och övervaka åtkomsten till Azure-resurser (förhandsversion) inom organisationen för att:

- Prenumerationer
- Resursgrupper
- Virtuella datorer 

Alla resurser i Azure-portalen som använder Azure RBAC-funktionen kan dra nytta av alla funktioner för säkerhets- och livscykelhantering som Azure AD Privileged Identity Management har att erbjuda.

Mer information finns i [Privilegierad identitetshantering för Azure-resurser](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---

### <a name="access-reviews"></a>Åtkomstgranskningar

**Typ:** Ny funktion  
**Servicekategori:** Access recensioner  
**Produktkapacitet:** Överensstämmelse  

Organisationer kan använda åtkomstgranskningar (förhandsversioner) för att effektivt hantera gruppmedlemskap och åtkomst till företagsprogram: 

- Du kan certifiera om gästanvändares åtkomst med åtkomstgranskningar av deras åtkomst till program och medlemskap i grupper. Granskare kan effektivt bestämma om du vill tillåta gäster fortsatt åtkomst baserat på de insikter som tillhandahålls av åtkomstgranskningarna.
- Du kan certifiera om medarbetares åtkomst till program och gruppmedlemskap med åtkomstgranskningar.

Du kan samla in åtkomstgranskningskontroller i program som är relevanta för din organisation för att spåra granskningar för efterlevnad eller riskkänsliga program.

Mer information finns i [Azure AD-åtkomstgranskningar](https://docs.microsoft.com/azure/active-directory/active-directory-azure-ad-controls-access-reviews-overview).

---

### <a name="hide-third-party-applications-from-my-apps-and-the-office-365-app-launcher"></a>Dölj program från tredje part från Mina program och startprogrammet för Office 365

**Typ:** Ny funktion  
**Servicekategori:** Mina appar  
**Produktkapacitet:** Enkel inloggning  

Nu kan du bättre hantera appar som visas på användarnas portaler via en ny **dölj-appegenskap.** Du kan dölja appar som kan hjälpa dig i fall där apppaneler visas för backend-tjänster eller dubblettpaneler och skräpar upp användarnas appstartprogram. Växlingsknappen finns i avsnittet **Egenskaper** i tredjepartsappen och är märkt **synlig för användaren?** Du kan också dölja en app programmässigt via PowerShell. 

Mer information finns [i Dölj ett program från tredje part från en användares upplevelse i Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app). 


**Vad finns tillgängligt?**

 Som en del av övergången till den nya administratörskonsolen finns två nya API:er för att hämta Azure AD-aktivitetsloggar tillgängliga. Den nya uppsättningen API:er ger rikare filtrerings- och sorteringsfunktioner förutom att tillhandahålla rikare gransknings- och inloggningsaktiviteter. De data som tidigare var tillgängliga via säkerhetsrapporterna kan nu nås via API:et för identitetsskyddsriskidentifieringar i Microsoft Graph.


## <a name="september-2017"></a>September 2017

### <a name="hotfix-for-identity-manager"></a>Snabbkorrigering för Identity Manager

**Typ:** Ändrad funktion  
**Servicekategori:** Identitetshanterare  
**Produktkapacitet:** Hantering av identitetslivscykeln  

Ett samlad snabbkorrigeringspaket (version 4.4.1642.0) är tillgängligt från och med den 25 september 2017 för Identity Manager 2016 Service Pack 1. Detta sammanslagningspaket:

- Löser problem och lägger till förbättringar.
- Är en kumulativ uppdatering som ersätter alla Uppdateringar för Identity Manager 2016 Service Pack 1 upp till build 4.4.1459.0 för Identity Manager 2016. 
- Kräver att du har Identity Manager 2016 build 4.4.1302.0. 

Mer information finns i [Samlad snabbkorrigeringspaket (version 4.4.1642.0) är tillgängligt för Identity Manager 2016 Service Pack 1](https://support.microsoft.com/help/4021562). 

---
