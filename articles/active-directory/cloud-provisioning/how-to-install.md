---
title: Installera agenten för Azure AD Connect-molnetablering
description: Den här artikeln beskriver hur du installerar Azure AD Connect Cloud Provisioning-agenten.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/19/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a5b59942731c8ca7b29de30e160d8370c9cf76ef
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "85807646"
---
# <a name="install-the-azure-ad-connect-cloud-provisioning-agent"></a>Installera agenten för Azure AD Connect-molnetablering
Det här dokumentet vägleder dig genom installations processen för den Azure Active Directory (Azure AD) Connect-programetablerings agenten och hur du konfigurerar den för första gången i Azure Portal.

>[!IMPORTANT]
>Följande installations anvisningar förutsätter att alla [krav](how-to-prerequisites.md) är uppfyllda.

Att installera och konfigurera Azure AD Connect etablering sker i följande steg:
    
- [Installera agenten](#install-the-agent)
- [Verifiera agent installation](#verify-agent-installation)


## <a name="install-the-agent"></a>Installera agenten
Följ dessa steg om du vill installera agenten.

1. Logga in på den server som du ska använda med företags administratörens behörigheter.
1. Logga in på Azure Portal och gå sedan till **Azure Active Directory**.
1. På den vänstra menyn väljer du **Azure AD Connect**.
1. Välj **Hantera etablering (för hands version)**  >  **Granska alla agenter**.
1. Hämta Azure AD Connect etablerings agenten från Azure Portal.

   ![Hämta lokal agent](media/how-to-install/install9.png)</br>
1. Kör installations programmet för Azure AD Connect etablering (AADConnectProvisioningAgent. Installer).
1. På skärmen **Microsoft Azure AD koppla etablerings agent paket** godkänner du licens villkoren och väljer **Installera**.

   ![Skärmen Microsoft Azure AD koppla etablerings agent paket](media/how-to-install/install1.png)</br>

1. När den här åtgärden har slutförts startar konfigurations guiden. Logga in med ditt globala administratörs konto för Azure AD.
1. På skärmen **anslut Active Directory** väljer du **Lägg till katalog**. Logga sedan in med ditt Active Directory administratörs konto. Den här åtgärden lägger till din lokala katalog. Välj **Nästa**.

   ![Anslut Active Directory skärmen](media/how-to-install/install3.png)</br>

1. På skärmen **konfiguration avslutad** väljer du **Bekräfta**. Den här åtgärden registrerar och startar om agenten.

   ![Skärmen konfiguration slutförd](media/how-to-install/install4.png)</br>

1. När den här åtgärden har slutförts bör du se meddelandet att **agent konfigurationen har verifierats.** Välj **Avsluta**.

   ![Knappen Avsluta](media/how-to-install/install5.png)</br>
1. Välj **Stäng**om du fortfarande ser det första **Microsoft Azure AD ansluta agenten för etablering av agent paket** .

## <a name="verify-agent-installation"></a>Verifiera agent installation
Agent verifiering sker i Azure Portal och på den lokala server som kör agenten.

### <a name="azure-portal-agent-verification"></a>Azure Portal agent verifiering
Följ dessa steg om du vill kontrol lera att agenten visas av Azure.

1. Logga in på Azure Portal.
1. Välj **Azure Active Directory**  >  **Azure AD Connect**till vänster. I mitten väljer du **Hantera etablering (för hands version)**.

   ![Azure Portal](media/how-to-install/install6.png)</br>

1.  På skärmen **Azure AD Provisioning (för hands version)** väljer du **Granska alla agenter**.

    ![Alternativet granska alla agenter](media/how-to-install/install7.png)</br>
 
1. På skärmen **lokala etablerings agenter** visas de agenter som du har installerat. Kontrol lera att agenten i fråga finns där och har marker ATS som *aktiv*.

   ![Skärmen lokala etablerings agenter](media/how-to-install/verify1.png)</br>



### <a name="on-the-local-server"></a>På den lokala servern
Kontrol lera att agenten körs genom att följa dessa steg.

1.  Logga in på servern med ett administratörs konto.
1.  Öppna **tjänster** genom att antingen navigera till den eller genom att **Starta**  >  **köra**  >  **Services. msc**.
1.  Under **tjänster**kontrollerar du att **Microsoft Azure AD ansluter agent uppdaterings** **agenten och Microsoft Azure AD ansluta etablerings agenten** är där och att deras status är *igång*.

    ![Sidan tjänster](media/how-to-troubleshoot/troubleshoot1.png)

>[!IMPORTANT]
>Agenten har installerats men måste konfigureras och aktive ras innan den kommer att starta synkronisering av användare. Information om hur du konfigurerar en ny agent finns i [skapa en ny konfiguration för Azure AD Connect molnbaserad etablering](how-to-configure.md).



## <a name="next-steps"></a>Nästa steg 

- [Vad är etablering?](what-is-provisioning.md)
- [Vad är Azure AD Connect-molnetablering?](what-is-cloud-provisioning.md)
 
