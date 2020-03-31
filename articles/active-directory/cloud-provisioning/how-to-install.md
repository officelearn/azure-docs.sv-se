---
title: Installera agenten för Azure AD Connect-molnetablering
description: I den här artikeln beskrivs hur du installerar Azure AD Connect-molnetableringsagenten.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4581ab89f74425682f569425f62714ead2c27c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263353"
---
# <a name="install-the-azure-ad-connect-cloud-provisioning-agent"></a>Installera agenten för Azure AD Connect-molnetablering
Det här dokumentet går igenom installationsprocessen för Azure Active Directory (Azure AD) Connect-etableringsagenten och hur du först konfigurerar den i Azure-portalen.

>[!IMPORTANT]
>Följande installationsanvisningar förutsätter att alla förutsättningar har [uppfyllts.](how-to-prerequisites.md)

Installation och konfiguration av Azure AD Connect-etablering sker i följande steg:
    
- [Installera agenten](#install-the-agent)
- [Verifiera agentinstallation](#verify-agent-installation)


## <a name="install-the-agent"></a>Installera agenten
Så här installerar du agenten.

1. Logga in på den server som du ska använda med behörigheten företagsadministratör.
1. Gå till Azure Portal. Till vänster väljer du **Azure Active Directory**.
1. Välj **Hantera etablering (förhandsgranskning)** > **Granska alla agenter**.
1. Hämta etableraren för Azure AD Connect från Azure-portalen.

   ![Ladda ner lokal agent](media/how-to-install/install9.png)</br>
1. Kör installationsprogrammet för Azure AD Connect (AADConnectProvisioningAgent.Installer).
1. På skärmen **Microsoft Azure AD Connect Provisioning Agent Package** godkänner du licensvillkoren och väljer **Installera**.

   ![Paketskärmen för Microsoft Azure AD Connect-etableringsagent](media/how-to-install/install1.png)</br>

1. När den här åtgärden är klar startar konfigurationsguiden. Logga in med ditt globala Azure AD-administratörskonto.
1. Välj **Lägg till katalog**på skärmen Anslut Active **Directory** . Logga sedan in med ditt Active Directory-administratörskonto. Den här åtgärden lägger till den lokala katalogen. Välj **Nästa**.

   ![Ansluta Active Directory-skärm](media/how-to-install/install3.png)</br>

1. På skärmen **Konfigurera fullständig** väljer du **Bekräfta**. Den här åtgärden registrerar och startar om agenten.

   ![Skärmen Konfigurera fullständig](media/how-to-install/install4.png)</br>

1. När den här åtgärden är klar bör du se meddelandet **Din agentkonfiguration har verifierats.** Välj **Avsluta**.

   ![Knappen Avsluta](media/how-to-install/install5.png)</br>
1. Om du fortfarande ser den första skärmen **Microsoft Azure AD Connect Provisioning Agent Package** väljer du **Stäng**.

## <a name="verify-agent-installation"></a>Verifiera agentinstallation
Agentverifiering sker i Azure-portalen och på den lokala servern som kör agenten.

### <a name="azure-portal-agent-verification"></a>Verifiering av Azure Portal-agent
Så här verifierar du att agenten visas av Azure.

1. Logga in på Azure Portal.
1. Till vänster väljer du **Azure Active Directory** > **Azure AD Connect**. Välj **Hantera etablering (förhandsgranskning)** i mitten .

   ![Azure Portal](media/how-to-install/install6.png)</br>

1.  På skärmen **Azure AD-etablering (förhandsversion)** väljer du **Granska alla agenter**.

    ![Alternativet Granska alla agenter](media/how-to-install/install7.png)</br>
 
1. På skärmen **Lokala etableringsagenter** visas de agenter som du har installerat. Kontrollera att agenten i fråga är där och är markerad *som aktiv*.

   ![Skärmen Lokalt etableringsagenter](media/how-to-install/verify1.png)</br>

### <a name="verify-the-port"></a>Verifiera porten
Följ dessa steg för att verifiera att Azure lyssnar på port 443 och att din agent kan kommunicera med den.

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

Det här testet verifierar att dina agenter kan kommunicera med Azure via port 443. Öppna en webbläsare och gå till föregående URL från servern där agenten är installerad.

![Kontroll av porternas nåbarhet](media/how-to-install/verify2.png)

### <a name="on-the-local-server"></a>På den lokala servern
Följ dessa steg för att kontrollera att agenten körs.

1.  Logga in på servern med ett administratörskonto.
1.  Öppna **tjänster** genom att antingen navigera till den eller genom att gå till **Start** > **Run** > **Services.msc**.
1.  Under **Tjänster**kontrollerar du att **Microsoft Azure AD Connect Agent Updater** och **Microsoft Azure AD Connect-etableringsagenten** finns där och att deras status *körs*.

    ![Skärmen Tjänster](media/how-to-troubleshoot/troubleshoot1.png)

>[!IMPORTANT]
>Agenten har installerats men den måste konfigureras och aktiveras innan den börjar synkronisera användare. Information om hur du konfigurerar en ny agent finns i [Skapa en ny konfiguration för Azure AD Connect molnbaserad etablering](how-to-configure.md).



## <a name="next-steps"></a>Nästa steg 

- [Vad är etablering?](what-is-provisioning.md)
- [Vad är Azure AD Connect-molnetablering?](what-is-cloud-provisioning.md)
 
