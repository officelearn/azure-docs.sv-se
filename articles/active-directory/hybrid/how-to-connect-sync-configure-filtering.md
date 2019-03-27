---
title: 'Azure AD Connect-synkronisering: Konfigurera filtrering | Microsoft Docs'
description: Förklarar hur du konfigurerar filtrering i Azure AD Connect-synkronisering.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 880facf6-1192-40e9-8181-544c0759d506
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: eeb2af6283e5c9d8a41e74152a94b85efdae1866
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58487325"
---
# <a name="azure-ad-connect-sync-configure-filtering"></a>Azure AD Connect-synkronisering: Konfigurera filtrering
Med hjälp av filtrering, kan du kontrollera vilka objekt som visas i Azure Active Directory (Azure AD) från din lokala katalog. Standardkonfigurationen tar alla objekt i alla domäner i de Konfigurera skogarna. I allmänhet är är det här den rekommenderade konfigurationen. Användare som använder Office 365-arbetsbelastningar, t.ex Exchange Online och Skype för företag, dra nytta av en fullständig globala adresslistan så att de kan skicka e-post och anropa alla. Med standardkonfigurationen, skulle de ha samma upplevelse som skulle uppstå med en lokal implementering av Exchange- eller Lync.

I vissa fall är du nödvändiga göra några ändringar på standardkonfigurationen. Här följer några exempel:

* Du planerar att använda den [flera Azure AD directory-topologi](plan-connect-topologies.md#each-object-only-once-in-an-azure-ad-tenant). Du måste använda ett filter för att styra vilka objekt som synkroniseras till en viss Azure AD-katalog.
* Du kör ett pilotprojekt i Azure eller Office 365 och du bara vill att en delmängd användare i Azure AD. I små piloten är det inte viktigt att ha en fullständig globala adresslistan som demonstrerar funktionerna.
* Du har många tjänstkonton och andra pålitligheten konton som du inte vill i Azure AD.
* Av kompatibilitetsskäl, kan du inte tar bort några användare konton lokalt. Du bara inaktivera dem. Men i Azure AD kan du bara vill att aktiva konton måste finnas.

Den här artikeln beskriver hur du konfigurerar de olika metoderna för filtrering.

> [!IMPORTANT]
> Microsoft stöder inte ändring eller hantering av Azure AD Connect-synkronisering utöver de åtgärder som är formellt dokumenterade. Sådana åtgärder kan göra att Azure AD Connect-synkroniseringen hamnar i ett inkonsekvent tillstånd eller ett tillstånd som inte stöds. Därför kan Microsoft inte tillhandahålla teknisk support för sådana distributioner.

## <a name="basics-and-important-notes"></a>Grunderna och viktiga meddelanden
I Azure AD Connect-synkronisering, kan du aktivera filtrering när som helst. Om du börjar med en standardkonfiguration av katalogsynkronisering och sedan konfigurera filtrering synkroniseras de objekt som filtreras bort inte längre till Azure AD. Alla objekt i Azure AD som tidigare har synkroniserats men filtrerades sedan raderas på grund av den här ändringen i Azure AD.

Innan du börjar gör ändringar i filtrering, kontrollerar du att du [inaktivera den schemalagda aktiviteten](#disable-the-scheduled-task) så att du inte råkar exportera ändringar som du ännu inte har verifierat för att vara korrekt.

Eftersom filtrering kan det ta bort många objekt på samma gång, som du vill se till att din nya filter är korrekt innan du börjar exportera ändringar till Azure AD. När du har slutfört konfigurationen, rekommenderar vi att du följer den [verifieringssteg](#apply-and-verify-changes) innan du exporterar och göra ändringar i Azure AD.

Att skydda dig från att ta bort många objekt av misstag, funktionen ”[förhindra oavsiktliga borttagningar](how-to-connect-sync-feature-prevent-accidental-deletes.md)” är aktiverad som standard. Om du tar bort många objekt på grund av filtrering (500 som standard), måste du följa stegen i den här artikeln för att tillåta borttagningar gå igenom till Azure AD.

Om du använder en version före November 2015 ([1.0.9125](reference-connect-version-history.md#1091250)), gör en ändring i en filterkonfiguration och använda synkronisering av lösenordshash, måste du utlösa en fullständig synkronisering av alla lösenord när du har slutfört konfigurationen. Stegvisa instruktioner för hur du utlöser en fullständig synkronisering av lösenord finns [Utlös en fullständig synkronisering av alla lösenord](tshoot-connect-password-hash-synchronization.md#trigger-a-full-sync-of-all-passwords). Om du använder version 1.0.9125 eller senare, sedan vanliga **fullständig synkronisering** åtgärd beräknar även om lösenord ska synkroniseras och om den här extra steg krävs inte längre.

Om **användaren** objekt oavsiktligt har tagits bort i Azure AD på grund av ett filterfel, du kan återskapa användarobjekt i Azure AD genom att ta bort dina filtrering konfigurationer. Du kan sedan synkronisera dina kataloger igen. Den här åtgärden återställer användare från Papperskorgen i Azure AD. Du kan inte ångra dock borttagning av andra objekttyper. Till exempel om du råkar ta bort en säkerhetsgrupp och den har använts för att ACL: kan en resurs, gruppen och dess ACL: er inte återställas.

Azure AD Connect tar endast bort objekt som den har en gång anses vara i omfånget. Om det finns objekt i Azure AD som har skapats av en annan Synkroniseringsmotorn och objekten som inte finns i omfattningen, att lägga till filtrering inte ta bort dem. Till exempel om du börjar med en DirSync-server som skapade en fullständig kopia av hela katalogen i Azure AD, och du installerar en ny Azure AD Connect-synkroniseringsserver parallellt med filtrering aktiverat från början, Azure AD Connect inte ta bort extra objekt som skapas av DirSync.

Hur filtrering ska behållas när du installerar eller uppgraderar till en nyare version av Azure AD Connect. Det är alltid en bra idé att kontrollera att konfigurationen inte oavsiktligt ändrats efter en uppgradering till en nyare version innan du kör den första synkroniseringscykeln.

Om du har fler än en skog, måste du koppla de konfigurationer som filtrerande som beskrivs i det här avsnittet för varje skog (förutsatt att du vill att samma konfiguration för alla dessa).

### <a name="disable-the-scheduled-task"></a>Inaktivera den schemalagda aktiviteten
Följ dessa steg om du vill inaktivera den inbyggda scheduler som utlöser en synkroniseringscykel var 30: e minut:

1. Gå till ett PowerShell-prompt.
2. Kör `Set-ADSyncScheduler -SyncCycleEnabled $False` att inaktivera scheduler.
3. Gör de ändringar som finns dokumenterade i den här artikeln.
4. Kör `Set-ADSyncScheduler -SyncCycleEnabled $True` att aktivera scheduler igen.

**Om du använder en Azure AD Connect-version innan 1.1.105.0**  
Följ dessa steg om du vill inaktivera den schemalagda aktiviteten som utlöser en synkroniseringscykel var tredje timme:

1. Starta **Schemaläggaren** från den **starta** menyn.
2. Direkt under **bibliotek för Schemaläggaren**, hitta aktiviteten { **Azure AD Sync Scheduler**, högerklicka och välj **inaktivera**.  
   ![Schemaläggaren](./media/how-to-connect-sync-configure-filtering/taskscheduler.png)  
3. Du kan nu göra ändringar i konfigurationen och köra Synkroniseringsmotorn manuellt från den **hanteraren för synkroniseringstjänsten** konsolen.

När du har slutfört dina ändringar som filtrerande Glöm inte att komma tillbaka och **aktivera** aktiviteten igen.

## <a name="filtering-options"></a>Alternativ för filtrering
Du kan använda följande konfigurationstyper av filtrering för verktyget för directory-synkronisering:

* [**Gruppbaserad**](#group-based-filtering): Filtrering baserat på en grupp kan bara konfigureras på den första installationen med hjälp av installationsguiden.
* [**Domänbaserade**](#domain-based-filtering): Genom att använda det här alternativet kan välja du vilka domäner som synkroniseras med Azure AD. Du kan också lägga till och ta bort domäner från synkroniseringskonfiguration för motorn när du gör ändringar i din lokala infrastruktur när du har installerat Azure AD Connect-synkronisering.
* [**Organisationsenhet (OU) – baserade**](#organizational-unitbased-filtering): Genom att använda det här alternativet kan välja du vilka organisationsenheter synkroniseras till Azure AD. Det här alternativet är för alla typer av objekt i valda organisationsenheter.
* [**Attributbaserad**](#attribute-based-filtering): Genom att använda det här alternativet kan filtrera du objekt baserat på attributvärden för objekt. Du kan också ha olika filter för olika objekttyper.

Du kan använda flera alternativ för filtrering på samma gång. Exempel: du kan använda OU-baserad filtrering som bara inkluderar objekt i en Organisationsenhet. På samma gång, kan du använda attribut-baserad filtrering för att filtrera objekt längre. När du använder flera filtreringsmetoder Använd filtren en logisk ”AND” mellan filtren.

## <a name="domain-based-filtering"></a>Domänbaserad filtrering
Det här avsnittet ger dig hur du konfigurerar ditt domän-filter. Om du har lagts till eller ta bort domäner i skogen när du har installerat Azure AD Connect kan behöva du också uppdatera konfigurationen av filtrering.

Det bästa sättet att ändra domänbaserade filtreringen är genom att köra installationsguiden och ändra [domän- och OU-filtrering](how-to-connect-install-custom.md#domain-and-ou-filtering). Med hjälp av installationsguiden automatiskt alla uppgifter som finns dokumenterade i det här avsnittet.

Du bör bara följa dessa steg om det inte går att köra installationsguiden av någon anledning.

Domänbaserade filtreringen konfigurationen består av följande:

1. Välj de domäner som du vill ska ingå i synkroniseringen.
2. Justera körningsprofilerna för varje domän som har lagts till och borttagna.
3. [Tillämpa och verifiera ändringar av](#apply-and-verify-changes).

### <a name="select-the-domains-to-be-synchronized"></a>Markera domänerna som ska synkroniseras
Det finns två sätt att välja domänerna som ska synkroniseras:
    - Med synkroniseringstjänsten
    - Med hjälp av Azure AD Connect-guiden.


#### <a name="select-the-domains-to-be-synchronized-using-the-synchronization-service"></a>Markera domänerna som ska synkroniseras med synkroniseringstjänsten
Om du vill ange filter för domänen, gör du följande:

1. Logga in på den server som kör Azure AD Connect-synkronisering genom att använda ett konto som är medlem i den **ADSyncAdmins** säkerhetsgrupp.
2. Starta **synkroniseringstjänsten** från den **starta** menyn.
3. Välj **Anslutningsappar**, och i den **Anslutningsappar** väljer du Anslutningsappen med typen **Active Directory Domain Services**. I **åtgärder**väljer **egenskaper**.  
   ![Egenskaper för](./media/how-to-connect-sync-configure-filtering/connectorproperties.png)  
4. Klicka på **konfigurera katalogpartitioner**.
5. I den **Välj katalogpartitioner** väljer och avmarkera domäner efter behov. Kontrollera att endast de partitioner som du vill synkronisera är markerade.  
   ![Partitioner](./media/how-to-connect-sync-configure-filtering/connectorpartitions.png)  
   Om du har ändrat din lokala Active Directory-infrastruktur och har lagts till eller ta bort domäner från skogen, sedan klickar du på den **uppdatera** för att få en uppdaterad lista. När du uppdaterar, blir du tillfrågad om autentiseringsuppgifter. Ange några autentiseringsuppgifter med läsbehörighet till Windows Server Active Directory. Det behöver inte vara den användare som är automatiskt i dialogrutan.  
   ![Uppdatering behövs](./media/how-to-connect-sync-configure-filtering/refreshneeded.png)  
6. När du är klar stänger du den **egenskaper** dialogrutan genom att klicka på **OK**. Om du har tagit bort domäner från skogen ett popup-meddelande som anger att en domän har tagits bort och den konfigurationen rensas.
7. Fortsätt att justera körningsprofilerna.

#### <a name="select-the-domains-to-be-synchronized-using-the-azure-ad-connect-wizard"></a>Markera domänerna som ska synkroniseras med hjälp av Azure AD Connect-guiden
Om du vill ange filter för domänen, gör du följande:

1.  Starta Azure AD Connect-guiden
2.  Klicka på **Konfigurera**.
3.  Välj **anpassa synkroniseringsalternativ** och klicka på **nästa**.
4.  Ange dina autentiseringsuppgifter för Azure AD
5.  På den **anslutna kataloger** skärmen klickar du på **nästa**.
6.  På den **domän och Organisationsenhet filtrera sidan** klickar du på **uppdatera**.  Nya domäner som är negativa nu och borttagna domäner kommer att försvinna.
   ![Partitioner](./media/how-to-connect-sync-configure-filtering/update2.png)  

### <a name="update-the-run-profiles"></a>Uppdatera körningsprofilerna
Om du har uppdaterat din domän-filter, måste du även uppdatera körningsprofilerna.

1. I den **kopplingar** Kontrollera att den koppling som du har ändrat i föregående steg har valts. I **åtgärder**väljer **Konfigurera körningsprofiler**.  
   ![Connector körningsprofiler 1](./media/how-to-connect-sync-configure-filtering/connectorrunprofiles1.png)  
2. Hitta och identifiera följande profiler:
    * Fullständig Import
    * Fullständig synkronisering
    * Deltaimport
    * Deltasynkronisering
    * Exportera
3. Varje profil, justera den **har lagts till** och **bort** domäner.
    1. Utför följande steg för var och en för var och en av de fem profilerna **har lagts till** domän:
        1. Välj körningsprofilen och klicka på **nytt steg**.
        2. På den **konfigurera steg** sidan den **typ** nedrullningsbara menyn och välj steget typ med samma namn som den profil som du konfigurerar. Klicka sedan på **Nästa**.  
        ![Connector körningsprofiler 2](./media/how-to-connect-sync-configure-filtering/runprofilesnewstep1.png)  
        3. På den **Kopplingskonfiguration** sidan den **Partition** nedrullningsbara menyn, Välj namnet på den domän som du har lagt till din domän-filter.  
        ![Connector körningsprofiler 3](./media/how-to-connect-sync-configure-filtering/runprofilesnewstep2.png)  
        4. Stäng den **konfigurera Körningsprofil** dialogrutan klickar du på **Slutför**.
    2. Utför följande steg för var och en för var och en av de fem profilerna **bort** domän:
        1. Välj körningsprofilen.
        2. Om den **värdet** av den **Partition** attributet är ett GUID, väljer du kör steget och klicka på **ta bort steg**.  
        ![Connector körningsprofiler 4](./media/how-to-connect-sync-configure-filtering/runprofilesdeletestep.png)  
    3. Kontrollera din ändring. Varje domän som du vill synkronisera bör anges som ett steg i varje körningsprofil.
4. Stäng den **Konfigurera körningsprofiler** dialogrutan klickar du på **OK**.
5.  Om du vill slutföra konfigurationen, måste du köra en **fullständig import** och en **Deltasynkronisering**. Fortsätt att läsa avsnittet [tillämpa och verifiera ändringar av](#apply-and-verify-changes).

## <a name="organizational-unitbased-filtering"></a>Organisationens enhet – baserad filtrering
Det bästa sättet att ändra OU-baserad filtrering är genom att köra installationsguiden och ändra [domän- och OU-filtrering](how-to-connect-install-custom.md#domain-and-ou-filtering). Med hjälp av installationsguiden automatiskt alla uppgifter som finns dokumenterade i det här avsnittet.

Du bör bara följa dessa steg om det inte går att köra installationsguiden av någon anledning.

Om du vill konfigurera organisationens enhet – baserad filtrering, gör du följande:

1. Logga in på den server som kör Azure AD Connect-synkronisering genom att använda ett konto som är medlem i den **ADSyncAdmins** säkerhetsgrupp.
2. Starta **synkroniseringstjänsten** från den **starta** menyn.
3. Välj **Anslutningsappar**, och i den **Anslutningsappar** väljer du Anslutningsappen med typen **Active Directory Domain Services**. I **åtgärder**väljer **egenskaper**.  
   ![Egenskaper för](./media/how-to-connect-sync-configure-filtering/connectorproperties.png)  
4. Klicka på **konfigurera katalogpartitioner**, Välj den domän som du vill konfigurera och klicka sedan på **behållare**.
5. När du uppmanas ange några autentiseringsuppgifter med läsbehörighet till din lokala Active Directory. Det behöver inte vara den användare som är automatiskt i dialogrutan.
6. I den **Välj behållare** dialogrutan rutan tar du bort organisationsenheter som du inte vill synkronisera med molnkatalogen och klicka sedan på **OK**.  
   ![Organisationsenheter i dialogrutan Välj behållare](./media/how-to-connect-sync-configure-filtering/ou.png)  
   * Den **datorer** behållare måste väljas för Windows 10-datorer som har ska synkroniseras till Azure AD. Om din domänanslutna datorer finns i andra organisationsenheter, kontrollera att de är markerade.
   * Du ska markera containern **ForeignSecurityPrincipals** om du har flera skogar med förtroenden. Med den här containern kan säkerhetsgruppmedlemskap i flera skogar lösas.
   * Den **RegisteredDevices** OU ska vara markerat om du har aktiverat funktionen för tillbakaskrivning av enhet. Om du använder en annan tillbakaskrivning av funktion, till exempel tillbakaskrivning av grupp kontrollerar du de här platserna har valts.
   * Välj andra Organisationsenheter som där användare, InetOrgPerson, grupper, kontakter och datorer är placerade. I bilden finns dessa organisationsenheter i ManagedObjects OU.
   * Om du använder gruppbaserad filtrering, måste den Organisationsenhet där gruppen finns att inkluderas.
   * Observera att du kan konfigurera om nya organisationsenheter som läggs till när filtrering konfigurationen är klar ska synkroniseras eller inte synkroniserats. Se nästa avsnitt för information.
7. När du är klar stänger du den **egenskaper** dialogrutan genom att klicka på **OK**.
8. Om du vill slutföra konfigurationen, måste du köra en **fullständig import** och en **Deltasynkronisering**. Fortsätt att läsa avsnittet [tillämpa och verifiera ändringar av](#apply-and-verify-changes).

### <a name="synchronize-new-ous"></a>Synkronisera nya organisationsenheter
Nya organisationsenheter som skapats efter filtrering har konfigurerats synkroniseras som standard. Det här tillståndet anges av en markerad kryssruta. Du kan också avmarkera vissa sub-organisationsenheter. Klicka på rutan för att få det här beteendet, tills den blir vit med en blå markering (standardtillståndet). Sedan avmarkera alla underordnade organisationsenheter som du inte vill synkronisera.

Om alla underordnade organisationsenheter ska synkroniseras, är rutan vit med en blå markering.  
![Organisationsenheten med alla rutor som valts](./media/how-to-connect-sync-configure-filtering/ousyncnewall.png)

Om vissa sub-organisationsenheter har omarkerade, är det grå med en vit bock i rutan.  
![Organisationsenheten med vissa underordnade organisationsenheter Avmarkerades](./media/how-to-connect-sync-configure-filtering/ousyncnew.png)

En ny Organisationsenhet som skapades under ManagedObjects är synkroniserad med den här konfigurationen.

Installationsguiden för Azure AD Connect skapar alltid den här konfigurationen.

### <a name="dont-synchronize-new-ous"></a>Synkronisera inte nya organisationsenheter
Du kan konfigurera Synkroniseringsmotorn för att inte synkronisera nya organisationsenheterna efter filtrering konfigurationen har slutförts. Det här tillståndet anges i Användargränssnittet visas helt grått med en bockmarkering bredvid. Klicka på rutan för att få det här beteendet, tills den blir vit med en bockmarkering. Välj sedan de underordnade organisationsenheter som du vill synkronisera.

![Organisationsenheten med rot Avmarkerades](./media/how-to-connect-sync-configure-filtering/oudonotsyncnew.png)

En ny Organisationsenhet som skapades under ManagedObjects är inte synkroniserat med den här konfigurationen.

## <a name="attribute-based-filtering"></a>Attributet-baserad filtrering
Se till att du använder November 2015 ([1.0.9125](reference-connect-version-history.md#1091250)) eller senare versionen för dessa åtgärder ska fungera.

> [!IMPORTANT]
>Microsoft rekommenderar att du inte ändra de standardregler som skapats av **Azure AD Connect**. Om du vill ändra regeln klona den och inaktivera den ursprungliga regeln. Göra ändringar i den klonade regeln. Tänk på att på så sätt (inaktiverar ursprungliga regeln) du missar eventuella felkorrigeringar eller funktioner som aktiveras via regeln.

Attributet-baserad filtrering är det mest flexibla sättet att filtreringsobjekt. Du kan använda kraften i [deklarativ etablering](concept-azure-ad-connect-sync-declarative-provisioning.md) att styra nästan alla aspekter av när ett objekt har synkroniserats till Azure AD.

Du kan använda [inkommande](#inbound-filtering) filtrering från Active Directory till metaversum, och [utgående](#outbound-filtering) filtrering från metaversum till Azure AD. Vi rekommenderar att du tillämpar inkommande filtrering eftersom det är enklast att underhålla. Du bör endast använda utgående filtrering om det krävs för att ansluta till objekt från fler än en skog innan utvärderingen kan äga rum.

### <a name="inbound-filtering"></a>Inkommande filtrering
Filtrering av inkommande använder standardkonfigurationen där objekt som kommer att Azure AD måste ha metaversum attributet cloudFiltered som inte har angetts till ett värde som ska synkroniseras. Om värdet för det här attributet är inställt på **SANT**, och sedan objektet har inte synkroniserats. Det bör inte sättas till **FALSKT**, avsiktligt. Om du vill se till att andra regler har möjlighet att bidra med ett värde, det här attributet endast ska ha värden **SANT** eller **NULL** (frånvarande).

I inkommande filtrering, använder du kraften hos **omfång** att avgöra vilka objekt som ska synkroniseras eller inte längre synkronisera. Det här är där du kan göra justeringar så att de passar din organisation krav. Scope-modulen har en **grupp** och en **satsen** att avgöra när en synkroniseringsregel är inom omfånget. En grupp innehåller en eller flera satser. Det finns en logisk ”AND” mellan flera villkor och ett logiskt ”eller” mellan flera grupper.

Låt oss titta på ett exempel:  
![Omfång](./media/how-to-connect-sync-configure-filtering/scope.png)  
Detta ska läsas som **(avdelning = IT) eller (avdelning = försäljning och c = US)**.

Du använder för user-objektet som ett exempel i följande exempel och steg, men du kan använda detta för alla typer av objekt.

I följande exempel börjar prioritet värdet med 50. Detta är ett värde som inte används, men bör vara lägre än 100.

#### <a name="negative-filtering-do-not-sync-these"></a>Negativt filtrering: ”synkroniseras inte dessa”
I följande exempel, du kan filtrera bort (inte längre synkronisera) alla användare där **extensionAttribute15** har värdet **NoSync**.

1. Logga in på den server som kör Azure AD Connect-synkronisering genom att använda ett konto som är medlem i den **ADSyncAdmins** säkerhetsgrupp.
2. Starta **Synchronization Rules Editor** från den **starta** menyn.
3. Se till att **inkommande** är markerad och klicka på **Lägg till ny regel**.
4. Ge regeln ett beskrivande namn, till exempel ”*i från AD – användaren DoNotSyncFilter*”. Välj rätt skogen, Välj **användaren** som den **CS objekttyp**, och välj **Person** som den **MV-objekttyp**. I **länktyp**väljer **ansluta**. I **prioritet**, ange ett värde som inte är för närvarande används av andra Synkroniseringsregler (till exempel 50) och klicka sedan på **nästa**.  
   ![Inkommande 1 beskrivning](./media/how-to-connect-sync-configure-filtering/inbound1.png)  
5. I **Scoping filter**, klickar du på **Lägg till grupp**, och klicka på **Lägg till sats**. I **attributet**väljer **ExtensionAttribute15**. Se till att **operatorn** är inställd på **lika**, och ange sedan värdet **NoSync** i den **värdet** box. Klicka på **Nästa**.  
   ![Inkommande 2 omfång](./media/how-to-connect-sync-configure-filtering/inbound2.png)  
6. Lämna den **ansluta** regler tom och klickar sedan på **nästa**.
7. Klicka på **omvandlingen Lägg**väljer den **ExchangeRate för FlowType** som **konstant**, och välj **cloudFiltered** som den **mål Attributet**. I den **källa** textruta, typ **SANT**. Klicka på **Lägg till** spara regeln.  
   ![Inkommande 3 omvandling](./media/how-to-connect-sync-configure-filtering/inbound3.png)
8. Om du vill slutföra konfigurationen, måste du köra en **Full sync**. Fortsätt att läsa avsnittet [tillämpa och verifiera ändringar av](#apply-and-verify-changes).

#### <a name="positive-filtering-only-sync-these"></a>Positivt filtrering: ”bara synkronisera dessa”
Kan vara mer utmanande att uttrycka positivt filtrering eftersom du behöver tänka på objekt som inte är uppenbara som ska synkroniseras, till exempel konferensrum. Du även ska åsidosätta standardfiltret i out-of-box-regeln **i från AD - användare ansluta**. När du skapar anpassade filter, se till att inte inkludera kritiska systemobjekt, konflikt replikeringsobjekt, särskilt postlådor och tjänstkontona för Azure AD Connect.

Det positiva filtrering alternativet kräver två Synkroniseringsregler. Du behöver en regel (eller flera) av rätt omfånget för objekt som ska synkroniseras. Du behöver också en andra allomfattande synkroniseringsregel som filtrerar ut alla objekt som ännu inte har identifierats som ett objekt som ska synkroniseras.

I följande exempel du bara synkronisera objekt där attributet avdelning har värdet **försäljning**.

1. Logga in på den server som kör Azure AD Connect-synkronisering genom att använda ett konto som är medlem i den **ADSyncAdmins** säkerhetsgrupp.
2. Starta **Synchronization Rules Editor** från den **starta** menyn.
3. Se till att **inkommande** är markerad och klicka på **Lägg till ny regel**.
4. Ge regeln ett beskrivande namn, till exempel ”*i från AD – användaren försäljning synkronisera*”. Välj rätt skogen, Välj **användaren** som den **CS objekttyp**, och välj **Person** som den **MV-objekttyp**. I **länktyp**väljer **ansluta**. I **prioritet**, ange ett värde som inte är för närvarande används av andra Synkroniseringsregler (till exempel 51) och klicka sedan på **nästa**.  
   ![Inkommande 4 beskrivning](./media/how-to-connect-sync-configure-filtering/inbound4.png)  
5. I **Scoping filter**, klickar du på **Lägg till grupp**, och klicka på **Lägg till sats**. I **attributet**väljer **avdelning**. Se till att Operator har angetts till **lika**, och ange sedan värdet **försäljning** i den **värdet** box. Klicka på **Nästa**.  
   ![Inkommande 5 omfång](./media/how-to-connect-sync-configure-filtering/inbound5.png)  
6. Lämna den **ansluta** regler tom och klickar sedan på **nästa**.
7. Klicka på **omvandlingen Lägg**väljer **konstant** som den **ExchangeRate för FlowType**, och välj den **cloudFiltered** som den **mål Attributet**. I den **källa** skriver **FALSKT**. Klicka på **Lägg till** spara regeln.  
   ![Inkommande 6 omvandling](./media/how-to-connect-sync-configure-filtering/inbound6.png)  
   Det här är ett specialfall där du uttryckligen ställa in cloudFiltered till **FALSKT**.
8. Nu har vi skapa allomfattande synkroniseringsregel. Ge regeln ett beskrivande namn, till exempel ”*i från AD – användaren allomfattande filter*”. Välj rätt skogen, Välj **användaren** som den **CS objekttyp**, och välj **Person** som den **MV-objekttyp**. I **länktyp**väljer **ansluta**. I **prioritet**, ange ett värde som inte är för närvarande används av andra Synkroniseringsregler (till exempel 99). Du har valt ett prioritetsvärde som är högre (lägre prioritet) än den föregående regeln för synkronisering. Men du har också utrymme så att du kan lägga till mer synkronisering filtreringsregler senare när du vill starta synkroniseringen ytterligare avdelningar. Klicka på **Nästa**.  
   ![Inkommande 7 beskrivning](./media/how-to-connect-sync-configure-filtering/inbound7.png)  
9. Lämna **Scoping filter** tom och klickar på **nästa**. Ett tomt filter anger att regeln ska tillämpas på alla objekt.
10. Lämna den **ansluta** regler tom och klickar sedan på **nästa**.
11. Klicka på **omvandlingen Lägg**väljer **konstant** som den **ExchangeRate för FlowType**, och välj **cloudFiltered** som den **mål Attributet**. I den **källa** skriver **SANT**. Klicka på **Lägg till** spara regeln.  
    ![Inkommande 3 omvandling](./media/how-to-connect-sync-configure-filtering/inbound3.png)  
12. Om du vill slutföra konfigurationen, måste du köra en **Full sync**. Fortsätt att läsa avsnittet [tillämpa och verifiera ändringar av](#apply-and-verify-changes).

Om du vill kan skapa du flera regler av typen första där du kan inkludera flera objekt i synkroniseringen.

### <a name="outbound-filtering"></a>Filtrering av utgående
I vissa fall är det nödvändigt att utföra filtrering när objekten har anslutna i metaversum. Exempelvis kan det vara nödvändigt att titta på e-postattributet från resursskogen och userPrincipalName-attribut från kontoskogen, att avgöra om ett objekt som ska synkroniseras. I dessa fall kan skapa du filtrering på utgående regel.

I det här exemplet ändrar du filtrering så att endast användare som har både sina e-post och userPrincipalName som slutar på @contoso.com synkroniseras:

1. Logga in på den server som kör Azure AD Connect-synkronisering genom att använda ett konto som är medlem i den **ADSyncAdmins** säkerhetsgrupp.
2. Starta **Synchronization Rules Editor** från den **starta** menyn.
3. Under **typ**, klickar du på **utgående**.
4. Beroende på vilken version av Connect som du använder antingen hitta regeln med namnet **ut till AAD – användare ansluta** eller **ut till AAD - användare ansluta-SOAInAD**, och klicka på **redigera**.
5. I popup-fönstret besvara **Ja** att skapa en kopia av regeln.
6. På den **beskrivning** , ändra **prioritet** till ett värde som inte används, till exempel 50.
7. Klicka på **Scoping filter** i det vänstra navigeringsfältet och sedan på **Lägg till sats**. I **attributet**väljer **e**. I **operatorn**väljer **ENDSWITH**. I **värdet**, typ  **\@contoso.com**, och klicka sedan på **Lägg till sats**. I **attributet**väljer **userPrincipalName**. I **operatorn**väljer **ENDSWITH**. I **värdet**, typ  **\@contoso.com**.
8. Klicka på **Spara**.
9. Om du vill slutföra konfigurationen, måste du köra en **Full sync**. Fortsätt att läsa avsnittet [tillämpa och verifiera ändringar av](#apply-and-verify-changes).

## <a name="apply-and-verify-changes"></a>Tillämpa och verifiera ändringar
När du har gjort konfigurationsändringarna, måste du använda dem till objekt som redan finns i systemet. Det kan också vara att objekt som inte är för närvarande i Synkroniseringsmotorn ska bearbetas (och Synkroniseringsmotorn behöver läsa källsystemet igen för att verifiera dess innehåll).

Om du har ändrat konfigurationen med hjälp av **domän** eller **organisatorisk enhet** filtrering, måste du göra en **fullständig import**, följt av **Delta synkronisering**.

Om du har ändrat konfigurationen med hjälp av **attributet** filtrering, måste du göra en **fullständig synkronisering**.

Utför följande steg:

1. Starta **synkroniseringstjänsten** från den **starta** menyn.
2. Välj **Anslutningsappar**. I den **kopplingar** väljer du den anslutning där du har gjort en konfigurationsändring tidigare. I **åtgärder**väljer **kör**.  
   ![Anslutningen som kör](./media/how-to-connect-sync-configure-filtering/connectorrun.png)  
3. I **körningsprofiler**, markera den åtgärd som har nämnts ovan. Om du behöver köra två åtgärder, kör du andra efter den första som har slutförts. (Den **tillstånd** kolumnen är **inaktiv** för den valda anslutningsappen.)

Efter synkroniseringen går mellanlagras alla ändringar för att exporteras. Innan du utför ändringarna i Azure AD, som du vill kontrollera att alla dessa ändringar är korrekta.

1. Starta en kommandotolk och gå till `%Program Files%\Microsoft Azure AD Sync\bin`.
2. Kör `csexport "Name of Connector" %temp%\export.xml /f:x`.  
   Namnet på anslutningen är i synkroniseringstjänsten. Den har ett namn som liknar ”contoso.com – AAD” för Azure AD.
3. Kör `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv`.
4. Nu har du en fil i % temp % med namnet export.csv som kan granskas i Microsoft Excel. Den här filen innehåller alla ändringar som är på väg att exporteras.
5. Gör nödvändiga ändringar till informationen eller konfigurationen och köra de här stegen igen (Import, synkronisera och verifiera) tills de ändringar som är på väg att exporteras är vad du förväntar dig.

När du är nöjd kan du exportera ändringarna till Azure AD.

1. Välj **Anslutningsappar**. I den **kopplingar** väljer du Azure AD Connector. I **åtgärder**väljer **kör**.
2. I **körningsprofiler**väljer **exportera**.
3. Om konfigurationsändringarna tar bort många objekt kan se du ett fel i exporten om talet är mer än det konfigurerade tröskelvärdet (som standard 500). Om du ser detta fel, måste du tillfälligt inaktivera det ”[förhindra oavsiktliga borttagningar](how-to-connect-sync-feature-prevent-accidental-deletes.md)” funktionen.

Nu är det dags att aktivera scheduler igen.

1. Starta **Schemaläggaren** från den **starta** menyn.
2. Direkt under **bibliotek för Schemaläggaren**, hitta aktiviteten { **Azure AD Sync Scheduler**, högerklicka och välj **aktivera**.

## <a name="group-based-filtering"></a>Gruppbaserad filtrering
Du kan konfigurera gruppbaserad filtrering första gången du installerar Azure AD Connect med hjälp av [anpassad installation](how-to-connect-install-custom.md#sync-filtering-based-on-groups). Den är avsedd för en pilotdistribution där du vill att endast en liten uppsättning objekt som ska synkroniseras. När du inaktiverar gruppbaserad filtrering kan aktiveras inte igen. Den har *stöds inte* du använder gruppbaserad filtrering i en anpassad konfiguration. Den stöds endast om du vill konfigurera den här funktionen med hjälp av installationsguiden. När du har slutfört ditt pilotprojekt, Använd sedan någon av de andra alternativen som filtrerande i det här avsnittet. När du använder OU-baserad filtrering tillsammans med gruppbaserad filtrering, måste organisationsenheterna där gruppen och dess medlemmar finns tas.

När du synkroniserar flera AD-skogar, kan du konfigurera gruppbaserad filtrering genom att ange en annan grupp för varje AD-koppling. Om du inte vill för att synkronisera en användare i en AD-skog och samma användare har en eller flera motsvarande objekt i andra AD-skogar måste du se till att användarobjektet och dess motsvarande objekt i gruppbaserad filtrering omfång. Exempel:

* Du har en användare i en skog som har ett motsvarande FSP (sekundärt säkerhetsobjekt) objekt i en annan skog. Båda-objekten måste inom gruppbaserad filtrering omfång. I annat fall kommer användaren inte att synkroniseras till Azure AD.

* Du har en användare i en skog som har ett motsvarande resurskonto (t.ex. länkad postlåda) i en annan skog. Dessutom har du konfigurerat Azure AD Connect för att länka användare med resurskontot. Båda-objekten måste inom gruppbaserad filtrering omfång. I annat fall kommer användaren inte att synkroniseras till Azure AD.

* Du har en användare i en skog som har ett motsvarande e-postmeddelande kontakt på en annan skog. Dessutom har du konfigurerat Azure AD Connect om du vill koppla användaren till kontakten för e-post. Båda-objekten måste inom gruppbaserad filtrering omfång. I annat fall kommer användaren inte att synkroniseras till Azure AD.


## <a name="next-steps"></a>Nästa steg
- Läs mer om [Azure AD Connect-synkronisering](how-to-connect-sync-whatis.md) konfiguration.
- Läs mer om [integrera dina lokala identiteter med Azure AD](whatis-hybrid-identity.md).
