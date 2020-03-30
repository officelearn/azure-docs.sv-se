---
title: Självstudiekurs - Integrera en befintlig skog och en ny skog med en enda Azure AD-klient med Azure AD Connect-molnetablering.
description: Tutorial.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa612ad30ae0faa42071613be15c1d91fb96b8f6
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80332265"
---
# <a name="integrate-an-existing-forest-and-a-new-forest-with-a-single-azure-ad-tenant"></a>Integrera en befintlig skog med en ny skog med en enskild Azure AD-klientorganisation

Den här självstudien går igenom att lägga till molnetablering i en befintlig hybrididentitetsmiljö. 

![Skapa](media/tutorial-existing-forest/existing-forest-new-forest.png)

Du kan använda den miljö som du skapar i den här självstudien för testning eller för att bekanta dig med hur en hybrididentitet fungerar. 

I det här fallet finns det en befintlig skog synkroniserad med Azure AD Connect-synkronisering till en Azure AD-klientorganisation. Och du har en ny skog som du vill synkronisera med samma Azure AD-klientorganisation. Du ställer in molnetablering för den nya skogen. 

## <a name="prerequisites"></a>Krav
### <a name="in-the-azure-active-directory-admin-center"></a>I administrationscentret för Azure Active Directory

1. Skapa ett globalt administratörskonto endast för molnet på din Azure AD-klientorganisation. På så sätt kan du hantera konfigurationen av din klient om dina lokala tjänster misslyckas eller blir otillgängliga. Läs mer om hur du [lägger till ett globalt administratörskonto för molnet](../active-directory-users-create-azure-portal.md). Att slutföra det här steget är viktigt för att säkerställa att du inte blir utelåst från din klient.
2. Lägg till ett eller flera [anpassade domännamn](../active-directory-domains-add-azure-portal.md) i din Azure AD-klientorganisation. Användarna kan logga in med ett av dessa domännamn.

### <a name="in-your-on-premises-environment"></a>I din lokala miljö

1. Identifiera en domänansluten värdserver som kör Windows Server 2012 R2 eller senare med minst 4 GB RAM-minne och .NET 4.7.1+ körningstid 

2. Om det finns en brandvägg mellan servrarna och Azure AD konfigurerar du följande objekt:
   - Se till att agenter kan göra *utgående* begäranden till Azure AD via följande portar:

     | Portnummer | Hur den används |
     | --- | --- |
     | **80** | Hämtar listorna för återkallade certifikat (CRL: er) när TLS/SSL-certifikatet valideras |
     | **443** | Hanterar all utgående kommunikation med tjänsten |
     | **8080** (tillval) | Agenter rapporterar sin status var 10:e minut över port 8080, om port 443 inte är tillgänglig. Den här statusen visas på Azure AD-portalen. |
     
     Om brandväggen tillämpar regler enligt de ursprungliga användarna öppnar du dessa portar för trafik från Windows-tjänster som körs som en nätverkstjänst.
   - Om brandväggen eller proxyn tillåter att du anger säkra suffix lägger du till anslutningar i ** \*.msappproxy.net** och ** \*.servicebus.windows.net**. Om inte, tillåt åtkomst till [Azure datacenter IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653), som uppdateras varje vecka.
   - Dina agenter behöver tillgång till **login.windows.net** och **login.microsoftonline.com** för första registrering. Öppna brandväggen för dessa webbadresser också.
   - För certifikatvalidering avblockerar du följande webbadresser: **mscrl.microsoft.com:80**, **crl.microsoft.com:80**, **ocsp.msocsp.com:80**och **www\.microsoft.com:80**. Eftersom dessa url:er används för certifikatvalidering med andra Microsoft-produkter kanske du redan har dessa url:er blockerade.

## <a name="install-the-azure-ad-connect-provisioning-agent"></a>Installera etableringsagenten för Azure AD Connect
1. Logga in på den domänanslutna servern.  Om du använder basic [AD- och Azure-miljöhandledningen](tutorial-basic-ad-azure.md) är det DC1.
2. Logga in på Azure-portalen med globala administratörsuppgifter för molnet.
3. Till vänster väljer du **Azure Active Directory**, klickar på Azure AD **Connect** och väljer i mitten Hantera **etablering (förhandsversion).**</br>
![Azure-portalen](media/how-to-install/install6.png)</br>
4. Klicka på "Download agent"
5. Kör etableringsagenten för Azure AD Connect
6. På välkomstskärmen **godkänner du** licensvillkoren och klickar på **Installera**.</br>
![Välkomstskärmen](media/how-to-install/install1.png)</br>

7. När den här åtgärden är klar startar konfigurationsguiden.  Logga in med ditt globala Azure AD-administratörskonto.  Observera att om du har IE förbättrad säkerhet aktiverat detta kommer att blockera inloggning.  Om så är fallet stänger du installationen, inaktiverar förbättrad IE-säkerhet i Serverhanteraren och klickar på **guiden AAD Connect-etableringsagent** för att starta om installationen.
8. Klicka på Lägg till **katalog** på skärmen **Anslut Active Directory** och logga sedan in med active directory-domänadministratörskontot.  Domänadministratörskontot bör inte ha krav på lösenordsändring. Om lösenordet upphör att gälla eller ändras måste du konfigurera om agenten med de nya autentiseringsuppgifterna. Den här åtgärden lägger till din lokala katalog.  Klicka på **Nästa**.</br>
![Välkomstskärmen](media/how-to-install/install3.png)</br>

9. Klicka på **Bekräfta**på skärmen **Konfigurationen.**  Den här åtgärden registrerar och startar om agenten.</br>
![Välkomstskärmen](media/how-to-install/install4.png)</br>

10. När den här åtgärden är klar bör du se ett meddelande: **Agentkonfigurationen har verifierats.**  Du kan klicka på **Avsluta**.</br>
![Välkomstskärmen](media/how-to-install/install5.png)</br>
11. Om du fortfarande ser den första välkomstskärmen klickar du på **Stäng**.


## <a name="verify-agent-installation"></a>Verifiera agentinstallation
Agentverifiering sker i Azure-portalen och på den lokala servern som kör agenten.

### <a name="azure-portal-agent-verification"></a>Verifiering av Azure Portal-agent
Så här verifierar du att agenten visas av Azure:

1. Logga in på Azure Portal.
2. Till vänster väljer du **Azure Active Directory**, klickar på Azure AD **Connect** och väljer i mitten Hantera **etablering (förhandsversion).**</br>
![Azure-portalen](media/how-to-install/install6.png)</br>

3.  Klicka på **Granska alla agenter**på skärmen Azure AD **Provisioning (preview).**
![Azure AD-etablering](media/how-to-install/install7.png)</br>
 
4. På **skärmen Lokala etableringsagenter** visas de agenter som du har installerat.  Kontrollera att agenten i fråga är där och är markerad **som aktiv**.
![Etableringsagenter](media/how-to-install/verify1.png)</br>

### <a name="on-the-local-server"></a>På den lokala servern
Så här kontrollerar du att agenten kör:

1.  Logga in på servern med ett administratörskonto
2.  Öppna **tjänster** genom att antingen navigera till den eller genom att gå till Start/Run/Services.msc.
3.  Under **Tjänster**kontrollerar du att **Microsoft Azure AD Connect Agent Updater** och Microsoft Azure AD **Connect-etableringsagent** finns och att statusen **körs**.
![Tjänster](media/how-to-troubleshoot/troubleshoot1.png)

## <a name="configure-azure-ad-connect-cloud-provisioning"></a>Konfigurera Azure AD Connect-molnetablering
 Använd följande steg för att konfigurera etablering

1.  Logga in på Azure AD-portalen.
2.  Klicka på **Azure Active Directory**
3.  Klicka på **Azure AD Connect**
4.  Välj **Hantera etablering (förhandsgranskning)**
![](media/how-to-configure/manage1.png)
5.  Klicka på **Ny konfiguration**
![](media/tutorial-single-forest/configure1.png)
7.  På konfigurationsskärmen anger du ett **e-postmeddelande,** flyttar väljaren till **Aktivera** och klickar på **Spara**.
![](media/tutorial-single-forest/configure2.png)
1.  Konfigurationsstatusen ska nu vara **felfri**.
![](media/how-to-configure/manage4.png)

## <a name="verify-users-are-created-and-synchronization-is-occurring"></a>Verifiera att användare skapas och att synkronisering sker
Du kommer nu att verifiera att de användare som du hade i vår lokala katalog har synkroniserats och nu finns i vår Azure AD-klientorganisation.  Observera att det kan ta några timmar att slutföra.  För att verifiera att användarna synkroniseras gör du följande.


1. Bläddra till [Azure-portalen](https://portal.azure.com) och logga in med ett konto som har en Azure-prenumeration.
2. Välj **Azure Active Directory** till vänster
3. Under **Hantera** väljer du **Användare**.
4. Kontrollera att du ser de nya användarna i vår klient</br>
![Synch](media/tutorial-single-forest/synchronize1.png)</br>

## <a name="test-signing-in-with-one-of-our-users"></a>Testa att logga in med någon av våra användare

1. Bläddra till[https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Logga in med ett användarkonto som har skapats i vår nya klient.  Du måste logga in med följande format: (user@domain.onmicrosoft.com). Använd samma lösenord som användaren använder för att logga in lokalt.</br>
   ![Verifiera](media/tutorial-single-forest/verify1.png)</br>

Du har nu konfigurerat en hybrididentitetsmiljö som du kan använda för att testa och bekanta dig med vad Azure har att erbjuda.

## <a name="next-steps"></a>Nästa steg 

- [Vad är etablering?](what-is-provisioning.md)
- [Vad är Azure AD Connect-molnetablering?](what-is-cloud-provisioning.md)
