---
title: "Förhandsgranska förfallodatum för Office 365-grupper i Azure Active Directory | Microsoft Docs"
description: "Hur du ställer in förfallodatum för Office 365-grupper i Azure Active Directory (förhandsgranskning)"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: it-pro
ms.openlocfilehash: 8a43df84fd050d7b4bd8d937b8c55e744cb805d3
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2017
---
# <a name="configure-office-365-groups-expiration-preview"></a>Konfigurera förfallodatum för Office 365-grupper (förhandsgranskning)

Du kan nu hantera livscykeln för Office 365-grupper genom att ange upphör att gälla för alla Office 365-grupper som du väljer. När den här giltighetstid har angetts uppmanas ägare i dessa grupper att förnya sina grupper om de fortfarande behöver grupperna. En Office 365-grupp som inte förnyas tas bort. Du kan återställa en Office 365-grupp som har tagits bort inom 30 dagar av gruppen ägare eller administratören.  


> [!NOTE]
> Du kan ställa in ett utgångsdatum för Office 365-grupper.
>
> Ange förfallodatum för O365-grupper måste du ange att en Azure AD Premium-licens har tilldelats
>   - Administratören som konfigurerar inställningar för giltighetstid för klienten
>   - Alla medlemmar i grupper som valts för den här inställningen

## <a name="set-office-365-groups-expiration"></a>Ställa in ett utgångsdatum för Office 365-grupper

1. Öppna den [administrationscentret för Azure AD](https://aad.portal.azure.com) med ett konto som är en global administratör i Azure AD-klienten.

2. Öppna Azure AD, Välj **användare och grupper**.

3. Välj **gruppinställningarna** och välj sedan **giltighetstid** att öppna inställningarna för förfallodatum.
  
  ![Bladet upphör att gälla](./media/active-directory-groups-lifecycle-azure-portal/expiration-settings.png)

4. På den **giltighetstid** bladet kan du:

  * Ange grupp-livstid i dagar. Du kan välja något av de fördefinierade värdena eller ett anpassat värde (ska vara 31 dagar eller mer). 
  * Ange en e-postadress där förnyelse och förfallodatum meddelanden ska skickas när en grupp har ingen ägare. 
  * Välj vilka Office 365-grupper att gälla. Du kan aktivera förfallodatum för **alla** Office 365-grupper du kan välja bland Office 365-grupper eller du väljer **ingen** att inaktivera upphör att gälla för alla grupper.
  * Spara dina inställningar när du är klar genom att välja **spara**.

Instruktioner om hur du hämtar och installerar Microsoft PowerShell-modulen konfigurera giltighetstid för Office 365-grupper via PowerShell finns i [Azure Active Directory V2 PowerShell-modul - offentliga förhandsversionen 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137).

E-postmeddelanden som den här skickas till Office 365 gruppen ägare 30 dagar, 15 dagar och 1 dag innan upphör att gälla i gruppen.

![Förfallodatum för e-postavisering](./media/active-directory-groups-lifecycle-azure-portal/expiration-notification.png)

Gruppägare kan sedan välja **förnya grupp** att förnya Office 365 grupp eller välja **går du till gruppen** att se medlemmarna och annan information om gruppen.

När en grupp upphör att gälla bort gruppen en dag efter förfallodatumet. Ett e-postmeddelande som den här skickas till Office 365 gruppen ägare informerar dem om förfallodatum och efterföljande borttagning av deras Office 365-grupp.

![Gruppen borttagning e-postavisering](./media/active-directory-groups-lifecycle-azure-portal/deletion-notification.png)

Gruppen kan återställas genom att välja **återställning grupp** eller med hjälp av PowerShell-cmdlets som beskrivs i [återställa en borttagen Office 365-grupp i Azure Active Directory] (https://docs.microsoft.com/azure/active-directory/ Active-Directory-groups-Restore-Azure-Portal).
    
Om gruppen du återställa innehåller dokument, SharePoint-webbplatser eller andra beständiga objekt, kan det ta upp till 24 timmar att fullständigt återställa gruppen och dess innehåll.

> [!NOTE]
> * När du distribuerar inställningar för giltighetstid kan finnas det vissa grupper som är äldre än fönstret upphör att gälla. Dessa grupper inte är raderas omedelbart, men är inställda på 30 dagar tills upphör att gälla. Första förnyelse-e-postmeddelandet skickas ut inom en dag. Till exempel grupp A skapades 400 dagar sedan och förfallodatum intervallet anges till 180 dagar. När du använder inställningar för giltighetstid har grupp A 30 dagar innan den tas bort, såvida inte ägaren förnyar den.
> * När en dynamisk grupp tas bort och återställs, är det ses som en ny grupp och nytt fylls enligt regeln. Den här processen kan ta upp till 24 timmar.

## <a name="next-steps"></a>Nästa steg
Dessa artiklar innehåller ytterligare information om Azure AD-grupper.

* [Se befintliga grupper](active-directory-groups-view-azure-portal.md)
* [Hantera inställningar för en grupp](active-directory-groups-settings-azure-portal.md)
* [Hantera medlemmar i en grupp](active-directory-groups-members-azure-portal.md)
* [Hantera medlemskap i en grupp](active-directory-groups-membership-azure-portal.md)
* [Hantera dynamiska regler för användare i en grupp](active-directory-groups-dynamic-membership-azure-portal.md)
