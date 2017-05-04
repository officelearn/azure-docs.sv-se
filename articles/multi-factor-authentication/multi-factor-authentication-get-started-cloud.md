---
title: "Kom igång Azure MFA i molnet | Microsoft Docs"
description: "Det här är sidan om Microsoft Azure Multi-Factor Authentication som beskriver hur du kommer igång med Azure MFA i molnet."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 6b2e6549-1a26-4666-9c4a-cbe5d64c4e66
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/14/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 554931e96e073ec2f2f68df2297e1ee21f5eda87
ms.lasthandoff: 04/27/2017


---
# <a name="getting-started-with-azure-multi-factor-authentication-in-the-cloud"></a>Komma igång med Azure Multi-Factor Authentication i molnet
I den här artikeln beskrivs hur du kommer igång med Azure Multi-Factor Authentication i molnet.

> [!NOTE]
> Följande dokumentation innehåller information om hur du aktiverar användare med hjälp av **den klassiska Azure-portalen**. Information om hur du konfigurerar Azure Multi-Factor Authentication för O365-användare finns i [Konfigurera Multi-Factor Authentication för Office 365.](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6?ui=en-US&rs=en-US&ad=US)

![MFA i molnet](./media/multi-factor-authentication-get-started-cloud/mfa_in_cloud.png)

## <a name="prerequisite"></a>Krav
[Registrera dig för en Azure-prenumeration](https://azure.microsoft.com/pricing/free-trial/) – Om du inte redan har en Azure-prenumeration måste du registrera dig för en. Om du precis börjat använda Azure MFA kan du använda en utvärderingsprenumeration.

## <a name="enable-azure-multi-factor-authentication"></a>Aktivera Azure Multi-Factor Authentication
Så länge användarna har licenser som inkluderar Azure Multi-Factor Authentication behöver du inte göra något för att aktivera Azure MFA. Du kan börja med att kräva tvåstegsverifiering för enskilda användare. Följande licenser aktiverar Azure MFA:
- Azure Multi-Factor Authentication
- Azure Active Directory Premium
- Enterprise Mobility + Security

Om du saknar någon av dessa tre licenser, eller om du inte har tillräckligt med licenser för alla användare, är det också ok. Du behöver bara utföra ett extra steg och [Skapa en Multi-Factor Authentication-provider](multi-factor-authentication-get-started-auth-provider.md) i din katalog.

## <a name="turn-on-two-step-verification-for-users"></a>Aktivera tvåstegsverifiering för användare
För att börja kräva tvåstegsverifiering för en användare ändrar du användarens tillstånd från inaktiverat aktiverat.  Mer information om användarstatus finns i [Användarstatus i Azure Multi-Factor Authentication](multi-factor-authentication-get-started-user-states.md)

Följ stegen nedan om du vill aktivera MFA för dina användare.

### <a name="to-turn-on-multi-factor-authentication"></a>Så här aktiverar du Multi-Factor Authentication
1. Logga in på [den klassiska Azure-portalen](https://manage.windowsazure.com) som administratör.
2. Klicka på **Active Directory** till vänster.
3. Under Katalog väljer du katalogen för den användare som du vill aktivera.
   ![Klicka på Katalog](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4. Klicka på **Användare** överst på sidan.
5. Klicka på **Hantera Multi-Factor Auth** längst ned på sidan. En ny webbläsarflik öppnas.
   ![Klicka på Katalog](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6. Hitta den användare som du vill aktivera för tvåstegsverifiering. Du kan behöva ändra vyn överst på sidan. Kontrollera att statusen är **inaktiverad.**
   ![Aktivera användare](./media/multi-factor-authentication-get-started-cloud/enable1.png)
7. Markera **rutan** bredvid användarens namn.
8. Klicka på **Aktivera** till höger.
   ![Aktivera användare](./media/multi-factor-authentication-get-started-cloud/user1.png)
9. Klicka på **Aktivera Multi-Factor Auth**.
   ![Aktivera användare](./media/multi-factor-authentication-get-started-cloud/enable2.png)
10. Nu ändras användarens status från **inaktiverad** till **aktiverad**.
    ![Aktivera användare](./media/multi-factor-authentication-get-started-cloud/user.png)

När du har aktiverat användarna bör du meddela dem via e-post. Nästa gång de försöker logga in, ombeds de att registrera sina konton för tvåstegsverifiering. Då de börjar använda tvåstegsverifiering, ska de också ställa in applösenord för att undvika att låsas ute från icke-webbläsarbaserade appar.

## <a name="use-powershell-to-automate-turning-on-two-step-verification"></a>Använd PowerShell för att aktivera tvåstegsverifiering
Du kan ändra [status](multi-factor-authentication-whats-next.md) med [Azure AD PowerShell](/powershell/azure/overview) genom att använda följande.  Du kan ändra `$st.State` till någon av följande statusar:

* Enabled
* Enforced
* Disabled  

> [!IMPORTANT]
> Vi avråder från att flytta användare direkt från inaktiverat tillstånd till tvingat tillstånd. Icke-webbläsarbaserade appar slutar fungera eftersom användaren inte har genomgått MFA-registrering och hämtat ett [applösenord](multi-factor-authentication-whats-next.md#app-passwords). Om du har icke-webbläsarbaserade appar och kräver lösenord, rekommenderar vi att du går från ett inaktiverat tillstånd till ett aktiverat. På så sätt kan användarna registrera sig och erhålla sina applösenord. Sedan kan flytta du dem till tvingat tillstånd.

PowerShell är ett alternativ om du vill aktivera många användare samtidigt. För närvarande finns det inget alternativ för massaktivering på Azure-portalen och du måste markera varje användare separat. Detta kan vara ganska tidskrävande om du har många användare. Genom att skapa ett PowerShell-skript med följande kod kan du loopa igenom listan med användare och aktivera dem.

        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta

Här är ett exempel:

    $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"
    foreach ($user in $users)
    {
        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
    }


Mer information finns i [Användarstatus i Azure Multi-Factor Authentication](multi-factor-authentication-get-started-user-states.md)

## <a name="next-steps"></a>Nästa steg
Nu när du har konfigurerat Multi-Factor Authentication i molnet kan du konfigurera din distribution. Se [Konfigurera Azure Multi-Factor Authentication](multi-factor-authentication-whats-next.md) för mer information.


