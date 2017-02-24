---
title: "Använda Azure AD Connect Health med synkronisering | Microsoft Docs"
description: "Den här sidan om Azure AD Connect Health innehåller information om hur du övervakar Azure AD Connect-synkronisering."
services: active-directory
documentationcenter: 
author: karavar
manager: samueld
editor: curtand
ms.assetid: 1dfbeaba-bda2-4f68-ac89-1dbfaf5b4015
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/10/2017
ms.author: vakarand
translationtype: Human Translation
ms.sourcegitcommit: 21e599fc2adf3d7dce84cc1c4130504b71928db7
ms.openlocfilehash: 7a2f53de0067879a811c9954199026ecfe3ec906


---
# <a name="using-azure-ad-connect-health-for-sync"></a>Använda Azure AD Connect Health för synkronisering
Följande dokumentation är specifik för övervakning av Azure AD Connect (Sync) med Azure AD Connect Health.  Information om övervakning av AD FS med Azure AD Connect Health finns i [Använda Azure AD Connect Health med AD FS](active-directory-aadconnect-health-adfs.md). Mer information om övervakning av Active Directory Domain Services med Azure AD Connect Health finns i [Använda Azure AD Connect Health med AD DS](active-directory-aadconnect-health-adds.md).

![Azure AD Connect Health för synkronisering](./media/active-directory-aadconnect-health-sync/sync-blade.png)

## <a name="alerts-for-azure-ad-connect-health-for-sync"></a>Aviseringar för Azure AD Connect Health för synkronisering
Avsnittet om Azure AD Connect Health-aviseringar för synkronisering innehåller en lista över aktiva aviseringar. Varje avisering innehåller relevant information, lösningssteg och länkar till relaterad dokumentation. Om du väljer en aktiv eller åtgärdad avisering visas ett nytt blad med ytterligare information, samt de steg som du kan utföra för att åtgärda aviseringen liksom länkar till ytterligare dokumentation. Du kan också visa historiska data för tidigare åtgärdade aviseringar.

Om du väljer en avisering visas ytterligare information samt de steg som du kan utföra för att åtgärda aviseringen liksom länkar till ytterligare dokumentation.

![Azure AD Connect-synkroniseringsfel](./media/active-directory-aadconnect-health-sync/alert.png)

### <a name="limited-evaluation-of-alerts"></a>Begränsad utvärdering av aviseringar
Om Azure AD Connect INTE använder standardkonfigurationen (till exempel om attributfiltrering ändras från standardkonfigurationen till en anpassad konfiguration) överför inte Azure AD Connect Health-agenten felhändelser relaterade till Azure AD Connect.

Detta begränsar tjänstens utvärdering av aviseringar. En banderoll visas som anger det här tillståndet på Azure Portal under din tjänst.

![Azure AD Connect Health för synkronisering](./media/active-directory-aadconnect-health-sync/banner.png)

Du kan ändra detta genom att klicka på Inställningar och låta Azure AD Connect Health-agenten överföra alla felloggarna.

![Azure AD Connect Health för synkronisering](./media/active-directory-aadconnect-health-sync/banner2.png)

## <a name="sync-insight"></a>Synkronisera Insight
Administratörer vill ofta veta hur lång tid det tar att synkronisera ändringar av Azure AD och mängden ändringar som sker. Den här funktionen innehåller ett enkelt sätt att visualisera detta med hjälp av diagrammet nedan:   

* Svarstiden för synkroniseringsåtgärder
* Objektändringstrend

### <a name="sync-latency"></a>Synkronisera svarstider
Den här funktionen visar en grafisk trend över svarstiderna för synkroniseringsåtgärder (import, export osv.) för anslutningsappar.  Detta är ett snabbt och enkelt sätt att förstå inte bara svarstiderna för dina åtgärder (vilket är bra om många ändringar äger rum), men är också ett sätt att identifiera avvikelser i svarstiderna som kan kräva ytterligare utredning.

![Synkronisera svarstider](./media/active-directory-aadconnect-health-sync/synclatency02.png)

Som standard visas endast svarstider för exportåtgärden för Azure AD-anslutningsappen.  Om du vill visa fler åtgärder för anslutningsappen eller om du vill visa åtgärder från andra anslutningsappar högerklickar du på diagrammet, välj Redigera diagram eller klicka på knappen ”Redigera svarstidsdiagram” och välj den specifika åtgärden och anslutningsapparna.

### <a name="sync-object-changes"></a>Synkronisera objektändringar
Den här funktion visar en grafisk trend över antalet ändringar som utvärderas och exporteras till Azure AD.  I dag är det svårt att försöka samla in den här informationen från synkroniseringsloggarna.  Diagrammet är inte bara ett enklare sätt att övervaka antalet ändringar som äger rum i din miljö, utan tillhandahåller också en visuell översikt över de fel som uppstår.

![Synkronisera svarstider](./media/active-directory-aadconnect-health-sync/syncobjectchanges02.png)

## <a name="object-level-synchronization-error-report-preview"></a>Felrapport för synkronisering på objektnivå (förhandsgranskning)
Denna funktion tillhandahåller en rapport om synkroniseringsfel som kan uppstå när identitetsdata synkroniseras mellan Windows Server AD och Azure AD med Azure AD Connect.

* Rapporten avser fel registrerade av synkroniseringsklienten (Azure AD Connect version 1.1.281.0 eller senare)
* Den innehåller de fel som uppstod under den senaste synkroniseringen på synkroniseringsmotorn. (”Export” i Azure AD Connector.)
* Azure AD Connect Health-agenten för synkronisering måste ha en utgående anslutning till de nödvändiga slutpunkterna för rapporten för att inkludera den senaste informationen.
* Rapporten **uppdateras efter var 30:e minut** med hjälp av data som överförts av Azure AD Connect Health-agenten för synkronisering.
  Det ger följande viktiga funktioner

  * Kategorisering av fel
  * Lista över objekt med fel per kategori
  * Alla data om felen på en plats
  * Jämförelse sida vid sida av objekt med fel på grund av en konflikt
  * Hämta felrapporten som en CVS-fil (kommer snart)

### <a name="categorization-of-errors"></a>Kategorisering av fel
Rapporten kategoriserar befintliga synkroniseringsfel i följande kategorier:

| Kategori | Beskrivning |
| --- | --- |
| Duplicerat attribut |Fel när Azure AD Connect försöker skapa eller uppdatera objekt med dubblerade värden av ett eller flera attribut i Azure AD som måste vara unika i en klient, till exempel proxyAddresses, UserPrincipalName. |
| Felmatchning av data |Fel när en ungefärlig matchning inte matchar objekt som kan leda till problem med synkronisering. |
| Verifieringsfel för data |Fel på grund av ogiltiga data, till exempel tecken som inte stöds i viktiga attribut såsom UserPrincipalName, formatfel som inte kan valideras innan de skrivs i Azure AD. |
| Stora attribut |Fel när ett eller flera attribut är större än den tillåtna storleken, längden eller antalet. |
| Annat |Alla andra fel som inte passar in i ovanstående kategorier. Baserat på feedback, kommer den här kategorin att delas upp i underkategorier. |

![ Rapportsammanfattning för synkroniseringsfel](./media/active-directory-aadconnect-health-sync/errorreport01.png)
![Rapportkategorier för synkroniseringsfel](./media/active-directory-aadconnect-health-sync/errorreport02.png)

### <a name="list-of-objects-with-error-per-category"></a>Lista över objekt med fel per kategori
Att gå djupare in i varje kategori kommer att ge listan över objekt som har fel i kategorin.
![Rapportlista för synkroniseringsfel](./media/active-directory-aadconnect-health-sync/errorreport03.png)

### <a name="error-details"></a>Information om fel
Följande data är tillgängliga i den detaljerade vyn för varje fel

* Identifierare för inblandade *AD-objekt*
* Identifierare för inblandade *Azure AD-objekt* (som tillämpligt)
* Felbeskrivning och hur du åtgärdar
* Relaterade artiklar

![Rapportdetaljer för synkroniseringsfel](./media/active-directory-aadconnect-health-sync/errorreport04.png)

### <a name="download-the-error-report-as-csv"></a>Hämta felrapporten som CSV
Du kan hämta en CSV-fil med all information om felen genom att klicka på Exportera.

## <a name="related-links"></a>Relaterade länkar
* [Felsöka fel under synkronisering](../connect/active-directory-aadconnect-troubleshoot-sync-errors.md)
* [Återhämtning av duplicerat attribut](../connect/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md)
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Installation av Azure AD Connect Health Agent](active-directory-aadconnect-health-agent-install.md)
* [Azure AD Connect Health-åtgärder](active-directory-aadconnect-health-operations.md)
* [Använda Azure AD Connect Health med AD FS](active-directory-aadconnect-health-adfs.md)
* [Använda Azure AD Connect Health med AD DS](active-directory-aadconnect-health-adds.md)
* [Vanliga frågor och svar om Azure AD Connect Health](active-directory-aadconnect-health-faq.md)
* [Versionshistorik för Azure AD Connect Health](active-directory-aadconnect-health-version-history.md)


<!--HONumber=Feb17_HO2-->


