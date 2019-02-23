---
title: Extern delning i Office 365 och B2B - samarbete i Azure Active Directory | Microsoft Docs
description: Beskriver delning av resurser med externa partner med O365 och Azure Active Directory B2B collaboration.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/24/2017
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: 388d42cd41d34a8aebed41dafc48e42006a78457
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2019
ms.locfileid: "56673459"
---
# <a name="office-365-external-sharing-and-azure-active-directory-b2b-collaboration"></a>Extern delning i Office 365 och Azure Active Directory B2B-samarbete

Extern delning i Office 365 (OneDrive, SharePoint Online, enhetliga grupper, etc.) och Azure Active Directory (Azure AD) B2B-samarbete är tekniskt sett samma sak. Alla extern delning (utom OneDrive/SharePoint Online), inklusive gäster i Office 365-grupper, använder redan Azure AD B2B-samarbetsinbjudan API: er för att dela.

## <a name="how-does-azure-ad-b2b-differ-from-external-sharing-in-sharepoint-online"></a>Hur skiljer sig Azure AD B2B från extern delning i SharePoint Online?

OneDrive/SharePoint Online har en separat inbjudan chef. Stöd för extern delning i OneDrive/SharePoint Online startats innan Azure AD utvecklas stödet. OneDrive/SharePoint Online extern delning ackumuleras har flera funktioner med tiden, och många miljontals användare som använder produkten är inbyggd delning mönster. Det finns dock några små skillnader mellan hur OneDrive/SharePoint Online extern delning fungerar och hur fungerar Azure AD B2B-samarbete. Du kan lära dig mer om OneDrive/SharePoint Online extern delning i [externa översikt över delade](https://docs.microsoft.com/sharepoint/external-sharing-overview). Processen skiljer sig vanligtvis från Azure AD B2B på följande sätt:

- OneDrive/SharePoint Online lägger till användare i katalogen när användarna har löst in sina inbjudningar. Därför före inlösen ser du användaren i Azure AD-portalen. Om en annan plats bjuder in en användare under tiden, skapas en ny inbjudan. Men när du använder Azure AD B2B-samarbete läggs användare till direkt på inbjudan så att de visas var som helst.

- Inlösen upplevelse i OneDrive/SharePoint Online ser annorlunda ut än upplevelse i Azure AD B2B-samarbete. När en användare redeems inbjudan, hur ser likadan ut.

- Azure AD B2B-samarbete inbjuden användare kan hämtas från OneDrive/SharePoint Online delning av dialogrutor. OneDrive/SharePoint Online inbjuden användare visas också i Azure AD när de lösa sina inbjudningar.

- Licenskraven skiljer sig åt. För var och en betald Azure AD-licens kan du låta upp till 5 gästanvändare åtkomst till din betalda Azure AD-funktioner. Mer information om licensiering finns [Azure AD B2B-licensiering](https://docs.microsoft.com/azure/active-directory/b2b/licensing-guidance) och [”vad är en extern användare”? i SharePoint Online extern delning översikt](https://docs.microsoft.com/sharepoint/external-sharing-overview#what-is-an-external-user).

För att hantera extern delning i OneDrive/SharePoint Online med Azure AD B2B-samarbete, ställa in OneDrive/SharePoint Online extern delning inställningen för att **Tillåt delning endast med externa användare som redan finns i din organisation Directory**. Användare kan gå till externt delade platser och välj från externa medarbetare som administratören har lagt till. Administratören kan lägga till de externa medarbetarna via API: er för inbjudan i B2B-samarbete.


![OneDrive/SharePoint Online extern delning](media/o365-external-user/odsp-sharing-setting.png)

När du har aktiverat extern delning är möjligheten att söka efter befintliga gästanvändare i SharePoint Online (SPO) Personväljaren av som standard så att de matchar äldre beteende.

Du kan aktivera den här funktionen med hjälp av inställningen ”ShowPeoplePickerSuggestionsForGuestUsers” på samlingsnivå klient- och plats. Du kan ange funktionen med hjälp av Set-SPOTenant och Set-SPOSite cmdletar som ger möjlighet att söka efter alla befintliga gästanvändare i katalogen. Ändringar i klientorganisationsområdet påverkar inte redan etablerade SPO-webbplatser.

## <a name="next-steps"></a>Nästa steg

* [Vad är Azure AD B2B-samarbete?](what-is-b2b.md)
* [Att lägga till en B2B-användare till en roll](add-guest-to-role.md)
* [Delegera inbjudningar för B2B-samarbete](delegate-invitations.md)
* [Dynamiska grupper och B2B-samarbete](use-dynamic-groups.md)
* [Felsökning av Azure Active Directory B2B-samarbete](troubleshoot.md)
