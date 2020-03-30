---
title: 'Synkronisering av Azure AD Connect: Konfigurera filtrering | Microsoft-dokument'
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
ms.openlocfilehash: 983699dfbfe3e8fa332da4810d1514a11029077f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261104"
---
# <a name="azure-ad-connect-sync-configure-filtering"></a>Azure AD Connect-synkronisering: Konfigurera filtrering
Genom att använda filtrering kan du styra vilka objekt som visas i Azure Active Directory (Azure AD) från din lokala katalog. Standardkonfigurationen tar alla objekt i alla domäner i de konfigurerade skogarna. I allmänhet är detta den rekommenderade konfigurationen. Användare som använder Office 365-arbetsbelastningar, till exempel Exchange Online och Skype för företag, kan dra nytta av en fullständig global adresslista så att de kan skicka e-post och ringa alla. Med standardkonfigurationen skulle de ha samma upplevelse som de skulle ha med en lokal implementering av Exchange eller Lync.

I vissa fall måste du dock göra vissa ändringar i standardkonfigurationen. Här följer några exempel:

* Du planerar att använda [topologin för flera Azure AD-katalogen](plan-connect-topologies.md#each-object-only-once-in-an-azure-ad-tenant). Sedan måste du använda ett filter för att styra vilka objekt som synkroniseras till en viss Azure AD-katalog.
* Du kör en pilot för Azure eller Office 365 och du vill bara ha en delmängd av användare i Azure AD. I den lilla piloten är det inte viktigt att ha en fullständig global adresslista för att demonstrera funktionaliteten.
* Du har många tjänstkonton och andra icke-personliga konton som du inte vill ha i Azure AD.
* Av efterlevnadsskäl tar du inte bort några användarkonton lokalt. Du inaktiverar dem bara. Men i Azure AD vill du bara att aktiva konton ska finnas.

Den här artikeln beskriver hur du konfigurerar de olika filtreringsmetoderna.

> [!IMPORTANT]
> Microsoft stöder inte ändring eller hantering av Azure AD Connect-synkronisering utöver de åtgärder som är formellt dokumenterade. Alla dessa åtgärder kan resultera i ett inkonsekvent eller icke-stödt tillstånd för Azure AD Connect-synkronisering. Därför kan Microsoft inte tillhandahålla teknisk support för sådana distributioner.

## <a name="basics-and-important-notes"></a>Grunderna och viktiga anteckningar
I Azure AD Connect-synkronisering kan du aktivera filtrering när som helst. Om du börjar med en standardkonfiguration av katalogsynkronisering och sedan konfigurerar filtrering, synkroniseras inte längre de objekt som filtreras ut till Azure AD. På grund av den här ändringen tas alla objekt i Azure AD som tidigare synkroniserats men sedan filtrerats bort i Azure AD.

Innan du börjar göra ändringar i filtreringen måste du [inaktivera den schemalagda aktiviteten](#disable-the-scheduled-task) så att du inte av misstag exporterar ändringar som du ännu inte har verifierat för att vara korrekta.

Eftersom filtrering kan ta bort många objekt samtidigt vill du se till att dina nya filter är korrekta innan du börjar exportera ändringar till Azure AD. När du har slutfört konfigurationsstegen rekommenderar vi starkt att du följer [verifieringsstegen](#apply-and-verify-changes) innan du exporterar och gör ändringar i Azure AD.

För att skydda dig från att ta bort många objekt av misstag är funktionen "[förhindra oavsiktliga borttagningar](how-to-connect-sync-feature-prevent-accidental-deletes.md)" aktiverad som standard. Om du tar bort många objekt på grund av filtrering (500 som standard) måste du följa stegen i den här artikeln så att borttagningarna kan gå till Azure AD.

Om du använder en version före november 2015 ([1.0.9125](reference-connect-version-history.md#1091250)), gör en ändring i en filterkonfiguration och använder synkronisering av lösenord hash- ska du utlösa en fullständig synkronisering av alla lösenord när du har slutfört konfigurationen. Steg om hur du utlöser en fullständig synkronisering av ett lösenord finns i [Utlösa en fullständig synkronisering av alla lösenord](tshoot-connect-password-hash-synchronization.md#trigger-a-full-sync-of-all-passwords). Om du är på build 1.0.9125 eller senare, beräknar den vanliga **fullständiga synkroniseringsåtgärden** också om lösenord ska synkroniseras och om det här extra steget inte längre krävs.

Om **användarobjekt** av misstag togs bort i Azure AD på grund av ett filtreringsfel kan du återskapa användarobjekten i Azure AD genom att ta bort dina filtreringskonfigurationer. Sedan kan du synkronisera dina kataloger igen. Den här åtgärden återställer användare från papperskorgen i Azure AD. Du kan dock inte ta bort andra objekttyper. Om du till exempel av misstag tar bort en säkerhetsgrupp och den användes för att acl en resurs, kan gruppen och dess ACL inte återställas.

Azure AD Connect tar bara bort objekt som den en gång har ansett vara i omfånget. Om det finns objekt i Azure AD som har skapats av en annan synkroniseringsmotor och dessa objekt inte är i omfång, tas inte dem bort genom att lägga till filtrering. Om du till exempel börjar med en DirSync-server som skapade en fullständig kopia av hela katalogen i Azure AD och installerar en ny Azure AD Connect-synkroniseringsserver parallellt med filtrering aktiverad från början, tar Azure AD Connect inte bort de extra objekten som skapas av DirSync.

Filtreringskonfigurationen behålls när du installerar eller uppgraderar till en nyare version av Azure AD Connect. Det är alltid en bra idé att kontrollera att konfigurationen inte oavsiktligt ändrades efter en uppgradering till en nyare version innan den första synkroniseringscykeln kördes.

Om du har mer än en skog måste du använda filtreringskonfigurationerna som beskrivs i det här avsnittet på varje skog (förutsatt att du vill ha samma konfiguration för dem alla).

### <a name="disable-the-scheduled-task"></a>Inaktivera den schemalagda aktiviteten
Så här inaktiverar du den inbyggda schemaläggaren som utlöser en synkroniseringscykel var 30:e minut:

1. Gå till en PowerShell-prompt.
2. Kör `Set-ADSyncScheduler -SyncCycleEnabled $False` för att inaktivera schemaläggaren.
3. Gör ändringarna som dokumenteras i den här artikeln.
4. Kör `Set-ADSyncScheduler -SyncCycleEnabled $True` för att aktivera schemaläggaren igen.

**Om du använder en Azure AD Connect-version före 1.1.105.0**  
Så här inaktiverar du den schemalagda aktiviteten som utlöser en synkroniseringscykel var tredje timme:

1. Starta **Schemaläggaren** från **Start-menyn.**
2. Direkt under **Schemaläggarens bibliotek**hittar du uppgiften **Azure AD Sync Scheduler**, högerklickar och väljer **Inaktivera**.  
   ![Schemaläggaren](./media/how-to-connect-sync-configure-filtering/taskscheduler.png)  
3. Nu kan du göra konfigurationsändringar och köra synkroniseringsmotorn manuellt från **konsolen Synkroniseringstjänsthanteraren.**

När du har slutfört alla dina filtreringsändringar ska du inte glömma att komma tillbaka och **aktivera** uppgiften igen.

## <a name="filtering-options"></a>Alternativ för filtrering
Du kan använda följande filtreringskonfigurationstyper på katalogsynkroniseringsverktyget:

* [**Gruppbaserad:**](#group-based-filtering)Filtrering baserad på en enskild grupp kan bara konfigureras vid den första installationen med hjälp av installationsguiden.
* [**Domänbaserad:**](#domain-based-filtering)Genom att använda det här alternativet kan du välja vilka domäner som ska synkroniseras med Azure AD. Du kan också lägga till och ta bort domäner från synkroniseringsmotorkonfigurationen när du gör ändringar i din lokala infrastruktur när du har installerat Azure AD Connect-synkronisering.
* [**Organisationsenhet (OU)–baserad:**](#organizational-unitbased-filtering)Genom att använda det här alternativet kan du välja vilka organisationsenheter som ska synkroniseras med Azure AD. Det här alternativet gäller för alla objekttyper i markerade ru:er.
* [**Attributbaserad:**](#attribute-based-filtering)Genom att använda det här alternativet kan du filtrera objekt baserat på attributvärden för objekten. Du kan också ha olika filter för olika objekttyper.

Du kan använda flera filtreringsalternativ samtidigt. Du kan till exempel använda organisationsenhetsbaserad filtrering för att bara inkludera objekt i en organisationsenhet. Samtidigt kan du använda attributbaserad filtrering för att filtrera objekten ytterligare. När du använder flera filtreringsmetoder använder filtren ett logiskt "OCH" mellan filtren.

## <a name="domain-based-filtering"></a>Domänbaserad filtrering
Det här avsnittet innehåller stegen för att konfigurera domänfiltret. Om du har lagt till eller tagit bort domäner i skogen efter att du har installerat Azure AD Connect måste du också uppdatera filtreringskonfigurationen.

Det bästa sättet att ändra domänbaserad filtrering är att köra installationsguiden och ändra [domän- och organisationsenhetsfiltrering](how-to-connect-install-custom.md#domain-and-ou-filtering). Installationsguiden automatiserar alla uppgifter som dokumenteras i det här avsnittet.

Du bör bara följa dessa steg om du inte kan köra installationsguiden av någon anledning.

Domänbaserad filtreringskonfiguration består av följande steg:

1. Markera de domäner som du vill inkludera i synkroniseringen.
2. Justera körningsprofilerna för varje tillagd och borttagen domän.
3. [Tillämpa och verifiera ändringar](#apply-and-verify-changes).

### <a name="select-the-domains-to-be-synchronized"></a>Välj de domäner som ska synkroniseras
Det finns två sätt att välja vilka domäner som ska synkroniseras:
    - Använda synkroniseringstjänsten
    - Använda Azure AD Connect-guiden.


#### <a name="select-the-domains-to-be-synchronized-using-the-synchronization-service"></a>Välj de domäner som ska synkroniseras med synkroniseringstjänsten
Så här anger du domänfiltret:

1. Logga in på servern som kör Azure AD Connect-synkronisering med hjälp av ett konto som är medlem i säkerhetsgruppen **ADSyncAdmins.**
2. Starta **synkroniseringstjänsten** på **Start-menyn.**
3. Välj **Anslutningsappar**och välj kopplingen med typen **Active Directory Domain Services**i listan **Kopplingar** . Välj **Egenskaper**i **Åtgärder**.  
   ![Egenskaper för koppling](./media/how-to-connect-sync-configure-filtering/connectorproperties.png)  
4. Klicka på **Konfigurera katalogpartitioner**.
5. Markera och avmarkera domäner efter behov i listan **Välj katalogpartitioner.** Kontrollera att endast de partitioner som du vill synkronisera är markerade.  
   ![Partitioner](./media/how-to-connect-sync-configure-filtering/connectorpartitions.png)  
   Om du har ändrat din lokala Active Directory-infrastruktur och lagt till eller tagit bort domäner från skogen klickar du på knappen **Uppdatera** för att få en uppdaterad lista. När du uppdaterar blir du tillfrågad om autentiseringsuppgifter. Ange eventuella autentiseringsuppgifter med läsåtkomst till Active Directory för Windows Server. Det behöver inte vara användaren som är förifylld i dialogrutan.  
   ![Uppdatera behövs](./media/how-to-connect-sync-configure-filtering/refreshneeded.png)  
6. När du är klar stänger du dialogrutan **Egenskaper** genom att klicka på **OK**. Om du har tagit bort domäner från skogen visas ett meddelande på ett meddelande som säger att en domän har tagits bort och att konfigurationen ska rensas.
7. Fortsätt att justera körningsprofilerna.

#### <a name="select-the-domains-to-be-synchronized-using-the-azure-ad-connect-wizard"></a>Välj de domäner som ska synkroniseras med azure AD Connect-guiden
Så här anger du domänfiltret:

1.  Starta Azure AD Connect-guiden
2.  Klicka på **Konfigurera**.
3.  Välj **Anpassa synkroniseringsalternativ** och klicka på **Nästa**.
4.  Ange dina autentiseringsuppgifter för Azure AD
5.  Klicka på **Nästa**på skärmen **Anslutna kataloger** .
6.  Klicka på **Uppdatera**på **sidan Domän- och organisationsenhetsfiltrering.**  Nya domäner sjuka visas nu och borttagna domäner försvinner.
   ![Partitioner](./media/how-to-connect-sync-configure-filtering/update2.png)  

### <a name="update-the-run-profiles"></a>Uppdatera körningsprofilerna
Om du har uppdaterat domänfiltret måste du också uppdatera körningsprofilerna.

1. Kontrollera att kopplingen som du ändrade i föregående steg är markerad i listan **Kopplingar.** Välj Konfigurera **körprofiler i** **Åtgärder**.  
   ![Profilerna för anslutningskörning 1](./media/how-to-connect-sync-configure-filtering/connectorrunprofiles1.png)  
2. Hitta och identifiera följande profiler:
    * Fullständig import
    * Fullständig synkronisering
    * Deltaimport
    * Deltasynkronisering
    * Exportera
3. För varje profil justerar du de **tillagda** och **borttagna** domänerna.
    1. Gör följande för varje **tillagd** domän för var och en av de fem profilerna:
        1. Markera körprofilen och klicka på **Nytt steg**.
        2. På sidan **Konfigurera steg** på listrutan **Typ** väljer du stegtyp med samma namn som den profil som du konfigurerar. Klicka sedan på **Nästa**.  
        ![Profiler för anslutningskörning 2](./media/how-to-connect-sync-configure-filtering/runprofilesnewstep1.png)  
        3. På sidan **Anslutningskonfiguration** på listrutan **Partition** väljer du namnet på den domän som du har lagt till i domänfiltret.  
        ![Anslutningskörningsprofiler 3](./media/how-to-connect-sync-configure-filtering/runprofilesnewstep2.png)  
        4. Om du vill stänga dialogrutan **Konfigurera körprofil** klickar du på **Slutför**.
    2. Gör följande för varje **borttagen** domän för var och en av de fem profilerna:
        1. Välj körningsprofilen.
        2. Om **attributet Value** för **attributet Partition** är ett GUID markerar du körningssteget och klickar på Ta bort **steg**.  
        ![Profiler för anslutningskörning 4](./media/how-to-connect-sync-configure-filtering/runprofilesdeletestep.png)  
    3. Kontrollera din ändring. Varje domän som du vill synkronisera bör visas som ett steg i varje körningsprofil.
4. Om du vill stänga dialogrutan **Konfigurera körprofiler** klickar du på **OK**.
5.  För att slutföra konfigurationen måste du köra en **fullständig import** och en **Delta-synkronisering**. Fortsätt läsa avsnittet [Använd och verifiera ändringar](#apply-and-verify-changes).

## <a name="organizational-unitbased-filtering"></a>Organisationsenhetsbaserad filtrering
Det bästa sättet att ändra organisations- och organisationsenhetens standardfiltrering är att köra installationsguiden och ändra [domän- och organisationsenhetsfiltrering](how-to-connect-install-custom.md#domain-and-ou-filtering). Installationsguiden automatiserar alla uppgifter som dokumenteras i det här avsnittet.

Du bör bara följa dessa steg om du inte kan köra installationsguiden av någon anledning.

Så här konfigurerar du organisationsenhetsbaserad filtrering:

1. Logga in på servern som kör Azure AD Connect-synkronisering med hjälp av ett konto som är medlem i säkerhetsgruppen **ADSyncAdmins.**
2. Starta **synkroniseringstjänsten** på **Start-menyn.**
3. Välj **Anslutningsappar**och välj kopplingen med typen **Active Directory Domain Services**i listan **Kopplingar** . Välj **Egenskaper**i **Åtgärder**.  
   ![Egenskaper för koppling](./media/how-to-connect-sync-configure-filtering/connectorproperties.png)  
4. Klicka på **Konfigurera katalogpartitioner**, markera den domän som du vill konfigurera och klicka sedan på **Behållare**.
5. När du uppmanas att ange alla autentiseringsuppgifter med läsbehörighet till din lokala Active Directory. Det behöver inte vara användaren som är förifylld i dialogrutan.
6. Avmarkera de arbetsbehållare som du inte vill synkronisera med molnkatalogen i dialogrutan **Välj behållare** och klicka sedan på **OK**.  
   ![OUs i dialogrutan Välj behållare](./media/how-to-connect-sync-configure-filtering/ou.png)  
   * Behållaren **Datorer** bör väljas för att dina Windows 10-datorer ska synkroniseras med Azure AD. Om dina domänanslutna datorer finns i andra ru:er kontrollerar du att de är markerade.
   * Du ska markera containern **ForeignSecurityPrincipals** om du har flera skogar med förtroenden. Med den här containern kan säkerhetsgruppmedlemskap i flera skogar lösas.
   * Organisationsenheten **För registeredevices** ska väljas om du har aktiverat enhetens tillbakaskrivningsfunktion. Om du använder en annan tillbakaskrivningsfunktion, till exempel gruppåterskrivning, kontrollerar du att dessa platser är markerade.
   * Välj alla andra organisationsorganisationer där användare, iNetOrgPersons, Grupper, Kontakter och Datorer finns. I bilden finns alla dessa organisationsenheter i organisationsenheten ManagedObjects.
   * Om du använder gruppbaserad filtrering måste organisationsenheten där gruppen finns inkluderas.
   * Observera att du kan konfigurera om nya ru:er som läggs till efter att filtreringskonfigurationen har slutförts synkroniseras eller inte synkroniseras. Mer information finns i nästa avsnitt.
7. När du är klar stänger du dialogrutan **Egenskaper** genom att klicka på **OK**.
8. För att slutföra konfigurationen måste du köra en **fullständig import** och en **Delta-synkronisering**. Fortsätt läsa avsnittet [Använd och verifiera ändringar](#apply-and-verify-changes).

### <a name="synchronize-new-ous"></a>Synkronisera nya us
Nya ru:er som skapas efter att filtrering har konfigurerats synkroniseras som standard. Det här tillståndet visas med en markerad kryssruta. Du kan också avmarkera vissa underenheter. Om du vill få det här beteendet klickar du på rutan tills den blir vit med en blå bock (dess standardtillstånd). Avmarkera sedan alla underlicenser som du inte vill synkronisera.

Om alla underenheter är synkroniserade är rutan vit med en blå bockmarkering.  
![Organisationsenhet med alla rutor markerade](./media/how-to-connect-sync-configure-filtering/ousyncnewall.png)

Om vissa underenheter har avmarkerats är rutan grå med en vit bockmarkering.  
![Organisationsenheten med vissa sub-OUs omarkerade](./media/how-to-connect-sync-configure-filtering/ousyncnew.png)

Med den här konfigurationen synkroniseras en ny organisationsenhet som har skapats under ManagedObjects.

Installationsguiden för Azure AD Connect skapar alltid den här konfigurationen.

### <a name="dont-synchronize-new-ous"></a>Synkronisera inte nya us
Du kan konfigurera synkroniseringsmotorn så att den inte synkroniserar nya ru:er när filtreringskonfigurationen är klar. Det här tillståndet anges i användargränssnittet av rutan som visas helgrått grått utan att markera markeringen. Om du vill få det här beteendet klickar du på rutan tills den blir vit utan att markera det. Välj sedan de underenheter som du vill synkronisera.

![Organisationsenhet med roten omarkerat](./media/how-to-connect-sync-configure-filtering/oudonotsyncnew.png)

Med den här konfigurationen synkroniseras inte en ny organisationsenhet som har skapats under ManagedObjects.

## <a name="attribute-based-filtering"></a>Attributbaserad filtrering
Se till att du använder november 2015 ([1.0.9125](reference-connect-version-history.md#1091250)) eller senare bygga för att dessa steg ska fungera.

> [!IMPORTANT]
>Microsoft rekommenderar att du inte ändrar standardreglerna som skapats av **Azure AD Connect**. Om du vill ändra regeln klonar du den och inaktiverar den ursprungliga regeln. Gör eventuella ändringar i den klonade regeln. Observera att genom att göra så (inaktivera ursprungliga regeln) kommer du att missa eventuella buggfixar eller funktioner som är aktiverade genom den regeln.

Attributbaserad filtrering är det mest flexibla sättet att filtrera objekt. Du kan använda kraften i [deklarativ etablering](concept-azure-ad-connect-sync-declarative-provisioning.md) för att styra nästan alla aspekter av när ett objekt synkroniseras med Azure AD.

Du kan använda [inkommande](#inbound-filtering) filtrering från Active Directory till metaversum och [utgående](#outbound-filtering) filtrering från metaversum till Azure AD. Vi rekommenderar att du använder inkommande filtrering eftersom det är det enklaste att underhålla. Du bör bara använda utgående filtrering om det krävs för att koppla objekt från mer än en skog innan utvärderingen kan äga rum.

### <a name="inbound-filtering"></a>Inkommande filtrering
Inkommande filtrering använder standardkonfigurationen, där objekt som går till Azure AD måste ha metaversumattributet cloudFiltered inte inställt på ett värde som ska synkroniseras. Om attributets värde är inställt på **Sant**synkroniseras inte objektet. Det bör inte ställas in på **False**, avsiktligt. Om du vill vara säker på att andra regler har möjlighet att bidra med ett värde ska det här attributet bara ha värdena **Sant** eller **NULL** (frånvarande).

I inkommande filtrering använder du **omfattningens** kraft för att avgöra vilka objekt som ska synkroniseras eller inte synkroniseras. Här gör du justeringar för att passa din egen organisations krav. Scopemodulen har en **grupp** och en **sats** för att avgöra när en synkroniseringsregel är i omfånget. En grupp innehåller en eller flera satser. Det finns ett logiskt "OCH" mellan flera satser och ett logiskt "ELLER" mellan flera grupper.

Låt oss titta på ett exempel:  
![Omfång](./media/how-to-connect-sync-configure-filtering/scope.png)  
Detta bör läsas som **(avdelning = IT) ELLER (avdelning = Försäljning OCH c = US)**.

I följande exempel och steg använder du användarobjektet som ett exempel, men du kan använda det för alla objekttyper.

I följande exempel börjar prioritetsvärdet med 50. Detta kan vara valfritt antal som inte används, men bör vara lägre än 100.

#### <a name="negative-filtering-do-not-sync-these"></a>Negativ filtrering: "Synkronisera inte dessa"
I följande exempel filtrerar du bort (inte synkroniserar) alla användare där **tilläggetAttribute15** har värdet **NoSync**.

1. Logga in på servern som kör Azure AD Connect-synkronisering med hjälp av ett konto som är medlem i säkerhetsgruppen **ADSyncAdmins.**
2. Starta Redigeraren för **synkroniseringsregler** på **Start-menyn.**
3. Kontrollera att **Inkommande** är markerat och klicka på **Lägg till ny regel**.
4. Ge regeln ett beskrivande namn, till exempel "*In från AD – User DoNotSyncFilter*". Markera rätt skog, välj **Användare** som **CS-objekttyp**och välj **Person** som **MV-objekttyp**. Välj **Anslut**i **länktyp**. Skriv **Precedence**ett värde som för närvarande inte används av en annan synkroniseringsregel (till exempel 50) och klicka sedan på **Nästa**.  
   ![Inkommande 1 beskrivning](./media/how-to-connect-sync-configure-filtering/inbound1.png)  
5. Klicka på Lägg **till grupp**i **filtret Omfångsfilter**och klicka på **Lägg till sats**. Välj **ExtensionAttribute15**i **Attribut**. Kontrollera att **Operator** är inställt **på EQUAL**och skriv värdet **NoSync** i rutan **Värde.** Klicka på **Nästa**.  
   ![Inkommande 2-scope](./media/how-to-connect-sync-configure-filtering/inbound2.png)  
6. Lämna **kopplingsreglerna** tomma och klicka sedan på **Nästa**.
7. Klicka på **Lägg till omvandling,** välj **FlowType** som **Konstant**och välj **cloudFiltered** som **målattribut**. Skriv **True**i textrutan **Källa** . Klicka på **Lägg till** om du vill spara regeln.  
   ![Inkommande 3 omvandling](./media/how-to-connect-sync-configure-filtering/inbound3.png)
8. För att slutföra konfigurationen måste du köra en **fullständig synkronisering**. Fortsätt läsa avsnittet [Använd och verifiera ändringar](#apply-and-verify-changes).

#### <a name="positive-filtering-only-sync-these"></a>Positiv filtrering: "synkronisera bara dessa"
Att uttrycka positiv filtrering kan vara mer utmanande eftersom du också måste överväga objekt som inte är uppenbara för att synkroniseras, till exempel konferensrum. Du kommer också att åsidosätta standardfiltret i out-of-box-regeln **i från AD - Användarkoppling**. När du skapar ditt anpassade filter ska du se till att du inte inkluderar kritiska systemobjekt, replikeringskonfliktobjekt, särskilda postlådor och tjänstkonton för Azure AD Connect.

Alternativet positiv filtrering kräver två synkroniseringsregler. Du behöver en regel (eller flera) med rätt omfattning av objekt för att synkronisera. Du behöver också en andra catch-all-synkroniseringsregel som filtrerar bort alla objekt som ännu inte har identifierats som ett objekt som ska synkroniseras.

I följande exempel synkroniserar du bara användarobjekt där avdelningsattributet har värdet **Försäljning**.

1. Logga in på servern som kör Azure AD Connect-synkronisering med hjälp av ett konto som är medlem i säkerhetsgruppen **ADSyncAdmins.**
2. Starta Redigeraren för **synkroniseringsregler** på **Start-menyn.**
3. Kontrollera att **Inkommande** är markerat och klicka på **Lägg till ny regel**.
4. Ge regeln ett beskrivande namn, till exempel "*In från AD – Synkronisering av användarförsäljning*". Markera rätt skog, välj **Användare** som **CS-objekttyp**och välj **Person** som **MV-objekttyp**. Välj **Anslut**i **länktyp**. Skriv **Precedence**ett värde som för närvarande inte används av en annan synkroniseringsregel (till exempel 51) och klicka sedan på **Nästa**.  
   ![Inkommande 4 beskrivning](./media/how-to-connect-sync-configure-filtering/inbound4.png)  
5. Klicka på Lägg **till grupp**i **filtret Omfångsfilter**och klicka på **Lägg till sats**. Välj **avdelning**i **Attribut**. Kontrollera att Operator är inställt **på EQUAL**och skriv värdet **Försäljning** i rutan **Värde.** Klicka på **Nästa**.  
   ![Inkommande 5-scope](./media/how-to-connect-sync-configure-filtering/inbound5.png)  
6. Lämna **kopplingsreglerna** tomma och klicka sedan på **Nästa**.
7. Klicka på **Lägg till omvandling**, välj **Konstant** som **FlowType**och välj **molnet Filtrerat** som **målattribut**. Skriv **Falskt**i rutan **Källa** . Klicka på **Lägg till** om du vill spara regeln.  
   ![Inkommande 6 omvandling](./media/how-to-connect-sync-configure-filtering/inbound6.png)  
   Detta är ett specialfall där du uttryckligen anger cloudFiltered till **False**.
8. Vi måste nu skapa synkroniseringsregeln för catch-all. Ge regeln ett beskrivande namn, till exempel "*In från AD – Filter för användar catch-all*". Markera rätt skog, välj **Användare** som **CS-objekttyp**och välj **Person** som **MV-objekttyp**. Välj **Anslut**i **länktyp**. I **Prioritet**skriver du ett värde som för närvarande inte används av en annan synkroniseringsregel (till exempel 99). Du har valt ett prioritetsvärde som är högre (lägre prioritet) än den tidigare synkroniseringsregeln. Men du har också lämnat ett visst utrymme så att du kan lägga till fler synkroniseringsregler för filtrering senare när du vill börja synkronisera ytterligare avdelningar. Klicka på **Nästa**.  
   ![Inkommande 7 beskrivning](./media/how-to-connect-sync-configure-filtering/inbound7.png)  
9. Lämna **filtret Omfångsfilter** tomt och klicka på **Nästa**. Ett tomt filter anger att regeln ska tillämpas på alla objekt.
10. Lämna **kopplingsreglerna** tomma och klicka sedan på **Nästa**.
11. Klicka på **Lägg till omvandling**, välj **Konstant** som **FlowType**och välj **cloudFiltered** som **målattribut**. Skriv **True**i rutan **Källa** . Klicka på **Lägg till** om du vill spara regeln.  
    ![Inkommande 3 omvandling](./media/how-to-connect-sync-configure-filtering/inbound3.png)  
12. För att slutföra konfigurationen måste du köra en **fullständig synkronisering**. Fortsätt läsa avsnittet [Använd och verifiera ändringar](#apply-and-verify-changes).

Om du behöver kan du skapa fler regler av den första typen där du inkluderar fler objekt i synkroniseringen.

### <a name="outbound-filtering"></a>Utgående filtrering
I vissa fall är det nödvändigt att göra filtrering först efter att objekten har gått samman i metaversumet. Det kan till exempel vara nödvändigt att titta på e-postattributet från resursskogen och attributet userPrincipalName från kontoskogen för att avgöra om ett objekt ska synkroniseras. I dessa fall skapar du filtrningen på den utgående regeln.

I det här exemplet ändrar du filtrningen så att endast användare @contoso.com som har både sin e-post och användarenPrincipalName slutar i synkroniseras:

1. Logga in på servern som kör Azure AD Connect-synkronisering med hjälp av ett konto som är medlem i säkerhetsgruppen **ADSyncAdmins.**
2. Starta Redigeraren för **synkroniseringsregler** på **Start-menyn.**
3. Klicka på **Utgående**under **Regeltyp.**
4. Beroende på vilken version av Connect du använder, antingen hitta regeln **Ut till AAD - Användarkoppling** eller **ut till AAD - User Join SOAInAD**, och klicka på **Redigera**.
5. Svara **Ja** om du vill skapa en kopia av regeln i popup-programmet.
6. På sidan **Beskrivning** ändrar **du Prioritet** till ett oanvänt värde, till exempel 50.
7. Klicka på **Omfångsfilter** i vänsternavigering och klicka sedan på **Lägg till sats**. Välj **e-post**i **Attribut**. Välj SLUTAR **MED**I **Operator**. Skriv ** \@contoso.com i** **Värde**och klicka sedan på **Lägg till sats**. Välj **userPrincipalName**i **Attribut**. Välj SLUTAR **MED**I **Operator**. Skriv ** \@contoso.com i** **Värde**.
8. Klicka på **Spara**.
9. För att slutföra konfigurationen måste du köra en **fullständig synkronisering**. Fortsätt läsa avsnittet [Använd och verifiera ändringar](#apply-and-verify-changes).

## <a name="apply-and-verify-changes"></a>Använda och verifiera ändringar
När du har gjort konfigurationsändringarna måste du använda dem på de objekt som redan finns i systemet. Det kan också vara så att de objekt som för närvarande inte finns i synkroniseringsmotorn ska bearbetas (och synkroniseringsmotorn måste läsa källsystemet igen för att verifiera dess innehåll).

Om du har ändrat konfigurationen med hjälp av filtrering av **domäner** eller enheter måste du göra en **fullständig import**, följt av **Delta-synkronisering** . **Delta synchronization**

Om du har ändrat konfigurationen med hjälp av **attributfiltrering** måste du göra en fullständig **synkronisering**.

Gör följande:

1. Starta **synkroniseringstjänsten** på **Start-menyn.**
2. Välj **Kopplingar**. I listan **Kopplingar** väljer du den koppling där du gjorde en konfigurationsändring tidigare. Välj **Kör**i **Åtgärder**.  
   ![Anslutningskörning](./media/how-to-connect-sync-configure-filtering/connectorrun.png)  
3. Välj den åtgärd som nämndes i föregående avsnitt i **Kör profiler.** Om du behöver köra två åtgärder kör du den andra efter att den första har avslutats. (Kolumnen **Tillstånd** är **inaktiv** för den valda kopplingen.)

Efter synkroniseringen mellanlagras alla ändringar som ska exporteras. Innan du faktiskt gör ändringarna i Azure AD vill du kontrollera att alla dessa ändringar är korrekta.

1. Starta en kommandotolk `%ProgramFiles%\Microsoft Azure AD Sync\bin`och gå till .
2. Kör `csexport "Name of Connector" %temp%\export.xml /f:x`.  
   Namnet på kopplingen finns i synkroniseringstjänsten. Den har ett namn som liknar "contoso.com – AAD" för Azure AD.
3. Kör `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv`.
4. Du har nu en fil i %temp% med namnet export.csv som kan undersökas i Microsoft Excel. Den här filen innehåller alla ändringar som är på väg att exporteras.
5. Gör nödvändiga ändringar i data eller konfiguration och kör dessa steg igen (Importera, Synkronisera och Verifiera) tills de ändringar som är på väg att exporteras är vad du förväntar dig.

När du är nöjd exporterar du ändringarna till Azure AD.

1. Välj **Kopplingar**. Välj Azure AD Connector i listan **Kopplingar.** Välj **Kör**i **Åtgärder**.
2. Välj **Exportera**i **Kör profiler**.
3. Om konfigurationsändringarna tar bort många objekt visas ett fel i exporten när numret är mer än det konfigurerade tröskelvärdet (som standard 500). Om det här felet visas måste du tillfälligt inaktivera funktionen "[förhindra oavsiktliga borttagningar".](how-to-connect-sync-feature-prevent-accidental-deletes.md)

Nu är det dags att aktivera schemaläggaren igen.

1. Starta **Schemaläggaren** från **Start-menyn.**
2. Direkt under **Schemaläggarens bibliotek**hittar du uppgiften **Azure AD Sync Scheduler**, högerklickar och väljer **Aktivera**.

## <a name="group-based-filtering"></a>Gruppbaserad filtrering
Du kan konfigurera gruppbaserad filtrering första gången du installerar Azure AD Connect med anpassad [installation](how-to-connect-install-custom.md#sync-filtering-based-on-groups). Den är avsedd för en pilotdistribution där du bara vill att en liten uppsättning objekt ska synkroniseras. När du inaktiverar gruppbaserad filtrering kan den inte aktiveras igen. Det *stöds inte* att använda gruppbaserad filtrering i en anpassad konfiguration. Det stöds bara för att konfigurera den här funktionen med hjälp av installationsguiden. När du har slutfört piloten använder du något av de andra filtreringsalternativen i det här avsnittet. När du använder organisations- och organisationsenhetens standardbaserad filtrering tillsammans med gruppbaserad filtrering måste organisationsenheten där gruppen och dess medlemmar finns inkluderas.

När du synkroniserar flera AD-skogar kan du konfigurera gruppbaserad filtrering genom att ange en annan grupp för varje AD-koppling. Om du vill synkronisera en användare i en AD-skog och samma användare har ett eller flera motsvarande objekt i andra AD-skogar, måste du se till att användarobjektet och alla motsvarande objekt finns inom gruppbaserad filtreringsomfattning. För exempel:

* Du har en användare i en skog som har ett motsvarande FSP-objekt (Foreign Security Principal) i en annan skog. Båda objekten måste finnas inom gruppbaserad filtreringsomfång. Annars synkroniseras användaren inte till Azure AD.

* Du har en användare i en skog som har ett motsvarande resurskonto (t.ex. länkad postlåda) i en annan skog. Vidare har du konfigurerat Azure AD Connect för att länka användaren till resurskontot. Båda objekten måste finnas inom gruppbaserad filtreringsomfång. Annars synkroniseras användaren inte till Azure AD.

* Du har en användare i en skog som har en motsvarande e-postkontakt i en annan skog. Vidare har du konfigurerat Azure AD Connect för att länka användaren till e-postkontakten. Båda objekten måste finnas inom gruppbaserad filtreringsomfång. Annars synkroniseras användaren inte till Azure AD.


## <a name="next-steps"></a>Nästa steg
- Läs mer om [azure AD Connect-synkroniseringskonfiguration.](how-to-connect-sync-whatis.md)
- Läs mer om [hur du integrerar dina lokala identiteter med Azure AD](whatis-hybrid-identity.md).
