---
title: Företagsarbetskonton och Partnercenter
description: Så här kontrollerar du om ditt företag har konfigurerat ett arbetskonto hos Microsoft, skapar ett nytt arbetskonto eller konfigurerar flera arbetskonton som ska användas med Partner Center.
author: dsindona
ms.author: parthp
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: c4e7427d87c5f88d8c686b867ef88ceb05f28286
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281433"
---
# <a name="company-work-accounts-and-partner-center"></a>Företagsarbetskonton och Partnercenter

Partner Center använder företagsarbetskonton, även kallade AZURE Active Directory (AD) klienter, för att hantera kontoåtkomst för flera användare, kontrollera behörigheter, värdgrupper och program och underhålla profildata. Genom att länka företagets e-postkontodomän för arbete till ditt Partner Center-konto kan anställda i ditt företag logga in på Partner Center för att hantera marketplace-erbjudanden med sina egna användarnamn och lösenord för arbetskonto.

## <a name="check-whether-your-company-already-has-a-work-account"></a>Kontrollera om ditt företag redan har ett arbetskonto

Om ditt företag har prenumererat på en Microsoft-molntjänst som Azure, Microsoft Intune eller Office 365 har du redan en e-postkontodomän för arbete (kallas även en Azure Active Directory-klientorganisation) som kan användas med Partner Center.

Så här kontrollerar du:
1. Logga in på Azure-administratörsportalen på https://portal.azure.com.
2. Välj **Azure Active Directory** på menyn till vänster och välj sedan Anpassade **domännamn**.
3. Om du redan har ett arbetskonto visas ditt domännamn.

Om ditt företag inte redan har ett arbetskonto skapas ett för dig under registreringsprocessen för Partner center.

## <a name="set-up-multiple-work-accounts"></a>Ställ in flera arbetskonton

Innan du bestämmer dig för att använda ett befintligt arbetskonto bör du tänka på hur många användare i arbetskontot som behöver åtkomst till Partner Center. Om du har användare i arbetskontot som inte behöver komma åt Partner Center kan du överväga att skapa flera arbetskonton, så att endast de användare som behöver åtkomst till Partner Center representeras på ett visst konto.

## <a name="create-a-new-work-account"></a>Skapa ett nytt arbetskonto

Följ stegen nedan om du vill skapa ett nytt arbetskonto för ditt företag. Du kan behöva begära hjälp från den som har administratörsbehörighet för ditt företags Microsoft Azure-konto.

1. Logga in på [Microsoft Azure Portal](https://portal.azure.com).
2. Välj **Azure Active Directory** -> **Users**på menyn till vänster .
3. Välj **Ny användare** och skapa ett nytt Azure-arbetskonto genom att ange ett namn och en e-postadress. Kontrollera att **katalogrollen** är inställd på **Användare** och markera kryssrutan **Visa lösenord** längst ned för att visa och anteckna lösenordet automatiskt.
4. Välj **Skapa** om du vill spara den nya användaren.

E-postadressen för användarkontot måste vara ett verifierat domännamn i katalogen. Du kan lista alla verifierade domäner i katalogen genom att välja **Azure Active Directory** -> **Custom-domännamn** på menyn till vänster.

Mer information om hur du lägger till anpassade domäner i Azure Active Directory finns i [Lägga till eller associera en domän i Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-add-domain).

## <a name="troubleshoot-work-email-sign-in"></a>Felsöka inloggning via e-post för arbete

Om du har problem med att logga in på ditt arbetskonto (kallas även din Azure AD-klient) hittar du scenariot i diagrammet nedan som bäst matchar din situation och följer de rekommenderade stegen.

![Diagram för felsökning av inloggning av arbetskonto](./media/onboarding-aad-flow.png)

## <a name="next-steps"></a>Nästa steg

- [Hantera ditt kommersiella Marketplace-konto i Partner Center](./manage-account.md) 
