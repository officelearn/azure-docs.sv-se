---
title: Självstudiekurs - Pilot Azure AD Connect-molnetablering för en befintlig synkroniserad AD-skog
description: Tutorial.
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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "78298827"
---
# <a name="pilot-cloud-provisioning-for-an-existing-synced-ad-forest"></a>Styr molnetablering för en befintlig synkroniserad AD-skog 

Den här självstudien går igenom pilotmolnetablering för en test Active Directory-skog som redan är synkroniserad med Azure Active Directory (Azure AD) Connect-synkronisering.

![Skapa](media/tutorial-migrate-aadc-aadccp/diagram.png)

## <a name="considerations"></a>Överväganden
Innan du provar den här självstudien bör du tänka på följande:
1. Se till att du är bekant med grunderna i molnetablering. 
2. Se till att du kör Azure AD Connect sync version 1.4.32.0 eller senare och har konfigurerat synkroniseringsreglerna som dokumenterade. När du lotsar kommer du att ta bort en testfet eller grupp från Azure AD Connect-synkroniseringsomfånget. Om du flyttar objekt utanför omfånget kan du ta bort dessa objekt i Azure AD. När det gäller användarobjekt tas objekten i Azure AD bort mjukt och kan återställas. När det gäller gruppobjekt tas objekten i Azure AD bort hårt och kan inte återställas. En ny länktyp har införts i Azure AD Connect-synkronisering som förhindrar borttagning i händelse av ett pilotscenario. 
3. Kontrollera att objekten i pilotomfattningen har ms-ds-consistencyGUID ifyllt så att molnetablering hård matchar objekten. 

   > [!NOTE]
   > Azure AD Connect-synkronisering fyller inte *ms-ds-consistencyGUID* som standard för gruppobjekt.

4. Detta är ett avancerat scenario. Se till att du följer stegen som dokumenteras i den här självstudien exakt.

## <a name="prerequisites"></a>Krav
Följande är förutsättningar som krävs för den här självstudien
- En testmiljö med Azure AD Connect-synkronisering version 1.4.32.0 eller senare
- En organisationsenhet eller grupp som är synkroniserad och som kan användas som pilot. Vi rekommenderar att du börjar med en liten uppsättning objekt.
- En server som kör Windows Server 2012 R2 eller senare och som är värd för etableringsagenten.  Detta kan inte vara samma server som Azure AD Connect-servern.
- Källankaret för AAD Connect-synkronisering bör vara antingen *objectGuid* eller *ms-ds-consistencyGUID*

## <a name="update-azure-ad-connect"></a>Uppdatera Azure AD Connect

Som ett minimum bör du ha [Azure AD connect](https://www.microsoft.com/download/details.aspx?id=47594) 1.4.32.0. Om du vill uppdatera Synkroniseringen av Azure AD Connect slutför du stegen i [Azure AD Connect: Uppgradera till den senaste versionen](../hybrid/how-to-upgrade-previous-version.md).  

## <a name="stop-the-scheduler"></a>Stoppa schemaläggaren
Azure AD Connect-synkronisering synkroniserar ändringar som sker i din lokala katalog med hjälp av en schemaläggare. Om du vill ändra och lägga till anpassade regler vill du inaktivera schemaläggaren så att synkroniseringarna inte körs medan du arbetar med detta.  Använd följande steg:

1.  Öppna PowerShell med administratörsbehörighet på servern som kör Azure AD Connect.
2.  Kör `Stop-ADSyncSyncCycle`.  Tryck enter.
3.  Kör `Set-ADSyncScheduler -SyncCycleEnabled $false`.

>[!NOTE] 
>Om du kör din egen anpassade schemaläggare för AAD Connect sync, vänligen inaktivera schemaläggaren. 

## <a name="create-custom-user-inbound-rule"></a>Skapa anpassad regel för inkommande användare

 1. Starta synkroniseringsredigeraren från programmenyn på skrivbordet enligt nedan:</br>
 ![Redigerare för synkroniseringsregel](media/how-to-cloud-custom-user-rule/user8.png)</br>
 
 2. Välj **Inkommande i** listrutan för Riktning och klicka på Lägg till ny **regel**.
 ![Anpassad regel](media/how-to-cloud-custom-user-rule/user1.png)</br>
 
 3. På sidan **Beskrivning** anger du följande och klickar på **Nästa:**

    **Namn:** Ge regeln ett meningsfullt namn<br>
    **Beskrivning:** Lägga till en meningsfull beskrivning<br>
    **Anslutet system:** Välj den AD-anslutningsappen som du skriver den anpassade synkroniseringsregeln för<br>
    **Objekttyp för anslutna system:** Användaren<br>
    **Objekttyp för metaversum:** Person<br>
    **Länktyp:** Ansluta sig till<br>
    **Prioritet:** Ange ett värde som är unikt i systemet<br>
    **Tagg:** Lämna detta tomt<br>
    ![Anpassad regel](media/how-to-cloud-custom-user-rule/user2.png)</br>
 
 4. På sidan **Omfångsfilter** anger du den organisationsenhet eller säkerhetsgrupp som du vill att piloten ska baseras på.  Om du vill filtrera på organisationsenhetsenheten lägger du till organisationsenhetens del av det unika namnet. Den här regeln tillämpas på alla användare som ingår i organisationsenheten.  Så om DN slutar med "OU=Processorers,DC=contoso,DC=com, lägger du till det här filtret.  Klicka sedan på **Nästa**. 

    |Regel|Attribut|Operator|Värde|
    |-----|----|----|-----|
    |Omfång för organisationsenhet|Dn|ENDSWITH|Unikt namn på organisationsenheten.|
    |Omfångsgrupp||ISMEDLEM|Unikt namn på säkerhetsgruppen.|

    ![Anpassad regel](media/how-to-cloud-custom-user-rule/user3.png)</br>
 
 5. Klicka på **Nästa**på sidan **Koppla** regler .
 6. Lägg till en konstant omvandling: flöde Sant för cloudNoFlow-attribut på sidan **Omvandlingar.** Klicka på **Lägg till**.
 ![Anpassad regel](media/how-to-cloud-custom-user-rule/user4.png)</br>

Samma steg måste följas för alla objekttyper (användare, grupp och kontakt). Upprepa steg per konfigurerad AD Connector / per AD-skog. 

## <a name="create-custom-user-outbound-rule"></a>Skapa anpassad regel för utgående användare

 1. Välj **Utgående** i listrutan för Riktning och klicka på **Lägg till regel**.
 ![Anpassad regel](media/how-to-cloud-custom-user-rule/user5.png)</br>
 
 2. På sidan **Beskrivning** anger du följande och klickar på **Nästa:**

    **Namn:** Ge regeln ett meningsfullt namn<br>
    **Beskrivning:** Lägga till en meningsfull beskrivning<br>
    **Anslutet system:** Välj den AAD-anslutning som du skriver den anpassade synkroniseringsregeln för<br>
    **Objekttyp för anslutna system:** Användaren<br>
    **Objekttyp för metaversum:** Person<br>
    **Länktyp:** JoinNoFlow<br>
    **Prioritet:** Ange ett värde som är unikt i systemet<br>
    **Tagg:** Lämna detta tomt<br>
    
    ![Anpassad regel](media/how-to-cloud-custom-user-rule/user6.png)</br>
 
 3. På sidan **Omfångsfilter** väljer du **cloudNoFlow** lika **sant**. Klicka sedan på **Nästa**.
 ![Anpassad regel](media/how-to-cloud-custom-user-rule/user7.png)</br>
 
 4. Klicka på **Nästa**på sidan **Koppla** regler .
 5. Klicka på **Lägg till**på sidan **Omvandlingar.**

Samma steg måste följas för alla objekttyper (användare, grupp och kontakt).

## <a name="install-the-azure-ad-connect-provisioning-agent"></a>Installera etableringsagenten för Azure AD Connect
1. Logga in på den server som du ska använda med behörigheten företagsadministratör.  Om du använder basic [AD- och Azure-miljöhandledningen](tutorial-basic-ad-azure.md) är det CP1.
2. Hämta Azure AD Connect-molnetableringsagenten med hjälp av stegen som beskrivs [här](how-to-install.md#install-the-agent).
3. Kör Azure AD Connect-molnetablering (AADConnectProvisioningAgent.Installer)
3. På välkomstskärmen **godkänner du** licensvillkoren och klickar på **Installera**.</br>
![Välkomstskärmen](media/how-to-install/install1.png)</br>

4. När den här åtgärden är klar startar konfigurationsguiden.  Logga in med ditt globala Azure AD-administratörskonto.
5. Klicka på Lägg till **katalog** på skärmen **Anslut Active Directory** och logga sedan in med active directory-administratörskontot.  Den här åtgärden lägger till din lokala katalog.  Klicka på **Nästa**.</br>
![Välkomstskärmen](media/how-to-install/install3.png)</br>

6. Klicka på **Bekräfta**på skärmen **Konfigurationen.**  Den här åtgärden registrerar och startar om agenten.</br>
![Välkomstskärmen](media/how-to-install/install4.png)</br>

7. När den här åtgärden är klar bör du se ett meddelande **Din har verifierats.**  Du kan klicka på **Avsluta**.</br>
![Välkomstskärmen](media/how-to-install/install5.png)</br>
8. Om du fortfarande ser den första välkomstskärmen klickar du på **Stäng**.

## <a name="verify-agent-installation"></a>Verifiera agentinstallation
Agentverifiering sker i Azure-portalen och på den lokala servern som kör agenten.

### <a name="azure-portal-agent-verification"></a>Verifiering av Azure Portal-agent
Så här verifierar du att agenten visas av Azure:

1. Logga in på Azure Portal.
2. Till vänster väljer du **Azure Active Directory**, klickar på Azure AD **Connect** och väljer i mitten Hantera **etablering (förhandsversion).**</br>
![Azure-portalen](media/how-to-install/install6.png)</br>

3.  Klicka på **Granska alla agenter**på skärmen Azure AD **Provisioning (preview).**
![Azure AD-etablering](media/how-to-install/install7.png)</br>
 
4. På **skärmen Lokala etableringsagenter** visas de agenter som du har installerat.  Kontrollera att agenten i fråga är där och har **markerats som inaktiverad**.  Agenten är inaktiverad som standard ![etablering agenter](media/how-to-install/verify1.png)</br>

### <a name="on-the-local-server"></a>På den lokala servern
Så här kontrollerar du att agenten kör:

1.  Logga in på servern med ett administratörskonto
2.  Öppna **tjänster** genom att antingen navigera till den eller genom att gå till Start/Run/Services.msc.
3.  Under **Tjänster** se till att **Microsoft Azure AD Connect Agent Updater** och Microsoft Azure AD Connect **Provisioning Agent** finns där och statusen **körs**.
![Tjänster](media/how-to-troubleshoot/troubleshoot1.png)

## <a name="configure-azure-ad-connect-cloud-provisioning"></a>Konfigurera Azure AD Connect-molnetablering
Så här konfigurerar du etablering:

 1. Logga in på Azure AD-portalen.
 2. Klicka på **Azure Active Directory**
 3. Klicka på **Azure AD Connect**
 4. Välj **Hantera etablering (förhandsgranskning)**
 ![](media/how-to-configure/manage1.png)</br>
 5.  Klicka på **Ny konfiguration**
 ![](media/tutorial-single-forest/configure1.png)</br>
 6.  På konfigurationsskärmen anger du ett **e-postmeddelande,** flyttar väljaren till **Aktivera** och klickar på **Spara**.
 ![](media/tutorial-single-forest/configure2.png)</br>
 7. Under **Konfigurera**väljer du **Alla användare** om du vill ändra konfigurationsregelns omfattning.
 ![](media/how-to-configure/scope2.png)</br>
 8. Till höger ändrar du omfånget så att det innehåller den specifika organisationsenhet som du just skapade "OU=processorer,DC=contoso,DC=com".
 ![](media/tutorial-existing-forest/scope2.png)</br>
 9.  Klicka på **Klar** och **Spara**.
 10. Scopet ska nu ställas in på en organisationsenhet. 
 ![](media/tutorial-existing-forest/scope3.png)</br>
 

## <a name="verify-users-are-provisioned-by-cloud-provisioning"></a>Verifiera att användare etableras genom molnetablering
Du kommer nu att verifiera att de användare som du hade i vår lokala katalog har synkroniserats och nu finns i ut Azure AD-klienten.  Observera att det kan ta några timmar att slutföra.  Så här verifierar du att användare etablerar sig genom molnetablering:

1. Bläddra till [Azure-portalen](https://portal.azure.com) och logga in med ett konto som har en Azure-prenumeration.
2. Välj **Azure Active Directory** till vänster
3. Klicka på **Azure AD Connect**
4. Klicka på **Hantera etablering (förhandsgranskning)**
5. Klicka på **knappen Loggar**
6. Sök efter ett användarnamn för att bekräfta att användaren etableras genom molnetablering

Dessutom kan du kontrollera att användaren och gruppen finns i Azure AD.

## <a name="start-the-scheduler"></a>Starta schemaläggaren
Azure AD Connect-synkronisering synkroniserar ändringar som sker i din lokala katalog med hjälp av en schemaläggare. Nu när du har ändrat reglerna kan du starta schemaläggaren igen.  Använd följande steg:

1.  Öppna PowerShell med administratörsbehörighet på servern som kör Azure AD Connect
2.  Kör `Set-ADSyncScheduler -SyncCycleEnabled $true`.
3.  Kör `Start-ADSyncSyncCycle`.  Tryck enter.  

>[!NOTE] 
>Om du kör din egen anpassade schemaläggare för AAD Connect-synkronisering aktiverar du schemaläggaren. 

## <a name="something-went-wrong"></a>Något gick fel
Om piloten inte fungerar som förväntat kan du gå tillbaka till azure AD Connect-synkroniseringsinställningen genom att följa stegen nedan:
1.  Inaktivera etableringskonfiguration i Azure-portalen. 
2.  Inaktivera alla anpassade synkroniseringsregler som skapats för molnetablering med hjälp av verktyget Redigera synkroniseringsregel. Om du inaktiverar bör det orsaka fullständig synkronisering på alla kontakter.

## <a name="configure-azure-ad-connect-sync-to-exclude-the-pilot-ou"></a>Konfigurera Azure AD Connect-synkronisering för att utesluta pilot-organisationsenheten
När du har verifierat att användare från pilotorganisationsenheten har hanterats av molnetablering kan du konfigurera om Azure AD Connect så att den pilotorganisationsenhet som skapades ovan utesluts.  Molnetableringsagenten hanterar synkronisering för dessa användare framöver.  Följ följande steg för att begränsa Azure AD Connect.

 1. Dubbelklicka på Azure AD Connect på servern som kör Azure AD Connect.
 2. Klicka på **Konfigurera**
 3. Välj **Anpassa synkroniseringsalternativ** och klicka på nästa.
 4. Logga in på Azure AD och klicka på **Nästa**.
 5. Klicka på **Nästa**på skärmen **Anslut dina kataloger** .
 6. På **skärmen Domän- och organisationsenhetsfiltrering** väljer du **Synkronisera valda domäner och organisationsenheter**.
 7. Expandera domänen och **avmarkera** **organisationsenheten för processorer.**  Klicka på **Nästa**.
![Omfattning](media/tutorial-existing-forest/scope1.png)</br>
 9. Klicka på **Nästa**på skärmen **Valfria funktioner** .
 10. Klicka på **Konfigurera**på skärmen **Klar att konfigurera** .
 11. När det är klart klickar du på **Avsluta**. 

## <a name="next-steps"></a>Nästa steg 

- [Vad är etablering?](what-is-provisioning.md)
- [Vad är Azure AD Connect-molnetablering?](what-is-cloud-provisioning.md)

