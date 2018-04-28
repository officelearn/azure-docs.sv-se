---
title: 'Azure AD Connect: Topologier som stöds | Microsoft Docs'
description: Det här avsnittet beskrivs topologier som stöds respektive inte stöds för Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 1034c000-59f2-4fc8-8137-2416fa5e4bfe
ms.service: active-directory
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.topic: article
ms.date: 02/27/2018
ms.author: billmath
ms.openlocfilehash: f47cf18f70572ad93f5075c2f2c883d80af8220e
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
---
# <a name="topologies-for-azure-ad-connect"></a>Topologier för Azure AD Connect
Den här artikeln beskrivs olika lokalt och Azure Active Directory (Azure AD)-topologier som använder Azure AD Connect-synkronisering som viktiga integrationslösning. Den här artikeln innehåller både stöds och som inte stöds.

Här är förklaring för bilder i artikeln:

| Beskrivning | Symbol |
| --- | --- |
| Lokala Active Directory-skog |![Lokala Active Directory-skog](./media/active-directory-aadconnect-topologies/LegendAD1.png) |
| Lokala Active Directory med filtrerade import |![Active Directory med filtrerade import](./media/active-directory-aadconnect-topologies/LegendAD2.png) |
| Azure AD Connect sync-servern |![Azure AD Connect sync-servern](./media/active-directory-aadconnect-topologies/LegendSync1.png) |
| Azure AD Connect sync-servern ”mellanlagringsläge” |![Azure AD Connect sync-servern ”mellanlagringsläge”](./media/active-directory-aadconnect-topologies/LegendSync2.png) |
| GALSync med Forefront Identity Manager (FIM) 2010 eller Microsoft Identity Manager (MIM) 2016 |![GALSync med FIM 2010 eller MIM 2016](./media/active-directory-aadconnect-topologies/LegendSync3.png) |
| Azure AD Connect sync-servern, detaljerad |![Azure AD Connect sync-servern, detaljerad](./media/active-directory-aadconnect-topologies/LegendSync4.png) |
| Azure AD |![Azure Active Directory](./media/active-directory-aadconnect-topologies/LegendAAD.png) |
| Scenario som inte stöds |![Scenario som inte stöds](./media/active-directory-aadconnect-topologies/LegendUnsupported.png) |


> [!IMPORTANT]
> Microsoft stöder inte ändra eller operativsystemet Azure AD Connect-synkronisering utanför konfigurationer eller åtgärder som dokumenteras formellt. Något av dessa konfigurationer eller åtgärder kan resultera i tillståndet inkonsekvent eller som inte stöds av Azure AD Connect-synkronisering. Därför kan Microsoft inte tillhandahålla teknisk support för sådana distributioner.


## <a name="single-forest-single-azure-ad-tenant"></a>Enkel skog, ett Azure AD-klient
![Topologi för en enda skog och en enskild klient](./media/active-directory-aadconnect-topologies/SingleForestSingleDirectory.png)

Den vanligaste topologin är en enda lokal skog, med en eller flera domäner och en enda Azure AD-klient. Synkronisering av lösenord används för Azure AD-autentisering. Snabbinstallation av Azure AD Connect har stöd för den här topologin.

### <a name="single-forest-multiple-sync-servers-to-one-azure-ad-tenant"></a>Enkel skog, flera synkroniseringsservrar till en Azure AD-klient
![Filtrerade topologi i stöds inte för en enskild skog](./media/active-directory-aadconnect-topologies/SingleForestFilteredUnsupported.png)

Att ha flera servrar för Azure AD Connect sync anslutna till samma Azure AD-klienten inte stöds, utom för en [mellanlagring server](#staging-server). Det har stöds inte även om servrarna är konfigurerade för att synkronisera med en uppsättning objekt som inte anges samtidigt. Du har anses den här topologin om du inte kan nå alla domäner i skogen från en enskild server, eller om du vill fördela belastningen över flera servrar.

## <a name="multiple-forests-single-azure-ad-tenant"></a>Flera skogar, enkel Azure AD-klient
![Topologi för flera skogar och en enskild klient](./media/active-directory-aadconnect-topologies/MultiForestSingleDirectory.png)

Många organisationer har miljöer med flera lokala Active Directory-skogar. Det finns flera orsaker till att ha fler än en lokal Active Directory-skog. Vanliga exempel är med kontot resursskogar och resultatet av en sammanslagning eller förvärv.

När du har flera skogar, alla skogar måste kunna nås av en enskild server för Azure AD Connect-synkronisering. Du behöver ansluta servern till en domän. Om det är nödvändigt för att nå alla skogar, kan du placera servern i ett perimeternätverk (även kallat DMZ, demilitariserad zon och avskärmat undernät).

Installationsguiden för Azure AD Connect erbjuder flera alternativ för att konsolidera användare som representeras i flera skogar. Målet är att en användare representeras endast en gång i Azure AD. Det finns några vanliga topologier som du kan konfigurera i den anpassade installationssökvägen i installationsguiden. På den **identifiera användarna unikt** markerar du motsvarande alternativ som representerar din topologi. Konsolidering konfigurerats enbart för användare. Duplicerade grupper konsolideras inte med standardkonfigurationen.

Vanliga topologier beskrivs i avsnitten om [separata topologier](#multiple-forests-separate-topologies), [fullständig nät](#multiple-forests-full-mesh-with-optional-galsync), och [konto-resurs-topologi](#multiple-forests-account-resource-forest).

Standardkonfigurationen i Azure AD Connect-synkronisering förutsätter:

* Varje användare har bara ett aktiverat konto och skogen där det finns det här kontot används för att autentisera användaren. Detta antagande används för både Lösenordssynkronisering och federation. UserPrincipalName och sourceAnchor/immutableID komma från den här skogen.
* Varje användare har en postlåda.
* Skogen som är värd för postlådan för en användare har bästa data kvalitet för attribut som visas i den Exchange globala adresslistan (GAL). Om det inte finns någon postlåda för användaren, användas en skog för att bidra attributvärdena.
* Om du har en länkad postlåda finns även ett konto i en annan skog som används för inloggning.

Om din miljö inte matchar dessa antagande, händer följande:

* Om du har mer än en aktiv konto eller mer än en postlåda Synkroniseringsmotorn hämtar en och ignorerar den andra.
* En länkad postlåda som inte aktivt konto exporteras inte till Azure AD. Användarkontot är inte representeras som en medlem i någon grupp. En länkad postlåda i DirSync visas alltid som en normal postlåda. Den här ändringen är avsiktligt annorlunda bättre stöd för scenarier med flera skogar.

Du hittar mer information finns i [förstå standardkonfigurationen](active-directory-aadconnectsync-understanding-default-configuration.md).

### <a name="multiple-forests-multiple-sync-servers-to-one-azure-ad-tenant"></a>Flera skogar, flera synkroniseringsservrar till en Azure AD-klient
![Stöds inte topologi för flera skogar och flera synkroniseringsservrar](./media/active-directory-aadconnect-topologies/MultiForestMultiSyncUnsupported.png)

Med mer än en Azure AD Connect sync-servern är ansluten till en enda Azure AD-klient stöds inte. Undantaget är att använda en [mellanlagring server](#staging-server).

### <a name="multiple-forests-separate-topologies"></a>Flera skogar, separat topologier
![Alternativet för att representera användare bara en gång i alla kataloger](./media/active-directory-aadconnect-topologies/MultiForestUsersOnce.png)

![Presentation av flera skogar och separata topologier](./media/active-directory-aadconnect-topologies/MultiForestSeperateTopologies.png)

I den här miljön behandlas alla lokala skogar som separata entiteterna. Ingen användare finns i andra skogar. Varje skog har sin egen Exchange-organisation och det finns ingen GALSync mellan skogar. Den här topologin kanske situationen efter en fusion/förvärvet eller i en organisation där varje affärsenhet fungerar oberoende av varandra. Dessa skogar finns i samma organisation i Azure AD och visas med en enhetlig GAL. I den föregående bilden visas en gång i metaversum varje objekt i varje skog och aggregeras i målet Azure AD-klient.

### <a name="multiple-forests-match-users"></a>Flera skogar: matcha användare
Gemensamt för alla dessa scenarier är att distribution och säkerhetsgrupper kan innehålla en blandning av användare, kontakter och externa säkerhetsobjekt (FSP: er). FSP: er används för medlemmar från andra skogar i en säkerhetsgrupp i Active Directory Domain Services (AD DS). Alla FSP: er ska matchas med det verkliga objektet i Azure AD.

### <a name="multiple-forests-full-mesh-with-optional-galsync"></a>Flera skogar: fullständig nät med valfritt GALSync
![Alternativet för att använda e-postattributet för att matcha när användaridentiteter finns i flera kataloger](./media/active-directory-aadconnect-topologies/MultiForestUsersMail.png)

![Fullständig nättopologi för flera skogar](./media/active-directory-aadconnect-topologies/MultiForestFullMesh.png)

En fullständig nättopologi att användare och resurser kan finnas i en skog. Det är ofta, tvåvägs förtroenden mellan skogar.

Om Exchange finns i mer än en skog, kan det finnas (valfritt) en lokal GALSync lösning. Varje användare representeras sedan som en kontakt i andra skogar. GALSync implementeras ofta via FIM 2010 eller MIM 2016. Azure AD Connect kan inte användas för lokala GALSync.

I det här scenariot är identitetsobjekt anslutna via e postattribut. En användare som har en postlåda i en skog är ansluten och kontakter i andra skogar.

### <a name="multiple-forests-account-resource-forest"></a>Flera skogar: kontot resursskogen
![Alternativet för att använda attributen ObjectSID och msExchMasterAccountSID för att matcha när identiteter finns i flera kataloger](./media/active-directory-aadconnect-topologies/MultiForestUsersObjectSID.png)

![Konto-resurs skogstopologi för flera skogar](./media/active-directory-aadconnect-topologies/MultiForestAccountResource.png)

I en skogstopologi konto-resurs har ett eller flera *konto* skogar med aktiva användarkonton. Du har också en eller flera *resurs* skogar med inaktiverade konton.

I det här scenariot skogsförtroenden (minst) resurs alla kontoskogarna. Resursskogen har vanligtvis ett utökat Active Directory-schema med Exchange och Lync. Alla Exchange och Lync tjänster, tillsammans med andra delade tjänster finns i den här skogen. Användare har ett inaktiverat användarkonto i den här skogen och postlådan är kopplad till kontoskogen.

## <a name="office-365-and-topology-considerations"></a>Office 365 och topologiöverväganden
Vissa Office 365-arbetsbelastningar har vissa begränsningar på topologier som stöds:

| Arbetsbelastning | Begränsningar |
| --------- | --------- |
| exchange online | Mer information om hybridtopologier som stöds av Exchange Online finns [hybriddistributioner med flera Active Directory-skogar](https://technet.microsoft.com/library/jj873754.aspx). |
| Skype för företag | När du använder flera lokala skogar stöds endast skogstopologi konto-resurs. Mer information finns i [miljökrav för Skype för Business Server 2015](https://technet.microsoft.com/library/dn933910.aspx). |

Om du är en större organisation, så bör du använda den [Office 365 PreferredDataLocation](active-directory-aadconnectsync-feature-preferreddatalocation.md) funktion. Det kan du definiera i vilken region datacenter användarens resurserna finns.

## <a name="staging-server"></a>Server för Förproduktion
![Mellanlagring server i en topologi](./media/active-directory-aadconnect-topologies/MultiForestStaging.png)

Azure AD Connect har stöd för installation av en andra server i *mellanlagringsläge*. En server i det här läget läser data från alla anslutna kataloger, men skriva inte något till anslutna kataloger. Den använder normal synkroniseringscykel och därför har en uppdaterad kopia av identitetsdata.

I en katastrof där den primära servern kraschar, kan du växla över till den fristående servern. Du kan göra detta i Azure AD Connect-guiden. Den här andra-server kan finnas i olika datacenter eftersom någon infrastruktur delas med den primära servern. Du måste manuellt kopiera någon konfigurationsändring gjorts på den primära servern till den andra servern.

Du kan använda en fristående server för att testa en ny anpassad konfiguration och den effekt som det har på dina data. Du kan förhandsgranska ändringarna och ändra konfigurationen. När du är nöjd med den nya konfigurationen kan du göra testservern den aktiva servern och ange den gamla aktiva servern som mellanlagringsläge.

Du kan också använda den här metoden för att ersätta active sync-servern. Förbered den nya servern och ange det till mellanlagringsläge. Kontrollera att den är i ett fungerande tillstånd, inaktivera mellanlagringsläge (att göra den aktiva), och stänga av den aktiva servern.

Det är möjligt att ha fler än en fristående server när du vill ha flera säkerhetskopieringar i olika datacenter.

## <a name="multiple-azure-ad-tenants"></a>Flera Azure AD-klienter
Vi rekommenderar att du har en enskild klient i Azure AD för en organisation.
Innan du planerar att använda flera Azure AD-klienter finns i artikeln [hantering av administrativa enheter i Azure AD](../active-directory-administrative-units-management.md). Den omfattar vanliga scenarier där du kan använda en enskild klient.

![Topologi för flera skogar och flera innehavare](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectory.png)

Det finns en 1:1-relation mellan en Azure AD Connect sync-server och en Azure AD-klient. För varje Azure AD-klient behöver du en Azure AD Connect sync-serverinstallation. Azure AD-klient instanser separat avsiktligt. Användare i en klient Se inte att användare i den andra innehavaren. Om du vill använda den här separationen är en konfiguration som stöds. I annat fall bör du använda enda modell för Azure AD-klienter.

### <a name="each-object-only-once-in-an-azure-ad-tenant"></a>Varje objekt bara en gång i en Azure AD-klient
![Filtrerade topologin för en enda skog](./media/active-directory-aadconnect-topologies/SingleForestFiltered.png)

I den här topologin är en Azure AD Connect sync-servern ansluten till varje Azure AD-klient. Azure AD Connect sync-servrar måste konfigureras för att filtrera så att var och en har ett ömsesidigt uteslutande uppsättning objekt att använda. Du kan till exempel definiera omfattningen av varje server till en viss domän eller organisationsenhet.

En DNS-domän kan registreras i ett enda Azure AD-klient. UPN-namnen för användare i den lokala Active Directory-instansen måste också använda separata namnområden. I den föregående bilden registreras exempelvis tre separata UPN-suffix i den lokala Active Directory-instansen: contoso.com, fabrikam.com och Internetadress. Användare i varje lokala Active Directory-domän använda ett annat namnområde.

>[!NOTE]
>Globala adress lista synkronisering (GalSync) görs inte automatiskt i den här topologin och kräver en ytterligare anpassade MIM-implementeringen så varje klient en fullständig globala adresslistan (GAL) i Exchange Online och Skype för företag – Online.


Den här topologin har följande begränsningar för annars scenarier som stöds:

* Endast en av Azure AD-klienter kan aktivera en Exchange-hybrid med lokala Active Directory-instans.
* Windows 10-enheter kan associeras med endast en Azure AD-klient.
* Enkel inloggning (SSO) alternativet för synkronisering och direkt lösenordsautentisering kan användas med endast en Azure AD-klient.

Krav för en ömsesidigt uteslutande uppsättning objekt gäller även för tillbakaskrivning. Vissa funktioner för tillbakaskrivning stöds inte med den här topologin eftersom de förutsätter en enda lokal konfiguration. Funktionerna är:

* Tillbakaskrivning av grupp med standardkonfiguration.
* Tillbakaskrivning av enheter.

### <a name="each-object-multiple-times-in-an-azure-ad-tenant"></a>Varje objekt flera gånger i en Azure AD-klient
![Topologi som stöds inte för en enda skog och flera innehavare](./media/active-directory-aadconnect-topologies/SingleForestMultiDirectoryUnsupported.png) ![Topologi som stöds inte för en enda skog och flera kopplingar](./media/active-directory-aadconnect-topologies/SingleForestMultiConnectorsUnsupported.png)

Dessa uppgifter stöds inte:

* Synkronisera samma användare till flera innehavare av Azure AD.
* Ändrar konfigurationen så att användarna i en Azure AD-klient visas som kontakter i en annan Azure AD-klient.
* Ändra Azure AD Connect-synkronisering för att ansluta till flera Azure AD-klienter.

### <a name="galsync-by-using-writeback"></a>GALSync genom att använda tillbakaskrivning
![Stöds inte topologi för flera skogar, flera kataloger med GALSync fokusera på Azure AD](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync1Unsupported.png) ![Stöds inte topologi för flera skogar, flera kataloger med GALSync fokusera på lokala Active Directory](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync2Unsupported.png)

Azure AD-klienter separat avsiktligt. Dessa uppgifter stöds inte:

* Ändra konfigurationen av Azure AD Connect-synkronisering för att läsa data från en annan Azure AD-klient.
* Exportera användare som kontakter till en annan lokal Active Directory-instans med hjälp av Azure AD Connect-synkronisering.

### <a name="galsync-with-on-premises-sync-server"></a>GALSync med lokala synkroniseringsserver
![GALSync i en topologi för flera skogar och flera kataloger](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync.png)

Du kan använda FIM 2010 eller MIM 2016 lokalt för att synkronisera användare (via GALSync) mellan två Exchange-organisationer. Användare i en organisation visas som externa användare/kontakter i den andra organisationen. Dessa olika lokala Active Directory instanser kan sedan synkroniseras med sina egna Azure AD-klienter.

## <a name="next-steps"></a>Nästa steg
Information om hur du installerar Azure AD Connect för dessa scenarier finns [anpassad installation av Azure AD Connect](active-directory-aadconnect-get-started-custom.md).

Lär dig mer om den [Azure AD Connect-synkronisering](active-directory-aadconnectsync-whatis.md) konfiguration.

Lär dig mer om [integrera dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md).
