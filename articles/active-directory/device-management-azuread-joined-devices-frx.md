---
title: "Ansluta till en ny Windows 10-enhet med Azure AD under den första körningen | Microsoft Docs"
description: "Ett avsnitt som förklarar hur användare kan konfigurera Azure AD Join under välkomstprogrammet."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 0e71df2333dee9c4eb9935d3397d343be246be65
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2018
---
# <a name="join-a-new-windows-10-device-with-azure-ad-during-a-first-run"></a>Ansluta till en ny Windows 10-enhet med Azure AD under den första körningen

Med hantering av enheter i Azure Active Directory (Azure AD), kan du se till att dina användare har åtkomst till dina resurser från enheter som uppfyller dina krav för säkerhet och efterlevnad. Mer information finns i [introduktion till hantering av enheter i Azure Active Directory](device-management-introduction.md).

Du kan koppla en ny enhet till Azure AD under den första körningen (FRX) med Windows 10.  
På så sätt kan du distribuera form av krymp enheter till dina anställda eller studenter.

Om du har Windows 10 Professional eller Windows 10 Enterprise som installerats på en enhet, standard upplevelsen installationsprocessen för företagsägda enheter.

I Windows *out of box experience*, ansluter till en lokal Active Directory (AD)-domän stöds inte. Om du planerar att ansluta en dator till en AD-domän under installationen, ska du markera länken **konfigurera Windows med ett lokalt konto**. Du kan ansluta till domänen från inställningarna på datorn.
 


## <a name="before-you-begin"></a>Innan du börjar

Registreringstjänsten för enheter måste konfigureras så att du kan registrera enheter för att ansluta till en Windows 10-enhet. Förutom att du har behörighet att ansluta enheter i din Azure AD-klient, måste du ha färre enheter som har registrerats än den konfigurerade maximalt. Mer information finns i [konfigurera Enhetsinställningar](device-management-azure-portal.md#configure-device-settings).

## <a name="joining-a-device"></a>Koppla en enhet

**Att ansluta till en Windows 10-enhet till Azure AD under FRX:**


1. När du aktiverar den nya enheten och starta installationen, bör du se den **komma redo** meddelande. Följ anvisningarna för att konfigurera din enhet.

2. Starta genom att anpassa din region och språk. Acceptera licensvillkoren för programvara från Microsoft.
 
    ![Anpassa för din region](./media/device-management-azuread-joined-devices-frx/01.png)

3. Markera det nätverk som du vill använda för att ansluta till Internet.

4. Klicka på **den här enheten tillhör mitt företag**. 

    ![Vem äger den här datorn skärmen](./media/device-management-azuread-joined-devices-frx/02.png)

5. Ange de autentiseringsuppgifter som har fått av din organisation och klicka sedan på **logga in**.

    ![Inloggningssida](./media/device-management-azuread-joined-devices-frx/03.png)

6. Enheten du söker efter en matchande klient i Azure AD. Om du är i en federerad domän, omdirigeras till din lokala Secure säkerhetstokentjänst (STS) server, till exempel Active Directory Federation Services (AD FS).

7. Ange dina autentiseringsuppgifter direkt på Azure AD-värdbaserad sidan om du är en användare i en ofedererad domän. 

8. Du ombeds ange en utmaning för multifaktorautentisering. 
 
9. Azure AD kontrollerar om det krävs en registrering i hantering av mobila enheter.

10. Windows registrerar enheten i organisationens katalog i Azure AD och registrerar i hantering av mobila enheter, om tillämpligt.

11. Om du är:
    - En användare som hanterade Windows går du till skrivbordet genom processen för automatisk inloggning.

    - En federerad användare dirigeras du till skärmen för Windows att ange dina autentiseringsuppgifter.

## <a name="verification"></a>Verifiering

Kontrollera om en enhet är ansluten till din Azure AD genom att granska den **åtkomst arbetet eller skolan** dialogrutan på Windows-enheten. Dialogrutan bör ange att du är ansluten till Azure AD-katalogen.

![Åtkomst till arbetet eller skolan](./media/device-management-azuread-joined-devices-frx/13.png)


## <a name="next-steps"></a>Nästa steg

- Mer information finns i [introduktion till hantering av enheter i Azure Active Directory](device-management-introduction.md).

- Mer information om hur du hanterar enheter i Azure AD-portalen finns [hantera enheter med hjälp av Azure portal](device-management-azure-portal.md).
