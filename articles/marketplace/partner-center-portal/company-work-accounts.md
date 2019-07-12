---
title: Företagets arbetskonton och Partner Center
description: Så här att kontrollera om ditt företag har ett arbetskonto ställa in med Microsoft, skapa ett nytt arbetskonto eller ställa in flera arbetskonton för användning med Partner Center.
author: mattwojo
manager: evansma
ms.author: parthp
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: d9326cab6cb5ed4ca76c9a84654697f9f90bcfcd
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2019
ms.locfileid: "67619554"
---
# <a name="company-work-accounts-and-partner-center"></a>Företagets arbetskonton och Partner Center

Partner Center använder företagets arbetskonton, även kallat Azure Active Directory (AD)-innehavare, för att hantera åtkomst för flera användare, kontrollera behörigheter, grupper och program och underhålla profildata. Genom att länka ditt företags arbetsdomän e-konto till ditt Partner Center-konto, kan anställda på företaget logga in på Partner Center att hantera marketplace-erbjudanden med sina egna arbets-användarnamn och lösenord.

## <a name="check-whether-your-company-already-has-a-work-account"></a>Kontrollera om ditt företag redan har ett arbetskonto

Om företaget prenumererar på en Microsoft-molntjänst som Azure, Microsoft Intune eller Office 365, sedan har du redan en e-konto arbetsdomän (kallas även en Azure Active Directory-klient) som kan användas med Partner Center.

Följ dessa steg för att kontrollera:
1. Logga in på Azure-administrationsportalen på https://portal.azure.com.
2. Välj **Azure Active Directory** från den vänstra navigeringsmenyn och välj sedan **anpassade domännamn**.
3. Om du redan har ett arbetskonto, visas ditt domännamn.

Om ditt företag inte redan har ett arbetskonto kan skapas en åt dig under registreringsprocessen Partner Center.

## <a name="set-up-multiple-work-accounts"></a>Konfigurera flera arbetskonton

Innan du bestämmer dig att använda ett befintligt arbetskonto, Överväg hur många användare anger arbetskontot behöver åtkomst till Partner Center. Om du har användare anger arbetskontot som inte behöver åtkomst till Partner Center kan du överväga att skapa flera arbetskonton, så att endast de användare som behöver åtkomst till Partner Center representeras på ett visst konto.

## <a name="create-a-new-work-account"></a>Skapa ett nytt arbetskonto

Följ stegen nedan om du vill skapa ett nytt arbetskonto för ditt företag. Du kan behöva begära hjälp från den som har administrativa behörigheter på Microsoft Azure-konto för ditt företag.

1. Logga in på [Microsoft Azure Portal](https://portal.azure.com).
2. Den vänstra menyn väljer du den **Azure Active Directory** -> **användare**.
3. Välj **ny användare** och skapa ett nytt konto i Azure-arbetskonto genom att ange ett namn och e-postadress. Se till att den **katalogroll** är inställd på **användaren** och välj den **visa lösenord** kryssrutan längst ned för att visa och anteckna automatiskt genererade lösenordet.
4. Välj **skapa** att spara den nya användaren.

E-postadressen för användarkontot måste vara ett verifierat domännamn i katalogen. Du kan lista de verifierade domänerna i din katalog genom att välja **Azure Active Directory** -> **anpassade domännamn** i den vänstra navigeringsmenyn.

Mer information om att lägga till anpassade domäner i Azure Active Directory finns [Lägg till eller koppla en domän i Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-add-domain).

## <a name="troubleshoot-work-email-sign-in"></a>Felsöka e-post-inloggningen för arbete

Om du har problem med att logga in på ditt arbetskonto (även kallat Azure AD-klienten), hitta scenario i diagrammet nedan som bäst matchar din situation och följ de rekommenderade stegen.

![Diagram för att felsöka arbete kontoinloggningen](./media/onboarding-aad-flow.png)

## <a name="next-steps"></a>Nästa steg

- [Hantera ditt kommersiella Marketplace-konto i Partnercenter](./manage-account.md) 
