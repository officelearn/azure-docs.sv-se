
<properties
    pageTitle="Använda Azure AD Connect Health med synkronisering | Microsoft Azure"
    description="Den här sidan om Azure AD Connect Health innehåller information om hur du övervakar Azure AD Connect-synkronisering."
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/12/2016"
    ms.author="billmath"/>

# Använda Azure AD Connect Health för synkronisering
Följande dokumentation är specifik för övervakning av Azure AD Connect-synkronisering med Azure AD Connect Health.  Information om övervakning av AD FS med Azure AD Connect Health finns i [Använda Azure AD Connect Health med AD FS](active-directory-aadconnect-health-adfs.md).

![Azure AD Connect Health för synkronisering](./media/active-directory-aadconnect-health-sync/sync.png)

## Aviseringar för Azure AD Connect Health för synkronisering
Avsnittet om Azure AD Connect Health-aviseringar för synkronisering innehåller en lista över aktiva aviseringar. Varje avisering innehåller relevant information, lösningssteg och länkar till relaterad dokumentation. Om du väljer en aktiv eller åtgärdad avisering visas ett nytt blad med ytterligare information, samt de steg som du kan utföra för att åtgärda aviseringen liksom länkar till ytterligare dokumentation. Du kan också visa historiska data för tidigare åtgärdade aviseringar.

Om du väljer en avisering visas ytterligare information samt de steg som du kan utföra för att åtgärda aviseringen liksom länkar till ytterligare dokumentation.

![Azure AD Connect-synkroniseringsfel](./media/active-directory-aadconnect-health-sync/alert.png)

### Begränsad utvärdering av aviseringar
Om Azure AD Connect INTE använder standardkonfigurationen (till exempel om attributfiltrering ändras från standardkonfigurationen till en anpassad konfiguration) överför inte Azure AD Connect Health-agenten felhändelser relaterade till Azure AD Connect. 

Detta begränsar tjänstens utvärdering av aviseringar. En banderoll visas som anger det här tillståndet på Azure Portal under din tjänst.

![Azure AD Connect Health för synkronisering](./media/active-directory-aadconnect-health-sync/banner.png)

Du kan ändra detta genom att klicka på Inställningar och låta Azure AD Connect Health-agenten överföra alla felloggarna.

![Azure AD Connect Health för synkronisering](./media/active-directory-aadconnect-health-sync/banner2.png)

## Synkronisera Insight
Med den senaste versionen av Azure AD Connect Health för synkronisering har följande nya funktioner lagts till:

- Svarstiden för synkroniseringsåtgärder
- Objektändringstrend

### Synkronisera svarstider
Den här funktionen visar en grafisk trend över svarstiderna för synkroniseringsåtgärder (import, export osv.) för anslutningsappar.  Detta är ett snabbt och enkelt sätt att förstå inte bara svarstiderna för dina åtgärder (vilket är bra om många ändringar äger rum), men är också ett sätt att identifiera avvikelser i svarstiderna som kan kräva ytterligare utredning.

![Synkronisera svarstider](./media/active-directory-aadconnect-health-sync/synclatency.png)

Som standard visas endast svarstider för exportåtgärden för Azure AD-anslutningsappen.  Om du vill visa fler åtgärder för anslutningsappen eller om du vill visa åtgärder från andra anslutningsappar högerklickar du på diagrammet och välj den specifika åtgärden och anslutningsappen.

### Synkronisera objektändringar
Den här funktion visar en grafisk trend över antalet ändringar som utvärderas och exporteras till Azure AD.  I dag är det svårt att försöka samla in den här informationen från synkroniseringsloggarna.  Diagrammet är inte bara ett enklare sätt att övervaka antalet ändringar som äger rum i din miljö, utan tillhandahåller också en visuell översikt över de fel som uppstår.

![Synkronisera svarstider](./media/active-directory-aadconnect-health-sync/syncobjectchanges.png)

## Relaterade länkar

* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Installation av Azure AD Connect Health Agent](active-directory-aadconnect-health-agent-install.md)
* [Azure AD Connect Health-åtgärder](active-directory-aadconnect-health-operations.md)
* [Använda Azure AD Connect Health med AD FS](active-directory-aadconnect-health-adfs.md)
* [Vanliga frågor och svar om Azure AD Connect Health](active-directory-aadconnect-health-faq.md)
* [Versionshistorik för Azure AD Connect Health](active-directory-aadconnect-health-version-history.md)




<!--HONumber=Jun16_HO2-->


