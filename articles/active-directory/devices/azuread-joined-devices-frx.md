---
title: Ansluta till en ny Windows 10-enhet med Azure AD under första körningen | Microsoft Docs
description: En artikel som förklarar hur användare kan ställa in Azure AD Join under den första kör upplevelsen.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 1376f011d056aac33333f6ac31ee2eaadaf3ef4a
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39415003"
---
# <a name="join-a-new-windows-10-device-with-azure-ad-during-a-first-run"></a>Ansluta till en ny Windows 10-enhet med Azure AD under första körningen

Med enhetshantering i Azure Active Directory (AD Azure), kan du se till att dina användare kommer åt dina resurser från enheter som uppfyller dina krav för säkerhet och efterlevnad. Mer information finns i den [introduktionen till enhetshantering i Azure Active Directory](overview.md).

Du kan koppla en ny enhet till Azure AD under första körningen av (FRX) med Windows 10.  
På så sätt kan du distribuera form av krymp enheter till dina anställda eller studenter.

Om du har Windows 10 Professional eller Windows 10 Enterprise som installerats på en enhet är standard upplevelsen konfigurationsprocessen för företagsägda enheter.

I Windows *out-of-box experience*, finns inte stöd för att ansluta till en lokal Active Directory (AD)-domän. Om du planerar att ansluta en dator till en AD-domän under installationen, bör du välja länken **konfigurera Windows med ett lokalt konto**. Du kan ansluta till domänen från inställningarna på datorn.
 


## <a name="before-you-begin"></a>Innan du börjar

Om du vill ansluta till en Windows 10-enhet, måste registreringstjänsten för enheter konfigureras så att du kan registrera enheter. Du måste ha färre enheter som har registrerats än det konfigurerade maxantalet förutom att ha behörighet att ansluta enheter i din Azure AD-klient. Mer information finns i [konfigurerar du inställningar för](device-management-azure-portal.md#configure-device-settings).

Dessutom, om din klient är federerad din identitetsprovider måste ha stöd för slutpunkt för WS-Fed och WS-Trust användarnamn/lösenord. Detta kan vara version 1.3 eller 2005. Den här protokollstöd krävs till både ansluta enheten till Azure AD och logga in på enheten med ett lösenord.

## <a name="joining-a-device"></a>Ansluta till en enhet

**Att ansluta till en Windows 10-enhet till Azure AD under FRX:**


1. När du aktiverar den nya enheten och starta installationen, bör du se den **komma redo** meddelande. Följ anvisningarna för att konfigurera din enhet.

2. Starta genom att anpassa din region och språk. Acceptera licensvillkoren för programvara från Microsoft.
 
    ![Anpassa för din region](./media/azuread-joined-devices-frx/01.png)

3. Välj det nätverk som du vill använda för att ansluta till Internet.

4. Klicka på **den här enheten tillhör min organisation**. 

    ![Vem äger den här datorn skärmen](./media/azuread-joined-devices-frx/02.png)

5. Ange de autentiseringsuppgifter som har du fått av din organisation och klicka sedan på **logga in**.

    ![Inloggningsskärm](./media/azuread-joined-devices-frx/03.png)

6. Du enheten söker efter en matchande klient i Azure AD. Om du använder en federerad domän kan omdirigeras till din lokala skydda säkerhetstokentjänst (STS) server, till exempel Active Directory Federation Services (AD FS).

7. Om du är en användare i en icke-federerade domän kan du ange dina autentiseringsuppgifter direkt på Azure AD-värdbaserade sidan. 

8. Du uppmanas att ange en utmaning för multifaktorautentisering. 
 
9. Azure AD kontrollerar om det krävs en registrering i hantering av mobila enheter.

10. Windows registrerar enheten i organisationens katalog i Azure AD och registreras i hantering av mobila enheter, om tillämpligt.

11. Om du är:
    - En hanterad användare Windows tar dig till skrivbordet genom processen för automatisk inloggning.

    - En federerad användare dirigeras du till skärmen för Windows att ange dina autentiseringsuppgifter.

## <a name="verification"></a>Verifiering

Du kan kontrollera om en enhet är ansluten till din Azure AD genom att granska den **åtkomst till arbete eller skola** dialogrutan på din Windows-enhet. Dialogrutan ska indikera att du är ansluten till Azure AD-katalogen.

![Åtkomst till arbete eller skola](./media/azuread-joined-devices-frx/13.png)


## <a name="next-steps"></a>Nästa steg

- Mer information finns i den [introduktionen till enhetshantering i Azure Active Directory](overview.md).

- Mer information om hur du hanterar enheter i Azure AD-portalen finns i [hantera enheter med hjälp av Azure-portalen](device-management-azure-portal.md).
