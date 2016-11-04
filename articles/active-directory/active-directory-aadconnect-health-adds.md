
---
title: Använda Azure AD Connect Health med AD DS | Microsoft Docs
description: Den här sidan om Azure AD Connect Health innehåller information om hur du övervakar AD DS.
services: active-directory
documentationcenter: ''
author: arluca
manager: samueld
editor: curtand

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/30/2016
ms.author: arluca

---
# Använda Azure AD Connect Health med AD DS
Följande dokumentation är specifik för övervakning av Active Directory Domain Services med Azure AD Connect Health. Versioner av AD DS som stöds är: Windows Server 2008 R2, Windows Server 2012 och Windows Server 2012 R2.

Mer information om övervakning av AD FS med Azure AD Connect Health finns i [ Använda Azure AD Connect Health med AD FS](active-directory-aadconnect-health-adfs.md). Mer information om övervakning av Azure AD Connect (Sync) med Azure AD Connect Health finns i [Använda Azure AD Connect Health för synkronisering](active-directory-aadconnect-health-sync.md).

![Azure AD Connect Health för AD DS](./media/active-directory-aadconnect-health/aadconnect-health-adds-entry.png)

## Aviseringar för Azure AD Connect Health för AD DS
Avsnittet Aviseringar i Azure AD Connect Health för AD DS innehåller en lista över aktiva och lösta aviseringar relaterade till dina domänkontrollanter. När du väljer en aktiv eller löst avisering öppnas ett nytt blad med ytterligare information. Det innehåller lösningssteg och länkar till stöddokumentation. Varje typ av avisering kan ha en eller flera instanser som motsvarar var och en av de domänkontrollanter som berörs av den specifika aviseringen. Du kan dubbelklicka på en domänkontrollant längst ner på aviseringsbladet om du vill öppna ytterligare ett blad med mer information om aviseringen.

På det här bladet kan du aktivera e-postmeddelanden för aviseringar och ändra tidsintervallet för vyn. Om du utökar tidsintervallet kan du se tidigare lösta aviseringar.

![Azure AD Connect-synkroniseringsfel](./media/active-directory-aadconnect-health/aadconnect-health-adds-alerts.png)

## Instrumentpanel för domänkontrollanter
Den här instrumentpanelen innehåller en topologisk vy över din miljö, tillsammans med operativa nyckelvärden och hälsostatus för var och en av dina övervakade domänkontrollanter. De presenterade mätvärdena hjälper dig att snabbt identifiera domänkontrollanter som kan kräva ytterligare undersökning. Som standard visas endast en delmängd av kolumnerna. Du kan hitta samtliga tillgängliga kolumner genom att dubbelklicka på kommandot kolumner. Genom att markera de kolumner du är intresserad av omvandlas intrumentpanelen till en enkel, smidig plats där du kan övervaka AD DS-miljöns hälsa. 

![Domänkontrollanter](./media/active-directory-aadconnect-health/aadconnect-health-adds-domainsandsites-dashboard.png)

Domänkontrollanter kan grupperas efter deras respektive domän eller plats, vilket är användbart för att förstå miljöns topologi. Slutligen, om du dubbelklickar på bladhuvudet maximeras instrumentpanelen så att du kan använda hela skärmens utrymme. Den här större vyn är användbar när flera kolumner visas. 

## Instrumentpanelen Replikeringsstatus
Den här instrumentpanelen innehåller en vy över replikeringsstatusen och replikeringstopologin för de övervakade domänkontrollanterna. Statusen för det senaste replikeringsförsöket anges tillsammans med användbar dokumentation för eventuella fel som hittas. Du kan dubbelklicka på en domänkontrollant med ett fel för att öppna ett nytt blad med information, såsom: information om felet, rekommenderade lösningssteg och länkar till felsökningsdokumentation. 

![Replikeringsstatus](./media/active-directory-aadconnect-health/aadconnect-health-adds-replication.png)

## Övervakning
Den här funktionen visar grafiska trender hos olika prestandaräknare som samlas in kontinuerligt från varje övervakad domänkontrollant. En domänkontrollants prestanda kan enkelt jämföras med alla övriga övervakade domänkontrollanter i skogen. Dessutom visas olika prestandaräknare sida vid sida, vilket är användbart när du felsöker problem i din miljö. 

![Övervakning](./media/active-directory-aadconnect-health/aadconnect-health-adds-monitoring.png)

Som standard har vi förvalt fyra prestandaräknare. Du kan dock inkludera andra genom att klicka på filterkommandot och markera eller avmarkera alla önskade prestandaräknare. Dessutom kan du dubbelklicka på ett diagram för en prestandaräknare för att öppna ett nytt blad som innehåller datapunkter för alla övervakade domänkontrollanter.

## Relaterade länkar
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Installation av Azure AD Connect Health Agent](active-directory-aadconnect-health-agent-install.md)
* [Azure AD Connect Health-åtgärder](active-directory-aadconnect-health-operations.md)
* [Använda Azure AD Connect Health med AD FS](active-directory-aadconnect-health-adfs.md)
* [Använda Azure AD Connect Health för synkronisering](active-directory-aadconnect-health-sync.md)
* [Vanliga frågor och svar om Azure AD Connect Health](active-directory-aadconnect-health-faq.md)
* [Versionshistorik för Azure AD Connect Health](active-directory-aadconnect-health-version-history.md)

<!--HONumber=Oct16_HO1-->


