---
title: Extern delning och B2B-samarbete i Office 365 – Azure AD
description: I artikeln beskrivs delning av resurser med externa partner med O365- och Azure Active Directory B2B-samarbete.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/24/2017
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d70aed6fbe0f09ea6284f913c88186ecf94e297
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74272276"
---
# <a name="office-365-external-sharing-and-azure-active-directory-b2b-collaboration"></a>Extern delning i Office 365 och Azure Active Directory B2B-samarbete

Extern delning i Office 365 (OneDrive, SharePoint Online, Unified Groups, etc.) och Azure Active Directory (Azure AD) B2B-samarbete är tekniskt sett samma sak. All extern delning (utom OneDrive/SharePoint Online), inklusive gäster i Office 365-grupper, använder redan Azure AD B2B-samarbetsanbjudans API:er för delning.

## <a name="how-does-azure-ad-b2b-differ-from-external-sharing-in-sharepoint-online"></a>Hur skiljer sig Azure AD B2B från extern delning i SharePoint Online?

OneDrive/SharePoint Online har en separat inbjudningshanterare. Stöd för extern delning i OneDrive/SharePoint Online startade innan Azure AD utvecklade sitt stöd. Med tiden har extern delning av OneDrive/SharePoint Online samlat på sig flera funktioner och många miljoner användare som använder produktens inbyggda delningsmönster. Det finns dock vissa subtila skillnader mellan hur extern delning av OneDrive/SharePoint Online fungerar och hur Azure AD B2B-samarbete fungerar. Du kan läsa mer om extern delning av OneDrive/SharePoint Online i [översikten över extern delning](https://docs.microsoft.com/sharepoint/external-sharing-overview). Processen skiljer sig i allmänhet från Azure AD B2B på följande sätt:

- OneDrive/SharePoint Online lägger till användare i katalogen efter att användarna har löst in sina inbjudningar. Så före inlösen ser du inte användaren i Azure AD-portalen. Om en annan webbplats bjuder in en användare under tiden genereras en ny inbjudan. Men när du använder Azure AD B2B-samarbete läggs användare till omedelbart på inbjudan så att de visas överallt.

- Inlösenupplevelsen i OneDrive/SharePoint Online ser annorlunda ut än erfarenheten i Azure AD B2B-samarbete. När en användare har löst in en inbjudan ser upplevelserna likadana ut.

- Azure AD B2B-samarbete inbjudna användare kan väljas från OneDrive/SharePoint Online delningsdialogrutor. Användare som bjudits in i OneDrive/SharePoint Online visas också i Azure AD när de har löst in sina inbjudningar.

- Licenskraven skiljer sig åt. För varje betald Azure AD-licens kan du låta upp till fem gästanvändare komma åt dina betalda Azure AD-funktioner. Mer information om licensiering finns i [Azure AD B2B-licensiering](https://docs.microsoft.com/azure/active-directory/b2b/licensing-guidance) och ["Vad är en extern användare?" i den externa delningsöversikten](https://docs.microsoft.com/sharepoint/external-sharing-overview#what-happens-when-users-share)för SharePoint Online .

Om du vill hantera extern delning i OneDrive/SharePoint Online med Azure AD B2B-samarbete anger du inställningen för extern delning i OneDrive/SharePoint Online så att **den endast tillåter delning med externa användare som redan finns i organisationens katalog**. Användare kan gå till externt delade webbplatser och välja från externa medarbetare som administratören har lagt till. Administratören kan lägga till externa medarbetare via API:er för b2b-samarbetsanbjudan.


![Inställningen för extern delning i OneDrive/SharePoint Online](media/o365-external-user/odsp-sharing-setting.png)

När du har aktiverat extern delning är möjligheten att söka efter befintliga gästanvändare i SharePoint Online -personer som väljer AV som standard för att matcha äldre beteende.

Du kan aktivera den här funktionen med inställningen "ShowPeoplePickerSuggestionsForGuestUsers" på klient- och webbplatssamlingsnivå. Du kan ange funktionen med cmdlets Set-SPOTenant och Set-SPOSite, som gör det möjligt för medlemmar att söka efter alla befintliga gästanvändare i katalogen. Ändringar i klientomfånget påverkar inte redan etablerade SPO-platser.

## <a name="next-steps"></a>Nästa steg

* [Vad är Azure AD B2B-samarbete?](what-is-b2b.md)
* [Lägga till en B2B-samarbetsanvändare i en roll](add-guest-to-role.md)
* [Inbjudan till ombud B2B-samarbete](delegate-invitations.md)
* [Dynamiska grupper och B2B-samarbete](use-dynamic-groups.md)
* [Felsöka Azure Active Directory B2B-samarbete](troubleshoot.md)
