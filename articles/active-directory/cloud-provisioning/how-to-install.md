---
title: Installerar Azure AD Connect etablerings agent
description: Det här avsnittet beskriver steg för steg hur du installerar en etablerings agent.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8cf479d4962f6d7aa9a0ba43b48f99bd67566fb6
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74794489"
---
# <a name="install-azure-ad-connect-provisioning-agent"></a>Installera Azure AD Connect etablerings agent
Det här dokumentet vägleder dig genom installations processen för Azure AD Connect etablerings agenten och hur du konfigurerar den för första gången i Azure Portal.

>[!IMPORTANT]
>Följande installations anvisningar förutsätter att alla [krav](how-to-prerequisites.md) är uppfyllda.

Att installera och konfigurera Azure AD Connect etablering sker i följande steg:
    
- [Installera agenten](#install-the-agent)
- [Verifiera agent installation](#verify-agent-installation)


## <a name="install-the-agent"></a>Installera agenten

1. Logga in på den server som du vill använda med företags administratörs behörighet.
2. Navigera till Azure Portal och välj Azure Active Directory till vänster.
3. Klicka på **Hantera etablering (för hands version)** och välj **Granska alla agenter**.
3. Hämta Azure AD Connect etablerings agenten från Azure Portal.
![Välkomstskärmen](media/how-to-install/install9.png)</br>
3. Kör Azure AD Connect etablering (AADConnectProvisioningAgent. Installer)
3. På Välkomst skärmen **godkänner** du licens villkoren och klickar på **Installera**.</br>
![Välkomstskärmen](media/how-to-install/install1.png)</br>

4. När den här åtgärden har slutförts startas konfigurations guiden.  Logga in med ditt globala administratörs konto för Azure AD.
5. På skärmen **anslut Active Directory** klickar du på **Lägg till katalog** och loggar sedan in med ditt Active Directory administratörs konto.  Den här åtgärden lägger till din lokala katalog.  Klicka på **Next**.</br>
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

1. Logga in på Azure Portal.
2. Till vänster väljer du **Azure Active Directory**, klickar på **Azure AD Connect** och i mitten väljer du **Hantera etablering (för hands version)** .</br>
![Azure-portalen](media/how-to-install/install6.png)</br>

3.  På skärmen **Azure AD Provisioning (för hands version)** klickar du på **Granska alla agenter**.
![Azure AD-etablering](media/how-to-install/install7.png)</br>
 
4. På **skärmen lokala etablerings agenter** visas de agenter som du har installerat.  Kontrol lera att agenten i fråga finns där och har marker ATS som **aktiv**.
![etablerings agenter](media/how-to-install/verify1.png)</br>

### <a name="verify-the-port"></a>Verifiera porten
För att verifiera att Azure lyssnar på port 443 och att agenten kan kommunicera med den, kan du använda följande:

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

Det här testet kontrollerar att dina agenter kan kommunicera med Azure via port 443.  Öppna en webbläsare och gå till ovanstående URL från servern där agenten är installerad.
![Tjänster](media/how-to-install/verify2.png)

### <a name="on-the-local-server"></a>På den lokala servern
Verifiera att agenten körs genom att följa dessa steg:

1.  Logga in på servern med ett administratörs konto
2.  Öppna **tjänster** genom att antingen navigera till den eller genom att gå till starta/köra/Services. msc.
3.  Under **tjänster** ser du till **Microsoft Azure AD ansluta Agent updateer** och **Microsoft Azure AD ansluta etablerings agenten** finns där, och statusen **körs**.
![Tjänster](media/how-to-troubleshoot/troubleshoot1.png)

>[!IMPORTANT]
>Agenten har installerats men måste konfigureras och aktive ras innan den kan starta synkronisering av användare.  Om du vill konfigurera en ny agent se [Azure AD Connect etablering av ny agent konfiguration](how-to-configure.md).



## <a name="next-steps"></a>Nästa steg 

- [Vad är etablering?](what-is-provisioning.md)
- [Vad är Azure AD Connect Cloud revisioner?](what-is-cloud-provisioning.md)
 
