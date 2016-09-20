
<properties
    pageTitle="Använda Azure AD Connect Health med AD DS | Microsoft Azure"
    description="Den här sidan om Azure AD Connect Health innehåller information om hur du övervakar AD DS."
    services="active-directory"
    documentationCenter=""
    authors="arluca"
    manager="samueld"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/14/2016"
    ms.author="arluca"/>

# Använda Azure AD Connect Health med AD DS
Följande dokumentation är specifik för övervakning av Active Directory Domain Services med Azure AD Connect Health. Detta inkluderar AD DS installerat på Windows Server 2008 R2, Windows Server 2012 och Windows Server 2012 R2.

Information om övervakning av AD FS med Azure AD Connect Health finns i [Använda Azure AD Connect Health med AD FS](active-directory-aadconnect-health-adfs.md). Mer information om övervakning av Azure AD Connect (Sync) med Azure AD Connect Health finns i [Använda Azure AD Connect Health för synkronisering](active-directory-aadconnect-health-sync.md).

![Azure AD Connect Health för AD DS](./media/active-directory-aadconnect-health/aadconnect-health-adds-entry.png)

## Aviseringar för Azure AD Connect Health för AD DS
Avsnittet Aviseringar i Azure AD Connect Health för AD DS innehåller en lista över aktiva och lösta aviseringar relaterade till dina domänkontrollanter. Om du markerar en avisering som är aktiv eller löst, öppnas ett nytt blad med ytterligare information, tillsammans med lösningssteg och länkar till stöddokumentation. Varje typ av avisering kan ha en eller flera instanser som motsvarar var och en av de domänkontrollanter som berörs av den specifika aviseringen. Nästan längst ned på aviseringsbladet kan du välja en berörd domänkontrollant, varpå ett nytt blad öppnas med ytterligare information om den specifika aviseringsinstansen.

På det här bladet kan du aktivera e-postmeddelanden för aviseringar och ändra tidsintervallet i vyn. Genom att expandera tidsintervallet kan du visa tidigare lösta aviseringar.

![Azure AD Connect-synkroniseringsfel](./media/active-directory-aadconnect-health/aadconnect-health-adds-alerts.png)

## Domänkontrollanter
Den här instrumentpanelen innehåller en topologisk vy över din miljö, tillsammans med operativa nyckelvärden och hälsostatus för var och en av dina övervakade domänkontrollanter. De presenterade mätvärdena hjälper dig att snabbt identifiera domänkontrollanter som kan kräva ytterligare undersökning. Som standard visas bara en delmängd med kolumner men genom att klicka på kolumnkommandot kan du visa hela den tillgängliga uppsättningen med kolumner. Om du markerar de kolumner som du är mest intresserad av, blir den här instrumentpanelen till en lättöverskådlig plats för att visa hälsotillståndet för din AD DS-miljö. 

![Domänkontrollanter](./media/active-directory-aadconnect-health/aadconnect-health-adds-domainsandsites-dashboard.png)

Domänkontrollanter kan grupperas efter respektive domän eller plats, vilket är användbart för att förstå miljötopologin. Om du dubbelklickar på bladrubriken maximerar du slutligen också instrumentpanelen och kan utnyttja tillgängliga funktioner på skärmen. Detta kan särskilt användbart när du visar flera kolumner. 

## Replikeringsstatus
Den här instrumentpanelen innehåller en vy över replikeringsstatus och replikeringstopologi för dina övervakade domänkontrollanter. Statusen för det senaste replikeringsförsöket anges tillsammans med användbar dokumentation för eventuella fel som hittas. Om du markerar en domänkontrollant med ett fel, öppnas ett nytt blad med ytterligare information, tillsammans med lösningssteg och länkar till felsökningsdokumentation. 

![Replikeringsstatus](./media/active-directory-aadconnect-health/aadconnect-health-adds-replication.png)

## Övervakning
Denna funktion tillhandahåller grafiska trender för olika prestandaräknare som kontinuerligt samlas in från var och en av de övervakade domänkontrollanterna. En domänkontrollants prestanda kan enkelt jämföras med alla övriga övervakade domänkontrollanter i skogen. Dessutom visas olika prestandaräknare sida vid sida, vilket är användbart när du felsöker problem i din miljö. 

![Övervakning](./media/active-directory-aadconnect-health/aadconnect-health-adds-monitoring.png)

Som standard har vi förvalt fyra prestandaräknare. Du kan dock inkludera andra genom att klicka på filterkommandot och markera eller avmarkera alla önskade prestandaräknare. Dessutom öppnas ett nytt blad, som innehåller respektive datapunkter för var och en av de övervakade domänkontrollanterna, om du klickar på en viss prestandadräknargraf.

## Relaterade länkar

* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Installation av Azure AD Connect Health Agent](active-directory-aadconnect-health-agent-install.md)
* [Azure AD Connect Health-åtgärder](active-directory-aadconnect-health-operations.md)
* [Använda Azure AD Connect Health med AD FS](active-directory-aadconnect-health-adfs.md)
* [Använda Azure AD Connect Health för synkronisering](active-directory-aadconnect-health-sync.md)
* [Vanliga frågor och svar om Azure AD Connect Health](active-directory-aadconnect-health-faq.md)
* [Versionshistorik för Azure AD Connect Health](active-directory-aadconnect-health-version-history.md)



<!--HONumber=sep16_HO1-->


