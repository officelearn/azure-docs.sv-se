---
title: Ansluta en ny Windows 10-enhet med Azure AD under den första körningen | Microsoft Docs
description: Ett ämne som förklarar hur användare kan konfigurera Azure AD Join under den första körningen.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/03/2019
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: b3dd14aefc929e14ce22a701013c2be84a4ef01c
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2019
ms.locfileid: "55695693"
---
# <a name="tutorial-join-a-new-windows-10-device-with-azure-ad-during-a-first-run"></a>Självstudier: Ansluta en ny Windows 10-enhet med Azure AD under den första körningen

Med enhetshanteringen i Azure Active Directory (Azure AD) kan du se till att användarna har tillgång till dina resurser från enheter som uppfyller dina krav för säkerhet och kompatibilitet. Mer information finns i [Introduktion till hantering av enheter i Azure Active Directory](overview.md).

Med Windows 10 kan du ansluta en ny enhet till Azure AD under den första körningen (FRX).  
Det gör att du kan distribuera minimerade enheter till dina anställda eller studenter.

Om du har Windows 10 Professional eller Windows 10 Enterprise installerat på en enhet är standardinställningen konfigurationsprocessen för företagsägda enheter.

I Windows *OOBE (out-of-box experience)* går det inte att ansluta en lokal AD-domän (Active Directory). Om du planerar att ansluta en dator till en AD-domän under installationen bör du följa länken **Konfigurera Windows med ett lokalt konto**. Du kan sedan ansluta till domänen från inställningarna på datorn.
 
I den här självstudien får du lära dig hur du ansluter en enhet till Azure AD under FRX:
 > [!div class="checklist"]
> * Nödvändiga komponenter
> * Ansluta en enhet
> * Verifiering

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill ansluta en Windows 10-enhet måste enhetsregistreringstjänsten konfigureras så att du kan registrera enheter. Förutom att du måste ha behörighet att ansluta enheter i din Azure AD-klient måste du ha färre enheter registrerade än det angivna högsta antalet. Mer information finns i [Konfigurera enhetsinställningar](device-management-azure-portal.md#configure-device-settings).

Om din klient är federerad måste dessutom din identitetsprovider stödja slutpunkter för WS-Fed- och WS-Trust-användarnamn/-lösenord. Detta kan vara version 1.3 eller 2005. Det här protokollstödet krävs både för att ansluta enheten till Azure AD och logga in på enheten med ett lösenord.

## <a name="joining-a-device"></a>Ansluta en enhet

**Så här ansluter du en Windows 10-enhet till Azure AD under FRX:**


1. När du slår på din nya enhet och startar installationen bör du se meddelandet **Förbereder**. Följ anvisningarna för att konfigurera din enhet.

2. Börja med att anpassa din region och ditt språk. Godkänn därefter licensvillkoren för Microsoft-programvara.
 
    ![Anpassa för din region](./media/azuread-joined-devices-frx/01.png)

3. Välj det nätverk du vill använda för att ansluta till Internet.

4. Klicka på **This device belongs to my organization** (Den här enheten tillhör min organisation). 

    ![Vem äger den här datorskärmen](./media/azuread-joined-devices-frx/02.png)

5. Ange de autentiseringsuppgifter som har du fått av din organisation och klicka på **Logga in**.

    ![Inloggningsskärm](./media/azuread-joined-devices-frx/03.png)

6. Enheten söker efter en matchande klient i Azure AD. Om du är i en federerad domän omdirigeras du till din lokala STS-server (Secure Token Service), till exempel Active Directory Federation Services (AD FS).

7. Om du är en användare i en icke-federerad domän kan du ange dina autentiseringsuppgifter direkt på den Azure AD-värdbaserade sidan. 

8. Du uppmanas att ange en utmaning för multifaktorautentisering. 
 
9. Azure AD kontrollerar om det krävs en registrering för hantering av mobila enheter.

10. Windows registrerar enheten i organisationens katalog i Azure AD och registrerar den för hantering av mobila enheter, om det är tillämpligt.

11. Om du är:
    - En hanterad användare tar Windows dig till skrivbordet via processen för automatisk inloggning.

    - En federerad användare dirigeras till Windows inloggningsskärm för att ange sina autentiseringsuppgifter.

## <a name="verification"></a>Verifiering

Du kan kontrollera om en enhet är ansluten till Azure AD genom att titta på dialogrutan **Access work or school** (Åtkomst till arbete eller skola) på din Windows-enhet. Dialogrutan bör visa att du är ansluten till din Azure AD-katalog.

![Åtkomst till arbete eller skola](./media/azuread-joined-devices-frx/13.png)


## <a name="next-steps"></a>Nästa steg

- Mer information finns i [Introduktion till hantering av enheter i Azure Active Directory](overview.md).

- Mer information om att hantera enheter i Azure AD-portalen finns i [Managing devices using the Azure portal](device-management-azure-portal.md) (Hantera enheter med Azure-portalen).
