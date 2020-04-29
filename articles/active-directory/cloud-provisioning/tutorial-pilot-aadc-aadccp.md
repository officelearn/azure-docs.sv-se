---
title: Självstudie – pilot Azure AD Connect Cloud etablering för en befintlig synkroniserad AD-skog
description: gång.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 03/04/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: aba42e6bd9b11e47d793219c0ff06b9177d609f5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "78298827"
---
# <a name="pilot-cloud-provisioning-for-an-existing-synced-ad-forest"></a>Styr molnetablering för en befintlig synkroniserad AD-skog 

Den här självstudien vägleder dig genom pilot etablering av molnet för en test Active Directory skog som redan har synkroniserats med Azure Active Directory (Azure AD) Connect-synkronisering.

![Skapa](media/tutorial-migrate-aadc-aadccp/diagram.png)

## <a name="considerations"></a>Överväganden
Tänk på följande innan du provar den här självstudien:
1. Se till att du är bekant med grunderna i moln etableringen. 
2. Se till att du kör Azure AD Connect Sync version 1.4.32.0 eller senare och har konfigurerat reglerna för synkronisering enligt dokumentationen. Vid pilotering kommer du att ta bort en test-OU eller-grupp från Azure AD Connect Sync-omfånget. Att flytta objekt utanför omfattning leder till att dessa objekt tas bort i Azure AD. Om det rör sig om användar objekt är objekten i Azure AD mjuk-borttagna och kan återställas. I händelse av grupp objekt tas objekten i Azure AD bort och kan inte återställas. En ny länktyp har introducerats i Azure AD Connect Sync som förhindrar borttagningen om ett pilot scenario används. 
3. Se till att objekten i pilot omfattningen har ms-DS-consistencyGUID ifyllda så att moln etablerings hårdheten matchar objekten. 

   > [!NOTE]
   > Azure AD Connect Sync fyller inte i *MS-DS-consistencyGUID* som standard för grupp objekt.

4. Det här är ett avancerat scenario. Se till att du följer de steg som beskrivs i den här självstudien noggrant.

## <a name="prerequisites"></a>Krav
Följande är förutsättningar som krävs för den här självstudien
- En test miljö med Azure AD Connect Sync version 1.4.32.0 eller senare
- En ORGANISATIONSENHET eller grupp som finns i omfånget och kan användas som pilot. Vi rekommenderar att du börjar med en liten uppsättning objekt.
- En server som kör Windows Server 2012 R2 eller senare och som kommer att vara värd för etablerings agenten.  Detta kan inte vara samma server som Azure AD Connect servern.
- Käll ankare för AAD Connect-synkronisering ska vara antingen *objectGUID* eller *MS-DS-consistencyGUID*

## <a name="update-azure-ad-connect"></a>Uppdatera Azure AD Connect

Som minst bör du ha [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) -1.4.32.0. Slutför stegen i Azure AD Connect för att uppdatera Azure AD Connect synkronisering [: uppgradera till den senaste versionen](../hybrid/how-to-upgrade-previous-version.md).  

## <a name="stop-the-scheduler"></a>Stoppa Scheduler
Azure AD Connect synkronisera synkroniserar ändringar som sker i din lokala katalog med hjälp av en Scheduler. För att kunna ändra och lägga till anpassade regler vill du inaktivera Scheduler så att synkroniseringarna inte körs medan du arbetar med detta.  Använd följande steg:

1.  På den server som kör Azure AD Connect synkroniserar du öppna PowerShell med administratörs behörighet.
2.  Kör `Stop-ADSyncSyncCycle`.  Tryck på RETUR.
3.  Kör `Set-ADSyncScheduler -SyncCycleEnabled $false`.

>[!NOTE] 
>Om du kör en egen anpassad schemaläggare för AAD Connect-synkronisering inaktiverar du Scheduler. 

## <a name="create-custom-user-inbound-rule"></a>Skapa anpassad regel för inkommande användare

 1. Starta redigeraren för synkronisering från program-menyn i Skriv bordet enligt nedan:</br>
 ![Redigerings meny för Synkroniseringsregel](media/how-to-cloud-custom-user-rule/user8.png)</br>
 
 2. Välj **inkommande** i list rutan för riktning och klicka på **Lägg till ny regel**.
 ![Anpassad regel](media/how-to-cloud-custom-user-rule/user1.png)</br>
 
 3. På sidan **Beskrivning** anger du följande och klickar på **Nästa**:

    **Namn:** Ge regeln ett beskrivande namn<br>
    **Beskrivning:** Lägg till en meningsfull beskrivning<br>
    **Anslutet system:** Välj den AD-anslutning som du vill skriva den anpassade synkroniseringsregeln för<br>
    **Ansluten system objekt typ:** Användarvänlig<br>
    **Metaversum-objekt typ:** Sända<br>
    **Länktyp:** Ansluta<br>
    **Prioritet:** Ange ett värde som är unikt i systemet<br>
    **Tagg:** Lämna detta tomt<br>
    ![Anpassad regel](media/how-to-cloud-custom-user-rule/user2.png)</br>
 
 4. På sidan **omfångs filter** anger du den organisationsenhet eller säkerhets grupp som du vill att piloten ska baseras på.  Om du vill filtrera på OU lägger du till OU-delen av det unika namnet. Den här regeln gäller för alla användare som finns i ORGANISATIONSENHETen.  Så om DN slutar med "OU = processors, DC = contoso, DC = com, lägger du till det här filtret.  Klicka sedan på **Nästa**. 

    |Regel|Attribut|Operator|Värde|
    |-----|----|----|-----|
    |Scope OU|NAMNET|ENDSWITH|Unikt namn på ORGANISATIONSENHETen.|
    |Omfångs grupp||ISMEMBEROF|Unikt namn på säkerhets gruppen.|

    ![Anpassad regel](media/how-to-cloud-custom-user-rule/user3.png)</br>
 
 5. Klicka på **Nästa**på sidan **Anslut** regler.
 6. På sidan **omvandlingar** lägger du till en konstant omvandling: Flow true to cloudNoFlow-attribut. Klicka på **Lägg till**.
 ![Anpassad regel](media/how-to-cloud-custom-user-rule/user4.png)</br>

Samma steg måste följas för alla typer av objekt (användare, grupp och kontakt). Upprepa steg per konfigurerad AD-anslutning/per AD-skog. 

## <a name="create-custom-user-outbound-rule"></a>Skapa anpassad regel för utgående användare

 1. Välj **utgående** i list rutan för riktning och klicka på **Lägg till regel**.
 ![Anpassad regel](media/how-to-cloud-custom-user-rule/user5.png)</br>
 
 2. På sidan **Beskrivning** anger du följande och klickar på **Nästa**:

    **Namn:** Ge regeln ett beskrivande namn<br>
    **Beskrivning:** Lägg till en meningsfull beskrivning<br>
    **Anslutet system:** Välj den AAD-koppling som du skriver den anpassade synkroniseringsregeln för<br>
    **Ansluten system objekt typ:** Användarvänlig<br>
    **Metaversum-objekt typ:** Sända<br>
    **Länktyp:** JoinNoFlow<br>
    **Prioritet:** Ange ett värde som är unikt i systemet<br>
    **Tagg:** Lämna detta tomt<br>
    
    ![Anpassad regel](media/how-to-cloud-custom-user-rule/user6.png)</br>
 
 3. På sidan **omfångs filter** väljer du **CloudNoFlow** lika med **Sant**. Klicka sedan på **Nästa**.
 ![Anpassad regel](media/how-to-cloud-custom-user-rule/user7.png)</br>
 
 4. Klicka på **Nästa**på sidan **Anslut** regler.
 5. På sidan **omvandlingar** klickar du på **Lägg till**.

Samma steg måste följas för alla typer av objekt (användare, grupp och kontakt).

## <a name="install-the-azure-ad-connect-provisioning-agent"></a>Installera Azure AD Connect etablerings agenten
1. Logga in på den server som du vill använda med företags administratörs behörighet.  Om du använder [Basic AD-och Azure-miljön](tutorial-basic-ad-azure.md) blir det CP1.
2. Hämta Azure AD Connect Cloud Provisioning agent med hjälp av stegen som beskrivs [här](how-to-install.md#install-the-agent).
3. Kör Azure AD Connect Cloud etableringen (AADConnectProvisioningAgent. Installer)
3. På Välkomst skärmen **godkänner** du licens villkoren och klickar på **Installera**.</br>
![Välkomstskärmen](media/how-to-install/install1.png)</br>

4. När den här åtgärden har slutförts startas konfigurations guiden.  Logga in med ditt globala administratörs konto för Azure AD.
5. På skärmen **anslut Active Directory** klickar du på **Lägg till katalog** och loggar sedan in med ditt Active Directory administratörs konto.  Den här åtgärden lägger till din lokala katalog.  Klicka på **Nästa**.</br>
![Välkomstskärmen](media/how-to-install/install3.png)</br>

6. Klicka på **Bekräfta**på sidan **konfiguration slutförd** .  Den här åtgärden registrerar och startar om agenten.</br>
![Välkomstskärmen](media/how-to-install/install4.png)</br>

7. När den här åtgärden har slutförts bör du se ett meddelande om **att det har verifierats.**  Du kan klicka på **Avsluta**.</br>
![Välkomstskärmen](media/how-to-install/install5.png)</br>
8. Om du fortfarande ser den inledande välkomst skärmen klickar du på **Stäng**.

## <a name="verify-agent-installation"></a>Verifiera agent installation
Agent verifiering sker i Azure Portal och på den lokala server som kör-agenten.

### <a name="azure-portal-agent-verification"></a>Azure Portal agent verifiering
Följ dessa steg för att kontrol lera att agenten visas i Azure:

1. Logga in på Azure-portalen.
2. Till vänster väljer du **Azure Active Directory**, klickar på **Azure AD Connect** och i mitten väljer du **Hantera etablering (för hands version)**.</br>
![Azure-portalen](media/how-to-install/install6.png)</br>

3.  På skärmen **Azure AD Provisioning (för hands version)** klickar du på **Granska alla agenter**.
![Azure AD-etablering](media/how-to-install/install7.png)</br>
 
4. På **skärmen lokala etablerings agenter** visas de agenter som du har installerat.  Kontrol lera att agenten i fråga finns där och har marker ATS som **inaktive rad**.  Agenten är inaktive ![rad som standard etablerings agenter](media/how-to-install/verify1.png)</br>

### <a name="on-the-local-server"></a>På den lokala servern
Verifiera att agenten körs genom att följa dessa steg:

1.  Logga in på servern med ett administratörs konto
2.  Öppna **tjänster** genom att antingen navigera till den eller genom att gå till starta/köra/Services. msc.
3.  Under **tjänster** ser du till **Microsoft Azure AD ansluta Agent updateer** och **Microsoft Azure AD ansluta etablerings agenten** finns där, och statusen **körs**.
![Tjänster](media/how-to-troubleshoot/troubleshoot1.png)

## <a name="configure-azure-ad-connect-cloud-provisioning"></a>Konfigurera Azure AD Connect Cloud-etablering
Använd följande steg för att konfigurera etableringen:

 1. Logga in på Azure AD-portalen.
 2. Klicka på **Azure Active Directory**
 3. Klicka på **Azure AD Connect**
 4. Välj **Hantera etablering (förhands granskning)**
 ![](media/how-to-configure/manage1.png)</br>
 5.  Klicka på **ny konfiguration**
 ![](media/tutorial-single-forest/configure1.png)</br>
 6.  På sidan konfiguration anger du ett **e-postmeddelande för aviseringar**, flyttar väljaren för att **Aktivera** och klickar på **Spara**.
 ![](media/tutorial-single-forest/configure2.png)</br>
 7. Under **Konfigurera**väljer du **alla användare** för att ändra omfånget för konfigurations regeln.
 ![](media/how-to-configure/scope2.png)</br>
 8. Till höger ändrar du omfattningen så att den innehåller den angivna ORGANISATIONSENHETen som du nyss skapade "OU = processorer, DC = contoso, DC = com".
 ![](media/tutorial-existing-forest/scope2.png)</br>
 9.  Klicka på **färdig** och **Spara**.
 10. Omfånget ska nu vara inställt på en organisationsenhet. 
 ![](media/tutorial-existing-forest/scope3.png)</br>
 

## <a name="verify-users-are-provisioned-by-cloud-provisioning"></a>Verifiera att användare tillhandahålls av moln etablering
Nu ska du kontrol lera att användarna som du hade i vår lokala katalog har synkroniserats och nu finns i Azure AD-klienten.  Observera att det kan ta några timmar att slutföra.  Följ dessa steg om du vill verifiera att användarna har etablering genom moln etablering:

1. Bläddra till [Azure-portalen](https://portal.azure.com) och logga in med ett konto som har en Azure-prenumeration.
2. Välj **Azure Active Directory** till vänster
3. Klicka på **Azure AD Connect**
4. Klicka på **Hantera etablering (förhands granskning)**
5. Klicka på **loggar** -knappen
6. Sök efter ett användar namn för att bekräfta att användaren har tillhandahållits av moln etablering

Dessutom kan du kontrol lera att användaren och gruppen finns i Azure AD.

## <a name="start-the-scheduler"></a>Starta Scheduler
Azure AD Connect synkronisera synkroniserar ändringar som sker i din lokala katalog med hjälp av en Scheduler. Nu när du har ändrat reglerna kan du starta om Scheduler.  Använd följande steg:

1.  Öppna PowerShell med administratörs behörighet på den server som kör Azure AD Connect Sync
2.  Kör `Set-ADSyncScheduler -SyncCycleEnabled $true`.
3.  Kör `Start-ADSyncSyncCycle`.  Tryck på RETUR.  

>[!NOTE] 
>Aktivera Scheduler om du kör en egen schemaläggare för AAD Connect-synkronisering. 

## <a name="something-went-wrong"></a>Något gick fel
Om piloten inte fungerar som förväntat kan du gå tillbaka till Azure AD Connect Sync-installationen genom att följa stegen nedan:
1.  Inaktivera etablerings konfiguration i Azure Portal. 
2.  Inaktivera alla regler för anpassad synkronisering som skapats för moln etablering med hjälp av verktyget Sync Rule Editor. Om du inaktiverar, bör det orsaka fullständig synkronisering för alla anslutningar.

## <a name="configure-azure-ad-connect-sync-to-exclude-the-pilot-ou"></a>Konfigurera Azure AD Connect Sync att undanta pilot-OU
När du har kontrollerat att användare från pilot-ORGANISATIONSENHETen hanteras av moln etableringen kan du konfigurera om Azure AD Connect för att utesluta pilot ORGANISATIONSENHETen som skapades ovan.  Moln Provisioning-agenten hanterar synkronisering för de här användarna kommer att vidarebefordra.  Använd följande steg för att omfånget Azure AD Connect.

 1. Dubbelklicka på Azure AD Connect-ikonen på den server som kör Azure AD Connect.
 2. Klicka på **Konfigurera**
 3. Välj **Anpassa alternativ för synkronisering** och klicka på Nästa.
 4. Logga in på Azure AD och klicka på **Nästa**.
 5. På sidan **Anslut dina kataloger** klickar du på **Nästa**.
 6. På skärmen **domän-och OU-filtrering** väljer du **Synkronisera valda domäner och organisationsenheter**.
 7. Expandera din domän och **avmarkera** **CPU: er** ou.  Klicka på **Nästa**.
![utrymme](media/tutorial-existing-forest/scope1.png)</br>
 9. Klicka på **Nästa**på skärmen **valfria funktioner** .
 10. Klicka på **Konfigurera**på skärmen **redo att konfigurera** .
 11. När den är klar klickar du på **Avsluta**. 

## <a name="next-steps"></a>Nästa steg 

- [Vad är etablering?](what-is-provisioning.md)
- [Vad är Azure AD Connect-molnetablering?](what-is-cloud-provisioning.md)

