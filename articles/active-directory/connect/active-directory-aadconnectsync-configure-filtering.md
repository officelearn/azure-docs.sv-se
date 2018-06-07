---
title: 'Azure AD Connect-synkronisering: Konfigurera filtrering | Microsoft Docs'
description: Beskriver hur du konfigurerar filtrering i Azure AD Connect-synkronisering.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 880facf6-1192-40e9-8181-544c0759d506
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: f54f0fb16838b245ac09c08c7afd9e6a53449afc
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34594235"
---
# <a name="azure-ad-connect-sync-configure-filtering"></a>Azure AD Connect-synkronisering: Konfigurera filtrering
Med filtrering kan kan du kontrollera vilka objekt som visas i Azure Active Directory (AD Azure) från din lokala katalog. Standardkonfigurationen tar alla objekt i alla domäner i de konfigurerade skogarna. I allmänhet är är det här den rekommenderade konfigurationen. Användare som använder Office 365-arbetsbelastningar, t.ex Exchange Online och Skype för företag, nytta av en fullständig globala adresslistan så att de kan skicka e-post och anropa alla. Med standardkonfigurationen, skulle de ha samma upplevelse som de kan ha med en lokal implementering av Exchange och Lync.

I vissa fall använder du obligatoriska göra vissa ändringar i standardkonfigurationen. Här följer några exempel:

* Du planerar att använda den [flera Azure AD directory-topologi](active-directory-aadconnect-topologies.md#each-object-only-once-in-an-azure-ad-tenant). Måste du använda ett filter för att styra vilka objekt som synkroniseras till en viss Azure AD-katalog.
* Du kör en pilot för Azure eller Office 365 och du vill endast en delmängd användare i Azure AD. I små pilotprojektet är det inte viktigt att ha en fullständig globala adresslistan att demonstrera funktionerna.
* Du har många tjänstkonton och andra pålitligheten konton som du inte vill i Azure AD.
* Av kompatibilitetsskäl, kan du inte tar bort alla användare konton lokalt. Du bara inaktivera dem. Men i Azure AD kan du bara vill att aktiva konton måste finnas.

Den här artikeln beskriver hur du konfigurerar olika filtreringsmetoder.

> [!IMPORTANT]
> Microsoft stöder inte ändring eller hantering av Azure AD Connect-synkronisering utöver de åtgärder som är formellt dokumenterade. Sådana åtgärder kan göra att Azure AD Connect-synkroniseringen hamnar i ett inkonsekvent tillstånd eller ett tillstånd som inte stöds. Därför kan Microsoft inte tillhandahålla teknisk support för sådana distributioner.

## <a name="basics-and-important-notes"></a>Grunderna och viktig information
Du kan aktivera filtrering när som helst i Azure AD Connect-synkronisering. Om du börjar med en standardkonfiguration av katalogsynkronisering och sedan konfigurera filtrering är de objekt som har filtrerats ut synkroniserade till Azure AD. Alla objekt i Azure AD som tidigare har synkroniserats men filtrerades sedan raderas på grund av den här ändringen i Azure AD.

Innan du börjar att göra ändringar i filtrering, kontrollerar du att du [inaktivera den schemalagda aktiviteten](#disable-scheduled-task) så att du inte exportera ändringar som du ännu inte har verifierat att rätt av misstag.

Eftersom filtrering kan det ta bort många objekt på samma gång, som du vill kontrollera att din nya filter är korrekt innan du börjar exportera ändringar till Azure AD. När du har slutfört konfigurationsstegen, vi rekommenderar starkt att du följer den [verifieringssteg](#apply-and-verify-changes) innan du exporterar och göra ändringar i Azure AD.

Att skydda dig från att ta bort många objekt av misstag, funktionen ”[förhindra oavsiktliga borttagningar](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)” är aktiverad som standard. Om du tar bort många objekt på grund av filtrering (500 som standard), måste du följa stegen i den här artikeln för att tillåta borttagningar gå igenom till Azure AD.

Om du använder en version före November 2015 ([1.0.9125](active-directory-aadconnect-version-history.md#1091250)), gör en ändring i en filterkonfiguration och använder hash-synkronisering av lösenord, måste du starta en fullständig synkronisering av alla lösenord när du har slutfört konfigurationen. Stegvisa instruktioner för hur du utlöser en fullständig synkronisering av lösenord finns [utlösa en fullständig synkronisering av alla lösenord](active-directory-aadconnectsync-troubleshoot-password-hash-synchronization.md#trigger-a-full-sync-of-all-passwords). Om du är på version 1.0.9125 eller senare, sedan vanliga **fullständig synkronisering** åtgärd beräknar även om lösenord ska synkroniseras och om den här extra steg krävs inte längre.

Om **användaren** objekt togs bort av misstag i Azure AD på grund av ett filterfel, kan du återskapa användarobjekt i Azure AD genom att ta bort dina filtrering konfigurationer. Du kan sedan synkronisera dina kataloger igen. Den här åtgärden återställer användare från Papperskorgen i Azure AD. Du kan dock återställa andra objekttyper. Till exempel om du av misstag tar bort en säkerhetsgrupp och den användes för att ACL kan en resurs, gruppen och dess ACL: er inte återställas.

Azure AD Connect tar bara bort objekt som den har en gång anses vara i omfånget. Om det finns objekt i Azure AD som har skapats av en annan Synkroniseringsmotorn och objekten som inte finns i omfattningen, att lägga till filtrering inte ta bort dem. Till exempel om du börjar med en DirSync-server som skapats av en fullständig kopia av hela katalogen i Azure AD, och du installerar en ny Azure AD Connect-synkroniseringsserver parallellt med filtrering aktiverat från början, Azure AD Connect inte ta bort de extra objekt som skapas av DirSync.

Filtrera konfigurationen bevaras när du installerar eller uppgraderar till en nyare version av Azure AD Connect. Det är alltid en bra idé att kontrollera att konfigurationen inte oavsiktligt ändrats efter en uppgradering till en nyare version innan du kör den första synkroniseringscykeln.

Om du har mer än en skog, måste du använda filtrering konfigurationerna som beskrivs i det här avsnittet för varje skog (förutsatt att du vill att samma konfiguration för alla).

### <a name="disable-the-scheduled-task"></a>Inaktivera den schemalagda aktiviteten
Följ dessa steg om du vill inaktivera inbyggda Schemaläggaren som utlöser en synkroniseringscykel var 30: e minut:

1. Gå till en PowerShell-prompt.
2. Kör `Set-ADSyncScheduler -SyncCycleEnabled $False` att inaktivera Schemaläggaren.
3. Gör de ändringar som beskrivs i den här artikeln.
4. Kör `Set-ADSyncScheduler -SyncCycleEnabled $True` aktivera Schemaläggaren igen.

**Om du använder en Azure AD Connect-version innan 1.1.105.0**  
Följ dessa steg om du vill inaktivera den schemalagda aktiviteten som utlöser en synkroniseringscykel alla tre timmar:

1. Starta **Schemaläggaren** från den **starta** menyn.
2. Direkt under **bibliotek för Schemaläggaren**, hitta aktiviteten **Azure AD Sync Scheduler**, högerklicka och välj **inaktivera**.  
   ![Schemaläggaren](./media/active-directory-aadconnectsync-configure-filtering/taskscheduler.png)  
3. Du kan nu göra konfigurationsändringar och köra Synkroniseringsmotorn manuellt från den **Synchronization Service Manager** konsolen.

När du har slutfört alla filtrering ändringar, Glöm inte att gå tillbaka och **aktivera** aktiviteten igen.

## <a name="filtering-options"></a>Alternativ för filtrering
Du kan använda följande konfigurationstyper av filtrering för verktyg för directory-synkronisering:

* [**Gruppbaserade**](#group-based-filtering): filtrering baserat på en grupp kan endast konfigureras på första installationen med hjälp av installationsguiden.
* [**Domänbaserade**](#domain-based-filtering): med det här alternativet kan du välja vilka domäner synkronisera till Azure AD. Du kan också lägga till och ta bort domäner från synkroniseringskonfiguration för motorn när du gör ändringar i din lokala infrastruktur när du har installerat Azure AD Connect-synkronisering.
* [**Organisationsenhet (OU) – baserad**](#organizational-unitbased-filtering): med det här alternativet kan du välja vilka organisationsenheter synkronisera till Azure AD. Det här alternativet är för alla typer av objekt i valda organisationsenheter.
* [**Attributbaserad**](#attribute-based-filtering): med det här alternativet kan du filtrera objekt baserat på attributvärden för objekten. Du kan också ha olika filter för olika objekttyper.

Du kan använda flera alternativ för filtrering på samma gång. T.ex, kan du använda OU-baserade filtrering som bara inkluderar objekt i en Organisationsenhet. Samtidigt, kan du använda attributbaserad filtrering för att filtrera objekt längre. När du använder flera filtreringsmetoder filter att använda en logisk ”AND” mellan filtren.

## <a name="domain-based-filtering"></a>Domänbaserad filtrering
Det här avsnittet ger dig hur du konfigurerar filtret domän. Om du har lagt till eller ta bort domäner i skogen när du har installerat Azure AD Connect måste du även uppdatera konfigurationen för filtrering.

Det bästa sättet att ändra domänbaserad filtrering är genom att köra installationsguiden och ändra [domän- och organisationsenhetsfiltrering](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering). Installationsguiden automatiserar uppgifter som beskrivs i det här avsnittet.

Du bör bara följa dessa steg om det inte går att köra installationsguiden av någon anledning.

Domänbaserad filtrering konfigurationen består av de här stegen:

1. [Markera domänerna](#select-domains-to-be-synchronized) som du vill inkludera i synkroniseringen.
2. För varje lagt till och ta bort domänen kan du justera de [körningsprofiler](#update-run-profiles).
3. [Tillämpa och verifiera ändringar](#apply-and-verify-changes).

### <a name="select-the-domains-to-be-synchronized"></a>Markera domänerna som ska synkroniseras
För att ange filtret domän, gör du följande steg:

1. Logga in på den server som kör Azure AD Connect-synkronisering genom att använda ett konto som är medlem i den **ADSyncAdmins** säkerhetsgrupp.
2. Starta **synkroniseringstjänsten** från den **starta** menyn.
3. Välj **kopplingar**, och i den **kopplingar** väljer kopplingen med typen **Active Directory Domain Services**. I **åtgärder**väljer **egenskaper**.  
   ![Kopplingsegenskaper](./media/active-directory-aadconnectsync-configure-filtering/connectorproperties.png)  
4. Klicka på **konfigurera katalogpartitioner**.
5. I den **Välj katalogpartitioner** markerar och avmarkerar domäner efter behov. Kontrollera att endast de partitioner som du vill synkronisera är markerade.  
   ![Partitioner](./media/active-directory-aadconnectsync-configure-filtering/connectorpartitions.png)  
   Om du har ändrat din lokala Active Directory-infrastruktur och lagts till eller ta bort domäner från skogen, klicka på den **uppdatera** för att få en uppdaterad lista. När du uppdaterar, uppmanas du att ange autentiseringsuppgifter. Ange inga autentiseringsuppgifter med läsbehörighet till Windows Server Active Directory. Den har inte den användare som är förinställd i dialogrutan.  
   ![Uppdatering behövs](./media/active-directory-aadconnectsync-configure-filtering/refreshneeded.png)  
6. När du är klar stänger du den **egenskaper** dialogrutan genom att klicka på **OK**. Om du har tagit bort domäner från skogen ett popup-meddelande som anger att en domän har tagits bort och att konfigurationen rensas.
7. Fortsätta att justera det [körningsprofiler](#update-run-profiles).

### <a name="update-the-run-profiles"></a>Uppdatera körning av profiler
Om du har uppdaterat din domän-filter, behöver du även uppdatera körning av profiler.

1. I den **kopplingar** lista, se till att den koppling som du har ändrat i föregående steg är markerad. I **åtgärder**väljer **Konfigurera körningsprofiler**.  
   ![Kopplingen körningsprofiler 1](./media/active-directory-aadconnectsync-configure-filtering/connectorrunprofiles1.png)  
2. Hitta och identifiera följande profiler:
    * Fullständig Import
    * Fullständig synkronisering
    * Deltaimport
    * Deltasynkronisering
    * Exportera
3. Varje profil, justera det **läggs** och **bort** domäner.
    1. För var och en av fem profiler gör du följande steg för varje **läggs** domän:
        1. Välj körningsprofilen och på **nytt steg**.
        2. På den **konfigurera steg** sidan den **typen** listrutan väljer du steget-typ med samma namn som den profil som du konfigurerar. Klicka sedan på **Nästa**.  
        ![Kopplingen körningsprofiler 2](./media/active-directory-aadconnectsync-configure-filtering/runprofilesnewstep1.png)  
        3. På den **Kopplingskonfiguration** sidan den **Partition** nedrullningsbara menyn väljer du namnet på den domän som du har lagt till din domän-filter.  
        ![Kopplingen körningsprofiler 3](./media/active-directory-aadconnectsync-configure-filtering/runprofilesnewstep2.png)  
        4. Stäng den **konfigurera Körningsprofil** dialogrutan klickar du på **Slutför**.
    2. För var och en av fem profiler gör du följande steg för varje **bort** domän:
        1. Välj körningsprofilen.
        2. Om den **värdet** av den **Partition** attributet är ett GUID och välj steget Kör klickar du på **ta bort steg**.  
        ![Kopplingen körningsprofiler 4](./media/active-directory-aadconnectsync-configure-filtering/runprofilesdeletestep.png)  
    3. Kontrollera din ändring. Varje domän som du vill synkronisera ska visas som ett steg i varje körningsprofil.
4. Stäng den **Konfigurera körningsprofiler** dialogrutan klickar du på **OK**.
5.  Om du vill slutföra konfigurationen måste du köra en **fullständig import** och en **Deltasynkronisering**. Fortsätta att läsa avsnittet [tillämpa och verifiera ändringar](#apply-and-verify-changes).

## <a name="organizational-unitbased-filtering"></a>Organisatorisk enhet – baserad filtrering
Det bästa sättet att ändra OU-baserade filtrering är genom att köra installationsguiden och ändra [domän- och organisationsenhetsfiltrering](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering). Installationsguiden automatiserar uppgifter som beskrivs i det här avsnittet.

Du bör bara följa dessa steg om det inte går att köra installationsguiden av någon anledning.

Om du vill konfigurera organisationens enhet – baserad filtrering, gör du följande steg:

1. Logga in på den server som kör Azure AD Connect-synkronisering genom att använda ett konto som är medlem i den **ADSyncAdmins** säkerhetsgrupp.
2. Starta **synkroniseringstjänsten** från den **starta** menyn.
3. Välj **kopplingar**, och i den **kopplingar** väljer kopplingen med typen **Active Directory Domain Services**. I **åtgärder**väljer **egenskaper**.  
   ![Kopplingsegenskaper](./media/active-directory-aadconnectsync-configure-filtering/connectorproperties.png)  
4. Klicka på **konfigurera katalogpartitioner**, Välj den domän som du vill konfigurera och klicka sedan på **behållare**.
5. När du uppmanas ange någon autentiseringsuppgifter med läsbehörighet till din lokala Active Directory. Den har inte den användare som är förinställd i dialogrutan.
6. I den **Välj behållare** dialogrutan rutan tar du bort organisationsenheter som du inte vill synkronisera med molnkatalogen och klicka sedan på **OK**.  
   ![Organisationsenheter i dialogrutan Välj behållare](./media/active-directory-aadconnectsync-configure-filtering/ou.png)  
   * Den **datorer** behållaren ska vara markerad för Windows 10-datorer som har ska synkroniseras till Azure AD. Om din domänanslutna datorer finns i andra organisationsenheter, kontrollerar du de är markerade.
   * Du ska markera behållaren **ForeignSecurityPrincipals** om du har flera skogar med förtroenden. Med den här behållaren kan säkerhetsgruppmedlemskap i flera skogar lösas.
   * Den **Registreradeenheter** OU måste väljas om du har aktiverat funktionen för tillbakaskrivning av enhet. Om du använder en annan tillbakaskrivning funktion, till exempel tillbakaskrivning av grupp, kontrollera dessa platser är markerade.
   * Välj andra Organisationsenheter som där användare, InetOrgPerson, grupper, kontakter och datorer finns. I bilden finns alla organisationsenheter i ManagedObjects OU.
   * Om du använder gruppbaserade filtrering, måste den Organisationsenhet där gruppen finns tas med.
   * Observera att du kan konfigurera om nya organisationsenheter som läggs till när filtrering konfigurationen är klar synkroniseras eller inte synkroniserats. Finns i nästa avsnitt för mer information.
7. När du är klar stänger du den **egenskaper** dialogrutan genom att klicka på **OK**.
8. Om du vill slutföra konfigurationen måste du köra en **fullständig import** och en **Deltasynkronisering**. Fortsätta att läsa avsnittet [tillämpa och verifiera ändringar](#apply-and-verify-changes).

### <a name="synchronize-new-ous"></a>Synkronisera nya organisationsenheter
Nya organisationsenheter som skapas efter filtrering har konfigurerats synkroniseras som standard. Detta tillstånd indikeras av en markerad kryssruta. Du kan också avmarkera vissa sub organisationsenheter. Få det här problemet klickar du på rutan tills den blir en vit med en blå bock (standardtillståndet). Sedan avmarkera alla sub-organisationsenheter som du inte vill synkronisera.

Om alla sub-organisationsenheter synkroniseras är rutan vitt med en blå markering.  
![Organisationsenhet med alla markerade rutor](./media/active-directory-aadconnectsync-configure-filtering/ousyncnewall.png)

Om vissa sub organisationsenheter har inte är markerad, är rutan grå med en vit markering.  
![Organisationsenhet med vissa sub organisationsenheter omarkerade](./media/active-directory-aadconnectsync-configure-filtering/ousyncnew.png)

En ny Organisationsenhet som skapades under ManagedObjects synkroniseras med den här konfigurationen.

Azure AD Connect skapas-installationen alltid den här konfigurationen.

### <a name="dont-synchronize-new-ous"></a>Synkronisera inte nya organisationsenheter
Du kan konfigurera Synkroniseringsmotorn för att synkronisera nya organisationsenheter inte efter filtrering konfigurationen har slutförts. Det här tillståndet anges i Gränssnittet visas helt grått med en bockmarkering bredvid. Få det här problemet klickar du på rutan tills den blir en vit med en bockmarkering. Välj sedan sub-organisationsenheter som du vill synkronisera.

![Organisationsenhet med roten omarkerade](./media/active-directory-aadconnectsync-configure-filtering/oudonotsyncnew.png)

En ny Organisationsenhet som skapades under ManagedObjects är inte synkroniserat med den här konfigurationen.

## <a name="attribute-based-filtering"></a>Attributbaserad filtrering
Kontrollera att du använder November 2015 ([1.0.9125](active-directory-aadconnect-version-history.md#1091250)) eller senare skapa för dessa åtgärder ska fungera.

Attributbaserad filtrering är det mest flexibla sättet att filtreringsobjekt. Du kan använda [deklarativ etablering](active-directory-aadconnectsync-understanding-declarative-provisioning.md) att styra nästan alla aspekter av när ett objekt har synkroniserats till Azure AD.

Du kan använda [inkommande](#inbound-filtering) filtrering från Active Directory för metaversum, och [utgående](#outbound-filtering) filtrering från metaversum till Azure AD. Vi rekommenderar att du tillämpar inkommande filtrering eftersom det är den enklaste att underhålla. Du bör endast använda utgående filtrering om det krävs för att ansluta till objekt från mer än en skog innan utvärderingen kan äga rum.

### <a name="inbound-filtering"></a>Inkommande filtrering
Filtrering av inkommande använder standardkonfigurationen där objekt som kommer att Azure AD måste ha metaversum attributet cloudFiltered som inte har angetts till ett värde som ska synkroniseras. Om det här attributet har värdet **SANT**, och sedan objektet har inte synkroniserats. Det bör inte sättas till **FALSKT**, avsiktligt. Om du vill kontrollera andra regler har möjlighet att bidra med ett värde, det här attributet endast ska ha värden **SANT** eller **NULL** (saknas).

I inkommande filtrering, använder du kraften hos **omfång** att avgöra vilka objekt som ska synkronisera eller synkroniseras inte. Det är där du kan göra justeringar så att de passar din organisation krav. Scope-modulen har en **grupp** och en **satsen** att avgöra när en synkroniseringsregel är inom omfånget. En grupp innehåller en eller flera satser. Det finns en logisk ”AND” mellan flera villkor och ett logiskt ”eller” mellan flera grupper.

Låt oss titta på ett exempel:  
![Omfång](./media/active-directory-aadconnectsync-configure-filtering/scope.png)  
Detta ska läsas som **(avdelning = IT) eller (avdelning = försäljnings-och c = US)**.

Du använder användarobjektet som ett exempel i följande exempel och steg, men du kan använda detta för alla typer av objekt.

I följande exempel startar prioritet värdet med 50. Detta kan vara ett tal som inte används, men måste vara lägre än 100.

#### <a name="negative-filtering-do-not-sync-these"></a>Negativt filtrering: ”är inte synkroniserade dessa”
I följande exempel filtrera bort (inte synkronisera) alla användare där **extensionAttribute15** har värdet **NoSync**.

1. Logga in på den server som kör Azure AD Connect-synkronisering genom att använda ett konto som är medlem i den **ADSyncAdmins** säkerhetsgrupp.
2. Starta **Synchronization regler Editor** från den **starta** menyn.
3. Kontrollera att **inkommande** är markerad och klicka på **Lägg till ny regel**.
4. Ge regeln ett beskrivande namn, exempelvis ”*i från AD-användare DoNotSyncFilter*”. Väljer rätt skogen, väljer **användare** som den **CS objekttyp**, och välj **Person** som den **MV objekttyp**. I **länktypen**väljer **ansluta**. I **prioritet**, ange ett värde som inte är för närvarande används av en annan regel för synkronisering (till exempel 50) och klicka sedan på **nästa**.  
   ![Inkommande 1 beskrivning](./media/active-directory-aadconnectsync-configure-filtering/inbound1.png)  
5. I **Scoping filter**, klickar du på **Lägg till grupp**, och klicka på **Lägg till sats**. I **attributet**väljer **ExtensionAttribute15**. Se till att **operatorn** är inställd på **lika**, och skriv sedan värdet **NoSync** i den **värdet** rutan. Klicka på **Nästa**.  
   ![Inkommande 2 omfång](./media/active-directory-aadconnectsync-configure-filtering/inbound2.png)  
6. Lämna den **ansluta** regler tom och klickar sedan på **nästa**.
7. Klicka på **omvandlingen Lägg**, Välj den **ExchangeRate för FlowType** som **konstant**, och välj **cloudFiltered** som den **målattribut**. I den **källa** skriver **SANT**. Klicka på **Lägg till** spara regeln.  
   ![Inkommande 3 omvandling](./media/active-directory-aadconnectsync-configure-filtering/inbound3.png)
8. Om du vill slutföra konfigurationen måste du köra en **fullständig synkronisering**. Fortsätta att läsa avsnittet [tillämpa och verifiera ändringar](#apply-and-verify-changes).

#### <a name="positive-filtering-only-sync-these"></a>Positivt filtrering: ”bara synkronisera dessa”
Uttrycka positivt filtrering kan vara mer utmanande eftersom du behöver överväga objekt som inte är uppenbara ska synkroniseras, till exempel konferensrum. Du även ska åsidosätta standardfiltret i out-of-box-regeln **i från AD - användare ansluta**. När du skapar anpassade filtret, kontrollera att inte inkludera kritiska systemobjekt, konflikt replikeringsobjekt, särskilda postlådor och tjänstkontona för Azure AD Connect.

Positivt filtreringsalternativ kräver två regler för synkronisering. Du behöver en regel (eller flera) med rätt omfattning av objekt som ska synkroniseras. Du måste också en andra fångar in alla synkroniseringsregel som filtrerar ut alla objekt som ännu inte har identifierats som ett objekt som ska synkroniseras.

I följande exempel du bara synkronisera användarobjekt där attributet avdelning har värdet **försäljning**.

1. Logga in på den server som kör Azure AD Connect-synkronisering genom att använda ett konto som är medlem i den **ADSyncAdmins** säkerhetsgrupp.
2. Starta **Synchronization regler Editor** från den **starta** menyn.
3. Kontrollera att **inkommande** är markerad och klicka på **Lägg till ny regel**.
4. Ge regeln ett beskrivande namn, exempelvis ”*i från AD-användaren försäljning synkronisera*”. Väljer rätt skogen, väljer **användare** som den **CS objekttyp**, och välj **Person** som den **MV objekttyp**. I **länktypen**väljer **ansluta**. I **prioritet**, ange ett värde som inte är för närvarande används av en annan regel för synkronisering (till exempel 51) och klicka sedan på **nästa**.  
   ![Inkommande 4 beskrivning](./media/active-directory-aadconnectsync-configure-filtering/inbound4.png)  
5. I **Scoping filter**, klickar du på **Lägg till grupp**, och klicka på **Lägg till sats**. I **attributet**väljer **avdelning**. Kontrollera att Operator har angetts till **lika**, och skriv sedan värdet **försäljning** i den **värdet** rutan. Klicka på **Nästa**.  
   ![Inkommande 5 omfång](./media/active-directory-aadconnectsync-configure-filtering/inbound5.png)  
6. Lämna den **ansluta** regler tom och klickar sedan på **nästa**.
7. Klicka på **omvandlingen Lägg**väljer **konstant** som den **ExchangeRate för FlowType**, och välj den **cloudFiltered** som den **målattribut**. I den **källa** skriver **FALSKT**. Klicka på **Lägg till** spara regeln.  
   ![Inkommande 6 omvandling](./media/active-directory-aadconnectsync-configure-filtering/inbound6.png)  
   Detta är ett specialfall där du uttryckligen ställa in cloudFiltered till **FALSKT**.
8. Vi har nu att skapa regeln fångar in alla synkronisering. Ge regeln ett beskrivande namn, exempelvis ”*i från AD-användaren fångar in alla filter*”. Väljer rätt skogen, väljer **användare** som den **CS objekttyp**, och välj **Person** som den **MV objekttyp**. I **länktypen**väljer **ansluta**. I **prioritet**, ange ett värde som inte är för närvarande används av en annan regel för synkronisering (till exempel 99). Du har markerat ett värde för prioritet som är högre (lägre prioritet) än tidigare synkroniseringsregel. Men du har också utrymme så att du kan lägga till flera sync filtreringsregler senare när du vill starta synkroniseringen ytterligare avdelningar. Klicka på **Nästa**.  
   ![Inkommande 7 beskrivning](./media/active-directory-aadconnectsync-configure-filtering/inbound7.png)  
9. Lämna **Scoping filter** tom och klickar på **nästa**. Ett tomt filter anger att regeln ska tillämpas på alla objekt.
10. Lämna den **ansluta** regler tom och klickar sedan på **nästa**.
11. Klicka på **omvandlingen Lägg**väljer **konstant** som den **ExchangeRate för FlowType**, och välj **cloudFiltered** som den **målattribut**. I den **källa** skriver **SANT**. Klicka på **Lägg till** spara regeln.  
    ![Inkommande 3 omvandling](./media/active-directory-aadconnectsync-configure-filtering/inbound3.png)  
12. Om du vill slutföra konfigurationen måste du köra en **fullständig synkronisering**. Fortsätta att läsa avsnittet [tillämpa och verifiera ändringar](#apply-and-verify-changes).

Om du behöver, kan du skapa flera regler av typen första där du kan inkludera flera objekt i synkroniseringen.

### <a name="outbound-filtering"></a>Utgående filtrering
I vissa fall är det nödvändigt att utföra filtrering förrän objekten har anslutna i metaversum. Exempelvis kan det vara nödvändigt att titta på e-postattribut från resursskogen och attributet userPrincipalName från kontoskogen att avgöra om ett objekt som ska synkroniseras. I dessa fall kan skapa du filtrering på den utgående regeln.

I det här exemplet du ändra filtreringen så att endast användare som har både sina e-post och userPrincipalName som slutar på @contoso.com synkroniseras:

1. Logga in på den server som kör Azure AD Connect-synkronisering genom att använda ett konto som är medlem i den **ADSyncAdmins** säkerhetsgrupp.
2. Starta **Synchronization regler Editor** från den **starta** menyn.
3. Under **typ av identifieringsregler**, klickar du på **utgående**.
4. Beroende på vilken version av Connect som du använder antingen hitta regeln med namnet **på AAD-användare ansluta** eller **ut till AAD - användare ansluta till SOAInAD**, och klicka på **redigera**.
5. I popup-fönstret besvara **Ja** att skapa en kopia av regeln.
6. På den **beskrivning** ändrar **prioritet** till ett värde som inte används, till exempel 50.
7. Klicka på **Scoping filter** på vänstra navigeringsfönstret och klicka sedan på **Lägg till sats**. I **attributet**väljer **e**. I **operatorn**väljer **ENDSWITH**. I **värdet**, typen **@contoso.com**, och klicka sedan på **Lägg till sats**. I **attributet**väljer **userPrincipalName**. I **operatorn**väljer **ENDSWITH**. I **värdet**, typen **@contoso.com**.
8. Klicka på **Spara**.
9. Om du vill slutföra konfigurationen måste du köra en **fullständig synkronisering**. Fortsätta att läsa avsnittet [tillämpa och verifiera ändringar](#apply-and-verify-changes).

## <a name="apply-and-verify-changes"></a>Tillämpa och verifiera ändringar
När du har gjort konfigurationsändringarna, måste du använda dem för objekt som redan finns i systemet. Det kan också vara objekt som inte är för närvarande i Synkroniseringsmotorn ska bearbetas (och Synkroniseringsmotorn måste läsas i källsystemet igen för att verifiera dess innehåll).

Om du har ändrat konfigurationen med hjälp av **domän** eller **organisationsenhet** filtrering, måste du göra en **fullständig import**, följt av **Deltasynkronisering**.

Om du har ändrat konfigurationen med hjälp av **attributet** filtrering, måste du göra en **fullständig synkronisering**.

Utför följande steg:

1. Starta **synkroniseringstjänsten** från den **starta** menyn.
2. Välj **kopplingar**. I den **kopplingar** listan, Välj den koppling som du har gjort en konfigurationsändring tidigare. I **åtgärder**väljer **kör**.  
   ![Koppling som kör](./media/active-directory-aadconnectsync-configure-filtering/connectorrun.png)  
3. I **körningsprofiler**, Välj åtgärden som nämndes i föregående avsnitt. Om du behöver köra två åtgärder, kör du andra efter den första som har slutförts. (Den **tillstånd** kolumnen är **inaktivt** för den markerade kopplingen.)

Efter synkroniseringen går mellanlagras alla ändringar som för att exporteras. Innan du utför ändringarna i Azure AD, som du vill kontrollera att alla dessa ändringar är korrekta.

1. Starta en kommandotolk och gå till `%Program Files%\Microsoft Azure AD Sync\bin`.
2. Kör `csexport "Name of Connector" %temp%\export.xml /f:x`.  
   Namnet på anslutningen finns i synkroniseringstjänsten. Den har ett namn som liknar ”contoso.com – AAD” för Azure AD.
3. Kör `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv`.
4. Nu har du en fil i % temp % med namnet export.csv som kan granskas i Microsoft Excel. Den här filen innehåller alla ändringar som ska exporteras.
5. Gör nödvändiga ändringar av informationen eller konfigurationen och köra de här stegen igen (Import, synkronisering och kontrollera) tills ändringarna som ska exporteras är vad du förväntar dig.

När du är nöjd exportera ändringarna till Azure AD.

1. Välj **kopplingar**. I den **kopplingar** Välj Azure AD Connector. I **åtgärder**väljer **kör**.
2. I **körningsprofiler**väljer **exportera**.
3. Om konfigurationsändringarna tar bort många objekt kan se du ett fel i exporten när antalet är större än det konfigurerade tröskelvärdet (som standard 500). Om du ser detta fel, måste du tillfälligt inaktivera det ”[förhindra oavsiktliga borttagningar](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)” funktionen.

Nu är det dags att aktivera Schemaläggaren igen.

1. Starta **Schemaläggaren** från den **starta** menyn.
2. Direkt under **bibliotek för Schemaläggaren**, hitta aktiviteten **Azure AD Sync Scheduler**, högerklicka och välj **aktivera**.

## <a name="group-based-filtering"></a>Gruppbaserade filtrering
Du kan konfigurera gruppbaserade filtrering första gången du installerar Azure AD Connect med hjälp av [anpassad installation](active-directory-aadconnect-get-started-custom.md#sync-filtering-based-on-groups). Den är avsedd för en pilotdistribution där du vill att endast en liten uppsättning objekt som ska synkroniseras. När du inaktiverar gruppbaserade filtrering kan aktiveras inte igen. Den har *stöds inte* att använda gruppbaserade filtrering i en anpassad konfiguration. Det kan bara användas om du vill konfigurera den här funktionen med hjälp av installationsguiden. När du har slutfört din pilot och sedan använda en av de andra filtreringsalternativ i det här avsnittet. När du använder OU-baserade filtrering tillsammans med gruppbaserade filtrering måste organisationsenheterna där gruppen och dess medlemmar finns tas med.

Du kan konfigurera gruppbaserade filtrering genom att ange en annan grupp för varje AD-koppling när du synkroniserar flera AD-skogar. Om du inte vill för att synkronisera en användare i en AD skog och samma användare har en eller flera motsvarande objekt i andra AD-skogar, måste du se till att användarobjektet och dess motsvarande objekt inom gruppbaserade filtrerar omfång. Exempel:

* Du har en användare i en skog som har ett motsvarande FSP (sekundärt säkerhetsobjekt) objekt i en annan skog. Båda objekten måste inom gruppbaserade filtrera scope. Annars kommer användaren inte att synkroniseras till Azure AD.

* Du har en användare i en skog som har en motsvarande resurskonto (t.ex. länkad postlåda) i en annan skog. Dessutom har du konfigurerat Azure AD Connect för att länka användare med resurskonto. Båda objekten måste inom gruppbaserade filtrera scope. Annars kommer användaren inte att synkroniseras till Azure AD.

* Du har en användare i en skog som har en motsvarande post Kontakta i en annan skog. Dessutom har du konfigurerat Azure AD Connect för att länka användare med e-post kontakten. Båda objekten måste inom gruppbaserade filtrera scope. Annars kommer användaren inte att synkroniseras till Azure AD.


## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [Azure AD Connect-synkronisering](active-directory-aadconnectsync-whatis.md) konfiguration.
- Lär dig mer om [integrera dina lokala identiteter med Azure AD](active-directory-aadconnect.md).
