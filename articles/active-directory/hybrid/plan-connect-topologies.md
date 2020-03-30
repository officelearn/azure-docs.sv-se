---
title: 'Azure AD Connect: Topologier som stöds | Microsoft-dokument'
description: Den här ämnesinformationen som stöds och inte stöds av topologier för Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 1034c000-59f2-4fc8-8137-2416fa5e4bfe
ms.service: active-directory
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.topic: conceptual
ms.date: 11/27/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9618e02f54fbb2a3b92771761c5fcf700d126b5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253837"
---
# <a name="topologies-for-azure-ad-connect"></a>Topologier för Azure AD Connect
I den här artikeln beskrivs olika lokala och Azure Active Directory(Azure AD) topologier som använder Azure AD Connect-synkronisering som nyckelintegrationslösning. Den här artikeln innehåller både konfigurationer som stöds och som inte stöds.


Här är legenden för bilder i artikeln:

| Beskrivning | Symbol |
| --- | --- |
| Lokal Active Directory-skog |![Lokal Active Directory-skog](./media/plan-connect-topologies/LegendAD1.png) |
| Lokal Active Directory med filtrerad import |![Active Directory med filtrerad import](./media/plan-connect-topologies/LegendAD2.png) |
| Synkroniseringsserver för Azure AD Connect |![Synkroniseringsserver för Azure AD Connect](./media/plan-connect-topologies/LegendSync1.png) |
| Synkroniseringsservern för Azure AD Connect "mellanlagringsläge" |![Synkroniseringsservern för Azure AD Connect "mellanlagringsläge"](./media/plan-connect-topologies/LegendSync2.png) |
| GALSync med Forefront Identity Manager (FIM) 2010 eller Microsoft Identity Manager (MIM) 2016 |![GALSync med FIM 2010 eller MIM 2016](./media/plan-connect-topologies/LegendSync3.png) |
| Azure AD Connect-synkroniseringsserver, detaljerad |![Azure AD Connect-synkroniseringsserver, detaljerad](./media/plan-connect-topologies/LegendSync4.png) |
| Azure AD |![Azure Active Directory](./media/plan-connect-topologies/LegendAAD.png) |
| Scenario som inte stöds |![Scenario som inte stöds](./media/plan-connect-topologies/LegendUnsupported.png) |


> [!IMPORTANT]
> Microsoft stöder inte att ändra eller använda Azure AD Connect-synkronisering utanför de konfigurationer eller åtgärder som dokumenteras formellt. Någon av dessa konfigurationer eller åtgärder kan resultera i ett inkonsekvent eller icke-stödt tillstånd för Azure AD Connect-synkronisering. Därför kan Microsoft inte tillhandahålla teknisk support för sådana distributioner.


## <a name="single-forest-single-azure-ad-tenant"></a>Enkel skog, en enda Azure AD-klient
![Topologi för en enskild skog och en enda klient](./media/plan-connect-topologies/SingleForestSingleDirectory.png)

Den vanligaste topologin är en enda lokal skog, med en eller flera domäner och en enda Azure AD-klientorganisation. För Azure AD-autentisering används synkronisering av lösenordsh hash. Expressinstallationen av Azure AD Connect stöder endast den här topologin.

### <a name="single-forest-multiple-sync-servers-to-one-azure-ad-tenant"></a>En skog, flera synkroniseringsservrar till en Azure AD-klient
![Filtrerad topologi som inte stöds för en enskild skog](./media/plan-connect-topologies/SingleForestFilteredUnsupported.png)

Att ha flera Azure AD Connect-synkroniseringsservrar anslutna till samma Azure AD-klient stöds inte, förutom en [mellanlagringsserver](#staging-server). Det stöds inte även om dessa servrar är konfigurerade för att synkronisera med en ömsesidigt exklusiv uppsättning objekt. Du kanske har övervägt den här topologin om du inte kan nå alla domäner i skogen från en enda server, eller om du vill distribuera belastningen på flera servrar.

## <a name="multiple-forests-single-azure-ad-tenant"></a>Flera skogar, en enda Azure AD-klient
![Topologi för flera skogar och en enda klient](./media/plan-connect-topologies/MultiForestSingleDirectory.png)

Många organisationer har miljöer med flera lokala Active Directory-skogar. Det finns olika skäl till att ha mer än en lokal Active Directory-skog. Typiska exempel är formgivningar med kontoresursskogar och resultatet av en sammanslagning eller ett förvärv.

När du har flera skogar måste alla skogar kunna nås av en enda Azure AD Connect-synkroniseringsserver. Servern måste vara ansluten till en domän. Om det behövs för att nå alla skogar kan du placera servern i ett perimeternätverk (kallas även DMZ, demilitariserad zon och skärmat undernät).

Installationsguiden för Azure AD Connect innehåller flera alternativ för att konsolidera användare som finns representerade i flera skogar. Målet är att en användare bara representeras en gång i Azure AD. Det finns några vanliga topologier som du kan konfigurera i den anpassade installationssökvägen i installationsguiden. På sidan **Unikt identifiera användarna** väljer du motsvarande alternativ som representerar din topologi. Konsolideringen är endast konfigurerad för användare. Duplicerade grupper konsolideras inte med standardkonfigurationen.

Vanliga topologier diskuteras i avsnitten om separata topologier, [full mesh](#multiple-forests-full-mesh-with-optional-galsync)och [kontoresurstopologin](#multiple-forests-account-resource-forest).

Standardkonfigurationen i Azure AD Connect-synkroniseringen förutsätter:

* Varje användare har bara ett aktiverat konto och skogen där det här kontot finns används för att autentisera användaren. Det här antagandet gäller synkronisering av lösenord hash, direktautentisering och federation. UserPrincipalName och sourceAnchor/immutableID kommer från den här skogen.
* Varje användare har bara en postlåda.
* Skogen som är värd för postlådan för en användare har den bästa datakvaliteten för attribut som visas i DEN globala adresslistan för Exchange (GAL). Om det inte finns någon postlåda för användaren kan alla skogar användas för att bidra med dessa attributvärden.
* Om du har en länkad postlåda finns det också ett konto i en annan skog som används för inloggning.

Om din miljö inte matchar dessa antaganden händer följande:

* Om du har mer än ett aktivt konto eller mer än en postlåda väljer synkroniseringsmotorn det ena och ignorerar det andra.
* En länkad postlåda med inget annat aktivt konto exporteras inte till Azure AD. Användarkontot representeras inte som medlem i någon grupp. En länkad postlåda i DirSync representeras alltid som en vanlig postlåda. Den här ändringen är avsiktligt ett annat beteende för att bättre stödja scenarier för flera skogar.

Du hittar mer information i [Förstå standardkonfigurationen](concept-azure-ad-connect-sync-default-configuration.md).

### <a name="multiple-forests-multiple-sync-servers-to-one-azure-ad-tenant"></a>Flera skogar, flera synkroniseringsservrar till en Azure AD-klient
![Topologi som inte stöds för flera skogar och flera synkroniseringsservrar](./media/plan-connect-topologies/MultiForestMultiSyncUnsupported.png)

Att ha mer än en Azure AD Connect-synkroniseringsserver ansluten till en enda Azure AD-klient stöds inte. Undantaget är användningen av en [mellanlagringsserver](#staging-server).

Den här topologin skiljer sig från den nedan genom att **flera synkroniseringsservrar** som är anslutna till en enda Azure AD-klient inte stöds.

### <a name="multiple-forests-single-sync-server-users-are-represented-in-only-one-directory"></a>Flera skogar, en enda synkroniseringsserver, användare finns bara representerade i en katalog
![Alternativ för att representera användare endast en gång i alla kataloger](./media/plan-connect-topologies/MultiForestUsersOnce.png)

![Skildring av flera skogar och separata topologier](./media/plan-connect-topologies/MultiForestSeparateTopologies.png)

I den här miljön behandlas alla lokala skogar som separata entiteter. Det finns ingen användare i någon annan skog. Varje skog har en egen Exchange-organisation och det finns ingen GALSync mellan skogarna. Denna topologi kan vara situationen efter en sammanslagning / förvärv eller i en organisation där varje affärsenhet är verksam självständigt. Dessa skogar finns i samma organisation i Azure AD och visas med en enhetlig GAL. I föregående bild representeras varje objekt i varje skog en gång i metaversumet och aggregeras i mål Azure AD-klienten.

### <a name="multiple-forests-match-users"></a>Flera skogar: matcha användare
Gemensamt för alla dessa scenarier är att distributions- och säkerhetsgrupper kan innehålla en blandning av användare, kontakter och FSP (Foreign Security Principals). FSPs används i Active Directory Domain Services (AD DS) för att representera medlemmar från andra skogar i en säkerhetsgrupp. Alla FSPs är lösta till det verkliga objektet i Azure AD.

### <a name="multiple-forests-full-mesh-with-optional-galsync"></a>Flera skogar: full mesh med valfri GALSync
![Alternativ för att använda e-postattributet för matchning när användaridentiteter finns över flera kataloger](./media/plan-connect-topologies/MultiForestUsersMail.png)

![Fullständig nättopologi för flera skogar](./media/plan-connect-topologies/MultiForestFullMesh.png)

En fullständig nättopologi gör att användare och resurser kan finnas i alla skogar. Vanligtvis finns det tvåvägstruster mellan skogarna.

Om Exchange finns i mer än en skog kan det finnas (eventuellt) en lokal GALSync-lösning. Varje användare representeras sedan som en kontakt i alla andra skogar. GALSync implementeras ofta via FIM 2010 eller MIM 2016. Azure AD Connect kan inte användas för lokala GALSync.

I det här fallet kopplas identitetsobjekt via e-postattributet. En användare som har en postlåda i en skog är ansluten med kontakterna i de andra skogarna.

### <a name="multiple-forests-account-resource-forest"></a>Flera skogar: kontoresursskog
![Alternativ för att använda attributen ObjectSID och msExchMasterAccountSID för matchning när identiteter finns över flera kataloger](./media/plan-connect-topologies/MultiForestUsersObjectSID.png)

![Kundresursskogstopologi för flera skogar](./media/plan-connect-topologies/MultiForestAccountResource.png)

I en kundtopologi för kontoresurs har du en eller flera *kontoskogar* med aktiva användarkonton. Du har också en eller flera *resursskogar* med inaktiverade konton.

I det här fallet litar en (eller flera) resursskog alla kontoskogar. Resursskogen har vanligtvis ett utökat Active Directory-schema med Exchange och Lync. Alla Exchange- och Lync-tjänster, tillsammans med andra delade tjänster, finns i den här skogen. Användare har ett inaktiverat användarkonto i den här skogen och postlådan är länkad till kontoskogen.

## <a name="office-365-and-topology-considerations"></a>Office 365 och topologi överväganden
Vissa Office 365-arbetsbelastningar har vissa begränsningar för topologier som stöds:

| Arbetsbelastning | Begränsningar |
| --------- | --------- |
| exchange online | Mer information om hybridtopologier som stöds av Exchange Online finns i [Hybriddistributioner med flera Active Directory-skogar](https://technet.microsoft.com/library/jj873754.aspx). |
| Skype för företag | När du använder flera lokala skogar stöds bara kontoresursskogstopologin. Mer information finns i [Miljökrav för Skype för företag Server 2015](https://technet.microsoft.com/library/dn933910.aspx). |

Om du är en större organisation bör du överväga att använda office [365 PreferredDataLocation-funktionen.](how-to-connect-sync-feature-preferreddatalocation.md) Det låter dig definiera i vilken datacenterregion användarens resurser finns.

## <a name="staging-server"></a>Mellanlagringsserver
![Mellanlagringsserver i en topologi](./media/plan-connect-topologies/MultiForestStaging.png)

Azure AD Connect stöder installation av en andra server i *mellanlagringsläge*. En server i det här läget läser data från alla anslutna kataloger men skriver ingenting till anslutna kataloger. Den använder den normala synkroniseringscykeln och har därför en uppdaterad kopia av identitetsdata.

I en katastrof där den primära servern misslyckas kan du växla över till mellanlagringsservern. Du gör detta i Azure AD Connect-guiden. Den här andra servern kan finnas i ett annat datacenter eftersom ingen infrastruktur delas med den primära servern. Du måste kopiera alla konfigurationsändringar som gjorts manuellt på den primära servern till den andra servern.

Du kan använda en mellanlagringsserver för att testa en ny anpassad konfiguration och den effekt som den har på dina data. Du kan förhandsgranska ändringarna och justera konfigurationen. När du är nöjd med den nya konfigurationen kan du göra mellanlagringsservern till den aktiva servern och ställa in den gamla aktiva servern på mellanlagringsläge.

Du kan också använda den här metoden för att ersätta den aktiva synkroniseringsservern. Förbered den nya servern och ställ in den på mellanlagringsläge. Kontrollera att den är i ett bra tillstånd, inaktivera mellanlagringsläge (vilket gör den aktiv) och stäng av den aktiva servern.

Det är möjligt att ha mer än en mellanlagringsserver när du vill ha flera säkerhetskopior i olika datacenter.

## <a name="multiple-azure-ad-tenants"></a>Flera Azure AD-klienter
Vi rekommenderar att du har en enda klient i Azure AD för en organisation.
Innan du planerar att använda flera Azure AD-klienter läser du artikeln [Hantering av administrativa enheter i Azure AD](../users-groups-roles/directory-administrative-units.md). Den täcker vanliga scenarier där du kan använda en enda klient.

![Topologi för flera skogar och flera klienter](./media/plan-connect-topologies/MultiForestMultiDirectory.png)

Det finns en 1:1-relation mellan en Azure AD Connect-synkroniseringsserver och en Azure AD-klientorganisation. För varje Azure AD-klient behöver du en Azure AD Connect-synkroniseringsserverinstallation. Azure AD-klientinstanserna är isolerade av design. Det vill än, användare i en klient kan inte se användare i den andra klienten. Om du vill ha den här separationen är detta en konfiguration som stöds. Annars bör du använda den enda Azure AD-klientmodellen.

### <a name="each-object-only-once-in-an-azure-ad-tenant"></a>Varje objekt endast en gång i en Azure AD-klient
![Filtrerad topologi för en enskild skog](./media/plan-connect-topologies/SingleForestFiltered.png)

I den här topologin är en Azure AD Connect-synkroniseringsserver ansluten till varje Azure AD-klientorganisation. Azure AD Connect-synkroniseringsservrarna måste konfigureras för filtrering så att var och en har en ömsesidigt exklusiv uppsättning objekt att arbeta med. Du kan till exempel begränsa varje server till en viss domän eller organisationsenhet.

En DNS-domän kan bara registreras i en enda Azure AD-klientorganisation. UPN för användarna i den lokala Active Directory-instansen måste också använda separata namnområden. I föregående bild registreras till exempel tre separata UPN-suffix i den lokala Active Directory-instansen: contoso.com, fabrikam.com och wingtiptoys.com. Användarna i varje lokal Active Directory-domän använder ett annat namnområde.

>[!NOTE]
>Global Address List Synchronization (GalSync) görs inte automatiskt i den här topologin och kräver ytterligare en anpassad MIM-implementering för att säkerställa att varje klient har en fullständig global adresslista (GAL) i Exchange Online och Skype för företag – Online.


Den här topologin har följande begränsningar för scenarier som stöds på annat sätt:

* Endast en av Azure AD-klienter kan aktivera en Exchange-hybrid med den lokala Active Directory-instansen.
* Windows 10-enheter kan associeras med endast en Azure AD-klientorganisation.
* Alternativet enkel inloggning (SSO) för synkronisering av lösenord hash och direktautentisering kan endast användas med en Azure AD-klientorganisation.

Kravet på en ömsesidigt uteslutande uppsättning objekt gäller även för tillbakaskrivning. Vissa tillbakaskrivningsfunktioner stöds inte med den här topologin eftersom de antar en enda lokal konfiguration. Dessa funktioner omfattar:

* Gruppåterskrivning med standardkonfiguration.
* Tillbakaskrivning av enheten.

### <a name="each-object-multiple-times-in-an-azure-ad-tenant"></a>Varje objekt flera gånger i en Azure AD-klient
![Topologi som inte stöds för en enskild skog och flera klienter](./media/plan-connect-topologies/SingleForestMultiDirectoryUnsupported.png) ![Topologi som inte stöds för en enskild skog och flera kopplingar](./media/plan-connect-topologies/SingleForestMultiConnectorsUnsupported.png)

Dessa uppgifter stöds inte:

* Synkronisera samma användare med flera Azure AD-klienter.
* Gör en konfigurationsändring så att användare i en Azure AD-klient visas som kontakter i en annan Azure AD-klientorganisation.
* Ändra Azure AD Connect-synkronisering för att ansluta till flera Azure AD-klienter.

### <a name="galsync-by-using-writeback"></a>GALSync med hjälp av tillbakaskrivning
![Topologi som inte stöds för flera skogar och flera kataloger, med GALSync med fokus på Azure AD](./media/plan-connect-topologies/MultiForestMultiDirectoryGALSync1Unsupported.png) ![Topologi som inte stöds för flera skogar och flera kataloger, med GALSync som fokuserar på lokal Active Directory](./media/plan-connect-topologies/MultiForestMultiDirectoryGALSync2Unsupported.png)

Azure AD-klienter är isolerade av design. Dessa uppgifter stöds inte:

* Ändra konfigurationen av Azure AD Connect-synkronisering för att läsa data från en annan Azure AD-klientorganisation.
* Exportera användare som kontakter till en annan lokal Active Directory-instans med hjälp av Azure AD Connect-synkronisering.

### <a name="galsync-with-on-premises-sync-server"></a>GALSync med lokal synkroniseringsserver
![GALSync i en topologi för flera skogar och flera kataloger](./media/plan-connect-topologies/MultiForestMultiDirectoryGALSync.png)

Du kan använda FIM 2010 eller MIM 2016 lokalt för att synkronisera användare (via GALSync) mellan två Exchange-organisationer. Användarna i en organisation visas som utländska användare/kontakter i den andra organisationen. Dessa olika lokala Active Directory-instanser kan sedan synkroniseras med sina egna Azure AD-klienter.

## <a name="next-steps"></a>Nästa steg
Mer information om hur du installerar Azure AD Connect för dessa scenarier finns i [Anpassad installation av Azure AD Connect](how-to-connect-install-custom.md).

Läs mer om synkroniseringskonfigurationen för [Azure AD Connect.](how-to-connect-sync-whatis.md)

Läs mer om [hur du integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
