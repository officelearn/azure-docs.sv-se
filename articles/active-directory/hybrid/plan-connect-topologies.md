---
title: 'Azure AD Connect: Topologier som stöds | Microsoft Docs'
description: Det här avsnittet beskrivs som stöds respektive topologier för Azure AD Connect
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
ms.date: 11/27/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 74b2cea8e3adfb6689d4ac7049e7892f8f0ecf4a
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/29/2018
ms.locfileid: "52582641"
---
# <a name="topologies-for-azure-ad-connect"></a>Topologier för Azure AD Connect
Den här artikeln beskrivs olika lokala och Azure Active Directory (Azure AD)-topologier som använder Azure AD Connect-synkronisering som viktiga integrationslösning. Den här artikeln innehåller konfigurationer för både stöds och som inte stöds.


Här är förklaringen för bilder i artikeln:

| Beskrivning | Symbol |
| --- | --- |
| Den lokala Active Directory-skog |![Den lokala Active Directory-skog](./media/plan-connect-topologies/LegendAD1.png) |
| Den lokala Active Directory med filtrerade import |![Active Directory med filtrerade import](./media/plan-connect-topologies/LegendAD2.png) |
| Azure AD Connect-synkroniseringsservern |![Azure AD Connect-synkroniseringsservern](./media/plan-connect-topologies/LegendSync1.png) |
| Azure AD Connect-synkroniseringsservern ”mellanlagringsläge” |![Azure AD Connect-synkroniseringsservern ”mellanlagringsläge”](./media/plan-connect-topologies/LegendSync2.png) |
| GALSync med Forefront Identity Manager (FIM) 2010 eller Microsoft Identity Manager (MIM) 2016 |![GALSync med FIM 2010 eller MIM 2016](./media/plan-connect-topologies/LegendSync3.png) |
| Azure AD Connect-synkroniseringsservern detaljerad |![Azure AD Connect-synkroniseringsservern detaljerad](./media/plan-connect-topologies/LegendSync4.png) |
| Azure AD |![Azure Active Directory](./media/plan-connect-topologies/LegendAAD.png) |
| Scenario som inte stöds |![Scenario som inte stöds](./media/plan-connect-topologies/LegendUnsupported.png) |


> [!IMPORTANT]
> Microsoft stöder inte ändring eller Azure AD Connect-synkronisering utanför konfigurationer eller de åtgärder som är formellt dokumenterade. Någon av dessa konfigurationer eller åtgärder kan resultera i tillståndet är inkonsekvent eller som inte stöds av Azure AD Connect-synkronisering. Därför kan Microsoft inte tillhandahålla teknisk support för sådana distributioner.


## <a name="single-forest-single-azure-ad-tenant"></a>Enkel skog, ett Azure AD-klient
![Topologi för en enda skog och en enda klient](./media/plan-connect-topologies/SingleForestSingleDirectory.png)

De vanligaste topologin är en enda lokal skog, med en eller flera domäner och en enda Azure AD-klient. Synkronisering av lösenordshash används för Azure AD-autentisering. Snabbinstallation av Azure AD Connect stöder endast den här topologin.

### <a name="single-forest-multiple-sync-servers-to-one-azure-ad-tenant"></a>Enkel skog, flera synkroniseringsservrar till en Azure AD-klient
![Som inte stöds, som har filtrerats topologi för en enskild skog](./media/plan-connect-topologies/SingleForestFilteredUnsupported.png)

Med flera Azure AD Connect-synkroniseringsservrar som är anslutna till samma Azure AD-klienten inte stöds, förutom för en [mellanlagring server](#staging-server). Det har som inte stöds även om servrarna är konfigurerade för att synkronisera med en uppsättning objekt som inte anges samtidigt. Du har anses den här topologin om du inte kan nå alla domäner i skogen från en enda server, eller om du vill fördela belastningen över flera servrar.

## <a name="multiple-forests-single-azure-ad-tenant"></a>Flera skogar, en Azure AD-klient
![Topologi för flera skogar och en enda klient](./media/plan-connect-topologies/MultiForestSingleDirectory.png)

Många organisationer har miljöer med flera lokala Active Directory-skogar. Det finns olika anledningar till att ha fler än en lokal Active Directory-skog. Vanliga exempel är layouter med konto-resursskogar och resultatet av en sammanslagning eller förvärv.

När du har flera skogar, alla skogar måste kunna nås av en enda Azure AD Connect-synkroniseringsservern. Du behöver att ansluta servern till en domän. Om det behövs för att nå alla skogar, kan du placera servern i ett perimeternätverk (även kallat DMZ, demilitariserad zon och avskärmat undernät).

Installationsguiden för Azure AD Connect erbjuder flera alternativ för att konsolidera användare som representeras i flera skogar. Målet är att en användare representeras endast en gång i Azure AD. Det finns vissa vanliga topologier som du kan konfigurera i den anpassade installationssökvägen i installationsguiden. På den **identifierar användarna** markerar du motsvarande alternativ som representerar din topologi. Konsolideringen har konfigurerats endast för användare. Duplicerade grupper konsolideras inte med standardkonfigurationen.

Vanliga topologier beskrivs i avsnitten om [avgränsa topologier](#multiple-forests-separate-topologies), [fullständig nät](#multiple-forests-full-mesh-with-optional-galsync), och [resursen för lagringskonton topologin](#multiple-forests-account-resource-forest).

Med standardkonfigurationen i Azure AD Connect-synkronisering förutsätter:

* Varje användare har bara ett aktiverat konto och skogen där det finns det här kontot används för att autentisera användaren. Den här antas för hash-synkronisering av lösenord, direktautentisering och federation. UserPrincipalName och sourceAnchor/immutableID kommer från den här skogen.
* Varje användare har en postlåda.
* Den skog som är värd för postlådan för en användare har bästa möjliga datakvalitet för för attribut som är synliga i den Exchange globala adresslistan (GAL). Om det finns inga postlåda för användaren, användas en skog för att bidra attributvärdena.
* Om du har en länkad postlåda, finns det också ett konto i en annan skog som används för att logga in.

Om din miljö inte matchar dessa antaganden, händer följande:

* Om du har flera aktiva konton eller mer än en postlåda, hämtar ett Synkroniseringsmotorn och ignorerar den andra.
* En länkad postlåda med inga aktiva konton exporteras inte till Azure AD. Användarkontot är inte representeras som en medlem i någon grupp. En länkad postlåda i DirSync representeras alltid en normal postlåda. Den här ändringen är avsiktligt annorlunda bättre stöd för scenarier med flera skogar.

Du hittar mer information finns i [förstå standardkonfigurationen](concept-azure-ad-connect-sync-default-configuration.md).

### <a name="multiple-forests-multiple-sync-servers-to-one-azure-ad-tenant"></a>Flera skogar, flera synkroniseringsservrar till en Azure AD-klient
![Stöds inte topologi för flera skogar och flera synkroniseringsservrar](./media/plan-connect-topologies/MultiForestMultiSyncUnsupported.png)

Mer än ett Azure AD Connect-synkroniseringsservern är ansluten till en enda Azure AD-klient stöds inte. Undantaget är att använda en [mellanlagring server](#staging-server).

Den här topologin skiljer sig från den som visas nedan i som **flera synkroniseringsservrar** är ansluten till en enda Azure AD klient stöds inte.

### <a name="multiple-forests-single-sync-server-users-are-represented-in-only-one-directory"></a>Flera skogar, enskild synkroniseringsserver användare representeras i endast en katalog
![Alternativet för att representera användare bara en gång i alla kataloger](./media/plan-connect-topologies/MultiForestUsersOnce.png)

![Presentation av flera skogar och separata topologier](./media/plan-connect-topologies/MultiForestSeparateTopologies.png)

I den här miljön behandlas alla lokala skogar som separata entiteter. Ingen användare finns i en annan skog. Varje skog har en egen Exchange-organisation och det finns inga GALSync mellan skogar. Den här topologin kan vara situationen efter en sammanslagning/förvärvet eller i en organisation där varje affärsenhet fungerar oberoende av varandra. Dessa skogar finns i samma organisation i Azure AD och visas med en enhetlig GAL. I den föregående bilden visas en gång i metaversum varje objekt i varje skog och aggregeras i målets Azure AD-klient.

### <a name="multiple-forests-match-users"></a>Flera skogar: matcha användare
Gemensamt för alla dessa scenarier är att distribution och säkerhetsgrupper kan innehålla en blandning av användare, kontakter och främmande säkerhetsobjekt (FSP: er). FSP: er används för medlemmar från andra skogar i en säkerhetsgrupp i Active Directory Domain Services (AD DS). Alla FSP: er ska matchas med det verkliga objektet i Azure AD.

### <a name="multiple-forests-full-mesh-with-optional-galsync"></a>Flera skogar: fullständig nät med valfritt GALSync
![Alternativet för att använda e-postattributet för att matcha när användaridentiteter finns i flera kataloger](./media/plan-connect-topologies/MultiForestUsersMail.png)

![Fullständig nättopologi för flera skogar](./media/plan-connect-topologies/MultiForestFullMesh.png)

En fullständig nättopologi att användare och resurser kan finnas i en skog. Det finns ofta, dubbelriktat förtroende mellan skogarna.

Om Exchange finns i fler än en skog, kan det finnas (valfritt) en lokal GALSync lösning. Varje användare representeras sedan som en kontakt i andra skogar. GALSync implementeras vanligtvis via FIM 2010 eller MIM 2016. Azure AD Connect kan inte användas för den lokala GALSync.

I det här scenariot är identitetsobjekt anslutna via e-postattributet. En användare som har en postlåda i en skog är ansluten och kontakter i andra skogar.

### <a name="multiple-forests-account-resource-forest"></a>Flera skogar: konto-resursskog
![Alternativet för att använda ObjectSID- och msExchMasterAccountSID attribut för att matcha när identiteter finns i flera kataloger](./media/plan-connect-topologies/MultiForestUsersObjectSID.png)

![Resursen för lagringskonton skogstopologi för flera skogar](./media/plan-connect-topologies/MultiForestAccountResource.png)

I en skogstopologi resursen för lagringskonton har du en eller flera *konto* skogar med aktiva användarkonton. Du har också en eller flera *resource* skogar med inaktiverade konton.

I det här scenariot litar (minst) resursskog på alla kontoskogarna. Resursskogen har vanligtvis ett utökat Active Directory-schema med Exchange och Lync. Alla Exchange och Lync tjänster, tillsammans med andra delade tjänster finns i den här skogen. Användare har ett inaktiverat användarkonto i den här skogen och postlådan är länkad till kontoskogen.

## <a name="office-365-and-topology-considerations"></a>Office 365 och topologiöverväganden
Vissa Office 365-arbetsbelastningar har vissa begränsningar på topologier som stöds:

| Arbetsbelastning | Begränsningar |
| --------- | --------- |
| exchange online | Mer information om hybridtopologier som stöds av Exchange Online finns i [hybriddistributioner med flera Active Directory-skogar](https://technet.microsoft.com/library/jj873754.aspx). |
| Skype för företag | När du använder flera lokala skogar stöds endast skogstopologi resursen för lagringskonton. Mer information finns i [miljökrav för Skype för Business Server 2015](https://technet.microsoft.com/library/dn933910.aspx). |

Om du är en större organisation så bör du använda den [Office 365 PreferredDataLocation](how-to-connect-sync-feature-preferreddatalocation.md) funktionen. Det kan du definiera i vilken datacenterregion du användarens resurserna finns.

## <a name="staging-server"></a>Mellanlagringsserver
![Mellanlagringsserver i en topologi](./media/plan-connect-topologies/MultiForestStaging.png)

Den Azure AD Connect stöder installation av en andra server i *mellanlagringsläge*. En server i det här läget läser data från alla anslutna kataloger, men skriva inte något till anslutna kataloger. Den använder den normala synkroniseringscykeln och därför har en uppdaterad kopia av identitetsdata.

I ett haveri där den primära servern kraschar, kan du växla över till den tillfälliga servern. Detta gör du i Azure AD Connect-guiden. Den här andra servern kan befinna sig i ett annat datacenter eftersom någon infrastruktur som delas med den primära servern. Du måste manuellt kopiera alla konfigurationsändring som gjorts på den primära servern till den andra servern.

Du kan använda en fristående server för att testa en ny anpassad konfiguration och den effekt som det har på dina data. Du kan granska ändringarna och justera konfigurationen. När du är nöjd med den nya konfigurationen kan du göra testservern den aktiva servern och ange den gamla aktiva servern till mellanlagringsläge.

Du kan också använda den här metoden för att ersätta active sync-servern. Förbered den nya servern och ange den till mellanlagringsläge. Kontrollera att den är i fullgott inaktivera mellanlagringsläge (och göra den aktiv), och stänga av den aktiva servern.

Det är möjligt att ha fler än en mellanlagringsserver när du vill använda flera säkerhetskopieringar i olika datacenter.

## <a name="multiple-azure-ad-tenants"></a>Flera Azure AD-klienter
Vi rekommenderar att du har en enda klient i Azure AD för en organisation.
Innan du planerar att använda flera Azure AD-klienter finns i artikeln [hantering av administrativa enheter i Azure AD](../users-groups-roles/directory-administrative-units.md). Det här täcker vanliga scenarier där du kan använda en enda klient.

![Topologi för flera skogar och flera innehavare](./media/plan-connect-topologies/MultiForestMultiDirectory.png)

Det finns en 1:1-relation mellan en Azure AD Connect-synkroniseringsservern och en Azure AD-klient. För varje Azure AD-klient behöver du en Azure AD Connect sync-serverinstallation. Azure AD-klient-instanser är isolerade avsiktligt. Det vill säga kan inte användare i en klient Se användare i den andra klienten. Om du vill att den här separationen är det här en konfiguration som stöds. I annat fall bör du använda en enda modell för Azure AD-klient.

### <a name="each-object-only-once-in-an-azure-ad-tenant"></a>Varje objekt bara en gång i en Azure AD-klient
![Filtrerade topologi för en enskild skog](./media/plan-connect-topologies/SingleForestFiltered.png)

I den här topologin är en Azure AD Connect-synkroniseringsserver ansluten till varje Azure AD-klient. Azure AD Connect-synkroniseringsservrar måste konfigureras för filtrering så att var och en har ett ömsesidigt uteslutande uppsättning objekt ska bearbetas. Du kan exempelvis begränsa varje server till en viss domän eller organisationsenhet.

En DNS-domän kan registreras i ett enda Azure AD-klient. UPN-namnen för användare i den lokala Active Directory-instans måste också använda separata namnområden. I den föregående bilden registreras till exempel tre separata UPN-suffix i den lokala Active Directory-instans: contoso.com och fabrikam.com Internetadress. Användare i varje lokala Active Directory-domän använda en annan namnrymd.

>[!NOTE]
>Global adress lista synkronisering (GalSync) görs inte automatiskt i den här topologin och kräver en ytterligare anpassad MIM-implementering för att kontrollera varje klient har minst en fullständig globala adresslistan (GAL) i Exchange Online och Skype för Business Online.


Den här topologin har följande begränsningar för annars stöds scenarier:

* Endast en av Azure AD-klienter kan aktivera en Exchange-hybrid med lokala Active Directory-instans.
* Windows 10-enheter kan associeras med endast en Azure AD-klient.
* Enkel inloggning (SSO) alternativet för hash-synkronisering och direktlagringsdiskar lösenordsautentisering kan användas med endast en Azure AD-klient.

Krav för en ömsesidigt uteslutande uppsättning objekt gäller även för tillbakaskrivning av. Vissa funktioner för tillbakaskrivning av stöds inte med den här topologin eftersom de antar att en enda lokal konfiguration. Dessa funktioner:

* Gruppen tillbakaskrivning med standardkonfiguration.
* Tillbakaskrivning av enhet.

### <a name="each-object-multiple-times-in-an-azure-ad-tenant"></a>Varje objekt flera gånger i en Azure AD-klient
![Stöds inte topologi för en enda skog och flera innehavare](./media/plan-connect-topologies/SingleForestMultiDirectoryUnsupported.png) ![Stöds inte topologi för en enda skog och flera kopplingar](./media/plan-connect-topologies/SingleForestMultiConnectorsUnsupported.png)

Dessa uppgifter stöds inte:

* Synkronisera samma användare till flera Azure AD-klienter.
* Ändrar konfigurationen så att användarna i en Azure AD-klient visas som kontakter i en annan Azure AD-klient.
* Ändra Azure AD Connect-synkronisering för att ansluta till flera Azure AD-klienter.

### <a name="galsync-by-using-writeback"></a>GALSync med hjälp av tillbakaskrivning
![Stöds inte topologi för flera skogar, flera kataloger med GALSync fokus på Azure AD](./media/plan-connect-topologies/MultiForestMultiDirectoryGALSync1Unsupported.png) ![Stöds inte topologin för flera skogar, flera kataloger med GALSync fokusera på den lokala Active Directory](./media/plan-connect-topologies/MultiForestMultiDirectoryGALSync2Unsupported.png)

Azure AD-klienter är isolerade avsiktligt. Dessa uppgifter stöds inte:

* Ändra konfigurationen av Azure AD Connect-synkronisering för att läsa data från en annan Azure AD-klient.
* Exportera användare som kontakter till en annan lokal Active Directory-instans med hjälp av Azure AD Connect-synkronisering.

### <a name="galsync-with-on-premises-sync-server"></a>GALSync med lokala synkroniseringsserver
![GALSync i en topologi för flera skogar och flera kataloger](./media/plan-connect-topologies/MultiForestMultiDirectoryGALSync.png)

Du kan använda lokala FIM 2010 eller MIM 2016 för att synkronisera användare (via GALSync) mellan två Exchange-organisationer. Användare i en organisation visas som externa användare/kontakter i den andra organisationen. Dessa olika lokala Active Directory instanser kan sedan synkroniseras med sin egen Azure AD-klienter.

## <a name="next-steps"></a>Nästa steg
Läs hur du installerar Azure AD Connect för dessa scenarier i [anpassad installation av Azure AD Connect](how-to-connect-install-custom.md).

Läs mer om den [Azure AD Connect-synkronisering](how-to-connect-sync-whatis.md) konfiguration.

Läs mer om [integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
